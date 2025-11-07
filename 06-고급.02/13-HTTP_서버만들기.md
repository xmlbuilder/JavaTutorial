# HTTP 서버 만들기

문서는 자바로 직접 HTTP 서버를 구현하는 과정을 단계별로 설명하고 있습니다.  
아래에 전체 흐름을 단계별 요약, 각 단계의 핵심 소스 설명, 그리고 최종 소스 통합 정리로 구성.

## 🧭 전체 흐름 요약
| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| Hello World              | 기본 응답 메시지. `/` 요청 시 `<h1>Hello World</h1>` HTML 반환       |
| /site1/search            | URL 경로 기반 기능 분기. `/site1`, `/search?q=hello` 등 처리 가능    |
| HttpRequest              | 요청 메시지를 파싱해 구조화한 객체. 메서드, 경로, 쿼리, 헤더 관리     |
| URLDecoder / URLEncoder | 퍼센트 인코딩 처리. 한글 등 비-ASCII 문자를 안전하게 URL에 포함       |

## ✅ 기능 흐름 예시
- 클라이언트가 /search?q=가나다 요청
- 서버는 HttpRequest 객체로 요청 파싱
- q=가나다는 %EA%B0%80%EB%82%98%EB%8B%A4로 인코딩됨
- 서버는 URLDecoder.decode()로 디코딩하여 "가나다" 획득
- 응답 HTML에 검색어 출력

### 🧱 1단계: 기본 HTTP 서버 (HttpServerV1)
```java
public class HttpServerV1 {
    public void start() throws IOException {
        ServerSocket serverSocket = new ServerSocket(port);
        while (true) {
            Socket socket = serverSocket.accept();
            process(socket);
        }
    }

    private void process(Socket socket) throws IOException {
        BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream(), UTF_8));
        PrintWriter writer = new PrintWriter(socket.getOutputStream(), false, UTF_8);
        String request = requestToString(reader);
        responseToClient(writer);
    }

    private void responseToClient(PrintWriter writer) {
        String body = "<h1>Hello World</h1>";
        writer.println("HTTP/1.1 200 OK");
        writer.println("Content-Type: text/html");
        writer.println("Content-Length: " + body.getBytes(UTF_8).length);
        writer.println();
        writer.println(body);
        writer.flush();
    }
}
```
- 단일 요청 처리
- 요청 메시지 출력 및 간단한 HTML 응답

### 🧵 2단계: 동시 요청 처리
```java
public class HttpServerV2 {
    private final ExecutorService es = Executors.newFixedThreadPool(10);
    public void start() throws IOException {
        ServerSocket serverSocket = new ServerSocket(port);
        while (true) {
            Socket socket = serverSocket.accept();
            es.submit(new HttpRequestHandlerV2(socket));
        }
    }
}
```
- 스레드 풀을 사용해 최대 10개의 요청을 동시에 처리
- 각 요청은 HttpRequestHandlerV2에서 처리

### 🌐 3단계: URL 경로별 기능 분기
```java
if (requestString.startsWith("GET /site1")) {
    site1(writer);
} else if (requestString.startsWith("GET /search")) {
    search(writer, requestString);
} else if (requestString.startsWith("GET /")) {
    home(writer);
} else {
    notFound(writer);
}
```
- /site1, /site2, /search?q=hello, / 등 경로에 따라 다른 응답
- search()는 쿼리 파라미터를 파싱해 검색어를 출력

### 🧾 4단계: 요청 메시지 구조화 (HttpRequest 클래스)
```java
public class HttpRequest {
    private String method;
    private String path;
    private Map<String, String> queryParameters;
    private Map<String, String> headers;

    public HttpRequest(BufferedReader reader) throws IOException {
        parseRequestLine(reader);
        parseHeaders(reader);
    }

    private void parseRequestLine(BufferedReader reader) {
        String[] parts = reader.readLine().split(" ");
        method = parts[0];
        path = parts[1].split("\\?")[0];
        // query 파싱
    }
}
```

- 요청 메시지를 객체로 구조화
- 메서드, 경로, 쿼리 파라미터, 헤더를 분리해 관리

