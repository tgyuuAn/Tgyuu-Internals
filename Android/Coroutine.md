### 코루틴이 스레드에 비해서 경량인 이유에 대해서 설명해주세요.

- 코루틴은 별도의 스레드를 만들지 않고 Continuation와 CPS 스타일을 이용해서 메모리 영역에서 Context 정보를 저장하기 때문에 Context Switching 비용이 매우 경미
- Dispatcher를 이용해서 일정 양의 스레드를 가지는 스레드 풀을 만들고 스레드를 생성하고 소멸시키고를 반복하지 않고 만들어진 스레드를 재사용

<br><br><br>

### 코루틴 Job의 Cancel 과정에 대해서 설명해주세요.

- 코루틴의 Job은 Cancel 되었다고 바로 취소되는 것이 아니라, 다음 suspend 지점을 마주해야지만 Cancel될 수 있음
- 그렇기 때문에 내부적으로 CPU작업이 많다고 하면 중간중간에 yield를 찍거나 isActive 조건을 이용해서 suspend 지정믈 만들어줘야 함

https://blog.naver.com/tgyuu_/223335112559
https://blog.naver.com/tgyuu_/223335786631

<br><br><br>

### launch와 async의 차이점에 대해서 설명해주세요.

- launch는 폭죽을 쏘는 것과 같이 return 값이 없는 행위에 대해서 병렬 및 동시적으로 작업하기 이해 사용. Job객체가 생성되며 동기를 맞추기 위해서 join()을 사용
- aysnc는 launch와 비슷한데 return 값이 있는 작업에 대해서 병렬적으로 작업. Job객체를 상속받는 Deferred 객체가 생성되며 동기를 맞추기 위해서 wait()을 사용
- async의 경우 wait()하는 시점에서 에러가 터질 수 있으므로 Result나 try-catch로 잡아줘야 될 수도 있음

https://blog.naver.com/tgyuu_/223308250175

<br><br><br>

### 그러면 코루틴은 스레드를 사용하지 않나요?

- 워커 스레드를 사용해야할 때나 병렬적으로 작업을 해야할 때, Dispatcher 전환이 될 때, 코루틴 스케줄러에 의해 내부적으로 Runnable 객체로 감싸져서 Excutor로 보내짐

<br><br><br>

### 코루틴 스케줄러에 대해서 아는대로 말해주세요.

- Dispatcher는 스레드 풀을 가지며 코루틴을 어떤 스레드에서 실행시킬 지 결정하는 역할을 하는 객체
- Dispatcher 내부에 코루틴 스케줄러가 존재하며 실질적인 Dispatcher의 역할을 Scheduler가 수행함
- 코루틴 스케줄러 내부에는 CPUQueue와 BlockingQueue가 존재하는데, CPUQueue는 CPU Bound Job, BlockingQueue는 IO Bound Job을 저장함
- 내부적으로 2개의 Global 큐 (CPUQueue와 BlockingQueue)를 사용하는 이유는 IO Blocking 작업을 수행하는 워커 스레드에서는 계속해서 IO 작업을 수행하게하기 위해서임
- IO 작업을 수행하는 워커 스레드에 계속해서 IO 작업을 수행시키는 이유는 IO 작업의 경우 CPU Blocking 시간이 매우 짧기 때문에 최대한 병렬적으로 IO 작업을 최소한의 개수의 스레드에서 수행할 수 있기 때문 _(Dispatcher.Default로 보내진 작업은 CPUQueue, Disaptcher.IO로 보내진 작업은 BlockingQueue로 보내짐)_
- 추가적으로 각각의 워커 스레드는 로컬 큐를 가지며, 로컬 큐를 먼저 확인하고 작업이 없을 경우 글로벌 큐를 확인, 글로벌 큐도 비었을 경우 다른 워커 스레드의 로컬 큐를 확인하여 워크 스틸링 방식으로 효율적으로 수행.

<img src="resource/image-8.png" alt="alt text" width="500" />

https://myungpyo.medium.com/%EC%BD%94%EB%A3%A8%ED%8B%B4-%EB%94%94%EC%8A%A4%ED%8C%A8%EC%B3%90-%EC%A1%B0%EA%B8%88-%EB%8D%94-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0-92db58efca24

<br>

++ 추가적으로 BlockingQueue는 내부적으로 3가지 동기화 관리 포인트가 있음

