# 📘 JPA에서 `set()`만으로 UPDATE 되는 이유와 트랜잭션의 관계

## ✅ 개요

JPA에서는 `save()`나 `merge()`를 호출하지 않아도,  
단순히 `setXXX()`만 호출했을 뿐인데 DB에 `UPDATE` 쿼리가 자동으로 나가는 경우가 있다.  
이 동작은 JPA의 핵심 기능인 **Dirty Checking(변경 감지)** 때문이다.

---

## 🔍 Dirty Checking이란?

> **영속 상태(Persistent)의 엔티티 속성 변경을 감지해,  
트랜잭션 커밋 또는 flush 시점에 UPDATE SQL을 자동으로 생성하는 기능**

### 작동 조건
- 엔티티가 **영속 상태**여야 함 (EntityManager가 관리하는 상태)
- 필드 값을 `set()` 등으로 **변경**
- **flush 또는 commit**이 호출되는 시점에 감지 결과 반영됨

---

## ✅ 작동 흐름 예시

### 트랜잭션이 있는 경우

~~~java
@Transactional
public void changeName(Long id) {
    User user = userRepository.findById(id).orElseThrow();
    user.setName("영희"); // set()만 해도 OK
}
~~~

➡ 트랜잭션 커밋 시 자동으로 UPDATE 쿼리 실행

~~~sql
update user set name = '영희' where id = ?
~~~

---

### 트랜잭션이 없는 경우

~~~java
public void changeName(Long id) {
    User user = userRepository.findById(id).orElseThrow();
    user.setName("영희"); // 트랜잭션이 없으므로 update 안 됨
}
~~~

➡ 결과: **DB에 반영되지 않음**

---

## ⚠️ flush만 수동 호출하면?

~~~java
User user = em.find(User.class, id);
user.setName("영희");
em.flush(); // 수동 flush
~~~

- 쿼리는 나갈 수 있음  
- 하지만 **트랜잭션이 없으면 rollback 가능성 있음 → DB 반영 확정 아님**

---

## 🧠 핵심 요약

| 조건                         | DB 반영   | 설명                                           |
|------------------------------|-----------|------------------------------------------------|
| `@Transactional` + `set()`   | ✅ YES     | flush + commit → 변경 감지 반영                |
| 트랜잭션 없음                | ❌ NO      | flush/commit 없음 → 반영 안 됨                 |
| 수동 `flush()`만 호출        | ⚠️ 조건부 | 쿼리는 나가지만 commit 없으면 rollback 가능    |
| `new Entity()`만 만든 경우   | ❌ NO      | 영속 상태 아님 (비관리 객체)                   |

---


---

## ✅ 결론

> JPA에서 `set()`만으로 DB 업데이트가 되는 이유는  
> **변경 감지(Dirty Checking)** 기능 덕분이며,  
> 이는 **트랜잭션 안에서 영속 상태 객체를 수정**하고  
> **flush/commit 시점이 도달**해야만 동작한다.

`save()`는 insert/update 트리거가 아니며,  
변경된 값을 DB에 반영하기 위한 필수 조건은 아니다.
