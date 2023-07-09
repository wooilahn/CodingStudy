## CHATEPER 2(가독성) _ ITEM 12. 연산자 오버로드를 할 때는 의미에 맞게 사용하라.

- **큰 힘에는 큰 책임이 따른다.**
  - 연산자 오버로딩은 굉장히 강력한 기능이지만, '큰 힘에는 큰 책임이 따른다'라는 말처럼 조심히 사용해야 한다.
  - Kotlin은 factorial 연산에 대해 '6!'와 같은 기능을 지원하지 않지만, 아래와 같이 연산자 오버로딩을 사용하여 만들어 낼 수 있다.
  ```Kotlin
    fun Int.factorial(): Int = (1..this).product()
    fun Iterable<Int>.product(): Int =
        fold(1) { acc, i -> acc * i }
    operator fun Int.not() = factorial()

    print(10 * 6.factorial()) // 7200
    print(10 * !6) // 7200
  ```  
  - 하지만, 이렇게 코드를 작성할 경우 '!'의 기본적인 의의인 '논리 연산'에 맞지 않게 사용법이 변경된다.
  - 즉, 관례에 맞지 않는 사용법으로 코드 작성에 혼란을 줄 수 있으므로 팩토리얼 연산에 '!'을 사용해선 안된다는 것이다.
 
---------------------------------------
- **분명하지 않은 경우**
  - 문제가 되는 경우는, '관례에 맞는지 안맞는지 확실하지 않은 경우'이다.
  - 예를 들어, 함수를 세 배 한다는 것은 어떤 의미일까? 아래 코드와 같이 생각하는 사람이 있을 것이다.
  ```Kotlin
    operator fun Int.times(operation: () -> Unit): ()->Unit =
      {repeat(this) { operation() } }

    val tripledHello = 3 * { print("Hello") }
    tripledHello() // 출력: HelloHelloHello
  ```
  - 의미가 명확하지 않다고, 생각되는 경우에는 infix 함수를 활용한 확장 함수를 활용할 수 있다.
  ```Kotlin
    infix fun Int.timeRepeated(operation: () - > Unit) = {
      repeat(this) { operation() }
    }

    val tripledHello = 3 timesRepeated { print("Hello") }
    tripledHello() // 출력: HelloHelloHello
  ```

---------------------------------------
- **규칙을 무시해도 되는 경우**
  - 도메인 특화 언어(Domain Specific Language, DSL)을 설계할 때에는, 연산자 오버로딩 규칙을 무시해도 된다.
  ```DSL
    body {
      div {
        +"Some text"
      }
    }
  ```

---------------------------------------
- **최종 정리**
  - 연산자 오버로딩은 그 이름의 의미에 맞게 사용해주어야 하며, 연산자의 의미가 명확하지 않다면 연산자 오버로딩을 사용하지 않는 것이 좋다.
  - 그러나, 꼭 연산자와 같은 형태로 사용하고 싶다면 infix 확장 함수 또는 Top-level 함수를 활용하는 편이 좋다.
