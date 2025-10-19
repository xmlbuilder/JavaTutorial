# 타입 매개변수 제한 흐름 요약
## 1️⃣ DogHospital / CatHospital (타입 고정)
- 구조: 각각 Dog, Cat 타입에 고정된 병원 클래스
- 장점: 타입 안전성 O (다른 동물 넣으면 컴파일 오류)
- 단점: 코드 중복 심함, 재사용성 X
```java
DogHospital dogHospital = new DogHospital();
dogHospital.set(new Dog(...)); // ✅
dogHospital.set(new Cat(...)); // ❌ 컴파일 오류
```


## 2️⃣ AnimalHospitalV1 (다형성 기반)
- 구조: Animal 타입으로 통합
- 장점: 코드 재사용 O
- 단점: 타입 안전성 X (개 병원에 고양이 넣어도 컴파일 오류 없음)
```java
AnimalHospitalV1 dogHospital = new AnimalHospitalV1();
dogHospital.set(new Cat(...)); // ⚠️ 컴파일은 되지만 논리 오류 가능
Dog dog = (Dog) dogHospital.getBigger(...); // ⚠️ 다운캐스팅 필요
```


## 3️⃣ AnimalHospitalV2<T> (제네릭 도입)
- 구조: 제네릭 <T> 사용
- 장점: 코드 재사용 O
- 단점: 타입 안전성 X, 기능 제한 O
- T가 어떤 타입인지 알 수 없기 때문에 getName(), sound() 호출 불가
- Integer, Object도 타입 인자로 들어올 수 있음
```java
AnimalHospitalV2<Dog> dogHospital = new AnimalHospitalV2<>();
AnimalHospitalV2<Integer> intHospital = new AnimalHospitalV2<>(); // ❌ 논리 오류
```

| 버전               | 코드 재사용성 | 타입 안전성 | 기능 사용 가능성 | 문제 요약                         |
|--------------------|----------------|--------------|------------------|-----------------------------------|
| DogHospital / CatHospital | ❌             | ✅           | ✅               | 클래스 중복 많음                  |
| AnimalHospitalV1   | ✅             | ❌           | ✅               | 타입 혼동 가능, 다운캐스팅 필요   |
| AnimalHospitalV2<T> | ✅            | ❌           | ❌               | 타입 제한 없음, 기능 호출 불가    |



## ✅ 해결 방향: 타입 매개변수 제한
다음 단계에서는 제네릭에 타입 제한을 걸어야 합니다:
```java
public class AnimalHospitalV3<T extends Animal> { ... }
```
- T는 반드시 Animal 또는 그 자식 타입만 가능
- getName(), sound() 등 Animal의 기능을 안전하게 사용 가능
- Integer, Object 같은 무관한 타입은 컴파일 오류 발생



## 🧠 타입 매개변수 제한 요약: AnimalHospitalV3
### 📌 핵심 문법
```java
public class AnimalHospitalV3<T extends Animal> { ... }
```

- `T extends Animal` → T는 반드시 Animal 또는 그 자식 클래스여야 함
- 컴파일러는 T가 Animal의 기능을 가진다고 확신할 수 있음
- getName(), getSize(), sound() 같은 메서드 사용 가능

| 항목                        | 설명                                                                 |
|-----------------------------|----------------------------------------------------------------------|
| `DogHospital.set(cat)`       | ❌ 컴파일 오류 발생 → 잘못된 타입 입력 방지                          |
| `T`                         | `T extends Animal` → 타입 매개변수 제한으로 타입 안전성 확보          |
| `Animal`                     | `Animal`의 기능 (`getName()`, `sound()`, `getSize()`) 사용 가능       |
| `Dog` / `Cat` / `Animal`         | ✅ 타입 인자로 사용 가능                                              |
| `Integer` / `Object`           | ❌ 타입 제한으로 컴파일 오류 발생 → 논리 오류 사전 차단               |


