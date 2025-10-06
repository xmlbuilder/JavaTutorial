## 🎯 자바 Scanner

### 🧾 Scanner 기본 사용법
```java
Scanner scanner = new Scanner(System.in);
```
### 주요 메서드
| 메서드              | 설명                          |
|---------------------|-------------------------------|
| `nextLine()`        | 문자열 입력 (엔터까지)        |
| `nextInt()`         | 정수 입력                     |
| `nextDouble()`      | 실수 입력                     |


### 🔁 Scanner 반복 예제
#### 예제 1: 문자열 반복 입력
```java
while (true) {
    System.out.print("문자열을 입력하세요(exit: 종료):");
    String str = scanner.nextLine();
    if (str.equals("exit")) {
        System.out.println("프로그램을 종료합니다.");
        break;
    }
    System.out.println("입력한 문자열: " + str);
}
```


### 🧪 문제와 풀이 1
#### 문제: 이름과 나이 입력
```java
System.out.print("당신의 이름을 입력하세요:");
String name = scanner.nextLine();

System.out.print("당신의 나이를 입력하세요:");
int age = scanner.nextInt();

System.out.println("당신의 이름은 " + name + "이고, 나이는 " + age + "살입니다.");
```



#### 문제: 홀수 짝수 판별
```java
System.out.print("하나의 정수를 입력하세요:");
int number = scanner.nextInt();

if (number % 2 == 0) {
    System.out.println("입력한 숫자 " + number + "는 짝수입니다.");
} else {
    System.out.println("입력한 숫자 " + number + "는 홀수입니다.");
}
```


#### 문제: 음식 주문 총 가격 계산
```java
System.out.print("음식 이름을 입력해주세요: ");
String foodName = scanner.nextLine();

System.out.print("음식의 가격을 입력해주세요: ");
int foodPrice = scanner.nextInt();

System.out.print("음식의 수량을 입력해주세요: ");
int foodQuantity = scanner.nextInt();

int totalPrice = foodPrice * foodQuantity;

System.out.println(foodName + " " + foodQuantity + "개를 주문하셨습니다. 총 가격은 " + totalPrice + "원입니다.");
```


#### 문제: 구구단 출력
```java
System.out.print("구구단의 단 수를 입력해주세요: ");
int n = scanner.nextInt();

System.out.println(n + "단의 구구단:");
for (int i = 1; i <= 9; i++) {
    System.out.println(n + " x " + i + " = " + (n * i));
}
```


### 🧪 문제와 풀이 2
#### 문제: 변수 값 교환
```java
int a = 10;
int b = 20;
int temp;

temp = a;
a = b;
b = temp;

System.out.println("a = " + a);
System.out.println("b = " + b);

```

#### 문제: 사이 숫자 출력
```java
System.out.print("첫 번째 숫자를 입력하세요:");
int num1 = scanner.nextInt();

System.out.print("두 번째 숫자를 입력하세요:");
int num2 = scanner.nextInt();

if (num1 > num2) {
    int temp = num1;
    num1 = num2;
    num2 = temp;
}

System.out.print("두 숫자 사이의 모든 정수:");
for (int i = num1; i <= num2; i++) {
    System.out.print(i);
    if (i != num2) {
        System.out.print(",");
    }
}
```


### 🧪 문제와 풀이 3
#### 문제: 이름과 나이 반복 입력
```java
while (true) {
    System.out.print("이름을 입력하세요 (종료를 입력하면 종료): ");
    String name = scanner.nextLine();
    if (name.equals("종료")) {
        System.out.println("프로그램을 종료합니다.");
        break;
    }
    System.out.print("나이를 입력하세요: ");
    int age = scanner.nextInt();
    scanner.nextLine(); // 줄바꿈 처리
    System.out.println("입력한 이름: " + name + ", 나이: " + age);
}

```

#### 문제: 상품 가격 계산 반복
```java
while (true) {
    System.out.print("상품의 가격을 입력하세요 (-1을 입력하면 종료): ");
    int price = scanner.nextInt();
    if (price == -1) {
        System.out.println("프로그램을 종료합니다.");
        break;
    }
    System.out.print("구매하려는 수량을 입력하세요: ");
    int quantity = scanner.nextInt();
    System.out.println("총 비용: " + (price * quantity));
}
```


### 🧪 문제와 풀이 4
#### 문제: 입력한 숫자의 합계와 평균
```java
int sum = 0;
int count = 0;

System.out.println("숫자를 입력하세요. 입력을 중단하려면 -1을 입력하세요:");
while (true) {
    int input = scanner.nextInt();
    if (input == -1) break;
    sum += input;
    count++;
}

double average = (double) sum / count;
System.out.println("입력한 숫자들의 합계: " + sum);
System.out.println("입력한 숫자들의 평균: " + average);
```


#### 문제: 상품 구매 프로그램
```java
int totalCost = 0;

while (true) {
    System.out.println("1: 상품 입력, 2: 결제, 3: 프로그램 종료");
    int option = scanner.nextInt();

    if (option == 1) {
        scanner.nextLine(); // 줄바꿈 처리
        System.out.print("상품명을 입력하세요: ");
        String product = scanner.nextLine();

        System.out.print("상품의 가격을 입력하세요: ");
        int price = scanner.nextInt();

        System.out.print("구매 수량을 입력하세요: ");
        int quantity = scanner.nextInt();

        totalCost += price * quantity;
        System.out.println("상품명:" + product + " 가격:" + price + " 수량:" + quantity + " 합계:" + (price * quantity));
    } else if (option == 2) {
        System.out.println("총 비용: " + totalCost);
        totalCost = 0;
    } else if (option == 3) {
        System.out.println("프로그램을 종료합니다.");
        break;
    } else {
        System.out.println("올바른 옵션을 선택해주세요.");
    }
}
```


### 📥 정리
- Scanner는 사용자 입력을 처리하는 핵심 도구
- 반복문과 조건문을 활용하면 실전형 프로그램을 만들 수 있음
- 문제를 직접 풀고, 코드를 따라치며 몸으로 익히는 것이 중요

