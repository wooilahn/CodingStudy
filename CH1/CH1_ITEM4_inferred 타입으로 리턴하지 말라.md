## CHATEPER 1(안정성) _ ITEM 4. inferred 타입으로 리턴하지 말라

- **코틀린의 타입 추론(Type inference)는 JVM 세계에서 가장 널리 알려진 코틀린의 특징이다.** 
- 타입 추론을 사용할 때에는, 아래와 같은 부분들을 기억해두어야 한다.
  - (1) 할당 때, inferred 타입은 정확하게 오른쪽에 있는 피연산자에 맞게 설정되어야 한다는 것을 기억해야 한다.
  - (2) 슈퍼클래스 또는 인터페이스로는 설정되지 않는다.
```kotlin
// error case
open class Animal
class Zebra: Animal()

fun main() {
  var animal = Zebra()
  animal = Animal() // error: Type mismatch
}

// solution case
open class Animal
class Zebra: Animal()

fun main() {
  var animal: Animal = Zebra()
  animal = Animal()
}
```
--------------------------------------------

- **타입을 확실하게 지정해야 하는 경우에는 명시적으로 타입을 지정해야 한다는 원칙을 기억하자.**
- **안전을 위해서 외부 API를 만들 때는 반드시 타입을 지정하고, 이렇게 지정한 타입을 특별한 이유없이 제거하지 말자.**
