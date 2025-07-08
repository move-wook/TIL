# 동일성과 동등성 (Identity vs Equality)

## 📌 핵심 개념 요약

| 구분       | 설명                                    | 자바 구현 방식         | 예시 결과 |
|------------|-----------------------------------------|------------------------|-----------|
| 동일성     | 두 객체가 **같은 메모리 주소**를 가리키는가 | `==` 연산자              | `false` 또는 `true` (참조 비교) |
| 동등성     | 두 객체의 **내용이 논리적으로 같은가**      | `.equals()` 메서드       | `true` 또는 `false` (값 비교) |

---

## ✅ 동일성 (Identity)

- **정의:** 객체의 메모리 참조가 같은지 판단
- **비교 방법:** `==` 연산자 사용
- **사용 시점:** 캐시, 싱글턴, 세션, 참조 일치 확인이 필요할 때

```java
Apple a1 = new Apple(100);
Apple a2 = new Apple(100);
Apple a3 = a1;

System.out.println(a1 == a2); // false
System.out.println(a1 == a3); // true
```

---

## ✅ 동등성 (Equality)

- **정의:** 객체의 상태/값이 같다고 판단
- **비교 방법:** `.equals()` 오버라이딩하여 사용
- **사용 시점:** 도메인 비교, 컬렉션(Set, Map 등), DTO 비교

```java
public class Apple {
    private final int weight;

    public Apple(int weight) {
        this.weight = weight;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Apple apple = (Apple) o;
        return weight == apple.weight;
    }

    @Override
    public int hashCode() {
        return Objects.hash(weight);
    }
}
```

```java
Apple a1 = new Apple(100);
Apple a2 = new Apple(100);

System.out.println(a1.equals(a2)); // true
```

---

## ⚠️ Object 클래스의 equals 기본 구현

```java
public boolean equals(Object obj) {
    return (this == obj); // 동일성 비교
}
```

→ equals()를 오버라이딩하지 않으면 `==`과 같은 효과만 제공됨.

---

## 📚 String과 Integer 비교 예시

### ✅ String: 상수풀 존재

```java
String s1 = "hello";
String s2 = "hello";
String s3 = new String("hello");

System.out.println(s1 == s2);       // true (상수 풀)
System.out.println(s1 == s3);       // false (new 객체)
System.out.println(s1.equals(s3));  // true
```

### ✅ Integer: -128 ~ 127 범위 캐싱

```java
Integer i1 = 100;
Integer i2 = 100;
Integer i3 = 200;
Integer i4 = 200;

System.out.println(i1 == i2); // true
System.out.println(i3 == i4); // false
System.out.println(i3.equals(i4)); // true
```

---

## 🧠 한 단계 위 시야: 객체 비교의 판단 기준

| 판단 기준 레벨 | 설명 | 사용 예 |
|----------------|------|---------|
| 의미 기반      | 역할/도메인 정의 중심 비교 | 동일 사용자, 동일 주문 |
| 상태 기반      | 내부 값이 같은지 비교 | DTO, 값 객체 |
| 참조 기반      | 메모리 주소 동일성 | 캐시, 세션, 동기화 |
| 기술 기반      | JVM 최적화/상수풀 | String, Integer 등 |

---

## 🎯 equals & hashCode는 항상 같이 오버라이딩해야 하는 이유

- HashMap, HashSet 등에서 `equals()`만 오버라이딩하면 **동등하지만 다른 hashCode를 가지는 객체**는 같은 키로 인식되지 않음

```java
Set<Apple> apples = new HashSet<>();
apples.add(new Apple(100));
apples.contains(new Apple(100)); // equals만 오버라이딩하면 false 가능성
```

---

## 📌 결론 요약

- `==`은 객체의 **동일성(참조)** 비교
- `.equals()`는 **동등성(내용/의미)** 비교
- equals를 정의할 땐 **도메인에서 “같다”의 의미**를 먼저 고민해야 한다
- hashCode도 반드시 같이 오버라이딩해야 컬렉션에서 제대로 동작한다

---

## 📎 추천 확장 질문

- equals/hashCode를 올바르게 구현하기 위한 전략은?
- ORM(JPA)에서 equals/hashCode를 잘못 정의하면 어떤 문제가 생길까?
- Java record나 Kotlin data class는 어떻게 동등성을 자동 구현할까?
