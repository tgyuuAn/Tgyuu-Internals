### LaunchedEffect에 key를 왜 넣나요?

- LaunchedEffect는 리컴포지션마다 호출되는 것이 아니라 UI와 별개로 동작하며 코루틴을 이용한 SideEffect를 위해서 만들어진 Composable
- 이 때 LaunchedEffect에 넣어준 key 값이 변경될 때 마다 LauncehdEffect의 블록이 새롭게 호출되는 방식임
- 즉 특정한 값의 변경을 트리거로 SideEffect를 만들고싶다면 LaunchedEffect에 해당 값을 key로 넣어주면 된다.

<br><br><br>

### Recomposition 횟수를 알 수 있는 방법과 이를 조절하는 방식에는 어떤 것이 있나요?

- LayoutInspetor를 이용해서 리컴포지션 횟수 측정
- 혹은 SideEffect를 이용해서 별도로 stateful하게 측정할 수도 있을 것 같긴한데 굳이 ?!

<br><br><br>

### Compose에서 Stable, Immutable Annotation를 추가하는 이유를 알고 있나요?

- 해당 어노테이션을 사용하지 않을 경우, UI를 그리는 모듈의 외부 모듈과 내부적으로 가변적인 객체가 있을경우 Non-Stable하다고 판단하여 항상 Restartable로 판단하게 됨
- 내부적으로 가변 객체가 있더라도 Copy() 연산을 통해서 값이 바뀐다거나 바뀔일이 없다면 위 어노테이션을 사용함으로써 해당 Composable을 Skippable 하게 만들수 있음.
- @Immutable은 내부에 가변적인 객체가 없으며 모두 val일 경우 사용가능. 이는 해당 객체가 변경될 때 copy()를 통해서만 재할당될 때에만 사용가능
- @Stable의 경우 Immutable처럼 빡빡한 조건은 아니지만 내부적으로 Coompose Snapshot 기능이 있는 mutableState나 SnapShotList 등에 대해서는 가변적인 것도 허용됨 _(값이 변경되도 ComposeRuntime이 바로 파악할 수 있으므로)_
- @Immutable과 @Stable은 위의 방법 외에는 리컴포지션 정보에 대해서 ComposeRuntime이 알 수 없으므로 개발자가 잘 판단해서 사용해야 함.

https://blog.naver.com/tgyuu_/223720324026

<br><br><br>

### CompositionLocal에 대해서 설명해주세요. StaticCompositionLocal과 다른 점은 무엇인가요?

- PropsDrilling 방식으로 데이터를 전달하는 것이 아닌, DI를 할 때 IoC 컨테이너를 사용하는 것처럼 CompositionLocal을 사용하면 하위 컴포저블에서 원할 때 언제던지 해당 값을 가져와서 사용할 수 있음
- StaticCompositionLocal는 가능하면 데이터가 자주 변하지 않는 곳에 사용해야하는데, 왜냐하면 StaticCompositionLocal로 제공하는 값이 변경될 경우 하위 컴포저블 스코프 아래에 있는 모든 컴포저블이 리컴포지션 됨
- 반면 CompsositionLocal은 해당 값을 참조하고 있는 Composable **만** 리컴포지션 되는데, 그렇기 때문에 값이 변할 가능성이 있다면 CompositionLocal을 쓰는 것이 바람직함.
- 그럼 왜 StaticCompositionLocal을 사용하느냐고 반문할 수 있는데, Immutable한 객체를 제공하면 컴포즈 컴파일러 내부적으로 더 공격적인 최적화를 할 수 있으며 Recomposition Tracking을 할 필요가 없기 때문임.
- 또한 테마나 언어 설정의 경우는 하위 모든 Composable이 변경되어야만 하므로 staticCompositionLocal을 사용했을 때 더 원하는 결과가 나올 수 있음.

<br><br><br>

### Compose의 렌더링 과정에 대해서 설명해주세요.

