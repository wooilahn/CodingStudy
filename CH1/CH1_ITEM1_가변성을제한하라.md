## CHATEPER 1(안정성) _ ITEM 1. 가변성을 제한하라

- **코틀린은 모듈(Module)로 프로그램을 설계한다.** 
  - 모듈은 클래스, 객체, 함수 등 다양한 요소로 구성되는데, 이러한 요소 중 일부는 상태(state)를 가질 수 있다. 
  - (ex) 아래와 같이 (1) read-write property를 가진 'var'를 사용하거나 (2) mutable 객체를 사용하는 경우에 그렇다.
```kotlin
var a = 10  // (1)
var list: MutableList<Int> = mutableListOf() // (2)
```
---------------------------------------
- **시간에 따라 변화할 수 있는 '상태(State)' 요소를 표현할 수 있다는 것은 유용할수도, 위험할수도 있는 양날의 검이다.**
  - (1) 프로그램을 이해하고 디버그하기 힘들어진다. 상태를 갖는 요소들의 관계 및 추적이 까다로워지기 때문이다.
  - (2) 가변성(mutability)가 있으면 코드의 실행을 추론하기 어려워진다. 시점에 따라 값이 계속해서 변화하기 때문이다.
  - (3) 멀티스레드(Multi-Thread) 프로그램일 때에는 적절한 동기화가 필요하다. 변경이 일어날 때 충돌이 일어날 수 있기 때문이다.
  - (4) 테스트를 수행하기 어렵다. 상태의 모든 경우의 수에 대비하여 테스트를 진행하여야 하기 때문이다.
  - (5) 상태 변경이 일어날 때, 다른 부분에 알려야 하는 경우가 있다. 정렬리스트에 가변 요소를 추가할 경우, 매번 다시 정렬을 수행해야 한다.

---------------------------------------
- **변할 수 있는 지점을 줄여 동기화의 안정성을 높이는 것이 중요하다.**
```kotlin
/* bad example */
var num = 0
for (i in 1..1000) {
  thread {
    Thread.sleep(10)
    num += 1
  }
}
Thread.sleep(5000)
print(num) // 1000이 아닐 확률이 매우 높다.

/* good example */
val lock = Any()
var num = 0
for (i in 1..1000) {
  thread {
    Thread.sleep(10)
    synchronized(lock) {
      num += 1
    }
}
Thread.sleep(1000)
print(num) // 1000
```
---------------------------------------
- **코틀린에서 가변성을 제한하는 3가지 방법**
  - **(1) 읽기 전용 property (val)**
    - 'val'은 읽기전용 property이나, mutable 객체를 담고 있다면 내부적으로 변화 가능하다.
  ```kotlin
  val a = 10
  a = 20 // error
  
  val list = mutableListOf(1, 2, 3)
  list.add(4) 
  ```  
  
  - **(2) 가변 컬렉션과 읽기 전용 컬렉션 구분하기**
    - 코틀린은 읽고 쓸 수 있는 컬렉션(Collection)과 읽기 전용 컬렉션(Collection)으로 구분된다.
    - 읽고 쓸 수 있는 컬렉션(Collection): Iterable, Collection, Set, List 인터페이스
    - 읽기 전용 컬렉션(Collection): MutableIterable, MutableCollection, MutableSet, MutableList 인터페이스    
      
    
  - **(3) 데이터 클래스의 copy** 
    - immutable 객체를 사용하는 것의 장점
      - (1) 한 번 정의된 상태가 유지되기 때문에, 코드를 이해하기 쉽다.
      - (2) immutable 객체를 공유할 때에도, 충돌이 따로 이루어지지 않으므로 병렬 처리를 안전하게 할 수 있다.
      - (3) immutable 객체에 대한 참조는 변경되지 않으므로, 쉽게 캐시할 수 있다.
      - (4) immutable 객체는 defensive copy를 만들 필요가 없다.
      - (5) immutable 객체는 다른 객체를 만들 때 활용하기 좋다.
      - (6) immutable 객체는 'set' 또는 'map'의 키로 사용하기 좋다.

---------------------------------------
- **다른 종류의 변경 가능 지점**
```kotlin
val list1: MutableList<Int> = mutableListOf()
var list2: List<Int> = listOf()

list1.add(1)
list2.list2 + 1
```

- 두 가지 모두 변경가능 지점이 있지만, 그 위치가 다르다.
  - 첫 번째 코드는 구체적인 리스트 구현 내부에 변경 가능 지점이 있다.
  - 두 번째 코드는 프로퍼티 자체가 변경 가능 지점이다.

- 최악의 방식은 아래와 같이 프로퍼티와 컬렉션을 모두 변경 가능한 지점으로 만드는 것이다.
```kotlin
var list3 = mutableListOf<Int>()
```
