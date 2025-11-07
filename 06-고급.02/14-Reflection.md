# Refelection

리플렉션 학습 자료를 기반으로 자바의 리플렉션 개념과 활용법을 단계별로 정리하고, 샘플 코드 중심으로 설명.

## 📚 자바 리플렉션(Reflection) 완전 정리
### 1️⃣ 리플렉션이 필요한 이유
- 기존 방식의 한계
```java
servletManager.add("/site1", new Site1Servlet());
servletManager.add("/site2", new Site2Servlet());
```
    - 기능마다 클래스를 만들고 URL에 매핑해야 함 → 번거롭고 확장성 낮음
- 리플렉션 기반 개선
```java
public class ReflectController {
    public void site1(HttpRequest req, HttpResponse res) { ... }
    public void site2(HttpRequest req, HttpResponse res) { ... }
    public void search(HttpRequest req, HttpResponse res) { ... }
}
```
    - URL과 메서드 이름을 매칭 → 리플렉션으로 메서드 탐색 및 호출 가능

### 2️⃣ 클래스 메타데이터 조회
- 방법 3가지
```java
Class<BasicData> c1 = BasicData.class;
Class<?> c2 = new BasicData().getClass();
Class<?> c3 = Class.forName("reflection.data.BasicData");
```
- .class, .getClass(), Class.forName() 모두 Class 객체 반환
    - 클래스 정보 출력
```java
System.out.println(c1.getName());           // 전체 이름
System.out.println(c1.getSimpleName());     // 단순 이름
System.out.println(c1.getPackage());        // 패키지
System.out.println(c1.getSuperclass());     // 부모 클래스
System.out.println(Arrays.toString(c1.getInterfaces())); // 인터페이스
```


### 3️⃣ 메서드 탐색과 동적 호출
- 메서드 목록 조회
```java
Method[] methods = clazz.getMethods();           // public + 상속 포함
Method[] declared = clazz.getDeclaredMethods();  // 모든 선언된 메서드
```

- 동적 호출 예시
```java
Method method = clazz.getDeclaredMethod("hello", String.class);
method.setAccessible(true); // private도 접근 가능
Object result = method.invoke(instance, "hi");
```

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
        public int sub(int a, int b) {
    return a - b;
    }
}
```
```java
public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
    Scanner scanner = new Scanner(System.in);

    System.out.print("호출 메서드: ");
    String methodName = scanner.nextLine();

    System.out.print("숫자1: ");
    int num1 = scanner.nextInt();

    System.out.print("숫자2: ");
    int num2 = scanner.nextInt();

    Calculator calculator = new Calculator();
    
    // 호출할 메서드를 변수 이름으로 동적으로 선택
    Class<? extends Calculator> aClass = Calculator.class;
    Method method = aClass.getMethod(methodName, int.class, int.class);
    Object returnValue = method.invoke(calculator, num1, num2);

    System.out.println("returnValue = " + returnValue);
}
```
- 📌 invoke()는 인스턴스와 인자를 받아 실제 메서드를 실행

### 4️⃣ 필드 탐색과 값 변경
- 필드 목록 조회
```java
Field[] fields = clazz.getFields();              // public 필드
Field[] declaredFields = clazz.getDeclaredFields(); // 모든 필드
```

- 필드 값 변경 예시
```java
Field f = clazz.getDeclaredField("name");
f.setAccessible(true);
f.set(user, "newName");
```
- private 필드도 접근 가능
- setAccessible(true)는 보안 경고 없이 내부 접근 허용

### 5️⃣ 리플렉션 활용 예제: null → 기본값
- 유틸리티 클래스
```java
public class FieldUtil {
    public static void nullFieldToDefault(Object target) throws Exception {
        for (Field f : target.getClass().getDeclaredFields()) {
            f.setAccessible(true);
            if (f.get(target) == null) {
                if (f.getType() == String.class) f.set(target, "");
                else if (f.getType() == Integer.class) f.set(target, 0);
            }
        }
    }
}
```

- 사용 예시
```java
User user = new User("id1", null, null);
Team team = new Team("team1", null);
FieldUtil.nullFieldToDefault(user);
FieldUtil.nullFieldToDefault(team);
```
- 모든 null 필드를 기본값으로 자동 설정

### 6️⃣ 생성자 탐색과 객체 생성
- 생성자 목록 조회
```java
Constructor<?>[] cons = clazz.getDeclaredConstructors();
```

- 객체 생성 예시
```java
Constructor<?> c = clazz.getDeclaredConstructor(String.class);
c.setAccessible(true);
Object instance = c.newInstance("hello");
```
- private 생성자도 호출 가능
- newInstance()로 동적 객체 생성

| 기능 구분           | 주요 메서드 또는 접근 방식                  | 샘플 코드 예시                          |
|--------------------|---------------------------------------------|----------------------------------------|
| 클래스 메타데이터   | `.class`, `.getClass()`, `Class.forName()` | `Class<?> c = Class.forName("...")`    |
| 메서드 탐색 및 호출 | `getMethod()`, `invoke()`                  | `method.invoke(obj, args...)`          |
| 필드 접근 및 수정   | `getDeclaredField()`, `set()`              | `field.set(obj, value)`                |
| 생성자 탐색 및 생성 | `getDeclaredConstructor()`, `newInstance()`| `constructor.newInstance(args...)`     |
| 필드 유틸리티       | (사용자 정의 유틸리티)                     | `FieldUtil.nullFieldToDefault(obj)`    |

### 🔍 설명 요약
- 클래스 메타데이터: 런타임에 클래스 정보를 조회하는 기본
- 메서드 탐색/호출: 이름 기반으로 메서드를 찾아 실행
- 필드 접근/수정: private 필드도 접근 가능 (setAccessible(true))
- 생성자 호출: private 생성자도 호출 가능
- 유틸리티 활용: 공통 필드 처리 로직을 리플렉션으로 일반화

리플렉션은 강력하지만 남용하면 유지보수에 악영향을 줄 수 있음.  
테스트, 프레임워크, 동적 매핑 등에서 적절히 활용하면 매우 유용합니다.


## 실전 예제
```java
public class SiteControllerV6 {
    public void site1(HttpRequest request, HttpResponse response) {
        response.writeBody("<h1>site1</h1>");
    }

