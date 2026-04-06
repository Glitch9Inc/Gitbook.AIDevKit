# Unity Editor Crash Analysis

## 크래시 개요

* **발생 위치**: `UnityWebRequestManager::TickEditor`
* **크래시 유형**: Native Crash (UNKNOWN)
* **Unity 버전**: 6000.1.3f1

## 크래시 발생 시나리오

### 1. Tool 실행 및 에러 발생

```
SpreadsheetAgent의 UpdateColumn 툴 실행
↓
에러 반환: "Must provide column_index or column_name to identify the target column"
↓
FunctionOutput.Error 생성 및 pending queue에 저장
```

**코드 위치**: `Assets/Glitch9/AIDevKit.Sheets/Editor/Scripts/SpreadsheetAgent/Tools/Common/UpdateColumn.cs:55`

### 2. 스트리밍 완료 시 Tool Output 제출

```
AgentChatApiAdapter.CompleteResponseTcs() 호출
↓
AgentControl.NotifyStreamingCompleted() 호출
↓
Pending tool outputs를 모두 SubmitToolOutputImmediately()로 제출
↓
m_Agent.SubmitToolOutputAsync().Forget() 실행
```

**코드 위치**:

* `AgentChatApiAdapter.cs:95` - `CompleteResponseTcs()`
* `AgentControl.cs:226` - `NotifyStreamingCompleted()`
* `AgentControl.cs:199` - `SubmitToolOutputImmediately()`

### 3. 새로운 OpenAI Responses API 요청 시작

```
SubmitToolOutputAsync() 
↓
새로운 ResponseRequest 생성
↓
StreamAsyncInternal() → ProviderBridgeHub.StreamResponseAsync()
↓
UnityWebRequest 생성 및 StreamingTextDownloadHandler 설정
↓
스트리밍 시작
```

### 4. Native Crash 발생

```
이전 스트리밍 리소스가 아직 정리되지 않은 상태에서
새로운 UnityWebRequest 스트리밍이 시작됨
↓
UnityWebRequestManager::TickEditor에서 충돌
↓
Native Crash (Unity Engine 내부 오류)
```

## 근본 원인 분석

### 정확한 크래시 원인: await foreach 루프 내부에서의 CompleteResponseTcs 호출

**핵심 문제**:

```csharp
// AgentChatApiAdapter.cs (수정 전)
protected override async UniTask StreamAsync(...)
{
    await m_Api.StreamAsync(...);
    
    await foreach (StreamEvent<...> evt in stream)
    {
        evt.Accept(m_Visitor);
        evt.Match(onComplete: CompleteResponseTcs);  // ❌ 문제: 여기서 바로 호출!
    }
    // await foreach가 완전히 끝나면 StreamAsync의 finally에서 uwr.Dispose() 호출
}
```

**실행 순서 분석**:

```
1. UnityWebRequest 생성 및 스트리밍 시작
2. StreamAsync의 await foreach 루프에서 이벤트 수신
3. complete 이벤트 수신 → evt.Match(onComplete: CompleteResponseTcs)
4. CompleteResponseTcs() 즉시 실행
   ├─ NotifyStreamingCompleted() 호출
   ├─ pending tool outputs를 모두 SubmitToolOutputImmediately()로 제출
   └─ 새로운 UnityWebRequest 생성 및 스트리밍 시작 ❌
5. ⚠️ 이 시점에서 await foreach 루프가 아직 끝나지 않음!
6. ⚠️ 이전 UnityWebRequest의 finally { uwr.Dispose() }가 아직 실행 안 됨!
7. ⚠️ 두 개의 UnityWebRequest가 동시에 활성 상태
8. UnityWebRequestManager::TickEditor에서 충돌 💥
```

**UnityWebRequestRunner.cs의 리소스 관리**:

```csharp
// UnityWebRequestRunner.cs:190
public static async UniTask StreamAsync<TDelta, TResult>(...)
{
    try
    {
        await uwr.SendWebRequest();
        // 스트리밍 처리...
    }
    finally
    {
        // Channel 완료 후 리소스 정리
        if (caught != null)
            deltaCh.Writer.TryComplete(caught);
        else
            deltaCh.Writer.TryComplete();
            
        uwr.Dispose();  // ⚠️ 여기서 dispose!
    }
}
```

### 왜 await foreach 내부에서 호출하면 위험한가?

