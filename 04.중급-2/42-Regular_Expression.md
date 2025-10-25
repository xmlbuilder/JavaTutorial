# Regular Expression
Java의 정규 표현식(Pattern, Matcher)을 활용해 문자열에서 특정 패턴을 추출하고 검사하는 예제.  
아래에 각 코드 블록을 기능별로 분석하고 설명.

## 🔍 첫 번째 블록 분석
```java
String text = "private List<string> results = new List<string>();";
Pattern pattern = Pattern.compile("List<\\w+>");
Matcher matcher = pattern.matcher(text);
```

### ✅ 목적
- "List<string>" 같은 제네릭 타입을 찾기 위한 정규식
### ✅ 정규식 설명
- "List<\\w+>"
- List< : 문자열 "List<" 그대로
- \\w+ : 하나 이상의 알파벳/숫자/언더스코어
- > : 닫는 꺾쇠
### ✅ 결과
```java
while(matcher.find()){
    String group = matcher.group();
    System.out.println(group);
}
```

- 출력 결과:
```
List<string>
List<string>
```
- find()는 매칭되는 부분 문자열을 반복적으로 찾음


###❗ 주의: matcher.matches()는 전체 일치 검사
```java
boolean find = matcher.matches();
System.out.println(find); // false
```

- matches()는 문자열 전체가 정규식과 일치해야 true
- 여기선 "private List<string> results = new List<string>();" 전체가 "List<\\w+>"와 일치하지 않으므로 false

## 🔍 두 번째 블록 분석
```java
var text = "private List<string> results = new List<string>()?";
var pattern = Pattern.compile("\\b[a-z]+\\b");
var matcher = pattern.matcher(text);
```

### ✅ 정규식 설명
- "\\b[a-z]+\\b"
- \\b : 단어 경계
- [a-z]+ : 소문자 알파벳 하나 이상
### ✅ 결과
```java
while(matcher.find()){
    String group = matcher.group();
    System.out.println(group);
}
```

- 출력 결과:
```
private
results
new
```
- List, string은 대문자 또는 꺾쇠 때문에 매칭되지 않음


## 🧠 정규식 비교 요약

| 항목         | `List<\\w+>`                     | `\\b[a-z]+\\b`                  |
|--------------|----------------------------------|---------------------------------|
| 목적         | 제네릭 타입 추출 (`List<string>`) | 소문자 단어 추출 (`private`, `new`) |
| `find()` 결과| `List<string>` 2회               | `private`, `results`, `new`     |
| `matches()` 결과 | `false` (전체 일치 아님)         | 사용 안 함                      |

---

# 정규 표현식 기능별 요약

## 📌 1. 특정 키워드 포함 필터링
```java
var matcher1 = pattern.matcher(text);
List<String> results = matcher1.results()
    .filter(s -> s.group().contains("private"))
    .map(s -> s.group())
    .toList();
System.out.println(results.size());
```

- 목적: 특정 키워드를 포함한 결과만 필터링
- 추가 샘플: "public List<int> values"에서 "public" 포함 여부 필터링

## 📌 2. 꺾쇠 괄호 안의 텍스트 추출
```java
Pattern.compile("<([^<>]+)>")
```

- 목적: <값형> → "값형" 추출
- 추가 샘플: <tag>내용</tag> → "tag" 추출

## 📌 3. 대소문자 구분 없는 단어 매칭
```java
Pattern.compile("\\bkor\\b", Pattern.CASE_INSENSITIVE)
```
- 목적: "kor", "KOR", "Kor" 모두 매칭
- 추가 샘플: "java", "JAVA", "Java" → \\bjava\\b

## 📌 4. 줄마다 특정 길이의 단어 추출
```java
Pattern.compile("^[a-zA-Z]{5,7}", Pattern.MULTILINE)
```

- 목적: 줄 시작에서 5~7자 알파벳 단어 추출
- 추가 샘플: "Hello\nWorld\nExample" → "Hello", "World"

## 📌 5. 유사 표현 치환
```java
text.replaceAll("쪼끔씩|쪼금씩|쬐끔씩", "조금씩")
```

- 목적: 다양한 표현을 표준화
- 추가 샘플: "예쁘다|이쁘다" → "예쁘다"

📌 6. 확장자 변경
```java
text.replaceAll("\\.(htm)\\b", ".html")
```

