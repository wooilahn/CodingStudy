## CHATEPER 1(안정성) _ ITEM 3. 최대한 플랫폼 타입을 사용하지 말라

- 자바에서는 널 포인터 예외(Null-Pointer Exception, NPE)를 자주 볼 수 있지만, 코틀린에서는 null-safety 메커니즘으로 인해 찾아보기 힘들다.
- 코틀린에서 null이 아님을 단정할 수 있는 메소드의 경우, '!!'을 붙인다.  

- **코틀린은 자바 등의 다른 프로그래밍 언어에서 넘어온 타입들을 특수하게 다루는 데, 이를 '플랫폼 타입(Platform type)'이라고 칭한다.**
- 플랫폼 타입(Platform type)이란, 다른 프로그래밍 언어에서 전달되어서 nullable인지 알 수 없는 타입을 말한다.  


---------------------------------------------------------------
- **플랫폼 타입의 경우, 안전하지 않으므로 최대한 빨리 제거하는 것이 좋다.**
```kotlin
// Java
public class JavaClass {
  public String getValue() {
    return null;
  }
}

// Kotlin
fun statedType() {
  val value: String = JavaClass().value // Null Pointer Exception (NPE)
  //...
  println(value.length)
}

fun platformType() {
  val value = JavaClass().value
  //...
  println(value.length) // Null Pointer Exception (NPE)
}
```
- statedType()의 경우, 자바에서 값을 가져오는 위치에서 NPE가 발생한다.
- platformType()의 경우, 값을 활용할 때 NPE가 발생한다.
---------------------------------------------------------------

- **인터페이스에서의 platform Type의 위험성**
```kotlin
 interface UserRepo {
   fun getUserName() = JavaClass().value
 }
 
 cass RepoImpl: UserRepo {
   override fun getUsername(): String? {
     return null
   }
 }
 
 fun main() {
   val repo: UserRepo = RepoImpl()
   val text: String = repo.getUserName() // 런타임 때 NPE
   print("User name length is ${text.length}")
 }
```
