## CHATEPER 1(안정성) _ ITEM 8. user를 사용하여 리소스를 닫아라

- **close 메소드를 사용하여 명시적으로 닫아야 하는 리소스들이 있다.**
  - 아래 리소스들이 그러하며, 이들은 AutoCloseable을 상속받는 Closeable 인터페이스를 구현(implement)하고 있다.
    - (1) InputStream과 OutputStream
    - (2) java.sql.Connection
    - (3) java.io.Reader(FileReader, BufferReader, CSSParser)
    - (4) java.new.Socket과 java.util.Scanner
  - 이러한 리소스들은 최종적으로 리소스에 대한 레퍼런스가 없어질 때, 가비지 컬렉터(Garbage Collector)가 처리한다.
  - 그러나, 굉장히 느리고 그 전에 리소스를 유지하는 비용이 많이 들어가게 된다.
  - 이에, 명시적으로 close 메소드를 호출해주는 것이 좋으며 전통적으로는 아래와 같이 try-finally 블록을 이용하여 처리했다.
  ```kotlin
    fun countCharactersInFile(path: String): Int {
      val reader = BufferedReader(FileReader(path))
      try {
        return reader.lineSequence().sumBy { it.length }
      } finally {
        reader.close()
      }
    }
```

  - 해당 코드를, 'use' 함수를 사용하여 적절하게 변경할 수 있다.
```kotlin
  fun countCharactersInFile(path: String): Int {
    val reader = BufferedReader(FileReader(path))
    reader.user {
      return reader.lineSequence().sumBy { it.length }
    }
  }
  
  // 매개변수로 receiver가 전달되는 형태의 경우 아래처럼 줄일 수 있다.
  fun countCharactersInFile(path: String): Int {
    BufferedReader(FileReader(path)).user { reader ->
      return reader.lineSequence.sumBy { it.length }
    }
  }
```

  - 파일을 처리할 때에는, 파일을 한 줄씩 읽어 들이는 useLines을 사용하는 것이 좋다.
```kotlin
  fun countCharactersInFile(path: String): Int {
    File(path).useLines { lines ->
      return lines.sumBy { it.length }
    }
  }
```
