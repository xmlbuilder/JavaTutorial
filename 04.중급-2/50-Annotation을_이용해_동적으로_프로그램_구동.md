# 커스텀 어노테이션 과 리플렉션
Java에서 **커스텀 어노테이션(@ArgData)과 리플렉션(Reflection)** 을 활용해  
프로그램을 동적으로 구동하는 구조를 보여주는 예제.  
아래에 전체 흐름을 단계별로 정리하고 관련 개념을 설명.

## 전체 코드
```java

package com.thj;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface ArgData {
    String[] value();
}

package com.thj;
import java.util.ArrayList;
import com.thj.ArgData;
public class SampleCode1 {
    
    public SampleCode1(){
    }
    
    public boolean execute(){
        System.out.println("SampleCode1");
        return true;
    }

    @ArgData({
            "METHOD1",
            "METHOD2",
    })
    public void TestData1(ArrayList<String> args){
        System.out.println("SAMPLE");
    }
}

Reflections reflections = new Reflections("com.thj", new SubTypesScanner(false));
Set<Class> sets = reflections.getSubTypesOf(Object.class)
        .stream()
        .collect(Collectors.toSet());

for(Class cls : sets){
    String strClsName = cls.getName();
    if(strClsName.startsWith("com.thj.Sample")){
        Constructor<?> constructor = cls.getConstructor(null);
        SampleCode1 sampleCode1 = (SampleCode1) constructor.newInstance();
        Method[] methods = cls.getMethods();
        for (Method method : methods ){

            //execute 호출
            if(method.getName().contains("execute")){
                method.invoke(sampleCode1, null);
            }
            else{

                ArgData annotation = method.getAnnotation(ArgData.class);
                if(annotation == null) continue;

                //TestData1 이 호출
                String[] arguments = annotation.value();
                if(arguments != null){
                    ArrayList<String> lists  = new ArrayList<>( Arrays.asList(arguments) );
                    method.invoke(sampleCode1, lists);
                }

            }
        }
    }
}

```

## 🧭 전체 흐름 요약
이 프로그램은 다음과 같은 순서로 동작합니다:
- @ArgData라는 커스텀 어노테이션 정의
- 어노테이션이 붙은 메서드를 포함한 클래스 작성 (SampleCode1)
- Reflections 라이브러리를 사용해 클래스 탐색
- 해당 클래스의 인스턴스를 생성
- execute() 메서드 실행
- @ArgData가 붙은 메서드를 찾아 인자와 함께 실행

## 🔧 단계별 설명

### ✅ 1. 커스텀 어노테이션 정의
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface ArgData {
    String[] value();
}
```
- @Retention(RetentionPolicy.RUNTIME) → 런타임에 어노테이션 정보 유지
- value() → 문자열 배열을 인자로 받는 어노테이션

### ✅ 2. 어노테이션 사용 클래스 작성
```java
public class SampleCode1 {
    public boolean execute() {
        System.out.println("SampleCode1");
        return true;
    }

