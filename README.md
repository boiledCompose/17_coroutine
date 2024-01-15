## 동기 코드

동기 코드에서는 한 번에 하나의 작업만 진행된다. 즉 여러 작업들이 있는 경우 순차적으로 진행된다.
```
fun main() {
    println("weather forecast")
    println("Sunny")
}
```
`println`은 동기 호출 메서드로, 이 메서드가 동작이 완료되어야만 다음 `println` 문으로 넘어간다.

`main()`의 각 함수 호출이 동기 호출이므로 전체 `main()`함수는 동기식이다.

이러한 동기함수는 모든 작업들이 완전히 완료되어야 반환된다.

<br>

### 지연 추가

`delay()`는 Kotlin 코루틴 라이브러리에서 제공되는 특수 정지 함수이다. 

인자로 **milliseconds**를 받아 해당 시간동안 지연을 시킨다.

허나 이 메서드는 코루틴의 내부에서 실행되어야한다. 코루틴을 학습하기 위해선 `runBlocking` 함수를 호출하여 기존 코드를 감싼다.

`runBlocking()`는 다시 시작할 준비가 되면 각 작업을 중단된 지점에서 계속 진행하여 여러 작업들을 한 번에 처리할 수 있는 이벤트 루프를 실행한다.
```
import kotlinx.coroutines.*

fun main() {
    runBlocking{
        println("Weather forecast")
        delay(1000)
        println("Sunny")
    }
}
```

`runBlocking()`은 동기식으로, 블록 내의 모든 작업이 완료될 때까지는 반환되지 않는다.

`runBlocking()` 내부에 포함된 `delay()`로 인해 작업 도중 지연이 발생하지만, 이 지연 동안 다른 코루틴 내 작업들이 실행될 수 있다. 지연이 끝나면 다시 `runBlocking()` 블록으로 돌아와 작업을 재개한다.

### 정지 함수

코드가 길어지고 복잡해짐에 따라 `delay()`와 같은 지연을 자체 함수로 추출하여 관리하는 경우, 지연을 포함하는 함수는 **`suspend` 함수, 정지 함수**로 정의해야 한다.
```
import kotlinx.coroutines.*

fun main() {
    runBlocking{
        println("Weather forecast")
        printForecast()
    }
}

suspend fun printForecast() {
    delay(1000)
    println("Sunny")
}
```

정지 함수는 정지되었다가 나중에 다시 시작될 수 있는 함수를 의미한다. 정지 함수는 다른 정지 함수에서만 호출될 수 있다.

**정지 지점**이란 함수 내에서 함수 실행을 정지할 수 있는 위치로, 정지 함수 내에 정지 지점을 0개 이상 포함할 수 있다.

>[!NOTE]
> 코드의 실행 시간을 알아보고 싶으면 `measureTimeMillis{ code }`로 코드를 감싸면 된다.
