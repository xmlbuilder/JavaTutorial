# 자바 멀티스레드 연습 문제 
자바 멀티스레드 문제 1~4에 대한 문제 설명과 코드 풀이를 함께 정리한 해설 자료입니다.  
각 문제는 스레드 구현 방식이 다르며, 실행 결과를 통해 동작을 확인할 수 있습니다.

## 🧵 문제 1: Thread 클래스 상속
### ✅ 요구사항
- Thread 클래스를 상속한 CounterThread 클래스 생성
- 1초 간격으로 1~5 출력
- log() 함수 사용
- main()에서 스레드 실행
### 💡 핵심 코드
```java
static class CounterThread extends Thread {
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            log("value: " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

### 🧪 실행 코드
```java
public static void main(String[] args) {
    CounterThread thread = new CounterThread();
    thread.start();
}
```

### 📌 포인트
- `Thread` 를 직접 상속하면 `run()` 메서드만 오버라이드하면 됨
- `start()` 를 호출해야 새로운 스레드로 실행됨

## 🧵 문제 2: Runnable 인터페이스 구현
### ✅ 요구사항
- CounterRunnable 클래스는 Runnable 구현
- 1초 간격으로 1~5 출력
- Thread 생성 시 이름은 "counter"
### 💡 핵심 코드
```java
static class CounterRunnable implements Runnable {
    public void run() {
        for (int i = 1; i <= 5; i++) {
            log("value: " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 🧪 실행 코드
```java
public static void main(String[] args) {
    Thread thread = new Thread(new CounterRunnable(), "counter");
    thread.start();
}
```

### 📌 포인트
- Runnable은 다중 상속이 필요한 경우 유리함
- Thread 생성 시 이름 지정 가능

##🧵 문제 3: 익명 클래스 방식
#### ✅ 요구사항
- Runnable을 익명 클래스로 구현
- "counter"라는 이름의 스레드 생성
### 💡 핵심 코드
```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            log("value: " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
};
```

### 🧪 실행 코드
```java
Thread thread = new Thread(runnable, "counter");
thread.start();
```

### 📌 포인트
- 익명 클래스를 사용하면 코드가 간결해짐
- 람다 표현식으로도 대체 가능 (Java 8 이상)

## 🧵 문제 4: 여러 스레드 동시 실행
### ✅ 요구사항
- Thread-A: 1초마다 "A" 출력
- Thread-B: 0.5초마다 "B" 출력
- 무한 반복
### 💡 핵심 코드
```java
static class PrintWork implements Runnable {
    private String content;
    private int sleepMs;

    public PrintWork(String content, int sleepMs) {
        this.content = content;
        this.sleepMs = sleepMs;
    }

    @Override
    public void run() {
        while (true) {
            log(content);
            try {
                Thread.sleep(sleepMs);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

### 🧪 실행 코드
```java
public static void main(String[] args) {
    Thread threadA = new Thread(new PrintWork("A", 1000), "Thread-A");
    Thread threadB = new Thread(new PrintWork("B", 500), "Thread-B");
    threadA.start();
    threadB.start();
}
```

### 📌 포인트
- while(true)로 무한 실행
- 서로 다른 sleep 간격으로 병렬 출력
- 스레드 이름을 통해 로그 구분 가능

## ✅ 전체 요약: 자바 스레드 구현 방식 비교


| 문제   | 구현 방식       | 스레드 이름 | 반복 횟수 | 출력 간격     | 특징                      |
|--------|----------------|-------------|-----------|----------------|---------------------------|
| 문제 1 | Thread 상속     | Thread-0    | 5         | 1초            | 기본적인 스레드 상속 방식 |
| 문제 2 | Runnable 구현   | counter     | 5         | 1초            | 유연한 구조, 재사용 가능  |
| 문제 3 | 익명 Runnable   | counter     | 5         | 1초            | 코드 간결화, 람다 가능     |
| 문제 4 | 다중 스레드     | Thread-A/B  | 무한      | 1초 / 0.5초    | 병렬 실행, 실시간 출력     |

---

