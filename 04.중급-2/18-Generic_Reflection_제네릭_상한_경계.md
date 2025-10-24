# Generic / Reflection / 제네릭 상한 경계
이 코드는 **제네릭(Generic)** 과 **리플렉션(Reflection)**, 그리고 **제네릭의 상한 경계(Bounded Type Parameters)** 를  
활용한 예제입니다. 아래에 각 클래스와 메서드를 단계별로 해석하고, 전체적으로 어떤 구조와 의미를 갖는지 설명.

## 🧩 1. GenerateGenericTest 클래스
### 📦 목적
리플렉션을 사용해 제네릭 타입의 객체를 생성하는 테스트 코드입니다.

### 🔍 코드 분석
```java
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
```
- 리플렉션 API를 사용하기 위한 import입니다.

```java
public class GenerateGenericTest {
```
- 메인 클래스 선언

```java
    public static void main(String[] args){
        String str = genericMethod(String.class);
        System.out.println(str == null);
    }
```
- genericMethod를 호출해 String 객체를 생성하려고 시도
- 생성된 객체가 null인지 확인하여 출력

```java
    public static <T> T genericMethod(Class<T> cls)  {
        try {
            Constructor<T> constructor = cls.getDeclaredConstructor();
            T t = (T) constructor.newInstance();
            return t;
        } catch (Exception e){
            e.printStackTrace();
        }
        return null;
    }
```
#### 🧠 동작 설명
- Class<T>를 받아 해당 클래스의 기본 생성자를 찾음
- newInstance()로 객체 생성


## 🐾 2. Animal, Cat, Dog 클래스
### 📦 목적
다형성을 활용한 추상 클래스와 그 구현체들입니다.
```java
abstract class Animal {
    public abstract String getSound();
}
```
- Animal은 추상 클래스이며, 모든 동물은 getSound()를 구현해야 함

```java
class Cat extends Animal {
    @Override
    public String getSound() {
        return "Meow";
    }
}

class Dog extends Animal {
    @Override
    public String getSound() {
        return "Woof";
    }
}
```
- 각각 고양이와 개의 울음소리를 반환

## 📦 3. AnimalContainer<T extends Animal>
### 📦 목적
제네릭을 사용해 Animal의 하위 타입만 담을 수 있는 컨테이너 클래스입니다.
```java
class AnimalContainer<T extends Animal> {
    private final Collection<T> col;
    public AnimalContainer(){
        col = new ArrayList<>();
    }
    public void add(T t){
        col.add(t);
    }
    public void printAllSounds(){
        for( T t : col){
            System.out.println(t.getSound());
        }
    }
}
```

### 🔍 동작 설명
- T extends Animal → T는 반드시 Animal의 하위 클래스여야 함
- add()로 동물을 추가하고, printAllSounds()로 모든 동물의 소리를 출력

## 🧪 4. BoundedGenericTest 클래스
```java
public class BoundedGenericTest {
    public static void main(String[] args){
        AnimalContainer<Cat> cats = new AnimalContainer<>();
    }
}
```

### 📦 목적
- AnimalContainer를 Cat 타입으로 생성하는 예제입니다.

## 🧠 전체 구조 요약

| 클래스명               | 상속 또는 관계 대상     | 설명                                |
|------------------------|--------------------------|-------------------------------------|
| `GenerateGenericTest`  |                          | 리플렉션으로 제네릭 객체 생성 테스트 |
| `Animal`               |                          | 추상 클래스, `getSound()` 정의       |
| `Cat`, `Dog`           | `Animal`                 | `Animal`을 상속한 구체 클래스        |
| `AnimalContainer<T>`   | `T extends Animal`       | `Animal` 하위 타입만 담는 제네릭 컨테이너 |
| `BoundedGenericTest`   | `AnimalContainer` 사용   | `AnimalContainer<Cat>` 생성 예제     |

---

## printAllSounds

- ✅ printAllSounds()를 활용한 실행 예제
- ✅ genericMethod()의 수정 버전 (리플렉션 오류 없이 동작하도록 개선)

## ✅ 1. printAllSounds() 실행 예제
아래는 AnimalContainer에 Cat과 Dog 객체를 추가하고, printAllSounds()로 소리를 출력하는 예제입니다:
```java
public class BoundedGenericTest {
    public static void main(String[] args){
        AnimalContainer<Animal> animals = new AnimalContainer<>();
        animals.add(new Cat());
        animals.add(new Dog());
        animals.printAllSounds();  // 출력: Meow, Woof
    }
}
```

### 🔍 설명
- AnimalContainer<Animal>로 선언하면 다양한 하위 타입을 담을 수 있음.
- add()로 Cat, Dog 객체를 추가하고
- printAllSounds()로 각각의 getSound() 결과를 출력합니다.

## ✅ 전체 예제 코드
```java
import java.lang.reflect.Constructor;
import java.util.ArrayList;
import java.util.Collection;

// 추상 클래스
abstract class Animal {
    public abstract String getSound();
}

// 구현 클래스
class Cat extends Animal {
    @Override
    public String getSound() {
        return "Meow";
    }
}

class Dog extends Animal {
    @Override
    public String getSound() {
        return "Woof";
    }
}

// 제네릭 컨테이너
class AnimalContainer<T extends Animal> {
    private final Collection<T> col;

    public AnimalContainer() {
        col = new ArrayList<>();
    }

    public void add(T t) {
        col.add(t);
    }

    public void printAllSounds() {
        for (T t : col) {
            System.out.println(t.getSound());
        }
    }
}

// 리플렉션 기반 제네릭 객체 생성
class Generator {
    public static <T> T genericMethod(Class<T> cls) {
        try {
            Constructor<T> constructor = cls.getDeclaredConstructor();
            constructor.setAccessible(true);
            return constructor.newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}

// 실행 클래스
public class BoundedGenericTest {
    public static void main(String[] args) {
        AnimalContainer<Animal> animals = new AnimalContainer<>();

        // 리플렉션으로 Cat과 Dog 객체 생성 후 추가
        Animal cat = Generator.genericMethod(Cat.class);
        Animal dog = Generator.genericMethod(Dog.class);

        animals.add(cat);
        animals.add(dog);

        animals.printAllSounds(); // 출력: Meow, Woof
    }
}
```

## 🔍 핵심 포인트 요약

| 구성 요소                  | 역할 또는 설명                          |
|---------------------------|------------------------------------------|
| `Generator.genericMethod()` | 리플렉션을 활용해 제네릭 객체를 동적으로 생성 |
| `AnimalContainer<Animal>`   | 다양한 동물 타입을 담을 수 있는 제네릭 컨테이너 |
| `add()` / `printAllSounds()` | 동물 객체를 추가하고, 각 동물의 소리를 출력     |

---



