## CHATEPER 2(가독성) _ ITEM 18. 코딩 컨벤션을 가져라

- **코틀린 개발자가 지켜야할 컨벤션** 
  - 코틀린 커뮤니티에 속한 사람이라면 코틀린 문서의 'Coding Conversions'을 지켜주는 것이 좋다.
  - 이를 지키면, 아래의 장점을 얻을 수 있다.
    - (1) 어떤 프로젝트를 접해도 쉽게 이해할 수 있다.
    - (2) 다른 외부 개발자도 프로젝트의 코드를 쉽게 이해할 수 있다.
    - (3) 다른 개발자도 코드의 작동 방식을 쉽게 추측할 수 있다.
    - (4) 코드를 병합하고, 한 프로젝트의 코드 일부를 다른 코드로 이동하는 것이 쉽다.

---------------------------------------
- **대표적으로 실수하는 컨벤션** 
  - 1. 많은 파라미터를 가지고 있는 클래스와 함수는 다음과 같이 각각의 파라미터를 한 줄 씩 작성하는 방법을 사용한다.
  ```Kotlin
    class Person(
      val id: Int = 0,
      val name: String = "",
      val surname: String = ""
    ) : Human(id, name) {
      // 본문
    }

    public fun <T> Iterable<T>.joinToString(
      separator: CharSequence = ", ",
      prefix: CharSequence = "",
      postfix: CharSequence = "",
      limit: Int = -1,
      truncated: CharSequence = "...",
      transform: ((T) -> CharSequence)? = null
    ): String {
      // ...
    }
  ```
