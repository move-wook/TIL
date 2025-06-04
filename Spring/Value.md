# `@Value` 주의점 정리

## ✅ 기본 개념

`@Value`는 Spring에서 외부 설정 값을 주입받기 위한 어노테이션이다.  
`application.properties`, `application.yml`, 환경변수 등에서 값을 읽어와 필드에 주입한다.

예시:

@Value("${myapp.timeout}")
private int timeout;

---

## ⚠️ 사용 시 주의사항

### 1. 타입 불일치

- 설정 값과 필드 타입이 다르면 `NumberFormatException`, `IllegalArgumentException` 발생

예시:

@Value("${timeout}") // timeout=abc 라면 오류 발생
private int timeout;

---

### 2. 값 미정의 (Null 위험)

- 설정 값이 없으면 애플리케이션 시작 시 오류
- 기본값 설정으로 방지 가능

예시:

@Value("${my.prop:defaultValue}")
private String value;

---

### 3. 복잡한 SpEL 사용

- 표현력은 강력하나 유지보수가 어려움

예시:

@Value("#{2 * T(java.lang.Math).PI}")
private double piTimesTwo;

> 복잡한 계산은 Java 코드로 분리하는 것이 바람직

---

### 4. 초기화 순서 이슈

- 스프링 컨텍스트 초기화 시점과 충돌 가능성 있음
- `@PostConstruct` 내에서 `@Value` 필드 접근 시 주의

---

### 5. 테스트 어려움

- `@Value`는 필드에 직접 주입되므로 테스트에서 설정값 변경이 어렵다
- 테스트용 설정 적용이 잘 안 먹히는 경우 있음

---

## 📌 `@Value` vs `@ConfigurationProperties`

| 항목                         | @Value                         | @ConfigurationProperties           |
|----------------------------|--------------------------------|------------------------------------|
| 단일 값 주입                | 적합                            | 가능하나 과함                      |
| 여러 속성(설정 그룹) 주입     | 반복적이고 가독성 떨어짐         | 깔끔하게 바인딩 가능               |
| 기본값 지정                  | 가능 (`@Value("${a:default}")`) | 가능 (`@ConstructorBinding`)       |
| 타입 변환                    | 제한적, 자동 변환만 가능         | 강력한 타입 매핑 지원              |
| IDE 지원 (컴플리션, 검증 등) | 제한적                          | 풍부한 지원                        |
| 테스트 용이성                | 어려움                          | 명확한 테스트 가능                 |

---

## 🧭 권장 사용 가이드라인

| 상황                              | 권장 방식                  |
|----------------------------------|---------------------------|
| 단일 값 주입이 필요한 경우           | `@Value` 사용              |
| 설정이 많거나 그룹 단위로 관리할 경우 | `@ConfigurationProperties` |
| 민감한 값 (비밀번호, 키 등)       | 환경변수 / Vault 사용      |
| 테스트에서 설정을 자주 바꾸는 경우   | `@ConfigurationProperties` 사용 + Mocking |

---

## 🔄 다음 시야 전이 제안

- 설정 전략을 설계할 때 고려할 포인트:
  - 어떤 설정은 환경마다 다르고
  - 어떤 설정은 하드코딩이 더 적절함
  - 어떤 설정은 보안과 연관됨

→ `@Value`를 쓰느냐 마느냐보다  
**“어떤 설정을 어디에 두고 어떻게 유지보수할 것인가”**에 초점을 맞춰야 함