- 목적: .htm → .html로 변경
- 추가 샘플: .jpeg → .jpg

## 📌 7. 숫자 + 단위 치환 (C# 스타일)
```java
Regex.Replace(text, @"(\d+)바이트", "$1byte")
```

- 목적: "1024바이트" → "1024byte"
- 추가 샘플: "5킬로그램" → "5kg"

## 📌 8. 숫자 그룹화 포맷팅
```java
text.replaceAll("(\\d{4})(\\d{4})(\\d{4})(\\d{4})", "$1-$2-$3-$4")
```

- 목적: "1234567890123456" → "1234-5678-9012-3456"
- 추가 샘플: 전화번호 "01012345678" → "010-1234-5678"

## 📌 9. CSV-like 문자열 분리
```java
text.split("\\s*,\\s*")
```

- 목적: "Word, Excel  ,PowerPoint" → ["Word", "Excel", "PowerPoint"]
- 추가 샘플: "a;b ; c" → split("\\s*;\\s*")

## 📌 10. 특정 패턴의 식별자 추출
```java
Pattern.compile("\\b[a-zA-Z][0-9]{5,}\\b")
```

- 목적: "a123456", "AX98765" 등 추출
- 추가 샘플: "U00001", "Z99999"

## 📌 11. 한글 + 숫자 조합 추출
```java
Pattern.compile("[가-힣]+-[0-9]{2,3}-[0-9]+")
```

- 목적: "삼겹살-84-58433" 등 추출
- 추가 샘플: "김치-12-3456"

## 📌 12. XML/태그 이름 추출
```java
Pattern.compile("<(\\w+?)>")
```

- 목적: <name> → "name"
- 추가 샘플: <title>, <body> → "title", "body"

## 📌 13. 반복 문자 감지
```java
Pattern.compile("(\\w)\\1")
```

- 목적: "빵빵", "bb" 등 추출
- 추가 샘플: "ㅋㅋ", "ㅎㅎ"

## 📌 14. 회문 구조 감지 (ABA 패턴)
```java
Pattern.compile("\\b(\\w)\\w\\1\\b")
```

- 목적: "pop" → 회문 감지
- 추가 샘플: "dad", "mom"

## ✨ 정규 표현식 샘플

| 기능             | 정규식                              | 입력 예시                  | 추출 결과 또는 치환 결과     |
|------------------|--------------------------------------|-----------------------------|-------------------------------|
| 이메일 추출       | `[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}`   | `"test@domain.com"`         | `"test@domain.com"`           |
| URL 추출         | `https?://[^\\s]+`                  | `"https://example.com"`     | `"https://example.com"`       |
| 날짜 추출        | `\\d{4}-\\d{2}-\\d{2}`               | `"2025-10-25"`              | `"2025-10-25"`                |
| 한글 단어 추출    | `[가-힣]{2,}`                        | `"한글 테스트"`             | `"한글"`, `"테스트"`          |
| 숫자 추출        | `\\d+`                               | `"총 123명"`                | `"123"`                       |


## 🔧 확장 아이디어 정규식 요약

| 기능               | 정규식                          | 입력 예시                  | 추출 또는 치환 결과             |
|--------------------|----------------------------------|-----------------------------|----------------------------------|
| 전화번호 포맷팅     | `(\\d{3})(\\d{4})(\\d{4})`       | `"01012345678"`             | `"010-1234-5678"`                |
| HTML 태그 제거      | `<[^>]+>`                        | `"<b>bold</b>"`             | `"bold"`                         |
| 파일 확장자 추출    | `\\.(\\w+)$`                     | `"report.pdf"`              | `"pdf"`                          |
| 영문 단어 추출      | `\\b[a-zA-Z]{4,}\\b`             | `"This is a sample text"`   | `"This"`, `"sample"`, `"text"`   |
| 반복 문자 감지       | `(\\w)\\1`                       | `"빵빵", "bb", "ㅎㅎ"`       | `"빵빵"`, `"bb"`, `"ㅎㅎ"`        |

---

## 전체 소스

### 1.
```java
String text = "private List<string> results = new List<string>();";
Pattern pattern = Pattern.compile("List<\\w+>");
Matcher matcher = pattern.matcher(text);
while(matcher.find()){
    String group = matcher.group();
    System.out.println(group);
}
boolean find = matcher.matches();
System.out.println(find);
if(find){
    System.out.println("success");
}
else {
    System.out.println("failed");
}

var text = "private List<string> results = new List<string>()?";
var pattern = Pattern.compile("\\b[a-z]+\\b");
var matcher = pattern.matcher(text);
while(matcher.find()){
    String group = matcher.group();
    System.out.println(group);
}

```

