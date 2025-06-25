### Dagger에서 Component와 Module의 차이는 무엇인가요?

- Component는 직접 DI가 되어지는 IoC 컨테이너 역할. 어떤 객체를 어디에 주입할 지 결정
- Module은 Component에게 객체 생성 방식을 알려주는 역할. 예를 들면 인터페이스의 구현체를 지정 해주거나 외부 라이브러리의 생성 방식, 동일 타입을 분기할 때 사용

<br><br><br>

### ActivityComponent와 SingletonComponent 차이는 무엇인가요?

- ActivityComponent는 객체의 수명이 Activity로 한정됨. 즉, 각 Activity마다 별도의 인스턴스를 생성해서 주입시켜 줌.
- SingletonComponent는 객체의 수명이 Singleton으로 설정됨, 즉, 어디에서 DI를 사용하던 동일한 객체를 주입해줌.

<br><br><br>

### DI를 왜 사용하나요 ?

- Viewmodel -> UseCase -> Repository -> DataSource 와 같이 객체 의존성이 체이닝 되어있는 경우 결국 객체를 어디선가 생성해야 하는데 이 부분은 코드가 깔끔하지 못할 수 있음. 이 때 IoC 컨테이너를 사용해서 객체의 생성을 담당하고 실제로 객체를 사용하는 객체에서는 의존성을 주입받아서 해당 객체가 어떤 방식으로 생성되었는 지를 신경쓰지 않고 사용할 수 있음
- Android의 경우 Hilt를 사용하면 Compile 단계에서 객체 생성 및 주입을 다 해주므로 보일러 플레이트를 상당히 많이 줄일 수 있음.
- 특정 인터페이스에 대해서 런타임에 다형성을 주기 위해서 사용하기도 함. Hilt의 경우는 컴파일 단계에서 의존성 주입이 완료되지만 동적으로 setXXX을 호출하여 런타임에 다형성을 형성할 수 있음.

<br><br><br>

### DI를 할 수 있는 방법에 대해서 아는대로 소개해주세요.

- 생성자 주입 : 객체의 생성 시점에서 DI를 수행. 런타임에 다형성을 형성할 수 없는 단점이 있음
- 지연 주입 : 객체 생성 이후 setXXX 를 호출하여 DI를 수행할 수 있음. 런타임에 동적으로 다형성을 형성할 수 있지만, 객체 주입 이전에 객체 사용시 NullPointerException 에러를 야기할 수도 있음.
그렇기 때문에 생성자 주입 + 지연 주입의 형태로 많이 사용
- 메소드 전달 : 굳이 객체의 프로퍼티로 DI를 하지 않더라도 메소드에 객체를 전달하는 방식으로도 사용할 수 있음. 다만 자주 사용되는 객체라면 이 방법 보다는 위 2가지 방법을 통해서 하는 것이 바람직 할 수 있음.

<br><br><br>

### App 모듈에 모든 DI 모듈을 넣지않고 각 모듈에 넣을 수 있나요?

- Hilt는 컴파일 시점에 AppComponent부터 모든 모듈 의존 그래프를 추적하기 때문에 App 모듈이 직접 또는 간접적으로 의존하고 있는 모든 모듈을 컴파일 시점에 스캔하여 DI 그래프를 구성할 수 있음.

<br><br><br>

### Hilt는 컴파일 할 때 어떻게 DI되는 지 설명해주세요.

- App모듈로부터 직접 의존 및 간접 의존 하고 있는 모듈의 Hilt 모듈을 탐색하면서 DI 그래프를 그려나감.
- SingletonComponent의 경우 Hilt_Application, ActivityComponent의 경우 Hilt_XXXActivity와 같이 주입하려는 객체를 감싸는 Hilt_XXX 이라는 IoC 컨테이너가 생성되며 주입됨.

<br><br><br>

### Hilt를 사용할 때 동일한 타입을 DI할 때 사용할 수 있는 방법에 대해서 설명해주세요.

