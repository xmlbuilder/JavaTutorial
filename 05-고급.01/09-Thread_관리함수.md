
# 🧵 Thread 관리 메서드 요약표

| 메서드                      | 설명                                                         | 예제 코드 요약                                 |
|----------------------------|--------------------------------------------------------------|------------------------------------------------|
| `getName()`                | 스레드 이름 반환                                              | `thread.getName()`                            |
| `setName(String)`         | 스레드 이름 설정                                              | `thread.setName("worker")`                    |
| `getId()` / `threadId()`   | 스레드 고유 ID 반환                                           | `thread.getId()`                              |
| `getPriority()`            | 스레드 우선순위 반환 (1~10)                                   | `thread.getPriority()`                        |
| `setPriority(int)`         | 스레드 우선순위 설정                                          | `thread.setPriority(7)`                       |
| `getState()`               | 현재 스레드 상태 반환 (`NEW`, `RUNNABLE`, 등)                 | `thread.getState()`                           |
| `isAlive()`                | 스레드가 살아있는지 여부 확인                                | `thread.isAlive()`                            |
| `isDaemon()`               | 데몬 스레드 여부 확인                                         | `thread.isDaemon()`                           |
| `setDaemon(boolean)`       | 데몬 스레드로 설정 (시작 전에만 가능)                         | `thread.setDaemon(true)`                      |
| `getThreadGroup()`         | 스레드가 속한 그룹 반환                                       | `thread.getThreadGroup()`                     |
| `interrupt()`              | 스레드에 인터럽트 요청                                        | `thread.interrupt()`                          |
| `isInterrupted()`          | 인터럽트 상태 확인                                            | `thread.isInterrupted()`                      |
| `join()`                   | 해당 스레드가 종료될 때까지 현재 스레드 대기                  | `thread.join()`                               |
| `sleep(ms)`                | 현재 스레드를 지정 시간 동안 일시 정지                        | `Thread.sleep(1000)`                          |
| `currentThread()`          | 현재 실행 중인 스레드 객체 반환                               | `Thread.currentThread()`                      |


## 📌 샘플 예제: 주요 메서드 사용
### 1. 스레드 이름 설정 및 조회
```java
Thread thread = new Thread(() -> log("작업 중"), "worker");
log("스레드 이름: " + thread.getName());
```

### 2. 우선순위 설정
```java
thread.setPriority(Thread.MAX_PRIORITY); // 10
log("우선순위: " + thread.getPriority());
```

### 3. 스레드 상태 확인
```java
log("상태: " + thread.getState()); // NEW
thread.start();
log("상태: " + thread.getState()); // RUNNABLE 또는 TERMINATED
```

### 4. 데몬 스레드 설정
```java
Thread daemonThread = new Thread(() -> log("데몬 실행"));
daemonThread.setDaemon(true);
log("데몬 여부: " + daemonThread.isDaemon());
```

### 5. 인터럽트 처리
```java
Thread interruptThread = new Thread(() -> {
    while (!Thread.currentThread().isInterrupted()) {
        log("작업 중...");
    }
    log("인터럽트 감지됨");
});
interruptThread.start();
Thread.sleep(1000);
interruptThread.interrupt();
```

### 6. join() 사용
```java
Thread t1 = new Thread(() -> {
    log("작업 시작");
    try { Thread.sleep(1000); } catch (InterruptedException e) {}
    log("작업 종료");
});
t1.start();
t1.join(); // t1이 끝날 때까지 대기
log("모든 작업 완료");
```


## ✅ 정리
- Thread 클래스는 단순 실행뿐 아니라 상태 추적, 우선순위 조정, 인터럽트 처리, 데몬 설정 등 다양한 관리 기능을 제공
- 스레드의 상태와 속성을 확인하면 디버깅, 로깅, 병렬 처리 제어에 매우 유용

---
