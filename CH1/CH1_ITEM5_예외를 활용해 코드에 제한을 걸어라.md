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
  - (2) 문제가 있을 경우, 함수가 예상하지 못한 동작을 하지 않고 예외를 throw한다. (코드가 더 안정적으로 작동할 수 있게함.)
  - (3) 코드가 어느정도 자체적으로 점검이 가능하여, 단위 테스트를 줄일 수 있다.
  - (4) 스마트 캐스트(Smart cast)를 활용할 수 있게 되므로, 캐스트를 적게 할 수 있다.

-------------------------------------------
#### **아규먼트(Argument)**
- 함수 앞부분에 있는 입력 유효성 검사를 통해 입력값에 제한을 걸 때 'require' 함수가 주로 사용된다.
- require 함수는 조건을 만족시키지 못할 때, 무조건 'IllegalArgumentException'을 발생시키므로 무시할 수 없다.
  - (1) 팩토리얼을 계산하는 함수의 아규먼트는 양의 정수여야 한다.
  - (2) 클러스터링 하는 함수의 아규먼트는 비어있지 않는 좌표 목록이어야 한다. 
  - (3) 사용자로부터 이메일 주소를 받는 함수는 이메일 형식이 올바른지 확인해야 한다.
```kotlin
fun factorial(n: Int): Long {
  require(n >= 0)
     return if(n <= 1) 1 else factrial(n-1) * n
}

fun findClusters(points: List<Point>): List<Cluster>{
  require(points.isNotEmpty())
  ...
}

fun sendEmail(user: User, message: String) {
  require(isValidEmail(user.email))
  ...
}
```
-------------------------------------------
#### **상태(State)**
- 아래와 같이 구체적인 조건을 만족할 경우에 함수를 사용할 수 있게 하고 싶을 때에는 'check' 함수를 사용한다.
  - (1) 어떤 객체가 미리 초기화되어 있어야만 처리를 하게 하고 싶은 함수
  - (2) 사용자가 로그인했을 때만 처리를 하게 하고 싶은 함수
  - (3) 객체를 사용할 수 있는 시점에 사용하고 싶은 함수 
- check 함수는 지정된 예측을 만족하지 못할 때 IllegalStateException을 throw한다.
- 이 경우, 사용자가 규약을 어기고 사용하면 안되는 곳에서 함수를 호출하고 있다고 의심할 수 있다.
```kotlin
fun speak(text: String) {
  check(isInitialized)
  //...
}

fun getUserInfo(): UserInfo {
  checkNotNull(token)
  //...
}

fun next(): T {
  check(isOpen)
  //...
}
```
-------------------------------------------
#### **Assert 계열 함수 사용**
- 단위테스트는 구현의 정확성을 확인하는 가장 기본적인 방법이다.
```kotlin
fun pop(num: Int = 1): List<T> {
  //...
  assert(ret.size == num)
  return ret
}
- Assert 함수를 사용할 때의 장점은 다음과 같다.
  - (1) Assert 계열의 함수는 코드를 자체 점검하며, 더 효율적으로 테스트 할 수 있게 해준다.
  - (2) 특정 상황이 아닌 모든 상황에 대해 테스트 할 수 있다.
  - (3) 실행 시점에 정확하게 어떻게 되는지 파악할 수 있다.
```
-------------------------------------------
#### **nullability와 스마트 캐스팅(Smart casting)**
- nullability를 목적으로, 오른쪽에 throw 또는 return을 두고 Elvis 연산자를 활용할 수 있다.
```kotlin
fun sendEmail(person: Person, text: String) {
  val email: String = person.email ?: return
  //...
}

// 여러 처리를 해야할 때에는, run 함수를 조합하여 사용하면 된다.
fun sendEmail(person: Person, text: String) {
  val email: String = person.email ?: run {
    log("Email not sent, no email address")
    return
  }
  //...
}
```
