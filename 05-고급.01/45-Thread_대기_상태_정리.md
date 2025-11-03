# synchronized 대기 상태 정리
아래는 synchronized의 두 가지 대기 상태와 자바의 락/스레드 대기 집합 구조를 정리한 내용입니다.  
흐름을 기반으로 시퀀스 다이어그램도 함께 구성했습니다.

## 🔒 synchronized의 두 가지 대기 상태

| 구분        | 상태     | 설명                         |
|-------------|----------|------------------------------|
| 락 대기     | BLOCKED  | 모니터 락을 얻기 위해 대기     |
| 조건 대기   | WAITING  | `wait()` 호출 후 조건 대기 집합에서 대기 |

## 🧠 핵심 요약
- `BLOCKED`: 다른 스레드가 synchronized 블록을 점유 중일 때, 락을 얻기 위해 락 대기 집합에서 대기
- `WAITING`: wait() 호출 시 스레드 대기 집합으로 이동하며 락을 반납


## 🧠 자바 객체의 내부 구조
자바의 모든 객체는 다음 3가지 요소를 내장하고 있습니다:
- 모니터 락: synchronized 블록 진입을 제어
- 락 대기 집합: 모니터 락을 기다리는 BLOCKED 스레드들
- 스레드 대기 집합: wait() 호출 후 대기 중인 WAITING 스레드들

## 🧵 시퀀스 다이어그램: 소비자 스레드의 대기 흐름
```mermaid
sequenceDiagram
    participant c1
    participant c2
    participant c3
    participant MonitorLock
    participant LockWaitSet
    participant ThreadWaitSet
    participant p1

    Note over c1: c1이 락을 획득하고 임계 영역 진입
    c1->>MonitorLock: acquire lock
    c1->>ThreadWaitSet: wait() 호출 → WAITING 상태
    MonitorLock-->>LockWaitSet: lock released

    Note over c2, c3: c2, c3는 락을 기다리며 BLOCKED 상태
    c2->>LockWaitSet: 대기
    c3->>LockWaitSet: 대기

    Note over p1: p1이 락을 획득하고 데이터 저장
    p1->>MonitorLock: acquire lock
    p1->>ThreadWaitSet: notify() 호출
    ThreadWaitSet-->>c1: c1 깨움

    Note over c1: c1은 다시 락을 얻기 위해 BLOCKED 상태
    c1->>LockWaitSet: 대기
    MonitorLock-->>c1: 락 획득
    c1->>MonitorLock: 임계 영역 수행
```


## 🔁 대기 흐름 요약

| 단계               | 설명                                               |
|--------------------|----------------------------------------------------|
| c1 → wait()        | `c1`이 락을 획득한 후 `wait()` 호출 → WAITING 상태 |
| c2, c3 → BLOCKED   | `c2`, `c3`는 락을 얻지 못해 락 대기 집합에서 BLOCKED |
| p1 → notify(c1)    | `p1`이 락을 획득하고 `notify()` 호출 → `c1` 깨움     |
| c1 → BLOCKED       | `c1`은 다시 락을 얻기 위해 락 대기 집합으로 이동     |
| c1 → RUNNABLE      | 락을 획득한 `c1`이 임계 영역 수행 가능 상태로 전환   |

## 🧠 흐름 요약
- `WAITING` 은 스레드 대기 집합에서의 대기
- `BLOCKED` 는 락 대기 집합에서의 대기
- `RUNNABLE` 은 락을 획득하고 임계 영역을 수행할 수 있는 상태


## 🔐 2중 대기소 개념

| 대기소 구분     | 상태     | 설명                                       |
|----------------|----------|--------------------------------------------|
| 1차 대기소     | BLOCKED  | 모니터 락을 얻기 위해 락 대기 집합에서 대기 |
| 2차 대기소     | WAITING  | `wait()` 호출 후 스레드 대기 집합에서 대기  |

## 🧠 핵심 요약
- `BLOCKED` : 락을 얻지 못한 스레드는 락 대기 집합에서 대기
- `WAITING` : wait() 호출 시 락을 반납하고 스레드 대기 집합에서 대기
- 스레드는 `WAITING` → `BLOCKED` → `RUNNABLE` 순으로 2중 감옥을 탈출해야 임계 영역을 수행할 수 있음

## ✅ 핵심 정리
- synchronized는 모니터 락을 기반으로 동작
- wait()는 스레드 대기 집합으로 이동하며 락을 반납
- notify()는 스레드 대기 집합에서 스레드를 깨움
- 깨운 스레드는 다시 락 대기 집합으로 이동해 락을 기다림
- 락을 획득해야만 임계 영역 수행 가능

---
