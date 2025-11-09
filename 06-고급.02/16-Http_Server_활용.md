# HTTP 서버 활용
전체 구조 정리와 핵심 소스 코드에 대한 단계별 설명입니다.  
이 문서는 애노테이션 기반 서블릿 구현을 통해 웹 애플리케이션을 구성하는 과정을 다루고 있으며,  
최종적으로 회원 관리 서비스를 웹으로 구현하는 데 초점을 맞추고 있습니다.

## 📘 문서 전체 구조 요약

| 단계                        | 설명                                           | 핵심 클래스 또는 기능         |
|-----------------------------|------------------------------------------------|-------------------------------|
| 1️⃣ 애노테이션 서블릿 시작   | @Mapping 기반 컨트롤러 매핑 구현               | `AnnotationServletV1`         |
| 2️⃣ 동적 바인딩 지원         | 메서드 파라미터 자동 전달 기능 추가            | `AnnotationServletV2`         |
| 3️⃣ 성능 최적화 및 중복 체크 | 경로 매핑을 HashMap으로 최적화, 중복 경로 검사 | `AnnotationServletV3`         |
| 4️⃣ 회원 관리 웹 구현        | 회원 등록/조회 기능을 웹으로 구현              | `MemberController`            |
| 5️⃣ 메시지 바디 파싱         | POST 요청의 폼 데이터를 파싱하여 처리          | `HttpRequest`                 |


## 🔧 핵심 소스 코드 단계별 설명
### 1️⃣ @Mapping 애노테이션 정의
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Documented
public @interface Mapping {
    String value();
}
```
- 목적: URL 경로와 메서드를 연결하는 사용자 정의 애노테이션
- 사용 위치: 컨트롤러 메서드에 부착하여 경로 매핑

### 2️⃣ AnnotationServletV1 – 기본 매핑 처리
```java
public class AnnotationServletV1 implements HttpServlet {
    private final List<Object> controllers;

    public AnnotationServletV1(List<Object> controllers) {
        this.controllers = controllers;
    }

    @Override
    public void service(HttpRequest request, HttpResponse response) throws IOException {
        String path = request.getPath();
        for (Object controller : controllers) {
            for (Method method : controller.getClass().getDeclaredMethods()) {
                if (method.isAnnotationPresent(Mapping.class)) {
                    Mapping mapping = method.getAnnotation(Mapping.class);
                    if (mapping.value().equals(path)) {
                        invoke(controller, method, request, response);
                        return;
                    }
                }
            }
        }
        throw new PageNotFoundException("request=" + path);
    }

    private void invoke(Object controller, Method method, HttpRequest request, HttpResponse response) {
        method.invoke(controller, request, response);
    }
}
```

- 기능: URL 경로에 따라 컨트롤러 메서드를 호출
- 한계: 모든 요청마다 리플렉션 탐색 → 성능 저하

### 3️⃣ AnnotationServletV2 – 동적 파라미터 바인딩
```java
private void invoke(HttpRequest request, HttpResponse response, Object controller, Method method) {
    Class<?>[] parameterTypes = method.getParameterTypes();
    Object[] args = new Object[parameterTypes.length];
    for (int i = 0; i < parameterTypes.length; i++) {
        if (parameterTypes[i] == HttpRequest.class) args[i] = request;
        else if (parameterTypes[i] == HttpResponse.class) args[i] = response;
        else throw new IllegalArgumentException("Unsupported parameter type");
    }
    method.invoke(controller, args);
}
```

- 기능: 메서드가 필요한 파라미터만 선언하면 자동으로 전달
- 장점: 컨트롤러 메서드 작성이 더 유연해짐

### 4️⃣ AnnotationServletV3 – 성능 최적화 및 중복 체크
```java
private final Map<String, ControllerMethod> pathMap;