    public void site2(HttpRequest request, HttpResponse response) {
        response.writeBody("<h1>site2</h1>");
    }
}
```
```java
public class SearchControllerV6 {
    public void search(HttpRequest request, HttpResponse response) {
        String query = request.getParameter("q");
        response.writeBody("<h1>Search</h1>");
        response.writeBody("<ul>");
        response.writeBody("<li>query: " + query + "</li>");
        response.writeBody("</ul>");
    }
}
```
```java
public class ReflectionServlet implements HttpServlet {

private final List<Object> controllers;

public ReflectionServlet(List<Object> controllers) {
    this.controllers = controllers;
}

@Override
public void service(HttpRequest request, HttpResponse response) throws IOException {
    
    String path = request.getPath();
    for (Object controller : controllers) {

        Class<?> aClass = controller.getClass();
        Method[] methods = aClass.getDeclaredMethods();
        
        for (Method method : methods) {
            String methodName = method.getName();
            if (path.equals("/" + methodName)) {
                invoke(controller, method, request, response);
                return;
            }
        }
    }
    throw new PageNotFoundException("request=" + path);
}

private static void invoke(Object controller, Method method, HttpRequest request, HttpResponse response) {
    try {
        method.invoke(controller, request, response);
    } catch (InvocationTargetException | IllegalAccessException e) {
        throw new RuntimeException(e);
    }
}

```
```java
public class ServerMainV6 {
    private static final int PORT = 12345;
    public static void main(String[] args) throws IOException {

        List<Object> controllers = List.of(new SiteControllerV6(), new SearchControllerV6());
        HttpServlet reflectionServlet = new ReflectionServlet(controllers);
        ServletManager servletManager = new ServletManager();
        servletManager.setDefaultServlet(reflectionServlet);
        servletManager.add("/", new HomeServlet());
        servletManager.add("/favicon.ico", new DiscardServlet());
        HttpServer server = new HttpServer(PORT, servletManager);
        server.start();
    }
}
```
---
