# File 구분
Java에서 경로가 파일인지 디렉토리인지 구분하는 방법을 보여주는 예제.  
아래에 전체 흐름을 단계별로 설명하고, 관련 메서드를 표로 정리.

## 🧭 파일 vs 디렉토리 구분 단계별 설명
### ✅ 1. File 객체 생성
```java
File file = new File("/Users/jeongjunghwan");
```
- 경로를 기반으로 File 객체 생성
- 실제 파일이 존재하지 않아도 객체는 생성됨

### ✅ 2. 경로 존재 여부 확인
```java
file.exists();
```

- 해당 경로가 실제로 존재하는지 확인
- 존재하지 않으면 이후의 isFile()이나 isDirectory()는 의미 없음

### ✅ 3. 파일인지 확인
```java
file.isFile();
```

- 경로가 일반 파일이면 true
- 디렉토리나 존재하지 않는 경로면 false

### ✅ 4. 디렉토리인지 확인
```java
file.isDirectory();
```

- 경로가 디렉토리이면 true
- 일반 파일이나 존재하지 않는 경로면 false

## 🧠 주요 메서드 요약
| 메서드               | 설명                                      |
|----------------------|-------------------------------------------|
| `File.exists()`      | 경로가 실제로 존재하는지 확인             |
| `File.isFile()`      | 경로가 일반 파일인지 확인                 |
| `File.isDirectory()` | 경로가 디렉토리인지 확인                  |
| `File.getAbsolutePath()` | 절대 경로 반환                         |


## 📦 활용 팁
- exists()는 항상 먼저 호출해서 경로 유효성 확인
- isFile()과 isDirectory()는 상호 배타적일 수 있음
- getAbsolutePath()는 로그 출력이나 경로 디버깅에 유용


---

# 파일 속성
Java NIO와 기존 File API를 활용해 파일의 유형, 속성, 접근 권한 등을 확인하는 방법을 아래에 체계적으로 정리.

## 🧭 Java 파일 속성 및 상태 확인 요약
### ✅ 1. NIO 기반 파일 유형 확인
```java
Path path = Paths.get("sample.txt");

Files.isRegularFile(path);   // 일반 파일인지 확인
Files.isDirectory(path);     // 디렉토리인지 확인
Files.isSymbolicLink(path);  // 심볼릭 링크인지 확인
```
- Files.isRegularFile() → 일반 파일인지 확인
- Files.isDirectory() → 디렉토리인지 확인
- Files.isSymbolicLink() → 심볼릭 링크인지 확인

### ✅ 2. 숨김 파일 여부 확인
```java
Files.isHidden(path); // 숨김 파일 여부 확인
```
- 숨김 속성이 있는 파일이면 true
- OS에 따라 .파일명 또는 파일 속성으로 판단

### ✅ 3. 읽기/쓰기 권한 확인
```java
Files.isReadable(path);   // 읽기 가능 여부
Files.isWritable(path);   // 쓰기 가능 여부
Files.isExecutable(path); // 실행 가능 여부
```
- 권한에 따라 true/false 반환
- 권한이 없거나 파일이 없으면 false

### ✅ 4. 기존 File API와 비교
```java
File file = new File("sample.txt");

file.isFile();        // 일반 파일인지
file.isDirectory();   // 디렉토리인지
file.isHidden();      // 숨김 파일인지
file.canRead();       // 읽기 가능 여부
file.canWrite();      // 쓰기 가능 여부
file.canExecute();    // 실행 가능 여부
```

- File 클래스는 간단한 속성 확인에 적합
- Files는 예외 기반 처리와 더 많은 기능 제공

## 🧠 주요 메서드 비교 요약
| 기능             | NIO (`Files`)                  | 전통 방식 (`File`)         |
|------------------|--------------------------------|-----------------------------|
| 일반 파일 확인    | `Files.isRegularFile(path)`     | `file.isFile()`             |
| 디렉토리 확인     | `Files.isDirectory(path)`       | `file.isDirectory()`        |
| 심볼릭 링크 확인  | `Files.isSymbolicLink(path)`    | ❌ 지원 안 함               |
| 숨김 파일 확인    | `Files.isHidden(path)`          | `file.isHidden()`           |
| 읽기 가능 여부    | `Files.isReadable(path)`        | `file.canRead()`            |
| 쓰기 가능 여부    | `Files.isWritable(path)`        | `file.canWrite()`           |
| 실행 가능 여부    | `Files.isExecutable(path)`      | `file.canExecute()`         |



## 📦 활용 팁
- Files 메서드는 Path 기반이며, 예외를 던지지 않고 boolean을 반환 → 안전한 검사에 적합
- File은 간단한 로직에 유용하지만, 복잡한 파일 시스템 작업에는 Files + Path 조합이 더 강력
- Files.readAttributes()와 함께 사용하면 더 많은 메타데이터 확인 가능

---

# 🧠 Files.readAttributes() 개요
## ✅ 기본 사용법
```java
Path path = Paths.get("sample.txt");
BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);
```
- path: 대상 파일 또는 디렉토리 경로
- BasicFileAttributes.class: 읽고자 하는 속성 클래스
- 반환값은 속성 객체로, 다양한 정보에 접근 가능

## 🔍 주요 속성 클래스 요약