1. 생산자의 생산 (여러 생산자가 있을 경우 생산하는 입장에서 데이터가 유실될 수 있으므로 동기화)
2. 소비자의 소비 (여러 소비자가 있을 경우 같은 작업을 가져갈 수 있으므로 소비할 때 동기화)
3. 생산자-소비자 간의 동시성 관리 (Task Queue가 가득 찼을 경우 소비자가 소비를 해야만 생산자에서 Push를 할 수 있으므로 하는 동기화)

<br><br><br>

### Continutaion Passing Style에 대해서 설명해주세요.

- 코루틴은 내부적으로 launch나 Dispatcher 전환을 제외하고, 동일한 스레드에서는 Continuation 객체를 이용해서 힙 메모리에 컨텍스트 정보를 저장해감.
- Decorator 패턴과 매우 유사하지만 객체를 래핑하는 것은 아니고, 다움 Suspend 함수를 지날 때 마다 Continuation을 래핑하는 방식으로 동작하는 방식임. (Continuation Chaning)

https://blog.naver.com/tgyuu_/223290411871

<br><br><br>

### 코루틴을 지원하지 않는 레거시 코드를 코루틴을 이용한 비동기로 받기 위해서 사용하는 방법에는 어떤 것이 있는지 설명해주세요.

- Firebase를 사용할 때에 Return값으로 값이 오는 것이 아니라 콜백으로 데이터를 받기 때문에 일시적인 데이터는 suspendCancellableCoroutine을 이용. _(일회성 콜백)_
- RealtimeDataBase같이 지속적으로 데이터가 내려오는 형식이라면 CallBackFlow를 이용해서 지속적인 콜백 데이터를 Flow 파이프라인을 통해 받아올 수 있음 _(지속성 콜백)_
- SharedPreference의 경우 WithContext(Disaptcher.IO) 와 같은 방식을 사용해서 워커 스레드에서 동작하게 만들 수도 있음. _(단일 요청)_

<br><br><br>

### 웹소켓과 같이 특정 기능 모듈에 종속되지 않고 화면이 전환되도 계속해서 데이터를 수집해야할 때에는 어떻게 할 수 있는 지 설명해주세요.

- 다른 기능 모듈에서 데이터를 Collect하더라도 지속적으로 웹소켓 데이터를 수신하게 하려면 커스텀 싱글톤 코루틴 스코프를 정의해서 Data 레이어에서 받고있는 방식으로 사용할 수 있음.
- Activity의 onStart에서 웹소켓은 연결하고 onStop에서 웹소켓을 해제하는 등의 전략을 선택할 수 있을 것임.

<br><br><br>

### coroutineScope와 withContext의 차이점에 대해서 설명해주세요.

- coroutineScope나 withContext는 viewModelScope나 lifecycleScope같이 코루틴 스코프를 만들 수 없는 환경에서 launch나 async와 같은 코루틴 빌더를 사용하기 위해서 사용함.
- withContext를 사용하면 현재 사용하던 Dispatcher에서 다른 Dispatcher로 전환할 수도 있고, CoroutineExceptionHandler나 SupervisorJob과 같이 다른 코루틴 Context도 삽입해줄 수 있음.
- coroutineScope는 withContext(emptyContext())와 동일하게 동작하며 이전에 사용하던 Coroutine Context를 사용하면서 코루틴 빌더를 사용할 수 있도록 CoroutineScope 리시버를 제공해주는 블록을 만들어줌.
- 둘 다 내부적으로 suspend하게 동작함. (해당 스레드 내부에서 동기적으로 동작)

https://blog.naver.com/tgyuu_/223376649456
https://blog.naver.com/tgyuu_/223376678981

<br><br><br>

### Dispatcher의 종류와 그에 대해서 설명해주세요.

각 Dispatcher는 모두 스레드 풀에서 일정한 수의 스레드를 생성하고 재사용하는 방식을 사용.

