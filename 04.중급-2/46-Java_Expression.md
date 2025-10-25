# Java Expression
Java에서 수학 표현식을 동적으로 계산하려면 exp4j 라이브러리를 사용하는 것이 가장 간단하고 강력한 방법입니다.  
ExpressionBuilder를 통해 문자열 기반 수식을 파싱하고, 변수 설정 후 evaluate()로 계산할 수 있습니다.

## 🧠 exp4j란?
- exp4j는 Java에서 수학 표현식을 문자열로 받아 파싱하고 계산할 수 있게 해주는 오픈소스 라이브러리입니다.
- 내부적으로 Dijkstra의 Shunting Yard 알고리즘을 사용해 중위 표기식을 처리합니다.

## 🔧 주요 클래스와 메서드
| 클래스/메서드           | 역할 또는 반환값               |
|-------------------------|-------------------------------|
| ExpressionBuilder       | 수식 문자열을 기반으로 Expression 객체 생성 |
| .variable("x")          | 단일 변수 선언                 |
| .variables("x", "y")    | 다중 변수 선언                 |
| .setVariable("x", 2.0)  | 변수에 값 할당                 |
| .evaluate()             | 계산 결과 반환 (`double`)       |


## ✅ 코드 예제 설명
### 1. 기본 수식 계산
```java
Expression expression = new ExpressionBuilder("2+3").build();
double result = expression.evaluate(); // 5.0
```
- 문자열 "2+3"을 수식으로 파싱하고 계산

### 2. 변수 포함 수식
```java
Expression expression1 = new ExpressionBuilder("2*x*x+3*x+0.5")
    .variable("x").build();

expression1.setVariable("x", 1.5);
double result1 = expression1.evaluate(); // 변수 x에 따라 결과 달라짐
```

- x에 값을 설정하고 수식 계산
- 반복문으로 다양한 x 값에 대해 계산 가능

#### 3. 다중 변수 수식
```java
Expression expression2 = new ExpressionBuilder("D1 + D2 * D2 - D3")
    .variables("D1", "D2", "D3").build();

expression2.setVariable("D1", 0.1);
expression2.setVariable("D2", 0.2);
expression2.setVariable("D3", 0.14);
double result2 = expression2.evaluate(); // 0.0
```

- 여러 변수에 값을 설정하고 복잡한 수식 계산 가능
- Assert.assertEquals(result2, 0.0, 0.0)으로 테스트 검증

## ✨ 고급 기능
- 사용자 정의 함수: Function 클래스를 상속해 apply() 구현
- 사용자 정의 연산자: Operator 클래스를 상속해 커스텀 연산자 구현
- 지원 함수: sin, cos, log, sqrt, abs, exp, tanh 등

## 📦 Maven 의존성
```xml
<dependency>
  <groupId>net.objecthunter</groupId>
  <artifactId>exp4j</artifactId>
  <version>0.4.8</version>
</dependency>
```

## ⚙️ Gradle 설정법 for exp4j
### ✅ 1. build.gradle에 의존성 추가
```
dependencies {
    implementation 'net.objecthunter:exp4j:0.4.8'
}
```
---
