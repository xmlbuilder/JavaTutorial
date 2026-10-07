## 📘 자바 String 클래스 - 핵심 정리

### 1️⃣ 문자 vs 문자열
| 타입     | 설명                                               |
|----------|----------------------------------------------------|
| char     | 문자 하나를 표현하는 기본형. 예: `'a'`              |
| char[]   | 여러 문자를 배열로 표현. 문자열처럼 보이지만 불편함 |
| String   | 문자열을 다루는 참조형 클래스. 다양한 기능 제공     |

```java
char[] charArr = new char[]{'h','e','l','l','o'};
String str = "hello";
```

### 2️⃣ 문자열 생성 방식
| 방식                  | 설명                                                                 |
|-----------------------|----------------------------------------------------------------------|
| `"hello"`             | 문자열 리터럴. 문자열 풀에서 재사용됨. 동일한 문자열은 같은 참조값 사용 |
| `new String("hello")` | 항상 새로운 인스턴스를 생성. 문자열 풀과 무관. 참조값이 다름             |

```java
String str1 = "hello";
String str2 = new String("hello");
```


### 3️⃣ String 클래스 구조
```java
public final class String {
    private final char[] value; // 자바 9 이전
    private final byte[] value; // 자바 9 이후
    public int length() {...}
    public String concat(String str) {...}
    ...
}

```

- 내부에 문자 데이터를 char[] 또는 byte[]로 보관
- 다양한 문자열 처리 메서드 제공

### 4️⃣ 주요 메서드
| 메서드                     | 설명                                                  |
|----------------------------|-------------------------------------------------------|
| `length()`                 | 문자열의 길이를 반환                                  |
| `charAt(int)`             | 지정된 인덱스의 문자 반환                             |
| `substring(int, int)`     | 시작~끝 인덱스 사이의 부분 문자열 반환                |
| `indexOf(String)`         | 특정 문자열이 처음 등장하는 위치 반환                 |
| `toLowerCase()`, `toUpperCase()` | 소문자/대문자로 변환                        |
| `trim()`                  | 문자열 양쪽의 공백 제거                               |
| `concat(String)`          | 문자열을 이어 붙임 (`+` 연산자와 동일한 효과)         |


### 5️⃣ 문자열 연결
```java
String a = "hello";
String b = " java";
String result1 = a.concat(b);
String result2 = a + b;
```

- concat() 메서드 사용 가능
- 자바는 + 연산자도 문자열 연결에 대해 특별히 지원함

### 6️⃣ 문자열 비교
| 비교 방식   | 설명                                                                 |
|-------------|----------------------------------------------------------------------|
| `==`        | 참조값 비교. 두 객체가 **같은 인스턴스** 를 가리키는지 확인             |
| `equals()`  | 내부 값 비교. 두 문자열의 **내용이 같은지** 확인                        |

```java
String str1 = new String("hello");
String str2 = new String("hello");
System.out.println(str1 == str2);       // false
System.out.println(str1.equals(str2));  // true
```

- 항상 equals()를 사용해서 비교해야 안전함

### 7️⃣ 문자열 풀 (String Pool)
- 자바는 리터럴 문자열을 문자열 풀에 저장하여 재사용
- "hello" 같은 리터럴은 풀에서 동일한 참조값을 공유
- 메모리 절약 + 성능 최적화

```java
String str3 = "hello";
String str4 = "hello";
System.out.println(str3 == str4); // true
```

### 8️⃣ 실무 팁 — 문자열 처리 시 주의사항
| 상황                         | 권장 방식 또는 설명                                  |
|------------------------------|------------------------------------------------------|
| 문자열 비교                  | 항상 `equals()` 사용. `==`은 참조값 비교이므로 위험함 |
| 문자열 연결이 적을 때        | `+` 연산자 사용 가능. 컴파일 시 자동으로 최적화됨     |
| 문자열 연결이 많을 때        | `StringBuilder` 사용. 성능과 메모리 효율 높음         |
| 문자열 객체 생성             | `new String()` 지양. 리터럴 `"..."` 사용 권장         |
| 멀티쓰레드 환경에서 연결     | `StringBuffer` 사용. 동기화 지원                      |

---

## 📘 String 클래스 - 불변 객체 핵심 정리
### 1️⃣ 불변 객체란?
- 한 번 생성되면 내부 상태가 절대 변경되지 않는 객체
- String은 불변 객체이므로, 문자열을 수정하는 대신 새로운 객체를 반환함

