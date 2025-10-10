# 🧠 자바 Class 요약
## 📌 개요
자바의 Class 클래스는 클래스의 메타데이터(정보)를 다루기 위한 도구로,  
실행 중인 애플리케이션에서 클래스의 구조와 동작을 동적으로 조회하고 조작할 수 있게 해줍니다.

## 🔧 주요 기능
- 타입 정보 조회: 클래스 이름, 슈퍼클래스, 인터페이스, 접근 제한자 등 확인 가능
- 리플렉션 (Reflection):
- 필드, 메서드, 생성자 조회
- 객체 생성 및 메서드 호출 가능
- 동적 로딩 및 인스턴스 생성:
- Class.forName()으로 클래스 로딩
- newInstance() 또는 getDeclaredConstructor().newInstance()로 객체 생성
- 애노테이션 처리: 클래스에 적용된 애노테이션을 조회하고 활용 가능

### 🔍 Class 객체 얻는 방법
```java
Class clazz = String.class; // 클래스에서 직접
Class clazz = new String().getClass(); // 인스턴스에서
Class clazz = Class.forName("java.lang.String"); // 문자열로
```

### 클래스 생성 코드
```java
package lang.clazz;
public class Hello {
    public String hello() {
        return "hello!";
    }
}
package lang.clazz;
public class ClassCreateMain {
    public static void main(String[] args) throws Exception {
        Class helloClass = Class.forName("lang.clazz.Hello");
        Hello hello = (Hello) helloClass.getDeclaredConstructor().newInstance();
        String result = hello.hello();
        System.out.println("result = " + result);
    }
}
```


### 🧪 예제 코드 요약
```java
Class clazz = String.class;
Field[] fields = clazz.getDeclaredFields(); // 모든 필드 출력
Method[] methods = clazz.getDeclaredMethods(); // 모든 메서드 출력
System.out.println(clazz.getSuperclass().getName()); // 슈퍼클래스
Class[] interfaces = clazz.getInterfaces(); // 인터페이스 목록


Class helloClass = Class.forName("lang.clazz.Hello");
Hello hello = (Hello) helloClass.getDeclaredConstructor().newInstance();
System.out.println("result = " + hello.hello());
```


### 🧑‍💻 용어 팁
- class는 자바 예약어이므로 변수명으로 사용할 수 없음 → 관례적으로 clazz 사용

### 🪞 리플렉션이란?
클래스의 구조를 런타임에 분석하고 조작하는 기능.  
객체 생성, 메서드 호출, 애노테이션 처리 등 다양한 동적 작업이 가능하며, 프레임워크에서 널리 활용됨.

---

# ⚙️ 자바 System 클래스 요약
## 📌 개요
System 클래스는 자바에서 시스템과 관련된 다양한 기능을 제공하는 유틸리티 클래스입니다.  
대부분의 메서드는 정적(static)으로 선언되어 있어 바로 호출할 수 있습니다.

## 🔧 주요 기능
### 🕒 시간 측정
- System.currentTimeMillis() : 현재 시간을 밀리초 단위로 반환
- System.nanoTime() : 현재 시간을 나노초 단위로 반환 (정밀한 시간 측정에 유용)
### 🌱 환경 변수
- System.getenv() : 운영체제에서 설정한 환경 변수들을 Map 형태로 반환
### 🛠️ 시스템 속성
- System.getProperties() : 시스템 전체 속성 정보를 Properties 객체로 반환
- System.getProperty(String key) : 특정 시스템 속성 값을 조회
### 📤 표준 스트림
- System.in : 표준 입력 스트림 (키보드 입력 등)
- System.out : 표준 출력 스트림 (콘솔 출력)
- System.err : 표준 오류 출력 스트림
### 🚀 프로그램 종료
- System.exit(int status) : 프로그램을 종료하며 상태 코드를 반환
- 0 : 정상 종료
- 0 이외 : 오류 또는 비정상 종료
### 📦 배열 고속 복사
- System.arraycopy(src, srcPos, dest, destPos, length) :
- 배열을 빠르게 복사하는 메서드
- 반복문보다 훨씬 빠른 성능 제공

