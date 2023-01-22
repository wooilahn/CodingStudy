## CHATEPER 1(안정성) _ ITEM 5. 예외를 활용해 코드에 제한을 걸어라

- **코틀린에서 코드의 동작에 제한을 걸 때, 다음과 같은 방법을 사용할 수 있다.**
  - (1) require block: Argument를 제한할 수 있다.
  - (2) check block: 상태와 관련된 동작을 제한할 수 있다.
  - (3) assert block: 어떤 것이 true인지 확인할 수 있다. (aasert 블록은 테스트 코드에서만 작동한다.)
  - (4) return 또는 throw와 함께 활용하는 Elvis 연산자 

```kotlin
fun pop(num: Int = 1): List<T> {
  require(num <= size){
    "Cannot remove more elements than current size"
  }
  check(isOpen) { "Cannot pop from closed stack" }
  val ret = collection.take(num)
  collection = collection.drop(num)
  asserT(ret.size == num)
  return ret
}
```

-------------------------------------------
- **제한을 걸 때에, 다양한 장점이 발생할 수 있다.**
  - (1) 제한을 걸면, 문서를 읽지 않은 개발자도 문제를 확인할 수 있다.
  - (2) 문제가 있을 경우, 함수가 예상하지 못한 동작을 하지 않고 예외를 throw합니다. 