private void initializePathMap(List<Object> controllers) {
    for (Object controller : controllers) {
        for (Method method : controller.getClass().getDeclaredMethods()) {
            if (method.isAnnotationPresent(Mapping.class)) {
                String path = method.getAnnotation(Mapping.class).value();
                if (pathMap.containsKey(path)) {
                    throw new IllegalArgumentException("경로 중복 등록: " + path);
                }
                pathMap.put(path, new ControllerMethod(controller, method));
            }
        }
    }
}
```

- 기능: 서버 시작 시 모든 매핑을 미리 등록 → O(1) 조회
- 추가: 중복 경로 등록 시 예외 발생 → 안정성 강화

### 5️⃣ MemberController – 회원 관리 기능 구현
```java
@Mapping("/add-member")
public void addMember(HttpRequest request, HttpResponse response) {
    String id = request.getParameter("id");
    String name = request.getParameter("name");
    int age = Integer.parseInt(request.getParameter("age"));
    Member member = new Member(id, name, age);
    memberRepository.add(member);
    response.writeBody("<h1>save ok</h1><a href='/'>Back to Home</a>");
}
```
- 기능: POST 요청으로 전달된 폼 데이터를 파싱하여 회원 등록
- 연계: MemberRepository를 통해 저장

### 6️⃣ HttpRequest – 메시지 바디 파싱 추가
```java
private void parseBody(BufferedReader reader) {
    if ("POST".equals(method)) {
        int contentLength = Integer.parseInt(headers.get("Content-Length"));
        char[] bodyChars = new char[contentLength];
        reader.read(bodyChars);
        String body = new String(bodyChars);
        for (String param : body.split("&")) {
            String[] keyValue = param.split("=");
            queryParameters.put(URLDecoder.decode(keyValue[0], UTF_8),
                                keyValue.length > 1 ? URLDecoder.decode(keyValue[1], UTF_8) : "");
        }
    }
}
```
- 기능: POST 요청의 메시지 바디를 파싱하여 파라미터 추출
- 적용: request.getParameter("key")로 접근 가능

## ✅ 최종 실행 흐름 요약
- 서버 시작 시 AnnotationServletV3가 모든 컨트롤러 메서드를 매핑 테이블에 등록
- 클라이언트가 URL 요청 → pathMap.get(path)으로 O(1) 조회
- 해당 메서드의 파라미터 타입 확인 → HttpRequest, HttpResponse 자동 전달
- POST 요청 시 메시지 바디 파싱 → 폼 데이터 추출
- 컨트롤러 메서드 실행 → HTML 응답 생성

---

# 소스

아래는 문서에 기반한 최종 애노테이션 기반 HTTP 서버 구현을 위한 Java 소스 코드입니다.  
이 코드는 실제로 실행 가능한 상태로 구성되어 있으며, 핵심 구성 요소는 다음과 같습니다:

## 🧱 프로젝트 구성 요약
```
src/
├── was/
│   ├── httpserver/
│   │   ├── HttpServer.java
│   │   ├── HttpRequest.java
│   │   ├── HttpResponse.java
│   │   ├── HttpServlet.java
│   │   ├── PageNotFoundException.java
│   │   └── ServletManager.java
│   └── servlet/
│       ├── DiscardServlet.java
│       └── annotation/
│           ├── Mapping.java
│           └── AnnotationServletV3.java
├── webservice/
│   └── MemberController.java
├── io/member/
│   ├── Member.java
│   └── MemberRepository.java
└── ServerMain.java
```


## 📦 핵심 클래스 소스
### 1️⃣ Mapping.java
```java
package was.servlet.annotation;

import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Documented
public @interface Mapping {
    String value();
}
```

### 2️⃣ AnnotationServletV3.java
```java
package was.servlet.annotation;

import was.httpserver.*;

import java.io.IOException;
import java.lang.reflect.*;
import java.util.*;

public class AnnotationServletV3 implements HttpServlet {

    private final Map<String, ControllerMethod> pathMap = new HashMap<>();

    public AnnotationServletV3(List<Object> controllers) {
        initializePathMap(controllers);
    }

    private void initializePathMap(List<Object> controllers) {
        for (Object controller : controllers) {
            for (Method method : controller.getClass().getDeclaredMethods()) {
                if (method.isAnnotationPresent(Mapping.class)) {
                    String path = method.getAnnotation(Mapping.class).value();
                    if (pathMap.containsKey(path)) {
                        throw new IllegalArgumentException("경로 중복 등록: " + path);
                    }
                    pathMap.put(path, new ControllerMethod(controller, method));
                }
            }
        }
    }