### 🧪 예제 요약
```java

long currentTimeMillis = System.currentTimeMillis();
long currentTimeNano = System.nanoTime();

System.getenv(); // 환경 변수 출력
System.getProperties(); // 시스템 속성 출력
System.getProperty("java.version"); // 자바 버전 출력

char[] originalArray = {'h', 'e', 'l', 'l', 'o'};
char[] copiedArray = new char[5];
System.arraycopy(originalArray, 0, copiedArray, 0, originalArray.length);


System.out.println(Arrays.toString(copiedArray)); // 복사된 배열 출력

System.exit(0); // 프로그램 종료
```

---

# 🧮 자바 Math 클래스 요약
## 📌 개요
Math 클래스는 다양한 수학 연산을 위한 정적(static) 메서드를 제공하는 유틸리티 클래스입니다.  
복잡한 수학 계산을 간편하게 처리할 수 있도록 도와줍니다.

## 🔢 주요 메서드 분류
### 1. 기본 연산
- abs(x) : 절대값
- max(a, b) : 최대값
- min(a, b) : 최소값
### 2. 지수 및 로그
- exp(x) : e^x 계산
- log(x) : 자연 로그
- log10(x) : 로그 10
- pow(a, b) : a의 b 제곱
### 3. 반올림 및 정밀도
- ceil(x) : 올림 (소수점 올림)
- floor(x) : 내림 (소수점 내림)
- rint(x) : 가장 가까운 정수로 반올림 (double 반환)
- round(x) : 반올림 (long 또는 int 반환)
### 4. 삼각 함수
- sin(x) : 사인
- cos(x) : 코사인
- tan(x) : 탄젠트
### 5. 기타 유용한 기능
- sqrt(x) : 제곱근
- cbrt(x) : 세제곱근
- random() : 0.0 이상 1.0 미만의 난수(double) 반환
💡 고정밀 계산이 필요할 경우 BigDecimal 클래스 사용을 고려하세요.

### 예제 코드
```java
public class MathMain {
    public static void main(String[] args) {
        // 기본 연산 메서드
        System.out.println("max(10, 20): " + Math.max(10, 20)); //최대값
        System.out.println("min(10, 20): " + Math.min(10, 20)); //최소값
        System.out.println("abs(-10): " + Math.abs(-10)); //절대값

        // 반올림 및 정밀도 메서드
        System.out.println("ceil(2.1): " + Math.ceil(2.1)); //올림
        System.out.println("floor(2.7): " + Math.floor(2.7)); //내림
        System.out.println("round(2.5): " + Math.round(2.5)); //반올림

        // 기타 유용한 메서드
        System.out.println("sqrt(4): " + Math.sqrt(4)); //제곱근
        System.out.println("random(): " + Math.random()); //0.0 ~ 1.0 사이의 double 값
    }
}

```
### 🧪 예제 출력 예시
```
Math.max(10, 20) → 20
Math.min(10, 20) → 10
Math.abs(-10) → 10
Math.ceil(2.1) → 3.0
Math.floor(2.7) → 2.0
Math.round(2.5) → 3
Math.sqrt(4) → 2.0
Math.random() → 0.006347084592260965
```

---

# 🎲 자바 Random 클래스 요약
## 📌 개요
Random 클래스는 다양한 타입의 난수를 생성할 수 있는 클래스입니다.  
Math.random() 보다 더 정교한 제어가 가능합니다. java.util 패키지에 포함되어 있습니다.

### 🔧 주요 메서드
- nextInt() : 전체 범위의 int 난수
- nextDouble() : 0.0 이상 1.0 미만의 double 난수
- nextBoolean() : true 또는 false 반환
- nextInt(int bound) : 0 이상 bound 미만의 int 난수
- 예: nextInt(10) → 0 ~ 9
- nextInt(10) + 1 → 1 ~ 10

## 🌱 씨드(Seed) 개념
- new Random() : 내부적으로 System.nanoTime() 등을 사용해 매번 다른 난수 생성
- new Random(int seed) : 동일한 seed 값 사용 시 항상 같은 난수 시퀀스 생성
- 테스트 코드, 게임 지형 생성 등에 유용
```java
Random random = new Random(1);
System.out.println(random.nextInt()); // 항상 같은 값 출력
```
- 🎮 마인크래프트 같은 게임은 seed 값을 기반으로 동일한 지형을 생성합니다.

