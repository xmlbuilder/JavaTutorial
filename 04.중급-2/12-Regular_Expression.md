## 📘 Java Regular Expression

### 📌 전체 코드
```java
//문장이 완전히 일치 하는 지확인 하고 있다.       
String pattern = "^[0-9]*$";
String val = "123456789 123456789";
boolean regex = Pattern.matches(pattern, val);
System.out.println(regex);

String line = "This order was placed for QT 3000! OK?";
String pattern = "(.*?)(\\d+)(.*)";
// Create a Pattern object
Pattern r = Pattern.compile(pattern);
// Now create matcher object.
Matcher m = r.matcher(line);
if (m.find()) {
    System.out.println("Found value: " + m.group(0));
    System.out.println("Found value: " + m.group(1));
    System.out.println("Found value: " + m.group(2));
    System.out.println("Found value: " + m.group(3));
} else {
    System.out.println("NO MATCH");
}
```

#### 🔹 첫 번째 코드: 숫자만 포함된 문자열인지 확인
```java
String pattern = "^[0-9]*$";
String val = "123456789 123456789";
boolean regex = Pattern.matches(pattern, val);
System.out.println(regex);
```

#### 🔹 설명:
- `pattern = **^[0-9]*$**`
    - `^`: 문자열의 시작
    - `[0-9]*`: 숫자(0~9)가 0개 이상 반복
    - `$`: 문자열의 끝
- 즉, 전체 문자열이 숫자만으로 구성되어야 함.
- val = "123456789 123456789"
    - 숫자 사이에 공백이 포함되어 있음.
- `Pattern.matches(pattern, val)`
    - 전체 문자열이 패턴과 완전히 일치하는지 확인.
#### 🔹 출력 결과:
```
false
```
- 공백이 포함되어 있으므로 숫자만으로 이루어진 문자열이 아니기 때문에 false가 출력됩니다.


#### 🔹 두 번째 코드: 문장에서 숫자 추출
```java
String line = "This order was placed for QT 3000! OK?";
String pattern = "(.*?)(\\d+)(.*)";
Pattern r = Pattern.compile(pattern);
Matcher m = r.matcher(line);

if (m.find()) {
    System.out.println("Found value: " + m.group(0));
    System.out.println("Found value: " + m.group(1));
    System.out.println("Found value: " + m.group(2));
    System.out.println("Found value: " + m.group(3));
} else {
    System.out.println("NO MATCH");
}
```
#### 🔹 설명:
- pattern = **(.*?)(\\d+)(.*)**
    - `(.*?)`: 숫자 앞의 모든 문자 (최소 매칭)
    - `(\\d+)`: 하나 이상의 숫자
    - `(.*)`: 숫자 뒤의 모든 문자
- Pattern.compile(pattern)
    - 정규식을 컴파일하여 Pattern 객체 생성
    - Matcher m = r.matcher(line)
    - line 문자열에 대해 패턴을 적용한 Matcher 객체 생성
- m.find()
    - 패턴과 일치하는 부분을 찾음
- m.group(n)
    - group(0): 전체 매칭된 문자열
    - group(1): 숫자 앞 부분
    - group(2): 숫자
    - group(3): 숫자 뒤 부분
#### 🔹 출력 결과:
```
Found value: This order was placed for QT 3000! OK?
Found value: This order was placed for QT 
Found value: 3000
Found value: ! OK?
```

| 정규식 패턴             | 설명                                      | 입력값                              | 결과     |
|------------------------|-------------------------------------------|-------------------------------------|----------|
| ^[0-9]*$               | 전체 문자열이 숫자만으로 구성되어야 함     | 123456789 123456789                 | false    |
| (.*?)(\\d+)(.*)        | 숫자를 포함한 부분을 앞/숫자/뒤로 분리     | This order was placed for QT 3000! OK? | 3000 추출 |

---


### 📌 1. 숫자만 포함된 문자열 검사
```java
String pattern = "^[0-9]+$";
String input = "123456";
System.out.println(Pattern.matches(pattern, input)); // true
```

- ^[0-9]+$: 숫자만 포함되어야 함 (하나 이상)

### 📌 2. 이메일 주소 유효성 검사
```java
String pattern = "^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$";
String email = "test.email@domain.co.kr";
System.out.println(Pattern.matches(pattern, email)); // true
```
- `\\w`: 알파벳, 숫자, 밑줄
- `.`: 도메인 구분
- `{2,}`: 최종 도메인 길이 제한

### 📌 3. 전화번호 형식 검사 (010-xxxx-xxxx)
```java
String pattern = "^010-\\d{4}-\\d{4}$";
String phone = "010-1234-5678";
System.out.println(Pattern.matches(pattern, phone)); // true
```
### 📌 4. 특정 단어 포함 여부
```java
String pattern = ".*(error|fail|exception).*";
String log = "System error occurred at line 42";
System.out.println(Pattern.matches(pattern, log)); // true
```
- .*: 앞뒤 아무 문자
- (error|fail|exception): 세 단어 중 하나 포함

### 📌 5. 날짜 형식 검사 (YYYY-MM-DD)
```java
String pattern = "^\\d{4}-\\d{2}-\\d{2}$";
String date = "2025-10-24";
System.out.println(Pattern.matches(pattern, date)); // true
```


### 📌 6. 문자열에서 숫자 추출
```java
String line = "Order ID: QT3000X";
String pattern = "\\d+";
Matcher m = Pattern.compile(pattern).matcher(line);
while (m.find()) {
    System.out.println("Found number: " + m.group());
}
```

- `\\d+`: 하나 이상의 숫자
- 결과: 3000

### 📌 7. HTML 태그 제거
```java
String html = "<p>Hello <b>World</b></p>";
String cleaned = html.replaceAll("<[^>]*>", "");
System.out.println(cleaned); // Hello World
```

- `<[^>]*>`: <로 시작해서 >로 끝나는 태그

### 📌 8. 공백 기준으로 단어 분리
```java
String sentence = "Java is powerful";
String[] words = sentence.split("\\s+");
System.out.println(Arrays.toString(words)); // [Java, is, powerful]
```

- `\\s+`: 하나 이상의 공백

---

