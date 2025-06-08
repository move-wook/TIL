# 📌 Filter vs Interceptor 차이점 정리

## ✅ 1. 정의

| 항목        | Filter                                   | Interceptor                             |
|-------------|------------------------------------------|------------------------------------------|
| 소속        | Java EE (`javax.servlet.Filter`)         | Spring MVC (`HandlerInterceptor`)        |
| 작동 시점   | DispatcherServlet 이전                   | DispatcherServlet 이후                   |
| 작동 대상   | 모든 요청 (정적 리소스 포함)             | 스프링 컨트롤러 (`@Controller`) 요청만   |

---

## ✅ 2. 주요 메서드

| Filter 메서드                            | Interceptor 메서드                          |
|------------------------------------------|---------------------------------------------|
| `doFilter(request, response, chain)`     | `preHandle()`, `postHandle()`, `afterCompletion()` |

---

## ✅ 3. 주요 용도

| Filter                                   | Interceptor                                 |
|------------------------------------------|---------------------------------------------|
| 인코딩 설정 (`UTF-8`)                   | 인증 여부 확인 및 사용자 권한 체크          |
| CORS 헤더 처리                          | 요청 전 사용자 정보 주입                    |
| 모든 요청 로깅                          | 응답 이후 감사 로그 기록                    |
| 정적 리소스 처리 가능                  | 컨트롤러 단에서만 작동                     |

---

## ✅ 4. 설정 방법

| Filter                                   | Interceptor                                 |
|------------------------------------------|---------------------------------------------|
| `web.xml` 또는 Java Config 등록         | `WebMvcConfigurer.addInterceptors()` 오버라이드 |
| `@WebFilter`, `FilterRegistrationBean` 사용 가능 | 스프링 빈으로 등록 필요                    |

---

## ✅ 5. 설계 시 고려 기준

- 정적 리소스 포함 모든 요청 → **Filter**
- 인증/인가 처리, 권한 검증 → **Interceptor**
- 공통적인 요청 전처리 (ex. 로깅) → **Filter**
- 컨트롤러 진입 전/후 로직 분기 → **Interceptor**

---

## ✅ 6. 처리 흐름

```plaintext
Client
  ↓
Filter (ex. 인코딩, JWT 검사)
  ↓
DispatcherServlet
  ↓
Interceptor (ex. 로그인 여부 확인, 권한 체크)
  ↓
Controller
  ↓
View or JSON Response