### 예제
```java
public class RandomMain {
    public static void main(String[] args) {

        Random random = new Random();

        //Random random = new Random(1); //seed가 같으면 Random의 결과가 같다.
        int randomInt = random.nextInt();
        System.out.println("randomInt: " + randomInt);
        double randomDouble = random.nextDouble(); //0.0d ~ 1.0d
        System.out.println("randomDouble: " + randomDouble);
        boolean randomBoolean = random.nextBoolean();
        System.out.println("randomBoolean: " + randomBoolean);

        // 범위 조회
        int randomRange1 = random.nextInt(10); //0 ~ 9까지 출력
        System.out.println("0 ~ 9: " + randomRange1);
        int randomRange2 = random.nextInt(10) + 1; //1 ~ 10까지 출력
        System.out.println("1 ~ 10: " + randomRange2);
    }
}
```

## 🧪 예제 출력 예시
```
randomInt: -1316070581
randomDouble: 0.37735342193577215
randomBoolean: false
0 ~ 9: 5
1 ~ 10: 7
```

---

# 🧪 문제와 풀이 1: Wrapper 클래스 활용
## ✅ 문제 1: parseInt()로 문자열 숫자 더하기
### 목표: 문자열 "10"과 "20"을 정수로 변환하여 합산
#### 핵심 메서드: Integer.parseInt(String)
```java
String str1 = "10";
String str2 = "20";
int num1 = Integer.parseInt(str1);
int num2 = Integer.parseInt(str2);
int sum = num1 + num2;
System.out.println("두 수의 합: " + sum); // 출력: 두 수의 합: 30
```


## ✅ 문제 2: parseDouble()로 배열의 합 구하기
### 목표: 문자열 배열 {"1.5", "2.5", "3.0"}의 합산
### 핵심 메서드: Double.parseDouble(String)
```java
String[] array = {"1.5", "2.5", "3.0"};
double sum = 0;
for (String s : array) {
    sum += Double.parseDouble(s);
}
System.out.println("sum = " + sum); // 출력: sum = 7.0
```


## ✅ 문제 3: 박싱/언박싱 (수동)
### 목표: 문자열 → Integer, Integer → int, int → Integer
#### 주의: 오토 박싱/언박싱 사용 금지
```java
String str = "100";
Integer integer1 = Integer.valueOf(str); // String → Integer
int intValue = integer1.intValue();      // Integer → int
Integer integer2 = Integer.valueOf(intValue); // int → Integer
```

#### 출력
```
integer1 = 100
intValue = 100
integer2 = 100
```


## ✅ 문제 4: 오토 박싱/언박싱
### 목표: 동일한 변환을 오토 박싱/언박싱으로 처리
```java
String str = "100";
Integer integer1 = Integer.valueOf(str); // String → Integer
int intValue = integer1;                 // 오토 언박싱
Integer integer2 = intValue;             // 오토 박싱
```

#### 출력
```java
integer1 = 100
intValue = 100
integer2 = 100
```

---

# 🎲 문제와 풀이 2: 로또 번호 자동 생성기
## ✅ 문제 설명
- 1~45 사이의 숫자 중 중복 없이 6개를 랜덤으로 추출
- 실행할 때마다 결과가 달라야 함
### 🔧 핵심 구현
- Random.nextInt(45) + 1 : 1~45 범위의 난수 생성
- isUnique() : 중복 검사
- generate() : 로또 번호 생성
```java
public int[] generate() {
    lottoNumbers = new int[6];
    count = 0;
    while (count < 6) {
        int number = random.nextInt(45) + 1;
        if (isUnique(number)) {
            lottoNumbers[count] = number;
            count++;
        }
    }
    return lottoNumbers;
}
```

#### 🧪 실행 예시
```
로또 번호: 11 19 21 35 29 16
```

#### 💡 Random 클래스는 내부적으로 seed를 사용하며, seed가 같으면 결과도 동일합니다. 

---




