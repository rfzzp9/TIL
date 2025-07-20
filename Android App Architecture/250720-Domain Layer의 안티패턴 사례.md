## 1. Domain Layer 안티패턴 실제 사례


### 1-1. 거대한 유스케이스 (=God Use Case)

- 문제의 코드 <br>
  하나의 유스케이스가 너무 많은 책임을 가지고 있다.
```kotlin
class UserUseCase {
    fun login() { ... }
    fun updateProfile() { ... }
    fun deleteAccount() { ... }
    fun sendEmail() { ... }
    fun validatePassword() { ... }
}
```

- 개선 방법 <br>
  **단일 책임의 원칙**을 준수한 코드
```kotlin
class LoginUseCase { ... }
class LogoutUseCase { ... }
class UpdateProfileUseCase { ... }
```

<br>

### 1-2. 쓸모 없는 유스케이스 (=Useless UseCase)

- 문제의 코드 <br>
  중요한 행위는 없고, 단순 데이터 전달만 하고 있다.
  <br>

```kotlin
class GetUserUseCase(private val repository: UserRepository) {
    suspend fun execute(id: String) = repository.getUser(id)
}
```

<br>

- 개선 방법 <br>
  ViewModel에서 직접 repository에 접근하는게 더 적절하다. <br>
  혹은 ViewModel이 너무 많은 책임을 가지고 있다면 UseCase로 로직을 옮기는게 좋다.

<br>

### 1-3. Data Layer의 모델을 Domain Layer에서 직접 사용


- 문제의 코드 <br>
  Network 응답 모델을 그대로 사용하고 있다.
```kotlin
  class GetNewsUseCase(private val newsApi: NewsApi) {
    suspend operator fun invoke(): List<NewsResponse> {  // Network 모델 반환
        return newsApi.getLatestNews() 
    }
}
```

<br>

- 개선 방법 <br>
  Domain 전용 모델을 사용했다.
```kotlin
// Domain 모델
data class News(
    val id: String,
    val title: String,
    val content: String,
    val imageUrl: String?,
    val publishedAt: LocalDateTime,
    val isFavorite: Boolean
)

// Repository에서 모델 매핑
class NewsRepositoryImpl(
    private val newsApi: NewsApi,
    private val newsDao: NewsDao
) : NewsRepository {
    
    override suspend fun getLatestNews(): List<News> {
        return newsApi.getLatestNews().map { it.toDomainModel() }
    }
    
    override suspend fun getFavoriteNews(): List<News> {
        return newsDao.getFavoriteNews().map { it.toDomainModel() }
    }
}

// 확장 함수로 모델 매핑 로직 분리
private fun NewsResponse.toDomainModel(): News = News(
    id = id,
    title = title,
    content = content,
    imageUrl = imageUrl,
    publishedAt = LocalDateTime.parse(publishedAt),
    isFavorite = false
)

private fun NewsEntity.toDomainModel(): News = News(
    id = id,
    title = title,
    content = content,
    imageUrl = imageUrl,
    publishedAt = publishedAt,
    isFavorite = isFavorite
)
```

<br>

### 1-4. UseCase간 순환 참조 (순환 의존성)

- 문제의 코드 <br>
  UseCase간 서로 참조 상태를 가진다.
```kotlin
class LoginUseCase(
    private val logoutUseCase: LogoutUseCase  // 순환참조
) {
    suspend operator fun invoke(email: String, password: String): LoginResult {
        logoutUseCase()  // 다른 Use Case 호출
        
        return LoginResult.Success
    }
}

class LogoutUseCase(
    private val loginUseCase: LoginUseCase  // 순환참조
) {
    suspend operator fun invoke() {
        if (shouldAutoLogin()) {
            loginUseCase(savedEmail, savedPassword)  // 순환 호출
        }
    }
}
```

> UseCase는 다른 UseCase를 주입받을 수 있는가?
> - High Level UseCase는 High Level UseCase를 참조해서는 안된다.
> - High level UseCase가 Low level UseCase를 주입받아 사용할 때 가능하다.
> - Low level UseCase : 단일 로직을 수행한다. (EX. 이미지 URL을 받아온다.)
> - High level UseCase : Low level UseCase를 조합하여 비즈니스 로직을 수행한다. (EX. 게시물을 작성한다.) <br>


- 개선 방법 <br>
  UseCase간 서로 참조 상태를 가진다.
