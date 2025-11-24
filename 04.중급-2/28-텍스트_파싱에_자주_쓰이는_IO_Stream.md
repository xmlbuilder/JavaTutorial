# 텍스트 파싱에 자주 쓰이는 IO Stream
아래는 Java에서 텍스트 파싱 시 자주 사용되는 I/O 스트림 클래스들을 정리한 표입니다.  
BufferedReader와 BufferedWriter는 대표적인 예이며, 다양한 조합으로 활용됩니다.  

## 샘플 코드
```java
import java.io.*;
public class ReaderWriterTest {
    public static void main(String[] args){
        String strFileName = "Output.txt";
        try(BufferedReader br = new BufferedReader(new FileReader(strFileName))){
           String str;
           while((str = br.readLine()) != null){
               System.out.println(str);
           }
        }catch (IOException e){
            e.printStackTrace();
        }
```
```java
        try(BufferedWriter wr = new BufferedWriter(new FileWriter("Sample.txt"))){
            wr.write("Sample1");
            wr.newLine();
            wr.write("Sample2");
            wr.newLine();
            wr.write("Sample3");
            wr.newLine();
            wr.write("Sample4");
            wr.newLine();

        }catch (IOException e){
            e.printStackTrace();
        }
    }
}
```

## 📦 Java 텍스트 파싱용 I/O 스트림 요약

| 클래스            | 역할 및 특징 |
|-------------------|--------------|
| `FileReader`      | 텍스트 파일을 문자 단위로 읽음 (기본 인코딩 사용) |
| `BufferedReader`  | `Reader`를 버퍼링하여 효율적으로 줄 단위 읽기 (`readLine()`) |
| `InputStreamReader` | 바이트 스트림을 문자 스트림으로 변환 (인코딩 지정 가능) |
| `FileInputStream` | 파일을 바이트 단위로 읽음 |
| `FileWriter`      | 텍스트 파일에 문자 단위로 씀 (기본 인코딩 사용) |
| `BufferedWriter`  | `Writer`를 버퍼링하여 효율적으로 줄 단위 쓰기 (`newLine()`) |
| `OutputStreamWriter` | 문자 스트림을 바이트 스트림으로 변환 (인코딩 지정 가능) |
| `FileOutputStream` | 파일에 바이트 단위로 씀 |



## 🧠 인코딩을 명시하고 싶을 때
```java
BufferedReader br = new BufferedReader(
    new InputStreamReader(new FileInputStream("Output.txt"), "UTF-8")
);
```
```java
BufferedWriter bw = new BufferedWriter(
    new OutputStreamWriter(new FileOutputStream("Sample.txt"), "UTF-8")
);
```

- InputStreamReader와 OutputStreamWriter를 사용하면 인코딩을 명시적으로 지정할 수 있음
- 기본 FileReader/FileWriter는 시스템 기본 인코딩을 사용하므로 다국어 처리 시 주의

## 📌 요약
- BufferedReader + FileReader: 줄 단위 읽기
- BufferedWriter + FileWriter: 줄 단위 쓰기
- InputStreamReader / OutputStreamWriter: 인코딩 지정 시 필수
- FileInputStream / FileOutputStream: 바이트 단위 처리

---