- Mesaurable -> Placeable -> LaidOut
- Measureable 단계에서는 해당 컴포저블의 크기를 측정하며 Placeable에서는 해당 컴포저블이 배치되는 위치를 측정하며 마지막 LaidOut 단계에서 UI를 그려냄
- Measure 단계에서는 Bottom-Up 방식으로 컴포저블을 측정하고, LaidOut 단계에서는 Top-Down 방식으로 UI를 그려나감

<br><br><br>

### 호이스팅에 대해서 설명해주세요.

- Composable은 파라미터를 바탕으로 Compossable이 Skippable일 때 파라미터가 동일한 값일 경우 Skip할 수 있는데, 호이스팅 없이 모두 Root 수준으로 데이터를 올려서 Props Drilling 하게 되면 불필요한 리컴포지션이 많아질 수도 있고 보일러 플레이트 코드가 많이 생길 수 있음
- 또한 외부에서 값을 주입하는 방식으로 UI를 그리게되면 해당 Composable은 stateless하게 그릴 수 있으므로 Preview 및 테스트 코드를 작성하는데도 효율적임.
- 그렇기 때문에 공식문서에서 추천하는 방식은 여러 Composable에서 공유하거나 값을 핸들링해야 할 경우 최소한으로 올려서 상태를 호이스팅하는 방식을 추천함.

<br><br><br>

### Compose의 Composable은 stateless, stateful을 어떤 기준으로 구분하고 적용하셨는 지 설명해주세요.

- State가 빈번하게 변경되지 않을경우 stateless하게 관리하려고 노력하였으며, 만약 내부적으로 값의 변경이 잦거나 props drilling 해야하는 Compose의 트리 계층이 깊다면 내부적으로 stateful하게 값을 hold하고 있는 다음 특정 Intent에서 holding한 값을 ViewModel로 전송하려고 하였음. 
- 이 방식은 보일러 플레이트 코드도 많이 줄일 수 있지만 팀 내에서 stateless와 stateful을 규칙 과 약속 없이 사용하게 되면 디버깅이 힘들어질 수도 있고 Preview나 UI테스트를 작성하는 데 어려워 질 수 있으므로 트레이드 오프를 잘 고려하여 작성해야 함.

<br><br><br>

### 운영체제에 의해서 Process Kill 당했을 때 Compose UI를 복원시킬 수 있는 방법

- remember만 사용하면 Process Kill 당했을 때 Compose UI를 복원할 수 없음.
- 이 때 rememberSaveable을 사용하면 내부적으로 savedStateHandle을 이용해서 Bundle 형태로 데이터를 저장할 수 있으므로 다시 복원하는 시점에서 꺼내서 사용할 수 있음.
- Bundle에 담기지 않는 객체들은 Serilizable이나 Parcelabe이라면 저장할 수 있으나, 아닐 경우 Saver를 사용해야함.
- Saver는 ListSaver와 MapSaver 두 가지 형태가 있고 MapSaver보다 ListSaver가 성능상 약간 더 좋지만 가독성 측면에서는 MapSaver가 더 좋아서 이건 선택의 영역

<br><br><br>

### Compose 아키텍처에 대해서 설명해주세요

- Compose는 ComposeCompiler + ComposeRuntime + ComposeUI로 이루어져있음.
- ComposeCompiler는 개발자가 작성한 @Composable 코드를 ComposeRuntime이 알아듣기 쉽도록 코드를 변경하는 역할을 함.
- ComposeRuntime은 ComposeUI가 UI만 렌더링하면 될 정도로 Compose 내의 최적화와 리컴포지션, 컴포지션 등을 담당함
- ComposeUI는 Android 뿐만 아니라 Web, iOS등을 지원하기 위해 분리된 영역. ComposeRuntime의 Applier와 Node 인터페이스를 구현해서 만들고, 방문자 패턴으로 구현되어 있어서 ComposeUI 구현에 따라 UI가 그려지는 것과 프레임워크 영역이 달라질 수 있음.

<br><br><br>

### ComposeCompiler에 대해서 설명해주세요