### 2.
```java
var matcher1 = pattern.matcher(text);
List<String> results = matcher1.results().filter(s -> s.group().contains("private"))
        .map(s -> s.group()).toList();
System.out.println(results.size());


var text = "C#에는 <값형>과 <참조형>이라는 두가지의 형이 존재합니다.";
var pattern = Pattern.compile("<([^<>]+)>");
var matcher = pattern.matcher(text);
while(matcher.find()){
    
    String str  = matcher.group(1);
    System.out.println(str);
}


var text = "kor, KOR, Kor";
var pattern = Pattern.compile("\\bkor\\b", Pattern.CASE_INSENSITIVE);
var matcher = pattern.matcher(text);
while(matcher.find()){
    String str  = matcher.group();
    System.out.println(str);
}


var text = "Word\nExcel\nPowerPoint\nOutlook\nOneNote\n";
var pattern = Pattern.compile("^[a-zA-Z]{5,7}", Pattern.MULTILINE);
var matches = pattern.matcher(text);
while(matches.find()){
    String str = matches.group();
    System.out.println(str);
}
```

### 3.

```java
var text = "C# 공부를 쪼끔씩 진행해 보자";
String strReplaced = text.replaceAll("쪼끔씩|쪼금씩|쬐끔씩","조금씩");
System.out.println(strReplaced);


var text = "foo.htm bar.html baz.htm";
var replaced = text.replaceAll("\\.(htm)\\b", ".html");
System.out.println(replaced);

```

### 4.
```java
var text = "1024바이트, 8바이트 문자, 바이트, 킬로바이트";
var pattern = @"(\d+)바이트";
var replaced = Regex.Replace(text, pattern, "$1byte");
Console.WriteLine(replaced); //1024byte, 8byte 문자, 바이트, 킬로바이트

var text = "1234567890123456";
var pattern = "(\\d{4})(\\d{4})(\\d{4})(\\d{4})";
var replaced = text.replaceAll(pattern, "$1-$2-$3-$4");
System.out.println(replaced);

```

### 5.
```java
var text = "Word, Excel  ,PowerPoint   , Outlook,OneNote";
var pattern = "\\s*,\\s*";
String[] subStrings =  text.split(pattern);
for (var str : subStrings)
{
    System.out.println(str);
}
```

### 6.
```java
var text = "a123456 b123 z12345 AX98765";
var pattern = Pattern.compile("\\b[a-zA-Z][0-9]{5,}\\b");
var matches = pattern.matcher(text);
while(matches.find()){
    String str = matches.group();
    System.out.println(str);
}

var text = "삼겹살-84-58433, 상추-95838-488";
var pattern = Pattern.compile("[가-힣]+-[0-9]{2,3}-[0-9]+");
var matcher = pattern.matcher(text);
while (matcher.find())
{
    System.out.println(matcher.group());
}

```

### 7.
```java
/*
var text = "<person><name>JungHwan Jeong</name><age>22</age></person>";
var pattern = Pattern.compile("<(\\w[^>]+)>");
var matcher = pattern.matcher(text);
while(matcher.find()){
    System.out.println(matcher.group(1));
}
*/

var text = "<person><name>JungHwan Jeong</name><age>22</age></person>";
var pattern = Pattern.compile("<(\\w+?)>");
var matcher = pattern.matcher(text);
while(matcher.find()){
    System.out.println(matcher.group(1));
}   
```

### 8.
```java
//한글은 동작하지 않은 -> 원인 분석 필요함.        

var text = "도로를 지나가는 차들이 뛰뛰하고 경적을 울리면 반대쪽 차들이 빵빵하고 울렸다. bb";
var pattern = Pattern.compile("(\\w)\\1");
var matcher = pattern.matcher(text);
while (matcher.find())
{
    System.out.println(matcher.group());
}
var text = "기러기 펠리컨 pop";
var pattern = Pattern.compile("\\b(\\w)\\w\\1\\b");
var matcher =pattern.matcher(text);
while (matcher.find())
{
    System.out.println(matcher.group());
}
```

---
