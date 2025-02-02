## 1. Data, Sealed class


### 1-1. Data class


- 데이터를 저장하고 다루기 쉽게 하기 위해 만들어진 클래스이다.
  - toString(), hashCode(), equals(), copy() 메서드를 자동으로 생성해준다.
  - override하면 직접 코드를 구현 가능하다.
- 1개 이상의 프로퍼티가 있어야 한다.
- abstract, open, sealed, inner를 붙일 수 없다.
- 다른 클래스에 상속이 불가능하다.
```kotlin
fun main() {
    val person = Person("수지", 24)
    val dog = Dog("해피", 24) 

    println(person.toString())    //Person@6fadae5d
    println(dog.toString())       //Dog(name=해피, age=24)
}

class Person(
    val name: String,
    val age: Int
)

data class Dog(    //해당 클래스에는 toString() 메서드 등이 자동 생성되어 있다.
    val name:String,
    val age: Int
)
```


### 1-2. Sealed class


- 추상 클래스로, 상속받은 자식 클래스의 종류를 제한한다.
  - 컴파일러가 Sealed class의 자식 클래스가 어떤 것인지 안다.
  - when 표현식에서 안전한 타입 검사가 가능하다.
```kotlin
fun main() {
    val cat : Cat = BlueCat()
    val result = when(cat) {  //else를 적지 않아도 된다. 컴파일러가 자식 클래스가 어떤 것인지 알기 때문.
        is BlueCat -> { "blue" }
        is RedCat -> { "red" }
        is GreenCat -> { "green" }
    }

    println(result)
}

sealed class Cat
class BlueCat : Cat()  //자식 클래스
class RedCat : Cat()   //자식 클래스
class GreenCat : Cat() //자식 클래스
```
