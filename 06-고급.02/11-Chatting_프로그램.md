# Chatting 프로그램

## 📦 전체 구조 개요
```
chat/
├── client/
│   ├── ClientMain.java
│   ├── Client.java
│   ├── ReadHandler.java
│   └── WriteHandler.java
├── server/
│   ├── ServerMain.java
│   ├── Server.java
│   ├── Session.java
│   ├── SessionManager.java
│   ├── CommandManager.java
│   ├── CommandManagerV1.java
│   └── CommandManagerV2.java
```


## 🧩 단계별 설명 및 주요 소스
## 1️⃣ 클라이언트 시작: ClientMain.java
```java
public class ClientMain {
    public static void main(String[] args) throws IOException {
        Client client = new Client("localhost", 12345);
        client.start();
    }
}
```

- 클라이언트 진입점
- 서버에 연결 후 Read/Write 핸들러 스레드 시작

## 2️⃣ 클라이언트 관리: Client.java
```java
public class Client {
    private Socket socket;
    private DataInputStream input;
    private DataOutputStream output;
    private ReadHandler readHandler;
    private WriteHandler writeHandler;

    public void start() throws IOException {
        socket = new Socket(host, port);
        input = new DataInputStream(socket.getInputStream());
        output = new DataOutputStream(socket.getOutputStream());
        readHandler = new ReadHandler(input, this);
        writeHandler = new WriteHandler(output, this);
        new Thread(readHandler).start();
        new Thread(writeHandler).start();
    }

    public synchronized void close() {
        writeHandler.close();
        readHandler.close();
        closeAll(socket, input, output);
    }
}
```

- 서버와 연결 후 입출력 스트림 생성
- ReadHandler: 서버 메시지 수신
- WriteHandler: 사용자 입력 전송

## 3️⃣ 서버 시작: ServerMain.java
```java
public class ServerMain {
    public static void main(String[] args) throws IOException {
        SessionManager sessionManager = new SessionManager();
        CommandManager commandManager = new CommandManagerV2(sessionManager);
        Server server = new Server(12345, commandManager, sessionManager);
        server.start();
    }
}
```

- 서버 진입점
- CommandManagerV2: 모든 명령어 처리
- SessionManager: 클라이언트 세션 관리

## 4️⃣ 서버 실행: Server.java
```java
public void start() throws IOException {
    serverSocket = new ServerSocket(port);
    while (true) {
        Socket socket = serverSocket.accept();
        Session session = new Session(socket, commandManager, sessionManager);
        new Thread(session).start();
    }
}
```

- 클라이언트 연결 수락
- Session 객체 생성 및 스레드 실행

## 5️⃣ 클라이언트 세션: Session.java
```java
public class Session implements Runnable {
    public void run() {
        while (true) {
            String received = input.readUTF();
            commandManager.execute(received, this);
        }
    }

    public void send(String message) throws IOException {
        output.writeUTF(message);
    }
}
```
- 클라이언트 메시지 수신
- CommandManager를 통해 처리
- send(): 클라이언트에게 메시지 전송

## 6️⃣ 세션 관리: SessionManager.java
```java
public class SessionManager {
    private final List<Session> sessions = new ArrayList<>();

    public void sendAll(String message) {
        for (Session session : sessions) {
            session.send(message);
        }
    }

    public List<String> getAllUsername() {
        return sessions.stream()
            .map(Session::getUsername)
            .filter(Objects::nonNull)
            .collect(Collectors.toList());
    }
}
```

- 세션 등록/제거
- 전체 메시지 전송
- 사용자 목록 반환

