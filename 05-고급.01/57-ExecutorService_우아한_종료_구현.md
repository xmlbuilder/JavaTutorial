# ExecutorService 우아한 종료 - 핵심 요약
## ✅ 왜 우아한 종료가 필요한가?
- 서버 재시작 시 진행 중인 작업이 중단되면 고객 주문이 손실될 수 있음
- 이상적인 종료 방식:
    - 새로운 작업은 거절
    - 진행 중인 작업은 모두 완료
    - 이후 자원 정리 후 종료

## 🔧 종료 방식 비교

| 메서드             | 설명                                                             |
|--------------------|------------------------------------------------------------------|
| shutdown()         | 새로운 작업은 거절하고, 이미 제출된 작업은 모두 완료 후 종료     |
| shutdownNow()      | 실행 중인 작업에 인터럽트 발생, 대기 중 작업은 제거 후 반환       |
| awaitTermination() | 지정된 시간 동안 모든 작업 완료를 기다림 (블로킹 메서드)         |
| close()            | shutdown() 호출 후 일정 시간 지나면 shutdownNow() 자동 호출      |


## 🛠️ 구현 예제: shutdownAndAwaitTermination
```java
static void shutdownAndAwaitTermination(ExecutorService es) {
    es.shutdown(); // 우아한 종료 시도
    try {
        log("서비스 정상 종료 시도");
        if (!es.awaitTermination(10, TimeUnit.SECONDS)) {
            log("서비스 정상 종료 실패 -> 강제 종료 시도");
            es.shutdownNow(); // 강제 종료
            if (!es.awaitTermination(10, TimeUnit.SECONDS)) {
                log("서비스가 종료되지 않았습니다.");
            }
        }
    } catch (InterruptedException ex) {
        es.shutdownNow(); // 인터럽트 발생 시 강제 종료
    }
}
```
- 1단계: shutdown() 호출 → 10초간 작업 완료 대기
- 2단계: 완료되지 않으면 shutdownNow() 호출 → 인터럽트 발생
- 3단계: 다시 10초간 대기 → 그래도 종료되지 않으면 로그로 알림

## 📊 실행 흐름 예시

| 작업 이름   | 실행 시작 | 실행 완료 | 종료 방식         |
|-------------|------------|------------|--------------------|
| taskA       | O          | O          | 정상 종료 (shutdown) |
| taskB       | O          | O          | 정상 종료 (shutdown) |
| taskC       | O          | O          | 정상 종료 (shutdown) |
| longTask    | O          | ✖          | 강제 종료 (shutdownNow) |

## 🔍 설명
- taskA, taskB, taskC는 1초짜리 작업으로 shutdown() 호출 후 10초 내에 정상적으로 완료됨
- longTask는 100초짜리 작업으로 10초 내에 완료되지 않아 shutdownNow() 호출로 인터럽트 발생 → 강제 종료됨
- longTask는 10초 내에 끝나지 않아 강제 종료됨
- 인터럽트 발생 시 InterruptedException으로 예외 처리됨

## ⚠️ 주의 사항
- shutdown()은 논블로킹 → 호출 후 바로 다음 코드 실행됨
- awaitTermination()은 블로킹 → 지정 시간까지 대기
- shutdownNow() 후에도 작업이 즉시 종료되지 않을 수 있음
- 예: 무한 루프, 인터럽트 무시 코드
```
while (true) { /* 인터럽트 무시 */ }
```

- 이런 경우를 대비해 강제 종료 후에도 대기 시간 설정 필요

## ✅ 마무리 정리
- 우아한 종료 → 강제 종료 순으로 설계하는 것이 안전
- `shutdownAndAwaitTermination()` 은 공식 API 문서에서도 권장
- 종료 실패 시 로그를 남겨 문제를 추적할 수 있도록 설계

---
