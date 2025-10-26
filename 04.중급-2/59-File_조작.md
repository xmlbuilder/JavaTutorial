# 🧭 Java 파일 조작 단계별 요약

## ✅ 1. File ↔ Path 상호 변환
```java
File file = new File("test.txt");
Path path = file.toPath();

Path path = Paths.get("test.txt");
File file = path.toFile();
```
- File과 Path는 서로 변환 가능
- File은 전통적인 방식, Path는 NIO 기반으로 더 유연함

## ✅ 2. 디렉토리 및 파일 생성/삭제 (File 기반)
```java
File dir = new File("경로");
dir.mkdir(); // 디렉토리 생성

File file = new File(dir, "test.txt");
file.createNewFile(); // 파일 생성

file.delete(); // 파일 삭제
dir.delete();  // 디렉토리 삭제
```
- mkdir()은 단일 디렉토리 생성
- createNewFile()은 빈 파일 생성
- delete()는 해당 파일 또는 디렉토리 삭제

## ✅ 3. 경로 구분자 확인
```java
System.out.println(File.separator); // OS별 경로 구분자 출력
```
- Windows: \
- Unix/macOS: /

## ✅ 4. FileSystem을 통한 경로 구성
```java
FileSystem fs = FileSystems.getDefault();
Path path1 = fs.getPath("/", "Users", "jeongjunghwan");
```
- FileSystem은 경로 조합을 위한 고급 API
- getPath()는 OS에 맞게 경로를 안전하게 구성

## ✅ 5. 디렉토리 및 파일 생성/삭제 (Files 기반)
```java
Path dir = Paths.get("dir");
Files.createDirectory(dir); // 디렉토리 생성

Path file = dir.resolve("test.txt");
Files.createFile(file);     // 파일 생성

Files.delete(file);         // 파일 삭제
Files.delete(dir);          // 디렉토리 삭제
```
- Files 클래스는 NIO 기반으로 더 강력하고 예외 처리 중심
- resolve()는 경로 결합에 사용

## 🧠 주요 클래스 및 메서드 요약
| 클래스/메서드              | 설명                                                         |
|----------------------------|--------------------------------------------------------------|
| `File`                     | 파일 및 디렉토리 추상화 (레거시 방식)                        |
| `Path`                     | NIO 기반 경로 표현                                           |
| `File.toPath()`            | `File` → `Path` 변환                                         |
| `Path.toFile()`            | `Path` → `File` 변환                                         |
| `File.mkdir()`             | 디렉토리 생성                                                |
| `File.createNewFile()`     | 파일 생성                                                    |
| `File.delete()`            | 파일 또는 디렉토리 삭제                                      |
| `File.separator`           | OS별 경로 구분자 (`/` 또는 `\`)                             |
| `Paths.get(...)`           | 경로 조합                                                    |
| `FileSystems.getDefault()` | 기본 파일 시스템 객체 반환                                   |
| `Files.createDirectory()`  | 디렉토리 생성 (예외 처리 중심)                              |
| `Files.createFile()`       | 파일 생성                                                    |
| `Files.delete()`           | 파일 또는 디렉토리 삭제                                      |
| `Path.resolve()`           | 경로 결합 (`dir.resolve("file.txt")`)                        |

## 📦 활용 팁
- File은 간단한 작업에 적합하지만, 예외 처리가 약함
- Path와 Files는 NIO 기반으로 성능과 안정성이 뛰어남
- resolve()는 경로 조합 시 안전하게 사용 가능
- FileSystems.getDefault()는 OS에 맞는 경로 처리에 유용

---

# 🧭 Java 파일 조작 기능 요약
## ✅ 1. 파일 복사 (Files.copy())
```java
Path source = Paths.get("source.txt");
Path target = Paths.get("target.txt");
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
```
- StandardCopyOption.REPLACE_EXISTING → 기존 파일 덮어쓰기
- 디렉토리도 복사 가능 (단, 비어 있어야 함)

## ✅ 2. 파일 이동 (Files.move())
```java
Path source = Paths.get("old.txt");
Path target = Paths.get("new.txt");
Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
```
- 파일 또는 디렉토리를 새 위치로 이동
- 이름 변경에도 사용 가능

## ✅ 3. 존재 여부 확인 (Files.exists() / Files.notExists())
```java
Path path = Paths.get("check.txt");
if (Files.exists(path)) {
    System.out.println("파일이 존재합니다.");
}
```
- exists() → 파일 또는 디렉토리가 존재하는지 확인
- notExists() → 존재하지 않음을 명확히 확인

## ✅ 4. 디렉토리 탐색 (Files.list() / Files.walk())
```java
Path dir = Paths.get("/Users/junghwan/Documents");
try (Stream<Path> stream = Files.list(dir)) {
    stream.forEach(System.out::println);
}
```
- list() → 한 단계만 탐색
- walk() → 하위 디렉토리까지 재귀적으로 탐색

## ✅ 5. 파일 속성 읽기 (BasicFileAttributes)
```java
Path path = Paths.get("sample.txt");
BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);

System.out.println("생성일: " + attrs.creationTime());
System.out.println("수정일: " + attrs.lastModifiedTime());
System.out.println("크기: " + attrs.size());
System.out.println("디렉토리인가? " + attrs.isDirectory());
```
- creationTime(), lastModifiedTime() → 시간 정보
- size() → 바이트 크기
- isDirectory(), isRegularFile() → 파일 유형 확인

## 🧠 주요 API 요약
| 기능               | 메서드/클래스                          | 설명                                      |
|--------------------|----------------------------------------|-------------------------------------------|
| 파일 복사          | `Files.copy()`                         | 파일 또는 디렉토리를 복사                 |
| 파일 이동          | `Files.move()`                         | 파일 또는 디렉토리를 이동 또는 이름 변경  |
| 존재 여부 확인     | `Files.exists()`, `Files.notExists()`  | 경로 존재 여부 확인                       |
| 디렉토리 탐색      | `Files.list()`, `Files.walk()`         | 디렉토리 내 파일 목록 탐색                |
| 파일 속성 읽기     | `Files.readAttributes()`               | 파일의 메타 정보 읽기                     |
| 속성 클래스        | `BasicFileAttributes`                  | 생성일, 수정일, 크기, 유형 등 제공        |

## 📦 활용 팁
- Files.walk()는 대용량 디렉토리 탐색 시 스트림으로 처리 가능
- StandardCopyOption은 복사/이동 시 옵션 지정에 필수
- BasicFileAttributes는 백업, 로그, 정렬 등에 유용

---

