## CHATEPER 1(안정성) _ ITEM 8. 적절하게 null을 처리하라

#### **null은 '값이 부족하다(lack of value)'를 나타낸다.** 
  - 프로퍼티가 null이라는 것은 값이 설정되지 않았거나, 제거되었다는 것을 의미한다.
  - 함수가 null을 return 한다는 것은 다양한 의미를 가질 수 있다.
    - String.toIntOrNull()은 String을 Int로 적절하게 변환할 수 없는 경우 null을 리턴한다.
    - Iterable<T>.firstOrNull(() -> Boolean)은 주어진 조건에 맞는 요소가 없는 경우 null을 리턴한다.
  - nullable 값이 생기게 될 경우 처리하는 것은 결국 개발자이기 때문에, null은 최대한 명확한 의미를 갖는 것이 좋다.
```kotlin
val printer: Printer? = getPrinter()
printer.print()
  
printer?.print() // 안전 호출 (Safe call)
if(printer != null) printer.print() // 스마트 캐스팅(Smart casting)
printer!!.print() // not-null assertion
```
  
-----------------------------------------
#### **nullable type을 처리하는 세 가지 방법**
  - ?., 스마트캐스팅, Elvis 연산자 등을 활용하여 안전하게 처리한다.
  - 오류를 throw한다.
  - 함수 또는 프로퍼티를 리팩터링해서 nullable 타입이 나오지 않게 바꾼다.

-----------------------------------------
#### **안전 호출(safe call)과 스마트 캐스팅(smart casting)**
  - 아래 safe call과 smart casting 코드는 모두 printer가 null이 아닐 때 print 함수를 호출한다.
  - 개발자에게도 편리하여, nullable 값을 처리할 때 이 방법을 가장 많이 활용한다.
```kotlin
  printer?.print() // safe call
  if(printer != null) printer.print() // smart casting
```  
  
  - 추가로 인기 있는 다른 방법은 Elvis 연산자를 활용하여 nullable 변수를 처리하는 방법이다.
```kotlin
  val printerName1 = printer?.name ?: "Unnamed"
  val printerName2 = printer?.name ?: return
  val printerName3 = printer?.name ?: throw Error("Printer must be named")
```  
  
  - 컬렉션 처리에서 무언가 없다는 것을 나타낼 때 null이 아닌 빈 컬렉션(Empty collection)을 사용하는 것이 일반적이다.
  - 이에, Collection<T>?.orEmpty() 확장 함수를 사용하면 nullable이 아닌 List<T>를 리턴받는다.

-----------------------------------------
#### **방어적 프로그래밍(Defensive Programming)과 공격적 프로그래밍(Offensive Programming)**
  - 모든 가능성을 올바른 방식으로 처리하는 것을 방어적 프로그래밍(Defensive Programming)이라고 부른다.
  - 모든 상황을 안전하게 처리하는 것은 불가능하기에, 문제가 발생했을 때 개발자에게 알려서 수정하게 만드는 것을 공격적 프로그래밍(Offensive Programming)이라고 부른다.
  
-----------------------------------------
#### **오류 throw하기**
  - printer가 null일 때, 개발자가 null이 될 것이라고 예상하지 못했을 경우에 print 메소드가 호출되지 않아 이상하게 여길 것이다.
  - 이는 개발자가 오류를 찾기 어렵게 만들 것이기에 이런 부분에서는 개발자에게 오류를 강제적으로 발생시켜 주는 것이 좋다.
  - 이 때는, throw, !!, requireNotNull, checkNotNull 등을 활용한다.
```kotlin
  fun process(user: User){
    requireNotNull(user.name)
    val context = checkNotNull(context)
    val networkService =
        getNetworkService(context) ?:
        throw NoInternetConnection()
    networkService.getData { data, userData ->
        show(data!!, userData!!)
    }
  }
```

-----------------------------------------
#### **not-null assertion(!!)과 관련된 문제**
  - nullable을 처리할 때, '!!'은 사용하기 쉽지만 좋은 해결 방법은 아니다.
  - '!!'은 자바에서 nullable을 처리할 때의 문제가 동일하게 발생하며, 예외가 발생할 때 어떤 설명도 없는 generic exception이 발생한다.
  - 이에, '!!'은 타입이 nullable이지만, null이 나오지 않는다는 것이 거의 확실시 될 때만 신중하게 사용해야 한다.  
  
  - 아래와 같이 미리 null을 변수에 할당하여 사용하는 방법도 좋지 않은 방법이다.
    - (1) 프로퍼티를 계속해서 언팩(unpack)해야 하므로 사용하기 번거로워진다.
    - (2) 실제로 이후에 의미 있는 null 값을 가질 가능성 자체를 차단해버린다.