### 2️⃣ 문자열 연결 예시
```java
String str1 = "hello";
String str2 = str1.concat(" java");
System.out.println("str1 = " + str1); // hello
System.out.println("str2 = " + str2); // hello java
```

- str1은 그대로 "hello" 유지
- concat()은 "hello java"라는 새로운 String 객체를 만들어 반환
- 기존 객체는 절대 변경되지 않음

### 3️⃣ 불변 설계의 이유
| 항목                         | 설명                                                                 |
|------------------------------|----------------------------------------------------------------------|
| 사이드 이펙트 방지            | 공유된 문자열이 변경되면 다른 참조도 영향을 받음 → 불변으로 차단       |
| 문자열 풀 최적화             | 동일한 리터럴은 풀에서 재사용 → 값이 바뀌면 풀의 안정성 무너짐         |
| 멀티쓰레드 안전성            | 상태 변경이 없으므로 동기화 없이도 안전하게 공유 가능                  |
| 자료구조 호환성              | `String`은 `equals()`와 `hashCode()`를 재정의 → 해시 기반 구조에서 안정적 |


### 4️⃣ 문자열 풀과 불변성
```java
String str3 = "hello";
String str4 = "hello";
```

- "hello"는 문자열 풀에서 공유됨 → str3 == str4는 true
- 만약 String이 가변이었다면, str3의 값 변경 시 str4도 영향을 받음
- 불변이기 때문에 공유해도 안전함


---

## 📘 String 클래스 - 주요 메서드 ① 정리
### 1️⃣ 문자열 정보 조회
| 메서드         | 설명                                                                 |
|----------------|----------------------------------------------------------------------|
| `length()`     | 문자열의 길이를 반환. 공백 포함 전체 문자 수                          |
| `isEmpty()`    | 문자열이 비어 있는지 확인 (`length() == 0`)                          |
| `isBlank()`    | 문자열이 비어 있거나 `공백만` 있는지 확인 (자바 11 이상)               |
| `charAt(int)`  | 지정된 인덱스의 문자 반환. 인덱스는 0부터 시작                        |

#### 🔹 예시
```java
public class StringInfoMain {
    public static void main(String[] args) {
        String str = "Hello, Java!";
        System.out.println("문자열의 길이: " + str.length());
        System.out.println("문자열이 비어 있는지: " + str.isEmpty());
        System.out.println("문자열이 비어 있거나 공백인지1: " + str.isBlank()); //Java11
        System.out.println("문자열이 비어 있거나 공백인지2: " + " ".isBlank());
        char c = str.charAt(7);
        System.out.println("7번 인덱스의 문자: " + c);
    }
}
```

#### 🔹 예시 결과:
```
문자열의 길이: 12
문자열이 비어 있는지: false
문자열이 공백인지: false
문자열이 비어 있거나 공백인지: true
7번 인덱스의 문자: J
```

### 2️⃣ 문자열 비교
| 메서드                          | 설명                                                                 |
|---------------------------------|----------------------------------------------------------------------|
| `equals(Object)`                | 문자열 내용이 같은지 비교 (대소문자 구분)                            |
| `equalsIgnoreCase(String)`      | 문자열 내용이 같은지 비교 (대소문자 구분 없이)                       |
| `compareTo(String)`             | 사전 순으로 문자열 비교. 같으면 0, 앞서면 음수, 뒤면 양수 반환       |
| `compareToIgnoreCase(String)`   | 사전 순 비교 (대소문자 구분 없이)                                   |
| `startsWith(String)`            | 특정 접두사로 시작하는지 확인                                        |
| `endsWith(String)`              | 특정 접미사로 끝나는지 확인                                          |


#### 🔹 예시
```java
public class StringComparisonMain {
    public static void main(String[] args) {
        String str1 = "Hello, Java!"; //대문자 일부 있음
        String str2 = "hello, java!"; //대문자 없음 모두 소문자
        String str3 = "Hello, World!";
        System.out.println("str1 equals str2: " + str1.equals(str2));
        System.out.println("str1 equalsIgnoreCase str2: " + str1.equalsIgnoreCase(str2));
        System.out.println("'b' compareTo 'a': " + "b".compareTo("a"));
        System.out.println("str1 compareTo str3: " + str1.compareTo(str3));
        System.out.println("str1 compareToIgnoreCase str2: " + str1.compareToIgnoreCase(str2));
        System.out.println("str1 starts with 'Hello': " + str1.startsWith("Hello"));
        System.out.println("str1 ends with 'Java!': " + str1.endsWith("Java!"));
    }
}
```

