# DataOutputStream / DataInputStream
DataOutputStream과 DataInputStream은 자바에서 기본형 데이터 타입을 바이너리 형식으로 읽고 쓰기 위한 스트림 클래스입니다.  
아래에 표로 정리하고, 주요 메서드와 함께 간단한 설명.

## 📦 DataStream 클래스 요약
## DataOutputStream / DataInputStream 요약

| 클래스명           | 주요 메서드                          | 설명 |
|--------------------|--------------------------------------|------|
| DataOutputStream   | writeBoolean(), writeInt(), writeUTF() | 기본형 데이터를 바이너리로 출력 |
| DataInputStream    | readBoolean(), readInt(), readUTF()   | 바이너리로 저장된 기본형 데이터를 읽음 |

## 전체 코드
```java
import java.io.*;

public class DataStreamTest {

    public static void main(String[] args){

        try(DataOutputStream dos = new DataOutputStream(new FileOutputStream("data.bin"))){
            dos.writeBoolean(true);
            dos.write(5);
            dos.writeByte((byte)5);
            dos.writeInt(100);
            dos.writeDouble(200.5);
            dos.writeUTF("Hi., Hyangseon");
        }
        catch (IOException e){
            e.printStackTrace();
        }

        try(DataInputStream dos = new DataInputStream(new FileInputStream("data.bin"))){
            boolean b = dos.readBoolean();
            int b2 = dos.read();
            byte b3 = dos.readByte();
            int i = dos.readInt();
            double d = dos.readDouble();
            String s = dos.readUTF();
            System.out.println(b);
            System.out.println(b2);
            System.out.println(b3);
            System.out.println(i);
            System.out.println(d);
            System.out.println(s);
        }
        catch (IOException e){
            e.printStackTrace();
        }
    }
}
```

## 🧪 주요 메서드 설명 및 예시
### ✅ writeBoolean(boolean v) / readBoolean()
```java
dos.writeBoolean(true);       // true 저장
boolean b = dis.readBoolean(); // true 읽기
```

### ✅ write(int v) / read()
```java
dos.write(5);                 // 1바이트로 저장 (writeByte와 유사)
int b2 = dis.read();          // 1바이트 읽기 (0~255 범위)
```

### ✅ writeByte(byte v) / readByte()
```java
dos.writeByte((byte)5);       // 바이트 단위 저장
byte b3 = dis.readByte();     // 바이트 단위 읽기
```

### ✅ writeInt(int v) / readInt()
```java
dos.writeInt(100);            // 4바이트 정수 저장
int i = dis.readInt();        // 4바이트 정수 읽기
```

### ✅ writeDouble(double v) / readDouble()
```java
dos.writeDouble(200.5);       // 8바이트 실수 저장
double d = dis.readDouble();  // 8바이트 실수 읽기
```

### ✅ writeUTF(String s) / readUTF()
```java
dos.writeUTF("Hi., Hyangseon"); // UTF-8 문자열 저장
String s = dis.readUTF();       // UTF-8 문자열 읽기
```


## 📌 장점 요약
- 기본형 타입을 정확하게 저장/복원 가능
- 플랫폼 독립적인 바이너리 포맷
- 네트워크 전송, 파일 저장에 적합

## 🧠 주의할 점
- 쓰기 순서와 읽기 순서가 반드시 일치해야 함
- write()와 writeByte()는 다르게 동작할 수 있음 → write()는 int지만 1바이트만 저장

----
# 객체 직렬화나 네트워크 통신

DataStream은 기본형 타입을 바이너리로 처리하는 데 특화되어 있지만, 객체 직렬화나 네트워크 통신에도 활용할 수 있음.  
아래에 두 가지 예제 정리:

## 🧱 1. 객체 직렬화 예제 (DataOutputStream 기반)
이 방식은 Serializable을 쓰는 ObjectOutputStream과는 다르게, 직접 필드를 하나씩 바이너리로 저장하는 방식.

## ✅ 예제 클래스
```java
class Person {
    String name;
    int age;

    public Person(String name, int age){
        this.name = name;
        this.age = age;
    }
}
```

### ✅ 저장 (직렬화)
```java
try(DataOutputStream dos = new DataOutputStream(new FileOutputStream("person.bin"))){
    Person p = new Person("JungHwan", 30);
    dos.writeUTF(p.name);
    dos.writeInt(p.age);
}
```

### ✅ 읽기 (역직렬화)
```java
try(DataInputStream dis = new DataInputStream(new FileInputStream("person.bin"))){
    String name = dis.readUTF();
    int age = dis.readInt();
    Person p = new Person(name, age);
    System.out.println(p.name + ", " + p.age);
}
```


## 🌐 2. 네트워크 통신 예제 (Socket + DataStream)
클라이언트와 서버가 DataInputStream / DataOutputStream을 통해 기본형 데이터를 주고받는 구조입니다.

## ✅ 서버 코드
```java
import java.io.*;
import java.net.*;

public class DataServer {
    public static void main(String[] args) throws IOException {
        ServerSocket server = new ServerSocket(9999);
        Socket socket = server.accept();

        DataInputStream dis = new DataInputStream(socket.getInputStream());
        String msg = dis.readUTF();
        System.out.println("클라이언트로부터 받은 메시지: " + msg);

        DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
        dos.writeUTF("서버 응답: 안녕 JungHwan!");
        
        dis.close();
        dos.close();
        socket.close();
        server.close();
    }
}
```

