# 🧵 네트워크 - 프로그램2 요약 및 Thread 기반 구조 설명
## 1. 🔧 자원 정리 유틸리티 (SocketCloseUtil)
- InputStream, OutputStream, Socket을 안전하게 닫기 위한 유틸리티
- 닫는 순서: 스트림 → 소켓
- 예외 처리: 각 자원별로 try-catch로 예외를 개별 처리
```java
public static void closeAll(Socket socket, InputStream input, OutputStream output) {
    close(input);
    close(output);
    close(socket);
}
```

## 2. 🖥️ TCP 클라이언트-서버 예제 (v4)
### ✅ 기능
- 클라이언트가 메시지를 반복 전송
- "exit" 입력 시 종료
- 자원 정리를 finally에서 수행

### 📦 클라이언트 코드 핵심
```java
try {
    socket = new Socket("localhost", PORT);
    input = new DataInputStream(socket.getInputStream());
    output = new DataOutputStream(socket.getOutputStream());

    while (true) {
        String toSend = scanner.nextLine();
        output.writeUTF(toSend);
        if (toSend.equals("exit")) break;
        String received = input.readUTF();
    }
} finally {
    closeAll(socket, input, output);
}
```


## 3. 🧵 Thread 기반 서버 구조 (v4)
### ✅ 구조 개요
- 서버는 ServerSocket으로 클라이언트 연결을 수락
- 각 클라이언트 연결은 SessionV4 객체로 처리
- SessionV4는 Runnable을 구현하여 Thread로 실행
### 📦 서버 코드 핵심
```java
while (true) {
    Socket socket = serverSocket.accept(); // 블로킹
    SessionV4 session = new SessionV4(socket);
    Thread thread = new Thread(session);
    thread.start(); // 클라이언트별 스레드 실행
}
```

### 📦 SessionV4 클래스 핵심
```java
public class SessionV4 implements Runnable {
    public void run() {
        try {
            while (true) {
                String received = input.readUTF();
                if (received.equals("exit")) break;
                output.writeUTF(received + " World!");
            }
        } finally {
            closeAll(socket, input, output);
        }
    }
}
```

### 🧠 Thread 동작 요약
| 구성 요소              | 역할 설명                                                                 |
|------------------------|---------------------------------------------------------------------------|
| `main`                 | 서버 소켓을 열고 클라이언트 연결을 수락 (`accept()` 호출)               |
| `Thread-0`, `Thread-1` | 각 클라이언트 연결을 처리하는 독립적인 스레드 (세션별로 생성됨)         |
| `Runnable`             | 스레드 실행 로직을 정의하는 인터페이스 (`run()` 메서드 구현)            |
| `SessionV4.run()`      | 클라이언트와 메시지를 주고받고, `"exit"` 시 연결 종료 및 자원 정리 수행 |

## 4. ✅ 자원 정리 방식 비교
| 방식                    | 설명                                                                 | 특징 및 사용 예시                                      |
|-------------------------|----------------------------------------------------------------------|--------------------------------------------------------|
| `finally + closeAll()`  | 자원을 명시적으로 닫기 위해 `finally` 블록에서 유틸리티 메서드 호출 | 예외 발생 여부와 관계없이 모든 자원을 직접 정리 가능 |
| `try-with-resources`    | 자원을 선언과 동시에 자동으로 정리하는 구조                         | `AutoCloseable` 구현 객체에만 사용 가능               |

### 📌 코드 비교 예시
#### finally + closeAll() 방식
```java
Socket socket = null;
DataInputStream input = null;
DataOutputStream output = null;
try {
    socket = new Socket("localhost", PORT);
    input = new DataInputStream(socket.getInputStream());
    output = new DataOutputStream(socket.getOutputStream());
    // 통신 처리
} finally {
    SocketCloseUtil.closeAll(socket, input, output);
}
```

#### try-with-resources 방식
```java
try (
    Socket socket = new Socket("localhost", PORT);
    DataInputStream input = new DataInputStream(socket.getInputStream());
    DataOutputStream output = new DataOutputStream(socket.getOutputStream())
) {
    // 통신 처리
} catch (IOException e) {
    log(e);
}
```

## 5. 🧵 Thread 기반 서버 확장 (v6)
### ✅ 기능
- 여러 클라이언트 처리
- 서버 종료 시 모든 세션 자원 정리
- 셧다운 훅(Shutdown Hook) 사용

