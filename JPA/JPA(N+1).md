# 📌 JPA의 N + 1 문제

---

## 1️⃣ 시야: ORM과 지연 로딩(Lazy Loading)

- JPA는 엔티티 간 관계를 통해 객체 그래프를 탐색함
- 관계된 엔티티는 **프록시 객체**로 채워지고, 실제 DB 조회는 **접근 시점**에 발생
- 이게 지연 로딩(Lazy Loading)의 핵심이며, **필드 접근마다 쿼리가 발생할 수 있음**

---

## 2️⃣ 루프: N + 1 문제란?

```java
// 1. 부모 엔티티 목록 조회 (1번 쿼리)
List<Order> orders = em.createQuery("select o from Order o", Order.class).getResultList();

// 2. 각 주문마다 연관된 회원 정보 접근 (N번 쿼리 발생)
for (Order o : orders) {
    System.out.println(o.getMember().getName()); // 매 루프마다 SELECT
}
```

- `Order`를 10건 조회 → **1 쿼리**
- 각 `Order.getMember()` 마다 쿼리 발생 → **10 쿼리**
- 총 **1 + 10 = 11 쿼리**  
→ 데이터가 많아질수록 **N번 쿼리가 기하급수적으로 증가**

---

## 3️⃣ 기준 형성: 언제 문제가 되는가?

| 체크포인트 | 설명 |
|------------|------|
| 트래픽/건수 | 데이터 수가 많을수록 문제 심각 |
| 응답 지연 | 초 단위 응답 시간 증가 |
| DB 부하 | 동일 SELECT 반복 → 커넥션 압박 |
| 디버깅 어려움 | 코드상 문제 없어 보여서 추적 어려움 |

---

## 4️⃣ 근본 원인

- JPA의 **기본 Fetch 전략이 LAZY**
- 화면 or 서비스에서 **루프 안에서 연관 객체 접근**
- **JOIN FETCH 생략**, DTO projection 미적용

---

## 5️⃣ 해결 방안

| 전략 | 방법 | 특징 / 주의점 |
|------|------|----------------|
| 🔸 Fetch Join | `JOIN FETCH` 사용 | 단일 쿼리로 연관 객체 미리 로딩<br>컬렉션은 페이징 불가 |
| 🔸 EntityGraph | `@EntityGraph` 또는 JPQL로 지정 | 조건부 fetch 전략 동적 설정 |
| 🔸 배치 로딩 | `@BatchSize`, `hibernate.default_batch_fetch_size` | 다건 프록시 or 컬렉션을 IN 절로 묶어 한 번에 로딩 |
| 🔸 DTO 쿼리 | QueryDSL, JPQL projection | 복잡한 조인 → 필요한 필드만 조회 |
| 🔸 캐시 | Redis, 2nd-level cache | 반복 조회 회피, TTL 관리 필요 |
| 🔸 도메인 설계 변경 | CQRS, Aggregate 경계 재정의 | 근본적으로 N+1이 일어나지 않게 모델링 설계

---

## 6️⃣ 시야 전이: 단순 최적화 → 설계 개선

- **읽기와 쓰기를 분리** (CQRS)
- **API 단에 맞는 뷰 모델 설계**
- **서비스 흐름에서 연관 접근 패턴 최소화**
- **“지연 로딩”을 기본값으로 두되, 필요한 곳에만 fetch 전략 명시**

---

