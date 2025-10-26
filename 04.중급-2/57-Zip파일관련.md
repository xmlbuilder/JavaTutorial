# 🧭 ZIP 처리 단계별 요약

## ✅ 1. ZIP 압축하기 (ZipOutputStream 사용)
### 📌 주요 단계
```java
ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(zipFilePath));
ZipEntry entry = new ZipEntry("파일명");
zos.putNextEntry(entry);
zos.write(파일데이터);
zos.closeEntry();
```

### 🧩 설명
- ZipOutputStream은 ZIP 파일을 생성하는 스트림
- ZipEntry는 ZIP 내부의 각 파일을 나타냄
- putNextEntry()로 새 항목 시작
- write()로 파일 내용 기록
- closeEntry()는 생략 가능 (자동 처리됨)

## ✅ 2. ZIP 해제하기 (ZipInputStream 사용)
### 📌 예제 코드
```java
import java.io.*;
import java.util.zip.*;

public class ZipExtractTest {
    public static void main(String[] args) {
        String zipPath = "/Users/jeongjunghwan/Downloads/Sample.zip";
        String outputDir = "/Users/jeongjunghwan/Downloads/unzipped/";

        try (ZipInputStream zis = new ZipInputStream(new FileInputStream(zipPath))) {
            ZipEntry entry;
            while ((entry = zis.getNextEntry()) != null) {
                File outFile = new File(outputDir, Paths.get(entry.getName()).getFileName().toString());
                try (FileOutputStream fos = new FileOutputStream(outFile)) {
                    byte[] buffer = new byte[1024];
                    int len;
                    while ((len = zis.read(buffer)) > 0) {
                        fos.write(buffer, 0, len);
                    }
                }
                zis.closeEntry();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 🧩 설명
- ZipInputStream은 ZIP 파일을 읽는 스트림
- getNextEntry()로 다음 항목 탐색
- read()로 파일 내용 읽기
- FileOutputStream으로 파일 저장

## 🧠 핵심 클래스 요약
| 클래스/메서드        | 역할 및 설명                                      |
|----------------------|--------------------------------------------------|
| `ZipOutputStream`    | ZIP 파일 생성용 출력 스트림                       |
| `ZipEntry`           | ZIP 내부의 각 파일 항목                           |
| `putNextEntry()`     | 새 항목 시작                                      |
| `write()`            | 파일 내용 기록                                    |
| `ZipInputStream`     | ZIP 파일 읽기용 입력 스트림                       |
| `getNextEntry()`     | 다음 항목으로 이동                                |
| `read()`             | 압축된 파일 내용 읽기                             |
| `closeEntry()`       | 현재 항목 종료                                    |

## 📦 활용 팁
- 압축 시 ZipEntry 이름은 상대 경로로 지정하는 것이 좋음
- 해제 시 entry.getName()은 경로 포함 → Paths.get(...).getFileName()으로 정리 가능
- 대용량 파일은 BufferedInputStream과 BufferedOutputStream을 함께 사용하면 성능 향상

---
