# 🌟 널 오브젝트 패턴 (Null Object Pattern)

## 1. 개념

- **Null Object Pattern**은  
    객체가 `null`일 때, `null`을 그대로 넘기지 않고,  
    **"아무 일도 하지 않는 객체"** 를 대신 전달하는 패턴입니다.
    

---

## 2. 사용 이유

- **널 체크 반복을 제거**하여 코드 흐름을 단순화합니다.
    
- **코드 가독성**과 **유지보수성**을 향상시킵니다.
    

---

## 3. 예제

### ✅ 기존 방식 (널 체크 필요)

```java

public void doSomething(MyObject obj) {
    if (obj == null) {
     throw new Exception();
    }
    obj.doMethod(); }`
```
### ✅ Null Object Pattern 적용
```java
class MyNullObject implements MyObject {
    @Override
    public void doMethod() {
        // 아무 것도 하지 않음
        }
    }
class MyRealObject implements MyObject {
    @Override
    public void doMethod() {
         System.out.println("무엇인가 수행합니다.");
         }
    }
public void doSomething(MyObject obj) {
    obj.doMethod();
    // 별도 null 체크 없이 바로 호출
}

- `MyNullObject`를 넘기면 아무런 동작 없이 지나갑니다.
    
- `MyRealObject`를 넘기면 실제 동작이 수행됩니다.
    
```
---

## 4. 확장 예시

- **단순 Null 처리** 외에도,
    
- **특수 상황** (예: 스택 자료구조에서 용량이 0인 경우 `ZeroCapacityStack`)에도 적용할 수 있습니다.
    

---

## 5. 장점

- **반복적인 null 체크 코드 제거**
    
- **인터페이스 일관성 유지**
    
- **코드 흐름이 자연스러워짐**
    

---

## 6. 주의할 점

- **문제 탐지가 어려워질 수 있습니다.**
    
    - 실제로 오류가 발생해야 하는 상황을 "조용히" 무시할 위험이 있습니다.\
