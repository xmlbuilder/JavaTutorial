# C# dynamic 흉내내기
**C#** 의 dynamic 키워드를 사용하는 예제.  
Java에는 dynamic이라는 키워드는 없지만, 비슷한 개념을 리플렉션(reflection),  
Map 기반 구조, 또는 JSON 라이브러리를 통해 구현할 수 있음.

## 🔍 C#의 dynamic이란?
- dynamic은 런타임 타입 바인딩을 허용하는 키워드
- 컴파일 시 타입 검사를 생략하고, 런타임에 실제 타입을 결정
- 예제에서 deserializedJson.x는 컴파일러가 타입을 알 수 없지만, 런타임에 "x" 필드를 찾아서 처리함
```csharp
string json = @"{x : 10, y: ""ho""}";
dynamic deserializedJson = JsonConvert.DeserializeObject(json);
int x = deserializedJson.x;
string y = deserializedJson.y;
```


## 🧠 Java에서는 어떻게 비슷하게 구현할까?
Java는 정적 타입 언어이기 때문에 dynamic 키워드는 없지만, 다음 방식으로 유사한 기능을 구현할 수 있음:
### ✅ 1. Map<String, Object>로 처리
```java
String json = "{\"x\": 10, \"y\": \"ho\"}";
ObjectMapper mapper = new ObjectMapper();
Map<String, Object> map = mapper.readValue(json, new TypeReference<>() {});
int x = (int) map.get("x");
String y = (String) map.get("y");
System.out.println(x);
System.out.println(y);
```

- ObjectMapper는 Jackson 라이브러리의 JSON 파서
- Map<String, Object>는 동적 구조처럼 작동

### ✅ 2. JsonNode를 사용한 트리 구조 접근
```java
JsonNode node = mapper.readTree(json);
int x = node.get("x").asInt();
String y = node.get("y").asText();
```

- JsonNode는 JSON을 트리처럼 탐색할 수 있게 해줌
- 타입 캐스팅 없이 .asInt(), .asText()로 안전하게 접근 가능

### ✅ 3. 리플렉션을 통한 동적 접근
```java
Method method = obj.getClass().getMethod("getX");
Object result = method.invoke(obj);
```

- 클래스 구조를 모른 채 메서드나 필드에 접근 가능
- 하지만 성능 저하와 안전성 문제로 일반적으로는 JSON 방식이 더 선호됨

## ✨ 요약 비교
| 언어/기술       | 키워드/구조      | 설명                                                                 |
|----------------|------------------|----------------------------------------------------------------------|
| C#             | `dynamic`        | 컴파일 시 타입 체크 생략, 런타임에 실제 타입 결정. 매우 유연하지만 타입 안전성 낮음. |
| Java           | `Map<String, Object>` | JSON을 키-값 구조로 파싱하여 동적 접근. 타입 캐스팅 필요.                      |
| Java           | `JsonNode`       | Jackson의 트리 기반 JSON 탐색 구조. 타입 안전한 접근 가능 (`.asInt()`, `.asText()` 등). |
| Java           | Reflection       | 런타임에 클래스 구조 탐색 및 메서드 호출. `dynamic`처럼 동작하지만 복잡하고 느릴 수 있음. |

---

