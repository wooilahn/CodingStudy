## CHATEPER 1(안정성) _ ITEM 2. 변수의 스코프를 최소화하라

- **상태를 정의할 때는 변수와 프로퍼티의 스코프(Scope)를 최소화하는 것이 좋다.**
- **스코프를 좁게 만드는 것이 좋은 이유 중 가장 중요한 이유는 프로그램을 추적하고 관리하기 쉽기 때문이다.**
  - 프로퍼티보다 지역 변수(Local Variable)를 사용하는 것이 좋다.
  - 최대한 좁은 스코프(Scope)를 갖게 변수를 사용하는 것이 좋다.
  - 변수 스코프를 제한하는 예는 다음과 같다.
``` kotlin
// bad example
var user: User
for (i in users.indices) {
  user = users[i]
  print("User at $i is $user")
}

// better example
for (i in users.indices) {
  val user = users[i]
  print("User at $i is $user")
}

// best example
for ((i, user) in users.withIndex()) {
  print("User at $i is $user")
}
```

- **여러 프로퍼티를 한꺼번에 설정해야 하는 경우에는 구조분해 선언(destructuring declaration)을 활용하는 것이 좋다.**
```kotlin
// bad example
fun updateWeather (degrees: Int) {
  val description: String
  val color: Int
  if (degrees < 5) {
    description = "cold"
    color = Color.BLUE
  } else if (degrees < 23) {
    description = "mild"
    color = Color.YELLOW
  } else {
    description = "hot"
    color = Color.RED
  }
}

// better example
fun updateWeather (degrees: Int) {
  val (description, color) = when {
    degrees < 5 -> "cold" to Color.BLUE
    degrees < 23 -> "mild" to Color.YELLOW
    else -> "hot" to Color.RED
  }
}
```
