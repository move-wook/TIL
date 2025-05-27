# 싱글턴 패턴 (Singleton Pattern)

## ✅ 개요

**싱글턴 패턴**은 클래스의 인스턴스를 **오직 하나만 생성**하고, 그 인스턴스에 **전역 접근(global access)** 할 수 있도록 보장하는 디자인 패턴이다.

---

## 🧠 왜 사용하는가?

| 목적 | 설명 |
|------|------|
| 인스턴스 1개만 필요할 때 | ex. 설정 클래스, DB 연결 풀, 로그 관리자 등 |
| 메모리 낭비 방지 | 불필요한 인스턴스 생성을 방지함 |
| 전역 상태 관리 | 동일한 인스턴스를 여러 클래스에서 공유해야 할 때 사용 |

---

## 🛠 구조

1. 생성자를 `private`으로 막아 외부에서 직접 생성하지 못하게 한다.
2. 클래스 내부에 자신의 유일한 인스턴스를 static으로 생성한다.
3. 외부에서 접근 가능한 static 메서드(`getInstance`)를 통해 인스턴스를 반환한다.

---

## 🧾 예제 (Java)

```java
public class Singleton {
    private static final Singleton instance = new Singleton();

    // 생성자 private으로 막음
    private Singleton() {}

    // 외부에서 접근 가능한 메서드
    public static Singleton getInstance() {
        return instance;
    }
}
