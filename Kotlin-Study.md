#Kotlin

## 1. 개요 ( 자바와의 차이 위주 )
### 필드와 변수
  - var과 val의 차이 : var은 초기화 후 참조(값)가 가변될 수 있는 변수를 지정 / val은 초기화 후 참조(값)이 변하지 않는 불변 변수를 의미.
  - 초기화
    - 타입을 지정한 초기화(타입검사 가능) : val name: String = "진현 최"
    - 타입 추론을 이용한 초기화 : val name = "진현 최"
    - 가변 참조를 사용한 초기화 : var name = "진현 최"
  
  - 지연 초기화
    - 지연 초기화의 필요성 : 변수를 먼저 선언 후, 실제로 서비스에 필요할 때 초기화를 해야하는 경우.
    - 지연 초기화 방법 : nullable한 타입을 선언 후 null 값으로 초기화 후 값 변경.
    - 지연 초기화 예시
    ```kotlin
    // 1. null 값으로 초기화 후 사용시 참조 변경.(var 사용 필요, 여러번 변경 가능)
    var name: String? = null
    name = getName()
    
    // 2. 해당 참조가 최초로 사용될 시, 초기화 함수를 호출.(초기화 후 값 변경 없는 경우)
    val name: String by lazy { getName() } // 1. 람다 호출 방식
    val name: String by lazy(::getName)    // 2. 함수 호출 방식
    ```

### 클래스와 인터페이스
  - 접근 제어자 ( df : public )
    - 멤버 접근 제어자
      - protected : 상속 받은 클래스 혹은 인터페이스 에게만 접근 허용.
      - internal  : 같은 모듈 안의 클래스 혹은 인터페이스 에서만 접근 허용.
      - private   : 자기 자신만 접근 허용.
      - public    : 모든 클래스 혹은 인터페이스에게 접근 허용.
      
  - 클래스 선언
    - background : 코틀린은 멤버변수 접근자를 자동생성 / init 블록으로 생성자를 구현가능
    - 클래스 선언 예시
    ```kotlin
    // init을 사용한 방법
    class Person(val age: Int, val name: String?= null) {
      init {
          age = 100
      }
    }
    
    // init 생략
    class Person(val age: Int, val name: String)
    ```
    
  - 상속과 확장 (df: 확장 불가)