#### 🔹 예시 결과:
```
str1 equals str2: false
str1 equalsIgnoreCase str2: true
'b' compareTo 'a': 1
str1 compareTo str3: -13
str1 compareToIgnoreCase str2: 0
str1 starts with 'Hello': true
str1 ends with 'Java!': true
```


### 3️⃣ 문자열 검색
| 메서드                     | 설명                                                                 |
|----------------------------|----------------------------------------------------------------------|
| `contains(CharSequence)`   | 문자열에 특정 문자가 포함되어 있는지 확인                             |
| `indexOf(String)`          | 특정 문자열이 처음 등장하는 위치 반환                                 |
| `indexOf(String, int)`     | 지정된 위치부터 검색하여 처음 등장하는 위치 반환                      |
| `lastIndexOf(String)`      | 특정 문자열이 마지막으로 등장하는 위치 반환                           |


#### 🔹 예시
```java
public class StringSearchMain {
    public static void main(String[] args) {
        String str = "Hello, Java! Welcome to Java world.";
        System.out.println("문자열에 'Java'가 포함되어 있는지: " + str.contains("Java"));
        System.out.println("'Java'의 첫 번째 인덱스: " + str.indexOf("Java"));
        System.out.println("인덱스 10부터 'Java'의 인덱스: " + str.indexOf("Java", 10));
        System.out.println("'Java'의 마지막 인덱스: " + str.lastIndexOf("Java"));
    }
}
```

#### 🔹 예시 결과:
```
문자열에 'Java'가 포함되어 있는지: true
'Java'의 첫 번째 인덱스: 7
인덱스 10부터 'Java'의 인덱스: 24
'Java'의 마지막 인덱스: 24
```

--- 

## 📘 String 클래스 - 주요 메서드 ② 정리
### 1️⃣ 문자열 조작 및 변환
| 메서드                              | 설명                                                                 |
|-------------------------------------|----------------------------------------------------------------------|
| `substring(begin, end)`             | 지정된 범위의 부분 문자열을 추출                                     |
| `concat(String)`                    | 문자열 끝에 다른 문자열을 이어 붙임                                  |
| `replace(target, replacement)`      | 특정 문자열을 새 문자열로 대체                                       |
| `replaceAll(regex, replacement)`    | 정규식과 일치하는 모든 부분을 새 문자열로 대체                       |
| `replaceFirst(regex, replacement)`  | 정규식과 일치하는 첫 번째 부분만 새 문자열로 대체                    |
| `toLowerCase()` / `toUpperCase()`   | 문자열을 소문자 또는 대문자로 변환                                   |
| `trim()`                            | 문자열 양쪽 끝의 공백 제거 (단순 Whitespace만 제거)                  |
| `strip()` / `stripLeading()` / `stripTrailing

### 2️⃣ 문자열 분할 및 조합
| 메서드                          | 설명                                                                 |
|---------------------------------|----------------------------------------------------------------------|
| `split(String regex)`           | 정규 표현식을 기준으로 문자열을 분할하여 배열로 반환                  |
| `join(delimiter, elements...)`  | 주어진 구분자를 기준으로 여러 문자열을 하나로 결합                    |

#### 🔹 예시
```java
public class StringSplitJoinMain {
    public static void main(String[] args) {
        String str = "Apple,Banana,Orange";
        // split()
        String[] splitStr = str.split(",");
        for(String s : splitStr) {
            System.out.println(s);
        }
        // join()
        String joinedStr = String.join("-", "A", "B", "C");
        System.out.println("연결된 문자열: " + joinedStr);
        // 문자열 배열 연결
        String result = String.join("-", splitStr);
        System.out.println("result = " + result);
}
}

