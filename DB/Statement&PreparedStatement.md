# ✅ Statement vs PreparedStatement (JDBC 비교 정리)

## 📌 공통점
- 둘 다 `java.sql` 패키지에 포함된 JDBC 인터페이스
- DB에 SQL을 전송하고 실행하는 데 사용됨

---

## 🔍 차이점 비교

| 항목                  | Statement                          | PreparedStatement                             |
|-----------------------|-------------------------------------|------------------------------------------------|
| SQL 작성 방식         | SQL 문자열 직접 조립                | `?` 바인딩 방식으로 분리                       |
| SQL 실행 방식         | 매 요청마다 SQL 새로 파싱           | 한 번 파싱 후 파라미터만 바꿔 재사용           |
| 보안 (SQL Injection) | 취약                                | ✅ 안전 (파라미터 자동 이스케이프 처리)        |
| 성능                 | 느림 (파싱 반복)                   | ✅ 빠름 (SQL 캐싱 및 재사용 가능)             |
| 가독성 및 유지보수    | 문자열 연결로 복잡해짐              | ✅ 깔끔하고 명확한 쿼리 구조                   |
| 배치 처리             | 불편하거나 미지원                   | ✅ addBatch()/executeBatch() 완전 지원         |

---

## 💥 코드 예시

### ❌ Statement (보안 취약)
~~~java
String query = "SELECT * FROM users WHERE name = '" + name + "'";
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(query);
~~~

> 입력값이 `"홍길동' OR '1'='1"` 이면 SQL Injection 발생 가능

---

### ✅ PreparedStatement (권장 방식)
~~~java
String query = "SELECT * FROM users WHERE name = ?";
PreparedStatement pstmt = conn.prepareStatement(query);
pstmt.setString(1, name);
ResultSet rs = pstmt.executeQuery();
~~~

> 입력값이 자동으로 이스케이프되며 보안, 성능 모두 우수

---

## 🧠 언제 어떤 걸 써야 하나?

| 상황                           | 추천 방식          |
|--------------------------------|--------------------|
| 고정된 쿼리 (파라미터 없음)    | Statement (거의 안 씀) |
| 사용자 입력이 포함된 쿼리      | ✅ PreparedStatement |
| 대량 INSERT / UPDATE 반복      | ✅ PreparedStatement + 배치 처리 |
| 보안이 중요한 환경 (실무 전부) | ✅ PreparedStatement |

---

## ✅ 결론

> `PreparedStatement`는 **보안**, **성능**, **가독성** 모두 뛰어나며  
> **실무에서는 Statement 대신 기본으로 사용**해야 한다.
