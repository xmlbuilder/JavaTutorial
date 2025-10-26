# JSON Parsing
Java에서 JSON을 파싱하는 방법을 Object, Nested Object, Array 구조별로 정리.  
JSON의 기본 구조와 함께 org.json 라이브러리를 활용한 실전 코드도 포함.  

## 📦 JSON이란?
- JavaScript Object Notation의 약자
- 데이터를 Key-Value 형식으로 표현
- 기본 타입: String, Number (int, long, double), Boolean, null
- 구조 타입: Object, Array

## 🧠 JSON 구조별 파싱 방법
### ✅ 1. 단일 Object 파싱
```
{
  "title": "howtogetstroagesize",
  "url": "https://codechacha.com/ko/get-free-and-total-size-of-volumes-in-android/",
  "draft": false,
  "star": 10
}
```


```java
JSONObject jObject = new JSONObject(jsonString);
String title = jObject.getString("title");
String url = jObject.getString("url");
boolean draft = jObject.getBoolean("draft");
int star = jObject.getInt("star");
```


## ✅ 2. 중첩된 Object 파싱
```
{
  "post1": {
    "title": "howtogetstroagesize",
    "url": "...",
    "draft": false
  },
  "post2": {
    "title": "AndroidQ,ScopedStorage",
    "url": "...",
    "draft": false
  }
}
```

```java
JSONObject jsonObject = new JSONObject(jsonString);
JSONObject post1 = jsonObject.getJSONObject("post1");
JSONObject post2 = jsonObject.getJSONObject("post2");

System.out.println(post2.getString("title"));
```
## ✅ 3. Array 파싱
```
{
  "posts": [
    { "title": "...", "url": "...", "draft": false },
    { "title": "...", "url": "...", "draft": false },
    { "title": "...", "url": "...", "draft": true }
  ]
}
```

```java
JSONObject jsonObject = new JSONObject(jsonString);
JSONArray posts = jsonObject.getJSONArray("posts");

for (int i = 0; i < posts.length(); i++) {
    JSONObject post = posts.getJSONObject(i);
    System.out.println(post.getString("title"));
    System.out.println(post.getString("url"));
    System.out.println(post.getBoolean("draft"));
}
```
## 🔧 주요 JSON 파싱 메서드 요약

| 메서드               | 반환 타입     | 설명                                      |
|----------------------|---------------|-------------------------------------------|
| `getString("key")`   | `String`      | 지정된 키의 문자열 값을 가져옴            |
| `getInt("key")`      | `int`         | 지정된 키의 정수 값을 가져옴              |
| `getBoolean("key")`  | `boolean`     | 지정된 키의 불리언 값을 가져옴            |
| `getJSONObject("key")` | `JSONObject` | 중첩된 JSON 객체를 가져옴                 |
| `getJSONArray("key")`  | `JSONArray`  | 배열 형태의 JSON 데이터를 가져옴          |
| `length()`           | `int`         | `JSONArray`의 요소 개수 반환               |

## 🧪 예시 코드 스니펫
```java
JSONObject obj = new JSONObject(jsonString);
String title = obj.getString("title");
int star = obj.getInt("star");
boolean draft = obj.getBoolean("draft");

JSONObject nested = obj.getJSONObject("post1");
JSONArray posts = obj.getJSONArray("posts");
int count = posts.length();
```

## 📦 활용 팁
- JSON 문자열은 항상 유효한 형식이어야 하며, 따옴표는 `"` 로 감싸야 합니다.
- org.json은 Android에서도 사용 가능하지만, 복잡한 구조에는 Gson이나 Jackson이 더 유리.
- JSON 파싱 시 JSONException이 발생할 수 있으므로 예외 처리도 고려.