```
#### 🔹 예시 결과:
```
Apple
Banana
Orange
연결된 문자열: A-B-C
result = Apple-Banana-Orange
```

### 3️⃣ 기타 유틸리티

| 메서드                        | 설명                                                                 |
|-------------------------------|----------------------------------------------------------------------|
| `valueOf(Object obj)`         | 숫자, 불리언, 객체 등 다양한 타입을 문자열로 변환                     |
| `toCharArray()`               | 문자열을 문자 배열(`char[]`)로 변환                                   |
| `format(String, Object...)`   | 형식 문자열과 인자를 사용해 포맷된 문자열 생성 (`%d`, `%s`, `%f` 등) |
| `matches(String regex)`       | 문자열이 주어진 정규 표현식과 일치하는지 확인                         |

#### 🔹 예시
```java
public class StringUtilsMain1 {
    public static void main(String[] args) {
        int num = 100;
        boolean bool = true;
        Object obj = new Object();
        String str = "Hello, Java!";

        // valueOf 메서드
        String numString = String.valueOf(num);
        System.out.println("숫자의 문자열 값: " + numString);
        String boolString = String.valueOf(bool);
        System.out.println("불리언의 문자열 값: " + boolString);
        String objString = String.valueOf(obj);
        System.out.println("객체의 문자열 값: " + objString);

        //다음과 같이 간단히 변환할 수 있음 (문자 + x -> 문자x)
        String numString2 = "" + num;
        System.out.println("빈문자열 + num:" + numString2);

        // toCharArray 메서드
        char[] strCharArray = str.toCharArray();
        for (char c : strCharArray) {
            System.out.print(c);
        }
        System.out.println();
    }
}

```


### 📌 예시 결과:
```
숫자의 문자열 값: 100
불리언의 문자열 값: true
객체의 문자열 값: java.lang.Object@...
빈문자열 + num:100
Hello, Java!
```

---

## 📘 StringBuilder - 가변 문자열 처리
### 1️⃣ 불변 String 클래스의 단점
- String은 불변(immutable): 한 번 생성된 문자열은 변경 불가
- 문자열을 더하거나 수정할 때마다 새로운 객체가 생성됨
- 예시:
```java
String str = "A" + "B" + "C" + "D";
```
- 내부적으로 "AB", "ABC" 같은 중간 객체가 생성되며, 최종 "ABCD"만 사용됨  
    - 중간 객체는 GC 대상이 되어 메모리 낭비 발생  
    -❗ 문제점- 문자열 변경이 많을수록 객체 생성 + GC 비용 증가  
- CPU, 메모리 자원 소모 증가
- 대용량 문자열 처리 시 성능 저하

### 2️⃣ 해결책: StringBuilder 클래스- 자바는 가변 문자열을 위한 StringBuilder 제공
- 내부적으로 byte[] 또는 char[] 배열을 사용해 문자열을 직접 수정
- 새로운 객체 생성 없이 추가, 삭제, 삽입, 뒤집기 가능

### 📌 StringBuilder - 주요 메서드
| 메서드               | 설명                                      | 반환 타입     |
|----------------------|-------------------------------------------|---------------|
| `append(str)`        | 문자열을 끝에 추가                         | `StringBuilder` |
| `insert(pos, str)`   | 지정한 위치에 문자열 삽입                  | `StringBuilder` |
| `delete(start, end)` | 지정한 범위의 문자열 삭제                  | `StringBuilder` |
| `reverse()`          | 문자열을 뒤집음                            | `StringBuilder` |
| `toString()`         | 최종 문자열을 `String` 객체로 반환         | `String`        |

### 3️⃣ 사용 예제
```java
StringBuilder sb = new StringBuilder();
sb.append("A");
sb.append("B");
sb.append("C");
sb.append("D");
System.out.println("sb = " + sb); // ABCD

sb.insert(4, "Java");
System.out.println("insert = " + sb); // ABCDJava

sb.delete(4, 8);
System.out.println("delete = " + sb); // ABCD

sb.reverse();
System.out.println("reverse = " + sb); // DCBA

String result = sb.toString();
System.out.println("string = " + result); // DCBA
```
### 🔁 가변 vs 불변 비교

| 항목             | String (불변)                              | StringBuilder (가변)                          |
|------------------|--------------------------------------------|-----------------------------------------------|
| 변경 가능 여부    | ❌ 불가능 (새 객체 생성)                    | ✅ 가능 (기존 객체 수정)                       |
| 성능              | 느림 (객체 생성 많음)                      | 빠름 (객체 재사용)                             |
| 메모리 사용       | 많음 (GC 대상 증가)                        | 적음 (배열 기반 수정)                          |
| 사용 목적         | 변경이 적은 문자열 처리                    | 자주 변경되는 문자열 처리                      |
| 스레드 안전성     | ✅ 안전 (불변 특성)                         | ❌ 비동기 환경에서는 `StringBuffer` 사용 권장   |
| 최종 결과 반환     | 그대로 사용 가능                           | `toString()`으로 `String`으로 변환 필요         |

### 📌 실무 팁- 문자열을 반복적으로 수정해야 할 경우 → StringBuilder 사용
- 변경이 끝난 후에는 toString()으로 불변 String으로 변환해 안전하게 사용
- 멀티스레드 환경에서는 StringBuffer 사용 (동기화 지원)

---


## 📘 자바의 문자열 최적화
### 1️⃣ 문자열 리터럴 최적화
- 자바 컴파일러는 리터럴끼리의 문자열 결합을 컴파일 시점에 미리 처리함

```java
// 컴파일 전
String helloWorld = "Hello, " + "World!";

