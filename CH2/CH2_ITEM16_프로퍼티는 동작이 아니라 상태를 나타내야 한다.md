## CHATEPER 2(가독성) _ ITEM 16. 프로퍼티는 동작이 아니라 상태를 나타내야 한다.

- **코틀린의 프로퍼티(Property)** 
  - 코틀린의 프로퍼티는 자바의 필드와 '데이터를 저장할 수 있다.' 라는 점에서 비슷해보이나, 프로퍼티에는 더 많은 기능이 있다.
  - 기본적으로, 코틀린의 프로퍼티는 user-defined getter와 setter를 가질 수 있다.
  - var를 사용하여 만든 읽고 쓸 수 있는 프로퍼티는 파생 프로퍼티(derived property)라고 부른다.
```Kotlin
  // 코틀린의 프로퍼티 (Property of Kotlin)
  var name: String? = null
    get() = field?.toUpperCase()
    set(value) {
      if(!value.isNullOrBlank()) {
        field = value
      }
    }

  // 자바의 필드 (Field of Java)
```

  - 프로퍼티는 본질적으로 함수이므로, 확장 프로퍼티를 만들 수 있다.
  - 그러나, 프로퍼티를 함수 대신 사용하는 것처럼 아래와 같이 알고리즘의 동작을 넣어놓는 것은 좋지 않다.
```Kotlin
  // 아래와 같이 코드를 작성하는 것은 추천되지 않는다.
  val Tree<int>.sum: Int
    get() = when (this) {
      is Leaf -> value
      is Node -> left.sum + right.sum
    }
```
  - 위에서, sum 프로퍼티는 모든 요소를 반복적으로 처리하며 큰 컬렉션의 경우 답을 찾을 때 많은 계산량이 필요하다.
  - 이런 경우에는, 프로퍼티가 아닌 함수로 별도 구현하는 것이 좋다.
```Kotlin
  fun Tree<Int>.sum(): Int = when (this) {
    is Leaf -> value
    is Node -> left.sum() + right.sum()
  }
```

  - 원칙적으로 프로퍼티는 상태를 나타내거나 설정하기 위한 목적으로만 사용하는 것이 좋고, 다른 로직 등을 포함하지 않아야 한다.
  - '해당 프로퍼티를 함수로 정의할 경우, 접두사로 get과 set을 붙일 것인가?' 질문에 대해 아니라고 한다면, 이를 프로퍼티로 만드는 것은 좋지 않다.
  - 조금 더 구체적으로, 아래의 경우에는 프로퍼티 보다는 함수로 만드는 것이 좋다.
    - (1) 연산 비용이 높거나, 복잡도가 O(1)보다 큰 경우
    - (2) Business Logic을 포함하는 경우: 관습적으로 getter는 로깅, 리스너 통지와 같은 로직을 수행하지 않는다.
    - (3) 결정적이지 않은 경우: 같은 동작을 연속적으로 두번할 때, 다른 값이 나올 수 있을 때
    - (4) 변환의 경우: 관습적으로 변환은 Int.toDouble()과 같은 함수로 이루어진다.
    - (5) getter에서 프로퍼티의 상태 변경이 일어나야 하는 경우: 관습적으로 getter는 프로퍼티의 상태변화를 일으키지 않는다. 