```kotlin
  class UserControllerTest {
    private var dao: UserDao? = null
    private var controller: UserController? = null
    
    @BeforeEach
    fun init() {
      dao = mockk()
      controller = UserController(dao!!)
    }
    
    @Test
    fun test() {
      controller!!.doSomething()
    }
  }
```
  
-----------------------------------------
#### **의미 없는 nullability 피하기**
  - nullability는 어떻게든 적절하게 처리해야 하므로, 최대한 적게 생성하는 것이 좋다.
  - nullability를 피할 때 사용할 수 있는 몇 가지 방법은 다음과 같다.
    - (1) 클래스에서 nullability에 따라 여러 함수를 만들어서 제공할 수 있다. (ex. List<T>의 get과 getOrNull)
    - (2) 어떤 값이 클래스 생성 이후에 확실하게 설정된다는 보장이 있다면, lateinit 프로퍼티와 notNull 델리게이트를 사용한다.
    - (3) 빈 컬렉션(Empty collection) 대신에 null을 리턴하지 말아야 한다. 요소가 부족할 경우에도, 빈 컬렉션을 리턴하는 것이 좋다.
    - (4) nullable enum과 None enum 값은 완전히 다른 의미이다. null enum은 별도로 처리해야 하지만, None enum 정의에 없으므로 필요한 경우 사용하는 쪽에서 추가해서 활용할 수 있다는 의미이다.

-----------------------------------------
#### **lateinit 프로퍼티와 notNull 델리게이트 **
  - 클래스가 클래스 생성 중에 초기화할 수 없는 프로퍼티를 가지는 것은 종종 일어나는 일이다. (ex. Junit의 @BeforeEach)
  - 프로퍼티를 사용할 때마다 nullable에서 null이 아닌 것으로 타입 변환하는 것은 바람직하지 못하다.
  - 이 때, 프로퍼티가 이후에 설정된 것임을 명시하는 lateinit 한정자를 사용하는 것이 좋다.
```kotlin
  // bad example
  class UserControllerTest {
    private var dao: UserDao? = null
    private var controller: UserController? = null
  
    @BeforeEach
    fun init() {
      dao = mockk()
      controller = UserController(dao!!)
    }
  
    @Test
    fun test() {
      controller!!.doSomething()
    }
  }
  
  // good example (with 'lateinit' keyword)
  class User ControllerTest {
    private lateinit var dao: UsrDao
    private lateinit var controller: UserController
  
    @BeforeEach
    fun init() {
      dao = mockk()
      controller = UserController(dao)
    }
  
    @Test
    fun test() {
      controller.doSomething()
    }
  }
```
  
- JVM에서 Int, Long과 같은 기본 타입과 연결된 타입으로 프로퍼티를 초기화해야 하는 경우, lateinit을 사용할 수 없다.
- 이런 경우에는 lateinit보다는 약간 느리지만, 'Delegates.notNull'을 사용한다.
  
```kotlin
  class DoctorActivity: Activity() {
    private var doctorId: Int by Delegates.notNull()
    private var fromNotification: Boolean by Delegates.notNull() // notNull 델리게이트 초기화
  
    override fun onCreate(savedInstanceState: Bundle?) {
      super.onCreate(savedInstanceState)
      doctorId = intent.extras.getInt(DOCTOR_ID_ARG)
      fromNotification = intent.extra.getBoolean(FROM_NOTIFICATION_ARG)
    }
  }
```

- onCreate때 초기화하는 프로퍼티는 자연초기화하는 형태로 다음ㄴ과 같이 프로퍼티 위임(Property delegation)을 사용할 수도 있다.
```kotlin
  class DoctorActivity: Activity() {
    private var doctorId: Int by arg(DOCTOR_ID_ARG)
    private var fromNotification: Boolean by arg(FROM_NOTIFICATION_ARG)
  }
```
- 프로퍼티 위임을 사용하면, nullability로 발생하는 여러 가지 문제를 안전하게 처리할 수 있다. (자세한 내용은 ITEM 21에서)
