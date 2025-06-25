### LiveData, StateFlow는 Polling 방식인가요 인터럽트 방식인가요?

- LiveData와 StateFlow, SharedFlow는 내부적으로 옵저버 패턴을 구현한 Hot Flow 방식임.
- 그렇기 때문에 계속해서 값의 변경을 관찰하는 것 보다는 특정 객체의 값이 변경되었을 때 알림을 주는 인터럽트 방식이라고 할 수 있음.

<br><br><br>

### Viewmodel의 장점은 어떤 것이 있나요?

- Activity보다 오래 살아남기 때문에 Configuration Change에도 데이터를 보존할 수 있음.
- 메모리 부족으로 인해 시스템 Kill을 당하더라도 savedStateHandle을 사용해서 데이터를 재요청할 수 있음.
- 아키텍처적으로 MVVM이나 MVI를 구성하는데 데이터 홀더 역할로 사용할 수 있음.
- 내부적으로 ViewModelScope를 제공해주기 때문에 손쉽게 코루틴 빌더를 이용할 수 있음.
- LiveData나 StateFlow등 옵저버 패턴을 사용하는 객체를 이용함으로 써 UI와 비즈니스 의존성을 분리할 수 있음

https://blog.naver.com/tgyuu_/223603685994

<br><br><br>

### ViewModel에 Context 사용을 지양하는 이유는 무엇인가요?

- ViewModel에서 Context를 의존하게 되면 UI와 비즈니스 로직 의존성 분리의 경계가 모호해짐
- ViewModel이 Activity보다 오래 살아남기 때문에 ActivityConetxt 등을 사용할 경우 메모리 누수 가능성이 있음
- Cotext를 객체에서 사용하는 순간 테스트를 하기 어려운 구조로 바뀜 (일반 단위 테스트에서 Android Test로 바뀜)

<br><br><br>

### ViewModel이 어떻게 Configuration Changes에서도 살아남는 지 설명해주세요.

- Activity, Fragment, Navigation은 ViewModelStoreOwner 인터페이스가 구현되어 있음.
- Fragment는 FragmentManager 내부, Navigation은 NavigationBackStackEntry에 있음.
- ViewModelStoreOwner는 NonConfiguration 객체에 저장되므로 Activity, Fragment, Navigation이 소멸되더라도 살아 남아있음.
- 이후 사용자가 직접 명시한 finish()가 아니라면 ViewModelProvider를 이용해 ViewModel을 꺼내서 재사용

https://blog.naver.com/tgyuu_/223603685994

<br><br><br>

### ViewModel은 Android 프레임워크 의존성을 가지고 있는데, 테스트 할 때 AndroidTest가 아니라 일반 Test에 작성이 가능한 이유는?

- ViewModel을 단위 테스트할 때에는 프레임워크 생명주기를 따르지 않고 ViewModel 객체만 테스트 하므로 AndroidTest가 아니어도 됨
- 일반 Test에서 ViewModel에 파라미터를 넣은 채로 ViewModel을 생성할 수 있는 이유는, Activity와 같은 ViewModelOwner가 ViewModelProvider를 이용하는 과정이 없으므로 크래시가 나지 않음
- Activity나 Fragment에서는 ViewModelProvider를 이용해서 ConfigurationChange가 일어나더라도 똑같은 인스턴스를 반환받아야 하므로 ViewModelFactory가 없으면 크래시가 나게됨.

<br><br><br>

### HotFlow와 ColdFlow의 차이점에 대해서 설명해주세요.

- HotFlow는 구독자와 무관하게 데이터 생성 명령을 받으면 생성 후 값을 방출, 값을 공유 ex) LiveData, SharedFlow, StateFlow
- ColdFlow는 구독자가 구독을 시작했을 때 값을 방출하며 각 구독자마다 값을 공유하지 않고 각자 처음부터 모든 데이터를 전송 ex) 일반 Flow

<br><br><br>

### 페이징을 구현하였다면 Paging3 라이브러리를 사용하였는지, 사용했다면 사용하지 않고도 구현할 수 있는데 사용한 이유? 사용하지 않았다면 라이브러리를 사용하지 않은 이유는 무엇인가요?

- 페이징을 직접 구현했다면 : 페이징 라이브러리를 사용하지 않더라도 직접 구현할만한 코드라고 생각하여서 구현. 더욱이 Paging을 사용하게 되면 Repository도 페이징 라이브러리에서 제공해주는 것을 사용해야 하기 때문에 Domain Layer에 안드로이드 의존성에 오염될 수 있음

- 페이징 라이브러리를 사용했다면 : Paging라이브러리를 사용하면 Domain Layer에 안드로이드 의존성이 생길 수 있지만 그 부분을 고려하여 설계된 test 버전 라이브러리(paging-common)를 사용하면 안드로이드 의존성 없이 사용할 수 있었음. 또한 라이브러리 내부적으로 DiffUtil이 정의되어 있어서 xml을 사용한다면 세심한 디테일 고려없이 편하게 사용할 수 있었음.

<br><br><br>

### SavedStateHandle에 대해서 아는대로 설명해주세요.

- SavedStateHandle은 Android에서 데이터를 저장하는 방법 중 하나로서 주로 메모리를 과도하게 사용해서 운영체제에 의해 프로세스 메모리를 수거당했을 때 복원하는 용도로 사용함.
- 위 경우 Bundle에 데이터를 저장하므로 앱에 돌아왔을 때 Bundle 데이터를 바탕으로 Local이나 외부 API 호출을 통해서 데이터를 재요청 할 수 있음
- 혹은 Jetpack Navigation을 사용할 때 내부적으로 값을 공유할 때 Bundle을 이용해서 값을 공유하므로 SavedStateHandle을 사용한다면 바로 Navigation 도착지 화면의 ViewModel에서 값을 꺼내서 사용할 수 있음.

<br><br><br>

### 앱이 시스템에 의해 종료되었을 때, SavedStateHandle 데이터는 메모리에 저장되는데 어떻게 복원될 수 있나요?

- SavedStateHandle에 의해 저장되는 데이터는 메모리에만 저장되는 것이 아니고, 내부적으로 Android의 SavedStateRegistry를 통해 Bundle 형태로 직렬화된 데이터를 임시적으로 디스크에 저장하기도 함. (앱이 켜져있는 상태라면 메모리에 저장, 종료 직전이거나 Configuration Change 발생 시에는 디스크에 저장)
- 하지만 영속성은 없는데, Android System에 의해서 관리되고 앱이 완전히 종료되거나 시스템 리소스 부족하면 제거될 수도 있음.

<br><br><br>

### 로컬에 데이터를 저장한다면 Room과 DataStore(SharedPreference)를 어떤 기준으로 사용했는 지 말해주세요.

- 토큰이나 사용자 설정과 같이 단일 Key-Value쌍을 저장할 때에는 DataStore 사용
- 단일 객체를 캐싱하는 용도로는 DataStore + Gson(KotlinXSerialization)을 사용해서 하나의 Key를 사용하여 객체를 캐싱할 수 있도록 구현
- 만약 채팅과 같이 쿼리가 필요한 데이터의 경우에는 Room을 이용해서 저장 (Room의 경우는 DataStore에 비해 DAO 및 쿼리, 버전 관리의 관점에서 관리가 복잡함)
