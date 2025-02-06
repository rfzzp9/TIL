## 1. Activity Lifecycle


### 1-1. Activity 활동 수명 주기


- Activity 클래스는 6가지 콜백으로 이루어진 핵심 집합을 제공한다.
- onCreate(), onStart(), onResume, onPause(), onStop(), onDestroy() 시스템은 활동이 새 상태로 전환될 때 각 콜백이 호출된다.
![image](https://github.com/user-attachments/assets/183a72f6-0d94-4b13-945b-e2bc1f8be464)


### 1-2. onCreate()


- onCreate() 콜백은 먼저 활동을 생성할 때 실행되는 것으로, 필수적으로 구현해야 한다.
- 전체 수명 주기 동안 한 번만 발생한다.
  - 멤버 변수 정의, UI 구성(setContentView, xml 레이아웃 파일 정의)
- Activity 이전 저장 상태가 포함된 Bundle 객체인 savedInstanceState 매개변수를 반환한다.
  - 활동에 존재하지 않는 경우 Bundle 객체의 값은 null이다.
- onCreate() 콜백에 머무르지 않고 자동으로 onStart(), onResume() 콜백이 차례대로 호출된다.


### 1-3. onStart()


- onStart() 콜백이 호출되면 앱은 Activity를 포그라운드에 보내 상호작용할 수 있도록 준비한다.
- Activity가 사용자에게 표시된다.


### 1-4. onResume()


- Activity가 포그라운드에서 사용자와 상호작용할 수 있는 상태가 된다.
- 앱에서 포커스가 떠날 때까지 onResume() 상태에 머무른다.


### 1-5. onPause()


- 사용자가 떠난다는 첫 번째 신호로 onPause() 콜백을 호출한다.
  - Activity가 포그라운드에 있지 않음을 나타내지만 소멸된 것은 아니다.
  - 잠시 후 다시 시작할 작업을 일시 중지하거나 조정한다.
- onPause() 콜백을 통해 실행중이지 않을 때 필요하지 않은 리소스를 해지할 수 있다.
  - 이때 사용자가 Activity와 상호작용할 수 없기 때문이다.
- onPause() 콜백 상태에서 데이터를 저장하거나, 네트워크 호출, DB의 IO 작업을 하면 안된다.
  - 매우 짧은 시간이라 onPause() 상태가 끝나기 전에 Activity가 종료될 수 있다.


### 1-6. onStop()


- Activity가 사용자에게 더 이상 표시되지 않는 상태이다.
- Activity가 종료될 때 처리해야 하는 작업이 있다면 onPause() 상태가 아니라, onStop() 상태에서 실행하면 된다.
  - 비교적 CPU 집약적인 종료 작업을 수행할 수 있다.
  - 처리할 더 나은 시간을 찾을 수 없을 때 onStop()에서 작업을 수행한다.


### 1-7.onDestroy()


- Activity가 완전히 소멸되기 전에 호출된다.
- finish가 호출되어 종료되거나, 혹은 configurationChange(EX - 기기 회전, 멀티 윈도우)로 인해 일시적으로 소멸될 때 호출된다.
