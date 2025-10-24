# 🧠 타입 이레이저(Type Erasure)란?
Java 컴파일러는 제네릭을 컴파일할 때 **타입 정보를 제거(erase)** 합니다. 즉:
- 컴파일 시점: 타입 체크, 제약 조건 확인
- 런타임 시점: 타입 정보는 사라지고, 대부분 Object로 처리됨
이렇게 하는 이유는 JDK 1.5 이전의 코드와 호환성 유지를 위해서임.

## 🔍 왜 배열에 제네릭 타입을 직접 사용할 수 없을까?
```java
T[] array = new T[10]; // ❌ 컴파일 오류
```

- 이유: T는 런타임에 어떤 타입인지 알 수 없기 때문에 JVM이 메모리를 안전하게 할당할 수 없음
- 해결책: Object[]를 사용하고, 필요할 때 (T)로 캐스팅


## 전체 코드
```java
public class TArray<T> {
    private Object[] items = null;
    public TArray(){
        items = new Object[10];
    }

    public TArray(int size){
        setSize(size);
    }

    public TArray(TArray<T> rhs)
    {
        if(rhs.items != null && rhs.items.length != 0){
            items = new Object[rhs.items.length];
            for(int i = 0; i<rhs.items.length; i++){
                items[i] = rhs.items[i];
            }
        }
    }

    public void copyFrom(TArray<T> rhs){
        if(rhs.items != null && rhs.items.length != 0){
            items = new Object[rhs.items.length];
            for(int i = 0; i<rhs.items.length; i++){
                items[i] = rhs.items[i];
            }
        }
    }
    public void copyFrom(T[] aData){
        if(aData != null && aData.length != 0){
            items = new Object[aData.length];
            for(int i = 0; i < aData.length; i++){
                items[i] = aData[i];
            }
        }
    }
    public int getSize(){
        return (items != null) ?  items.length : 0;
    }

    public Object[] getItems() {
        return items;
    }
    public void setItems(T[] items) {
        this.items = items;
    }
    @SuppressWarnings("unchecked")
    public T getData(int idx){
        return (T)this.items[idx];
    }
    public void setData(int idx, T data){
        this.items[idx] = data;
    }
    public void clear(){
        this.items = null;
    }
    public void setSize(int size){
        this.items = new Object[size];
    }
    public void resize(int size){
        Object[] fTmp = new Object[size];
        System.arraycopy(items, 0, fTmp, 0, size);
        this.items = fTmp;
    }
    public boolean isEmpty(){
        return (getSize() != 0);
    }
}

TArray<Float> floatArray = new TArray<>(10);
floatArray.setData(0, 11.0f);
floatArray.setData(1, 12.0f);
floatArray.setData(2, 13.0f);
floatArray.setData(3, 14.0f);
floatArray.setData(4, 15.0f);
floatArray.setData(5, 16.0f);
floatArray.setData(6, 17.0f);
floatArray.setData(7, 18.0f);
floatArray.setData(8, 19.0f);
floatArray.setData(9, 20.0f);

float a = floatArray.getData(9);
System.out.println("a = " + a);

floatArray.resize(6);
a = floatArray.getData(5);
System.out.println("a = " + a);

floatArray.setData(5, 10000.0f);
for(int i=0; i<floatArray.getSize(); i++){
    float val = floatArray.getData(i);
    System.out.println("val = " + val);
}

Object[] fArray = floatArray.getItems();
for(int i=0; i<fArray.length; i++){
    float val  = (Float)fArray[i];
    System.out.println("val = " + val);
}


```


## ✅ 예제 코드 해석: TArray<T>
### 🔧 핵심 구조
```java
private Object[] items = null;
```

- 내부 배열은 Object[]로 선언
- 제네릭 타입 T는 런타임에 사라지므로 T[]는 사용할 수 없음

### 🔁 복사 및 설정
```java
public void copyFrom(T[] aData) {
    items = new Object[aData.length];
    for (int i = 0; i < aData.length; i++) {
        items[i] = aData[i];
    }
}
```

- 외부에서 T[]를 받아 내부 Object[]에 복사
### 🔍 읽기
```java
@SuppressWarnings("unchecked")
public T getData(int idx) {
    return (T) this.items[idx];
}
```

- Object를 T로 강제 캐스팅
- 컴파일러는 경고하지만, 개발자가 타입을 보장한다고 명시

### 🧪 실행 예제 해석
```java
TArray<Float> floatArray = new TArray<>(10);
floatArray.setData(0, 11.0f);
...
float a = floatArray.getData(9);
System.out.println("a = " + a);
```

- Float 타입으로 제네릭 배열을 사용
- 내부적으로는 Object[]에 저장되고, getData()에서 Float로 캐스팅됨

### 🔄 타입 이레이저가 적용된 결과
컴파일 후 TArray<Float>는 실제로는 다음과 같이 동작합니다:
```java
public class TArray {
    private Object[] items;
    public Object getData(int idx) {
        return items[idx]; // 실제 반환은 Object
    }
}
```

- T는 사라지고, 모든 타입은 Object로 처리됨
- 제네릭은 컴파일러가 타입 안전성을 보장해주는 문법적 도구일 뿐, 런타임에는 존재하지 않음

## 📌 타입 이레이저 요약

| 개념 또는 제한사항         | 설명 또는 해결 방법           |
|----------------------------|-------------------------------|
| 타입 정보는 런타임에 소거됨 | 컴파일 시점에만 타입 체크 수행 |
| 제네릭 배열 생성 불가       | `new T[10]` → `new Object[10]`으로 대체 |
| 타입 캐스팅 필요            | 내부 저장은 `Object`, 읽을 때 `(T)`로 캐스팅 |
| 타입 안전은 컴파일러가 보장 | 런타임에는 타입 정보 없음     |

---
