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

`runBlock()`의 경우 모든 작업이 끝나야만 블록이 반환되기 때문에 `runBlock()` 코드들간에 순차적인 실행이 이뤄졌지만, `launch()`의 경우 코루틴들이 동시에 실행될 수 있다.

<br>

### async() 

**`async()`함수**는 코루틴의 반환 값이 필요한 경우 사용하는 코루틴 라이브러리 함수이다. `async()` 함수는 `Deferred` 유형의 객체를 반환한다. 'await()`를 사용해 `Deferred` 객체의 결과에 접근할 수 있다.

```
import kotlinx.coroutines.*

fun main() {
    runBlocking {
        println("Weather forecast")
        /*async()는 Deferred 객체를 반환*/
        val forecast: Deferred<String> = async {
            getForecast()
        }
        val temperature: Deferred<String> = async {
            getTemperature()
        }

        /*Deferred 객체의 반환값엔 await()로 접근*/
        println("${forecast.await()} ${temperature.await()}")
        println("Have a good day!")
    }
}

suspend fun getForecast(): String {
    delay(1000)
    return "Sunny"
}

suspend fun getTemperature(): String {
    delay(1000)
    return "30\u00b0C"
}
```

### 병렬 분해

병렬 분해는 문제를 병렬로 해결할 수 있는 더 작은 하위 작업으로 세분화하는 것이다. 하위 작업의 결과가 준비되면 최종 결과로 결합할 수 있다.

**`coroutineScope()` 함수**는 모든 작업이 완료된 후에만 반환되는 함수로, 함수가 내부적으로 동시에 작업을 하고 있더라도 모든 작업이 완료되기 전까지 반환되지 않아 호출자의 입장에서는 함수가 동기 작업처럼 보이도록 한다.

```
fun main() {
    runBlocking {
        println("Weather forecast")
        println(getWeatherReport())
        println("Have a good day!")
    }
}

suspend fun getWeatherReport() = coroutineScope {
    val forecase = async { getForecast() }
    val temperature = async { getTemperature() }
    "${forecast.await()} ${temperature.await()}"
}

suspend fun getForecast(): String {
    delay(1000)
    return "Sunny"
}

suspend fun getTemperature(): String {
    delay(1000)
    return "30\u00b0C"
}
```

여기서 주목할만한 사항은 getWeatherReport 내부에선 비동기적으로 함수들을 호출하여 순서가 명확히 정해지지 않았지만, main의 내부에선 `runBlocking()`을 사용함으로써 출력문이 순서대로 진행된다.



