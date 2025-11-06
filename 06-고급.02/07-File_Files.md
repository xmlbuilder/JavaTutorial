# File, Files
문서 흐름에 따라 자바의 File, Files, 경로 처리, 문자 파일 읽기/쓰기, 파일 복사까지 전체 내용을 정리.  
기능은 표로 정리하고, 샘플 코드도 함께 포함해 학습과 실무에 바로 활용할 수 있도록 구성했습니다.

## 📁 1. File 클래스
- 자바 1.0부터 제공된 클래스로, 파일과 디렉토리를 다룰 수 있습니다.

### 🔧 주요 기능 정리
| 메서드              | 설명                                 | 샘플 코드                          |
|---------------------|--------------------------------------|------------------------------------|
| `exists()`          | 파일 또는 디렉토리 존재 여부 확인     | `file.exists()`                    |
| `createNewFile()`   | 새 파일 생성                         | `file.createNewFile()`             |
| `mkdir()`           | 새 디렉토리 생성                     | `directory.mkdir()`                |
| `delete()`          | 파일 또는 디렉토리 삭제              | `file.delete()`                    |
| `isFile()`          | 파일인지 확인                        | `file.isFile()`                    |
| `isDirectory()`     | 디렉토리인지 확인                    | `directory.isDirectory()`          |
| `getName()`         | 이름 반환                            | `file.getName()`                   |
| `length()`          | 파일 크기 반환 (byte 단위)           | `file.length()`                    |
| `renameTo(File)`    | 이름 변경 또는 이동                  | `file.renameTo(newFile)`           |
| `lastModified()`    | 마지막 수정 시간 반환                | `new Date(file.lastModified())`    |


## 📂 2. Files 클래스
- 자바 1.7부터 등장한 고성능 유틸리티 클래스. Path와 함께 사용하며, File보다 편리하고 빠릅니다.

### 🔧 주요 기능 정리
| 메서드                        | 설명                                     | 샘플 코드                                           |
|-------------------------------|------------------------------------------|-----------------------------------------------------|
| `Files.exists(Path)`          | 파일 또는 디렉토리 존재 여부 확인         | `Files.exists(path)`                                |
| `Files.createFile(Path)`      | 새 파일 생성                             | `Files.createFile(path)`                            |
| `Files.createDirectory(Path)` | 새 디렉토리 생성                         | `Files.createDirectory(path)`                       |
| `Files.delete(Path)`          | 파일 또는 디렉토리 삭제                  | `Files.delete(path)`                                |
| `Files.isRegularFile(Path)`   | 일반 파일인지 확인                       | `Files.isRegularFile(path)`                         |
| `Files.isDirectory(Path)`     | 디렉토리인지 확인                        | `Files.isDirectory(path)`                           |
| `Files.getFileName(Path)`     | 파일 또는 디렉토리 이름 반환             | `path.getFileName()`                                |
| `Files.size(Path)`            | 파일 크기 반환 (byte 단위)               | `Files.size(path)`                                  |
| `Files.move(Path, Path)`      | 파일 이동 또는 이름 변경                 | `Files.move(src, dest)`                             |
| `Files.getLastModifiedTime()` | 마지막 수정 시간 반환                    | `Files.getLastModifiedTime(path)`                   |
| `Files.readAttributes()`      | 파일의 기본 속성 일괄 조회               | `Files.readAttributes(path, BasicFileAttributes.class)` |


## 📌 3. 경로 처리
### 📁 File 경로 처리
| 메서드               | 설명                                      | 샘플 코드                     |
|----------------------|-------------------------------------------|-------------------------------|
| `getPath()`          | 생성 시 입력한 경로 문자열 반환           | `file.getPath()`              |
| `getAbsolutePath()`  | 절대 경로 반환                            | `file.getAbsolutePath()`      |
| `getCanonicalPath()` | 정규 경로 반환 (경로 계산 포함)           | `file.getCanonicalPath()`     |
| `listFiles()`        | 하위 파일 및 디렉토리 목록 반환           | `file.listFiles()`            |


### 📂 Files 경로 처리
| 메서드                 | 설명                                      | 샘플 코드                     |
|------------------------|-------------------------------------------|-------------------------------|
| `Path.toAbsolutePath()`| 현재 경로를 절대 경로로 변환              | `path.toAbsolutePath()`       |
| `Path.toRealPath()`    | 정규 경로 반환 (경로 계산 포함)           | `path.toRealPath()`           |
| `Files.list(Path)`     | 디렉토리 내 파일 및 폴더 목록 스트림 반환 | `Files.list(path)`            |