## 7️⃣ 명령어 처리: CommandManagerV2.java
```java
public void execute(String totalMessage, Session session) throws IOException {
    if (totalMessage.startsWith("/join")) {
        String username = totalMessage.split("\\|")[1];
        session.setUsername(username);
        sessionManager.sendAll(username + "님이 입장했습니다.");
    } else if (totalMessage.startsWith("/message")) {
        String message = totalMessage.split("\\|")[1];
        sessionManager.sendAll("[" + session.getUsername() + "] " + message);
    } else if (totalMessage.startsWith("/change")) {
        String newName = totalMessage.split("\\|")[1];
        sessionManager.sendAll(session.getUsername() + "님이 " + newName + "로 이름을 변경했습니다.");
        session.setUsername(newName);
    } else if (totalMessage.startsWith("/users")) {
        List<String> users = sessionManager.getAllUsername();
        session.send("전체 접속자: " + users.size() + "\n - " + String.join("\n - ", users));
    } else if (totalMessage.startsWith("/exit")) {
        throw new IOException("exit");
    } else {
        session.send("처리할 수 없는 명령어입니다: " + totalMessage);
    }
}
```

- /join, /message, /change, /users, /exit 명령어 처리
- 사용자 이름 등록 및 변경
- 전체 사용자 목록 출력

## 8️⃣ 클라이언트 입력 처리: WriteHandler.java
```java
public void run() {
    String username = inputUsername(scanner);
    output.writeUTF("/join|" + username);
    while (true) {
        String toSend = scanner.nextLine();
        if (toSend.equals("/exit")) {
            output.writeUTF(toSend);
            break;
        } else if (toSend.startsWith("/")) {
            output.writeUTF(toSend);
        } else {
            output.writeUTF("/message|" + toSend);
        }
    }
}
```

- 사용자 이름 입력 후 /join 전송
- 명령어 또는 일반 메시지 구분하여 전송

## 9️⃣ 클라이언트 수신 처리: ReadHandler.java
```java
public void run() {
    while (true) {
        String received = input.readUTF();
        System.out.println(received);
    }
}
```

- 서버로부터 메시지 수신 후 콘솔 출력

## UML 흐름도
```mermaid
@startuml
title Chat Program UML Diagram

interface CommandManager {
    +execute(totalMessage: String, session: Session): void
}

class CommandManagerV1 {
    -sessionManager: SessionManager
    +execute(totalMessage: String, session: Session): void
}

class CommandManagerV2 {
    -sessionManager: SessionManager
    +execute(totalMessage: String, session: Session): void
}

class Session {
    -socket: Socket
    -input: DataInputStream
    -output: DataOutputStream
    -commandManager: CommandManager
    -sessionManager: SessionManager
    -username: String
    +run(): void
    +send(message: String): void
    +getUsername(): String
    +setUsername(name: String): void
    +close(): void
}

class SessionManager {
    -sessions: List<Session>
    +add(session: Session): void
    +remove(session: Session): void
    +sendAll(message: String): void
    +getAllUsername(): List<String>
    +closeAll(): void
}

class Server {
    -port: int
    -commandManager: CommandManager
    -sessionManager: SessionManager
    -serverSocket: ServerSocket
    +start(): void
}

class ServerMain {
    +main(args: String[]): void
}

class Client {
    -socket: Socket
    -input: DataInputStream
    -output: DataOutputStream
    -readHandler: ReadHandler
    -writeHandler: WriteHandler
    +start(): void
    +close(): void
}

class ClientMain {
    +main(args: String[]): void
}

class ReadHandler {
    -input: DataInputStream
    -client: Client
    +run(): void
    +close(): void
}

class WriteHandler {
    -output: DataOutputStream
    -client: Client
    +run(): void
    +close(): void
}

CommandManager <|.. CommandManagerV1
CommandManager <|.. CommandManagerV2

ServerMain --> Server
Server --> SessionManager
Server --> CommandManager
Server --> Session

Session --> CommandManager
Session --> SessionManager

ClientMain --> Client
Client --> ReadHandler
Client --> WriteHandler

ReadHandler --> Client
WriteHandler --> Client

@enduml
```

## ✅ 실행 흐름 요약
```
[ClientMain] → Client → ReadHandler + WriteHandler
[ServerMain] → Server → Session → CommandManagerV2
```

- 클라이언트는 이름을 입력하고 서버에 /join으로 접속
- 서버는 세션을 등록하고 메시지를 전체에 전송
- 클라이언트는 메시지를 /message|내용 형식으로 전송
- 서버는 [이름] 내용 형식으로 전체에 전달
- /change, /users, /exit 등 명령어도 처리