## 🧪 실행 예시
```java
AnimalHospitalV3<Dog> dogHospital = new AnimalHospitalV3<>();
dogHospital.set(new Dog("멍멍이1", 100));
dogHospital.checkup();
Dog biggerDog = dogHospital.getBigger(new Dog("멍멍이2", 200));
```

- Dog 타입만 받도록 제한되어 있어 안전하게 작동
- getBigger()는 Dog 타입을 반환하므로 캐스팅 필요 없음

## ❗ 기존 문제와 해결
| 문제 유형             | 이전 방식 (제한 없음) | 개선 방식 (`T extends Animal`) | 설명                                               |
|----------------------|------------------------|-------------------------------|----------------------------------------------------|
| 타입 안전성          | T                      | T extends Animal              | `T`는 아무 타입 가능 → `Animal` 자식만 허용       |
| 기능 사용 가능성     | TObject                | Animal                        | `Object` 기능만 사용 가능 → `Animal` 기능 사용 가능 |
| 타입 인자 제한       | Integer, Object 가능   | Animal만 가능                 | 무관한 타입 차단 → 논리 오류 사전 방지            |


## 🧾 결론
제네릭에 **타입 매개변수 상한 (extends)** 을 지정함으로써:
- ✅ 타입 안전성 확보
- ✅ 코드 재사용성 향상
- ✅ 기능 호출 가능성 확보
→ 실무에서 제네릭을 사용할 때 꼭 기억해야 할 핵심 패턴입니다.

---


## 📦 제네릭 메서드란?
- 정의: 메서드 단위에서 타입 매개변수를 선언하여 다양한 타입을 처리
- 형식: <T> T methodName(T t)
- 타입 인자 전달 시점: 메서드 호출 시점에 타입이 결정됨

| 항목               | 제네릭 타입                        | 제네릭 메서드                              |
|--------------------|------------------------------------|--------------------------------------------|
| 정의 위치          | 클래스 선언부                      | 메서드 선언부                              |
| 적용 범위          | 클래스 전체                        | 해당 메서드만                              |
| 타입 인자 전달 시점 | 객체 생성 시점                     | 메서드 호출 시점                           |
| 예시               | `class Box<T>`                     | `public static <T> T method(T t)`          |
| static 메서드 적용 | ❌ 불가능 (타입 매개변수 사용 불가) | ✅ 가능 (제네릭 메서드로 정의해야 함)       |
| 타입 추론 가능 여부 | ❌ (생성 시 명시 필요)              | ✅ (컴파일러가 인자 기반으로 추론 가능)     |


## 🧪 예제 요약
### ✅ 기본 메서드
```java
public static Object objMethod(Object obj)
```

- 어떤 타입이든 Object로 처리
- 타입 안전성 없음

### ✅ 제네릭 메서드
```java
public static <T> T genericMethod(T t)
```

- 호출 시점에 타입 결정
- 타입 추론 가능

### ✅ 타입 제한된 제네릭 메서드
```java
public static <T extends Number> T numberMethod(T t)
```

- Number와 그 자식만 허용 (Integer, Double, Long 등)
- String 등은 컴파일 오류

### 🧠 타입 추론
```java
Integer result = GenericMethod.genericMethod(i); // 타입 추론됨
```

- 컴파일러가 전달된 인자와 반환 타입을 보고 자동으로 타입 결정
- <Integer> 생략 가능

## 🐾 제네릭 메서드 활용 예: AnimalHospital 기능 분리
```java
public static <T extends Animal> void checkup(T t)
public static <T extends Animal> T getBigger(T t1, T t2)
```

- Animal을 상한으로 제한하여 기능 사용 가능
- getName(), sound() 등 안전하게 호출 가능

## ⚠️ 제네릭 타입 vs 제네릭 메서드 우선순위
```java
class ComplexBox<T extends Animal> {
    public <T> T printAndReturn(T t) { ... }
}
```

- 클래스의 T와 메서드의 <T>는 서로 다른 타입 매개변수
- 메서드의 <T>가 우선 적용됨 → 상한 없음 → Object로 취급됨
- t.getName() 호출 불가

