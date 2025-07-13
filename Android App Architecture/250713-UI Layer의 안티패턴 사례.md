## 1. UI Layer 안티패턴 실제 사례


### 1-1. 다른 프래그먼트/액티비티의 ViewModel을 직접 가져다 써 메모리 누수 발생 사례


```kotlin
// 프래그먼트A 뷰모델
class FragmentAViewModel : ViewModel() {
    val sharedData = MutableLiveData<String>()
}
```

```kotlin
// 프래그먼트B (잘못된 사용)
class FragmentB : Fragment() {

    // 직접 생성함 → 프래그먼트A에서 쓰던 ViewModel이 아님
    private val viewModel = FragmentAViewModel()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
    }
}

```
🔥 왜 안티패턴인가?

- ViewModel은 LifecycleOwner 범위에 맞게 동작해야 하는데, 의도하지 않은 Lifecycle에서 관리되면 메모리 누수의 원인이 된다.


✅ 고친 방법
- 프래그먼트A와 프래그먼트B가 같은 액티비티 안에 있음
- 액티비티 범위 공유 ViewModel을 활용함 (activityViewModels())
```kotlin
// 프래그먼트B
class FragmentB : Fragment() {
    // 고친 부분
    private val viewModel: SharedViewModel by activityViewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    }
}

```

<br>

### 1-2. View가 ViewModel의 상태를 직접 변경 (상태 노출) 사례


```kotlin
viewModel.userName.value = "김짱구"
```
🔥 왜 안티패턴인가?

- 상태가 어디에서 어떻게 바꼈는지 추적이 어렵다.

  
✅ 고친 방법
- 상태를 Immutable 형태로만 노출
```kotlin
private val _state = MutableStateFlow(UiState())
val state: StateFlow<UiState> = _state
```

<br>

### 1-3. ViewModel init 블록에서 초기 데이터를 불러오는 것은 안티패턴이다?

```kotlin
class ProductViewModel : ViewModel() {
    private val _products = MutableStateFlow<List<Product>>(emptyList())
    val products = _products.asStateFlow()
    
    init {
        // 뷰모델 생성 도중 사이드 이펙트 발생 우려
        loadProducts()
    }
}
```
🔥 왜 안티패턴인가?

- ViewModel이 언제 생성될지 모르므로 사이드 이펙트도 예측 불가능한 시점에 실행될 우려가 있다.
- UI에서 데이터를 observe하지 않는 상태에서 에러 발생 우려가 있다.


✅ 권장하는 방법
- Cold Flow를 사용한 지연 초기화를 권장하고 있다. 방법은 아래와 같다.
1. Repository에서 Cold Flow 생성
```kotlin
class ProductRepository {
    
    // Cold Flow - 구독할 때만 API 호출
    fun getProducts(): Flow<List<Product>> = flow {
        val products = api.getProducts() // 구독 시에만 호출
        emit(products)
    }
}
```

2. ViewModel에서 Hot Flow로 변환
```kotlin
class ProductViewModel(
    private val repository: ProductRepository
) : ViewModel() {
    
    //Cold Flow를 Hot Flow로 변환
    val products: StateFlow<List<Product>> = repository.getProducts()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000), //핵심
            initialValue = emptyList()
        )
}
```

2-1. SharingStarted.WhileSubscribed가 하는 일
```kotlin
// SharingStarted.WhileSubscribed(5000)의 의미
//1. 구독자가 생기면 → Flow 시작
//2. 구독자가 없어지면 → 5초 후 Flow 중단
//3. 구독자가 다시 생기면 → Flow 재시작

@Composable
fun ProductScreen(viewModel: ProductViewModel = hiltViewModel()) {
    //화면이 보일 때 구독 시작 (API 호출)
    val products by viewModel.products.collectAsStateWithLifecycle()
    
    //화면이 사라질 때 구독 중단 (5초 후 API 호출 중단)
    
    LazyColumn {
        items(products) { product ->
            ProductItem(product)
        }
    }
}
```


2-2. 5_000은 어디서 온 숫자인가?

- 실제로 화면 터치, 키 누르기 등 이벤트에 5초 안에 응답하지 않으면 ANR이 트리거 된다.
  ANR의 기한이 5초이다.
- 따라서 마지막 구독자가 5초 이상 사라지면 이미 타임아웃이며, 데이터 Flow가 더이상 UI에 영향을 줄 수 없다.


2-3. 이 방법을 사용했을 시 장점

- 실제로 필요할 때만 데이터를 로딩한다. (지연 초기화)
- UI가 보일 때만 활성화된다.
- 불필요한 호출을 방지한다.
- Configuration Change에도 물론 안전하다.


<br>
<br>
<br>


[참고자료] <br>

[[Android] 초기 데이터 로드 : LaunchedEffect vs ViewModel](https://onlyfor-me-blog.tistory.com/1095) <br>
[Google 공식문서 - UI Layer](https://developer.android.com/topic/architecture/ui-layer?hl=ko)