// 컴파일 후
String helloWorld = "Hello, World!";
- 결과적으로 런타임 성능 향상
- + 연산을 사용해도 성능 저하 없음 (리터럴일 경우)

````


### 2️⃣ 변수 기반 문자열 결합
- 변수는 컴파일 시점에 값을 알 수 없기 때문에 최적화가 어려움
```java
String result = str1 + str2;
```
- 자바는 내부적으로 StringBuilder를 사용해 최적화함
```java
String result = new StringBuilder().append(str1).append(str2).toString();
```
- 자바 9 이상에서는 StringConcatFactory를 통해 더 정교한 최적화 수행

### 3️⃣ 최적화가 어려운 경우
- 반복문 안에서 문자열 누적 시에는 최적화가 적용되지 않음
```java
String result = "";
for (int i = 0; i < 100000; i++) {
    result += "Hello Java ";
}
```
- 이 경우 매 반복마다 새로운 String 객체가 생성되어 성능 저하 + 메모리 낭비

#### 🔹 StringBuilder를 직접 사용하는 것이 좋은 경우
- 반복문에서 문자열을 계속 더할 때
- 조건문을 통해 문자열을 동적으로 조합할 때
- 문자열의 특정 부분을 수정해야 할 때
- 대용량 문자열을 다룰 때

### 📌 StringBuilder vs StringBuffer
| 항목             | StringBuilder                          | StringBuffer                           |
|------------------|----------------------------------------|-----------------------------------------|
| 동기화 여부       | ❌ 비동기 (멀티스레드 안전하지 않음)     | ✅ 동기화 지원 (멀티스레드 안전)          |
| 성능              | 빠름 (동기화 오버헤드 없음)             | 느림 (동기화 오버헤드 있음)              |
| 사용 환경         | 단일 스레드 환경                        | 멀티 스레드 환경                         |
| 기능              | append, insert, delete 등 동일         | append, insert, delete 등 동일           |
| 실무 사용 빈도     | 일반적으로 더 많이 사용됨               | 동기화가 필요한 경우에만 사용됨           |

-  지금은 StringBuffer가 있다는 것만 알아두고, 멀티스레드 학습 이후에 실제 사용 시점을 판단하면 됨.

---

## 📘 메서드 체이닝 (Method Chaining)
### 1️⃣ 개념
- 메서드 체이닝이란 메서드 호출의 결과로 **자기 자신(this)** 을 반환하여, 다음 메서드를 이어서 호출할 수 있는 방식을 말함
- 마치 메서드들이 체인처럼 연결되어 있는 구조

### 2️⃣ 기본 구조 예제
```java
public class ValueAdder {
    private int value;

    public ValueAdder add(int addValue) {
        value += addValue;
        return this; // 자기 자신 반환
    }

    public int getValue() {
        return value;
    }
}
```

- add() 메서드는 값을 누적하고 this를 반환함
- 반환된 객체를 통해 다음 메서드를 바로 호출 가능

### 3️⃣ 사용 방식 비교
#### ❌ 일반 방식 (체이닝 없음)
```java
ValueAdder adder = new ValueAdder();
adder.add(1);
adder.add(2);
adder.add(3);
int result = adder.getValue(); // result = 6
```

- 각 메서드 호출마다 객체를 따로 호출해야 함

#### 🔹 체이닝 방식
```java
ValueAdder adder = new ValueAdder();
int result = adder.add(1).add(2).add(3).getValue(); // result = 6
```

- add()가 반환한 객체에 바로 .을 찍어 다음 메서드 호출 가능
- 변수 없이도 연속 호출 가능 → 코드 간결 + 가독성 향상

### 4️⃣ 실행 흐름
```java
adder.add(1).add(2).add(3).getValue();
```

- add(1) → this 반환 → add(2) 호출
- add(2) → this 반환 → add(3) 호출
- add(3) → this 반환 → getValue() 호출
- 최종적으로 value = 6