## ✅ 해결 방법: 이름 충돌 피하기
```java
public <Z> Z printAndReturn(Z z) { ... }
```

- 메서드의 타입 매개변수 이름을 다르게 설정하여 혼동 방지

| 항목                     | 설명                                               |
|--------------------------|----------------------------------------------------|
| `<T> T method(T t)`      | 기본 제네릭 메서드 문법. 호출 시점에 타입 결정     |
| `<T extends Animal>`     | 타입 매개변수 제한. `Animal` 또는 그 자식만 허용   |
| 타입 추론                | 컴파일러가 인자 타입을 보고 자동으로 타입 결정     |
| static 메서드 적용       | 제네릭 타입은 불가 → 제네릭 메서드로만 가능        |
| 인스턴스 메서드 적용     | 제네릭 타입과 제네릭 메서드 모두 사용 가능         |
| 이름 충돌 주의           | 클래스와 메서드의 타입 매개변수 이름은 다르게 설정 |

---
# 타입 매개변수 제한 흐름 요약
## 1️⃣ DogHospital / CatHospital (타입 고정)
- 구조: 각각 Dog, Cat 타입에 고정된 병원 클래스
- 장점: 타입 안전성 O (다른 동물 넣으면 컴파일 오류)
- 단점: 코드 중복 심함, 재사용성 X
```java
DogHospital dogHospital = new DogHospital();
dogHospital.set(new Dog(...)); // ✅
dogHospital.set(new Cat(...)); // ❌ 컴파일 오류
```


## 2️⃣ AnimalHospitalV1 (다형성 기반)
- 구조: Animal 타입으로 통합
- 장점: 코드 재사용 O
- 단점: 타입 안전성 X (개 병원에 고양이 넣어도 컴파일 오류 없음)
```java
AnimalHospitalV1 dogHospital = new AnimalHospitalV1();
dogHospital.set(new Cat(...)); // ⚠️ 컴파일은 되지만 논리 오류 가능
Dog dog = (Dog) dogHospital.getBigger(...); // ⚠️ 다운캐스팅 필요
```


## 3️⃣ AnimalHospitalV2<T> (제네릭 도입)
- 구조: 제네릭 <T> 사용
- 장점: 코드 재사용 O
- 단점: 타입 안전성 X, 기능 제한 O
- T가 어떤 타입인지 알 수 없기 때문에 getName(), sound() 호출 불가
- Integer, Object도 타입 인자로 들어올 수 있음
```java
AnimalHospitalV2<Dog> dogHospital = new AnimalHospitalV2<>();
AnimalHospitalV2<Integer> intHospital = new AnimalHospitalV2<>(); // ❌ 논리 오류
```

| 버전               | 코드 재사용성 | 타입 안전성 | 기능 사용 가능성 | 문제 요약                         |
|--------------------|----------------|--------------|------------------|-----------------------------------|
| DogHospital / CatHospital | ❌             | ✅           | ✅               | 클래스 중복 많음                  |
| AnimalHospitalV1   | ✅             | ❌           | ✅               | 타입 혼동 가능, 다운캐스팅 필요   |
| AnimalHospitalV2<T> | ✅            | ❌           | ❌               | 타입 제한 없음, 기능 호출 불가    |



## ✅ 해결 방향: 타입 매개변수 제한
다음 단계에서는 제네릭에 타입 제한을 걸어야 합니다:
```java
public class AnimalHospitalV3<T extends Animal> { ... }
```
- T는 반드시 Animal 또는 그 자식 타입만 가능
- getName(), sound() 등 Animal의 기능을 안전하게 사용 가능
- Integer, Object 같은 무관한 타입은 컴파일 오류 발생



## 🧠 타입 매개변수 제한 요약: AnimalHospitalV3
### 📌 핵심 문법
```java
public class AnimalHospitalV3<T extends Animal> { ... }
```

- `T extends Animal` → T는 반드시 Animal 또는 그 자식 클래스여야 함
- 컴파일러는 T가 Animal의 기능을 가진다고 확신할 수 있음
- getName(), getSize(), sound() 같은 메서드 사용 가능

