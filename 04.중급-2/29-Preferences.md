# Preferences
이 코드는 Java의 Preferences API를 활용해 사용자 설정을 저장하고 변경 이벤트를 감지하는 구조를 보여줍니다.  
아래에 Preferences의 개념 설명과 함께, 샘플 코드를 단계적으로 분석.

## 🧠 Preferences란?
Java의 java.util.prefs.Preferences 클래스는 사용자 또는 시스템 설정을 저장하는 키-값 기반의 영속 저장소입니다.  
레지스트리(Windows) 또는 XML 파일(macOS/Linux)에 저장되며, 다음과 같은 특징이 있음:
- 키-값 쌍으로 설정 저장 (put, get, putBoolean, getBoolean, 등)
- 사용자 노드와 시스템 노드 구분 (userNodeForPackage, systemNodeForPackage)
- 설정 변경 및 노드 변경 이벤트 감지 가능
- 영속적 저장소이지만 파일 경로를 직접 지정하지 않음

## 소스 코드
```java
import java.util.prefs.NodeChangeEvent;
import java.util.prefs.NodeChangeListener;
import java.util.prefs.Preferences;

public class PreferenceTest {
    public static void main(String[] args){

        Preferences preferences = Preferences.userNodeForPackage(PreferenceTest.class);

        // addPreferenceChangeListener
        preferences.addPreferenceChangeListener(evt -> {
            String newValue = evt.getNewValue();
            String changedPrefKey = evt.getKey();
            Preferences changeNode = evt.getNode();
            System.out.println(newValue);
            System.out.println(changedPrefKey);
        });

        // addNodeChangeListener
        preferences.addNodeChangeListener(new NodeChangeListener() {
            @Override
            public void childAdded(NodeChangeEvent evt) {
                Preferences addChild = evt.getChild();
                System.out.println(addChild.name());

            }
            @Override
            public void childRemoved(NodeChangeEvent evt) {
                Preferences removeChild = evt.getChild();
                System.out.println(removeChild.name());
            }
        });

        preferences.putBoolean("showExitConfirmation", false);

        boolean doShowDialog = preferences.getBoolean("showExitConfirmation", true);
        if(!doShowDialog){
            System.out.println("ShowDialog shown none");
        }
        preferences.remove("showExitConfirmation");

    }
}
```

## 🔍 샘플 코드 단계 분석
### ① Preferences 객체 생성
```java
Preferences preferences = Preferences.userNodeForPackage(PreferenceTest.class);
```

- 현재 클래스(PreferenceTest)에 해당하는 사용자 노드를 생성
- 이 노드는 PreferenceTest 클래스의 패키지 경로를 기준으로 저장됨

### ② 설정 변경 리스너 등록
```java
preferences.addPreferenceChangeListener(evt -> {
    String newValue = evt.getNewValue();
    String changedPrefKey = evt.getKey();
    Preferences changeNode = evt.getNode();
    System.out.println(newValue);
    System.out.println(changedPrefKey);
});
```
- 설정 값이 변경될 때마다 호출되는 리스너
- 변경된 키와 새 값을 출력
- 예: putBoolean("showExitConfirmation", false) 호출 시 이벤트 발생


### ③ 노드 변경 리스너 등록
```java
preferences.addNodeChangeListener(new NodeChangeListener() {
    @Override
    public void childAdded(NodeChangeEvent evt) {
        Preferences addChild = evt.getChild();
        System.out.println(addChild.name());
    }
    @Override
    public void childRemoved(NodeChangeEvent evt) {
        Preferences removeChild = evt.getChild();
        System.out.println(removeChild.name());
    }
});
```
- 자식 노드가 추가되거나 제거될 때 호출되는 리스너
- 현재 코드에서는 자식 노드를 직접 추가하지 않지만, 구조적으로 감지 가능


### ④ 설정 저장 및 조회
```java
preferences.putBoolean("showExitConfirmation", false);
```

- "showExitConfirmation" 키에 false 값을 저장
- 저장 위치는 OS에 따라 다름 (Windows: 레지스트리, macOS/Linux: XML)
```java
boolean doShowDialog = preferences.getBoolean("showExitConfirmation", true);
if(!doShowDialog){
    System.out.println("ShowDialog shown none");
}
```
- 저장된 값을 읽어와 조건 분기
- 기본값은 true, 저장된 값이 있으면 그 값을 반환


### ⑤ 설정 삭제
```java
preferences.remove("showExitConfirmation");
```
- 해당 키를 삭제
- 이후 getBoolean() 호출 시 기본값 반환


## 📌 Preferences API 요약

| 기능 항목                  | 사용 메서드 또는 설명                          |
|---------------------------|-----------------------------------------------|
| Preferences 객체 생성      | `Preferences.userNodeForPackage(Class)`       |
| 설정 변경 감지 리스너 등록 | `addPreferenceChangeListener(listener)`       |
| 노드 변경 감지 리스너 등록 | `addNodeChangeListener(listener)`             |
| 설정 저장 및 조회         | `putBoolean(key, value)` / `getBoolean(key, default)` |
| 설정 삭제                 | `remove(key)`                                 |


# UI와 연동

아래는 Java의 Preferences API를 Swing UI와 연동하여 사용자 설정을 저장하고 불러오는 간단한 예제입니다.  
이 예제는 사용자가 체크박스를 통해 설정을 변경하면, 그 값이 Preferences에 저장되고, 다음 실행 시에도 유지되는 구조입니다.

## 🖥️ Java Preferences + Swing UI 연동 예제
```java
import javax.swing.*;
import java.awt.event.*;
import java.util.prefs.Preferences;

public class PreferenceSwingDemo extends JFrame {
    private JCheckBox showExitConfirmCheckBox;
    private Preferences prefs;

    public PreferenceSwingDemo() {
        super("Preferences UI Demo");

        // 1. Preferences 객체 생성
        prefs = Preferences.userNodeForPackage(PreferenceSwingDemo.class);

        // 2. UI 구성
        showExitConfirmCheckBox = new JCheckBox("Exit 시 확인창 보이기");

        // 3. Preferences에서 기존 값 불러오기 (기본값: true)
        boolean showConfirm = prefs.getBoolean("showExitConfirmation", true);
        showExitConfirmCheckBox.setSelected(showConfirm);

        // 4. 체크박스 변경 시 Preferences에 저장
        showExitConfirmCheckBox.addItemListener(e -> {
            boolean selected = showExitConfirmCheckBox.isSelected();
            prefs.putBoolean("showExitConfirmation", selected);
            System.out.println("설정 저장됨: showExitConfirmation = " + selected);
        });

        // 5. 프레임 설정
        add(showExitConfirmCheckBox);
        setSize(300, 100);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null); // 화면 중앙
        setVisible(true);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(PreferenceSwingDemo::new);
    }
}
```

## 📌 주요 포인트 요약

| 항목               | 설명 또는 사용 메서드                         |
|--------------------|-----------------------------------------------|
| Preferences         | `Preferences.userNodeForPackage()`로 노드 생성 |
| UI 컴포넌트         | `JCheckBox` 등 사용자 입력 요소와 연동         |
| 설정 값 불러오기    | `prefs.getBoolean(key, defaultValue)`          |
| 설정 변경 저장      | `addItemListener()` → `prefs.putBoolean()` 호출 |
| 실행 진입점         | `main()`에서 UI 초기화 및 실행                 |

---
