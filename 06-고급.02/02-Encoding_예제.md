# encoding 예제
두 개의 문자 인코딩 예제를 핵심 개념과 함께 깔끔하게 정리한 문서입니다.  

## 🧠 개념 요약
| 개념               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| `문자 인코딩`         | 문자를 컴퓨터가 이해할 수 있는 숫자(byte)로 변환하는 과정               |
| `문자 디코딩`         | 숫자(byte)를 다시 문자로 변환하는 과정                                 |
| `Charset`             | 인코딩/디코딩 시 사용하는 문자 집합 (예: UTF-8, EUC-KR, MS949 등)     |
| `getBytes(Charset)`  | 문자열을 지정한 Charset으로 byte 배열로 변환하는 메서드                 |
| `new String(bytes, Charset)` | byte 배열을 지정한 Charset으로 문자열로 복원하는 생성자         |


## 🔧 예제 1: 문자 인코딩만 수행
### 📄 코드: EncodingMain1.java
```
package charset;
import java.nio.charset.Charset;
import java.util.Arrays;
import static java.nio.charset.StandardCharsets.*;

public class EncodingMain1 {
    private static final Charset EUC_KR = Charset.forName("EUC-KR");
    private static final Charset MS_949 = Charset.forName("MS949");

    public static void main(String[] args) {
        System.out.println("== ASCII 영문 처리 ==");
        encoding("A", US_ASCII);
        encoding("A", ISO_8859_1);
        encoding("A", EUC_KR);
        encoding("A", MS_949);
        encoding("A", UTF_8);
        encoding("A", UTF_16BE);

        System.out.println("== 한글 지원 ==");
        encoding("가", EUC_KR);
        encoding("가", MS_949);
        encoding("가", UTF_8);
        encoding("가", UTF_16BE);
    }

    private static void encoding(String text, Charset charset) {
        byte[] bytes = text.getBytes(charset);
        System.out.printf("%s -> [%s] 인코딩 -> %s %sbyte\n",
            text, charset, Arrays.toString(bytes), bytes.length);
    }
}
```

### 📌 주요 결과 요약

| 문자 | 문자 집합       | 인코딩 결과 (byte 배열)     | 바이트 수 | 디코딩 결과 |
|------|------------------|-----------------------------|-----------|--------------|
| A    | US-ASCII         | [65]                        | 1byte     | A            |
| A    | UTF-16BE         | [0, 65]                     | 2byte     | (디코딩 실패) |
| 가    | EUC-KR           | [-80, -95]                  | 2byte     | 가            |
| 가    | UTF-8            | [-22, -80, -128]            | 3byte     | 가            |
| 뷁   | MS949            | [-108, -18]                 | 2byte     | 뷁           |



## 🔧 예제 2: 인코딩 + 디코딩 비교
### 📄 코드: EncodingMain2.java
```java
package charset;
import java.nio.charset.Charset;
import java.util.Arrays;
import static java.nio.charset.StandardCharsets.*;

public class EncodingMain2 {
    private static final Charset EUC_KR = Charset.forName("EUC-KR");
    private static final Charset MS_949 = Charset.forName("MS949");

    public static void main(String[] args) {
        System.out.println("== 영문 ASCII 인코딩 ==");
        test("A", US_ASCII, US_ASCII);
        test("A", US_ASCII, UTF_8);
        test("A", US_ASCII, UTF_16BE);

        System.out.println("== 한글 인코딩 - 기본 ==");
        test("가", EUC_KR, EUC_KR);
        test("가", UTF_8, UTF_8);
        test("가", UTF_16BE, UTF_16BE);

        System.out.println("== 한글 인코딩 - 복잡한 문자 ==");
        test("뷁", MS_949, MS_949);
        test("뷁", UTF_8, UTF_8);

        System.out.println("== 인코딩과 디코딩이 다른 경우 ==");
        test("가", EUC_KR, UTF_8); // 깨짐
        test("뷁", MS_949, EUC_KR); // 깨짐
    }

    private static void test(String text, Charset encodingCharset, Charset decodingCharset) {
        byte[] encoded = text.getBytes(encodingCharset);
        String decoded = new String(encoded, decodingCharset);
        System.out.printf("%s -> [%s] 인코딩 -> %s %sbyte -> [%s] 디코딩 -> %s\n",
            text, encodingCharset, Arrays.toString(encoded), encoded.length, decodingCharset, decoded);
    }
}
```

### 📌 디코딩 실패 예시

| 문자 | 인코딩 Charset | 디코딩 Charset | 인코딩 결과 (byte 배열) | 디코딩 결과 |
|------|------------------|------------------|---------------------------|--------------|
| 가   | EUC-KR           | UTF-8            | [-80, -95]                | � (깨짐)     |
| 뷁   | MS949            | EUC-KR           | [-108, -18]               | � (깨짐)     |
| 가   | UTF-8            | MS949            | [-22, -80, -128]          | 媛 (깨짐)    |
| A   | UTF-8            | UTF-16BE         | [65]                      |   (깨짐)     |

## 🧠 요약
- 문자 집합이 다르면 디코딩 시 깨짐 발생
- EUC-KR ↔ UTF-8, MS949 ↔ UTF-8 간 호환되지 않음
- UTF-16은 ASCII와 호환되지 않음 → 영문도 깨질 수 있음

## 🧠 인코딩 정리

| 문자 집합     | 특징                                                   | 비고                         |
|----------------|--------------------------------------------------------|------------------------------|
| US-ASCII        | 7비트, 영문/숫자/기본 특수문자 표현                   | 모든 문자 집합의 기반        |
| ISO-8859-1      | 8비트, ASCII + 서유럽 문자                            | ASCII 호환, 라틴 문자 확장   |
| EUC-KR          | 2바이트, 한글 2,350자 + 한자 + ASCII                  | 한국 초기 문자 집합          |
| MS949           | EUC-KR 확장, 한글 11,172자 표현 가능                  | Windows 기본 인코딩          |
| UTF-8           | 가변 길이(1~4바이트), ASCII 호환, 전 세계 문자 지원   | 웹 표준, 현대 기본 인코딩    |
| UTF-16BE        | 고정 2바이트(일부 4바이트), ASCII 비호환              | 자바 내부 문자 표현 방식     |
| UTF-16          | BOM 포함, BE/LE 구분 가능                             | 현재는 잘 사용되지 않음      |



## ⚠️ 한글 깨짐 주의사항
- EUC-KR ↔ UTF-8 간 디코딩은 호환되지 않음
- MS949 ↔ UTF-8 간 디코딩도 호환되지 않음
- ISO-8859-1은 한글을 지원하지 않음 → ?로 표시됨

---