| 항목                        | 설명                                                                 |
|-----------------------------|----------------------------------------------------------------------|
| `DogHospital.set(cat)`       | ❌ 컴파일 오류 발생 → 잘못된 타입 입력 방지                          |
| `T`                         | `T extends Animal` → 타입 매개변수 제한으로 타입 안전성 확보          |
| `Animal`                     | `Animal`의 기능 (`getName()`, `sound()`, `getSize()`) 사용 가능       |
| `Dog` / `Cat` / `Animal`         | ✅ 타입 인자로 사용 가능                                              |
| `Integer` / `Object`           | ❌ 타입 제한으로 컴파일 오류 발생 → 논리 오류 사전 차단               |


## 🧪 실행 예시
```java
AnimalHospitalV3<Dog> dogHospital = new AnimalHospitalV3<>();
dogHospital.set(new Dog("멍멍이1", 100));
dogHospital.checkup();
Dog biggerDog = dogHospital.getBigger(new Dog("멍멍이2", 200));
```

- Dog 타입만 받도록 제한되어 있어 안전하게 작동
- getBigger()는 Dog 타입을 반환하므로 캐스팅 필요 없음

## ❗ 기존 문제와 해결
| 문제 유형             | 이전 방식 (제한 없음) | 개선 방식 (`T extends Animal`) | 설명                                               |
|----------------------|------------------------|-------------------------------|----------------------------------------------------|
| 타입 안전성          | T                      | T extends Animal              | `T`는 아무 타입 가능 → `Animal` 자식만 허용       |
| 기능 사용 가능성     | TObject                | Animal                        | `Object` 기능만 사용 가능 → `Animal` 기능 사용 가능 |
| 타입 인자 제한       | Integer, Object 가능   | Animal만 가능                 | 무관한 타입 차단 → 논리 오류 사전 방지            |


## 🧾 결론
제네릭에 **타입 매개변수 상한 (extends)** 을 지정함으로써:
- ✅ 타입 안전성 확보
- ✅ 코드 재사용성 향상
- ✅ 기능 호출 가능성 확보
→ 실무에서 제네릭을 사용할 때 꼭 기억해야 할 핵심 패턴입니다.

---


## 📦 제네릭 메서드란?
- 정의: 메서드 단위에서 타입 매개변수를 선언하여 다양한 타입을 처리
- 형식: <T> T methodName(T t)
- 타입 인자 전달 시점: 메서드 호출 시점에 타입이 결정됨

| 항목               | 제네릭 타입                        | 제네릭 메서드                              |
|--------------------|------------------------------------|--------------------------------------------|
| 정의 위치          | 클래스 선언부                      | 메서드 선언부                              |
| 적용 범위          | 클래스 전체                        | 해당 메서드만                              |
| 타입 인자 전달 시점 | 객체 생성 시점                     | 메서드 호출 시점                           |
| 예시               | `class Box<T>`                     | `public static <T> T method(T t)`          |
| static 메서드 적용 | ❌ 불가능 (타입 매개변수 사용 불가) | ✅ 가능 (제네릭 메서드로 정의해야 함)       |
| 타입 추론 가능 여부 | ❌ (생성 시 명시 필요)              | ✅ (컴파일러가 인자 기반으로 추론 가능)     |


## 🧪 예제 요약
### ✅ 기본 메서드
```java
public static Object objMethod(Object obj)
```

- 어떤 타입이든 Object로 처리
- 타입 안전성 없음

### ✅ 제네릭 메서드
```java
public static <T> T genericMethod(T t)
```

- 호출 시점에 타입 결정
- 타입 추론 가능

### ✅ 타입 제한된 제네릭 메서드
```java
public static <T extends Number> T numberMethod(T t)
```

- Number와 그 자식만 허용 (Integer, Double, Long 등)
- String 등은 컴파일 오류

### 🧠 타입 추론
```java
Integer result = GenericMethod.genericMethod(i); // 타입 추론됨
```

- 컴파일러가 전달된 인자와 반환 타입을 보고 자동으로 타입 결정
- <Integer> 생략 가능

