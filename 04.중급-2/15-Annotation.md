# Annotation
주신 예제들은 Java의 Annotation(애너테이션) 기능을 아주 잘 보여주는 샘플들입니다.  
아래에 각 예제를 단계별로 자세히 설명하고, 마지막에는 실전에서 유용한 고급 예제도 하나 추가.

## 📘 1. 커스텀 애너테이션 + 배열 애너테이션
### 🔹 코드 요약
```java
@Retention(RetentionPolicy.RUNTIME)
@interface Author {
    String value();
}

@Retention(RetentionPolicy.RUNTIME)
@interface Authors {
    Author[] value();
}

@Authors({
    @Author("jhjeong"),
    @Author("hyangseon")
})
public class AnnotationSample1 {
    public static void main(String[] args) {
        Author[] authors = AnnotationSample1.class.getAnnotation(Authors.class).value();
        for (Author author : authors) {
            System.out.println(author.value());
        }
    }
}
```

### 🔹 단계별 설명
- @Retention(RetentionPolicy.RUNTIME)
    - 런타임에 애너테이션 정보를 읽을 수 있도록 설정.
- @interface Author
    - value()라는 하나의 속성을 가진 사용자 정의 애너테이션.
- @interface Authors
    - Author[] 배열을 속성으로 가지는 복합 애너테이션.
- @Authors(...)
    - 클래스에 여러 명의 작성자를 지정.
- getAnnotation(Authors.class)
    - 리플렉션을 통해 애너테이션 정보를 읽고 출력.

## 📘 2. @Inherited 애너테이션의 상속 여부
### 🔹 코드 요약
```java
@Inherited
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface InheritedAnnotationType {}

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface UnInheritedAnnotationType {}

@UnInheritedAnnotationType
class A {}

@InheritedAnnotationType
class B extends A {}

class C extends B {}
```

### 🔹 단계별 설명
- @Inherited
    - 해당 애너테이션이 자식 클래스에 상속될 수 있도록 허용.
- UnInheritedAnnotationType은 @Inherited가 없기 때문에 상속되지 않음.
- getClass().getAnnotation(...)
    - 클래스에 직접 선언된 애너테이션만 가져옴.
- 결과:
    - A: UnInheritedAnnotationType O, InheritedAnnotationType X
    - B: InheritedAnnotationType O
    - C: InheritedAnnotationType O (상속됨)

## 📘 3. 메서드에 애너테이션 + 디폴트 값
### 🔹 코드 요약
```java
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotationRunTime {
    String key() default "foo";
    String value() default "bar";
}


@MyAnnotationRunTime
public void testDefaults() { ... }

@MyAnnotationRunTime(key="sample", value="models")
public void testValues() { ... }
```

### 🔹 단계별 설명
- @Retention(RetentionPolicy.RUNTIME)
    - 런타임에 메서드 애너테이션을 읽을 수 있도록 설정.
- default 키워드
    - 애너테이션 속성에 기본값을 지정.
- getMethod(...) + getAnnotation(...)
    - 리플렉션으로 메서드에 선언된 애너테이션을 읽음.
- 결과:
    - testDefaults() → "foo", "bar"
    - testValues() → "sample", "models"

## 💡 실전 예제 추가: 권한 체크 애너테이션
### 🔹 목적
웹 애플리케이션에서 메서드 접근 권한을 지정하는 데 사용.
### 🔹 코드 예시
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface RequiresRole {
    String value(); // 예: "ADMIN", "USER"
}


public class UserService {

    @RequiresRole("ADMIN")
    public void deleteUser(String userId) {
        System.out.println("Deleting user: " + userId);
    }
}


public class SecurityInterceptor {
    public static void checkAccess(Object obj, String methodName, String currentRole) throws Exception {
        Method method = obj.getClass().getMethod(methodName, String.class);
        RequiresRole role = method.getAnnotation(RequiresRole.class);
        if (role != null && !role.value().equals(currentRole)) {
            throw new SecurityException("Access denied: role " + currentRole + " insufficient");
        }
        method.invoke(obj, "user123");
    }

    public static void main(String[] args) {
        try {
            checkAccess(new UserService(), "deleteUser", "USER"); // 예외 발생
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 🔹 실전 활용 포인트
- 보안, 로깅, 트랜잭션, API 버전 관리 등 다양한 분야에 응용 가능.
- 스프링 프레임워크에서도 @Transactional, @PreAuthorize 등으로 활용됨.

---

