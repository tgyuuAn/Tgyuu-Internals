### 옵저버 패턴에 대해서 설명해주세요.

- Publisher와 Subscriber가 1:N 의 관계로 있음.
- Subscriber는 Publisher에게 자기 자신을 구독자로 등록하면 Publisher가 메세지를 보낼때 마다 수신가능
- Publisher를 인터페이스로 구현하면 Publisher 구현체 <-> Subscriber는 서로 모르는 상태에서도 데이터를 수신할 수 있음
- EventBus, StateFlow, SharedFlow, 선언형 UI가 옵저버 패턴을 이용해서 구현됨
- MVVM과 MVI에서는 옵저버 패턴을 이용하여 ViewModel -> UI의 의존성을 끊어냄

<br><br><br>

### Repository를 사용하는 이유는 무엇인가요?

- Repository는 데이터 접근 로직을 추상화하여, 도메인 혹은 프레젠테이션 레이어가 데이터 소스의 세부 구현에 의존하지 않도록 만드는 계층
- 이를 통해 ViewModel이나 UseCase는 데이터가 Room, DataStore, Remote API, Firebase 등 어떤 소스에서 오든지 신경 쓸 필요 없이 Repository 인터페이스만 통해 데이터를 요청할 수 있음.
- 클린 아키텍처를 사용할 경우 RepositoryInterface를 Domain에 둠으로써 DIP를 가능하게 하고, 데이터 영역의 변경이나 확장에 유연함을 줌

<br><br><br>

### 클린 아키텍처와 안드로이드 권장 아키텍처(AAC)의 차이점에 대해서 설명해주시고 장단점을 말해주세요.

- 안드로이드 권장 아키텍처는 Presentation -> Domain(Optional) -> Data 계층으로 이루어져있음.
- 그렇기 때문에 Domain이 없을경우 ViewModel은 Data 영역을 직접 의존하게 되고 DomainModel이 없으므로 외부 Api 스키마 변경에 유연하지 못할 수 있음.
- KMP, CMP등 안드로이드 의존성이 불필요한 레이어에 대해서는 Data 영역을 의존하지 못하게 되므로 클린 아키텍처 같이 Domain 영역을 둠으로써 다양한 프레임워크에 확장성을 부여할 수 있음
- CleanArchitecture는 DomainModel이 필수적이고 Data 영역과 Interface로 느슨하게 연결되어있으므로 외부 모듈의 간섭없이 독립된 모듈에서 테스트가 가능하고, Domain 중심으로 코드가 작성되게 됨
- 다만 클린 아키텍처는 DIP를 위해 Domain 영역에 RepositoryInterface가 필수적이므로 보일러플레이트가 약간 생길 수 있음.
- 실제로 안정적인 프로젝트가 아니라 API 스키마가 계속해서 변경되는 환경에서 작업을 하다보니 CleanArchitecture로 앱을 설계하고 Presentation은 도메인에만 의존하게 만들어서 빠르게 개발할 수 있었음.

<br><br><br>

### MVC, MVP, MVVM, MVI 차이점에 대해서 설명해주세요.

- MVC : 사용자의 이벤트를 Controller로 받아서 Model이 갱신, Controller는 갱신된 Model을 이용해서 View의 UI를 그리는 메소드를 호출해서 UI를 갱신해줌. 그렇기 때문에 View는 Model을 알아야하며, Controller는 View와 Model을 다 알아야 하는 구조.
- MVP : MVC에서 View-> Model 의존을 끊기 위해 가운데 Presenter를 둠. Presenter는 인터페이스로 구현되어 View <-> Presenter의 의존을 느슨하게 만듬. 결국 View -> Model 의존을 끊을 수 있음
- MVVM : View <-> Presenter의 의존에서 옵저버 패턴을 도입해서 View -> ViewModel 구조로 변경. ViewModel은 View를 몰라도 되고, Presenter, Controller에서 수행하던 UI 메서드를 호출하는 로직을 View로 이동시켜서 책임을 View로 덜어냄. View 레이어는 옵저빙된 데이터로 UI를 그려내기만 하면 됨
- MVI : MVVM에서 각각 있는 State를 하나의 UiState로 묶어내고, 사용자는 Intent를 순차적으로 Reducer에 보내서 Model을 업데이트 하며, View에서는 갱신된 Model을 옵저빙하고있다가 그려내기만 하면됨. 하나의 UiState만 옵저빙하면 된다는 점과 UiState의 변경이 동기적으로 수행된다는 점에서 선언형 UI와 잘 맞아 떨어짐.

<br><br><br>

### MVI를 사용할 때 Orbit, Circuit, Mavericks 등과 같은 프레임워크가 필요하다고 생각하시나요?

- 팀 내 컨벤션에 따르는게 좋을 것 같음. 다만 프레임워크에서 제공하는 기능이 크지 않다면 굳이 사용하지 않는 것이 더 좋다고 생각함.
- 이전 프로젝트에서 Mavericks를 도입했었지만 프레임워크에서 제공하는 기능이 별로 많지 않았고 오히려 코틀린 언어로 작성된 코드가 더 이해하기 쉽다고 판단해서 다시 마이그레이션 한 경험이 있음.
- 하지만 이미 팀 내에서 결정된 사항이라면 새로운 팀원이 그에 적응하는 것이 더 비용이 쌀 것이라고 생각되고, 언어로만 구현된 사항에 대해서는 구현하는 방법에 따라서 코드가 달라지므로 프레임워크에서 제공하는 API에 맞춰서 개발하는 것이 더 비용이 쌀 수도 있기 때문에 그것을 따르는 것도 좋다고 생각함.

<br><br><br>

### MVVM에서도 MVI처럼 State들을 Ui Holder에 담아서 하나의 파라미터로 사용하면 되지 않나요? 이 방식을 사용할 때 MVVM과 MVI의 차이점은 무엇인가요?

- MVVM에서는 기본적으로 각 State를 개별 StateFlow로 관리, MVI에서는 하나의 UiState로 관리
- MVVM에서는 각 State마다 업데이트 로직이 따로 들어가지만 MVI에서는 하나의 UiState에 대해서 동기적으로 갱신됨
- MVVM에서도 UiState를 사용할 수 있음. 하지만 UiState를 갱신하는 로직은 각기 다르게 병렬로 수행되어서 UiState의 갱신이 RaceCondition에 의해 씹힐 가능성이 있음.

<br><br><br>

### MVP는 메모리 누수가 날 위험이 있는데 어떤 경우에 메모리 누수의 위험이 있을까요?

- View(Activity) 는 소멸되었지만 Presenter에서 View를 Hold하고 있을 경우 View는 GC되지 못해서 계속해서 메모리 누수가 될 가능성이 있음.
- 이를 방지하기 위해서 View를 WeakReference로 참조하거나 View의 onDestroy()시점에서 Presenter에서 View 참조를 제거해줘야 함.