- Dispatcher.Default : CPU Bound 작업을 수행하기 위해 만들어진 Dispatcher. 내부적으로 CPU 코어 개수만큼의 스레드를 관리 (CPU 작업을 처리할 때 코어 개수만큼 병렬작업을 처리할 수 있기 때문)
- Dispatcher.IO : IO Bound 작업을 수행하기 위해 만들어진 Dispatcher. 내부적으로 64개의 코어 개수만큼의 스레드를 관리 (limitedParellism을 통해 스레드 개수를 더 늘리거나 줄일 수 있음)
- Dispatcher.Main : UI 스레드에서 동작하는 작업을 관리하기 위해 만들어진 Dispatcher. viewModelScope나 lifecycleScope가 내부적으로 Dispatcher.Main과 SupervisorJob을 사용함
- Dispatcher.Unconfined : 현재 호출한 스레드에서 시작, 첫 suspend 지점 이후에는 재개 지점의 스레드에서 실행디는 Dispatcher. Dispatcher는 고정되어 있지 않고 컨텍스트에 따라 자유롭게 실행되며, 주로 테스트 환경이나 스레드 보존이 필요없는 환경에서 컨텍스트 스위칭 비용이 없으므로 특정한 조건에서는 좋은 선택이 될 수 있음.

https://blog.naver.com/tgyuu_/223332521125

<br><br><br>

### Dispatcher.Default 쓰지말고 스레드 개수를 많이 사용하면 더 좋은거 아닐까요?

- 코루틴을 사용하더라도 launch나 withContext같이 Dispatchdr 전환이 필요할 때에는 내부적으로 Excutor를 이용해서 Continuation을 Runnable로 감싸서 수행하기 때문에 스레드 개수가 많으면 ContextSwitching 비용이 불필요하게 커질 수 있음.

https://blog.naver.com/tgyuu_/223332521125

<br><br><br>

### 개발하면서 코루틴의 비동기를 지원하지 않는 라이브러리를 사용해서 withContext로 컨텍스트 전환이 필요했던 경험이 있나요?

- SharedPreference를 사용할 때 withContext를 이용했던 경험이 있음. DataStore는 내부적으로 비동기 작업을 지원하기 때문에 불필요.

<br><br><br>

### viewModelScope는 내부적으로 어떻게 구현있나요?

- viewModelScope는 내부적으로 Dispatchers.Main.immediate와 supervisorJob을 사용.
- SuperVisorJob을 사용하는 이유는 하나의 작업에서 예외가 발생하더라도 다른 코루틴 작업은 영향을 받으면 안되고 viewModelScope를 게속해서 Active한 상태로 만들어두기 위함
- Dispatcher.Main을 사용하는 이유는 코루틴 내부적으로 LiveData나 StateFlow를 사용하는데 이러한 값들은 스레드 안전성을 보장하지 않기 때문에, 여러 Dispatcher(IO, Default 등)에서 동시에 값을 변경하면 Race Condition이 발생할 수 있기 때문임.

https://blog.naver.com/tgyuu_/223530860419

<br><br><br>

### Supervisor에 대해서 설명해주세요.

- Suprevisor를 사용하면 자식 코루틴에서 예외가 발생하더라도 동일한 Suprevisor의 다른 자식 코루틴으로 예외 전파를 막을 수 있음.

https://blog.naver.com/tgyuu_/223352857665

<br><br><br>

### 코루틴의 Dispatcher와 Java의 Excutor의 차이점에 대해서 설명해주세요.

- 코루틴의 Dispatcher는 내부적으로 launch나 asynch 같이 병렬 및 동시 요청을 하지 않거나 withContext로의 디스패쳐 전환이 없으면 동일한 스레드에서 Continuation을 이용하여 Context를 유지 되며, suspend가 되면 thraed가 non-blokcing 됨
- 위 과정에서 만약 Dispatcher 전환이나 병렬 및 동시 요청을 할 경우 Continuation을 Runnable로 감싸서 Excutor를 이용해 Worker 스레드로 배치시킴
- Java의 Excutor는 내부적으로 스레드 풀을 만들고 관리하는데, 어떠한 작업이든 Blocking으로 동작하기 때문에 코루틴보다 무거움.

<br><br><br>

### Java의 가상스레드와 코루틴을 비교해주시고 장단점을 설명해주세요.

- Java의 가상스레드는 OS수준의 스레드 까지 내려가는 것이 아니라 Application 수준에서만 스레드를 관리하므로 실제 스레드를 사용하는 것 보다 가벼움
- 그럼에도 Context Switching 비용은 존재하며 suspend시 스레드가 blocking되는 것은 동일하기 때문에 Corouitne에 비해서 IO 작업은 무거움
- 하지만 CPU Bound 작업은 코루틴에 비해 미세하게 빠르거나 비슷하게 사용할 수 있다고 알려져있음. (코루틴이 더 가벼운 것 같은데..)