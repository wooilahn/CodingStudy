## CHATEPER 2(가독성) _ ITEM 13. Unit?을 리턴하지 말라

- **Unit?을 리턴하거나, 이를 기반으로 연산을 수행하지 않는 것이 좋다.** 
  - 아래 두 코드는 같은 기능을 수행할 수 있는 코드이다.
```kotlin
  // Case 1
  fun keyIsCorrect(key: String): Boolean = //...
  if(!keyIsCorrect(key)) return

  // Case 2
  fun verifyKey(key: String): Unit? = //...
  verifyKey(key) ?: return
```

  - Unit?으로 불을 표현하는 것은 오해의 소지가 있으며, 예측하기 힘든 오류를 발생시킬 수 있다.
  - Unit?보다는 Boolean을 사용하는 형태로 코드를 작성하는 것이 보통 좋다.
```kotlin
  if(!keyIsCorrect(key)) return // good
  verifyKey(key) ?: return // bad
```
