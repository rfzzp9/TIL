## 1. 의존성 주입이란?


### 1-1. 의존성 주입의 개념


- 소프트웨어 공학에서 말하는 의존성 주입(Dependency Injection)이란, 하나의 객체에 다른 객체의 의존성을 제공하는 기술을 말한다.
- 쉽게 말해서, 생성자 또는 메서드 등을 통해 외부로부터 생성된 객체를 전달받는 것을 말한다.


### 1-2. 의존성 주입의 특징


- 클래스 간 결합도를 느슨하게 한다.
- 인터페이스 기반으로 설계되며, 코드를 유연하게 한다.
- Stub 또는 Mock 객체를 사용하여 단위테스트를 하기가 더욱 쉬워진다.

##### [ 의존성 주입이 없는 코드 ]


```kotlin
class MemoRepository {
  private val db = SQLDatabase()

  fun load(id: String) { ... }
}

fun main() {
  val repository = MemoRepository()
  repository.load("8092")
}
```

##### [ 의존성을 외부로부터 주입 받는 코드 ]


```kotlin
/*
- 데이터베이스 객체 생성에 대한 책임을 MemoRepository가 가지고 있지 않음
- MemoRepository 생성자의 매개변수로 외부에서 생성된 데이터베이스 객체를 전달받음
*/

class MemoRepository(private val db: Database) {
  fun load(id: String) { ... }
}

fun main() {
  val db = SQLiteDatabase()
  val repository = MemoRepository(db)
  repository.load("8092")
}
```


## 2. 의존성 주입 프레임워크


### 2-1. Dagger2


- 
