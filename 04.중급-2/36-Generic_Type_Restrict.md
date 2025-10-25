# Generic Type Restrict

## 📌 1. 제네릭 타입에 제한 두기 (GenericTypeRestrictTest)

## ✅ 클래스 수준 제한
```java
public class FileStore<T extends OutputStream>
```

- T는 반드시 OutputStream을 상속한 타입이어야 함
- 예: FileOutputStream, BufferedOutputStream 등은 가능
- ❌ FileInputStream은 InputStream을 상속하므로 제한에 걸림

## ✅ 메서드 수준 제한
```java
public <E extends Serializable & Comparable<E>> int compare(E data1, E data2)
```

- E는 반드시 Serializable을 구현하고 Comparable<E>도 구현해야 함
- 즉, 직렬화 가능하면서 비교 가능한 객체만 전달 가능
- 예: Date, CompData는 가능
- ❌ ArrayList<String>은 Comparable을 구현하지 않으므로 제한됨

## 🔍 실전 의도
- 클래스 제한: FileStore는 출력 스트림만 다루도록 설계 → 타입 안정성 확보
- 메서드 제한: compare()는 비교 가능한 객체만 처리 → 런타임 오류 방지

## 📌 2. 와일드카드 활용 (WildcardTest)
### ✅ 주요 와일드카드 종류

| 문법                   | 설명 |
|------------------------|------|
| `List<?>`              | 모든 타입 허용. 읽기만 가능, 쓰기 불가 (`add()` 불가) |
| `List<? extends T>`    | T 또는 T의 하위 타입만 허용. 읽기 가능, 쓰기 불가 |
| `List<? super T>`      | T 또는 T의 상위 타입만 허용. 쓰기 가능, 읽기 시 `Object`로 반환 |


### ✅ 예제 분석
```java
List<?> list1 = getList();         // 읽기 가능, 쓰기 불가
List<? extends String> list2;      // String 또는 하위 타입만 허용
List<? super Integer> list3;       // Integer 또는 상위 타입 허용 → add 가능
```

### ✅ 제네릭 메서드 활용
```java
private static <E> void replaceHelper(List<E> list, int i)
```

- List<?>는 직접 수정 불가 → replaceHelper()에서 타입을 추론해 수정
- 와일드카드의 한계를 제네릭 메서드로 우회하는 전략

## ✅ 실전에서 왜 중요한가?

| 목적                     | 설명 |
|--------------------------|------|
| 타입 안정성 확보         | 잘못된 타입 사용을 컴파일 시점에 차단하여 런타임 오류 예방 |
| API 설계 명확화          | 메서드나 클래스가 어떤 타입만 허용하는지 명확하게 표현 가능 |
| 코드 재사용성 향상       | 제네릭 메서드와 와일드카드를 통해 다양한 타입을 유연하게 처리 가능 |
| 읽기/쓰기 제어 가능      | `? extends`, `? super`를 통해 컬렉션의 읽기/쓰기 권한을 세밀하게 조절 |
| 유지보수 및 확장 용이    | 타입 제한으로 인해 코드 변경 시 영향 범위가 줄어들고 안정성 향상 |

## 📌 Java 제네릭 제한 및 와일드카드 요약

| 항목                     | 설명 |
|--------------------------|------|
| `T extends OutputStream` | 클래스 수준에서 타입 제한 (출력 스트림만 허용) |
| `<E extends Serializable & Comparable<E>>` | 메서드 수준에서 다중 인터페이스 제한 |
| `List<?>`                | 모든 타입 허용 (읽기만 가능) |
| `List<? extends T>`      | T 이하 타입 허용 (읽기만 가능) |
| `List<? super T>`        | T 이상 타입 허용 (쓰기 가능) |
| 제네릭 메서드 활용       | 와일드카드의 한계를 우회해 타입 추론 기반 수정 가능 |

- 이 개념은 Java에서 타입 안정성과 유연성을 동시에 확보할 수 있는 핵심 기술. 


## 샘플 예제
```java
package com.thj;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.io.Serializable;
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.util.ArrayList;
import java.util.Date;


public class GenericTypeRestrictTest {

    public class FileStore<T extends OutputStream>{
        public <E extends Serializable & Comparable<E>> int compare(E data1, E data2){
            return data1.compareTo(data2);
        }
    }

    public class CompData implements Comparable<CompData>, Serializable
    {
        @Override
        public int compareTo(CompData o) {
            return 0;
        }
    }

    public static void main(String[] args){

        GenericTypeRestrictTest.FileStore<FileOutputStream> f1 = new GenericTypeRestrictTest().new FileStore<>();

        Date date1 = new Date();
        Date date2 = Date.from(LocalDateTime.of(2014, 9, 1, 0, 0)
            .atZone(ZoneId.systemDefault())
            .toInstant()
        );

        int i1 = f1.compare(date1, date2);
        System.out.println(i1);

        CompData compData1 = new GenericTypeRestrictTest().new CompData();
        CompData compData2 = new GenericTypeRestrictTest().new CompData();
        int i3 = f1.compare(compData1, compData2);
    }
}

public class WildcardTest {
    public static void main(String[] args){
        List list = getList();
        System.out.println(list);

        List<?> list1 = getList();
        System.out.println(list1);
        System.out.println(list1.get(0));
        String str = (String)list1.get(0);
        System.out.println(str);

        List<? extends String> list2 = getList();
        String str2 = list2.get(0);
        System.out.println(str2);

        List<? extends Number> listNumber = Arrays.asList(1);
        Number number = listNumber.get(0);
        System.out.println(number);
        Number number1 = 1;

        List<? super Integer> list3 = Arrays.asList(number1);
        list3.add(1);
    }
    
    public static List getList(){
        return Arrays.asList("Java", "R", "Rust");
    }
    
    public static void replace(List<?> list, int i){
        replaceHelper(
                list, i
        );
    }
    
    private static <E> void replaceHelper(List<E> list, int i){
        list.set(i, list.get(i-1));
    }
}
```
