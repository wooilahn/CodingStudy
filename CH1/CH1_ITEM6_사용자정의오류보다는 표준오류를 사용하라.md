## CHATEPER 1(안정성) _ ITEM 6. 사용자 정의 오류보다는 표준 오류를 사용하라


- **최대한 표준 라이브러리의 오류를 사용하는 것이 좋다.** 
  - 표준 라이브러리의 오류는 많은 개발자들이 알고 있으므로, 이를 재사용하는 것이 좋다.
  - 대표적으로 널리 사용되는 예외는 아래와 같다.
    - (1) IllegalArgumentException / IllegalStateException: require와 check를 사용해 throw 할 수 있는 예외
    - (2) IndexOutOfBoundsException: Index 파라미터의 값이 범위를 벗어났다는 것을 나타내는 예외
    - (3) ConcurrentModificationException: 동시 수정(concurrent modification)을 금지했는데, 발생해 버렸을 때의 예외
    - (4) UnsupportedOperationException: 사용자가 사용하려고 했던 메서드가 현재 객체에서는 사용할 수 없다는 것을 나타내는 예외  
    - (5) NoSuchElementException: 사용자가 사용하려고 했던 요소(element)가 존재하지 않음을 나타낸다. (ex. 내부에 요소가 없는 Iterable에 대해 next를 호출)
