# File Read / Write

## 📁 Java NIO 파일 입출력 정리

### ✅ 1. Files.readAllBytes(Path)
- 파일 전체를 바이트 배열로 읽음
- 바이너리 파일 처리에 적합
```java
byte[] bytes = Files.readAllBytes(path);
System.out.println("바이트 크기: " + bytes.length);
```

### ✅ 2. Files.readAllLines(Path)
- 파일을 한 줄씩 리스트로 읽음
- 텍스트 파일 처리에 적합
```java
List<String> lines = Files.readAllLines(path);
System.out.println("줄 수: " + lines.size());
```

### ✅ 3. Files.lines(Path, Charset)
- 파일을 스트림으로 읽음
- 대용량 파일 처리에 유리
```java
try (Stream<String> lines1 = Files.lines(path, StandardCharsets.UTF_8)) {
    lines1.forEach(System.out::println);
} catch (Exception ex) {
    ex.printStackTrace();
}
```

### ✍️ 4. 파일 쓰기 (Files.write())

#### 📌 문자열 리스트 쓰기
```java
List<String> contents = Arrays.asList("Hello", "JungHwan", "Stream API");
Path writePath = Paths.get("/Users/jeongjunghwan/Downloads/output.txt");
Files.write(writePath, contents, StandardCharsets.UTF_8);
```

#### 📌 바이트 배열 쓰기
```java
byte[] data = "Hello JungHwan!".getBytes(StandardCharsets.UTF_8);
Files.write(writePath, data);
```

#### 📌 옵션 추가 (덮어쓰기 or 이어쓰기)
```java
Files.write(writePath, contents, StandardCharsets.UTF_8, StandardOpenOption.APPEND);
```

## 🧠 파일 입출력 요약 비교

| 메서드              | 반환 타입 / 입력 타입       | 특징                          | 용도 예시                     |
|---------------------|-----------------------------|-------------------------------|-------------------------------|
| `readAllBytes()`    | `byte[]`                    | 전체 파일을 바이트로 읽음     | 이미지, 바이너리 파일         |
| `readAllLines()`    | `List<String>`              | 전체 파일을 줄 단위로 읽음    | 텍스트 파일, 설정 파일        |
| `lines()`           | `Stream<String>`            | 스트림 기반으로 줄 처리       | 대용량 텍스트 파일, 필터링    |
| `write()`           | `byte[]` 또는 `List<String>`| 파일에 내용 쓰기              | 로그 저장, 결과 출력, 파일 생성 |

## ✨ 선택 팁
- 작은 텍스트 파일 → readAllLines()
- 대용량 텍스트 파일 → lines() + 스트림 연산
- 이미지/바이너리 파일 → readAllBytes()
- 파일 저장/생성 → write() (옵션으로 APPEND, CREATE, TRUNCATE_EXISTING 등 지정 가능)

---

# FileChannel / Files.copy
아래는 Java NIO에서 제공하는 FileChannel과 **Files.copy()**에 대한 상세 설명.  
두 방식 모두 파일 입출력에 사용되지만, 목적과 성능, 유연성 면에서 차이가 있음.

## 🔌 FileChannel
### ✅ 개요
- Java NIO의 저수준 파일 입출력 채널
- RandomAccessFile, FileInputStream, FileOutputStream 등에서 얻을 수 있음
- 고성능, 대용량 파일 처리에 적합
### ✅ 주요 기능
- read(ByteBuffer) / write(ByteBuffer)
- transferTo() / transferFrom() → 파일 복사에 최적화
- position() → 읽기/쓰기 위치 제어
- truncate() → 파일 크기 자르기
### ✅ 예제: 파일 복사
```java
try (FileChannel sourceChannel = new FileInputStream("source.txt").getChannel();
     FileChannel destChannel = new FileOutputStream("dest.txt").getChannel()) {
    sourceChannel.transferTo(0, sourceChannel.size(), destChannel);
}
```
- transferTo()는 OS 수준에서 복사되므로 매우 빠름

## 📁 Files.copy()

### ✅ 개요
- Java NIO의 고수준 파일 복사 API
- 간단한 파일 복사, 스트림 복사에 적합
- Path 또는 InputStream 기반으로 사용

### ✅ 주요 기능
- Files.copy(Path source, Path target)
- Files.copy(InputStream in, Path target)
- StandardCopyOption.REPLACE_EXISTING 등 옵션 지원

### ✅ 예제: 파일 복사
```java
Path source = Paths.get("source.txt");
Path target = Paths.get("dest.txt");
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
```

## 🧠 FileChannel vs Files.copy() 비교 요약

| 항목               | `FileChannel`                          | `Files.copy()`                          |
|--------------------|----------------------------------------|-----------------------------------------|
| 수준               | 저수준 (NIO 채널)                      | 고수준 (편의 API)                       |
| 성능               | 매우 빠름 (OS 최적화)                  | 일반적인 복사 속도                      |
| 스트림 지원        | `ByteBuffer` 기반 직접 제어            | `InputStream`, `Path` 기반              |
| 위치 제어          | 가능 (`position()`, `truncate()`)      | 불가능                                  |
| 복사 방식          | `transferTo()` / `transferFrom()`      | `copy(source, target)`                  |
| 예외 처리          | 직접 try-catch 필요                    | 간단한 try-catch로 처리 가능            |
| 사용 대상          | 대용량 파일, 성능 최적화               | 일반 파일 복사, 스트림 복사             |

## ✨ 선택 팁
- 간단한 복사 → Files.copy()
- 고성능, 대용량 복사 → FileChannel.transferTo()
- 스트림 기반 복사 → Files.copy(InputStream, Path)
- 파일 일부만 복사하거나 위치 제어 → FileChannel

---

