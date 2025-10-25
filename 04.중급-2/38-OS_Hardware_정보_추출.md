# Hardware 정보
Java에서 OS 및 CPU 정보를 확인하려면 System.getProperty()로 OS 이름을 확인하고,  
Runtime.getRuntime().availableProcessors()  
또는 OSHI 라이브러리를 활용하면 CPU 코어 수, 아키텍처, 메모리, 디스크 등 다양한 시스템 정보를 얻을 수 있음.

## 🖥️ OS 정보 확인 (기본 Java API)
```java
String os = System.getProperty("os.name").toLowerCase();
if (os.contains("win")) {
    System.out.println("Windows");
} else if (os.contains("mac")) {
    System.out.println("Mac");
} else if (os.contains("aix")) {
    System.out.println("Unix");
} else if (os.contains("linux")) {
    System.out.println("Linux");
}
```

- System.getProperty("os.name"): OS 이름
- System.getProperty("os.arch"): CPU 아키텍처 (예: x86_64)
- System.getProperty("os.version"): OS 버전

## 🧠 CPU 정보 확인 (기본 Java API)
```java
int cores = Runtime.getRuntime().availableProcessors();
System.out.println("Available logical processors: " + cores);
```

- 논리 프로세서 수를 반환 (하이퍼스레딩 포함)
- 실제 물리 코어 수는 OSHI 같은 라이브러리 필요

## 🔍 OSHI 라이브러리로 시스템 정보 확인
**OSHI (Operating System and Hardware Information)** 는 JNA 기반의 Java 라이브러리로,  
OS/CPU/메모리/디스크/네트워크 정보를 플랫폼 독립적으로 제공합니다.
### ✅ Maven 의존성 추가
```xml
<dependency>
  <groupId>com.github.oshi</groupId>
  <artifactId>oshi-core</artifactId>
  <version>6.4.2</version>
</dependency>
```
### ⚙️ OSHI Gradle 의존성 설정
```
dependencies {
    implementation 'com.github.oshi:oshi-core:6.4.2'
}
```

### ✅ 샘플 코드
```java
import oshi.SystemInfo;
import oshi.hardware.CentralProcessor;
import oshi.hardware.GlobalMemory;
import oshi.software.os.OperatingSystem;

public class SystemCheck {
    public static void main(String[] args) {
        SystemInfo si = new SystemInfo();
        OperatingSystem os = si.getOperatingSystem();
        CentralProcessor cpu = si.getHardware().getProcessor();
        GlobalMemory memory = si.getHardware().getMemory();

        System.out.println("OS: " + os.toString());
        System.out.println("CPU: " + cpu.getProcessorIdentifier().getName());
        System.out.println("Logical CPUs: " + cpu.getLogicalProcessorCount());
        System.out.println("Physical CPUs: " + cpu.getPhysicalProcessorCount());
        System.out.println("Total Memory: " + memory.getTotal() / (1024 * 1024) + " MB");
        System.out.println("Available Memory: " + memory.getAvailable() / (1024 * 1024) + " MB");
    }
}
```

## 📊 주요 정보 요약

| 항목             | 확인 방법 또는 API 예시                              |
|------------------|------------------------------------------------------|
| 운영체제 이름     | `System.getProperty("os.name")`                      |
| CPU 논리 코어 수 | `Runtime.getRuntime().availableProcessors()`         |
| 전체/사용 가능 메모리 | `GlobalMemory.getTotal()`, `getAvailable()` (OSHI) |
| 디스크/네트워크 정보 | `HWDiskStore`, `NetworkIF` (OSHI)                   |


## ✅ 실전 활용 팁
- 운영 환경 모니터링: 서버 상태 체크, 리소스 경고 알림
- 클라이언트 정보 수집: 설치형 앱에서 사용자 시스템 정보 확인
- 성능 최적화: CPU 코어 수에 따라 병렬 처리 조정

---

# Android
## Android에서 시스템 정보 확인하는 방법
Android에서는 OSHI 대신 Android SDK의 API를 사용해야 해요:
### 📱 OS 및 디바이스 정보
```java
String manufacturer = Build.MANUFACTURER;
String model = Build.MODEL;
String version = Build.VERSION.RELEASE;
String osName = "Android " + version;
```

### 🧠 CPU 정보
```java
int cores = Runtime.getRuntime().availableProcessors();
String arch = System.getProperty("os.arch");
```

## 🧮 메모리 정보
```java
ActivityManager.MemoryInfo memoryInfo = new ActivityManager.MemoryInfo();
ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
activityManager.getMemoryInfo(memoryInfo);

long totalMem = memoryInfo.totalMem;
long availMem = memoryInfo.availMem;
```


## 📦 Android용 시스템 정보 라이브러리

| 라이브러리 이름                  | 주요 기능                                   | GitHub 또는 설명 링크 |
|----------------------------------|---------------------------------------------|------------------------|
| `AndroidDeviceNames`             | 제조사/모델 이름을 사용자 친화적으로 표시    | [GitHub 링크](https://github.com/jaredrummler/AndroidDeviceNames) |
| `DeviceInfo`                     | OS, CPU, 메모리, 디바이스 정보 수집          | [GitHub 링크](https://github.com/numetriclabz/android-system-info) |
| `Battery Historian` (Google)     | 배터리 사용량 분석 및 성능 모니터링          | [GitHub 링크](https://github.com/google/battery-historian) |
| `Simple System Info`             | CPU, RAM, 디스크, 센서 정보 간단 조회         | [GitHub 링크](https://github.com/romannurik/muzei) (내부 포함) |