###  🔤 5단계: URL 인코딩/디코딩 (PercentEncodingMain)
```java
String encode = URLEncoder.encode("가", UTF_8); // → %EA%B0%80
String decode = URLDecoder.decode(encode, UTF_8); // → 가
```
- 한글 등 비-ASCII 문자를 안전하게 URL에 포함시키기 위한 인코딩
- 서버에서 디코딩하여 원래 문자열 복원

### 📦 최종 소스 통합 구조
```
was/
├── v1/
│   ├── HttpServerV1.java
│   └── ServerMainV1.java
├── v2/
│   ├── HttpServerV2.java
│   ├── HttpRequestHandlerV2.java
│   └── ServerMainV2.java
├── v3/
│   ├── HttpServerV3.java
│   ├── HttpRequestHandlerV3.java
│   ├── ServerMainV3.java
│   └── PercentEncodingMain.java
├── httpserver/
│   └── HttpRequest.java
```

## ✅ 실행 흐름 요약
```
[ServerMainV3] → HttpServerV3 → HttpRequestHandlerV3 → HttpRequest
```

- 클라이언트 요청을 스레드로 처리
- 요청 메시지를 파싱해 HttpRequest 객체로 구조화
- 경로에 따라 응답 분기
- URL 인코딩된 검색어를 디코딩해 출력


## ✅ HTTP 통신의 본질
| 구성 요소       | 설명                                                                 |
|----------------|----------------------------------------------------------------------|
| Socket 기반     | HTTP는 TCP 소켓 위에서 동작하는 애플리케이션 계층 프로토콜입니다.       |
| Request/Response | 클라이언트가 요청(Request)을 보내고, 서버가 응답(Response)을 반환합니다. |
| Header          | 요청 또는 응답의 메타 정보 (예: Content-Type, Host, User-Agent 등)     |
| Body (Content)  | 실제 전송되는 데이터 (예: HTML, JSON, 이미지, 파일 등)                  |



## ✅ 핵심 요약
- HTTP는 문자 기반의 메시지 통신이며, 모든 메시지는 Header + Body 구조로 구성됩니다.
- 클라이언트와 서버는 소켓을 통해 연결되고, HTTP 메시지를 주고받습니다.
- Header는 전송 정보, Body는 실제 콘텐츠입니다.

## 📦 메시지 구조 요약
### 요청 메시지 (Request)
```
GET /search?q=hello HTTP/1.1
Host: www.example.com
User-Agent: Chrome/...
Accept: text/html

(요청 바디는 POST 등에서 사용)
```

### 응답 메시지 (Response)
```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 20

<h1>Hello World</h1>
```

## 🔧 핵심 개념 정리
- HTTP는 소켓 통신을 통해 문자열 메시지를 주고받는 프로토콜
- 메시지는 항상 Header + Body 구조
- 서버는 요청을 받고, 응답 메시지를 만들어 클라이언트에 전달
- 자바에서는 Socket, BufferedReader, PrintWriter 등을 사용해 직접 구현 가능

## ✅ 모든 HTTP 통신은 결국 Socket 기반입니다
| 구성 요소           | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| TCP 소켓 연결       | 클라이언트와 서버는 TCP 소켓을 통해 연결되어 데이터를 주고받습니다.     |
| 요청/응답 메시지    | HTTP는 요청(Request)과 응답(Response) 메시지를 주고받는 구조입니다.     |
| 메시지 형식         | 각 메시지는 Header(메타 정보) + Body(실제 콘텐츠)로 구성됩니다.         |
| 문자 기반 프로토콜  | HTTP는 사람이 읽을 수 있는 텍스트 형식으로 메시지를 구성합니다.         |


## 🔍 예시 비교
| 언어       | 구현 방식 예시                                      | 설명                                                    |
|------------|-----------------------------------------------------|---------------------------------------------------------|
| Java       | `Socket` → `HttpURLConnection` → `HttpClient`       | 저수준 소켓부터 고수준 HTTP 클라이언트까지 계층적으로 제공 |
| Python     | `socket` → `http.client` → `requests`               | 기본 소켓으로 직접 구현 가능, `requests`는 고수준 라이브러리 |
| JavaScript | (브라우저) `fetch` / `XMLHttpRequest`               | 내부적으로 TCP 소켓 사용, 개발자는 HTTP API만 접근 가능 |
| Go         | `net.Conn` → `http.Server` / `http.Client`          | 소켓과 HTTP 서버/클라이언트가 표준 라이브러리로 제공됨     |
| C#         | `TcpClient` → `HttpClient`                          | TCP 소켓으로 직접 구현 가능, `HttpClient`는 고수준 API     |