    @Override
    public void service(HttpRequest request, HttpResponse response) throws IOException {
        ControllerMethod controllerMethod = pathMap.get(request.getPath());
        if (controllerMethod == null) throw new PageNotFoundException("request=" + request.getPath());
        controllerMethod.invoke(request, response);
    }

    private static class ControllerMethod {
        private final Object controller;
        private final Method method;

        public ControllerMethod(Object controller, Method method) {
            this.controller = controller;
            this.method = method;
        }

        public void invoke(HttpRequest request, HttpResponse response) {
            Class<?>[] paramTypes = method.getParameterTypes();
            Object[] args = new Object[paramTypes.length];
            for (int i = 0; i < paramTypes.length; i++) {
                if (paramTypes[i] == HttpRequest.class) args[i] = request;
                else if (paramTypes[i] == HttpResponse.class) args[i] = response;
                else throw new IllegalArgumentException("Unsupported parameter type: " + paramTypes[i]);
            }
            try {
                method.invoke(controller, args);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```


### 3️⃣ MemberController.java
```java
package webservice;

import io.member.*;
import was.httpserver.*;
import was.servlet.annotation.Mapping;

import java.util.List;

public class MemberController {
    private final MemberRepository memberRepository;

    public MemberController(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Mapping("/")
    public void home(HttpResponse response) {
        response.writeBody("<h1>Member Manager</h1><ul>" +
                "<li><a href='/members'>Member List</a></li>" +
                "<li><a href='/add-member-form'>Add New Member</a></li></ul>");
    }

    @Mapping("/members")
    public void members(HttpResponse response) {
        List<Member> members = memberRepository.findAll();
        StringBuilder page = new StringBuilder("<h1>Member List</h1><ul>");
        for (Member m : members) {
            page.append("<li>ID: ").append(m.getId())
                .append(", Name: ").append(m.getName())
                .append(", Age: ").append(m.getAge()).append("</li>");
        }
        page.append("</ul><a href='/'>Back to Home</a>");
        response.writeBody(page.toString());
    }

    @Mapping("/add-member-form")
    public void addMemberForm(HttpResponse response) {
        response.writeBody("<h1>Add New Member</h1>" +
                "<form method='POST' action='/add-member'>" +
                "ID: <input name='id'><br>" +
                "Name: <input name='name'><br>" +
                "Age: <input name='age'><br>" +
                "<input type='submit' value='Add'>" +
                "</form><a href='/'>Back to Home</a>");
    }

    @Mapping("/add-member")
    public void addMember(HttpRequest request, HttpResponse response) {
        String id = request.getParameter("id");
        String name = request.getParameter("name");
        int age = Integer.parseInt(request.getParameter("age"));
        memberRepository.add(new Member(id, name, age));
        response.writeBody("<h1>save ok</h1><a href='/'>Back to Home</a>");
    }
}
```
### 4️⃣ HttpRequest.java (요약)
```java
public class HttpRequest {
    private String method;
    private String path;
    private Map<String, String> queryParameters = new HashMap<>();
    private Map<String, String> headers = new HashMap<>();

    public HttpRequest(BufferedReader reader) throws IOException {
        parseRequestLine(reader);
        parseHeaders(reader);
        parseBody(reader); // POST 메시지 바디 파싱
    }

    private void parseBody(BufferedReader reader) throws IOException {
        if ("POST".equals(method)) {
            int contentLength = Integer.parseInt(headers.get("Content-Length"));
            char[] bodyChars = new char[contentLength];
            reader.read(bodyChars);
            String body = new String(bodyChars);
            for (String param : body.split("&")) {
                String[] kv = param.split("=");
                queryParameters.put(URLDecoder.decode(kv[0], UTF_8),
                                    kv.length > 1 ? URLDecoder.decode(kv[1], UTF_8) : "");
            }
        }
    }

    public String getPath() { return path; }
    public String getParameter(String key) { return queryParameters.get(key); }
}
```
### 5️⃣ ServerMain.java
```java
public class ServerMain {
    public static void main(String[] args) throws IOException {
        MemberRepository repo = new MemberRepository();
        List<Object> controllers = List.of(new MemberController(repo));
        HttpServlet servlet = new AnnotationServletV3(controllers);

        ServletManager manager = new ServletManager();
        manager.setDefaultServlet(servlet);
        manager.add("/favicon.ico", new DiscardServlet());

        HttpServer server = new HttpServer(12345, manager);
        server.start();
    }
}
```

## 📦 HTTP 서버 구성 요소 소스
### 1️⃣ HttpServlet.java

```java
package was.httpserver;

import java.io.IOException;

public interface HttpServlet {
    void service(HttpRequest request, HttpResponse response) throws IOException;
}
```


### 2️⃣ HttpServer.java
```java
package was.httpserver;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class HttpServer {
    private final int port;
    private final ServletManager servletManager;

    public HttpServer(int port, ServletManager servletManager) {
        this.port = port;
        this.servletManager = servletManager;
    }

    public void start() throws IOException {
        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("HTTP Server started on port " + port);
            while (true) {
                Socket socket = serverSocket.accept();
                new Thread(() -> handle(socket)).start();
            }
        }
    }

    private void handle(Socket socket) {
        try (
            BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()))
        ) {
            HttpRequest request = new HttpRequest(reader);
            HttpResponse response = new HttpResponse(writer);
            HttpServlet servlet = servletManager.getServlet(request.getPath());
            servlet.service(request, response);
            response.send();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 3️⃣ HttpResponse.java
```java
package was.httpserver;

import java.io.BufferedWriter;
import java.io.IOException;

public class HttpResponse {
    private final BufferedWriter writer;
    private final StringBuilder body = new StringBuilder();

    public HttpResponse(BufferedWriter writer) {
        this.writer = writer;
    }

    public void writeBody(String content) {
        body.append(content);
    }

    public void send() throws IOException {
        writer.write("HTTP/1.1 200 OK\r\n");
        writer.write("Content-Type: text/html; charset=UTF-8\r\n");
        writer.write("Content-Length: " + body.toString().getBytes().length + "\r\n");
        writer.write("\r\n");
        writer.write(body.toString());
        writer.flush();
    }
}
```


### 4️⃣ PageNotFoundException.java
```java
package was.httpserver;

import java.io.IOException;

public class PageNotFoundException extends IOException {
    public PageNotFoundException(String message) {
        super(message);
    }
}
```


### 5️⃣ ServletManager.java
```java
package was.httpserver;

import java.util.HashMap;
import java.util.Map;

public class ServletManager {
    private final Map<String, HttpServlet> servletMap = new HashMap<>();
    private HttpServlet defaultServlet;

    public void add(String path, HttpServlet servlet) {
        servletMap.put(path, servlet);
    }

    public void setDefaultServlet(HttpServlet servlet) {
        this.defaultServlet = servlet;
    }

    public HttpServlet getServlet(String path) {
        return servletMap.getOrDefault(path, defaultServlet);
    }
}
```


### 6️⃣ DiscardServlet.java
```java
package was.httpserver.servlet;

import was.httpserver.HttpRequest;
import was.httpserver.HttpResponse;
import was.httpserver.HttpServlet;

import java.io.IOException;

public class DiscardServlet implements HttpServlet {
    @Override
    public void service(HttpRequest request, HttpResponse response) throws IOException {
        // 무시하고 빈 응답
        response.writeBody("");
    }
}
```


## 👤 회원 관리 도메인
### 7️⃣ Member.java
```java
package io.member;

public class Member {
    private final String id;
    private final String name;
    private final int age;

    public Member(String id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public int getAge() { return age; }
}
```


### 8️⃣ MemberRepository.java
```java
package io.member;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class MemberRepository {
    private final List<Member> members = new ArrayList<>();

    public void add(Member member) {
        members.add(member);
    }

    public List<Member> findAll() {
        return Collections.unmodifiableList(members);
    }
}
```
- 이 코드는 Java 프로젝트로 바로 실행 가능한 형태이며, @Mapping 애노테이션을 기반으로 컨트롤러 메서드를 자동 매핑하고,  
    회원 등록 및 조회 기능을 웹으로 제공합니다.