## 📄 4. 문자 파일 읽기/쓰기
### ✍️ 전체 문자열 처리
| 메서드               | 설명                              | 샘플 코드                                 |
|----------------------|-----------------------------------|-------------------------------------------|
| `Files.writeString()`| 문자열을 파일에 저장               | `Files.writeString(path, text, UTF_8)`    |
| `Files.readString()` | 파일에서 전체 문자열을 읽어옴      | `Files.readString(path, UTF_8)`           |


### 📃 라인 단위 처리
| 메서드               | 설명                                               | 샘플 코드                              |
|----------------------|----------------------------------------------------|----------------------------------------|
| `Files.readAllLines()` | 모든 라인을 한 번에 읽어 `List<String>`으로 반환 | `Files.readAllLines(path, UTF_8)`      |
| `Files.lines()`        | 스트림으로 한 줄씩 읽어 메모리 사용 최소화        | `Files.lines(path, UTF_8)`             |

### 📊 readAllLines() vs lines() 차이점 요약
| 항목               | `readAllLines()`                        | `lines()`                                |
|--------------------|------------------------------------------|-------------------------------------------|
| 반환 타입          | `List<String>`                          | `Stream<String>`                          |
| 메모리 사용량      | 전체 파일을 한 번에 메모리에 로드       | 한 줄씩 처리 가능, 메모리 효율적         |
| 대용량 파일 처리   | 비효율적일 수 있음                      | 효율적 (스트리밍 방식)                    |
| 반복 처리 방식     | 일반 for문 또는 향상된 for문 사용 가능  | 스트림 API 또는 람다식 사용               |
| 사용 예            | 작은 텍스트 파일                        | 수백 MB 이상의 대용량 로그 등            |



## 📦 5. 파일 복사
| 방식                  | 설명                                           | 샘플 코드                             |
|-----------------------|------------------------------------------------|----------------------------------------|
| `readAllBytes()` + `write()` | 파일 전체를 메모리에 올려 복사             | `fos.write(fis.readAllBytes())`        |
| `transferTo()`        | 스트림 간 직접 복사 (자바 9 이상)              | `fis.transferTo(fos)`                  |
| `Files.copy()`        | OS 레벨 복사, 가장 빠름                        | `Files.copy(src, dest)`                |


## 📄 파일 복사 방식 – 추가 설명 비교
| 항목             | readAllBytes + write         | transferTo()                  | Files.copy()                     |
|------------------|-------------------------------|--------------------------------|----------------------------------|
| 자바 버전        | 자바 8 이하 가능              | 자바 9 이상                    | 자바 7 이상                      |
| 메모리 사용량    | 높음 (전체 파일 메모리 로드) | 중간 (스트림 처리)            | 낮음 (OS 복사, 메모리 사용 적음)|
| 성능             | 보통                          | 좋음                           | 매우 좋음                        |
| 코드 간결성      | 중간                          | 간결                           | 매우 간결                        |
| 사용 용도        | 파일 → 자바 → 파일            | 스트림 → 스트림                | 파일 → 파일                      |

---

# 사용법

지금까지 정리한 주요 함수들을 중심으로, 각각의 기능이 어떻게 사용되는지 샘플 코드와 함께 정리.   
아래는 기능별로 대표적인 메서드와 그 사용 예시를 함께 담은 표입니다.

## 📁 File 클래스 – 주요 메서드 사용 예시
| 메서드              | 설명                         | 샘플 코드                                      |
|---------------------|------------------------------|------------------------------------------------|
| `exists()`          | 파일 존재 여부 확인          | `File file = new File("temp/a.txt"); file.exists();` |
| `createNewFile()`   | 새 파일 생성                 | `file.createNewFile();`                        |
| `mkdir()`           | 디렉토리 생성                | `File dir = new File("temp/folder"); dir.mkdir();` |
| `delete()`          | 파일 삭제                    | `file.delete();`                               |
| `renameTo(File)`    | 파일 이름 변경               | `file.renameTo(new File("temp/b.txt"));`       |
| `length()`          | 파일 크기 확인               | `file.length();`                               |



