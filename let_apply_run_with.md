# let, apply, run, with
---

## let
함수를 호출하는 객체를 이어지는 블록의 인자로 넘기고 블록의 결과값을 반환한다.

### 사용 예

1. 불필요한 선언 제거

```kotlin
val padding = TypedValue.applyDimension(
        TypedValue.COMPLEX_UNIT_DIP, 16f, resources.displayMetrics).toInt()
// 왼쪽, 오른쪽 padding 설정
setPadding(padding, 0, padding, 0)
```

```kotlin
TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 16f,
        resources.displayMetrics).toInt().let { padding ->
    // 계산된 값을 padding 이라는 이름의 인자로 받음
    setPadding(padding, 0, padding, 0)
}
```

위의 코드에서 padding은 단지 setPadding에서만 사용되고 이후에는 사용되지 않는다.
따라서 아래 코드와 같이 let을 사용하고 내부에서 인자로 padding을 받아서 처리하면 불필요한 선언을 할 필요가 없다.

2. if (null != obj) ... 제거

```kotlin
// null일 수 있는 변수 obj
var obj: String?

// ...작업 수행...

// obj가 null이 아닐 경우 작업 수행 (기존 방식)
if (null != obj) {
    Toast.makeText(applicationContext, obj, Toast.LENGTH_LONG).show()
}

// obj가 null이 아닐 경우 작업 수행 (Safe calls + let 사용)
obj?.let {
    Toast.makeText(applicationContext, it, Toast.LENGTH_LONG).show()
}
```

## apply
함수를 호출하는 객체를 이어지는 블록의 **리시버**로 전달하고, 객체 자체를 반환한다.

리시버란 바로 이어지는 블록 내에서 메서드 및 속성에 바로 접근할 수 있도록 할 객체를 의미한다.

### 사용 예
특정 객체를 생성하면서 동시에 함께 호출해야 하는 초기화 코드가 있는 경우 사용할 수 있다.

```kotlin
val param = LinearLayout.LayoutParams(0, LinearLayout.LayoutParams.WRAP_CONTENT)
param.gravity = Gravity.CENTER_HORIZONTAL
param.weight = 1f
param.topMargin = 100
param.bottomMargin = 100
```

```kotlin
val param = LinearLayout.LayoutParams(0, LinearLayout.LayoutParams.WRAP_CONTENT).apply {
    gravity = Gravity.CENTER_HORIZONTAL
    weight = 1f
    topMargin = 100
    bottomMargin = 100
}
```

위의 코드를 아래와 같이 처리하여 바로 param의 프로퍼티에 접근한다.

다른 예로는 Builder pattern이 있다.

```kotlin
Entity.newBuilder()
  .setName("number")
  .setValue(lighting_spinner.selectedItem.toString())
  .build())
```

```kotlin
Entity.newBuilder().apply {
  name = "number"
  value = lighting_spinner.selectedItem.toString()
}.build()
```

위에서 빌더패턴을 call 할 때 setName과 같은 것에 일일히 접근해야 했는데 apply를 사용하여 직접 name과 value에 접근하여 set 해줄 수 있다.

## run

인자가 없는 익명함수처럼 동학하는 형태와 객체에서 호출하는 형태 두 가지가 있다.

객체 없이 run 함수를 사용하면 이어지는 블럭 내에서 처리할 작업들을 넣어줄 수 있으며
일반함수와 마찬가지로 값을 반환하지 않거나 특정값을 반환할 수 있다.

객체에서 run 함수를 호출할 경우 호출하는 객체를 이어지는 블록의 리시버로 전달하고 블록의 **결과값**을 리턴한다.

### 사용 예
apply와 매우 유사하지만
- apply는 새로운 객체를 생성함과 동식에 연속적인 작업이 필요할때 사용한다.
- run은 이미 생성된 객체에 연속된 작업이 필요할 때 사용한다.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    supportActionBar?.run {
        setDisplayHomeAsUpEnabled(true)
        setHomeAsUpIndicator(R.drawable.ic_clear_white)
    }
    ...
}
```

supportActionBar가 있을 경우에만 블록내의 것들을 실행한다.

## with
인자로 는 객체를 이어지는 블록의 리시버로 전달하며 블록의 **결과값**을 반환한다.

with는 run과 매우 비슷하다. run 함수는 with함수를 좀 더 편리하게 사용하기 위해 let과 with를 합쳐놓은 형태라고도 볼 수 있다.

```kotlin
supportActionBar?.let {
    with(it) {
        setDisplayHomeAsUpEnabled(true)
        setHomeAsUpIndicator(R.drawable.ic_clear_white)    
    }
}
```

let만 사용하면 해당 객체를 리턴하기 때문에 with를 사용했고 그것을 이용해 내부 메소드를 사용 후에 그 결과값을 리턴 할 것이다.

run은 안전한 호출을 지원하지만 with는 지원하지 않으므로 특별한 경우가 아니라면 run 함수를 더 자주 사용할 것이다.