    @ArgData({"METHOD1", "METHOD2"})
    public void TestData1(ArrayList<String> args) {
        System.out.println("SAMPLE");
    }
}
```
- execute()는 일반 메서드
- TestData1()은 @ArgData 어노테이션을 통해 메타데이터를 포함

### ✅ 3. Reflections로 클래스 탐색
```java
Reflections reflections = new Reflections("com.thj", new SubTypesScanner(false));
Set<Class> sets = reflections.getSubTypesOf(Object.class);
```
- "com.thj" 패키지 내의 모든 클래스 탐색
- SubTypesScanner(false)는 모든 하위 타입을 포함

### ✅ 4. 클래스 필터링 및 인스턴스 생성
```java
if (strClsName.startsWith("com.thj.Sample")) {
    Constructor<?> constructor = cls.getConstructor(null);
    SampleCode1 sampleCode1 = (SampleCode1) constructor.newInstance();
```
- 클래스 이름이 "com.thj.Sample"로 시작하는 경우만 처리
- 기본 생성자를 통해 인스턴스 생성

### ✅ 5. 메서드 탐색 및 실행
```java
Method[] methods = cls.getMethods();
for (Method method : methods) {
    if (method.getName().contains("execute")) {
        method.invoke(sampleCode1, null);
    }

```
- execute() 메서드는 인자 없이 실행

### ✅ 6. 어노테이션 기반 메서드 실행
```java
ArgData annotation = method.getAnnotation(ArgData.class);
if (annotation != null) {
    String[] arguments = annotation.value();
    ArrayList<String> lists = new ArrayList<>(Arrays.asList(arguments));
    method.invoke(sampleCode1, lists);
}
```
- @ArgData가 붙은 메서드만 실행
- 어노테이션의 value()를 인자로 변환해 메서드에 전달

| 개념              | 설명                                                                 |
|-------------------|----------------------------------------------------------------------|
| `@interface`      | Java에서 커스텀 어노테이션을 정의할 때 사용하는 키워드               |
| `@Retention`      | 어노테이션의 유지 범위를 지정 (`SOURCE`, `CLASS`, `RUNTIME`)         |
| `Reflections`     | 런타임에 클래스, 메서드, 어노테이션 등을 탐색할 수 있는 라이브러리   |
| `getAnnotation()` | 특정 메서드나 클래스에 붙은 어노테이션 정보를 런타임에 가져오는 메서드 |
| `invoke()`        | 리플렉션을 통해 메서드를 동적으로 실행하는 메서드                    |

## ✨ 예시 흐름
- @interface ArgData → 어노테이션 정의
- @Retention(RetentionPolicy.RUNTIME) → 런타임에 어노테이션 정보 유지
- Reflections → SampleCode1 클래스 탐색
- getAnnotation(ArgData.class) → 어노테이션 정보 획득
- invoke() → 해당 메서드 실행

## 📦 Gradle 의존성 (Reflections)
```
dependencies {
    implementation 'org.reflections:reflections:0.10.2'
}
```


## ✨ 활용 예시
- 플러그인 시스템: 어노테이션으로 기능을 자동 등록
- 테스트 프레임워크: @Test 어노테이션 기반 자동 실행
- 커맨드 처리기: @Command("name")으로 명령 매핑

----

# method.invoke(sampleCode1, lists); 
이 코드가 어떻게 TestData1() 메서드를 실행하게 되는지, 그 내부 흐름을 단계별로 풀어서 설명.

## 🔍 핵심 흐름: method.invoke(sampleCode1, lists);
이 한 줄은 다음과 같은 과정을 거쳐 SampleCode1 클래스의 TestData1() 메서드를 실행합니다.

## 🧭 단계별 설명
### ✅ 1. 클래스 탐색
```java
Reflections reflections = new Reflections("com.thj", new SubTypesScanner(false));
Set<Class> sets = reflections.getSubTypesOf(Object.class);
```
- com.thj 패키지에서 모든 클래스를 탐색
- SampleCode1도 포함됨

### ✅ 2. 클래스 필터링 및 인스턴스 생성
```java
if (strClsName.startsWith("com.thj.Sample")) {
    Constructor<?> constructor = cls.getConstructor(null);
    SampleCode1 sampleCode1 = (SampleCode1) constructor.newInstance();
```
- 클래스 이름이 "com.thj.Sample"로 시작하면
- 기본 생성자를 통해 SampleCode1 객체 생성

### ✅ 3. 메서드 목록 탐색
```java
Method[] methods = cls.getMethods();
```
- SampleCode1 클래스의 모든 public 메서드를 가져옴
- execute()와 TestData1() 포함

### ✅ 4. 어노테이션 확인
```java
ArgData annotation = method.getAnnotation(ArgData.class);
String[] arguments = annotation.value();
```
- TestData1() 메서드에 붙은 @ArgData 어노테이션을 확인
- value()로부터 {"METHOD1", "METHOD2"} 배열 획득

###  ✅ 5. 인자 준비
```java
ArrayList<String> lists = new ArrayList<>(Arrays.asList(arguments));
```
- 어노테이션에서 받은 문자열 배열을 ArrayList<String>로 변환
- TestData1()의 매개변수 타입과 일치시킴

### ✅ 6. 메서드 실행
```java
method.invoke(sampleCode1, lists);
```
- method는 현재 TestData1() 메서드 객체
- sampleCode1은 실행 대상 인스턴스
- lists는 인자로 전달됨
- ➡️ 결과적으로 TestData1(ArrayList<String> args) 메서드가 실행되며 "SAMPLE"이 출력됨

## 🧠 핵심 요약
| 단계 | 설명                                           |
|------|------------------------------------------------|
| 1    | 클래스 탐색 및 필터링 (`SampleCode1`)          |
| 2    | 인스턴스 생성 (`newInstance()`)                |
| 3    | 메서드 탐색 및 어노테이션 확인                 |
| 4    | 어노테이션 값 → 인자 변환 (`ArrayList`)        |
| 5    | `invoke()`로 메서드 실행 (`TestData1`)         |

### ✨ 실행 결과 예시
```
SampleCode1
SAMPLE
```
- execute() → "SampleCode1"
- TestData1() → "SAMPLE"

---

