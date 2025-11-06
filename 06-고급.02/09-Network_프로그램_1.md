# 📡 네트워크 - 프로그램 1 요약 및 샘플
## 1. 🔧 로깅 유틸리티 클래스
네트워크 프로그램의 흐름을 추적하기 위해 스레드명과 시간을 출력하는 로깅 클래스 사용:
```java
public abstract class MyLogger {
    private static final DateTimeFormatter formatter =
        DateTimeFormatter.ofPattern("HH:mm:ss.SSS");

    public static void log(Object obj) {
        String time = LocalTime.now().format(formatter);
        System.out.printf("%s [%9s] %s\n", time,
            Thread.currentThread().getName(), obj);
    }
}
```

## 2. 🖥️ TCP 클라이언트-서버 예제 (v1)
### ✅ 동작 흐름
- 클라이언트 → 서버: "Hello" 전송
- 서버 → 클라이언트: "Hello World!" 응답
### 📦 클라이언트 코드
```java
Socket socket = new Socket("localhost", 12345);
DataOutputStream output = new DataOutputStream(socket.getOutputStream());
DataInputStream input = new DataInputStream(socket.getInputStream());

output.writeUTF("Hello");
String response = input.readUTF();
System.out.println("서버 응답: " + response);

input.close(); 
output.close(); 
socket.close();
```

### 📦 서버 코드
```java
ServerSocket serverSocket = new ServerSocket(12345);
Socket socket = serverSocket.accept();

DataInputStream input = new DataInputStream(socket.getInputStream());
DataOutputStream output = new DataOutputStream(socket.getOutputStream());

String received = input.readUTF();
output.writeUTF(received + " World!");

input.close(); 
output.close(); 
socket.close(); 
serverSocket.close();
```


3. 🧠 핵심 개념 정리
| 개념                          | 설명                                                                 | 샘플 코드 또는 사용 예시                                      |
|-------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------|
| `localhost`                   | 자기 자신을 의미하는 호스트명 (IP: 127.0.0.1)                        | `new Socket("localhost", 12345)`                              |
| `Socket`                      | 클라이언트-서버 간 연결 객체. 데이터를 주고받는 통로                | `Socket socket = new Socket("localhost", PORT);`              |
| `ServerSocket`                | 서버가 클라이언트 요청을 수신하기 위해 여는 리스닝 소켓             | `ServerSocket serverSocket = new ServerSocket(PORT);`         |
| `DataInputStream` / `DataOutputStream` | 자바 타입으로 메시지를 주고받기 위한 보조 스트림         | `new DataInputStream(socket.getInputStream())`<br>`new DataOutputStream(socket.getOutputStream())` |
| `accept()`                    | 클라이언트 연결을 수락하고 Socket 객체를 생성 (블로킹 메서드)       | `Socket socket = serverSocket.accept();`                      |


## 4. 🔁 반복 통신 예제 (v2)
### ✅ 기능
- 클라이언트가 여러 메시지를 전송 가능
- "exit" 입력 시 종료

### 📦 클라이언트 코드 (반복)
```java
Scanner scanner = new Scanner(System.in);
while (true) {
    String toSend = scanner.nextLine();
    output.writeUTF(toSend);
    if (toSend.equals("exit")) break;
    String received = input.readUTF();
    System.out.println("서버 응답: " + received);
}
```

### 📦 서버 코드 (반복)
```java
while (true) {
    String received = input.readUTF();
    if (received.equals("exit")) break;
    output.writeUTF(received + " World!");
}
```

## 5. ⚠️ 예외 및 주의사항
| 예외/오류 이름         | 설명 및 원인                                                  | 해결 방법 또는 참고 정보                    |
|------------------------|---------------------------------------------------------------|---------------------------------------------|
| `ConnectException`     | 서버가 실행되지 않은 상태에서 클라이언트가 연결을 시도할 때 발생 | 서버를 먼저 실행한 후 클라이언트를 실행     |
| `BindException`        | 지정한 포트가 이미 다른 프로세스에 의해 사용 중일 때 발생       | 포트를 변경하거나 기존 프로세스를 종료      |
| `localhost 오류`       | OS의 hosts 파일에 `localhost`가 `127.0.0.1`으로 매핑되지 않은 경우 | `127.0.0.1 localhost`를 hosts 파일에 추가   |



## 6. 🌐 DNS 탐색 예제
```java
InetAddress localhost = InetAddress.getByName("localhost");
InetAddress google = InetAddress.getByName("google.com");

System.out.println(localhost); // localhost/127.0.0.1
System.out.println(google);    // google.com/xxx.xxx.xxx.xxx
```

## 7. 🧵 멀티 클라이언트 문제
- ServerV2는 하나의 클라이언트만 처리 가능
- 여러 클라이언트가 동시에 접속하면 backlog queue에 쌓이지만 서버는 첫 번째 클라이언트만 처리
- 해결 방법: 멀티스레드 서버 구현 필요

## ✅ 다음 단계 제안
- ServerV3: Thread 또는 ExecutorService를 사용한 멀티 클라이언트 처리
- 클라이언트별 스레드 분리로 병렬 처리 가능

---

