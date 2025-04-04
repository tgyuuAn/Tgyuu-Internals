### LaunchedEffect에 key를 왜 넣나요?

- LaunchedEffect는 리컴포지션마다 호출되는 것이 아니라 UI와 별개로 동작하며 코루틴을 이용한 SideEffect를 위해서 만들어진 Composable
- 이 때 LaunchedEffect에 넣어준 key 값이 변경될 때 마다 LauncehdEffect의 블록이 새롭게 호출되는 방식임
- 즉 특정한 값의 변경을 트리거로 SideEffect를 만들고싶다면 LaunchedEffect에 해당 값을 key로 넣어주면 된다.

<br><br><br>

### Recomposition 횟수를 알 수 있는 방법과 이를 조절하는 방식에는 어떤 것이 있나요?

<br><br><br>

### Compose에서 Stable, Immutable Annotation를 추가하는 이유를 알고 있나요?

<br><br><br>

### CompositionLocal에 대해서 설명해주세요. StaticCompositionLocal과 다른 점은 무엇인가요?

<br><br><br>

### Compose의 렌더링 과정에 대해서 설명해주세요.

<br><br><br>

### 호이스팅에 대해서 설명해주세요.

<br><br><br>

### 