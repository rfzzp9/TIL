## 1. Android 기본 요소


### 1-1. 앱 구성요소


앱의 각 구성요소는 시스템이나 사용자가 앱에 진입할 수 있는 진입점이다. <br>
앱의 구성요소는 네 가지 유형이 있다.
- Activity
- Service
- Broadcast Receiver
- Content Provider


### 1-2. Activity


- Activity는 사용자와 상호작용하기 위한 진입점이다.
  - 앱을 실행할 때는 앱을 전체적으로 호출하는 것이 아니라 앱의 액티비티를 호출한다.
- 모든 앱에 반드시 1개 이상 존재한다.
- Activity는 사용자와 상호작용을 위한 UI가 있다.
  - 앱이 실행되면 화면이 표시된다.
  - 사용자의 입력값을 받는다. (화면 클릭, 더블 클릭, 롱클릭, 스와이프, 드래그 앤 드랍 등)
  - 사용자에게 제공하고자 하는 내용을 화면에 표시한다.
- Lifecycle이 있다. (별도 다룰 예정)


### 1-3. Service


- 백그라운드에서 오래 실행되는 작업 수행을 위한 컴포넌트이다.
- 사용자가 다른 앱으로 전환하더라도 백그라운드에서 계속 실행된다.
- UI가 없다.
- 포그라운드 서비스 <br>
  - 사용자에게 잘 보이는 작업이다.
  - 포그라운드 서비스의 경우 반드시 알림을 표시해야 하며, 사용자가 앱과 상호작용하지 않을 때도 계속 실행된다.
  - EX) 음악 재생
- 백그라운드 서비스 <br>
  - 사용자에게 직접 보이지 않는 작업이다.
  - EX) 저장소 압축, 게임 업데이트, 파일 압축 등
  - 앱이 API 수준 26 이상을 대상으로 할 경우
    - 즉시 실행해야 하는 작업 : Work Manager 사용 권장
    - 지연 작업 : Alarm Manager 사용 권장
- 바인드 서비스 <br>
  - 앱 컴포넌트가 bindService를 호출해 서비스를 호출하면 서비스가 바인딩 된다.
  - 바인딩 된 서비스는 클라이언트-서버 인터페이스를 제공해 서비스와 상호작용한다.
  - 여러개가 한꺼번에 바인딩 될 수 있고, 바인딩 된 컴포넌트가 모두 종료되면, 서비스도 종료된다.


### 1-4. Broadcast Receiver


- 안드로이드 OS에서 발생하는 이벤트와 정보를 앱에서 수신할 수 있도록 하는 구성요소이다.
- UI는 없다.
- EX) 화면이 꺼짐, 배터리가 부족함, 사진을 캡처함 등


### 1-5. Content Provider


- 파일 시스템, SQLite 데이터베이스, 웹상이나 앱이 액세스할 수 있는 모든 영구 저장위치에 저장 가능한 앱 데이터의 공유형 집합을 관리한다.
- 다른 앱은 Content Provider를 통해 해당 데이터를 질의하거나, 수정할 수 있다.
- EX) 연락처 정보, 갤러리 이미지/비디오 등


### 1-6. Manifest


- 앱의 필수적인 정보를 담고 있는 파일이다.
- 앱의 패키지명, 앱의 구성요소, 권한, 필요한 기능을 담고 있다.


### 1-7. Intent


- 구성요소 간의 통신을 할 수 있게 한다.
- 앱에 포함된 구성요소 이외에 다른 앱의 구성요소와도 통신할 수 있다.
- 명시적 인텐트
  - 특정 컴포넌트, 액티비티를 명확히 특정해 실행할 경우
  - EX) AActivity에서 BActivity 실행을 호출할 경우
- 암시적 인텐트
  - 동작을 특정하여 실행긴 했지만 실행될 대상이 달라질 수 있는 경우
  - EX) 특정 URL을 실행이라는 액션을 요청할 경우 웹브라우저 기능을 가진 다수의 앱이 호출될 수 있다.
