# `ExecutorService` 작업 컬렉션 처리 요약
Java의 ExecutorService는 여러 개의 Callable 작업을 동시에 처리할 수 있도록 다음 두 가지 주요 메서드를 제공합니다:
## ✅ `invokeAll()`
- 기능: 모든 작업을 제출하고, 모든 작업이 완료될 때까지 기다림.
- 반환값: 각 작업의 Future<T> 리스트.
- 특징:
    - 병렬로 실행되지만, 모든 작업이 끝나야 결과를 반환.
    - 각 Future에서 get()을 호출해 결과를 얻음.
## ✅ `invokeAny()`
- 기능: 여러 작업 중 가장 먼저 완료된 하나의 작업 결과만 반환.
- 반환값: 가장 먼저 완료된 작업의 결과 T.
- 특징:
    - 나머지 작업은 자동으로 취소됨.
    - 빠른 응답이 필요한 경우 유용.

## 🧪 예제 코드 및 실행 흐름
### 📌 CallableTask 클래스
```java
public class CallableTask implements Callable<Integer> {
    private String name;
    private int sleepMs = 1000;

    public CallableTask(String name, int sleepMs) {
        this.name = name;
        this.sleepMs = sleepMs;
    }

    @Override
    public Integer call() throws Exception {
        log(name + " 실행");
        sleep(sleepMs);
        log(name + " 완료, return = " + sleepMs);
        return sleepMs;
    }
}
```
- Callable<Integer> 구현
- 지정된 시간 동안 sleep 후 해당 시간(ms)을 반환

### 🧪 invokeAll() 예제
```java
List<CallableTask> tasks = List.of(task1, task2, task3);
List<Future<Integer>> futures = es.invokeAll(tasks);
for (Future<Integer> future : futures) {
    Integer value = future.get();
    log("value = " + value);
}
```

### 🕒 실행 결과
```
task1 실행 → 완료 (1000ms)
task2 실행 → 완료 (2000ms)
task3 실행 → 완료 (3000ms)
main 스레드에서 모든 결과 출력:
value = 1000
value = 2000
value = 3000
```

###  🧪 invokeAny() 예제
```java
List<CallableTask> tasks = List.of(task1, task2, task3);
Integer value = es.invokeAny(tasks);
log("value = " + value);
```

### 🕒 실행 결과
```
task1 실행 → 완료 (1000ms)
main 스레드에서 결과 출력: value = 1000
task2, task3는 인터럽트로 취소됨
```

## 🔍 차이점 요약

| 항목             | `invokeAll()`                     | `invokeAny()`                    |
|------------------|----------------------------------|--------------------------------|
| 반환 타입        | List<Future<T>>                 | T                              |
| 완료 조건        | 모든 작업 완료 시 반환           | 하나의 작업 완료 시 반환       |
| 나머지 작업 처리 | 계속 실행                        | 완료되지 않은 작업은 취소됨    |
| 사용 목적        | 모든 결과가 필요할 때            | 가장 빠른 결과만 필요할 때     |
| 예외 처리        | InterruptedException             | InterruptedException, ExecutionException, TimeoutException |


---


