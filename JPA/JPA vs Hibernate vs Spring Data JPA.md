
# 📘 JPA vs Hibernate vs Spring Data JPA 완전정리

---

## 🔷 1. 기술 개요

| 기술              | 설명 |
|-------------------|------|
| **JPA**           | Java ORM 표준 인터페이스. 직접 쓸 수는 없고, 구현체가 필요함 |
| **Hibernate**     | JPA의 대표적인 구현체. JPA를 구현하면서 자체 기능도 제공 |
| **Spring Data JPA** | JPA/Hibernate를 쉽게 사용하도록 도와주는 Spring의 추상화 계층 |

---

## 🔷 2. 기술 역할 및 계층 구조

```plaintext
[Spring Data JPA]
        ↓ (Spring 추상화)
         JPA (javax.persistence API)
        ↓ (구현체)
       Hibernate
```

---

## 🔷 3. 핵심 기능 요약

| 항목              | JPA                   | Hibernate                      | Spring Data JPA                          |
|-------------------|------------------------|--------------------------------|------------------------------------------|
| 역할              | ORM 표준 인터페이스      | JPA 구현체 (JPA + 추가 기능)     | JPA/Hibernate 추상화 및 자동화              |
| 주요 API          | `EntityManager`       | `Session`, `Criteria`         | `JpaRepository`, `@Query`, QueryDSL       |
| 쿼리              | JPQL                   | HQL / Native SQL              | JPQL + 자동 메서드 쿼리 + QueryDSL 지원     |
| 설정 복잡도       | 중                     | 중~상                          | 낮음 (Spring Boot 사용 시 거의 없음)       |
| 생산성            | 수작업 필요             | 수작업 + 기능 다양             | ✅ 인터페이스만 정의하면 자동 구현          |
| Lazy 로딩         | 지원                    | 지원                           | 지원                                     |
| Dirty Checking    | 지원                    | 고급                           | 내부적으로 Hibernate 기능 사용              |
| 트랜잭션          | 직접 관리                | 직접 관리                      | `@Transactional`으로 AOP 처리             |

---

## 🔷 4. 설정 예시

### ✅ Hibernate (순수 JPA 기반)

```java
// persistence.xml
<persistence-unit name="examplePU">
  <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
  <class>com.example.domain.User</class>
  <properties>
    <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL8Dialect"/>
    <property name="hibernate.hbm2ddl.auto" value="update"/>
    <property name="hibernate.show_sql" value="true"/>
  </properties>
</persistence-unit>
```

### ✅ Spring Boot + Spring Data JPA (application.yml)

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: pass
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        format_sql: true
```

---

## 🔷 5. 예제 비교

### 📌 JPA
```java
EntityManager em = ...;
User user = em.find(User.class, 1L);
em.persist(new User(...));
```

---

### 📌 Hibernate
```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();
User user = session.get(User.class, 1L);
session.save(new User(...));
tx.commit();
```

---

### 📌 Spring Data JPA
```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

---

## 🔷 6. 주요 이슈 및 고급 기능

### ✅ N+1 문제

- Lazy 로딩 시 연관된 엔티티를 반복 조회하는 현상
- 해결법:
  - `@EntityGraph` 사용
  - JPQL에서 `fetch join` 사용
  - QueryDSL의 `fetchJoin()` 사용

```java
@Query("SELECT p FROM Post p JOIN FETCH p.comments")
List<Post> findAllWithComments();
```

---

### ✅ Dirty Checking (변경 감지)

- 트랜잭션 안에서 엔티티 필드만 변경해도 `flush()` 시 자동으로 update 쿼리 발생

```java
@Transactional
public void changeUsername(Long id, String name) {
    User user = userRepository.findById(id).orElseThrow();
    user.setName(name); // save() 호출 없이도 변경됨
}
```

---

### ✅ JPQL vs Native SQL

| 쿼리 유형  | 특징                          |
|------------|-------------------------------|
| JPQL       | Entity 기반, 객체 지향 쿼리 언어 |
| Native SQL | 실제 DB 쿼리, 성능 튜닝에 유리      |

```java
@Query("SELECT u FROM User u WHERE u.email = :email")
Optional<User> findByEmail(@Param("email") String email);

@Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)
Optional<User> findNative(@Param("email") String email);
```

---

### ✅ 동적 쿼리

- 조건이 많고 유동적인 경우 → **QueryDSL** 사용 추천

```java
QUser user = QUser.user;
BooleanBuilder builder = new BooleanBuilder();
if (email != null) {
    builder.and(user.email.eq(email));
}
return queryFactory.selectFrom(user).where(builder).fetch();
```

---

## 🔷 7. 실무 적용 팁

- **JPA는 표준이고 Hibernate는 필수 구현체로 쓴다** → 대체 가능성 확보
- **Spring Data JPA는 반복 작업을 줄여준다** → 초반 빠른 개발에 유리
- **QueryDSL은 동적 조건이 많은 쿼리에서 사용** → JPQL 한계 보완
- **N+1 문제 반드시 체크** → 실무에서 자주 성능 병목의 원인이 됨
- **영속성 컨텍스트와 Dirty Checking의 동작 원리 이해 필수**

---

## 🔷 8. 결론

| 요약 |
|------|
| `JPA`는 표준 인터페이스 |
| `Hibernate`는 JPA의 대표 구현체 |
| `Spring Data JPA`는 JPA/Hibernate를 쉽게 쓸 수 있도록 도와주는 추상화 계층 |
| **보통 세 개를 같이 사용하며**, Spring Data JPA를 통해 생산성을 확보하고, 필요한 경우 Hibernate 기능을 직접 활용하는 구조가 일반적임 |