## ✅ 클라이언트 코드
```java
import java.io.*;
import java.net.*;

public class DataClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("localhost", 9999);

        DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
        dos.writeUTF("클라이언트 메시지: 안녕하세요!");

        DataInputStream dis = new DataInputStream(socket.getInputStream());
        String response = dis.readUTF();
        System.out.println("서버 응답: " + response);

        dos.close();
        dis.close();
        socket.close();
    }
}
```


## 📌 DataStream 활용 요약

| 활용 분야         | 설명                                 | 관련 메서드 / 클래스 |
|------------------|--------------------------------------|-----------------------|
| 객체 직렬화      | 객체의 필드를 직접 바이너리로 저장/복원 | writeUTF(), writeInt(), readUTF(), readInt() |
| 네트워크 통신    | Socket을 통해 기본형 데이터를 주고받음 | Socket, DataOutputStream, DataInputStream |
| 저장 순서 중요   | 쓰기와 읽기 순서가 반드시 일치해야 함 | write → read 순서 일치 필수 |
| 텍스트보다 효율적 | 바이너리 전송으로 속도와 정확도 향상     | 모든 기본형 write/read 메서드 |
| 플랫폼 독립성    | 자바 내부 포맷으로 다양한 환경에서 호환 | UTF, int, double 등 포맷 일관성 |


## 🔄 올바른 데코레이터 흐름 예시
```java
ObjectOutputStream oos = new ObjectOutputStream(
    new BufferedOutputStream(
        new FileOutputStream("object.bin")
    )
);
```

- FileOutputStream: 파일에 바이트 쓰기
- BufferedOutputStream: 버퍼링으로 성능 향상
- ObjectOutputStream: 객체 직렬화

## 🧱 왜 DataOutputStream 위에 ObjectOutputStream을 바로 씌우면 안 될까?
- DataOutputStream은 내부적으로 기본형 타입을 바이너리로 쓰는 데 특화되어 있음
- ObjectOutputStream은 자체적인 헤더와 직렬화 포맷을 사용함
- DataOutputStream 위에 ObjectOutputStream을 얹으면 데이터 포맷이 충돌하거나 깨질 수 있음


## ✅ 가능 조합 요약

| 조합 구조                             | 가능 여부 | 설명 | 추천 여부 |
|--------------------------------------|-----------|------|------------|
| BufferedOutputStream → DataOutputStream | ✅        | 버퍼링 + 기본형 데이터 출력 | ✅ |
| BufferedOutputStream → ObjectOutputStream | ✅        | 버퍼링 + 객체 직렬화 | ✅ |
| DataOutputStream → BufferedOutputStream | ❌        | 구조상 비정상적이며 기능 중복 | ❌ |
| DataOutputStream → ObjectOutputStream | ❌        | 포맷 충돌 가능성 있음 | ❌ |



## 🧪 추천 구조 예시
```java
try (ObjectOutputStream oos = new ObjectOutputStream(
         new BufferedOutputStream(
             new FileOutputStream("person.obj")))) {
    oos.writeObject(new Person("JungHwan", 30));
}
```

## 📦 객체 직렬화 & 역직렬화 흐름 요약

| 단계 | 클래스 | 설명 |
|------|--------|------|
| 1. 객체 저장 | ObjectOutputStream | 객체를 바이너리 형태로 직렬화하여 저장 |
| 2. 버퍼링 | BufferedOutputStream | 성능 향상을 위해 버퍼링 처리 |
| 3. 파일 출력 | FileOutputStream | 파일에 바이트 단위로 저장 |
| 4. 객체 읽기 | ObjectInputStream | 저장된 객체를 역직렬화하여 복원 |
| 5. 버퍼링 | BufferedInputStream | 성능 향상을 위해 버퍼링 처리 |
| 6. 파일 입력 | FileInputStream | 파일에서 바이트 단위로 읽기 |



## 🧪 전체 예제 코드
### ✅ 직렬화 (저장)
```java
import java.io.*;

class Person implements Serializable {
    String name;
    int age;

    public Person(String name, int age){
        this.name = name;
        this.age = age;
    }
}

public class SerializeExample {
    public static void main(String[] args) {
        try (ObjectOutputStream oos = new ObjectOutputStream(
                 new BufferedOutputStream(
                     new FileOutputStream("person.obj")))) {
            Person p = new Person("JungHwan", 30);
            oos.writeObject(p);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```


### ✅ 역직렬화 (읽기)
```java
import java.io.*;

public class DeserializeExample {
    public static void main(String[] args) {
        try (ObjectInputStream ois = new ObjectInputStream(
                 new BufferedInputStream(
                     new FileInputStream("person.obj")))) {
            Person p = (Person) ois.readObject();
            System.out.println(p.name + ", " + p.age);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

## 📌 주의사항
- Person 클래스는 반드시 Serializable 인터페이스를 구현해야 함
- 직렬화된 클래스의 버전 관리를 위해 serialVersionUID를 명시하는 것이 좋음
- 저장된 객체는 직렬화 당시의 클래스 구조와 일치해야 역직렬화가 성공함
