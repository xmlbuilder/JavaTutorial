# Generic 타입 추론
 이 코드는 언뜻 보면 단순해 보이지만, **Java 제네릭(Generic)** 과 타입 추론,  
 그리고 Collections.emptyList()의 동작을 이해하려는 의도로 작성된 것임. 
 
## ✅ 코드 분석
```java
List<List<String>> list = new ArrayList<>();
list.add(Collections.emptyList());
list.add(Collections.<String>emptyList());
```

## 🔍 핵심 포인트
### 타입 추론
- Collections.emptyList()는 타입이 List<Object>로 추론됨
- 즉, list.add(Collections.emptyList())는 경고 없이 컴파일되지만 타입 안전하지 않음
- List<List<String>>에 List<Object>를 넣는 셈  
- Collections.<String>emptyList()는 명시적으로 List<String> 타입 지정
- 타입 안정성 확보
- 제네릭 타입을 명시적으로 지정하는 방식

## ⚠️ 왜 의미가 있는가?
| 항목                              | 설명 |
|-----------------------------------|------|
| `emptyList()`                     | 타입 추론에 따라 `List<Object>`로 해석될 수 있어 타입 안정성에 주의 필요 |
| `<String>`                        | 명시적 타입 지정으로 `List<String>`을 반환 → 타입 안정성 확보 |
| `Collections.emptyList()` vs `new ArrayList<>()` | 불변(emptyList) vs 가변(ArrayList) 컬렉션 초기화 방식 비교 가능 |


## 🧪 실전에서 어떻게 쓰면 좋을까?
- 빈 리스트를 반환할 때 Collections.emptyList()를 자주 사용
- 하지만 제네릭 타입이 명확해야 할 경우에는 Collections.<Type>emptyList()처럼 명시적으로 타입 지정하는 것이 안전
- 예를 들어, 메서드에서 List<String>을 반환해야 할 때:
```java
public List<String> getNames() {
    if (noData) return Collections.<String>emptyList();
    return actualList;
}
```


## 📌 요약 정리
| 항목                     | 설명 |
|--------------------------|------|
| `Collections.emptyList()` | 타입 추론에 따라 `List<Object>`로 해석될 수 있음 |
| `Collections.<String>emptyList()` | 명시적 타입 지정으로 타입 안정성 확보 |
| 제네릭 학습 목적          | 타입 추론과 명시적 제네릭 지정의 차이를 실험 |
| 실전 활용                 | 빈 컬렉션 반환 시 안전한 타입 지정 방식으로 사용 가능 |

---



