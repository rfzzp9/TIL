## 1. Object, Companion object


### 1-1. Object


- 클래스를 정의하는 동시에 객체를 생성한다.
  - 싱글톤 객체를 쉽게 만들 수 있는 키워드이다.
    - 싱글톤 패턴은 단 하나의 인스턴스만 존재하도록 보장하는 디자인 패턴이다.
- 생성자 사용이 불가능하다.
- 프로퍼티, 메소드, 초기화 블록은 사용 가능하다.

```kotlin
fun main() {
    println(Counter.count)

    Counter.countUp()
    Counter.countUp()

    println(Counter.count)
    
    /* 콘솔 결과창
    초기화
    0
    2
     */
}
```


### 1-2. Companion Object


- Kotlin에서는 static 키워드가 없기 때문에, 클래스 내부에서 정적 멤버(Static Member)처럼 사용할 수 있는 객체를 만들고 싶을 때 Companion Object를 사용한다.
- 클래스 내부에 하나만 생성할 수 있고, 클래스 이름을 통해 직접 접근 가능하다.
- 객체 생성 없이 바로 사용할 수 있다.
```kotlin
fun main() {
    println(Book.NAME)
    println(Book.AGE)
    Book.create()
}

class Book {
    companion object {
        val NAME = "hello"
        const val AGE = 25
        fun create() = Book()
    }
}
```