## 🐾 제네릭 메서드 활용 예: AnimalHospital 기능 분리
```java
public static <T extends Animal> void checkup(T t)
public static <T extends Animal> T getBigger(T t1, T t2)
```

- Animal을 상한으로 제한하여 기능 사용 가능
- getName(), sound() 등 안전하게 호출 가능

## ⚠️ 제네릭 타입 vs 제네릭 메서드 우선순위
```java
class ComplexBox<T extends Animal> {
    public <T> T printAndReturn(T t) { ... }
}
```

- 클래스의 T와 메서드의 <T>는 서로 다른 타입 매개변수
- 메서드의 <T>가 우선 적용됨 → 상한 없음 → Object로 취급됨
- t.getName() 호출 불가

## ✅ 해결 방법: 이름 충돌 피하기
```java
public <Z> Z printAndReturn(Z z) { ... }
```

- 메서드의 타입 매개변수 이름을 다르게 설정하여 혼동 방지

| 항목                     | 설명                                               |
|--------------------------|----------------------------------------------------|
| `<T> T method(T t)`      | 기본 제네릭 메서드 문법. 호출 시점에 타입 결정     |
| `<T extends Animal>`     | 타입 매개변수 제한. `Animal` 또는 그 자식만 허용   |
| 타입 추론                | 컴파일러가 인자 타입을 보고 자동으로 타입 결정     |
| static 메서드 적용       | 제네릭 타입은 불가 → 제네릭 메서드로만 가능        |
| 인스턴스 메서드 적용     | 제네릭 타입과 제네릭 메서드 모두 사용 가능         |
| 이름 충돌 주의           | 클래스와 메서드의 타입 매개변수 이름은 다르게 설정 |

---

## 🧭 와일드카드란?
- ? 기호를 사용해 제네릭 타입 인자에 유연성을 부여
- 이미 만들어진 제네릭 타입을 매개변수로 받을 때 사용
- 제네릭 타입이나 제네릭 메서드를 정의하는 것이 아님

## 와일드카드 종류
| 종류             | 문법                     | 허용되는 타입 예시                         |
|----------------------------|--------------------------|--------------------------------------------|
| 비제한 와일드카드          | `Box<?>`                 | `Box<Object>`, `Box<Dog>`, `Box<Cat>` 등 모든 타입 |
| 상한 와일드카드 (`extends`) | `Box<? extends Animal>`  | `Box<Animal>`, `Box<Dog>`, `Box<Cat>`      |
| 하한 와일드카드 (`super`)   | `Box<? super Animal>`    | `Box<Animal>`, `Box<Object>`               |


| 항목                     | 제네릭 메서드 `<T>`                  | 와일드카드 `?`                          |
|--------------------------|--------------------------------------|----------------------------------------|
| 타입 선언 위치           | 메서드 선언부                        | 매개변수 타입 지정 시                  |
| 타입 지정 방식           | 호출 시점에 타입 인자 전달           | 컴파일 시점에 타입 추론               |
| 타입 이름                | `T`                                  | `?`                                    |
| 기능 호출 가능 여부      | `getName()`, `sound()` 가능 (`T extends Animal`) | `Animal` 타입으로 기능 호출 가능 (`? extends Animal`) |
| 반환 타입 지정 가능      | ✅ `T` 그대로 반환 가능               | ❌ `Animal` 등 상한 타입으로만 반환    |


## 📦 예제 비교
### ✅ 제네릭 메서드
```java
static <T extends Animal> T printAndReturnGeneric(Box<T> box)
```

- Dog dog = printAndReturnGeneric(dogBox) → 정확한 타입 반환

### ✅ 와일드카드
```java
static Animal printAndReturnWildcard(Box<? extends Animal> box)
```
- Animal animal = printAndReturnWildcard(dogBox) → 상한 타입으로 반환

### 📌 하한 와일드카드 예시
```java
static void writeBox(Box<? super Animal> box)


- Box<Object> ✅
- Box<Animal> ✅
- Box<Dog> ❌
- Box<Cat> ❌
→ Animal 이상의 타입만 허용, 하위 타입은 불가
```

