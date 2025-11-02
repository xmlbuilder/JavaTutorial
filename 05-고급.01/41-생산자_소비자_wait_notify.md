# wait / notify

## 🧠 문제 배경
기존 BoundedQueueV2는 sleep()을 사용하여 큐가 가득 찼거나 비었을 때 스레드가 대기하도록 구현됨.  
하지만 sleep()은 락을 해제하지 않기 때문에, 다른 스레드가 임계 영역에 진입할 수 없어 **교착 상태(deadlock-like stall)** 가 발생함.

## 🔧 해결 방법: Object.wait() / Object.notify()

| 메서드        | 동작 설명                                      | 특징 요약                         |
|---------------|------------------------------------------------|-----------------------------------|
| wait()        | 현재 스레드가 락을 반납하고 대기 상태로 전환     | 락 해제 + 대기, 다른 스레드 진입 가능 |
| notify()      | 대기 중인 스레드 중 하나를 깨움                 | 하나만 깨움, 선택적 협력             |
| notifyAll()   | 대기 중인 모든 스레드를 깨움                    | 전체 깨움, 브로드캐스트 방식         |

## 🧠 핵심 요점
- wait()은 락을 해제하면서 대기하므로 교착 상태를 방지할 수 있음
- notify()는 하나의 스레드만 깨우기 때문에 경쟁 상황에서 신중하게 사용해야 함
- notifyAll()은 모든 대기 스레드를 깨우므로 협력이 필요한 경우에 적합
- 이 메서드들은 반드시 synchronized 블록 안에서 호출되어야 하며, 락을 소유한 상태에서만 사용할 수 있음.


## ✅ 개선된 코드: BoundedQueueV3

```java
public class BoundedQueueV3 implements BoundedQueue {
    private final Queue<String> queue = new ArrayDeque<>();
    private final int max;
    
    public BoundedQueueV3(int max) {
        this.max = max;
    }

    public synchronized void put(String data) {
        while (queue.size() == max) {
            log("[put] 큐가 가득 참, 생산자 대기");
            try {
                wait(); // RUNNABLE -> WAITING, 락 반납
                log("[put] 생산자 깨어남");
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        queue.offer(data);
        log("[put] 생산자 데이터 저장, notify() 호출");
        notify(); // 대기 스레드, WAIT -> BLOCKED
        //notifyAll(); // 모든 대기 스레드, WAIT -> BLOCKED
    }

    public synchronized String take() {
        while (queue.isEmpty()) {
            log("[take] 큐에 데이터가 없음, 소비자 대기");
            try {
                wait();
                log("[take] 소비자 깨어남");
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        String data = queue.poll();
        log("[take] 소비자 데이터 획득, notify() 호출");
        notify(); // 대기 스레드, WAIT -> BLOCKED
        //notifyAll(); // 모든 대기 스레드, WAIT -> BLOCKED
        return data;
    }

    @Override
    public String toString() {
        return queue.toString();
    }
}
```

- put()과 take() 모두 synchronized 블록 내에서 wait()과 notify()를 사용
- 큐가 가득 찼거나 비었을 때, 해당 스레드는 wait()으로 대기하며 락을 반납
- 큐 상태가 바뀌면 notify()로 대기 중인 스레드를 깨움

## 🔁 실행 흐름 요약: 생산자 먼저 실행

| 스레드 그룹 | 동작 내용                         | 상태 변화 또는 이벤트           |
|-------------|----------------------------------|----------------------------------|
| p1, p2      | data1, data2 생산 후 저장         | 큐 채움, 정상 종료               |
| p3          | 큐 full → wait() 호출             | WAITING 상태로 대기              |
| c1          | data1 소비 → notify()로 p3 깨움   | p3 깨어남, c1 정상 종료          |
| p3          | data3 저장                        | 큐에 추가, 정상 종료             |
| c2, c3      | 나머지 데이터 소비                | 모두 정상 종료                   |

## 🧠 핵심 포인트
- p3는 큐가 가득 차면 wait()으로 대기 → 락을 반납하고 협력 가능
- c1이 데이터를 소비하면 notify()로 p3를 깨움 → 스레드 간 협력 발생
- 모든 스레드가 BLOCKED 없이 정상 종료

## 🔁 실행 흐름 요약: 소비자 먼저 실행

| 스레드 그룹     | 동작 내용                          | 상태 변화 또는 이벤트           |
|------------------|-----------------------------------|----------------------------------|
| c1, c2, c3       | 큐 비어 있음 → wait() 대기         | 모두 WAITING 상태                |
| p1               | data1 저장 → notify()로 c1 깨움    | c1 깨어남                        |
| c1               | data1 소비 → notify()로 p2 깨움    | p2 깨어남                        |
| p2               | data2 저장 → notify()로 c3 깨움    | c3 깨어남                        |
| c3               | data2 소비 → notify()로 p3 깨움    | p3 깨어남                        |
| p3               | data3 저장 → notify()로 c2 깨움    | c2 깨어남                        |
| c2               | data3 소비                         | 모든 스레드 정상 종료            |

## 🧠 핵심 포인트
- 초기에는 소비자 스레드들이 모두 wait()로 대기
- 생산자가 데이터를 넣을 때마다 notify()로 소비자를 깨움
- 소비자가 데이터를 소비할 때마다 다시 notify()로 생산자를 깨움
- 스레드 간 협력이 자연스럽게 이어지며 교착 없이 종료
- ✅ 모든 스레드 정상 종료

## 📊 개선 효과 요약

| 항목               | sleep() 방식                         | wait()/notify() 방식                     |
|--------------------|--------------------------------------|------------------------------------------|
| 락 해제 여부       | ❌ 해제 안 함                        | ✅ 해제함                                 |
| 스레드 협력 가능성 | ❌ 낮음                              | ✅ 높음                                   |
| 교착 위험          | ✅ 높음                              | ❌ 낮음                                   |
| 자원 활용 효율성   | ❌ 비효율적 (불필요한 반복 대기)     | ✅ 효율적 (필요 시점에만 깨어남)         |
| 스레드 상태 관리   | TIMED_WAITING + BLOCKED 반복         | WAITING → RUNNABLE → TERMINATED 자연스러움 |

## 🧠 핵심 요점
- sleep()은 락을 점유한 채 대기하므로 다른 스레드가 진입 불가
- wait()은 락을 반납하고 대기하므로 스레드 간 협력 가능
- notify()를 통해 필요한 시점에만 스레드를 깨워 불필요한 CPU 낭비 방지

## 🧩 결론
- wait()과 notify()는 자바의 기본 동기화 메커니즘으로, 락을 반납하고 협력적인 대기를 가능하게 함
- BoundedQueueV3는 BoundedQueueV2의 교착 문제를 해결하며, 생산자-소비자 간 원활한 협업을 보장
- 자바의 멀티스레드 환경에서 wait() / notify()는 필수적인 동기화 도구

---

