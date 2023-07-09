## CHATEPER 1(안정성) _ ITEM 7. 결과 부족이 발생할 경우 null과 Failure를 사용하라

- 함수를 원하는 결과를 만들어 낼 수 없을 때가 있다. (ex. 조건에 맞는 첫번째 요소를 찾으려 했으나 없는 경우)
- 이런 상황을 처리하는 메커니즘으로는 크게 2가지가 있다.
  - (1) 'null' 또는 'Failure' (실패를 나타내는 sealed class)를 리턴한다.
  - (2) 예외를 throw한다.
 
- 예외는 정보를 전달하는 방법으로 사용되서는 안되고, 잘못된 특별한 상황을 나타내고 처리될 때 사용되는 것이 좋다.
- 이에, null과 Failure는 예상되는 오류를 명시적이고 효율적으로 처리할 수 있다.
```kotlin
inline fun <reified T> String.readObjectOrNull(): T? {
  //...
  if(incorrectSign) {
    return null
  }
  //...
  return result
}

inline fun <reified T> String.readObject(): Result<T> {
  //...
  if(incorrectSign) {
    return Failure(JsonParsingException())
  }
  //...
  return Success(result)
}

sealed class Result<out T>
class Success<out T>(val result: T): Result<T>()
class Failure(val throwable: Throwable): Result<Nothing>()

calss JsonParsingException: Exception()

//val age = userText.readObjectOrNull<Person>()?.age ?: -1

val person = userText.readObjectOrNull<Person>()
val age = when(person) {
  is Success -> person.age
  is Failure -> -1
}
```
