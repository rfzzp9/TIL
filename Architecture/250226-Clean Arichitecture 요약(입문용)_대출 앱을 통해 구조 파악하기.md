## 1. Clean Arichitecture 요약(입문용)_대출 앱을 통해 구조 파악하기


### 1-1. 대출 앱 계층


크게 대출하기 버튼, 대출을 실행하는 비즈니스 로직, 대출 정보를 저장할 공간으로 구성된다.
![image](https://github.com/user-attachments/assets/5454b110-9fe3-403b-a7cf-7808ea993c38)
<br>

위의 그림을 코드로 바꾸면 아래의 그림과 같이 정의할 수 있다.
- Presentation 계층에는 대출하기 버튼인 UI와 버튼의 action을 받아주고 Domain과의 번역을 담당하는 Controller가 있다.
- Domain 계층에는 '대출한다'라는 애플리케이션 비즈니스 룰을 담은 UseCase와 핵심 기능을 가진 엔터프라이즈 비즈니스 룰을 담은 Entity로 나눌 수 있다.
- Data 계층에는 Repository가 있다.
![image](https://github.com/user-attachments/assets/93c6a237-387e-447a-88f0-42e9c1f8c36c)
<br>


### 1-2. 대출 앱 계층 동작 구조


- UI는 버튼 클릭 action을 Controller에게 전달한다.
- Controller에서는 대출 로직을 담은 UseCase를 실행한다.
- UseCase에서는 대출에 필요한 Entity들을 통해 최종 결정을 하고 대출이 실행된 후, Repository에 이를 반영한다.
- 필요에 따라, UseCase에서 변화된 값을 다시 Controller에 전달하고 이는 UI에 반영된다.
![image](https://github.com/user-attachments/assets/74d48fa6-ee20-4cfc-a495-cd74abcf4653)


### 1-3. Clean Architecture 구조


- 위의 구조를 쉽게 표현하면 다음과 같다.
- 원 밖에 있는 InfraStructure는 쉽게 변경이 가능한 영역, 내부에 있는 Domain은 쉽게 변하지 않는 영역이다.
- 의존성은 원 외부에서 내부 원으로 흐른다. 이 때문에 Domain은 외부 영향을 받지 않고 알 필요 또한 없다.
![image](https://github.com/user-attachments/assets/58976821-ae23-41a5-b377-7c262a8f729d)
<br>

- 위의 구조를 이해했다면, 아래의 Clean Architecture 구조는 훨씬 이해하기 쉽다.
![image](https://github.com/user-attachments/assets/c3e98735-9b34-436d-878b-238284fc6b81)


### 1-4. Clean Architecture 이점


- Clean Architecture의 핵심인 '소프트웨어를 계층으로 나누어 관심사를 분리함으로써' 다음과 같은 이득을 취할 수 있다.
  - 소스코드 전반을 쉽게 파악 가능하다.
  - 수정 사항에 대한 대응이 쉬워진다.
  - 다른 계층에 영향을 주지 않는다.
  - 테스트가 쉬워진다.
  - ***높은 응집도, 낮은 결합도를 통해 생산성 또한 향상된다.***
 

- Clean Architecture를 왜 사용하는가?
  - 더 높은 생산성을 바탕으로 유저들에게 더 빠르고, 안전하게 가치 전달을 하기 위함
 <br>
 <br>
 <br>
 <br>
 
#### [출처]
##### 리디의 삶은 개발, Clean Architecture 도대체 왜 쓰는거죠? | feat. MVC, MVVM, 클린아키텍쳐 | 주니어 개발자 꿀팁
##### https://www.youtube.com/watch?v=V0PZmJ7eDvo
