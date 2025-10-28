# Thread 연습 문제 2
우선 main thread만을 이용해서 풀이.

## ✅ 문제 풀이 구성 방식

| 구성 요소     | 설명                                                                 |
|---------------|----------------------------------------------------------------------|
| 문제 설명     | 문제의 핵심 요구사항과 조건을 간단히 요약                           |
| 접근 방법     | 어떤 알고리즘이나 전략으로 접근할지 설명                             |
| 핵심 포인트   | 시간 복잡도, 자료구조, 예외 처리 등 주의할 점                        |
| 코드 예제     | 실제 동작하는 코드로 풀이를 구현                                     |
| 출력 결과     | 코드 실행 시 예상되는 출력 또는 결과 설명                            |

## 🧪 예시: 1부터 N까지의 합 구하기
### 🔹 문제 설명
정수 N이 주어졌을 때, 1부터 N까지의 합을 구하시오.

###🔹 접근 방법
- 반복문을 사용하여 1부터 N까지 더한다.
- 또는 수학 공식 sum = N * (N + 1) / 2를 사용할 수 있다.
####🔹 코드 예제
```java
public class SumExample {
    public static void main(String[] args) {
        int N = 100;
        int sum = 0;
        for (int i = 1; i <= N; i++) {
            sum += i;
        }
        System.out.println("1부터 " + N + "까지의 합: " + sum);
    }
}
```

#### 🔹 출력 결과
```
1부터 100까지의 합: 5050
```

##🧪 예시: 배열에서 최대값 찾기
### 🔹 문제 설명
정수 배열이 주어졌을 때, 가장 큰 값을 찾아라.

### 🔹 접근 방법
- 배열을 순회하면서 최대값을 갱신한다.
### 🔹 코드 예제
```java
public class MaxFinder {
    public static void main(String[] args) {
        int[] numbers = {3, 7, 2, 9, 4};
        int max = numbers[0];
        for (int num : numbers) {
            if (num > max) {
                max = num;
            }
        }
        System.out.println("최대값: " + max);
    }
}
```

### 🔹 출력 결과
```
최대값: 9
```

# Thread 이용하기

방금 정리했던 문제 풀이 방식은 일반적인 알고리즘 문제에 적용되는 구조였고,  
스레드를 활용한 문제 풀이에는 별도의 구성과 코드 흐름이 필요합니다.  
아래에 스레드 기반 문제 풀이 구성 방식을 따로 정리.

## ✅ 스레드 기반 문제 풀이 구성 방식

| 구성 요소         | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 문제 설명         | 병렬 처리 또는 멀티스레딩이 필요한 이유와 목적 설명                  |
| 스레드 구조 설계   | 어떤 작업을 어떤 스레드에 분배할지, 스레드 수, 공유 자원 여부 등     |
| 동기화 전략        | `join()`, `sleep()`, `synchronized`, `volatile` 등 사용 여부 설명     |
| 스레드 구현 방식   | `Runnable` 또는 `Thread` 클래스를 활용한 스레드 정의 및 실행 방식     |
| 결과 수집 방식     | `main` 스레드가 결과를 어떻게 수집하고 처리하는지 설명               |
| 실행 흐름 설명     | 각 스레드의 상태 전이, 실행 순서, 대기 방식 등 전체 흐름 설명         |


## 🧪 예시: 1~100까지 합을 두 스레드로 나누어 계산

### 🔹 문제 설명
1부터 100까지의 합을 두 개의 스레드로 나누어 계산하고, main 스레드가 결과를 합산한다.

### 🔹 스레드 구조 설계
- thread-1: 1~50까지 합산
- thread-2: 51~100까지 합산
- main: 두 스레드의 결과를 받아 최종 합산

### 🔹 동기화 전략
- join()을 사용하여 main 스레드가 두 작업 스레드의 종료를 기다림

### 🔹 코드 예제
```java
public class SumWithThreads {
    public static void main(String[] args) throws InterruptedException {
        SumTask task1 = new SumTask(1, 50);
        SumTask task2 = new SumTask(51, 100);

        Thread thread1 = new Thread(task1, "thread-1");
        Thread thread2 = new Thread(task2, "thread-2");

        thread1.start();
        thread2.start();

        thread1.join(); // thread-1 종료까지 대기
        thread2.join(); // thread-2 종료까지 대기

        int total = task1.result + task2.result;
        System.out.println("총합: " + total); // 5050
    }

    static class SumTask implements Runnable {
        int start, end, result = 0;

        public SumTask(int start, int end) {
            this.start = start;
            this.end = end;
        }

        public void run() {
            for (int i = start; i <= end; i++) {
                result += i;
            }
            System.out.println(Thread.currentThread().getName() + " 계산 완료: " + result);
        }
    }
}
```

### 🔹 실행 흐름 설명
- main 스레드는 두 스레드를 실행하고 join()으로 대기
- 각 스레드는 독립적으로 계산 후 result에 저장
- main은 두 결과를 합산하여 출력

### 🔹 출력 결과 예시
```
thread-1 계산 완료: 1275
thread-2 계산 완료: 3775
총합: 5050
```



