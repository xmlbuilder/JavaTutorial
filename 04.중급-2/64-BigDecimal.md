# BigDecimal
Java의 BigDecimal은 정밀한 소수 계산을 위해 사용하는 클래스입니다.  
특히 금융, 과학 계산 등에서 부동소수점 오차를 방지할 수 있음.  
아래에 핵심 개념과 주요 메서드, 예제 코드를 정리.

## 💡 BigDecimal이 필요한 이유
- float 또는 double은 이진 부동소수점 방식으로 저장되기 때문에 정확한 소수 계산이 어려움
- 예: System.out.println(0.1 + 0.2); → 0.30000000000000004
- BigDecimal은 문자열 기반 생성으로 오차 없이 계산 가능

## ✅ 주요 생성 방식
```java
new BigDecimal("1.1")   // 정확한 값
new BigDecimal(1.1)     // 부정확 (double 기반 오차 발생)
```
- 항상 문자열로 생성하는 것이 안전합니다.


## 🔧 BigDecimal 주요 연산 메서드

| 메서드          | 반환 타입 | 사용 예시                                 |
|-----------------|------------|--------------------------------------------|
| `add()`         | `BigDecimal` | `a.add(b)` → 두 값을 더함                   |
| `subtract()`    | `BigDecimal` | `a.subtract(b)` → 두 값을 뺌                |
| `multiply()`    | `BigDecimal` | `a.multiply(b)` → 두 값을 곱함              |
| `divide()`      | `BigDecimal` | `a.divide(b, 4, RoundingMode.HALF_UP)` → 나눗셈 후 반올림 |
| `doubleValue()` | `double`     | `a.doubleValue()` → `BigDecimal`을 `double`로 변환 |

## 🧪 예시 코드
```java
import java.math.BigDecimal;
import java.math.RoundingMode;

public class BigDecimalTest {
    public static void main(String[] args) {
        BigDecimal bd = new BigDecimal("1.1");

        BigDecimal bdAdd = new BigDecimal("2.2").add(bd);       // 3.3
        BigDecimal bdSub = new BigDecimal("2.2").subtract(bd);  // 1.1
        BigDecimal bdMulti = new BigDecimal("2.2").multiply(bd); // 2.42
        BigDecimal bdDiv = new BigDecimal("2.2").divide(bd, 4, RoundingMode.HALF_UP); // 2.0000

        double val1 = bdAdd.doubleValue();
        double val2 = bdSub.doubleValue();
        double val3 = bdMulti.doubleValue();
        double val4 = bdDiv.doubleValue();

        System.out.println(val1 + "," + val2 + "," + val3 + "," + val4);
        // 출력: 3.3,1.1,2.42,2.0
    }
}
```

## 🧠 RoundingMode 종류

| 모드 이름        | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| `HALF_UP`        | 0.5 이상이면 올림, 그 외는 내림 (일반적인 반올림 방식)               |
| `HALF_DOWN`      | 0.5 이상이어도 내림, 0.5 초과일 때만 올림                            |
| `UP`             | 무조건 올림 (양수/음수 관계없이 절댓값 기준)                         |
| `DOWN`           | 무조건 내림 (절댓값 기준, 소수점 이하 버림)                          |
| `CEILING`        | 양수면 올림, 음수면 내림 (수학적 천장 방향)                          |
| `FLOOR`          | 양수면 내림, 음수면 올림 (수학적 바닥 방향)                          |



## 📦 활용 팁
- 금융 계산에서는 항상 BigDecimal을 사용하세요.
- compareTo()로 값 비교 (equals()는 scale까지 비교하므로 주의)
- setScale()로 소수점 자리수 조절 가능
```java
BigDecimal price = new BigDecimal("123.4567").setScale(2, RoundingMode.HALF_UP); // 123.46
```

# Compare / Stream

Java에서 BigDecimal.compareTo()를 활용한 정렬 예제와 Stream과 함께 사용하는 방식까지 정리한 코드.  
compareTo()는 BigDecimal 값을 비교할 때 equals()보다 더 실용적입니다.

