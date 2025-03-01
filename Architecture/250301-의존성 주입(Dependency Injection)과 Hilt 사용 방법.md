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
<br>

## 2. 의존성 주입 프레임워크, Hilt


### 2-1. Hilt의 개념


- 애플리케이션에서 DI를 사용하는 표준적인 방법을 제공하는 Dagger 기반의 프레임워크이다.


### 2-2. Hilt의 목표


- Dagger 사용의 단순화
- 표준화된 컴포넌트 세트와 스코프로 설정과 가독성/이해도 쉽게 만들기
- 쉬운 방법으로 다양한 빌드 타입에 대해 다른 바인딩 제공


### 2-3. Hilt의 특징


- Dagger2 기반의 라이브러리이다.
- 표준화된 Dagger3 사용법을 제시한다.
- 보일러플레이트 코드를 감소시킨다.
- 프로젝트 설정을 간소화시킨다.
- 쉬운 모듈 탐색과 통합 환경을 제공한다.
- 개선된 테스트 환경을 제공한다.
- Android Studio 4.2 version 이후부터 Hilt 그래프를 가시화된 형태로 볼 수 있다.
- AndroidX 라이브러리와 호환된다.


##### [ Hilt를 적용한 코드 ]


```kotlin
//@inject 어노테이션은 의존성 주입 받음을 의미
class MemoRepository @inject constructor(private val db: MemoDatabase) {
  fun load(id: String) { ... }
}
```
```kotlin
/*
- Hilt 사용시 선행되어야 하는 부분
- @HiltAndroidApp 어노테이션은 모든 의존성 주입의 시작점
*/
@HiltAndroidApp
class MemoApp: Application()
```

```kotlin
/*
- @AndroidEntryPoint 어노테이션은 Activity와 같은 Android 클래스에 추가할 수 있음
- @AndroidEntryPoint 어노테이션은 Activity 내에 선언된 @Inject 어노테이션이 달린 변수에
  대해 의존성 주입을 수행함
*/
@AndroidEntryPoint
class MemoActivity: AppCompatActivity() {

  @inject lateinit var repository: MemoRepository

  override fun onCreate(savedInstanceState: Bundle) {
    super.onCreate(Bundle)
    repository.load("YHLQMDLG")
  }
}
```
<br>

여기까지 했다면, Activity에 MemoRepository 객체 주입을 위한 기반 작업은 완료되었다.
이제 MemoRepository를 생성하기 위한 매개변수인 데이터베이스만 제공해주면 된다.

```kotlin
/*
- @Module 어노테이션이 달린 모듈 클래스에 @InstallIn 어노테이션을 추가함
- 모듈 클래스 내에 데이터베이스 객체를 생성하는 Provide 메서드를 생성하면 모든 작업이 끝나고
  MemoActivity에서 MemoRepository를 주입 받을 수 있게 됨
*/
@InstallIn(ApplicationComponent::class)
@Module
object DataModule {

  @Provides
  fun provideMemoDB(@ApplicationContext context: Context)=
    Room.databaseBuilder(context, MemoDatabase::class.java, "Memo.db").build()
}
```