- @Composable은 suspend처럼 컴파일되면 해당 함수의 파라미터에 Composer가 생성되고 이는 setContent에서 Recomposer라는것이 생성되고 해당 객체가 Composer를 만들고 CPS스타일과 비슷하게 함수를 타면서 파라미터로 전달됨
- 또한 각 컴포저블은 각각 고유의 ID가 있어서 이를 기반으로 리컴포지션을 조정하거나 Anchor를 이용해서 SlotTable 내부에서 빠르게 인덱스를 변화시킬 수 있음.
- 이 고유 ID는 파라미터, 함수의 호출 순서등을 이용해서 생성되므로 똑같은 함수라도 호출되는 위치가 다르면 다른 Id값을 할당받음.
- ComposeCompiler는 Composer뿐만 아니라 changed라는 Int값을 생성하는데, 이는 리컴포지션에 활용됨. 각 파라미터마다 이전 값과 비교해서 변경되었는 지를 해당 비트 값으로 판단함.

<br><br><br>

### ComposeRuntime의 최적화에 대해서 설명해주세요

- 위에서 설명했던 @Immutable, @Stable 어노테이션과 Compose 내부적으로 Stable을 판별하는 알고리즘을 사용해 Skippable, Restartable을 판별해서 리컴포지션을 스킵하거나 할 수 있음.
- 또한 changed 비트를 이용해서 변경된 파라미터에 대해 부모 컴포저블에서 이미 검사를 완료했으면, 자식 파라미터로 넘겨줄 때 이미 검사했다는 것을 알려줌으로써 해당 파라미터에 대해 추가 검사를 스킵할 수 있음.
- Restartable 이외에도 Moveable, Replaceable을 구분해서 Recomposition에 대해서 공격적으로 최적화 가능
- 각 Composable은 메모제이션 방식을 이용해서 변경되지 않은 Node는 그대로 사용, 재사용할 수 있는 노드는 재사용함.
- 내부적으로 SlotTable이라는 GapBuffer 자료구조를 사용함. SlotTable에는 Groups와 Slots라는 Array 있고 SlotReader와 SlotWriter를 통해서 SlotTable을 읽고 작성함.
- SlotReader가 SlotTable을 순회할 때 무작위 인덱스로 랜덤 접근하는 것이 아니라, 각 그룹마다 Anchor라는 메타데이터로 인덱스를 제공하기 때문에 빠른 접근이 가능함.
- 만약 Skippable Composable에서 skip을 만났다면 다음 컴포저블 Anchor를 이용해서 해당 인덱스로 점프함.
- 람다의 경우에는 외부 값이 Capture되지 않았다면 싱글톤으로 관리해서 최적화를 진행하고, 외부 값을 Capture했다면 해당 값을 remember의 Key로 이용해서 불필요한 객체 생성을 막는 식으로 최적화를 진행

<br><br><br>

### Compose가 Recomposition을 하는 방법에 대해서 설명해주세요.

- Composition과정 

1. SlotReader가 SlotTable을 읽음
2. 변경된 사항이 있으면 SlotBuffer에 기록 _(여기서 말하는 SlotBuffer는 변경 목록임. SlotReader와 SlotWriter는 동시성을 제어하기 위해 동기적으로 진행됨)_
3. SlotReader의 읽기 작업이 끝나면 SlotWriter가 SlotBuffer의 내용을 SlotTable로 반영
4. SlotTable이 업데이트 되면 Applier에 의해서 UI에 가시화가 진행
5. UI가 LaidOut되면 LaunchedEffect나 SideEffect 같은 사이드 이펙트 핸들러가 동작

- 위 작업 이후에 MutableState나 SnapShotFlow 같은 SnapShot 시스템에 의해 Recomposer로 리컴포지션을 트리거 시킴
- 리컴포지션이 트리거 되면 SlotTable에 Anchor로 해당 컴포저블로 접근하고 그 부분부터 SlotReader가 읽기 시작하고 위 과정을 반복