| 속성 클래스               | 설명                                                                 |
|---------------------------|----------------------------------------------------------------------|
| `BasicFileAttributes`     | 생성일, 수정일, 크기, 파일/디렉토리 여부 등 기본 정보               |
| `DosFileAttributes`       | Windows 전용: 숨김, 읽기 전용, 시스템 파일 여부 등                   |
| `PosixFileAttributes`     | UNIX/Linux 전용: 권한, 소유자, 그룹 정보 등                          |
| `AclFileAttributeView`    | Windows NTFS 전용: ACL 기반 접근 제어 목록                           |
| `FileOwnerAttributeView`  | 파일 소유자 정보 (모든 OS에서 사용 가능)                             |


## ✅ BasicFileAttributes 주요 메서드
```java
BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);

attrs.creationTime();       // 생성 시간
attrs.lastModifiedTime();   // 마지막 수정 시간
attrs.lastAccessTime();     // 마지막 접근 시간
attrs.size();               // 파일 크기 (바이트)
attrs.isDirectory();        // 디렉토리 여부
attrs.isRegularFile();      // 일반 파일 여부
attrs.isSymbolicLink();     // 심볼릭 링크 여부
```

## ✅ POSIX 권한 정보 예시
```java
PosixFileAttributes posixAttrs = Files.readAttributes(path, PosixFileAttributes.class);
Set<PosixFilePermission> perms = posixAttrs.permissions();
UserPrincipal owner = posixAttrs.owner();
GroupPrincipal group = posixAttrs.group();
```

- permissions() → rwx 권한 정보
- owner() / group() → 소유자 및 그룹 정보

## ✅ 소유자 정보 읽기
```java
FileOwnerAttributeView ownerView = Files.getFileAttributeView(path, FileOwnerAttributeView.class);
UserPrincipal owner = ownerView.getOwner();
System.out.println("소유자: " + owner.getName());
```

## 📦 활용 팁
- Files.readAttributes()는 파일 존재 여부와 무관하게 예외를 던질 수 있으므로 try-catch 필수
- 속성 클래스는 OS에 따라 다르므로 플랫폼 조건 분기가 필요할 수 있음
- Files.setAttribute()를 사용하면 일부 속성은 수정도 가능


---

# 속성 수정

Files.setAttribute()는 Java NIO에서 파일의 속성을 직접 수정할 수 있는 메서드.  
아래에 사용법, 주요 속성 키, 주의사항 등을 체계적으로 정리.

## 🧭 Files.setAttribute() 개요
### ✅ 기본 문법
```java
Files.setAttribute(Path path, String attribute, Object value, LinkOption... options);
```
- path: 대상 파일 또는 디렉토리
- attribute: 수정할 속성의 이름 (문자열)
- value: 설정할 값
- options: 심볼릭 링크 처리 옵션 (선택)

## 🔍 주요 속성 키 요약

| 속성 키                   | 설명                                | 설정 값 예시                        |
|---------------------------|-------------------------------------|-------------------------------------|
| `"basic:lastModifiedTime"`| 마지막 수정 시간 설정                | `FileTime.fromMillis(...)`          |
| `"basic:creationTime"`    | 생성 시간 설정                      | `FileTime.fromMillis(...)`          |
| `"basic:lastAccessTime"`  | 마지막 접근 시간 설정               | `FileTime.fromMillis(...)`          |
| `"dos:hidden"`            | 숨김 속성 설정 (Windows 전용)       | `true` / `false`                    |
| `"dos:readonly"`          | 읽기 전용 설정 (Windows 전용)       | `true` / `false`                    |
| `"posix:permissions"`     | POSIX 권한 설정 (UNIX/Linux 전용)   | `Set<PosixFilePermission>`          |
| `"owner:owner"`           | 파일 소유자 설정                    | `UserPrincipal`                     |

## ✅ 예시 코드
### 🔹 수정 시간 변경
```java
Path path = Paths.get("sample.txt");
FileTime newTime = FileTime.fromMillis(System.currentTimeMillis());
Files.setAttribute(path, "basic:lastModifiedTime", newTime);
```

### 🔹 숨김 속성 설정 (Windows)
```java
Files.setAttribute(path, "dos:hidden", true);
```

### 🔹 POSIX 권한 설정 (Linux/macOS)
```java
Set<PosixFilePermission> perms = PosixFilePermissions.fromString("rw-r--r--");
Files.setAttribute(path, "posix:permissions", perms);
```

## ⚠️ 주의사항
- 속성 키는 "view:attribute" 형식으로 지정
- OS에 따라 지원되는 속성이 다름
- Windows: dos:*
- UNIX/Linux: posix:*
- 예외 처리 필수: IOException, UnsupportedOperationException 등 발생 가능

## 📦 활용 팁
- Files.setAttribute()는 Files.getAttribute()와 함께 사용하면 읽기/쓰기 모두 가능
- FileTime은 시간 속성 설정에 필수
- UserPrincipalLookupService를 통해 소유자 객체를 얻을 수 있음
```java
UserPrincipal owner = FileSystems.getDefault()
    .getUserPrincipalLookupService()
    .lookupPrincipalByName("jeongjunghwan");
Files.setAttribute(path, "owner:owner", owner);
```

---






