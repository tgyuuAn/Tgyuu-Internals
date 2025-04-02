### DAO, DTO, VO

- DAO는 Data Access Object의 약자로 DB의 데이터에 접근하기 위한 객체를 가리킴
- DB에 접근하는 로직을 분리하기 위해서 사용
- DTO는 Data Transfer Object의 약자로 계층 간 데이터 교환을 위해 아무런 로직도 가지지 않는 객체를 말함 _(Getter와 Setter가 존재)_
- VO는 Valuable Object의 약자로 생성 후에는 Read-Only한 속성을 가짐. 주로 비즈니스 개념을 명확하게 표현하기 위해 사용함 _(Money, PhoneNumber, Address 등)_
- 클린 아키텍처에서 말하는 Entity와 VO는 비슷한 개념이고, Entity의 범주 안에 VO가 들어있다고 생각하면 편함.
- VO의 핵심은 불변성이며, 코틀린의 data class 내부에 val만 존재하고 copy로 새로운 값을 할당하는 것은 Entity이자 VO라고 봐도 무방함
