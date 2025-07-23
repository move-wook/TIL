# 📘 MySQL 트랜잭션 격리 수준 정리 및 팬텀 리드 실험 가이드

---

## 🧭 실험 목적

> **MySQL 트랜잭션 격리 수준별 동작 차이**를 직접 실험하며  
> Dirty Read / Non-Repeatable Read / Phantom Read가 발생하는 상황을 관찰하고,  
> **MVCC와 스냅샷 고정의 실제 효과**를 이해하는 것이 목적이다.

---

## ✅ 트랜잭션 격리 수준 요약

| 격리 수준 | Dirty Read | Non-Repeatable Read | Phantom Read | MySQL 기본값 |
|-----------|------------|---------------------|---------------|----------------|
| READ UNCOMMITTED | ✅ 발생 | ✅ 발생 | ✅ 발생 | ❌ |
| READ COMMITTED   | ❌ 방지 | ✅ 발생 | ✅ 발생 | ❌ |
| REPEATABLE READ  | ❌ 방지 | ❌ 방지 | ✅ 방지 (InnoDB만) | ✅ |
| SERIALIZABLE     | ❌ 방지 | ❌ 방지 | ❌ 방지 | ❌ |

---

## 🔬 실험 1: Dirty Read (READ UNCOMMITTED)

### ✅ 목표
> 커밋되지 않은 데이터를 다른 세션에서 읽을 수 있는가?

### 세션 A
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SET autocommit = 0;
START TRANSACTION;

UPDATE accounts SET balance = 0 WHERE name = 'Alice';
-- COMMIT 없이 대기
```

### 세션 B
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
START TRANSACTION;

SELECT * FROM accounts WHERE name = 'Alice';
-- 👉 결과: balance가 0이면 Dirty Read 발생
```

---

## 🔬 실험 2: Non-Repeatable Read (READ COMMITTED)

### ✅ 목표
> 같은 조건의 SELECT를 트랜잭션 중 두 번 했을 때, 값이 바뀌는가?

### 세션 A
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET autocommit = 0;
START TRANSACTION;

SELECT balance FROM accounts WHERE name = 'Bob';
-- 결과: 150
-- 대기
```

### 세션 B
```sql
UPDATE accounts SET balance = 50 WHERE name = 'Bob';
COMMIT;
```

### 다시 세션 A
```sql
SELECT balance FROM accounts WHERE name = 'Bob';
-- 👉 결과가 150 → 50이면 Non-Repeatable Read 발생
```

---

## 🔬 실험 3: Phantom Read (REPEATABLE READ)

### ✅ 목표
> 조건 기반 SELECT 결과가 중간에 INSERT된 row로 인해 달라지는가?

### 세션 A
```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET autocommit = 0;
START TRANSACTION;

SELECT COUNT(*) FROM accounts WHERE balance >= 100;
-- 👉 결과: 2
-- 대기
```

### 세션 B
```sql
INSERT INTO accounts (name, balance) VALUES ('Charlie', 200);
COMMIT;
```

### 다시 세션 A
```sql
SELECT COUNT(*) FROM accounts WHERE balance >= 100;
-- 👉 결과가 여전히 2이면 팬텀 리드 방지됨
-- 👉 결과가 3이면 팬텀 리드 발생 (정상적이면 안 보임)
```

---

## 🔬 실험 4: Snapshot 고정과 커밋 후 반응

> 커밋된 INSERT가 트랜잭션 중간에는 보이지 않고, COMMIT 후에만 보이는지 확인

### 세션 A
```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET autocommit = 0;
START TRANSACTION;

SELECT * FROM accounts WHERE name = 'Charlie';
-- 👉 결과 없음 (snapshot 고정 전)
```

### 세션 B
```sql
INSERT INTO accounts (name, balance) VALUES ('Charlie', 200);
COMMIT;
```

### 세션 A 계속
```sql
SELECT * FROM accounts WHERE name = 'Charlie';
-- 👉 결과 없음 (snapshot 기준이라 안 보임)

COMMIT;

SELECT * FROM accounts WHERE name = 'Charlie';
-- 👉 이제 보임 (새 snapshot으로 반영)
```

---

## 🔚 결론 요약

- ✅ INSERT + COMMIT은 DB에 실제 반영됨
- ❌ 하지만 트랜잭션 중에는 snapshot isolation으로 인해 보이지 않을 수 있음
- ✅ MySQL (InnoDB)는 REPEATABLE READ에서도 팬텀 리드를 방지하는 특수한 구조 (MVCC + next-key lock)