- @Named 어노테이션을 사용해서 분기 해주려는 객체별로 이름을 지정해주거나, @Qaulifier을 이용해서 어노테이션 자체로 분기해주는 방법을 설정한 뒤 Module에 알려주는 방식으로 분기해줄 수 있음.

<br><br><br>

### Hilt와 Koin의 차이점에 대해서 설명해주세요.

- Hilt는 컴파일 시점에 코드를 생성하는 방식으로 DI되기 때문에 컴파일 시점에 에러를 잡을 수 있으므로 런타임에 안전함. 내부적으로 리플렉션이 아니라 코드 젠 방식으로 DI 그래프를 만들기 때문에 속도도 빠름. 자동 주입
- Koin은 엄연히 말하면 DI 보다는 Service Locator 방식이며 리플렉션 방식을 사용함과 동시에 런타임에 명시적으로 주입해주어야 함. 그리고 개발자가 직접 DI를 해줘야 하는 번거로움이 있음. 다만 Hilt는 Android 프레임워크 의존성이 있으므로 KMP에서 Android 프레임워크 부분에서만 사용할 수 있다는 단점이 있어서 Koin을 택하기도 하는 추세임. 근데 이거 조만간 Hilt에서도 KMP 지원한다고 하네욥

<br><br><br>

### Provides와 Binds의 차이점에 대해서 설명해주시고, 프로젝트에 적용할 때 어떤 기준으로 적용했는 지 설명해주세요.

- @Binds는 인터페이스를 DI 시켜주려고 할 때 객체를 지정해주는 방식에 주로 사용. @Binds를 사용하면 객체 생성에 필요한 객체들을 알아서 주입해주므로 보일러 플레이트 코드가 많이 감소
- @Binds의 경우 디버그/릴리즈 모드 분기와 같이 다형성을 줘야하는 부분에서는 제약이 있음.
- @Provides는 인터페이스와 외부 라이브러리의 객체를 생성하는 방식을 알려줄 때 주로 사용. 외부 라이브러리는 @Binds를 통해서 생성 방법을 알려주지 못하기 때문에 이 경우 어쩔 수 없이 @Provides 사용. Provides의 경우에는 해당 객체를 생성하는 데 필요한 객체들을 일일이 명시해주어야 하는 단점이 있음.

그렇기 때문에 프로젝트 내부의 인터페이스를 생성할 때에는 보일러 플레이트를 감소하는 측면에서 @Binds를 사용하고 외부 라이브러리를 생성하는 방식을 알려줄 때나 Hilt 모듈에서 다형성을 형성해야 할 경우 어쩔 수 없이 @Provides를 채택.

<br><br><br>

### @HiltViewModel, @AndroidEntryPoint

- Hilt를 사용하지 않을경우, ViewModel에 생성자를 넣어주려면 ViewModelFactory를 이용해야 함.
- @HiltViewModel을 사용하면 컴파일 시점에 파라미터를 주입할 수 있도록 Factory를 알아서 생성해줌.
- Activity나 Fragment의 경우는 안드로이드 프레임워크에 의해 개발자가 직접 해당 객체를 인스턴스화 할 수 없는데, 그렇기 때문에 지연 주입을 사용하기 위해서는 @AndroidEntryPoint를 반드시 사용해야 함.

<br><br><br>

### AssistedInject를 사용하면 어떤 장점이 있나요?

- 객체를 컴파일 시점에 생성하는 것이 아니라 런타임 시점에 필요한 파라미터를 이용해서 지연 생성시킬 수 있음.
- ViewModel의 경우 AssistedInject를 사용하려면 @HiltViewModel 어노테이션을 제거하고 @AssistedFactroy를 이용해서 Hilt에게 ViewModel을 생성하는 방식을 알려줘야 함.
- 위에서 설명했듯이 @HiltViewModel의 경우 컴파일 시점에 Factory 코드를 생성해서 알아서 주입을 해주는데, 동적으로 주입되는 파라미터가 있을 경우 해당 파라미터를 받는 팩토리를 정의한 뒤, 나머지 코드는 알아서 생성하도록 하는 것임.
- SavedStateHandle 또한 런타임 파라미터 이므로, 예전에는 @Assisted로 주입해줘야 했지만 라이브러리가 업데이트됨에 따라 현재는 SavedStateHandle에 @Assisted는 필요 없어짐.
- 현재는 대부분 SavedStateHandle로 데이터를 가져오므로 필요없지만, NavigationArgument로 전달되는 인자가 아니거나 직렬화/역직렬화가 어렵거나 시간이 오래 걸린다면 @Assisted를 고려해볼만 함

