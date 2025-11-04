## ✅ 문제 요약: 주문 처리 최적화
### 📌 문제 상황
- 커머스 회사의 주문 처리 시스템은 다음 3가지 작업을 순차적으로 처리:
- 재고 업데이트 (1초)
- 배송 시스템 알림 (1초)
- 회계 시스템 업데이트 (1초)
- 총 3초 소요 → 고객 대기 시간 증가

### 📌 요구사항
- 3가지 작업은 서로 독립적이므로 병렬 실행 가능
- 모든 작업이 성공해야 주문 완료
- 기존 코드를 개선하여 총 처리 시간을 줄이기

## 🧾 기존 코드: 순차 실행 (OldOrderService)

```java
static class InventoryWork {
    private final String orderNo;

    public InventoryWork(String orderNo) {
        this.orderNo = orderNo;
    }
    public Boolean call() {
        log("재고 업데이트: " + orderNo);
        sleep(1000);
        return true;
    }
}
```
```java
static class ShippingWork {
    private final String orderNo;

    public ShippingWork(String orderNo) {
        this.orderNo = orderNo;
    }
    public Boolean call() {
        log("배송 시스템 알림: " + orderNo);
        sleep(1000);
        return true;
    }
}
```
```java
static class AccountingWork {
    private final String orderNo;

    public AccountingWork(String orderNo) {
        this.orderNo = orderNo;
    }

    public Boolean call() {
        log("회계 시스템 업데이트: " + orderNo);
        sleep(1000);
        return true;
    }
}
```
```java
public void order(String orderNo) {
    InventoryWork inventoryWork = new InventoryWork(orderNo);
    ShippingWork shippingWork = new ShippingWork(orderNo);
    AccountingWork accountingWork = new AccountingWork(orderNo);

    Boolean inventoryResult = inventoryWork.call();
    Boolean shippingResult = shippingWork.call();
    Boolean accountingResult = accountingWork.call();

    if (inventoryResult && shippingResult && accountingResult) {
        log("모든 주문 처리가 성공적으로 완료되었습니다.");
    } else {
        log("일부 작업이 실패했습니다.");
    }
}
```
- 각 작업은 call() 메서드에서 1초 대기
- 순차 실행 → 총 3초 소요
### 🕒 실행 결과
```
재고 → 배송 → 회계 순서로 실행
총 소요 시간: 약 3초
```

## ✅ 개선 코드: 병렬 실행 (NewOrderService)

```java
static class InventoryWork implements Callable<Boolean> {
    private final String orderNo;

    public InventoryWork(String orderNo) {
        this.orderNo = orderNo;
    }

    @Override
    public Boolean call() {
        log("재고 업데이트: " + orderNo);
        sleep(1000);
        return true;
    }
}
```

```java
static class ShippingWork implements Callable<Boolean> {
    private final String orderNo;

    public ShippingWork(String orderNo) {
        this.orderNo = orderNo;
    }

    @Override
    public Boolean call() {
        log("배송 시스템 알림: " + orderNo);
        sleep(1000);
        return true;
    }
}
```

```java
static class AccountingWork implements Callable<Boolean> {
    private final String orderNo;
    
    public AccountingWork(String orderNo) {
        this.orderNo = orderNo;
    }

    @Override
    public Boolean call() {
        log("회계 시스템 업데이트: " + orderNo);
        sleep(1000);
        return true;
    }
}
```

```java
public void order(String orderNo) throws ExecutionException, InterruptedException {

    InventoryWork inventoryWork = new InventoryWork(orderNo);
    ShippingWork shippingWork = new ShippingWork(orderNo);
    AccountingWork accountingWork = new AccountingWork(orderNo);

    try {

        Future<Boolean> inventoryFuture = es.submit(inventoryWork);
        Future<Boolean> shippingFuture = es.submit(shippingWork);
        Future<Boolean> accountingFuture = es.submit(accountingWork);

        Boolean inventoryResult = inventoryFuture.get();
        Boolean shippingResult = shippingFuture.get();
        Boolean accountingResult = accountingFuture.get();

        if (inventoryResult && shippingResult && accountingResult) {
            log("모든 주문 처리가 성공적으로 완료되었습니다.");
        } else {
            log("일부 작업이 실패했습니다.");
        }
    } finally {
        es.close();
    }
}
```
- Callable 구현 클래스 사용
- ExecutorService.submit()으로 병렬 실행
- Future.get()으로 결과 수집
## 🕒 실행 결과
```
재고, 배송, 회계가 동시에 실행됨
총 소요 시간: 약 1초
```
---


