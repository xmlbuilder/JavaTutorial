# 애노테이션
자바 애노테이션의 개념부터 정의, 활용까지 샘플 중심으로 단계별 정리.

## 🧩 자바 애노테이션 완전 정리
### 1️⃣ 애노테이션이 필요한 이유
- 기존 리플렉션 방식의 한계
```java
public class ReflectController {
    public void site1(HttpRequest req, HttpResponse res) { ... }
    public void site2(HttpRequest req, HttpResponse res) { ... }
}
```
- URL과 메서드 이름이 같아야만 호출 가능
- /add-member 같은 URL은 메서드 이름으로 표현하기 어려움
- 해결책: 애노테이션으로 URL 매핑
```java
@SimpleMapping("/site1")
public void page1() { ... }

@SimpleMapping("/")
public void home() { ... }
```
- 메서드 이름과 URL을 분리 가능
- 메타데이터로 URL을 명시하고 리플렉션으로 읽어 처리

## 2️⃣ 애노테이션 정의 및 사용
- 애노테이션 정의
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface SimpleMapping {
    String value();
}
```

- @interface로 정의
- @Retention(RUNTIME) → 실행 중에도 유지되어 리플렉션으로 읽을 수 있음
- 애노테이션 사용
```java
public class TestController {
    @SimpleMapping("/")
    public void home() { ... }

    @SimpleMapping("/site1")
    public void page1() { ... }
}
```

### 3️⃣ 애노테이션 읽기 (리플렉션 - getAnnotation)
```java
for (Method method : controller.getClass().getDeclaredMethods()) {
    SimpleMapping mapping = method.getAnnotation(SimpleMapping.class);
    if (mapping != null) {
        System.out.println("[" + mapping.value() + "] -> " + method.getName());
    }
}
```
- `getAnnotation()` 으로 메서드에 붙은 애노테이션 읽기
- `value()` 로 URL 경로 추출

### 4️⃣ 애노테이션 요소와 기본값
```java
public @interface AnnoElement {
    String value();
    int count() default 0;
    String[] tags() default {};
}
```

- 사용 예시
```java
@AnnoElement(value = "data", count = 10, tags = {"t1", "t2"})
public class ElementData1 { }

@AnnoElement("data") // value 생략 가능
public class ElementData2 { }
```

### 5️⃣ 메타 애노테이션
| 애노테이션 | 주요 옵션 또는 적용 대상           | 설명                                                                 |
|----------------|-------------------------------|----------------------------------------------------------------------|
| `@Retention`   | `SOURCE`, `CLASS`, `RUNTIME`  | 애노테이션의 생존 범위 지정. `RUNTIME` 설정 시 실행 중에도 리플렉션으로 접근 가능 |
| `@Target`      | `TYPE`, `METHOD`, `FIELD` 등  | 애노테이션을 적용할 수 있는 위치 지정. 클래스, 메서드, 필드 등 다양하게 설정 가능 |
| `@Documented`  | (옵션 없음)                   | Javadoc 생성 시 애노테이션 정보를 문서에 포함시킴                        |
| `@Inherited`   | (옵션 없음)                   | 클래스 상속 시 애노테이션도 자식 클래스에 자동 적용됨 (인터페이스에는 적용 안 됨) |

- 예시
```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@Documented
@Inherited
public @interface AnnoMeta {
    String value();

}
```
    - @Retention(RUNTIME) → 실행 중에도 유지
    - @Target(TYPE, METHOD) → 클래스와 메서드에 적용 가능
    - @Documented → API 문서에 포함
    - @Inherited → 부모 클래스에 붙은 애노테이션을 자식 클래스가 자동 상속



### 6️⃣ @Inherited 예제
```java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
public @interface InheritedAnnotation { }

@InheritedAnnotation
public class Parent { }

public class Child extends Parent { }

