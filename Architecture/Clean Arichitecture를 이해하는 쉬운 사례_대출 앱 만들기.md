## 1. Clean Arichitecture를 이해하는 쉬운 사례_대출 앱 만들기


### 1-1. 대출 앱 계층


- 크게 대출하기 버튼, 대출을 실행하는 비즈니스 로직, 대출 정보를 저장할 공간으로 구성된다.
![image](https://github.com/user-attachments/assets/5454b110-9fe3-403b-a7cf-7808ea993c38)

- 위의 그림을 코드로 바꾸면 아래와 같이 정의할 수 있다.
- Presentation 계층에는 대출하기 버튼인 UI와 버튼의 action을 받아주고 Domain과의 번역을 담당하는 Controll가 있다.
- Domain 계층에는 
