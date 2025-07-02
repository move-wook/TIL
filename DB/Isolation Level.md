
# 트랜잭션 격리 수준 (Isolation Level)

트랜잭션 격리 수준이란,  
**여러 트랜잭션이 동시에 수행될 때, 서로의 작업 결과에 대해 얼마나 간섭을 허용할지를 정의하는 기준**이다.  
이는 **데이터 정합성**과 **성능(동시성 처리)** 사이의 균형을 조절하는 핵심 설정이다.

---

## 💥 트랜잭션 간 발생 가능한 충돌 현상

| 현상 | 설명 |
|------|------|
| **Dirty Read** | 다른 트랜잭션이 아직 커밋하지 않은 데이터를 읽는 현상 |
| **Non-Repeatable Read** | 같은 데이터를 두 번 읽었는데 값이 달라짐 (중간에 다른 트랜잭션이 수정) |
| **Phantom Read** | 같은 조건으로 두 번 조회했는데 결과 행의 개수가 바뀜 (중간에 insert/delete 됨) |

---

## 🔐 ANSI SQL 표준 격리 수준 4단계

| 격리 수준 | Dirty Read | Non-Repeatable Read | Phantom Read | 특징 |
|------------|-------------|------------------------|----------------|--------|
| **READ UNCOMMITTED** | 허용 ✅ | 허용 ✅ | 허용 ✅ | 가장 낮은 격리 수준. 빠르지만 정합성 보장 없음 |
| **READ COMMITTED** *(Oracle 기본)* | 차단 ❌ | 허용 ✅ | 허용 ✅ | 커밋된 데이터만 읽음. 반복 조회 시 값 변할 수 있음 |
| **REPEATABLE READ** *(MySQL 기본)* | 차단 ❌ | 차단 ❌ | 허용 ✅ | 트랜잭션 내 동일 데이터 조회 시 값 고정 |
| **SERIALIZABLE** | 차단 ❌ | 차단 ❌ | 차단 ❌ | 가장 높은 격리 수준. 완벽한 정합성, 낮은 성능 |

---

## 🛠 Spring에서 설정 방법

```java
@Transactional(isolation = Isolation.REPEATABLE_READ)
public void someTransactionalMethod() {
    // ...
}
```

- `Isolation.DEFAULT`: DB 기본 격리 수준 사용
- `READ_COMMITTED`, `REPEATABLE_READ`, `SERIALIZABLE` 등 선택 가능

---

## 🧭 격리 수준 선택 가이드

| 업무 상황 | 추천 격리 수준 |
|-----------|----------------|
| 단순 조회 / 목록 출력 | READ COMMITTED |
| 재고 감소 / 금액 차감 등 정확한 정합성 필요 | REPEATABLE READ |
| 중복 방지, 강력한 정합성 필요 | SERIALIZABLE |

---

## 🧠 요약

- 격리 수준은 트랜잭션 간 충돌을 제어하기 위한 **정합성과 성능의 균형 도구**이다.
- 높은 격리 수준 → 데이터 정합성 강화, 성능 저하 가능
- 낮은 격리 수준 → 성능은 높지만 충돌 가능성 증가
- **업무 요구사항에 맞춰 격리 수준을 선택하는 것이 핵심이다.**
