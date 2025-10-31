# yield

## 소스 코드
```java
public class YieldMain {
    static final int THREAD_COUNT = 1000;

    public static void main(String[] args) {
        for (int i = 0; i < THREAD_COUNT; i++) {
            Thread thread = new Thread(new MyRunnable());
            thread.start();
        }
    }

    static class MyRunnable implements Runnable {
        public void run() {
            for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + " - " + i);
            // 1. empty
            //sleep(1); // 2. sleep
            //Thread.yield(); // 3. yield
            }
        }
    }
}
```

## 예제 구조 요약
```java
static final int THREAD_COUNT = 1000;
for (int i = 0; i < THREAD_COUNT; i++) {
    Thread thread = new Thread(new MyRunnable());
    thread.start();
}
```

- 총 1000개의 스레드를 생성하고 실행
- 각 스레드는 0부터 9까지 숫자를 출력하고 종료
- run() 내부에서 3가지 방식 중 하나를 선택하여 실행 흐름을 제어

## 🔄 단계별 실행 흐름
### 1️⃣ 기본 구조 (MyRunnable)
```java
static class MyRunnable implements Runnable {
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + " - " + i);
            // 1. empty
            // sleep(1); // 2. sleep
            // Thread.yield(); // 3. yield
        }
    }
}
```

- 각 스레드는 10번 반복하며 자신의 이름과 숫자를 출력
- 주석 처리된 부분을 바꿔가며 Empty, sleep(1), yield()의 차이를 실험

## ⚙️ 실행 방식별 비교
### ✅ 1. Empty (기본 실행)
// 아무 것도 호출하지 않음

- 운영체제의 기본 스케줄링에 따라 스레드가 실행됨
- 하나의 스레드가 연속적으로 실행되는 경향이 있음
### 📌 특징
- 스레드 간 양보 없음
- CPU 코어 수보다 적은 스레드 수일 경우 효율적
- 실행 순서가 불균형하게 보일 수 있음

### ✅ 2. sleep(1)
```java
sleep(1); // 1밀리초 동안 TIMED_WAITING 상태로 전환
```

- 스레드가 1ms 동안 잠시 멈춤
- 그 사이 다른 스레드가 실행될 수 있음
#### 📌 특징
- 명시적 양보 + 일정 시간 대기
- RUNNABLE → TIMED_WAITING → RUNNABLE 상태 전환 발생
- 양보할 스레드가 없어도 무조건 쉼 → 비효율 가능성 있음

### ✅ 3. yield()
```java
Thread.yield(); // 실행 양보 힌트
```

- 현재 스레드가 자발적으로 CPU를 양보
- RUNNABLE 상태를 유지하며 스케줄링 큐로 재진입
#### 📌 특징
- 양보할 스레드가 있을 때만 효과적
- 양보 힌트일 뿐, 반드시 다른 스레드가 실행되는 것은 아님
- 코어 수보다 많은 스레드가 있을 때 효과가 뚜렷함

## 🧠 상태 전이 요약

| 실행 방식     | 시작 상태   | 중간 상태           | 종료 상태   |
|---------------|-------------|----------------------|-------------|
| `Empty`         | RUNNING     | RUNNING              | TERMINATED  |
| `sleep(1)`      | RUNNING     | TIMED_WAITING → RUNNABLE | TERMINATED  |
| `yield()`       | RUNNING     | RUNNABLE (스케줄링 큐 재진입) | TERMINATED  |

## 🔍 설명 요약
- Empty: 스레드는 계속 실행되며 상태 변화 없이 종료됨
- sleep(1): 스레드는 일정 시간 동안 TIMED_WAITING 상태로 전환되었다가 다시 RUNNABLE로 복귀
- yield(): 스레드는 RUNNABLE 상태


## 📊 실행 결과 비교 요약

| 실행 방식   | 실행 흐름 특징                          | 반응성 | 효율성 | 양보 의도 | 상태 전이 복잡도 |
|-------------|------------------------------------------|--------|--------|------------|------------------|
| Empty       | 한 스레드가 연속적으로 실행되는 경향       | 낮음   | 높음   | 없음       | 낮음             |
| sleep(1)    | 실행 후 1ms 대기, 다른 스레드에 기회 제공   | 높음   | 낮음   | 강제 양보   | 높음             |
| yield()     | 자발적 양보, 스케줄러가 판단해 실행 순서 조정 | 중간   | 중간   | 힌트 양보   | 중간             |

## 🔍 요약 포인트
- Empty: 운영체제 스케줄링에만 의존 → 실행 순서가 불균형할 수 있음
- sleep(1): 확실한 양보지만 불필요한 대기 발생 가능 → 효율성 저하
- yield(): 양보 힌트를 주지만 스케줄러가 판단 → 반응성과 효율성의 균형


## ✅ 결론
Thread.yield()는 스레드가 자발적으로 CPU를 양보하는 메커니즘으로,  
스케줄링 큐에 다른 스레드가 많을 때 효과적으로 작동합니다. 
반면 sleep()은 무조건 쉬기 때문에 양보할 스레드가 없을 때는 오히려 비효율적일 수 있습니다.  

---