## 📚 총정리: ExecutorService 핵심 정리
### 🔧 Executor 인터페이스
```java
public interface Executor {
    void execute(Runnable command);
}
```
- 가장 단순한 작업 실행 인터페이스

### 🔧 ExecutorService 인터페이스
```java
public interface ExecutorService extends Executor, AutoCloseable {
    void shutdown();
    List<Runnable> shutdownNow();
    boolean isShutdown();
    boolean isTerminated();
    boolean awaitTermination(long timeout, TimeUnit unit);

    <T> Future<T> submit(Callable<T> task);
    <T> Future<T> submit(Runnable task, T result);
    Future<?> submit(Runnable task);

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks);
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit);

    <T> T invokeAny(Collection<? extends Callable<T>> tasks);
    <T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit);
}
```

- submit() : 단일 작업 제출
- invokeAll() : 여러 작업을 동시에 제출하고 모두 완료될 때까지 대기
- invokeAny() : 여러 작업 중 가장 먼저 완료된 하나의 결과만 반환, 나머지는 취소
- shutdown() : Executor 종료

## 🧠 핵심 메서드 비교

| 메서드       | 설명 또는 관련 메서드 | 반환 타입         |
|--------------|------------------------|-------------------|
| submit()     | 단일 작업 제출         | Future<T>         |
| invokeAll()  | 모든 작업 완료까지 대기 | List<Future<T>>   |
| invokeAny()  | 가장 먼저 완료된 작업 반환 | T              |
| shutdown()   | Executor 종료 요청      | void              |
| close()      | Java 19부터 지원, shutdown() 포함 | void      |


## ✅ 마무리 요약
- 순차 실행은 단순하지만 비효율적 (총 3초)
- 병렬 실행으로 처리 시간 대폭 단축 (총 1초)
- ExecutorService는 병렬 작업 처리에 매우 유용
- submit, invokeAll, invokeAny를 상황에 맞게 활용

---


## 🔍 Callable<T>란?
Callable<T>는 Java의 java.util.concurrent 패키지에 포함된 작업(task)을 표현하는 인터페이스입니다.  
Runnable과 비슷하지만 다음과 같은 차이점이 있습니다:
| 항목               | Runnable         | Callable<T>       |
|--------------------|------------------|-------------------|
| 반환값             | void             | T (제네릭 타입)   |
| 예외 처리          | checked exception 처리 불가 | Exception 던질 수 있음 |
| 사용 목적          | 단순 실행        | 결과 반환 필요 작업 |


## 🧠 implements Callable<Boolean>의 의미
```java
public class InventoryWork implements Callable<Boolean> {
    @Override
    public Boolean call() throws Exception {
        // 작업 수행
        return true;
    }
}
```
- 이 클래스는 Callable<Boolean>을 구현하므로:
- call() 메서드를 반드시 정의해야 함
- call()은 Boolean 값을 반환함
- 예외를 던질 수 있음 (throws Exception)
- 즉, 이 작업은 실행 후 성공 여부를 true 또는 false로 반환하는 구조

## ✅ 사용 예: ExecutorService와 함께
```java
ExecutorService executor = Executors.newFixedThreadPool(3);
Future<Boolean> result = executor.submit(new InventoryWork("Order#1234"));
Boolean success = result.get(); // 작업 결과를 기다림
```
- submit()을 통해 Callable 작업을 제출
- Future.get()을 통해 결과를 받아옴
- 병렬 처리와 결과 수집이 가능

## 📌 요약
- implements Callable<Boolean>은 Boolean 타입의 결과를 반환하는 비동기 작업을 정의
- call() 메서드에서 실제 작업 수행
- ExecutorService와 함께 사용하면 병렬 처리와 결과 수집이 가능
- Runnable보다 유연하고 결과 중심의 작업에 적합

---

## 💡 언어별 병렬 처리 도구 예시

| 언어        | 주요 병렬 처리 도구                          | 특징 요약                     |
|-------------|-----------------------------------------------|-------------------------------|
| Java        | ExecutorService, CompletableFuture            | 고수준 API, 안정적 병렬 처리 |
| Kotlin      | coroutines, Dispatchers                       | 경량 스레드, 구조적 동시성   |
| Python      | concurrent.futures, asyncio                   | 스레드 + 이벤트 루프 기반    |
| JavaScript  | Promise, async/await                          | 비동기 함수 중심              |
| Rust        | tokio, rayon, async/.await                    | 안전한 병렬성, 고성능         |
| Go          | goroutine, channel                            | 간결한 병행성 모델            |
| C# (.NET)   | Task, async/await, Parallel                   | 풍부한 병렬 API               |

---