### 📦 SessionManagerV6 전체 소스
#### Client
```java
public class ClientV6 {
    public static final int PORT = 12345;

    public static void main(String[] args) {

        log("클라이언트 시작");

        try(Socket socket = new Socket("localhost", PORT);
            DataInputStream input = new DataInputStream(socket.getInputStream());
            DataOutputStream output = new DataOutputStream(socket.getOutputStream())
        ){
            log("소캣 연결: " + socket);

            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.print("전송 문자: ");
                String toSend = scanner.nextLine();

                // 서버에게 문자 보내기
                output.writeUTF(toSend);
                log("client -> server: " + toSend);
                if (toSend.equals("exit")) {
                    break;
                }

                // 서버로부터 문자 받기
                String received = input.readUTF();
                log("client <- server: " + received);
            }
        } catch (IOException e) {
            log(e);
        }
    }
}
```
#### SessionManager
```java
public class SessionManagerV6 {
    private List<SessionV6> sessions = new ArrayList<>();

    public synchronized void add(SessionV6 session) {
        sessions.add(session);
    }

    public synchronized void remove(SessionV6 session) {
        sessions.remove(session);
    }

    public synchronized void closeAll() {
        for (SessionV6 session : sessions) {
            session.close();
        }
        sessions.clear();
    }
}
```
#### Server Thread
```java
public class SessionV6 implements Runnable {
    private final Socket socket;
    private final DataInputStream input;
    private final DataOutputStream output;
    private final SessionManagerV6 sessionManager;
    private boolean closed = false;

    public SessionV6(Socket socket, SessionManagerV6 sessionManager) throws IOException 
    {
        this.socket = socket;
        this.input = new DataInputStream(socket.getInputStream());
        this.output = new DataOutputStream(socket.getOutputStream());
        this.sessionManager = sessionManager;
        this.sessionManager.add(this);
    }

    @Override
    public void run() {
        try {
            while (true) {

                // 클라이언트로부터 문자 받기
                String received = input.readUTF();
                log("client -> server: " + received);
                if (received.equals("exit")) {
                    break;
                }

                // 클라이언트에게 문자 보내기
                String toSend = received + " World!";
                output.writeUTF(toSend);
                log("client <- server: " + toSend);
            }
        } catch (IOException e) {
            log(e);
        } finally {
            sessionManager.remove(this);
            close();
        }
    }

    // 세션 종료시, 서버 종료시 동시에 호출될 수 있다.
    public synchronized void close() {
        if (closed) {
            return;
        }
        
        closeAll(socket, input, output);
        closed = true;
        log("연결 종료: " + socket);
    }
}
```
#### Server Main
```java
public class ServerV6 {
    private static final int PORT = 12345;

    public static void main(String[] args) throws IOException {
        log("서버 시작");
        SessionManagerV6 sessionManager = new SessionManagerV6();
        ServerSocket serverSocket = new ServerSocket(PORT);
        log("서버 소켓 시작 - 리스닝 포트: " + PORT);

        // ShutdownHook 등록
        ShutdownHook shutdownHook = new ShutdownHook(serverSocket, sessionManager);
        Runtime.getRuntime().addShutdownHook(new Thread(shutdownHook, "shutdown"));

        try {
            while (true) {
                Socket socket = serverSocket.accept(); // 블로킹
                log("소켓 연결: " + socket);

                SessionV6 session = new SessionV6(socket, sessionManager);
                Thread thread = new Thread(session);
                thread.start();
            }
        } catch (IOException e) {
            log("서버 소캣 종료: " + e);
        }
    }

    static class ShutdownHook implements Runnable {
        private final ServerSocket serverSocket;
        private final SessionManagerV6 sessionManager;
        public ShutdownHook(ServerSocket serverSocket, SessionManagerV6 sessionManager) {
            this.serverSocket = serverSocket;
            this.sessionManager = sessionManager;
        }

        @Override
        public void run() {
            log("shutdownHook 실행");
            try {
                sessionManager.closeAll();
                serverSocket.close();
                Thread.sleep(1000); // 자원 정리 대기
            } catch (Exception e) {
                e.printStackTrace();
                System.out.println("e = " + e);
            }
        }
    }
}
```

### 📦 SessionManagerV6 요약
```java
public synchronized void closeAll() {
    for (SessionV6 session : sessions) {
        session.close();
    }
    sessions.clear();
}
```

