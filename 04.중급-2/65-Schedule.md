# Timer / TimerTask
Java에서 Timer와 TimerTask를 이용해 일정한 간격으로 작업을 수행하는 스케줄링 예제.  
아래에 Timer, ScheduledExecutorService, 그리고 Spring에서의 스케줄링까지 다양한 방식으로 정리.

## ⏱️ 1. Timer와 TimerTask 방식
### ✅ 기본 구조
```java
Timer timer = new Timer();
timer.scheduleAtFixedRate(new TimerTask() {
    @Override
    public void run() {
        System.out.println("Message");
    }
}, 0, TimeUnit.SECONDS.toMillis(1)); // 1초마다 실행
```

### ✅ 주요 스케줄링 메서드 요약
| 메서드                        | 설명                                                                 |
|-------------------------------|----------------------------------------------------------------------|
| `schedule(task, delay)`       | 지정된 시간(delay) 후에 한 번만 작업 실행 (`Timer`, `ScheduledExecutorService`) |
| `scheduleAtFixedRate(...)`    | 고정된 간격으로 반복 실행 (이전 작업 시작 시간 기준)                        |
| `scheduleWithFixedDelay(...)` | 이전 작업이 끝난 후 일정 시간(delay)을 두고 다음 작업 실행                     |

## ⚙️ 2. ScheduledExecutorService 방식 (추천)
```java
import java.util.concurrent.*;

public class ScheduleExecutorExample {
    public static void main(String[] args) {
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        Runnable task = () -> System.out.println("Message");
        scheduler.scheduleAtFixedRate(task, 0, 1, TimeUnit.SECONDS);
    }
}
```

### ✅ 장점
- Timer보다 예외 처리에 강함
- 여러 작업을 병렬로 처리 가능
- shutdown()으로 종료 제어 가능

## 🌱 3. Spring에서 @Scheduled 사용
```java
@Component
public class ScheduledTask {
    @Scheduled(fixedRate = 1000)
    public void printMessage() {
        System.out.println("Message");
    }
}
```

### ✅ 설정 추가
```
# application.properties
spring.main.allow-bean-definition-overriding=true
spring.task.scheduling.pool.size=1
```
```java
@EnableScheduling
@SpringBootApplication
public class App {}
```


## 📦 활용 팁
- Timer는 단일 스레드 기반이라 예외 발생 시 전체 중단될 수 있음 → ScheduledExecutorService가 더 안전
- scheduleAtFixedRate는 정해진 간격 기준, scheduleWithFixedDelay는 작업 종료 기준
- Spring에서는 @Scheduled(cron = "...")으로 복잡한 주기 설정도 가능


# cron / ScheduledFuture

cron 표현식, 스케줄 취소 방법, ScheduledFuture 결과 처리 정리.

## ⏰ 1. Cron 표현식 예제 (Spring 기준)
### ✅ 기본 형식
```
초 분 시 일 월 요일
```

### ⏰ Cron 표현식 예제 요약

| 표현식               | 실행 시점 설명                             |
|----------------------|--------------------------------------------|
| `0 0 * * * *`        | 매 시간 정각 (예: 01:00, 02:00, ..., 23:00) |
| `0 30 9 * * *`       | 매일 오전 9시 30분                         |
| `0 0 12 * * MON-FRI` | 평일(월~금) 정오 12시                      |
| `0 */10 * * * *`     | 매 10분마다 (예: 00:00, 00:10, 00:20...)   |
| `0 0 1 1 * *`        | 매년 1월 1일 새벽 1시                      |


### ✅ Spring 예시
```java
@Scheduled(cron = "0 0 9 * * MON-FRI")
public void weekdayMorningTask() {
    System.out.println("평일 오전 9시에 실행");
}
```
- 💡 @EnableScheduling 어노테이션이 필요합니다.


## ❌ 2. 스케줄 취소 방법
### ✅ TimerTask 취소
```java
Timer timer = new Timer();
TimerTask task = new TimerTask() {
    public void run() {
        System.out.println("실행 중...");
    }
};
timer.schedule(task, 0, 1000);

// 5초 후 취소
Thread.sleep(5000);
task.cancel();      // 작업 취소
timer.cancel();     // 타이머 전체 종료
```

### ✅ ScheduledExecutorService 취소
```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
ScheduledFuture<?> future = scheduler.scheduleAtFixedRate(() -> {
    System.out.println("반복 작업");
}, 0, 1, TimeUnit.SECONDS);

// 5초 후 취소
Thread.sleep(5000);
future.cancel(true); // true: 인터럽트 허용
scheduler.shutdown();
```


## 📦 3. ScheduledFuture 결과 처리
### ✅ 단일 작업 결과 받기
```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

Callable<String> task = () -> {
    Thread.sleep(2000);
    return "작업 완료!";
};

ScheduledFuture<String> future = scheduler.schedule(task, 1, TimeUnit.SECONDS);

// 결과 받기
String result = future.get(); // 블로킹
System.out.println(result);   // "작업 완료!"
```



### ✅ 예외 처리 포함
```java
try {
    String result = future.get();
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```


## 🧠 스케줄링 요약

| 역할/기능         | 관련 클래스 또는 인터페이스         | 주요 메서드 또는 동작                         |
|-------------------|--------------------------------------|-----------------------------------------------|
| 작업 등록         | `Timer`, `ScheduledExecutorService` | `scheduleAtFixedRate()`                       |
| 작업 취소         | `TimerTask`, `ScheduledFuture`      | `cancel()`                                    |
| 결과 처리 및 상태 | `ScheduledFuture<T>`                | `get()`, `isDone()`, `isCancelled()`          |


## 활용 팁
- Timer는 단일 스레드 기반이라 예외 발생 시 전체 중단 가능 → ScheduledExecutorService가 더 안전
- ScheduledFuture는 반복 작업뿐 아니라 지연 실행 후 결과 반환에도 사용 가능
- cancel(true)는 실행 중인 작업에 인터럽트를 시도함

---


