## 1. UI Layer 안티패턴 사례


### 1-1. 다른 프래그먼트/액티비티의 ViewModel을 직접 가져다 써 메모리 누수 발생 사례


```kotlin
// FragmentAViewModel.kt
class FragmentAViewModel : ViewModel() {
    val sharedData = MutableLiveData<String>()
}
```

```kotlin
// FragmentB.kt (잘못된 사용)
class FragmentB : Fragment() {

    // 직접 생성함 → 이건 FragmentA에서 쓰던 ViewModel이 아님
    private val viewModel = FragmentAViewModel()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
    }
}

```
