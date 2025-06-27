# ✅ Java 예외 처리: Checked vs Unchecked Exception

Java에서는 예외(Exception)를 두 가지로 분류한다: **Checked Exception**과 **Unchecked Exception**. 이 구조는 컴파일 타임 안전성과 런타임 유연성 사이의 균형을 위해 설계되었다.

---

## 📌 1. 예외의 두 가지 분류

| 분류               | 설명                                                           | 대표 예외 클래스                                        |
|--------------------|----------------------------------------------------------------|----------------------------------------------------------|
| ✅ Checked Exception  | 컴파일러가 예외 처리를 강제 (`try-catch` 또는 `throws` 필요)     | `IOException`, `SQLException`, `ParseException` 등        |
| ✅ Unchecked Exception | 컴파일러가 예외 처리를 강제하지 않음                              | `NullPointerException`, `IllegalArgumentException`, `IndexOutOfBoundsException` 등 |

---

## 🔍 2. 차이점 요약

| 항목                 | Checked Exception                                | Unchecked Exception                                |
|----------------------|--------------------------------------------------|----------------------------------------------------|
| 상속 구조            | `Exception` → `RuntimeException` 제외            | `RuntimeException` 하위 클래스                     |
| 예외 처리 강제 여부  | O (컴파일 타임에 강제)                           | X (선택적으로 처리 가능)                          |
| 사용 목적            | 복구 가능한 외부 실패 처리 (I/O, DB 등)          | 코드 결함, 프로그래밍 실수, 논리 오류             |
| 실무 활용            | 외부 API, 파일 처리 등                           | 유효성 검사, 계약 위반, null 접근 등              |

---

## 🧠 3. 예외 선택 기준

### ✅ Checked Exception을 쓰는 경우
- 예외가 **복구 가능**하거나 **호출자가 의미 있는 대처를 할 수 있을 때**
- 예시: 파일을 찾을 수 없음, 네트워크 요청 실패

### ✅ Unchecked Exception을 쓰는 경우
- 예외가 **프로그래밍 실수**이거나 **복구 불가능한 상황일 때**
- 예시: 잘못된 메서드 인자, null 포인터 접근

---

## ⚙️ 4. 코드 예시

```java
// Checked Exception 예시
public void loadFile(String path) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(path));
    ...
}

// Unchecked Exception 예시
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

✅ 결론
✔ Checked Exception은 안정성을 위해 강제 처리하지만,
✔ Unchecked Exception은 유연성과 간결성을 위해 더 널리 쓰인다.
