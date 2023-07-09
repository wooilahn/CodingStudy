## CHATEPER 2(가독성) _ ITEM 11. 가독성을 목표로 설계하라

- ** 가독성이 중요하다. ** 
  - 로버트마틴의 '클린코드' 에서, "개발자가 코드를 작성하는 데는 1분 걸리지만 이를 읽는데에는 10분이 걸린다."라는 말이 있다.
  - 프로그래밍은 그만큼 '쓰기'보다 '읽기'가 중요하다.

---------------------------------------
- ** 인식 부하 감소 **
  - 아래 두 코드들 중 어떤 것이 더 가독성이 좋은 코드일까? -> A (코틀린 초보자들도 이해하기 쉽도록 작성됨.)
```kotlin
  // 구현 A
  if(person != null && person.isAdult) {
    view.showPerson(person)
  } else {
    view.showError()
  }

  // 구현 B
  person?.takeif { it.isAdult }
    ?.let(view::showPerson)
    ?: view.showError()
```
  - 가독성이란, "코드를 얼마나 빠르게 이해할 수 있는지"를 의미한다.
  - 구현 B와 같이, 사용빈도가 적은 관용구의 사용은 코드를 복잡하게 만든다.
  - 또한, 구현 A는 아래 예시와 같이 구현 B에 비해 코드를 수정하기 쉽다.
```kotlin
  if(person != null && person.isAdult) {
    view.showPerson(person)
    view.hideProgressWithSuccess()
  } else {
    view.showError()
    view.hideProgress()
  }

  person?.takeIf { it.isAdult }
    ?.let {
        view.showPerson(it)
        view.hideProgressWithSucess()
    } ?: run {
        view.showError()
        view.hideProgress()
    }
```
  - 구현 A의 경우가 디버깅도 B에 비해 더 간단하다.
  - 결론적으로, '인지 부하'를 줄이는 방향으로 코드를 작성해야 한다.

---------------------------------------
- ** 극단적이 되지 않기 **
  - 이전의 경우 B에서, let을 사용함으로써 가독성을 낮출 수 있다고 하였지만, 절대로 쓰면 안된다는 것은 아니다.
    - nullable 가변 프로퍼티와 같은 경우에 대해 안전 호출이 필요한 때
    - 연산을 Argument 처리 후로 이동시킬 때
    - 데코레이터를 사용해서 객체를 Wrap 할 때
  - 항상 극단적인 방향으로 생각하여 코드를 작성하지 않도록 유의해야 한다.

---------------------------------------
- ** 컨벤션 **
  - 아래 코드는 책의 지은이가 생각하는 최악의 코틀린 코드이다. (이유는 아래와 같다.)
  -   연산자는 의미에 맞게 사용해야 한다. invoke를 이러한 형태로 사용하면 안된다.
  -   아래 코드에서 'and'라는 함수 이름이 실제 함수 내부에서 이루어지는 처리와 적절하지 않다.
  -   문자열을 결합하는 기능은 이미 내부적으로 구현되어 있으므로, 따로 만드는 것은 불필요하다.
```Kotlin
    operator fun String.invoke(f: ()->String): String = this + f()
    infix fun String.and(s: String) = this + s

    val abc = "A" { "B" } and "C"
    print(abc) // ABC
```
  -  
