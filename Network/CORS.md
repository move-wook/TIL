# CORS(Cross-Origin Resource Sharing) 발생 원인과 해결 방법

## CORS란?

브라우저의 **Same-Origin Policy(동일 출처 정책)** 으로 인해  
다른 Origin의 리소스를 호출할 때 발생하는 **보안 차단 문제**입니다.

> 예: React 앱이 `http://localhost:3000`에서 실행 중이고,  
> API 서버가 `http://localhost:8080`에 있을 경우 — 서로 다른 Origin

---

## 왜 발생하는가?

### Same-Origin Policy란?

브라우저는 다음 3가지가 모두 같아야 같은 Origin으로 간주합니다:

- 프로토콜 (http / https)
- 호스트 (localhost, example.com)
- 포트 (3000, 8080 등)

---

### Preflight 요청이란?

다음 조건 중 하나라도 만족하면 브라우저는 실제 요청 전에  
`OPTIONS` 메서드로 Preflight(사전 요청)을 먼저 보냅니다:

- `Authorization` 등의 커스텀 헤더 사용
- `PUT`, `DELETE`, `PATCH` 등의 메서드 사용
- `Content-Type`이 `application/json` 등 비표준

서버가 여기에 올바르게 응답하지 않으면 **브라우저는 본 요청을 차단**합니다.

---

## 서버에서 응답할 CORS 헤더 예시

서버는 아래와 같은 응답 헤더를 포함시켜야 합니다:

~~~
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 3600
~~~

---

## Spring에서의 해결 방법

### 방법 1. `WebMvcConfigurer` 사용 (Spring MVC 환경)

~~~
@Configuration
public class CorsConfig implements WebMvcConfigurer {
  @Override
  public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/api/**")
            .allowedOrigins("http://localhost:3000")
            .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
            .allowedHeaders("Content-Type", "Authorization")
            .maxAge(3600);
  }
}
~~~

> 주의: `@EnableWebMvc`를 사용 중이라면 이 설정이 무시될 수 있음

---

### 방법 2. `OncePerRequestFilter`로 필터 직접 구현

~~~
@Component
public class CorsFilter extends OncePerRequestFilter {
  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
          throws ServletException, IOException {

    response.setHeader("Access-Control-Allow-Origin", "*"); // 또는 http://localhost:3000
    response.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
    response.setHeader("Access-Control-Allow-Headers", "Content-Type, Authorization");
    response.setHeader("Access-Control-Max-Age", "3600");

    if ("OPTIONS".equalsIgnoreCase(request.getMethod())) {
      response.setStatus(HttpServletResponse.SC_OK);
      return;
    }

    filterChain.doFilter(request, response);
  }
}
~~~

---



