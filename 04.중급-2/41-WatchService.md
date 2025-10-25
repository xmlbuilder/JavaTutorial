# WatchService

Java NIO의 WatchService를 활용해 디렉토리 내 파일의 생성, 삭제, 수정 이벤트를 실시간으로 감시하는 예제.  
아래에 구조, 동작 방식, 주요 클래스, 주의사항까지 체계적으로 설명.

## 🔍 WatchService란?
- Java NIO에서 제공하는 파일 시스템 이벤트 감시 서비스
- 디렉토리 내에서 발생하는 파일 생성, 삭제, 수정 이벤트를 감지
- 백그라운드에서 이벤트 기반으로 동작하므로 효율적

## 🧱 코드 구조 설명
### 1. 감시 대상 디렉토리 지정
```java
Path path = Paths.get("/Users/jeongjunghwan/Downloads");
```

- 감시할 디렉토리를 Path 객체로 지정

## 2. WatchService 생성 및 디렉토리 등록
```java
WatchService watchService = FileSystems.getDefault().newWatchService();
path.register(watchService,
    StandardWatchEventKinds.ENTRY_CREATE,
    StandardWatchEventKinds.ENTRY_DELETE,
    StandardWatchEventKinds.ENTRY_MODIFY);

```
- WatchService는 OS의 파일 시스템 이벤트를 감지
- register()를 통해 감시할 이벤트 종류를 등록:
- ENTRY_CREATE: 파일 생성
- ENTRY_DELETE: 파일 삭제
- ENTRY_MODIFY: 파일 수정

## 3. 이벤트 감시 루프
```java
while (true) {
    WatchKey watchKey = watchService.take(); // 이벤트 발생 시까지 블로킹
    for (WatchEvent<?> event : watchKey.pollEvents()) {
        ...
    }
}
```

- take()는 이벤트가 발생할 때까지 대기 (블로킹)
- pollEvents()는 발생한 이벤트 목록을 가져옴

## 4. 이벤트 종류별 처리
```java
WatchEvent.Kind<?> kind = event.kind();
if (kind == StandardWatchEventKinds.OVERFLOW) continue;

Path name = (Path) event.context();
Path child = path.resolve(name);

if (kind == ENTRY_CREATE) {
    System.out.println(child + " 이 작성되었습니다.");
} else if (kind == ENTRY_DELETE) {
    System.out.println(child + " 이 삭제되었습니다.");
} else if (kind == ENTRY_MODIFY) {
    System.out.println(child + " 이 수정되었습니다.");
}
```

- event.context()는 변경된 파일/디렉토리의 상대 경로
- path.resolve(name)으로 절대 경로 생성
- 이벤트 종류에 따라 메시지 출력

## 📌 주요 클래스 요약

| 클래스/인터페이스         | 설명                                                                 |
|----------------------------|----------------------------------------------------------------------|
| `WatchService`             | 파일 시스템 변경 이벤트를 감시하는 서비스. 디렉토리를 등록해 이벤트 수신 |
| `WatchKey`                 | 감지된 이벤트 묶음. `pollEvents()`로 이벤트 목록을 가져옴              |
| `WatchEvent<?>`            | 개별 이벤트 객체. 생성, 삭제, 수정 등의 정보를 포함                    |
| `StandardWatchEventKinds`  | 감시할 수 있는 이벤트 종류 (CREATE, DELETE, MODIFY, OVERFLOW 등)       |


## ⚠️ 주의사항
- 하위 디렉토리는 자동 감시되지 않음 → 직접 재귀적으로 등록해야 함
- 파일 내용 변경은 감지되지 않음 → 메타데이터 변경만 감지됨
- OVERFLOW 이벤트는 이벤트 큐가 넘칠 때 발생 → 무시하거나 로그 처리
- 무한 루프이므로 백그라운드 스레드에서 실행하는 것이 좋음

## ✨ 확장 팁
- 여러 디렉토리를 동시에 감시하려면 Map<Path, WatchKey>로 관리
- 이벤트 발생 시 파일 내용 읽기, 로그 저장, 알림 전송 등과 연계 가능
- ScheduledExecutorService와 함께 사용하면 감시 주기 조절 가능