## 🔁 compareTo() 정렬 예제
```java
import java.math.BigDecimal;
import java.util.*;

public class CompareToSort {
    public static void main(String[] args) {
        List<BigDecimal> prices = Arrays.asList(
            new BigDecimal("10.50"),
            new BigDecimal("5.25"),
            new BigDecimal("7.75")
        );

        // 오름차순 정렬
        prices.sort((a, b) -> a.compareTo(b));
        System.out.println("오름차순: " + prices);

        // 내림차순 정렬
        prices.sort((a, b) -> b.compareTo(a));
        System.out.println("내림차순: " + prices);
    }
}
```


## 🌊 Stream과 함께 사용하는 예제
```java
import java.math.BigDecimal;
import java.util.*;
import java.util.stream.Collectors;

public class StreamBigDecimal {
    public static void main(String[] args) {
        List<BigDecimal> scores = Arrays.asList(
            new BigDecimal("88.5"),
            new BigDecimal("92.3"),
            new BigDecimal("75.0"),
            new BigDecimal("90.0")
        );

        // 80점 이상 필터링 후 오름차순 정렬
        List<BigDecimal> filteredSorted = scores.stream()
            .filter(score -> score.compareTo(new BigDecimal("80")) >= 0)
            .sorted()
            .collect(Collectors.toList());

        System.out.println("80점 이상 정렬: " + filteredSorted);
    }
}
```

## 🧠 compareTo() 반환값 요약

| 반환값 | 의미                          | 예시                            |
|--------|-------------------------------|----------------------------------|
| `< 0`  | 왼쪽 값이 오른쪽 값보다 작음  | `"apple".compareTo("banana")` → -1 |
| `0`    | 두 값이 같음                  | `"apple".compareTo("apple")` → 0  |
| `> 0`  | 왼쪽 값이 오른쪽 값보다 큼    | `"banana".compareTo("apple")` → 1 |

- equals()는 scale까지 비교하므로 "1.0"과 "1.00"은 다르게 판단됨. 
- compareTo()는 값만 비교하므로 "1.0".compareTo("1.00") == 0입니다.



## 🧠 핵심 개념: BigDecimal의 equals() vs compareTo()
- BigDecimal은 equals()와 compareTo()가 다르게 동작합니다.
- equals()는 값과 scale(자릿수) 모두 비교 → "1.0"과 "1.00"은 다름
- compareTo()는 값만 비교 → "1.0".compareTo("1.00") == 0
- 💡 Map의 키 비교는 equals()와 hashCode()를 사용하므로 "1.0"과 "1.00"은 다른 키로 인식됩니다.


## 🧪 예시 코드
```java
import java.math.BigDecimal;
import java.util.HashMap;
import java.util.Map;

public class BigDecimalMapTest {
    public static void main(String[] args) {
        Map<BigDecimal, String> priceMap = new HashMap<>();

        priceMap.put(new BigDecimal("1.0"), "Apple");
        priceMap.put(new BigDecimal("1.00"), "Banana");

        System.out.println(priceMap.size()); // 2 → 서로 다른 키로 인식
        System.out.println(priceMap.get(new BigDecimal("1.0")));  // Apple
        System.out.println(priceMap.get(new BigDecimal("1.00"))); // Banana
    }
}
```


## ⚠️ 주의사항
- new BigDecimal("1.0")과 new BigDecimal("1.00")은 equals()가 false → 서로 다른 키
- Map에서 BigDecimal을 키로 사용할 경우, scale을 통일하거나 normalize하는 것이 중요

## ✅ 해결 방법: stripTrailingZeros() 사용
```java
BigDecimal key = new BigDecimal("1.00").stripTrailingZeros();
```
- 이렇게 하면 "1.0"과 "1.00"이 동일한 키로 인식됩니다.

## 📦 활용 팁
- TreeMap<BigDecimal, V>을 사용할 경우 compareTo() 기반 정렬이 적용됨 → scale 무시
- HashMap<BigDecimal, V>은 equals() 기반 → scale 주의
- 키로 사용할 때는 항상 stripTrailingZeros() 또는 setScale()로 일관성 유지

---