## 🧾 정리
| 와일드카드 문법         | 의미 설명                                 |
|--------------------------|--------------------------------------------|
| `?`                      | 모든 타입 허용 (`? == <? extends Object>`) |
| `? extends Type`         | `Type` 및 그 하위 타입만 허용              |
| `? super Type`           | `Type` 및 그 상위 타입만 허용              |

---


# 🧽 타입 이레이저란?
- "이레이저(Eraser)"는 지우개라는 뜻
- 자바의 제네릭은 컴파일 시점에만 존재하고, 컴파일 후에는 타입 정보가 삭제됨
- .java → 타입 매개변수 존재  
.class → 타입 매개변수 제거됨

## 🔄 컴파일 전후 변화 예시
### 📦 컴파일 전: 제네릭 타입 선언
```java
public class GenericBox<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

### 🧱 컴파일 후: 타입 정보 제거
```java
public class GenericBox {
    private Object value;
    public void set(Object value) { this.value = value; }
    public Object get() { return value; }
}
```

- T → Object로 변환됨
- 반환 시 컴파일러가 자동으로 다운캐스팅 코드 추가
```java
GenericBox box = new GenericBox();
box.set(10);
Integer result = (Integer) box.get(); // 컴파일러가 캐스팅 추가
```


## 🐾 타입 제한이 있는 경우
### 컴파일 전
```java
public class AnimalHospitalV3<T extends Animal> {
    private T animal;
    public T getBigger(T target) { ... }
}
```

### 컴파일 후
```java
public class AnimalHospitalV3 {
    private Animal animal;
    public Animal getBigger(Animal target) { ... }
}
```

- T extends Animal → Animal로 대체됨
- 반환 시 Dog로 캐스팅 필요 → 컴파일러가 자동 추가

### 🚫 런타임 제약
제네릭 타입은 런타임에 존재하지 않기 때문에 다음과 같은 코드는 불가능:
```java
class EraserBox<T> {
    public boolean instanceCheck(Object param) {
        return param instanceof T; // ❌ 오류
    }
    public T create() {
        return new T(); // ❌ 오류
    }
}
```

- T는 런타임에 Object로 변환됨
- instanceof T → 항상 Object와 비교됨 → 의미 없음
- new T() → new Object()로 처리됨 → 생성 불가

## ✅ 요약
| 항목                     | 설명                                                   |
|--------------------------|--------------------------------------------------------|
| 타입 이레이저 결과       | `T`는 컴파일 후 `Object`로 변환됨 (`T → Object`)       |
| 타입 제한이 있는 경우    | `T extends Animal` → `Animal`으로 변환됨               |
| 반환 시 캐스팅 처리      | 컴파일러가 자동으로 다운캐스팅 코드 추가               |
| 런타임 타입 정보 없음    | `Box<Integer>` → `Box`로 변환됨, 타입 정보 사라짐       |
| `instanceof T` 불가      | `T`는 런타임에 `Object`로 처리되므로 비교 의미 없음     |
| `new T()` 불가           | 런타임에 `new Object()`가 되어버려 생성 불가능          |

---


# 🧪 문제와 풀이 요약

## 상속 전제
```java
public class Marine extends BioUnit
public class Zealot extends BioUnit
public class Zergling extends BioUnit
```

## 1️⃣ 제네릭 메서드 + 상한 제한
### 📌 목표: 두 유닛 중 체력이 높은 유닛 반환
### ✅ 핵심: 메서드에 <T extends BioUnit> 선언 → 입력과 반환 타입 일치

```java
public static <T extends BioUnit> T maxHp(T t1, T t2)
```

### ✅ 핵심 클래스: UnitUtil
```java
public class UnitUtil {
    public static <T extends BioUnit> T maxHp(T t1, T t2) {
        return t1.getHp() > t2.getHp() ? t1 : t2;
    }
}
```

### 🧪 사용 예시
```java
Marine m1 = new Marine("마린1", 40);
Marine m2 = new Marine("마린2", 50);
Marine result = UnitUtil.maxHp(m1, m2); // 반환 타입: Marine

