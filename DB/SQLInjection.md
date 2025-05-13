# 💥 SQL 인젝션 (SQL Injection)

## 🔍 개념 정의

**SQL 인젝션**은 외부 입력값이 검증 없이 SQL 쿼리에 직접 포함되면서,  
공격자가 의도하지 않은 쿼리를 실행할 수 있게 되는 **보안 취약점**이다.

예를 들어 아래와 같은 코드에서:

~~~java
String query = "SELECT * FROM users WHERE username = '" + userInput + "'";
~~~

`userInput`에 `' OR 1=1 --` 을 입력하면 실제 실행되는 쿼리는 다음과 같아진다:

~~~sql
SELECT * FROM users WHERE username = '' OR 1=1 --'
~~~

→ 로그인 우회, 전체 정보 노출, 심하면 데이터베이스 파괴까지 가능함.

---

## ⚠️ 왜 발생하는가?

| 원인 | 설명 |
|------|------|
| 🔓 입력값 검증 누락 | 사용자가 입력한 값을 신뢰하여 필터링 없이 쿼리에 포함 |
| 🧱 문자열 기반 쿼리 조합 | 쿼리를 직접 문자열 + 연산으로 구성 |
| 🧠 실행 전 파싱 없음 | SQL은 런타임에 쿼리를 문자열로 해석 후 실행 |

---

## 🚨 위험 시나리오

| 공격 유형 | 설명 |
|-----------|------|
| 로그인 우회 | `' OR '1'='1` 입력 시 조건 무력화 |
| 테이블 삭제 | `'; DROP TABLE users; --` |
| DB 구조 조회 | `' UNION SELECT table_name FROM information_schema.tables --` |
| 정보 탈취 | 비밀번호, 개인정보 조회 등 |

---

## 🛡️ 방어 전략

### ✅ 1. PreparedStatement 사용 (JDBC)

~~~java
String sql = "SELECT * FROM users WHERE username = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, userInput);  // 바인딩
~~~

→ 쿼리 구조와 데이터 분리되어 SQL 인젝션 불가능

### ✅ 2. ORM 쿼리 파라미터 (MyBatis, JPA 등)

~~~java
// JPA
@Query("SELECT u FROM User u WHERE u.username = :username")
User findByUsername(@Param("username") String username);
~~~

### ✅ 3. 입력값 화이트리스트 검증

- 문자열 길이 제한
- 정규식 검증 (`^[a-zA-Z0-9_]{3,20}$` 등)

### ✅ 4. 최소 권한 원칙 적용

- DB 계정별 권한 분리 (`SELECT`, `INSERT`, `UPDATE`만 허용 등)
- 운영계와 개발계 격리

---

## 🧠 사고 확장: 모든 입력은 명령어 해석 위험이 있다

| 분야 | 취약점 |
|------|--------|
| SQL | SQL Injection |
| HTML | XSS (Cross-site Scripting) |
| OS 명령어 | Command Injection |
| LDAP | LDAP Injection |
| 로그 | Log Injection |
| 이메일 헤더 | Header Injection |

> 🔐 핵심 원칙: **입력은 코드가 아니다.**

---

## ✅ 요약

| 항목 | 내용 |
|------|------|
| 📌 정의 | 사용자 입력이 SQL 쿼리로 실행되는 보안 취약점 |
| 🧨 위험 | 인증 우회, 데이터 유출/삭제, 전체 시스템 위협 |
| 🔐 대응 | PreparedStatement, ORM, 입력 검증, 최소 권한 |
| 🧠 확장 | 모든 해석 계층(HTML, OS, DB 등)에서 주의 필요 |
