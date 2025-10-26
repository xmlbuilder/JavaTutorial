# 🎲 Java Random 함수 요약

## ✅ 주요 클래스 비교
| 클래스         | 설명                                      | 보안 수준     | 주요 생성 방식                   |
|----------------|-------------------------------------------|----------------|----------------------------------|
| `Random`       | 일반적인 난수 생성용 클래스                | 낮음           | `new Random()`                   |
| `SecureRandom` | 암호학적으로 안전한 난수 생성용 클래스     | 높음           | `new SecureRandom()` 또는 `SecureRandom.getInstance("SHA1PRNG")` |


## ✅ 주요 메서드 비교
| 메서드                  | 설명                                 | 예시 출력 (입력값 기준) |
|--------------------------|--------------------------------------|--------------------------|
| `nextInt()`              | int 범위 전체에서 난수 생성           | `-123456789`             |
| `nextInt(bound)`         | 0 이상 bound 미만의 난수 생성         | `0 ~ bound-1`            |
| `nextDouble()`           | 0.0 이상 1.0 미만의 실수 생성          | `0.123456789`            |
| `nextBoolean()`          | true 또는 false 반환                  | `true` 또는 `false`      |
| `nextBytes(byte[])`      | 바이트 배열에 난수 채움               | `[12, -45, 67, ...]`     |


## 🧪 예시 코드
```java
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.util.Random;

public class RandomTest {
    public static void main(String[] args) throws NoSuchAlgorithmException {

        // 일반 난수 생성
        Random random = new Random();
        System.out.println("Random int: " + random.nextInt());
        System.out.println("Random int (0~999): " + random.nextInt(1000));

        // 보안 난수 생성
        Random secure1 = new SecureRandom();
        System.out.println("SecureRandom int (0~99): " + secure1.nextInt(100));

        Random secure2 = SecureRandom.getInstance("SHA1PRNG");
        System.out.println("SecureRandom double: " + secure2.nextDouble());
    }
}
```

## 🧠 활용 팁
- `Random` 은 게임, 시뮬레이션 등 일반적인 용도에 적합
- `SecureRandom` 은 비밀번호, 토큰, 암호 키 생성 등 보안이 중요한 경우에 사용
- `SecureRandom.getInstance("SHA1PRNG")` 는 알고리즘을 명시적으로 지정할 수 있음
- `nextInt(bound)` 은 경계값 포함하지 않으므로 0 <= 값 < bound 범위임

---