Zealot z1 = new Zealot("질럿1", 100);
Zealot z2 = new Zealot("질럿2", 150);
Zealot result = UnitUtil.maxHp(z1, z2); // 반환 타입: Zealot

```

- 타입 안전성 확보
- 다양한 유닛 타입에 재사용 가능

## 2️⃣ 제네릭 타입 + 상한 제한
### 📌 목표: 셔틀에 유닛을 태우고 정보 출력
### ✅ 핵심: 클래스에 <T extends BioUnit> 선언 → 유닛 기능 사용 가능
```java
public class Shuttle<T extends BioUnit>
```

### ✅ 핵심 클래스: Shuttle
```java
public class Shuttle<T extends BioUnit> {
    private T unit;

    public void in(T t) {
        unit = t;
    }

    public T out() {
        return unit;
    }

    public void showInfo() {
        System.out.println("이름: " + unit.getName() + ", HP: " + unit.getHp());
    }
}
```

### 🧪 사용 예시
```java
Shuttle<Marine> shuttle1 = new Shuttle<>();
shuttle1.in(new Marine("마린", 40));
shuttle1.showInfo(); // 이름: 마린, HP: 40

Shuttle<Zergling> shuttle2 = new Shuttle<>();
shuttle2.in(new Zergling("저글링", 35));
shuttle2.showInfo(); // 이름: 저글링, HP: 3
```

- in(), out()으로 유닛 관리
- showInfo()에서 getName(), getHp() 호출 가능

## 3️⃣ 제네릭 메서드 vs 와일드카드
### 📌 목표: 셔틀에 탑승한 유닛 정보 출력
| 항목                   | 제네릭 메서드 `<T extends BioUnit>`         | 와일드카드 `<? extends BioUnit>`              |
|------------------------|----------------------------------------------|-----------------------------------------------|
| 정의 위치              | 메서드 선언부에 `<T>`                        | 매개변수 타입에 `? extends BioUnit` 사용      |
| 타입 지정 방식         | 호출 시점에 타입 인자 추론 또는 명시         | 컴파일 시점에 타입 추론                       |
| 반환 타입              | `T` 그대로 반환 가능                         | `BioUnit` 타입으로만 반환 가능                |
| 기능 호출 가능 여부    | `T`의 기능 호출 가능 (`getName()`, `getHp()`) | `BioUnit` 기능 호출 가능                      |
| 타입 제어 유연성       | 높음 (입력과 출력 타입 일치 가능)            | 낮음 (출력 타입은 상한 타입으로 고정됨)       |
| 사용 목적              | 타입을 직접 제어하고 반환까지 처리할 때      | 단순 조회나 출력 등 타입 제어가 필요 없는 경우 |

---

# 실무 사용 

## 📦 실무 활용 정리
| 항목                     | 설명                                                   |
|--------------------------|--------------------------------------------------------|
| 실무에서의 제네릭 사용   | 직접 설계보다는 라이브러리나 프레임워크 활용 중심       |
| 주요 활용 영역           | 컬렉션 프레임워크에서 가장 많이 사용됨                  |
| 대표 예시                | `List<T>`, `Map<K, V>`, `Set<T>`, `Queue<T>` 등         |
| 사용 목적                | 타입 안전성 확보, 코드 재사용, 컴파일 시점 오류 방지     |
| 복잡한 제네릭 설계       | 실무에서는 드물고, 필요 시 학습 권장                     |
| 실무 학습 방법           | 컬렉션 사용을 통해 자연스럽게 제네릭 구조 익히기         |


## ✅ 결론
- 제네릭은 타입 안전성과 코드 재사용성을 높여주는 강력한 도구
- 복잡하게 설계하기보다는 간단하고 명확하게 활용하는 것이 실무에서는 더 중요
- 지금까지 배운 제네릭 문법과 패턴은 실무에서 충분히 활용 가능

---


