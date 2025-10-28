# 🧠 자바 스레드 정리
## ✅ 1. 사용자 스레드 vs 데몬 스레드

| 항목             | 사용자 스레드 (User Thread)                      | 데몬 스레드 (Daemon Thread)                        |
|------------------|--------------------------------------------------|----------------------------------------------------|
| 역할             | 주요 작업 수행                                    | 백그라운드 보조 작업 수행                          |
| 종료 조건        | 모든 사용자 스레드가 종료되면 JVM 종료            | 사용자 스레드가 모두 종료되면 자동 종료            |
| JVM 종료 시점    | 사용자 스레드 종료까지 기다림                     | 데몬 스레드 실행 중이라도 JVM은 바로 종료됨        |
| 실행 예시        | 사용자 요청 처리, 메인 로직                       | 로그 기록, 캐시 정리, 메모리 청소 등               |
| 설정 방법        | 기본값: `false`                                   | `setDaemon(true)` 호출 (단, `start()` 전에 설정)   |

-💡 데몬 스레드는 setDaemon(true)로 설정하며, 반드시 start() 호출 전에 지정해야 함

## ✅ Java 데몬 스레드 샘플 코드
```java
public class DaemonExample {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName() + ": main() start");

        Thread daemonThread = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + ": run() start");
            try {
                while (true) {
                    System.out.println(Thread.currentThread().getName() + ": working...");
                    Thread.sleep(1000); // 1초마다 작업 수행
                }
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + ": interrupted");
            }
        });

        daemonThread.setDaemon(true); // 데몬 스레드로 설정
        daemonThread.start();

        System.out.println(Thread.currentThread().getName() + ": main() end");
    }
}
```

- 💡 setDaemon(true)는 반드시 start() 호출 전에 설정해야 하며,  
  main() 스레드가 종료되면 데몬 스레드도 자동 종료됩니다.


## ✅ 2. 실행 결과 비교
```
setDaemon(true) 설정 시
main: main() start  
main: main() end  
Thread-0: run() start  
```

- main 스레드가 종료되면 JVM도 종료됨 → run() end 출력되지 않음
```
main: main() start  
Thread-0: run() start  
Thread-0: working...  
main: main() end  
Thread-0: working...  
Thread-0: working...  
(이후 JVM이 종료되며 데몬 스레드도 함께 종료됨)
```
- Thread-0가 사용자 스레드로 설정되어 JVM이 종료되지 않음 → run() end까지 출력됨


### 🧠 핵심 개념 요약: Java 스레드와 JVM 종료
| 구분           | 설명                                                                 |
|----------------|----------------------------------------------------------------------|
| 사용자 스레드   | 일반적인 작업용 스레드. 살아 있는 동안 JVM은 종료되지 않음             |
| 데몬 스레드     | 백그라운드용 스레드. 사용자 스레드가 모두 종료되면 JVM이 자동 종료됨   |
| JVM 종료 조건  | 모든 사용자 스레드가 종료되면 데몬 스레드가 남아 있어도 JVM은 종료됨   |

### ✅ 동작 흐름 예시
```java
public class Main {
    public static void main(String[] args) {
        Thread daemon = new Thread(() -> {
            while (true) {
                System.out.println("데몬 스레드 실행 중...");
                try { Thread.sleep(1000); } catch (InterruptedException e) {}
            }
        });
        daemon.setDaemon(true); // 데몬 스레드로 설정
        daemon.start();

        System.out.println("메인 종료");
    }
}
```
- 위 코드에서 main()이 끝나면 사용자 스레드는 모두 종료됨
- 데몬 스레드는 남아 있어도 JVM은 강제 종료됨

### ✨ 요약: Java 스레드 종류와 JVM 종료 조건

| 스레드 종류     | JVM 종료 조건                                       |
|----------------|------------------------------------------------------|
| 사용자 스레드   | 하나라도 살아 있으면 JVM은 종료되지 않음              |
| 데몬 스레드     | 사용자 스레드가 모두 종료되면 JVM이 자동으로 종료됨   |

- 즉, 데몬 스레드는 JVM이 종료될 때 자동으로 정리되는 백그라운드 작업자라고 보면 됨.

## ✅ 3. 스레드 생성 방식 비교

| 항목               | Thread 클래스 상속 방식             | Runnable 인터페이스 구현 방식         |
|--------------------|-------------------------------------|----------------------------------------|
| 구현 방식          | `Thread` 클래스 상속 후 `run()` 재정의 | `Runnable` 구현 후 `Thread`에 전달     |
| 코드 예시          | `class MyThread extends Thread`     | `class MyRunnable implements Runnable` |
| 실행 객체          | `MyThread` 자체가 스레드 객체        | `Runnable` 객체를 `Thread`에 전달      |
| 상속 제한          | 단일 상속만 가능                     | 다른 클래스 상속 가능                  |
| 코드 분리          | 스레드와 작업이 결합됨               | 스레드와 작업이 분리됨                 |
| 자원 공유          | 불가능                               | 여러 스레드가 하나의 `Runnable` 공유 가능 |
| 유연성             | 낮음                                 | 높음                                   |
| 실무 추천 방식     | 간단한 테스트나 예제에 적합          | 실무에서는 `Runnable` 방식 권장         |

- ✅ 실무에서는 Runnable 방식이 더 유연하고 유지보수에 유리하므로 권장됨


## ✅ 실행 흐름 예시 (`Runnable` 방식)
```java
public class HelloRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + ": run()");
    }
}
```
```java
HelloRunnable runnable = new HelloRunnable();
Thread thread = new Thread(runnable);
thread.start();
```
- `Runnable` 객체는 실행할 작업을 정의
- `Thread` 객체는 실행 환경을 제공
- 둘을 분리함으로써 코드의 유연성과 재사용성이 높아짐

---