<br><br><br>


### viewModel()와 hiltViewModel()의 차이

lifecycle.compose() 에서 제공해주는 `viewModel()`은 아래와 같이 구현.

```kotlin
package androidx.lifecycle.viewmodel.compose

@Composable
public inline fun <reified VM : ViewModel> viewModel(
    viewModelStoreOwner: ViewModelStoreOwner = checkNotNull(LocalViewModelStoreOwner.current) {
        "No ViewModelStoreOwner was provided via LocalViewModelStoreOwner"
    },
    key: String? = null,
    factory: ViewModelProvider.Factory? = null,
    extras: CreationExtras = if (viewModelStoreOwner is HasDefaultViewModelProviderFactory) {
        viewModelStoreOwner.defaultViewModelCreationExtras
    } else {
        CreationExtras.Empty
    }
): VM = viewModel(VM::class.java, viewModelStoreOwner, key, factory, extras)
```

- ViewTreeViewModelStoreOwner.current를 기준으로 ViewModel을 생성하는데, 대부분 Activity나 Fragment임.
- HiltViewModel을 사용하고 있다면 ViewModel의 파라미터를 ViewModelFactory를 만들어서 넘겨줘야 함.
- NavHost 내부에서 호출한다면 NavBackStackEntry, 아니라면 ViewModel()과 같음.

<br><br><br>

- HiltViewModel의 경우는 NavBackStackEntry가 있다면 NavBackStackEntry에 ViewModelStoreOwner로 설정한 뒤 hiltViewModelFactroy를 호출, 그게 아니라면 viewModel()과 동일하게 동작

```kotlin
@Composable
inline fun <reified VM : ViewModel> hiltViewModel(
    viewModelStoreOwner: ViewModelStoreOwner = checkNotNull(LocalViewModelStoreOwner.current) {
        "No ViewModelStoreOwner was provided via LocalViewModelStoreOwner"
    }
): VM {
    val factory = createHiltViewModelFactory(viewModelStoreOwner)
    return viewModel(viewModelStoreOwner, factory = factory)
}

@Composable
@PublishedApi
internal fun createHiltViewModelFactory(
    viewModelStoreOwner: ViewModelStoreOwner
): ViewModelProvider.Factory? = if (viewModelStoreOwner is NavBackStackEntry) {
    HiltViewModelFactory(
        context = LocalContext.current,
        navBackStackEntry = viewModelStoreOwner
    )
} else {
    // Use the default factory provided by the ViewModelStoreOwner
    // and assume it is an @AndroidEntryPoint annotated fragment or activity
    null
}

public fun HiltViewModelFactory(
    context: Context,
    navBackStackEntry: NavBackStackEntry
): ViewModelProvider.Factory {
    val activity = context.let {
        var ctx = it
        while (ctx is ContextWrapper) {
            if (ctx is Activity) {
                return@let ctx
            }
            ctx = ctx.baseContext
        }
        throw IllegalStateException(
            "Expected an activity context for creating a HiltViewModelFactory for a " +
                "NavBackStackEntry but instead found: $ctx"
        )
    }
    return HiltViewModelFactory.createInternal(
        activity,
        navBackStackEntry, // SavedStateRegistryOwner로 치환
        navBackStackEntry.arguments, // Bundle 객체
        navBackStackEntry.defaultViewModelProviderFactory, //ViewModelProvider.Factory
    )
}
```

- **viewModel()로 하더라도 생명주기가 Activity가 아니라는 것을 명심!!!**

<br><br><br>