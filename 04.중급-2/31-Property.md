# getProperties
이 코드는 Java에서 System.getProperties()를 사용해 **JVM 시스템 속성(System Properties)**을 가져와 출력하는 예제.  
아래에 개념 설명과 함께 코드 분석, 주요 속성 목록까지 정리.

## 🧠 System.getProperties()란?
- Java에서 JVM이 실행될 때 설정된 **환경 정보와 시스템 속성(key-value 쌍)** 을 담고 있는 Properties 객체를 반환
- 운영체제, 사용자 정보, 파일 경로, Java 버전 등 다양한 정보를 포함
- 설정은 JVM 실행 시 -Dkey=value 옵션으로 추가 가능

## 🔍 코드 분석
```java
Properties properties = System.getProperties();
```

- 현재 JVM의 시스템 속성을 모두 가져옴
```java
for (Enumeration en = properties.propertyNames(); en.hasMoreElements();) {
    String key = (String) en.nextElement();
    String value = properties.getProperty(key);
    System.out.println(key + "=" + value);
}
```

- 모든 속성 키를 순회하며 해당 값을 출력

## 📦 주요 시스템 속성 목록

| 속성 키           | 설명 |
|-------------------|------|
| `java.version`    | 현재 JVM의 버전 (예: 17.0.8) |
| `java.vendor`     | JVM 공급자 (예: Oracle Corporation, OpenJDK) |
| `java.home`       | JVM 설치 경로 |
| `os.name`         | 운영체제 이름 (예: Windows 11, macOS, Linux) |
| `os.arch`         | 운영체제 아키텍처 (예: amd64, x86) |
| `os.version`      | 운영체제 버전 정보 |
| `user.name`       | 현재 로그인한 사용자 이름 |
| `user.home`       | 사용자 홈 디렉토리 경로 |
| `user.dir`        | 현재 작업 디렉토리 (프로그램 실행 위치) |
| `file.separator`  | 파일 경로 구분자 (`/` 또는 `\`) |
| `path.separator`  | 클래스 경로 구분자 (`:` 또는 `;`) |
| `line.separator`  | 줄바꿈 문자 (`\n`, `\r\n`) |


## 🧪 실전 활용 예시
```java
String javaVersion = System.getProperty("java.version");
String userHome = System.getProperty("user.home");
System.out.println("Java Version: " + javaVersion);
System.out.println("User Home: " + userHome);
```


## 🛠️ 커맨드라인에서 설정하는 방법
```
java -Dapp.mode=debug -Dconfig.path=/etc/app ConfigLoader
```

- System.getProperty("app.mode") → "debug"
- System.getProperty("config.path") → "/etc/app"

---

# store / laod

Java의 Properties 클래스는 시스템 속성뿐 아니라 설정 파일 저장 및 로딩에도 자주 사용됩니다.  
아래에 store()와 load() 메서드를 활용한 예제를 단계적으로 정리.

## 📦 Java Properties 파일 저장 및 로딩 예제
### ✅ 1. Properties 저장 (store())
```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Properties;

public class SaveProperties {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.setProperty("username", "junghwan");
        props.setProperty("language", "ko");
        props.setProperty("theme", "dark");

        try (FileOutputStream out = new FileOutputStream("config.properties")) {
            props.store(out, "User Preferences");
            System.out.println("설정 저장 완료");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

- store(OutputStream, comment)는 .properties 파일 형식으로 저장
- 파일 내용 예시:
```
#User Preferences
#Sat Oct 25 17:36:00 KST 2025
username=junghwan
language=ko
theme=dark
```


### ✅ 2. Properties 로딩 (load())
```java
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class LoadProperties {
    public static void main(String[] args) {
        Properties props = new Properties();

        try (FileInputStream in = new FileInputStream("config.properties")) {
            props.load(in);
            String username = props.getProperty("username");
            String theme = props.getProperty("theme");
            System.out.println("사용자: " + username);
            System.out.println("테마: " + theme);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
- load(InputStream)은 .properties 파일을 읽어 Properties 객체로 로딩
- getProperty(key)로 값 조회


## 📌 Properties 저장/로딩 요약

| 기능       | 메서드 사용 예시                          |
|------------|-------------------------------------------|
| 저장       | `props.store(new FileOutputStream(...), comment)` |
| 로딩       | `props.load(new FileInputStream(...))`    |
| 값 설정    | `props.setProperty(key, value)`           |
| 값 조회    | `props.getProperty(key)`                  |


---