### 📦 ShutdownHook 등록
```java
Runtime.getRuntime().addShutdownHook(new Thread(new ShutdownHook(serverSocket, sessionManager)));
```
### 📦 SessionV6 자원 정리
```java
public synchronized void close() {
    if (closed) return;
    closeAll(socket, input, output);
    closed = true;
}
```

## 6. 🧠 Thread 구조 요약 (v6)
| 스레드 이름     | 역할 설명                                                                 |
|----------------|---------------------------------------------------------------------------|
| `main`         | 서버 소켓을 열고 클라이언트 연결을 수락 (`accept()` 호출)                 |
| `Thread-N`     | 클라이언트별 세션 처리 (`SessionV6.run()` 실행)                            |
| `shutdown`     | 자바 프로세스 종료 시 셧다운 훅 실행 → 모든 세션과 서버 소켓 자원 정리 수행 |


## 7. ⚠️ 예외 처리 및 OS 차이
| 예외 이름                          | 발생 상황 및 의미                                                    | 특징 및 OS 차이점                         |
|-----------------------------------|-----------------------------------------------------------------------|-------------------------------------------|
| `EOFException`                    | 클라이언트가 **정상적으로 연결을 종료**했을 때 서버가 입력을 읽으려 할 경우 | 주로 macOS에서 발생                       |
| `SocketException: Connection reset` | 클라이언트가 **강제로 연결을 종료**했을 때 서버가 입력을 읽으려 할 경우     | 주로 Windows에서 발생                     |

## 📌 추가 설명
- 두 예외 모두 IOException의 하위 클래스이며, 서버에서 input.readUTF() 같은 입력 대기 중 발생
- 서버는 예외를 통해 클라이언트의 연결 종료를 감지하고 자원 정리를 수행
- SessionV6에서는 finally 블록에서 close() 호출로 안전하게 자원 해제

## ✅ 마무리
- Thread 기반 서버는 클라이언트별로 독립적인 처리를 가능하게 함
- 자원 정리는 finally, try-with-resources, ShutdownHook으로 구성
- 서버 종료 시 모든 세션을 안전하게 정리하는 구조까지 완성됨

---

# Runtime.getRuntime().addShutdownHook
Runtime.getRuntime().addShutdownHook(Thread hook)는 자바에서 프로세스가 종료되기 직전에 실행할 작업을 등록할 수 있는 메서드입니다.  
이를 통해 네트워크 자원 정리, 로그 기록, 파일 저장 등 종료 시점의 후처리 작업을 안전하게 수행할 수 있습니다.

## 🧠 개념 요약
| 항목               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 셧다운 훅 (Shutdown Hook) | 자바 프로세스 종료 직전에 실행되는 후처리 작업을 등록하는 기능             |
| 실행 조건          | 정상 종료 시 작동 (Ctrl+C, IntelliJ Stop, kill 등 / kill -9 제외)   |
| 사용 목적          | 자원 정리, 로그 기록, 세션 종료 등 종료 직전 필요한 작업 수행         |
| 등록 방식          | `Runtime.getRuntime().addShutdownHook(new Thread(() -> { ... }))` |


## ✅ 코드 예시
```java
Runtime.getRuntime().addShutdownHook(new Thread(() -> {
    System.out.println("Shutdown hook 실행: 자원 정리 중...");
    // 예: 서버 소켓 닫기, 세션 종료, 로그 기록
    serverSocket.close();
    sessionManager.closeAll();
}));
```

## 📦 네트워크 서버에서의 활용 예시
```java
ShutdownHook shutdownHook = new ShutdownHook(serverSocket, sessionManager);
Runtime.getRuntime().addShutdownHook(new Thread(shutdownHook, "shutdown"));
```
- ShutdownHook 클래스는 Runnable을 구현
- 서버 종료 시 run() 메서드가 호출되어 모든 세션과 서버 소켓을 정리

## 🔁 실행 흐름 요약
- 서버 실행 중
- 사용자가 종료 신호 (Ctrl+C, Stop 버튼 등) 발생
- JVM이 ShutdownHook 스레드를 실행
- 등록된 자원 정리 로직 수행
- JVM 종료

이 기능은 네트워크 서버뿐 아니라 파일 처리, DB 연결, 백그라운드 작업 등 다양한 곳에서 유용하게 활용됩니다.

