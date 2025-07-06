# 📌 JPA ID 생성 전략 정리

## 🔍 개요

JPA에서 `@Id` 필드에 대해 고유한 식별자를 자동 생성하기 위한 전략은 다음과 같이 제공됨:

- `IDENTITY`
- `SEQUENCE`
- `TABLE`
- `AUTO`

전략에 따라 **ID 생성 시점**, **DB 의존성**, **성능**, **배치 처리 가능 여부** 등이 달라짐.

---

## 🎯 주요 전략 비교

| 전략        | 설명                                   | 특징                                        | DB 의존성      |
|-------------|----------------------------------------|---------------------------------------------|----------------|
| `AUTO`      | DB Dialect에 따라 자동 전략 선택       | 개발 편의는 좋지만 명확성은 떨어짐          | 낮음           |
| `IDENTITY`  | DB의 auto-increment 기능 활용           | `INSERT` 시점에 ID 확정 → 배치 불리함       | 높음 (MySQL 등)|
| `SEQUENCE`  | DB 시퀀스 객체 사용                     | `INSERT` 전 ID 확보 가능 → 성능 우수        | 높음 (Oracle, PostgreSQL) |
| `TABLE`     | 별도 테이블에서 next ID 관리            | DB 독립성 확보 가능하지만 성능 저하          | 없음           |

---

## 🧪 예시 코드

### 1. `AUTO`
~~~java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
~~~

### 2. `IDENTITY`
~~~java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
~~~

### 3. `SEQUENCE`
~~~java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq_gen")
@SequenceGenerator(
    name = "user_seq_gen",
    sequenceName = "user_seq",
    allocationSize = 50
)
private Long id;
~~~

### 4. `TABLE`
~~~java
@Id
@GeneratedValue(strategy = GenerationType.TABLE, generator = "table_gen")
@TableGenerator(
    name = "table_gen",
    table = "id_gen",
    pkColumnName = "entity_name",
    valueColumnName = "next_id",
    pkColumnValue = "user",
    allocationSize = 1
)
private Long id;
~~~

---

## ⚙️ 선택 기준 요약

| 상황                          | 적합한 전략        |
|-------------------------------|--------------------|
| MySQL 사용 시                 | `IDENTITY`         |
| Oracle/PostgreSQL + 성능 필요 | `SEQUENCE`         |
| DB 독립성 중요                | `TABLE`            |
| 자동 설정/테스트 목적         | `AUTO`             |

---

## ⚠️ 전략별 유의사항

### 🔸 `IDENTITY`
- ID 생성 시점이 `persist()`와 동일
- 연관관계 처리 시 ID가 없으면 어려움
- 배치 INSERT 비효율적

### 🔸 `SEQUENCE`
- `allocationSize` 조정으로 성능 개선 가능
- 미리 ID 확보 → 연관관계 유리
- DB 시퀀스 객체 필요

### 🔸 `TABLE`
- DB 독립성 확보 (JPA 표준)
- 하지만 성능은 가장 느림
- 동시성 이슈 주의 (락 또는 캐시로 보완 가능)

---

## 🔄 확장 학습 주제

- 커스텀 ID 전략 (날짜+시퀀스 조합 등)
- UUID 식별자 활용
- 비즈니스 키 기반 식별자 설계의 위험성
- Hibernate의 Enhanced Sequence Generator 사용법