System.out.println(Child.class.isAnnotationPresent(InheritedAnnotation.class)); // true
```
- 클래스 상속 시에만 동작
- 인터페이스 구현에는 적용되지 않음

### 7️⃣ 애노테이션 활용: 검증기 만들기
- 검증용 애노테이션 정의
```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface NotEmpty {
    String message() default "값이 비어있습니다.";
}
```
```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Range {
    int min();
    int max();
    String message() default "범위를 벗어났습니다.";
}
```

- 애노테이션 적용
```java
public class User {
    @NotEmpty(message = "이름이 비어있습니다.")
    private String name;

    @Range(min = 1, max = 100, message = "나이는 1~100 사이여야 합니다.")
    private int age;
}
```

- 검증기 구현
```java
public class Validator {
    public static void validate(Object obj) throws Exception {
        for (Field field : obj.getClass().getDeclaredFields()) {
            field.setAccessible(true);

            if (field.isAnnotationPresent(NotEmpty.class)) {
                String value = (String) field.get(obj);
                if (value == null || value.isEmpty()) {
                    throw new RuntimeException(field.getAnnotation(NotEmpty.class).message());
                }
            }

            if (field.isAnnotationPresent(Range.class)) {
                long value = field.getLong(obj);
                Range range = field.getAnnotation(Range.class);
                if (value < range.min() || value > range.max()) {
                    throw new RuntimeException(range.message());
                }
            }
        }
    }
}
```

- 사용 예시
```java
User user = new User("", 0);
Validator.validate(user); // 예외 발생: 이름이 비어있습니다.
```

### 8️⃣ 자바 기본 애노테이션
| 애노테이션         | 설명                                                                 | 사용 예시                                      |
|--------------------|----------------------------------------------------------------------|------------------------------------------------|
| `@Override`        | 부모 클래스의 메서드를 정확히 재정의했는지 컴파일러가 체크함         | `@Override public void toString() { ... }`     |
| `@Deprecated`      | 해당 요소가 더 이상 사용되지 않음을 표시. IDE나 컴파일러가 경고함     | `@Deprecated public void oldMethod() { ... }` |
| `@SuppressWarnings`| 컴파일 경고를 억제함. 예: unchecked, deprecation 등                  | `@SuppressWarnings("unchecked") List list = ...` |

- ✅ 사용 팁
    - @Override는 반드시 붙이는 습관을 들이면 실수 방지에 매우 효과적입니다.
    - @Deprecated는 API 변경 시 사용자에게 경고를 줄 수 있어 유지보수에 유용합니다.
    - @SuppressWarnings는 경고를 억제할 수 있지만, 남용은 피하고 꼭 필요한 경우에만 사용하세요.

- 예시
```java
@Override
public void toString() {
    return "Hello";
}
```

## ✅ 마무리 요약
| 분류               | 대표 애노테이션 예시                          | 설명 및 용도                                      | 비고                         |
|--------------------|-----------------------------------------------|--------------------------------------------------|------------------------------|
| 사용자 정의 애노테이션 | `@SimpleMapping("/home")`                    | URL 매핑 등 사용자 지정 메타데이터 부여           | 리플렉션 기반 서블릿에 활용  |
| 메타 애노테이션     | `@Retention(RUNTIME)`                         | 애노테이션 유지 범위 지정                         | `RUNTIME` 설정 시 리플렉션 가능 |
| 검증용 애노테이션   | `@NotEmpty`, `@Range(min=1, max=10)`         | 필드 값 유효성 검사 (빈 값, 범위 등)              | 커스텀 검증기에서 활용       |
| 자바 기본 애노테이션| `@Override`, `@Deprecated`                   | 재정의 확인, 사용 중단 경고                        | 컴파일러가 자동 체크         |


## 🧠 활용 팁
- @Retention(RUNTIME)은 리플렉션 기반 처리에 필수
- @Target을 함께 설정하면 적용 위치를 제한할 수 있음
- 검증 애노테이션은 다양한 객체에 공통 로직을 적용할 때 매우 유용
- 기본 애노테이션은 실수 방지와 유지보수에 꼭 필요한 도구

---

