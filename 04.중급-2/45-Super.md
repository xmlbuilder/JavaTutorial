# super
super 키워드의 활용을 명확하게 정리.  
이 코드는 상속 구조에서 부모 클래스의 생성자와 메서드를 자식 클래스에서 어떻게 호출하는지를 보여주는 예제.

## 🔍 super 키워드란?
super는 자식 클래스에서 **부모 클래스의 멤버(생성자, 메서드, 필드)** 를 참조할 때 사용하는 키워드입니다.

## ✅ 코드 분석
```java
class Parent {
    public Parent(){
        System.out.println("Parent Constructor");
    }
    public void someMessage(){
        System.out.println("Parent Message");
    }
}
```
- Parent 클래스는 기본 생성자와 someMessage() 메서드를 가짐

```java
class Child extends Parent {
    public Child(){
        super(); // 부모 생성자 호출
    }
    @Override
    public void someMessage(){
        super.someMessage(); // 부모 메서드 호출
        System.out.println("Child Message");
    }
}
```

- Child 클래스는 Parent를 상속
- super()는 부모 생성자 호출
- super.someMessage()는 부모 메서드 호출
- @Override는 부모 메서드를 재정의

## 🧾 실행 결과
```
Parent Constructor
Parent Message
Child Message
```

- new Child() → super() → "Parent Constructor"
- child.someMessage() → super.someMessage() → "Parent Message" → "Child Message"

| 구문                  | 사용 위치         | 설명                                                   |
|-----------------------|------------------|--------------------------------------------------------|
| `super()`             | 자식 클래스 생성자 | 부모 클래스의 생성자를 명시적으로 호출할 때 사용         |
| `super.someMethod()`  | 자식 클래스 메서드 | 오버라이딩된 부모 메서드를 호출하여 원래 동작을 유지하거나 확장할 때 |
| `super.someField`     | 자식 클래스 내부   | 부모 클래스의 필드에 접근할 때 사용 (필요 시)             |

## 예시 코드 요약
```java
class Parent {
    public void greet() {
        System.out.println("Hello from Parent");
    }
}

class Child extends Parent {
    public Child() {
        super(); // 부모 생성자 호출
    }

    @Override
    public void greet() {
        super.greet(); // 부모 메서드 호출
        System.out.println("Hello from Child");
    }
}
```


## 🧠 팁
- `super()` 는 `자식 생성자` 의 `첫 줄` 에서만 호출 가능
- super는 상속 관계에서 부모의 기능을 활용하거나 확장할 때 핵심적인 도구
- this는 현재 객체 참조, super는 부모 객체 참조