1. **Channel의 완료 신호**는 `deltaCh.Writer.TryComplete()`가 호출되어야 함
2. `TryComplete()` 후에 `await foreach` 루프가 종료됨
3. 하지만 `evt.Match(onComplete: ...)` 람다는 **동기적으로 실행**됨
4. 즉, `TryComplete()` 호출 → `await foreach`가 마지막 이벤트를 받음 → 람다 실행 → 새 요청 시작
5. 이때 **아직 finally 블록의 uwr.Dispose()가 실행 안 됨**!

### 타이밍 다이어그램

```
Time  │ Thread/Context          │ Action
──────┼────────────────────────┼──────────────────────────────────────
t0    │ UnityWebRequest 1      │ 스트리밍 시작
t1    │ StreamAsync            │ await foreach 이벤트 수신 중...
t2    │ Channel Writer         │ TryComplete() 호출
t3    │ await foreach          │ 마지막 이벤트 수신
t4    │ evt.Match lambda       │ CompleteResponseTcs() 실행 (동기)
      │                        │ └─ NotifyStreamingCompleted()
      │                        │    └─ SubmitToolOutputImmediately()
t5    │ UnityWebRequest 2      │ NEW 스트리밍 시작 ❌ 중복!
t6    │ UnityWebRequest 1      │ ⚠️ 아직 살아있음 (Dispose 안됨)
t7    │ StreamAsync finally    │ uwr.Dispose() 실행 시도
      │                        │ └─ 하지만 새 UWR2가 이미 시작됨
t8    │ UnityMainThread        │ 💥 CRASH - 두 UWR이 충돌
```

## 재현 조건

1. SpreadsheetAgent를 사용하여 tool call 실행
2. Tool이 에러를 반환 (예: 잘못된 인자)
3. 스트리밍 완료 시점에 자동으로 새로운 요청 시작
4. Unity 6 환경에서 UnityWebRequest의 동시성 제한 촉발
5. 높은 확률로 native crash 발생

## 해결 방안

### ✅ 적용된 수정사항 (2026-03-05)

#### 1. StreamAsync 메서드 수정 - await foreach 완료 후 CompleteResponseTcs 호출

**수정 전**:

```csharp
// AgentChatApiAdapter.cs (문제 코드)
protected override async UniTask StreamAsync(
    ConversationItem[] @in,
    CancellationToken ct = default)
{
    IGenerativeStream<ResponseEventBase, ConversationItem> stream =
        await m_Api.StreamAsync(@in, this, ct);

    await foreach (StreamEvent<ResponseEventBase, Generated<ConversationItem>> evt in stream)
    {
        evt.Accept(m_Visitor);
        evt.Match(onComplete: CompleteResponseTcs);  // ❌ 루프 내부에서 즉시 호출
    }
    // ⚠️ 이 시점에 도달하기 전에 CompleteResponseTcs가 실행되어 새 요청 시작
}
```

**수정 후**:

```csharp
// AgentChatApiAdapter.cs (수정됨)
protected override async UniTask StreamAsync(
    ConversationItem[] @in,
    CancellationToken ct = default)
{
    IGenerativeStream<ResponseEventBase, ConversationItem> stream =
        await m_Api.StreamAsync(@in, this, ct) ??
        throw new Exception("Failed to initiate streaming from ResponsesApi.");

    Generated<ConversationItem> finalResult = null;

    await foreach (StreamEvent<ResponseEventBase, Generated<ConversationItem>> evt in stream)
    {
        evt.Accept(m_Visitor);
        // CompleteResponseTcs 대신 결과만 저장
        evt.Match(onComplete: result => finalResult = result);
    }

    // ✅ await foreach가 완전히 종료된 후 (UnityWebRequest.Dispose() 완료 후)
    // CompleteResponseTcs 호출
    if (finalResult != null)
    {
        CompleteResponseTcs(finalResult);
    }
}
```

**효과**:

* `await foreach` 루프가 완전히 종료됨
* `UnityWebRequestRunner.StreamAsync`의 `finally { uwr.Dispose(); }` 실행 완료
* 리소스가 정리된 후에 `CompleteResponseTcs` 호출
* 새로운 스트리밍 요청이 안전하게 시작됨

#### 2. CompleteResponseTcs에 프레임 지연 추가 (이중 보호)

**수정 전**:

```csharp
protected void CompleteResponseTcs(Generated<ConversationItem> result)
{
    // Streaming 완료 알림 - pending tool outputs를 모두 submit
    m_Control.NotifyStreamingCompleted();  // ❌ 즉시 실행

    m_ResponseTcs?.TrySetResult(result);
    m_ResponseTcs = null;
}
```

**수정 후**:

