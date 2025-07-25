## 1. Data Layer


### 1-1. Data Layer가 중요한 이유


- 앱의 데이터가 어디서 오고, 어떻게 저장되며, 어떤 방식으로 변환되어 UI에 전달되는지를 관리
- Data Layer가 잘못 설계되면 데이터 불일치, 메모리 누수, 테스트 어려움 등이 발생한다.

<br>

### 1-2. Data Layer의 구성 요소


Repository
- 다양한 데이터 소스를 추상화하고 상위 계층에 일관된 인터페이스를 제공한다.
```kotlin
class ExampleRepository(
    private val exampleRemoteDataSource: ExampleRemoteDataSource, // network
    private val exampleLocalDataSource: ExampleLocalDataSource // database
) {

    val data: Flow<Example> = ...

    suspend fun modifyData(example: Example) { ... }
}
```

- 핵심역할 <br>
  로컬, 원격, 캐시 데이터를 적절히 조합 <br>
  비즈니스 로직 구현 <br>
  네트워크 상태에 따른 적절한 데이터 제공

<br>

DataSource
- 각 DataSource는 특정 데이터 소스에 대한 접근을 담당한다.
- 로컬 데이터베이스, 네트워크 API, 캐시와 같은 여러 구현체를 가질 수 있다.
```kotlin
class NewsRemoteDataSource(
  private val newsApi: NewsApi,
  private val ioDispatcher: CoroutineDispatcher
) {

    suspend fun fetchLatestNews(): List<ArticleHeadline> =

        withContext(ioDispatcher) {
            newsApi.fetchLatestNews()
        }
    }
}
```

<br>

Data Model
- 계층 간 데이터 전달을 담당한다.
- 각 계층에 적합한 데이터 모델을 정의하여 관심사를 분리한다.
```kotlin
data class ArticleApiModel(
    val id: Long,
    val title: String,
    val content: String,
    val publicationDate: Date,
    val modifications: Array<ArticleApiModel>,
    val comments: Array<CommentApiModel>,
    val lastModificationDate: Date,
    val authorId: Long,
    val authorName: String,
    val authorDateOfBirth: Date,
    val readTimeMin: Int
)
```

<br>

### 1-3. Single Source of Truth 패턴


특정 데이터에 대해 단일한 정보 출처를 유지하는 것을 의미한다.
- Repository에 노출되는 데이터는 항상 신뢰할 수 있는 소스에서 직접 가져온 데이터여야 한다.
- 일관되고 최신화 데이터를 제공해야 하며, 특정 유형 데이터의 모든 변경사항을 한 곳에서 관리한다.
- 다른 유형이 조작할 수 없도록 데이터를 보호하고, 변경사항을 쉽게 추적하여 버그를 발견한다.


<br>

### 1-4. Data Layer에 적용되어 있는 핵심 원칙

- 단일 책임 원칙: 각 컴포넌트는 명확한 하나의 책임만 가진다.
- 의존성 역전 원칙: 구체적인 구현보다는 추상화에 의존한다.
- 높은 테스트 가능성: 모든 컴포넌트가 독립적으로 테스트 가능하다.
- 확장성: 새로운 데이터 소스나 요구사항에 쉽게 확장할 수 있다.
