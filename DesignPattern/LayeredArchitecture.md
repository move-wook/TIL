# 레이어드 아키텍처 (Layered Architecture)

소프트웨어 시스템을 관심사에 따라 수직 계층으로 분리하여 구성한 전통적인 아키텍처 패턴.

---

## 🔁 계층 구조

[Presentation Layer] 👁 사용자와의 인터페이스
↓
[Application Layer] 🧠 유즈케이스와 흐름 제어
↓
[Domain Layer] 🧱 핵심 비즈니스 규칙
↓
[Infrastructure Layer] 🛠 기술적 상세 (DB, 외부 시스템 등)

yaml
복사
편집

---

## 📚 각 레이어 역할

### 1. Presentation Layer
- 사용자 요청 수신 및 응답 반환
- 예: Spring `@Controller`, JSP, React

~~~
@RestController
@RequestMapping("/users")
public class UserController {
    private final UserJoinService userJoinService;

    @PostMapping("/join")
    public ResponseEntity<Void> join(@RequestBody UserJoinRequestDto dto) {
        userJoinService.join(dto);
        return ResponseEntity.ok().build();
    }
}
~~~

---

### 2. Application Layer
- 유즈케이스 실행, 흐름 제어
- 도메인 객체 orchestrate
- 트랜잭션 시작점 역할

~~~
@Service
public class UserJoinService {
    private final UserRepository userRepository;

    @Transactional
    public void join(UserJoinRequestDto dto) {
        User user = User.create(dto.getEmail(), dto.getPassword());
        userRepository.save(user);
    }
}
~~~

---

### 3. Domain Layer
- 비즈니스 규칙의 핵심
- 기술에 의존하지 않는 순수한 로직
- 엔티티, 밸류 오브젝트, 도메인 서비스 포함

~~~
public class User {
    private String email;
    private String password;

    private User(String email, String password) {
        this.email = email;
        this.password = password;
    }

    public static User create(String email, String password) {
        if (!email.contains("@")) throw new IllegalArgumentException("Invalid email");
        return new User(email, password);
    }
}
~~~

---

### 4. Infrastructure Layer
- 기술 의존 영역
- DB, 메시지큐, 외부 API 등
- Repository 인터페이스의 구현체 위치

~~~
@Repository
public class JpaUserRepository implements UserRepository {
    @PersistenceContext
    private EntityManager em;

    @Override
    public void save(User user) {
        em.persist(user);
    }
}
~~~

---

## 🎯 목적

- 관심사의 분리(Separation of Concerns)
- 테스트 및 유지보수 용이성
- 계층 간 의존 방향 명확화 (위 → 아래 단방향)

---

## 🌀 흐름 예시: 회원가입

1. **Controller**: HTTP 요청 → DTO 파싱
2. **Service**: 트랜잭션 시작, 도메인 객체 생성
3. **Domain**: 유효성 검증 및 엔티티 생성
4. **Repository**: DB에 저장

---

## ⚠️ 전이 시점 판단

| 발생 상황               | 고려해야 할 변화 방향             |
|------------------------|----------------------------------|
| 서비스 클래스 비대화    | 유즈케이스 단위로 세분화          |
| 도메인 로직의 침식      | 클린 아키텍처, 도메인 보호        |
| 기술 의존 높은 도메인   | 인터페이스 분리, 인프라 분리      |
| 테스트 어려움           | 계층별 Mock 및 테스트 전략 정비   |

---

## 🧭 시야 전이: 확장 가능한 구조

### 헥사고날 아키텍처
- 도메인을 중심으로 외부 인터페이스를 포트/어댑터로 분리

### 클린 아키텍처
- 의존성 역전을 통해 도메인 → 인터페이스로 방향을 고정

### DDD 기반 아키텍처
- 유비쿼터스 언어, 어그리거트, 도메인 이벤트 등으로 정교화

---

## ✅ 요약

- 가장 널리 쓰이는 기본 아키텍처 패턴
- 초기에 적합하지만 구조적 비대화를 방지하기 위한 지속적 개선 필요
