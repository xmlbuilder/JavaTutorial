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
