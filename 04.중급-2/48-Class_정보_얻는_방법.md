# 🔍 Java에서 클래스 정보 얻는 방법
Java에서는 Class<?> 객체를 통해 클래스의 구조, 메서드, 필드, 생성자 등을 런타임에 탐색할 수 있음.  
이 기능은 리플렉션 API를 통해 제공됩니다.

## ✅ 주요 기능 요약
### 1. 객체로부터 클래스 정보 얻기
```java
String obj = "Sample";
Class<?> c1 = obj.getClass();
```
- getClass()는 객체의 런타임 클래스 정보를 반환
- Class<?>는 제네릭 타입으로 어떤 클래스든 받을 수 있음

### 2. 클래스 이름 출력
```java
System.out.println(c1.getSimpleName());     // 클래스 이름만
System.out.println(c1.getName());           // 전체 이름 (패키지 포함)
System.out.println(c1.getCanonicalName());  // 정규화된 이름
```


### 3. 클래스 리터럴로 직접 접근
```java
Class<?> c2 = String.class;
```

- String.class는 Class<String> 객체를 직접 참조

### 4. 생성자 호출 (동적 인스턴스 생성)
```java
String c3 = (String)c2.getDeclaredConstructor().newInstance();
```

- 기본 생성자를 통해 객체 생성
- newInstance()는 예외 처리 필요

### 5. 메서드 정보 얻기
```java
Class<?> c4 = Math.class;
Method m = c4.getMethod("sin", double.class);
System.out.println(m); // 메서드 시그니처 출력
System.out.println(m.getReturnType().getName()); // 반환 타입
```

- getMethod(name, parameterTypes...)로 공개 메서드 탐색
- 반환 타입은 getReturnType()으로 확인

### 6. 모든 메서드 탐색
```java
Method[] methodList = c4.getMethods();
for(Method method : methodList){
    System.out.println("Method Name : " + method.getName());
    for(Class<?> paramType : method.getParameterTypes()){
        System.out.println("Param Type : " + paramType.getName());
    }
}
```

- getMethods()는 public 메서드 전체 반환
- 매개변수 타입도 확인 가능

### 7. 인스턴스 메서드 호출
```java
Method method_replace = String.class.getMethod("replace", char.class, char.class);
Object returnValue = method_replace.invoke("Hello", 'l', 'x');
System.out.println(returnValue); // Hexxo
```
- invoke(target, args...)로 메서드 실행
- target은 인스턴스, args는 인자

### 8. static 메서드 호출
```java
Method method_abs = Math.class.getMethod("abs", double.class);
Object result = method_abs.invoke(null, -100.0);
System.out.println(result); // 100.0
```

- invoke(null, ...)로 static 메서드 호출

## ✨ 리플렉션 요약 표
| 기능             | 메서드 또는 구문                          | 설명                              |
|------------------|-------------------------------------------|-----------------------------------|
| 클래스 정보 얻기 | `obj.getClass()` / `String.class`         | 런타임 클래스 정보 획득           |
| 클래스 이름 출력 | `getSimpleName()`, `getName()`            | 클래스 이름 확인                  |
| 생성자 호출      | `getDeclaredConstructor().newInstance()`  | 동적 객체 생성                    |
| 메서드 탐색      | `getMethod(name, ...)` / `getMethods()`   | 메서드 정보 획득                  |
| 메서드 실행      | `method.invoke(target, args...)`          | 동적 메서드 호출                  |



## 🧠 활용 예시
- 테스트 프레임워크: JUnit이 메서드 이름으로 테스트 실행
- DI 컨테이너: Spring이 Bean을 리플렉션으로 생성하고 주입
- JSON 직렬화/역직렬화: Jackson, Gson 등이 필드/메서드 탐색에 사용

## 실전 샘플
```java
package com.thj;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ReflectionTest {
    public static void main(String[] args) throws InstantiationException, 
        IllegalAccessException, NoSuchMethodException, InvocationTargetException {
        String obj = "Sample";

        Class<?> c1 = obj.getClass();
        System.out.println(c1.getSimpleName());
        System.out.println(c1.getName());
        System.out.println(c1.getCanonicalName());

        Class<?> c2 = String.class;
        System.out.println(c2.getSimpleName());
        System.out.println(c2.getName());
        System.out.println(c2.getCanonicalName());

        Class<?> c4 = Math.class;
        Method m = c4.getMethod("sin", double.class);
        System.out.println(m);

        Class<?> returnType = m.getReturnType();
        System.out.println(returnType.getName());

        Method[] methodList = c4.getMethods();
        for(Method method : methodList){
            System.out.println("Method Name : " + method.getName());
            Class<?>[] paramTypes = method.getParameterTypes();
            for(Class<?> paramType : paramTypes){
                System.out.println("Param Type : " + paramType.getName());
            }
        }

        String str = "Hello";
        Class<?> class1 = String.class;
        Method method_replace = class1.getMethod("replace", char.class, char.class);
        Object returnValue = method_replace.invoke(str, 'l', 'x');
        System.out.println(returnValue);

        //Static Method Call
        Class<?> class2 = Math.class;
        Method method_abs = class2.getMethod("abs", double.class);
        returnValue = method_abs.invoke(null, -100.);
        System.out.println(returnValue);
    }
}
```

---

