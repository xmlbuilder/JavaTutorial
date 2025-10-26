# Region
Java에는 C#처럼 공식적인 `#region` 디렉티브는 없지만, **IDE(통합 개발 환경)** 에서 유사한 기능을 제공.  
아래에 Java에서 "Region" 스타일의 코드 접기 기능을 어떻게 사용하는지 정리.

## 🧠 Java에서 Region 기능 요약
### ✅ 1. C#의 Region 문법
```csharp
#region 설명
// 코드
#endregion
```
- Visual Studio에서 코드 블록을 접거나 펼칠 수 있음
- 주석 기반이 아닌 언어 디렉티브로 처리됨

### ✅ 2. Java에서 유사한 Region 기능 (IDE 기반)
```java
//region 설명
// 코드
//endregion
```

- Java 언어 자체에는 #region 문법 없음
- 하지만 IntelliJ IDEA, Eclipse, VS Code 같은 IDE에서는 `//region`, `//endregion` 주석을 인식해 코드 접기(folding) 기능 제공

#### 🔧 예시 (Java)
```java
public class Sample {
    
    //region Initialization
    public void init() {
        System.out.println("Initializing...");
    }
    //endregion

    //region Business Logic
    public void process() {
        System.out.println("Processing...");
    }
    //endregion
}
```

- `IntelliJ` 에서는 `//region` 주석을 자동 인식해 접기 가능
- Eclipse에서는 Ctrl + Shift + /로 수동 접기 가능 (주석 기반)

## ✨ 요약 비교
| 기능       | C#                         | Java (IDE 지원)              |
|------------|----------------------------|------------------------------|
| 문법       | `#region`, `#endregion`    | `//region`, `//endregion`    |
| 언어 지원  | 언어 차원에서 공식 지원     | 언어 차원에서는 미지원        |
| IDE 지원   | Visual Studio              | IntelliJ, Eclipse, VS Code   |
| 코드 접기  | 자동                        | 주석 기반 접기               |


## 🧠 팁
- Java에서 //region은 문서화나 구조화 목적으로도 유용해요
- 팀 프로젝트에서는 코드 블록을 명확히 구분하는 데 효과적
- IDE 설정에서 "Custom Folding Regions" 기능을 활성화해야 할 수도 있음

---

