## 1. Software Architecture


### 1-1. Software Architecture


- 아키텍처는 소프트웨어의 시스템 구조와 그 구성요소들을 설계하는 것이다.


### 1-2. Software Architecture는 왜 필요할까


- 크기와 복잡도가 증가하게 될 경우 코드 파악이 어려워진다.
- 같이 개발하는 개발자의 수에 따라 명확하고 일관된 아키텍처가 필요하다.
- 절대적인 코드량이 증가하는 단점은 있다.
- 아래는 각각 아키텍처 반영 전, 반영 후의 코드이다.


```kotlin
//before
data class User(val id: Int, val name: String, val email: String)

fun main() {
    println(fetchUserFromNetwork(1))
}

private fun fetchUserFromNetwork(userId: Int) : User? {
    // ... 네트워크에서 userId에 해당하는 사용자 정보를 가져오는 코드 ...
    return if(userJson != null) {
        User(userJson)
    } else {
        null
    }
}
```
```kotlin
//after
// -- domain layer
interface UserRepository {
    fun getUser(userId: Int): User?
}

data class User(val id: Int, val name: String, val email: String)

// --data layer
class NetworkUserRepository(private val apiClient: ApiClient) : UserRepository {
    override fun getUser(userId: Int): User? {
        val userJson = apiClient.fetchUser(userId)
        return if(userJson != null) {
            User(id = userId, name = "", email = "")
        } else {
            null
        }
    }
}

class ApiClient {
    fun fetchUser(userId: Int): String? {
        // ... 네트워크에서 userId에 해당하는 사용자 정보를 가져오는 코드 ...
        return "{}"
    }
}

//presentation layer
fun main() {
    val apiClient = ApiClient()
    val userRepository: UserRepository = NetworkUserRepository(apiClient)
    val user = userRepository.getUser(1)
    println(user)
}
```
<br>

## 2. Android 앱에서의 Clean Architecture


### 2-1. Clean Architecture


#### 관심사의 분리


- 관심사의 분리란 각 기능(관심사)을 독립적인 모듈로 나누어, 서로 영향을 최소화하고 유지보수를 쉽게 하는 것을 목표로 한다.


#### Clean Architecture의 계층 구조


- Android 앱 Architecture에는 3가지 레이어가 존재하며 이들은 각각의 '책임'을 명확히 가진다.
- UI Layer, Domain Layer, Data Layer로 구성된다.
- 아래 다이어그램의 화살표는 클래스 간의 종속성을 나타낸다.
![image](https://github.com/user-attachments/assets/026bad3c-5682-452a-84d8-f5b2bebd79e3)
1) UI Layer (Presentation Layer)
   - UI Layer에 해당하는 모듈은 다음과 같다.
     - Activity, Fragment + XML View, ComposeUI
     - ViewModel
   - ViewModel은 UI에 바인딩되는 데이터를 보유하고 이를 UI에 노출하며 로직을 처리하는 모듈이다. <br>
   ![image](https://github.com/user-attachments/assets/c83faf4e-92f0-4225-9280-365161c1a934)

2) Domain Layer
   - Domain Layer는 UI Layer와 Data Layer 사이에 있는 선택적 레이어이다.
   - Domain Layer에 해당하는 모듈은 다음과 같다.
     - UseCase
   - 복잡한 비즈니스 로직이나 여러 ViewModel에서 재사용되는 간단한 비즈니스 로직의 캡슐화를 담당한다.
   ![image](https://github.com/user-attachments/assets/707d15ac-b99b-44a8-9878-939a58fae8ef)
3) Data Layer
   - Data Layer에는 2가지 모듈이 포함되어 있다.
     - Repository
     - DataSource
   - 앱의 데이터 생성, 저장, 변경 방식을 결정하는 규칙으로 구성된다.
   - Data Layer는 앱에서 처리하는 다양한 유형의 데이터별로 저장소 클래스를 만들어야 한다.
   - 저장소 클래스에서는 비즈니스 로직 포함, 앱의 나머지 부분에서 데이터 소스 추상화, 여러 데이터 간의 충돌 해결, 데이터 변경사항을 한 곳에 집중, 앱의 나머지 부분에 데이터 노출 작업을 담당한다.
   ![image](https://github.com/user-attachments/assets/ef0a334f-ce7e-4ed4-a6d5-cbcb192c3232)

