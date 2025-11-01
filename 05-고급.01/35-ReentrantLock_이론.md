# 🔐 ReentrantLock 이론 정리
## 1️⃣ 등장 배경
- 자바 1.0부터 제공된 synchronized는 간단하고 편리하지만 다음과 같은 한계가 있음:
- 무한 대기: 락이 풀릴 때까지 BLOCKED 상태로 대기
- 인터럽트 불가: 대기 중인 스레드를 중단할 수 없음
- 공정성 부족: 어떤 스레드가 락을 얻을지 예측 불가 → 기아(starvation) 가능  
    → 이를 해결하기 위해 자바 1.5부터 Lock 인터페이스와 ReentrantLock 구현체 도입

## 2️⃣ Lock 인터페이스 주요 메서드

| 메서드                  | 설명 또는 반환값                                |
|-------------------------|--------------------------------------------------|
| lock()                  | 락을 획득. 인터럽트에 응하지 않음                |
| lockInterruptibly()     | 인터럽트 가능. 인터럽트 시 InterruptedException 발생 |
| tryLock()               | 즉시 락 획득 시도. 실패 시 false 반환             |
| tryLock(time, unit)     | 시간 제한 락 시도. 실패 시 false, 인터럽트 가능   |
| unlock()                | 락 해제. 락을 가진 스레드만 호출 가능             |
| newCondition()          | Condition 객체 생성. wait/notify 기능 제공        |

## 3️⃣ ReentrantLock의 특징
- Lock 인터페이스의 대표 구현체
- 재진입 가능: 동일 스레드가 여러 번 락을 획득 가능
- 공정성 모드 지원: 락 획득 순서를 제어할 수 있음

### 🔁 공정성 모드 비교

| 모드 구분       | 특징                                                   |
|----------------|--------------------------------------------------------|
| 비공정 모드     | 성능 우선, 락을 빨리 획득 가능, 기아 현상 발생 가능성 있음 |
| 공정 모드       | `new ReentrantLock(true)` 사용, 요청 순서대로 락 획득, 성능 저하 가능 |

## 4️⃣ 예시 코드
```java
Lock lock = new ReentrantLock(); // 비공정
Lock fairLock = new ReentrantLock(true); // 공정

lock.lock();
try {
    // 임계 영역
} finally {
    lock.unlock();
}
```
## 5️⃣ lock() vs lockInterruptibly()

| 항목               | lock()                                      | lockInterruptibly()                              |
|--------------------|----------------------------------------------|--------------------------------------------------|
| 인터럽트 대응       | ❌ 무시함                                     | ✅ 인터럽트 발생 시 `InterruptedException` 발생   |
| 대기 방식           | 무한 대기                                     | 대기 중 인터럽트 가능                            |
| 예외 처리           | 없음                                          | 예외 처리 필요 (`try-catch`)                     |
| 사용 목적           | 단순 락 획득                                   | 외부 취소 가능성이 있는 락 획득 상황에 적합       |
| 예시 상황           | 끝까지 기다리는 맛집 줄                       | 친구가 연락하면 포기하는 맛집 줄                  |


## 🔍 추가 개념: Condition 객체
- Condition은 wait/notify를 ReentrantLock과 함께 사용할 수 있게 해주는 도구
- 예를 들어, 특정 조건이 만족될 때까지 스레드를 대기시키고, 조건이 만족되면 깨울 수 있음
```java
Condition condition = lock.newCondition();

lock.lock();
try {
    condition.await(); // 대기
    // 조건 만족 후 실행
} finally {
    lock.unlock();
}
```
- await() → WAITING 상태 진입
- signal() 또는 signalAll() → 대기 중인 스레드 깨움

## 📌 ReentrantLock vs synchronized 요약 비교

| 항목               | synchronized                          | ReentrantLock                                 |
|--------------------|----------------------------------------|-----------------------------------------------|
| 대기 상태           | BLOCKED                                | WAITING / TIMED_WAITING                        |
| 인터럽트 대응       | 불가능                                 | 가능 (`lockInterruptibly()`)                  |
| 시간 제한 대기       | 불가능                                 | 가능 (`tryLock(time, unit)`)                  |
| 공정성 설정         | 불가능                                 | 가능 (`new ReentrantLock(true)`)              |
| 조건 대기/신호      | `wait()` / `notify()` / `notifyAll()` | `Condition.await()` / `signal()` / `signalAll()` |
| 재진입 가능 여부     | 가능                                   | 가능                                           |
| 제어 수준           | 고수준 (간단한 사용)                   | 저수준 (세밀한 제어 가능)                      |

## ✅ 결론
- ReentrantLock은 synchronized의 단점을 보완한 고급 락 도구
- 공정성, 인터럽트, 시간 제한, 조건 대기 등 다양한 기능을 제공
- 복잡한 동시성 제어가 필요한 상황에서는 ReentrantLock이 훨씬 유리함

---