```csharp
protected void CompleteResponseTcs(Generated<ConversationItem> result)
{
    m_ResponseTcs?.TrySetResult(result);
    m_ResponseTcs = null;

    // UnityWebRequest 리소스 정리를 보장하기 위해 다음 프레임으로 지연
    // 이중 보호: StreamAsync에서 await foreach 완료 후 호출 + 여기서 프레임 지연
    UniTask.Void(async () =>
    {
        await UniTask.DelayFrame(1);
        // ✅ 다음 프레임에 실행하여 리소스 정리 시간 보장
        m_Control.NotifyStreamingCompleted();
    });
}
```

**효과**:

* 첫 번째 방어선: `await foreach` 완료 후 호출
* 두 번째 방어선: `UniTask.DelayFrame(1)`로 다음 프레임까지 지연
* UnityWebRequest dispose가 Unity의 네이티브 레이어에서 완전히 처리될 시간 보장
* Unity 6의 엄격한 리소스 관리 정책에도 안전하게 대응

### 왜 이 해결책이 효과적인가?

1.  **UnityWebRequest 생명주기 보장**:

    ```
    t0: 스트리밍 시작
    t1: complete 이벤트 수신 → finalResult에 저장만 함
    t2: await foreach 종료
    t3: finally { uwr.Dispose() } 실행 ✅
    t4: CompleteResponseTcs() 호출
    t5: UniTask.DelayFrame(1) - 다음 프레임까지 대기 ✅
    t6: NotifyStreamingCompleted() - 새 요청 제출 ✅
    ```
2. **Runtime 코드에서 사용 가능**:
   * `EditorApplication.delayCall` 대신 `UniTask.DelayFrame(1)` 사용
   * Editor/Runtime 모두에서 동작
3. **예외 안전성**:
   * `UniTask.Void(async () => ...)` 패턴으로 fire-and-forget이지만
   * 크래시 원인이 제거되어 예외 발생 자체가 방지됨
4. **Unity 6 호환성**:
   * Unity 6의 더 엄격한 네이티브 리소스 관리에 대응
   * 프레임 지연으로 네이티브 레이어의 정리 시간까지 보장

### 이전에 제안했던 방법들이 부적절한 이유

#### ❌ EditorApplication.delayCall

```csharp
EditorApplication.delayCall += () => { /* ... */ };
```

* ❌ `AgentControl`은 **Runtime 코드**이므로 사용 불가
* Editor API는 Editor 어셈블리에서만 사용 가능

#### ❌ 단순 예외 처리

```csharp
try { await m_Agent.SubmitToolOutputAsync(...); }
catch (Exception ex) { /* ... */ }
```

* ❌ 예외가 발생하기 전에 **네이티브 크래시** 발생
* 관리 코드(C#)에서 catch 불가능

#### ❌ .Forget() 제거만

* ❌ 타이밍 문제의 근본 원인을 해결하지 못함
* 예외 처리만 개선될 뿐 크래시는 여전히 발생

## 결론

### 크래시의 본질

Unity 6의 UnityWebRequest는 **리소스가 완전히 정리되기 전에 새로운 요청을 시작하면 네이티브 크래시**를 발생시킵니다. 이는 Unity 6의 더 엄격한 리소스 관리 정책 때문입니다.

### 해결의 핵심

1. **await foreach 루프가 완전히 종료된 후** 새 요청 시작
2. **프레임 지연**으로 네이티브 레이어의 리소스 정리 시간 보장
3. Runtime 코드에서 사용 가능한 **UniTask.DelayFrame** 활용

### 적용된 변경사항

| 파일                       | 변경 내용                                             | 효과                              |
| ------------------------ | ------------------------------------------------- | ------------------------------- |
| `AgentChatApiAdapter.cs` | `StreamAsync`에서 결과를 먼저 저장 후 루프 종료 후 처리            | UnityWebRequest.Dispose() 완료 보장 |
| `AgentChatApiAdapter.cs` | `CompleteResponseTcs`에 `UniTask.DelayFrame(1)` 추가 | 네이티브 리소스 정리 시간 보장 (이중 보호)       |

### 검증 방법

1. SpreadsheetAgent를 사용하여 의도적으로 에러를 발생시키는 tool 실행
2. 스트리밍 완료 시 자동으로 새 요청이 제출되는지 확인
3. Unity Editor가 크래시 없이 정상 동작하는지 확인

### 추가 권장사항

* Unity Issue Tracker에서 Unity 6 UnityWebRequest 관련 이슈 모니터링
* 향후 Unity 6.1 이상에서 더 나은 동시 요청 관리 API가 제공될 수 있음
* 프로덕션 환경에서는 동시 스트리밍 요청 수를 1개로 제한하는 것이 안전함
