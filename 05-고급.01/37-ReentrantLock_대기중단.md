# ReentrantLock 대기 중단
아래는 ReentrantLock의 대기 중단 기능을 중심으로 단계별로 체계적으로 정리한 내용입니다.  
예제 흐름과 스레드 상태 변화까지 함께 설명.

## 소스 코드
```java
public class BankAccountV5 implements BankAccount {
`    private int balance;
    private final Lock lock = new ReentrantLock();

    public BankAccountV5(int initialBalance) {
        this.balance = initialBalance;
    }

    @Override
    public boolean withdraw(int amount) {
        log("거래 시작: " + getClass().getSimpleName());
        
        if (!lock.tryLock()) {
            log("[진입 실패] 이미 처리중인 작업이 있습니다.");
            return false;
        }
        try {
            log("[검증 시작] 출금액: " + amount + ", 잔액: " + balance);

            if (balance < amount) {
                log("[검증 실패] 출금액: " + amount + ", 잔액: " + balance);
                return false;
            }

            sleep(1000);

            balance = balance - amount;
            log("[출금 완료] 출금액: " + amount + ", 변경 잔액: " + balance);
        } finally {
            lock.unlock(); // ReentrantLock 이용하여 lock 해제
        }
        log("거래 종료");
        return true;
    }

    @Override
    public int getBalance() {
        lock.lock(); // ReentrantLock 이용하여 lock 걸기
        try {
            return balance;
        } finally {
            lock.unlock(); // ReentrantLock 이용하여 lock 해제
        }
    }
}
```

## 🔐 ReentrantLock – 대기 중단 기능 단계별 정리
### 1️⃣ 배경: 왜 대기 중단이 필요한가?
- `synchronized` 는 락을 얻을 때까지 무한 대기하며, 인터럽트 불가
- `ReentrantLock` 은 더 유연하게:
    - 즉시 포기 (tryLock())
    - 시간 제한 대기 (tryLock(time))

### 2️⃣ 즉시 포기: tryLock()
- ✅ 특징
    - 락을 즉시 시도하고, 실패하면 false 반환
    - 대기하지 않음, 스레드 상태는 RUNNABLE 유지
#### 💡 예시 흐름
```java
if (!lock.tryLock()) {
    log("[진입 실패] 이미 처리중인 작업이 있습니다.");
    return false;
}
```

### 🧵 실행 흐름

| 스레드 | 동작 설명                                      |
|--------|------------------------------------------------|
| t1     | 락 획득 후 임계 영역 실행                       |
| t2     | `tryLock()` 실패 → 즉시 포기 (`false` 반환)     |
| t2     | 메서드 종료 → `TERMINATED` 상태 진입            |
| t1     | 임계 영역 종료 후 `unlock()` 호출               |


### 3️⃣ 시간 제한 대기: tryLock(time, unit)
- ✅ 특징
    - 지정된 시간 동안 락을 시도
    - 시간 내 실패 시 false 반환
    - 대기 중 인터럽트 가능
    - 스레드 상태: TIMED_WAITING → RUNNABLE

#### 💡 예시 흐름
```java
if (!lock.tryLock(500, TimeUnit.MILLISECONDS)) {
    log("[진입 실패] 이미 처리중인 작업이 있습니다.");
    return false;
}
```

#### 🧵 실행 흐름

| 스레드 | 동작 설명                                      |
|--------|------------------------------------------------|
| t1     | 락 획득 후 임계 영역 실행                       |
| t2     | `tryLock(time)` 호출 → `TIMED_WAITING` 상태 진입 |
| t2     | 시간 초과 → 락 획득 실패 → `false` 반환          |
| t1     | 임계 영역 종료 후 `unlock()` 호출               |

### 4️⃣ 내부 동작 요약

| 동작 또는 메서드             | 설명 또는 결과                                |
|------------------------------|-----------------------------------------------|
| `tryLock()`                    | 락 획득 실패 시 즉시 false 반환                |
| `tryLock(time, unit)`          | 내부적으로 `LockSupport.parkNanos()` 호출      |
| 상태 전이                    | `TIMED_WAITING` → 시간 초과 후 `RUNNABLE` 복귀 |
| 예외 안전성                  | `finally` 블록에서 `unlock()` 반드시 호출      |

### 5️⃣ 정리: ReentrantLock의 유연한 제어

| 항목                     | 설명 또는 관련 기능                                      |
|--------------------------|----------------------------------------------------------|
| 대기 중단                | `tryLock()`, `tryLock(time)`으로 무한 대기 방지 가능       |
| 시간 제한 대기           | `tryLock(time)` 사용 시 지정 시간만큼 대기 후 포기 가능     |
| 상태 전이                | `RUNNABLE` ↔ `TIMED_WAITING` ↔ `TERMINATED` 명확히 관리    |
| 예외 안전성              | `finally` 블록에서 `unlock()` 호출로 락 반납 보장          |
| 공정성 설정              | `new ReentrantLock(true)`로 대기 순서대로 락 획득 가능      |


## 🧠 추가 팁
- tryLock()은 낮은 지연과 빠른 응답이 필요한 시스템에 적합
- tryLock(time)은 제한된 시간 동안만 자원 접근을 시도할 때 유용
- ReentrantLock은 synchronized보다 더 세밀한 동시성 제어를 가능하게 함

---

