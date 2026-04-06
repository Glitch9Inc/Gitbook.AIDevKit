# RESTApiV6 Streaming Architecture Concept

## 한눈에 보기

전송 레이어(wire)와 의미 레이어(domain)를 분리하여, 파싱·해석·도메인 처리를 단계적으로 수행한다.\
흐름: Transport → Parsing → Interpretation → Domain Event → Dispatch → Listener

## 용어 정리

* chunk: 전송 단위 (SSE frame, WS frame, HTTP chunk) — transport 관점
* delta: 의미적 변경분 — semantic 관점
* content: 실제 사용자 데이터 (텍스트, 오디오, 이미지, JSON) — domain 관점

## 메시지 흐름 요약

1. Transport (SSE frame 등) — 네트워크 단위, 전송 책임만 있음
2. Parsing — 문자열→구조체 변환, 스키마/형식 검증 (상태/누적 없음)
3. Interpretation/Normalization — 의미 해석, 스트림 누적·병합·flush 판단 (상태 가짐)
4. Domain Event — 확정된 의미 단위 (앱이 신뢰할 수 있음)
5. Dispatching — Domain Event 브로드캐스트 (전달만; 비즈니스 로직 금지)
6. Listener — UI 업데이트, 저장 등 실제 반응 처리 (transport 지식 불필요)

## 단계별 책임 및 금지사항

1. Transport (RESTApiV6 / UnityWebRequest / StreamingDownloadHandler) 책임: 순서, 연결, 재시도, heartbeat 등 전송 프로토콜 금지: 의미 해석, 앱 로직 접근
2. Parsing (IStreamParser) 책임: 형식 해석, 필드 추출, 타입 변환, 스키마 검증 금지: 상태 보유, 누적·완료 판단

2.5) Transport Stream Adapter (Channel Bridge) 책임: Parser 출력물을 async channel로 전달 금지: 의미 해석, 누적, 도메인 이벤트 생성

3. Interpretation + Normalization (IStreamInterpreter / IStreamNormalizer) 책임: provider별 차이 흡수, 공통 정규화 조각(NormalizedPiece) 으로 변환 산출: INormalizedPiece (TextDeltaUnit / ToolCallArgsUnit / ErrorUnit / EndMarkerUnit 등) 금지: 완료 확정(최종 결과 생산 시점 결정), 장기 누적/병합, 앱 로직
4. Aggregation + Domain Event Production (IStreamAggregator) 책임: 3의 조각을 누적/병합, 완료/에러/툴콜 등 도메인 이벤트 생성 산출: TextDeltaEvent, ToolCallEvent, GenerationCompletedEvent, StreamErrorEvent 등 금지: provider 원본 포맷 의존 === From this point on, RESTApiV6 does not intervene ===
5. EventHub (Main Component, implements IEventSink) 책임: 도메인 이벤트를 “받는 단일 진입점”, 필요한 cross-cutting(로그/트레이스/영속/메트릭) 수행 금지: UI/Scene 직접 조작(그건 Listener로)
6. Dispatching (EventHub 내부의 Dispatcher) 책임: 스레드 전환, 큐잉, fan-out(여러 리스너로 브로드캐스트) 금지: 비즈니스 로직, 이벤트 변형
7. Domain Listeners (Pure C#) 책임: 실제 앱 반응(상태전이/저장/UI 모델 업데이트 등) 특징: transport/provider 지식 없음
8. Unity Bridge (MonoBehaviour/UnityEvent) 책임: 7에서 나온 결과를 Scene 오브젝트로 전달(초보자용/UnityEvent 필요 시) 특징: 메인 스레드 강제

## 핵심 포인트 (간추림)

* 역할 분리: transport vs domain을 철저히 분리하면 설계·테스트·로깅이 명확해진다.
* 누적 필요: 하나의 Domain Event는 여러 transport frame에서 조합될 수 있다.
* 용어 엄격성: chunk(transport) / delta(semantic) / content(domain)를 혼동하지 말 것.
* 관점 유지: Event = "발생했다(happened)", SSE = "도착했다(arrived)" — 로그·설계에 반영.

## 스트리밍 종료의 3가지 유형

1. Transport 종료: 연결이 끝남 / 끊김 / 타임아웃
2. Protocol 종료: SSE의 빈 줄, DONE 프레임, event boundary 같은 “포맷 레벨”
3. Domain 종료: finish\_reason, completed 이벤트, messageStop, usage 도착 등 “의미 레벨”

각각 책임이 다르니까 각 단계에서 ‘자기 종료’만 판단하는 게 정답.
