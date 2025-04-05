### 코루틴이 스레드에 비해서 경량인 이유에 대해서 설명해주세요.

- 코루틴은 별도의 스레드를 만들지 않고 Continuation와 CPS 스타일을 이용해서 메모리 영역에서 Context 정보를 저장하기 때문에 Context Switching 비용이 매우 경미
- Dispatcher를 이용해서 일정 양의 스레드를 가지는 스레드 풀을 만들고 스레드를 생성하고 소멸시키고를 반복하지 않고 만들어진 스레드를 재사용

<br><br><br>

### 코루틴 Job의 Cancel 과정에 대해서 설명해주세요.

- 코루틴의 Job은 Cancel 되었다고 바로 취소되는 것이 아니라, 다음 suspend 지점을 마주해야지만 Cancel될 수 있음
- 그렇기 때문에 내부적으로 CPU작업이 많다고 하면 중간중간에 yield를 찍거나 isActive 조건을 이용해서 suspend 지정믈 만들어줘야 함

<br><br><br>

### launch와 async의 차이점에 대해서 설명해주세요.

- launch는 폭죽을 쏘는 것과 같이 return 값이 없는 행위에 대해서 병렬 및 동시적으로 작업하기 이해 사용. Job객체가 생성되며 동기를 맞추기 위해서 join()을 사용
- aysnc는 launch와 비슷한데 return 값이 있는 작업에 대해서 병렬적으로 작업. Job객체를 상속받는 Deferred 객체가 생성되며 동기를 맞추기 위해서 wait()을 사용
- async의 경우 wait()하는 시점에서 에러가 터질 수 있으므로 Result나 try-catch로 잡아줘야 될 수도 있음

<br><br><br>

### 그러면 코루틴은 스레드를 사용하지 않나요?

<br><br><br>

### 코루틴 스케줄러에 대해서 아는대로 말해주세요.

<br><br><br>

### Continutaion Passing Style에 대해서 설명해주세요.

<br><br><br>

### 코루틴을 지원하지 않는 레거시 코드를 코루틴을 이용한 비동기로 받기 위해서 사용하는 방법에는 어떤 것이 있는지 설명해주세요.

<br><br><br>

### 웹소켓과 같이 특정 기능 모듈에 종속되지 않고 화면이 전환되도 계속해서 데이터를 수집해야할 때에는 어떻게 할 수 있는 지 설명해주세요.

<br><br><br>

### coroutineScope와 withContext의 차이점에 대해서 설명해주세요.

<br><br><br>

### Dispatcher의 종류와 그에 대해서 설명해주세요.

<br><br><br>

### Dispatcher.Default 쓰지말고 스레드 개수를 많이 사용하면 더 좋은거 아닐까요?

<br><br><br>

### 개발하면서 코루틴의 비동기를 지원하지 않는 라이브러리를 사용해서 withContext로 컨텍스트 전환이 필요했던 경험이 있나요?

<br><br><br>

### ViewModelScope는 내부적으로 어떻게 구현있나요?

<br><br><br>

### Supervisor에 대해서 설명해주세요.

<br><br><br>

### 코루틴의 Dispatcher와 Java의 Excutor의 차이점에 대해서 설명해주세요.

<br><br><br>

### Java의 가상스레드와 코루틴을 비교해주시고 장단점을 설명해주세요.
