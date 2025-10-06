# 클래스

## 1️⃣ 클래스가 필요한 이유
### 문제: 학생 정보 출력
```java
String student1Name = "학생1";
int student1Age = 15;
int student1Grade = 90;
```

- 학생 수가 늘어날수록 변수 선언과 출력 코드가 증가
- 유지보수 어려움

### 개선: 배열 사용
```java
String[] names = {"학생1", "학생2"};
int[] ages = {15, 16};
int[] grades = {90, 80};
```

- 반복문으로 출력 가능
- 하지만 데이터가 분산되어 관리가 어려움

## 2️⃣ 클래스 정의와 객체 생성
### 클래스 정의
```java
public class Student {
    String name;
    int age;
    int grade;
}
```

- 클래스는 객체를 생성하기 위한 설계도
- 멤버 변수(필드): 객체가 가지는 속성
### 객체 생성
```java
Student student1 = new Student();
student1.name = "학생1";
student1.age = 15;
student1.grade = 90;
```

- new Student()로 객체 생성
- . 연산자로 멤버 변수 접근

## 3️⃣ 객체와 참조값
- 객체는 메모리에 생성되고, 변수에는 참조값이 저장됨
- 참조값을 통해 객체에 접근
```java
Student student1 = new Student(); // x001
Student student2 = new Student(); // x002
```

- student1은 x001 객체를 참조
- student2는 x002 객체를 참조
변수에는 인스턴스 자체가 아니라 참조값이 들어있다


## 4️⃣ 배열과 객체
### 객체 배열 선언
```java
Student[] students = new Student[2];
students[0] = student1;
students[1] = student2;
```

- 배열 요소도 참조값을 저장
- students[0] → x001 → student1 객체
### 객체 배열 출력
```java
System.out.println(students[0].name);
System.out.println(students[1].name);
```


## 5️⃣ 객체 출력
### for문 사용
```java
for (int i = 0; i < students.length; i++) {
    System.out.println("이름:" + students[i].name + " 나이:" + students[i].age + " 성적:" + students[i].grade);
}
```

### 향상된 for문
```java
for (Student s : students) {
    System.out.println("이름:" + s.name + " 나이:" + s.age + " 성적:" + s.grade);
}
```
코드가 간결하고 가독성이 좋음


## 6️⃣ 문제와 풀이
### 🎬 영화 리뷰 관리하기 1
```java
public class MovieReview {
    String title;
    String review;
}


MovieReview inception = new MovieReview();
inception.title = "인셉션";
inception.review = "인생은 무한 루프";
```

### 🎬 영화 리뷰 관리하기 2 (배열 도입)
```java
MovieReview[] reviews = new MovieReview[2];
reviews[0] = inception;
reviews[1] = aboutTime;

for (MovieReview review : reviews) {
    System.out.println("영화 제목: " + review.title + ", 리뷰: " + review.review);
}
```


### 🛒 상품 주문 시스템 개발
```java
public class ProductOrder {
    String productName;
    int price;
    int quantity;
}


ProductOrder[] orders = new ProductOrder[3];
orders[0] = order1;
orders[1] = order2;
orders[2] = order3;

int totalAmount = 0;
for (ProductOrder order : orders) {
    System.out.println("상품명: " + order.productName + ", 가격: " + order.price + ", 수량: " + order.quantity);
    totalAmount += order.price * order.quantity;
}
System.out.println("총 결제 금액: " + totalAmount);
```
## 7️⃣ 📥 정리
| 개념         | 설명                                      |
|--------------|-------------------------------------------|
| 클래스       | 객체를 생성하기 위한 설계도                |
| 객체         | 클래스 기반으로 생성된 실체                |
| 인스턴스     | 특정 클래스에서 생성된 객체                |
| 참조값       | 객체의 메모리 주소                        |
| 배열         | 객체들을 하나의 구조로 묶어 관리           |
| 향상된 for문 | 배열 요소를 간결하게 순회하는 반복문       |

클래스는 자바의 핵심이며, 데이터를 구조화하고 객체지향 프로그래밍으로 나아가기 위한 필수 개념입니다.  
배열과 함께 사용하면 객체를 효율적으로 관리할 수 있습니다.

---



