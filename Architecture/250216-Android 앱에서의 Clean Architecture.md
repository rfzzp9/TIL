## 1. Software Architecture


### 1-1. Software Architecture


- 아키텍처는 소프트웨어의 시스템의 구조와 그 구성요소들을 설계하는 것이다.


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


- 
![image](https://github.com/user-attachments/assets/026bad3c-5682-452a-84d8-f5b2bebd79e3)
