# volatile
- 아래에 volatile 키워드의 역할과 효과를 예제 중심으로 깔끔하게 정리.

## 🔒 volatile 키워드 정리
### ✅ 목적
- 멀티스레드 환경에서 메모리 가시성(memory visibility) 문제를 해결하기 위한 키워드

### 🧪 예제: VolatileFlagMain
```java
volatile boolean runFlag = true;
```

- main 스레드가 runFlag = false로 변경
- work 스레드는 while(runFlag) 조건을 즉시 감지하고 루프 탈출
- 모든 스레드가 정상 종료됨

## 실행 코드
```java
public class VolatileFlagMain {
    public static void main(String[] args) {
        MyTask task = new MyTask();
        Thread t = new Thread(task, "work");
        log("runFlag = " + task.runFlag);

        t.start();

        sleep(1000);
        log("runFlag를 false로 변경 시도");
        task.runFlag = false;
        log("runFlag = " + task.runFlag);
        log("main 종료");
    }
    
    static class MyTask implements Runnable {
        //boolean runFlag = true;
        volatile boolean runFlag = true;
    
        @Override
        public void run() {
            log("task 시작");
            while (runFlag) {
                //runFlag가 false로 변하면 탈출
            }
            log("task 종료");
        }
    }
}
```

## 📌 실행 결과
```
15:39:59.830 [ main] runFlag = true
15:39:59.830 [ thread-0] task 시작
15:40:00.837 [ main] runFlag를 false로 변경 시도
15:40:00.838 [ thread-0] task 종료
15:40:00.838 [ main] runFlag = false
15:40:00.838 [ main] main 종료
```
- runFlag 변경 직후 task 종료가 출력됨 → 가시성 확보 성공

## 🔍 volatile의 동작 방식

| 항목               | volatile 변수                                | synchronized / Atomic 변수                   |
|--------------------|-----------------------------------------------|----------------------------------------------|
| 메모리 접근 방식     | 항상 메인 메모리에서 읽고 씀                   | 메인 메모리 접근 + 락 또는 원자 연산 사용     |
| 캐시 사용 여부       | CPU 캐시 무시                                 | CPU 캐시 사용 가능                            |
| 메모리 가시성       | 모든 스레드에 즉시 반영됨                      | 락 또는 원자 연산으로 가시성 보장             |
| 명령어 재배치 방지   | 메모리 배리어로 재배치 방지                    | 락 또는 내부 배리어로 재배치 방지             |
| 원자성 보장         | ❌ 복합 연산은 보장하지 않음                   | ✅ 복합 연산도 안전하게 처리됨                |
| 성능                | 가볍고 빠름 (단순 변수에 적합)                 | 상대적으로 느림 (복잡한 연산에 적합)          |


## ⚠️ 주의 사항
- volatile은 가시성만 보장하고, 원자성은 보장하지 않음
- 예: count++ 같은 복합 연산에는 volatile만으로는 안전하지 않음
- 반드시 꼭 필요한 곳에만 사용해야 성능 저하를 피할 수 있음

## 🎯 언제 사용해야 할까?
- 단순한 상태 플래그 공유 시 (예: runFlag, isRunning)
- 읽기/쓰기만 있는 단일 변수를 여러 스레드가 공유할 때
- 복잡한 연산이나 조건부 로직이 포함된 경우에는 synchronized 또는 AtomicInteger 사용 권장

---
  
