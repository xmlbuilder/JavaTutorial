## 📘 Java Lambda Expression

- **람다 표현식(Lambda Expression)** 을 활용하여 컬렉션 정렬, 반복 처리, 그리고 스레드 생성 등의 예제.  
- 기존의 익명 클래스 방식과 람다 표현식을 비교하며, 코드의 간결성과 가독성 향상을 확인할 수 있습니다.

### 📌 클래스 구조
```java
package com.thj;

import java.util.ArrayList;
import java.util.Comparator;
```

#### 🔹 Product 클래스
```java
class Product {
    private String name;
    private boolean isFood;
    private String madeBy;

    public Product(String name, boolean isFood, String madeBy) {
        this.name = name;
        this.isFood = isFood;
        this.madeBy = madeBy;
    }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public boolean isFood() { return isFood; }
    public void setFood(boolean food) { isFood = food; }

    public String getMadeBy() { return madeBy; }
    public void setMadeBy(String madeBy) { this.madeBy = madeBy; }
}
```
- Product는 이름, 식품 여부, 제조국 정보를 담는 간단한 데이터 클래스입니다.

### 📌 RamdaTest 클래스
```java
public class RamdaTest {
    public static void main(String[] args) {
        ArrayList<Product> products = new ArrayList<>();
        products.add(new Product("Sample1", true, "Korea"));
        products.add(new Product("Sample22", true, "Korea"));
        products.add(new Product("Sample345", true, "Korea"));
```


### 📌 1. 전통적인 익명 클래스 기반 정렬
```java
products.sort(new Comparator<Product>() {
    @Override
    public int compare(Product o1, Product o2) {
        return o2.getName().length() - o1.getName().length();
    }
});

for (Product product : products) {
    System.out.println(product.getName());
}
```
- 이름의 길이를 기준으로 내림차순 정렬
- 전통적인 익명 클래스 방식 사용

### 📌 2. 람다 표현식 기반 정렬
```java
products.sort((o1, o2) -> o1.getName().length() - o2.getName().length());
```

- 이름의 길이를 기준으로 오름차순 정렬
- 람다 표현식을 사용하여 코드 간결화

### 📌 3. 람다를 이용한 컬렉션 순회
```java
products.forEach(product -> {
    System.out.println(product.getName());
});
```

- forEach와 람다를 이용한 간단한 반복 출력

### 📌 4. 스레드 생성 비교
#### 🔹 전통적인 방식
```java
Thread th = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("NormalThread");
    }
});
th.start();
```

#### 🔹 람다 표현식 방식
```java
Thread th1 = new Thread(() -> System.out.println("NormalStarted"));
th1.start();
```

- 람다를 사용하면 Runnable 구현이 간결해짐.

### 📌 요약: 람다 표현식 vs 전통 방식

| 기능             | 전통 방식                     | 람다 표현식 방식                  |
|------------------|-------------------------------|-----------------------------------|
| 정렬             | `Comparator` 익명 클래스 사용 | `(a, b) -> ...`                   |
| 컬렉션 순회      | `for-each` 루프               | `forEach(item -> { ... })`       |
| 스레드 생성      | `new Runnable() { ... }`      | `() -> { ... }`                  |

#### 🔹 설명
- Comparator: 익명 클래스를 사용하면 코드가 길어지지만, 람다를 사용하면 간결하게 정렬 조건을 표현할 수 있음.
- forEach: Java 8부터 List.forEach()와 람다를 함께 사용하면 반복 처리가 훨씬 깔끔.
- Runnable: 스레드 생성 시 람다를 사용하면 Runnable 구현을 줄일 수 있어 코드가 간결.


### 📌 참고
- Java 8 이상에서 람다 표현식 사용 가능
- 람다는 함수형 인터페이스를 구현할 때 사용됨
- C#에서도 유사한 람다 문법을 제공하며, LINQ와 함께 자주 사용됨

---