### 📌 메서드 체이닝의 장점
| 항목             | 설명                                      | 예시 또는 효과                         |
|------------------|-------------------------------------------|----------------------------------------|
| 코드 간결성       | 변수 없이 연속 호출 가능                  | `obj.method1().method2().method3()`    |
| 가독성 향상       | 흐름이 직관적이고 자연스럽게 읽힘         | 위에서 아래로 흐르는 구조              |
| 유지보수 용이     | 중간 상태 추적 없이 한 줄로 표현 가능      | 수정 시 한 줄만 변경하면 됨            |
| 객체 설계 유연성  | API 설계 시 유창한 인터페이스 제공 가능    | 빌더 패턴, 설정 객체 등에서 활용        |
| 실무 활용 빈도     | 다양한 라이브러리와 프레임워크에서 사용됨 | `StringBuilder`, `Stream`, `Optional` 등 |

---


## 📘StringBuilder와 메서드 체이닝

### 1️⃣ 메서드 체이닝이란?
- 메서드 호출의 결과로 **자기 자신(this)** 을 반환하여 다음 메서드를 이어서 호출할 수 있는 방식
- 마치 메서드들이 체인처럼 연결되어 있는 구조

### 2️⃣ StringBuilder의 체이닝 구조
```java
public StringBuilder append(String str) {
    super.append(str);
    return this; // 자기 자신 반환
}
```

- append(), insert(), delete(), reverse() 등 대부분의 메서드가 this를 반환
- 이를 통해 연속 호출이 가능해짐

### 3️⃣ 개선된 코드 예시
```java
StringBuilder sb = new StringBuilder();
String string = sb.append("A").append("B").append("C").append("D")
                  .insert(4, "Java")
                  .delete(4, 8)
                  .reverse()
                  .toString();
System.out.println("string = " + string); // DCBA
```

- 각 메서드가 StringBuilder 객체를 반환하므로 .메서드() 방식으로 연결 가능
- 최종적으로 toString()으로 String 객체로 변환

---

### 📌 자바 문자열 문제 & 풀이 요약
#### 🔹 문제 1: startsWith()
- 목적: 문자열이 특정 접두사로 시작하는지 확인
- 핵심 메서드: startsWith("https://")
- 정답 요약:
```java
boolean result = url.startsWith("https://");
```


#### 🔹 문제 2: length()
- 목적: 배열 내 문자열 길이 출력 및 총합 계산
- 핵심 메서드: s.length()
- 정답 요약:
```java
for (String s : arr) {
    System.out.println(s + ":" + s.length());
    sum += s.length();
}
```


#### 🔹 문제 3: indexOf()
- 목적: 특정 문자열의 시작 위치 찾기
- 핵심 메서드: str.indexOf(".txt")
- 정답 요약:
```java
int index = str.indexOf(".txt");
```


#### 🔹 문제 4: substring()
- 목적: 문자열을 부분으로 나누기
- 핵심 메서드: substring(0, 5), substring(5, 9)
- 정답 요약:
```java
String filename = str.substring(0, 5);
String extName = str.substring(5);
```


#### 🔹 문제 5: indexOf() + substring()
- 목적: 확장자 위치 기준으로 파일명과 확장자 분리
- 정답 요약
```java
int extIndex = str.indexOf(ext);
String filename = str.substring(0, extIndex);
String extName = str.substring(extIndex);
```


#### 🔹 문제 6: 반복 검색 indexOf()
- 목적: 특정 키워드 등장 횟수 세기
- 정답 요약:
```java
while (index >= 0) {
    count++;
    index = str.indexOf(key, index + 1);
}
```


#### 🔹 문제 7: trim()
- 목적: 문자열 양쪽 공백 제거
- 정답 요약:
```java
String trimmed = original.trim();
```


#### 🔹 문제 8: replace()
- 목적: 특정 단어를 다른 단어로 치환
- 정답 요약:
```java
String result = input.replace("java", "jvm");
```


#### 🔹 문제 9: split()
- 목적: 이메일을 ID와 도메인으로 분리
- 정답 요약:
```java
String[] parts = email.split("@");
```


#### 🔹 문제 10: split() + join()
- 목적: 문자열 분리 후 다시 연결
- 정답 요약:
```java
String[] splitFruits = fruits.split(",");
String joinedString = String.join("->", splitFruits);
```


#### 🔹 문제 11: StringBuilder.reverse()
- 목적: 문자열 뒤집기
- 정답 요약:
```java
String reversed = new StringBuilder(str).reverse().toString();
```

---










