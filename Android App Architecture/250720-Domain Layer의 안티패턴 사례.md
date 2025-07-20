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
```kotlin
class LoginUseCase(
    private val clearSessionUseCase: ClearSessionUseCase,    // Low Level
    private val authenticateUseCase: AuthenticateUseCase     // Low Level
) {
    suspend operator fun invoke(email: String, password: String): LoginResult {
        clearSessionUseCase()  // 세션 정리
        val result = authenticateUseCase(email, password)  // 인증
        return LoginResult.Success(result.user)
    }
}

class LogoutUseCase(
    private val requestLogoutUseCase: RequestLogoutUseCase,  // Low Level
    private val clearSessionUseCase: ClearSessionUseCase     // Low Level
) {
    suspend operator fun invoke() {
        requestLogoutUseCase()  // 로그아웃 요청
        clearSessionUseCase()   // 세션 정리
    }
}
```

<br>
<br>

> Google App Architecture의 Domain Layer
> - (단순함 추구) 복잡하지 않다면 UseCase를 사용하지 않는다.
> - (점진적 복잡성) 필요할 때만 레이어를 추가한다.
> - (실용적인 접근) 완벽한 분리보다는 유지보수성을 우선시한다.
> - (테스트) 의존성 주입과 추상화를 활용해 테스트 가능성을 높인다.


<br>
<br>
<br>
<br>

[참고 자료]

[[DroidKnights 2023] 박종혁 - 빈혈(anemic) 도메인 모델과 쓸모없는 유스케이스 그리고 비대한(Bloated) 뷰모델에 대해 생각해보기](https://www.youtube.com/watch?v=3mR8_vT7m1U) <br>
[[Android] Clean Architecture 를 도입하며](https://vagabond95.me/posts/clean-architecture-1/) <br>
[안드로이드 클린 아키텍처 도메인 레이어 설계](https://chanho-study.tistory.com/116)
