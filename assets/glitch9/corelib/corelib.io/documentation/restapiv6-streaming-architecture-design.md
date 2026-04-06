# RESTApiV6 Streaming Architecture Design

## 디자인 개요

`RESTApiV6-streaming-architecture.md`에서 각 역할을 세분화하여 정의했지만, RESTApiV6는 **스트리밍 파이프라인 전체를 내부에서 일관되게 처리**하도록 설계되었다. 이로 인해 외부 진입점(Ingress)을 별도의 계층으로 분리하지 않고, 모든 스트리밍 단계를 포괄하는 `IStreamHandler` 인터페이스에 `Ingress` 메서드를 포함하는 구조를 채택했다.

`IStreamAggregator`와 `IStreamDispatcher` 역시 아키텍처 구성 요소로 존재하지만, **모든 스트림이 항상 Aggregation/Dispatch 단계를 필요로 하지는 않는다.** 현재 구조에서 BinaryStream 및 AudioStream은 각 chunk가 즉시 소비 가능한 도메인 이벤트를 구성하므로, Aggregation/Dispatch 단계를 생략하는 것이 합리적이다.

따라서 **현 단계에서는** `IStreamAggregator`와 `IStreamDispatcher`가 주로 TextStream에 적용되지만, 이는 스트림 타입에 따른 고정 규칙이 아니라 **요청의 도메인 완성 정책에 따른 선택적 적용**이다.

***

## TextStream 흐름 요약

1. **Transport** RESTApiV6는 `StreamingTextDownloadHandler`(Unity `DownloadHandler`)를 통해 요청에 설정된 `IByteFramer`로부터 frame(`string`)을 수신한다.
2. **Parsing** `ITextParser<TEvent>`가 frame(`string`)을 `StreamEvent` 또는 `StreamEvent<TData>`로 변환한다.
3. **Interpretation / Normalization** 별도의 단계로 분리하지 않는다. SSE 처리, 필터링, 에러 판별, DONE 프레임 해석 등은 **Parser 체인(Parser + Filter) 내부에서 수행**되며, 결과로 바로 `StreamEvent`를 생성한다.
4. **Event Routing**
   * **4-1. Aggregator/Dispatcher가 없는 경우** `StreamEvent`는 즉시 `EventHub`로 전달되어 도메인 이벤트로 소비된다.
   * **4-2. Aggregator/Dispatcher가 있는 경우** 다음 단계로 진행한다.
5. **Aggregation** `IStreamAggregator`가 `StreamEvent<TData>`를 누적/병합하여 의미 있는 도메인 데이터(`TResult`)를 구성한다.
6. **Dispatch** `IStreamDispatcher`가 Aggregation 결과를 `EventHub`로 전달하며, Delta 이벤트와 Completion 이벤트를 정책에 따라 분기 처리한다.

***

## BinaryStream 흐름 요약

1. **Transport** RESTApiV6는 `StreamingBinaryDownloadHandler`를 통해 frame(`byte[]`)을 수신한다.
2. **Parsing** `IBinaryParser<TEvent>`가 frame(`byte[]`)을 즉시 `StreamEvent`로 변환한다.
3. **Interpretation / Normalization** 별도의 단계는 존재하지 않으며, Parser가 직접 최종 `StreamEvent`를 생성한다.
4. **Event Dispatch** 생성된 이벤트는 곧바로 `EventHub`로 전달된다.

> 현재 BinaryStream은 chunk 단위가 곧 도메인 이벤트이므로 Aggregation/Dispatch 단계를 생략한다.

***

## AudioStream 흐름 요약

1. **Transport** RESTApiV6는 `StreamingAudioDownloadHandler`를 통해 frame(`byte[]`)을 수신한다.
2. **Audio Decoding** `StreamingAudioDownloadHandler`가 frame(`byte[]`)을 PCM 기반 `float[]` 오디오 샘플로 디코딩한다.
3. **Parsing** `IAudioParser<TEvent>`가 오디오 샘플을 `StreamEvent`로 변환한다.
4. **Interpretation / Normalization** 별도의 단계는 존재하지 않으며, Parser가 직접 최종 이벤트를 생성한다.
5. **Event Dispatch** 이벤트는 즉시 `EventHub`로 전달된다.

> AudioStream 역시 현재는 각 chunk가 독립적인 소비 단위이므로 Aggregation/Dispatch 단계를 생략한다.

***

## 스트리밍 종료(Completion) 모델

RESTApiV6는 스트리밍 종료를 다음 세 가지 레벨로 구분한다.

1. **Transport Completion**
   * 네트워크 연결 종료
   * 타임아웃
   * DownloadHandler 종료
2. **Protocol Completion**
   * SSE DONE 프레임
   * 빈 라인
   * 이벤트 경계 신호
3. **Domain Completion**
   * `finish_reason`
   * `completed` 이벤트
   * `usage` 도착
   * 의미적으로 하나의 응답이 완성되었음을 나타내는 신호

각 `StreamEvent`는 Protocol / Domain 완료 여부를 함께 운반하며, `IStreamHandler`는 설정된 `StreamCompletionMode`에 따라 Completion 시점을 결정한다.

***

## Error Handling 정책

* Parsing / Filtering / Aggregation 단계에서는 **에러를 숨기지 않고 예외를 발생시킨다.**
* 모든 예외는 최상위 `IStreamHandler.Ingress` 또는 `DownloadHandler` 레벨에서 캐치된다.
* 에러 발생 시:
  * `OnFault(exception)` 호출
  * 요청 Abort
  * Channel / Event Stream을 예외와 함께 종료

이를 통해 try/catch 로직이 파이프라인 전반에 흩어지는 것을 방지한다.