## 🧠 핵심 요약
- HTTP는 문자 기반의 프로토콜이지만, 실제 전송은 바이트 스트림으로 이루어집니다.
- 이 바이트 스트림은 TCP 소켓을 통해 전달됩니다.
- 즉, 모든 HTTP 라이브러리나 프레임워크는 결국 소켓을 열고, 메시지를 읽고 쓰는 구조입니다.

---

# C 언어 소켓 통신
C 언어는 네트워크 프로그래밍의 기초 중의 기초로, 당연히 소켓 통신이 가능합니다.  
사실상 오늘날 대부분의 고수준 언어들이 사용하는 소켓 API는 C의 BSD 소켓 라이브러리를 기반으로 만들어졌어요.

## ✅ C 언어의 소켓 통신 개요
| 구성 요소         | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| socket()         | 소켓 생성. 통신의 출발점이 되는 파일 디스크립터 생성                   |
| bind()           | 서버 소켓에 IP 주소와 포트를 할당                                     |
| listen()         | 클라이언트의 연결 요청을 대기 상태로 설정                             |
| accept()         | 클라이언트 연결 수락. 새로운 소켓 반환                                |
| connect()        | 클라이언트가 서버에 연결 요청                                          |
| send() / write() | 데이터를 전송                                                          |
| recv() / read()  | 데이터를 수신                                                          |

## 🔧 C로 HTTP 서버를 만들면?
- 직접 TCP 소켓을 열고 recv()로 요청 메시지를 받고
- 문자열 파싱으로 HTTP 헤더와 바디를 분리하고
- send()로 응답 메시지를 구성해 전송합니다
즉, 자바에서 Socket을 쓰는 것처럼, C에서도 소켓을 직접 다루며 HTTP 메시지를 수작업으로 처리합니다.

## 🧠 핵심 요약
- C는 소켓 통신의 원형 언어라 할 수 있어요
- HTTP는 결국 문자열 기반의 프로토콜이므로, C에서도 충분히 구현 가능
- 다만 문자열 처리와 메모리 관리는 자바보다 더 세심한 주의가 필요합니다


### 🖥️ 서버 코드 (server.c)
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <netinet/in.h>

#define PORT 12345

int main() {
    int server_fd, client_fd;
    struct sockaddr_in address;
    char buffer[1024] = {0};

    server_fd = socket(AF_INET, SOCK_STREAM, 0);
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));
    listen(server_fd, 1);

    printf("서버 대기 중...\n");
    socklen_t addrlen = sizeof(address);
    client_fd = accept(server_fd, (struct sockaddr*)&address, &addrlen);

    read(client_fd, buffer, 1024);
    printf("클라이언트로부터 받은 메시지: %s\n", buffer);

    char *response = "서버 응답: Hello Client!";
    send(client_fd, response, strlen(response), 0);

    close(client_fd);
    close(server_fd);
    return 0;
}
```
### 💻 클라이언트 코드 (client.c)
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

#define PORT 12345

int main() {
    int sock = socket(AF_INET, SOCK_STREAM, 0);
    struct sockaddr_in server_addr;
    char buffer[1024] = {0};

    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(PORT);
    inet_pton(AF_INET, "127.0.0.1", &server_addr.sin_addr);

    connect(sock, (struct sockaddr*)&server_addr, sizeof(server_addr));

    char *message = "클라이언트 메시지: Hello Server!";
    send(sock, message, strlen(message), 0);

    read(sock, buffer, 1024);
    printf("서버로부터 받은 응답: %s\n", buffer);

    close(sock);
    return 0;
}
```

## 🧪 실행 방법
- 두 파일을 각각 server.c, client.c로 저장
- 컴파일:
```
gcc server.c -o server
gcc client.c -o client
```
- 실행:
- 터미널 1: ./server
- 터미널 2: ./client

---

