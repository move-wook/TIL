# 📘 JPA `spring.jpa.hibernate.ddl-auto` 옵션 정리

## 🔍 개요

`spring.jpa.hibernate.ddl-auto`는 Hibernate가 **어플리케이션 실행 시점에 DB 스키마를 어떻게 다룰지** 결정하는 설정이다.  
즉, JPA 엔티티와 DB 테이블 간의 동기화 전략을 선택하는 것.

---

## ⚙️ 옵션별 동작과 사용 시점

| 옵션          | 동작 설명                                                                 | 사용 시점 |
|---------------|---------------------------------------------------------------------------|-----------|
| `none`        | 아무 작업도 수행하지 않음                                                  | 운영 환경 (DB 수동 관리) |
| `validate`    | 테이블이 엔티티와 일치하는지 검증만 하고, 수정은 안 함                      | 운영 직전 검증용 |
| `update`      | 엔티티 변경사항을 테이블에 자동 반영 (컬럼 추가 등), 삭제는 안 됨           | 개발 단계 (빠른 반복 개발) |
| `create`      | 기존 테이블을 삭제하고, 애플리케이션 시작 시 새로 생성                      | 개발 초기 (전체 초기화 반복) |
| `create-drop` | `create`와 동일하되, 애플리케이션 종료 시점에 테이블도 삭제됨              | 테스트 환경 (단위 테스트 등) |

---

## 📌 옵션 선택 기준

| 상황/환경          | 추천 옵션       | 설명 |
|--------------------|-----------------|------|
| 운영 환경           | `none` 또는 `validate` | 운영 DB 보호를 위해 DDL 자동 생성 금지 |
| 개발 초기           | `create`, `update`     | 반복 개발 및 실험용으로 테이블 자동 생성 |
| 테스트 환경         | `create-drop`          | 테스트 종료 후 자동 정리 |
| CI/CD 자동화 테스트 | `update`, `create-drop`| 독립 테스트 DB에서만 사용 |

---

## 🧠 설계 관점 질문: 누가 DB를 관리하는가?

- **DBA가 스키마를 관리**하는 경우 → `validate` 또는 `none`
- **개발자가 코드로 관리**하고 싶을 경우 → `update`, `create`, `create-drop`

---

## 🚀 시야 전환: 마이그레이션 도구 도입 고려

Hibernate의 `ddl-auto`는 편리하지만, 운영 환경에선 위험함.  
보다 명확하고 안전한 방식은 다음과 같은 마이그레이션 도구를 도입하는 것:

- [Flyway](https://flywaydb.org)
- [Liquibase](https://www.liquibase.org)

이 도구들은 **DDL 변경을 버전 관리하고, 배포 가능한 이력으로 관리**할 수 있도록 도와줌.

---
