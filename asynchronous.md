## 비동기 코드

### launch()

Kotlin의 코루틴은 **구조화된 동시 실행**이라는 핵심 개념을 따른다. 즉, 코드가 동시 실행을 명시적으로 요청하지 않으면 기본 이벤트 루프와 계속 협력한다. 

함수를 호출하면 사용된 코루틴 수와 상관없이 함수가 반환되기 전까지 작업을 완전히 완료해야 한다고 가정한다. 예외가 발생해면 대기 중이던 작업이 없어진다. 따라서 예외가 발생했는지 작업이 성공적으로 완료되었는지와 상관없이 제어 흐름이 함수에서 반환되면 모든 작업이 완료된다.

```
import kotlinx.coroutines.*

fun main() {
    runBlocking {
        println("Weather forecast")
        launch {
            printForecast()
        }
        launch {
            printTemperature()
        }
    }
}

suspend fun printForecast() {
    delay(1000)
    println("Sunny")
}

suspend fun printTemperature() {
    delay(1000)
    println("30\u00b0C")
}
```

코루틴 라이브러리의 `launch()`를 사용하여 새로운 코루틴을 실행할 수 있다. 
`launch()`를 사용하게 되면 `runBlock()`과 달리 모든 작업이 끝나기 전에 블록을 반환하여 실행 속도가 더 빨라진다.

`runBlock()`의 경우 모든 작업이 끝나야만 블록이 반환되어 `runBlock()`간 순차적 실행이 이뤄졌지만, `launch()`의 경우 코루틴들이 동시에 실행될 수 있다.

<br>

### async() 

**`async()`함수**는 코루틴의 반환 값이 필요한 경우 사용하는 코루틴 라이브러리 함수이다.


