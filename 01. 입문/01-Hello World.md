## 🧑‍💻 1. 개발 환경 설정
### IDE 선택: IntelliJ vs Eclipse
- IntelliJ: 최근 인기, 빠르고 편리함. 대부분의 기업에서 사용.
- Eclipse: 과거에 많이 사용됨.
- 추천: IntelliJ Community Edition (무료)

### OS 선택
- Mac: 대다수 기업에서 사용
- Windows: 사용 가능
- Linux: 사용 가능

### 자바 설치
- IntelliJ에서 자동 설치 가능 (별도 설치 불필요)

## 💻 2. IntelliJ 설치 및 프로젝트 생성
### 설치 링크
- IntelliJ 다운로드

### 새 프로젝트 생성
- 이름: java-start
- 언어: Java
- JDK: 17 이상 (권장: OpenJDK 21)
- 샘플 코드 추가: 체크

### JDK 설정
- Vendor: Oracle OpenJDK 또는 Eclipse Temurin
- Mac M1/M2/M3: aarch64 선택
- 설치 위치: 기본값 사용

### 📂 3. 기존 소스 코드 실행
압축 푼 프로젝트 열기
- 메뉴: File → New → Project from Existing Sources
- JDK 없을 경우: + 버튼으로 OpenJDK 21 다운로드


### ▶️ 4. 자바 프로그램 실행
#### HelloJava 예제
```rust
public class HelloJava {
    public static void main(String[] args) {
        System.out.println("hello java");
    }
}
```

- `클래스 이름` 과 `파일명` 동일해야 함
- `main()` 메서드가 시작점
- `System.out.println()` 으로 콘솔 출력
- 문장 끝에는 `;` 필수

#### 실행 결과
```
hello java
```


### ✍️ 5. 주석 (Comment)
주석 종류
- 한 줄: `//`
- 여러 줄: `/* ... */`
#### 예제
```java
public class CommentJava {
    public static void main(String[] args) {
        System.out.println("hello java1"); // 출력 설명
        //System.out.println("hello java2"); // 실행 안됨

        /*
        System.out.println("hello java3");
        System.out.println("hello java4");
        */
    }
}
```

### 📘 6. 자바란?

#### 자바의 구조
- 표준 스펙: 자바의 설계도
- 구현체: Oracle OpenJDK, Amazon Corretto 등

#### 운영체제 독립성
- 자바는 JVM 위에서 실행되므로 OS에 상관없이 작동
- 개발은 Mac/Windows, 운영은 Linux 가능

#### 컴파일과 실행
- .java 파일 작성
- javac로 컴파일 → .class 생성
- java로 실행 → JVM이 작동

#### IntelliJ의 역할
- javac, java 명령어를 자동으로 처리
- 컴파일과 실행을 버튼 클릭으로 해결

---
