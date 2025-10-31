# yield 실전
이전 예제를 기반으로 CPU 자원 효율을 높이기 위해 `yield()` 를 도입한 개선 버전입니다.  
아래에 코드를 단계별로 설명하고, 왜 yield()가 적절한 위치에 추가되었는지를 함께 정리.

## 전체 소스
```java
public class MyPrinterV4 {
    public static void main(String[] args) throws InterruptedException {

        Printer printer = new Printer();
        Thread printerThread = new Thread(printer, "printer");
        printerThread.start();
        Scanner userInput = new Scanner(System.in);

        while (true) {
            System.out.println("프린터할 문서를 입력하세요. 종료 (q): ");
            String input = userInput.nextLine();

            if (input.equals("q")) {
                printerThread.interrupt();
                break;
            }
            printer.addJob(input);
        }
    }

    static class Printer implements Runnable {
    Queue<String> jobQueue = new ConcurrentLinkedQueue<>();

        @Override
        public void run() {
            while (!Thread.interrupted()) {

                if (jobQueue.isEmpty()) {

                    Thread.yield(); //추가

                    continue;
                }

                try {

                    String job = jobQueue.poll();
                    log("출력 시작: " + job + ", 대기 문서: " + jobQueue);
                    Thread.sleep(3000); //출력에 걸리는 시간
                    log("출력 완료: " + job);

                } catch (InterruptedException e) {

                    log("인터럽트!");
                    break;
                }
            }
            log("프린터 종료");
        }

        public void addJob(String input) {
            jobQueue.offer(input);
        }
    }
}
```

## 🧩 전체 구조 요약
- main 스레드: 사용자 입력을 받아 프린터에 전달
- printer 스레드: 입력된 작업을 출력
- 개선 포인트: jobQueue가 비어 있을 때 Thread.yield()를 호출하여 CPU 양보

## 🔄 단계별 코드 설명
### 1️⃣ 프린터 인스턴스 생성 및 스레드 시작
```java
Printer printer = new Printer();
Thread printerThread = new Thread(printer, "printer");
printerThread.start();
```

- Printer 객체를 생성하고 printerThread로 실행
- run() 메서드가 백그라운드에서 실행됨

### 2️⃣ 사용자 입력 처리 (main 스레드)
```java
Scanner userInput = new Scanner(System.in);
while (true) {
    System.out.println("프린터할 문서를 입력하세요. 종료 (q): ");
    String input = userInput.nextLine();
    if (input.equals("q")) {
        printerThread.interrupt(); // 종료 지시
        break;
    }
    printer.addJob(input); // 작업 추가
}
```

- 사용자 입력을 받아 jobQueue에 추가
- "q" 입력 시 interrupt() 호출로 프린터 스레드 종료 유도

### 3️⃣ 프린터 스레드의 작업 처리 ( `Thread.yield()` )
```java
while (!Thread.interrupted()) {
    if (jobQueue.isEmpty()) {
        Thread.yield(); // CPU 양보
        continue;
    }
}
```

- 핵심 개선 포인트: `jobQueue` 가 `비어 있을 경우` `yield()` 호출
- `yield()` 는 현재 스레드가 자발적으로 CPU를 양보하여 다른 스레드가 실행될 수 있도록 함
- 무한 루프에서 CPU를 낭비하지 않고, 다른 스레드에게 실행 기회를 넘김

### 4️⃣ 출력 작업 수행
```java
try {
    String job = jobQueue.poll();
    log("출력 시작: " + job + ", 대기 문서: " + jobQueue);
    Thread.sleep(3000); // 출력 시간
    log("출력 완료: " + job);
} catch (InterruptedException e) {
    log("인터럽트!");
    break;
}
```

- 작업이 있으면 꺼내서 3초간 출력
- sleep() 중 인터럽트가 발생하면 InterruptedException으로 즉시 종료

### 5️⃣ 종료 처리
```java
log("프린터 종료");
```

- 반복문을 빠져나오면 종료 메시지 출력

## ⚡ 왜 yield()가 필요한가?

| 상황 또는 조건         | 기존 방식 (continue)         | 개선 방식 (yield())             |
|------------------------|------------------------------|----------------------------------|
| jobQueue가 비어 있음   | 무한 루프 반복 → CPU 낭비     | CPU 양보 → 다른 스레드 실행 기회 제공 |
| 작업 대기 중           | 쉴 틈 없이 상태 체크 반복     | 스케줄러에 양보 힌트 전달         |
| 시스템 전체 효율       | 낮음 (불필요한 점유)          | 높음 (자원 분배 최적화)           |
``


## ✅ 결론
MyPrinterV4는 `Thread.yield()` 를 통해 불필요한 CPU 점유를 줄이고,  
다른 스레드에게 실행 기회를 양보함으로써 전체 시스템의 효율을 높인 구조입니다.  
특히 jobQueue가 비어 있는 동안 무한 루프를 돌며 CPU를 낭비하는 문제를 효과적으로 해결합니다.  


---

