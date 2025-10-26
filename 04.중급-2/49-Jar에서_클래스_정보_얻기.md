# 🔍 목표
- JAR 내부의 클래스 목록을 탐색
- 특정 패턴의 클래스만 필터링
- 해당 클래스의 인스턴스를 생성하고 메서드 실행

## 🧭 단계별 정리
### ✅ 1. Reflections 객체 생성
```java
Reflections reflections = new Reflections("com.jar", new SubTypesScanner(false));
```

- "com.jar": 탐색할 패키지 경로
- SubTypesScanner(false): 모든 하위 타입을 스캔 (인터페이스, 클래스 포함)

### ✅ 2. 클래스 목록 수집
```java
Set<Class> sets = reflections.getSubTypesOf(Object.class)
    .stream()
    .collect(Collectors.toSet());
```

- getSubTypesOf(Object.class): Object를 상속한 모든 클래스 탐색
- Set<Class>로 중복 없이 수집

### ✅ 3. 클래스 이름 출력 및 필터링
```java
for (Class cls : sets) {
    String strClsName = cls.getName();
    System.out.println(strClsName);
```

- 클래스 이름을 출력
- 특정 접두사 (com.jar.MacroCode)로 필터링

### ✅ 4. 인스턴스 생성 및 메서드 실행
```java
if (strClsName.startsWith("com.jar.MacroCode")) {
    Constructor<?> constructor = cls.getConstructor(null);
    MacroCode sampleCode1 = (MacroCode) constructor.newInstance();
    sampleCode1.execute();
}
```

- 기본 생성자 호출
- MacroCode 타입으로 캐스팅
- execute() 메서드 실행

## 🧠 전체 흐름 요약
| 단계 | 설명                                       |
|------|--------------------------------------------|
| 1    | Reflections 객체 생성 (패키지 + 스캐너 설정) |
| 2    | 클래스 목록 수집 (`getSubTypesOf`)          |
| 3    | 클래스 이름 출력 및 필터링                  |
| 4    | 인스턴스 생성 (`newInstance()`)             |
| 5    | 메서드 실행 (`execute()`)                   |

## 📦 Maven 의존성 (Reflections)
```xml
<dependency>
  <groupId>org.reflections</groupId>
  <artifactId>reflections</artifactId>
  <version>0.10.2</version>
</dependency>
```

## ⚙️ Gradle 설정법 for Reflections
### ✅ 1. build.gradle에 의존성 추가
```
dependencies {
    implementation 'org.reflections:reflections:0.10.2'
}
```

- implementation은 컴파일과 런타임에 모두 필요한 라이브러리일 때 사용
- 최신 버전은 Maven Central에서 확인 가능


## 🔐 주의사항
- JAR 파일이 클래스패스에 포함되어 있어야 탐색 가능
- 기본 생성자가 없거나 MacroCode 타입이 맞지 않으면 예외 발생
- newInstance()는 예외 처리 필수 (InvocationTargetException, IllegalAccessException 등)

---



