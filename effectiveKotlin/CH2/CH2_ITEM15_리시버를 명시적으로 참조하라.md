## CHATEPER 2(가독성) _ ITEM 15. 리시버를 명시적으로 참조하라

- **명시적으로 긴 코드를 사용할 때** 
  - 무언가를 좀 더 자세하게 설명하기 위해서, 명시적으로 긴 코드를 사용할 떄가 있다.
  - 대표적으로, 함수와 프로퍼티를 local 또는 Top-level variable이 아닌 다른 Receiver로부터 가져온다는 것을 나타낼 때가 있다.
  - Quick Sort에 대해 Receiver를 명시적으로 표시한 경우와 그렇지 않은 경우를 살펴보자.
```Kotlin
  // 명시적으로 표시한 경우
  fun<T : Comparable<T>> List<T>.quickSort(): List<T> {
    if(this.size < 2) return this
    val pivot = this.first()
    val (smaller, bigger) = this.drop(1)
      .partition { it < pivot }
    return smaller.quickSort() + pivot + bigger.quickSort()
  }

  // 명시적으로 표시하지 않은 경우
  fun<T : Comparable<T>> List<T>.quickSort(): List<T> {
    if(size < 2) return this
    val pivot = first()
    val(smaller, bigger) = drop(1)
      .partiton { it < pivot }
    return smaller.quickSort() + pivot + bigger.quickSort()
  }
```

---------------------------------------
- **여러 개의 리시버**
  - Scope 내부에 둘 이상의 Receiver가 있는 경우, Receiver를 명시적으로 나타내면 좋다. (ex. apply, with, run 함수를 사용할 때)
  - 아래의 경우를 보며, Receiver를 통해 가독성 을 높일 수 있을 뿐만 아니라 코드를 안전하게 사용할 수 있게 한다는 점을 알 수 있습니다.
``` Kotlin
  // Case 1 -> 'Created parent' 출력
  class Node(val name: String) {
    fun makeChild(childName: String) =
      create("$name.$childName")
        .apply { print("Created ${name}") }

    fun create(name: String): Node? = Node(name)
  }

  // Case 2 -> 'Create parent.child' 출력 (also 함수 사용)
  class Node(val name: String) {
    fun makeChild(childName: String) =
      create("$name.$childName")
        .also { print("Created ${it?.name}") }

    fun create(name: String): Node? = Node(name)  
  }

  // Case 3 -> 'Create parent.child in parent' 출력 (apply 함수와 label을 활용)
  class Node(val name: String) {
    fun makeChild(childName: String) =
      create($name.$childName").apply {
        print("Created ${this?.name} in " +
        " ${this@Node.name}") }

    fun create(name: String): Node? = Node(name)
  }

  fun main() {
    val node = Node("parent")
    node.makeChild("child")
  }
```

---------------------------------------
- **DSL 마커**
  - 코틀린 DSL을 사용할 때에는 여러 Receiver를 가진 요소들이 중첩되더라도, 이를 명시적으로 붙이지 않는다.
  - 그렇지만, DSL에서도 외부의 함수를 사용하는 것이 위험한 경우가 있다.
  ```Kotlin
    table {
      tr {
        td { + "Column 1" }
        td { + "Column 2" }

        tr {
          td { + "Value 1" }
          td { + "Value 2" }
        }
      }
    }
  ```

    - 이러한 잘못된 사용을 막으려면, 암묵적으로 외부 리시버를 사용하는 것을 막는 DslMarker라는 Meta Annotation을 사용한다.
  ```Kotlin
    @DslMarker
    annotation class HtmDsl
    fun table(f: TableDsl.() -> Unit) { /* ... */ }

    @HtmlDsl
    class TableDsl { /* ... */ }

    // 아래 코드 실행 시, 컴파일 오류 발생
    table {
        tr {
          td { + "Column 1" }
          td { + "Column 2" }
          tr { // 컴파일 오류
            td { + "Value 1" }
            td { + "Value 2" }
          }
        }
    }

    // 아래와 같이 외부 리시버를 명시적으로 사용해야 함.
    table {
      tr {
        td { + "Column 1" }
        td { + "Column 2" }
        this@table.tr {
          td { + "Value 1" }
          td { + "Value 2" }
        }
      }
    }
  ```
---------------------------------------
- **정리**
  - 짧게 적을 수 있다는 장점을 위해, Receiver를 제거해서는 안된다.
  - 여러 개의 Receiver가 있는 상황에서는, Receiver를 명시적으로 작성해주는 것이 좋다.
  - Receiver를 명시적으로 지정하면, 어떤 Receiver의 함수인지를 명확하게 알 수 있어 가독성이 향상된다.
  - DSL에서 외부 scope에 있는 Receiver를 명시적으로 적게하고 싶다면, 'DslMarker' Meta Annotation을 사용한다.