## 📂 Files 클래스 – 주요 메서드 사용 예시
| 메서드                        | 설명                         | 샘플 코드                                      |
|-------------------------------|------------------------------|------------------------------------------------|
| `Files.exists(Path)`          | 파일 존재 여부 확인          | `Files.exists(Path.of("temp/a.txt"));`         |
| `Files.createFile(Path)`      | 새 파일 생성                 | `Files.createFile(Path.of("temp/a.txt"));`     |
| `Files.delete(Path)`          | 파일 삭제                    | `Files.delete(Path.of("temp/a.txt"));`         |
| `Files.move()`                | 파일 이동 또는 이름 변경     | `Files.move(src, dest);`                       |
| `Files.size()`                | 파일 크기 확인               | `Files.size(Path.of("temp/a.txt"));`           |
| `Files.readAttributes()`      | 파일 속성 조회               | `Files.readAttributes(path, BasicFileAttributes.class);` |


## 샘플 코드

### 📄 문자 파일 처리 – Files.writeString, Files.readString
```java
Path path = Path.of("temp/hello.txt");
Files.writeString(path, "Hello World", StandardCharsets.UTF_8);
String content = Files.readString(path, StandardCharsets.UTF_8);
System.out.println(content);
```


### 📃 라인 단위 읽기 – readAllLines, lines
```java
List<String> lines = Files.readAllLines(path, UTF_8);
for (String line : lines) {
    System.out.println(line);
}

try (Stream<String> stream = Files.lines(path, UTF_8)) {
    stream.forEach(System.out::println);
}
```


### 📦 파일 복사 – 3가지 방식
```java
// 1. readAllBytes + write
byte[] bytes = Files.readAllBytes(Path.of("temp/a.txt"));
Files.write(Path.of("temp/b.txt"), bytes);
```
```java
// 2. transferTo (Java 9+)
fis.transferTo(fos);
```
```java
// 3. Files.copy
Files.copy(Path.of("temp/a.txt"), Path.of("temp/b.txt"), StandardCopyOption.REPLACE_EXISTING);
```

### 📁 1. File 클래스 경로 처리 샘플
- File 클래스는 인스턴스를 생성한 후 다양한 경로 관련 메서드를 사용할 수 있습니다.
```java
import java.io.File;
import java.io.IOException;

public class FilePathSample {
    public static void main(String[] args) throws IOException {
        File file = new File("temp/example.txt");

        System.out.println("getPath(): " + file.getPath()); // 상대 경로
        System.out.println("getAbsolutePath(): " + file.getAbsolutePath()); // 절대 경로
        System.out.println("getCanonicalPath(): " + file.getCanonicalPath()); // 정규 경로

        File[] files = new File("temp").listFiles();
        for (File f : files) {
            System.out.println((f.isFile() ? "F" : "D") + " | " + f.getName());
        }
    }
}
```


### 🧪 2. 인스턴스 기반 메서드 샘플 (File, Scanner, BufferedReader 등)
- 자바에서 static이 아닌 메서드는 객체를 생성한 후에 호출해야 합니다.  
  아래는 대표적인 예시입니다:
#### 📌 Scanner 사용 예시
```java
import java.util.Scanner;

public class ScannerSample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in); // 인스턴스 생성
        System.out.print("이름을 입력하세요: ");
        String name = scanner.nextLine(); // 인스턴스 메서드 호출
        System.out.println("입력한 이름: " + name);
    }
}
```
#### 📌 BufferedReader 사용 예시
```java
import java.io.*;

public class BufferedReaderSample {
    public static void main(String[] args) throws IOException {
        BufferedReader reader = new BufferedReader(new FileReader("temp/data.txt"));
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println("읽은 줄: " + line);
        }
        reader.close();
    }
}
```


#### 📌 File 인스턴스 메서드 예시
```java
File file = new File("temp/sample.txt");
if (!file.exists()) {
    file.createNewFile(); // 인스턴스 메서드
}
System.out.println("파일 크기: " + file.length());
```

## ✅ 정리
- File은 오래된 방식, Files는 최신 고성능 방식
- 경로는 Path를 통해 절대/정규 경로를 명확히 처리
- 문자 파일은 Files.writeString, Files.readString으로 간단하게 처리 가능
- 대용량 파일은 Files.lines 또는 BufferedReader로 스트리밍 처리
- 파일 복사는 Files.copy가 가장 빠르고 효율적


---

