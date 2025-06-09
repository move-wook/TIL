# 🌐 Spring MVC 실행 흐름 정리

## 1. 개요
Spring MVC는 Front Controller 패턴 기반 웹 프레임워크로, 모든 요청을 `DispatcherServlet`이 받아서 적절한 컴포넌트로 위임하고 최종 응답까지 책임지는 구조다.

---

## 2. 실행 흐름 (요청 → 응답)

```plaintext
[1] 클라이언트 요청
  ↓
[2] DispatcherServlet
  ↓
[3] HandlerMapping
     - 어떤 Controller가 처리할지 결정
  ↓
[4] HandlerAdapter
     - 해당 Controller를 실행 가능한 형태로 호출
  ↓
[5] Controller (@Controller)
     - 비즈니스 로직 처리
     - ModelAndView 생성
  ↓
[6] ViewResolver
     - 논리 View 이름 → 실제 View 경로 매핑
  ↓
[7] View (ex. JSP)
     - 화면 렌더링 수행
  ↓
[8] DispatcherServlet이 최종 응답 반환
```

---

## 3. 주요 컴포넌트 역할

| 컴포넌트 | 역할 |
|----------|------|
| **DispatcherServlet** | 전체 요청/응답 흐름 제어 |
| **HandlerMapping** | URL에 해당하는 Controller 매핑 |
| **HandlerAdapter** | Controller 실행 보조 |
| **Controller** | 요청 처리 로직, 모델 생성 |
| **ModelAndView** | View 이름 + 모델 데이터 담는 객체 |
| **ViewResolver** | View 이름 → 실제 파일 경로로 변환 |
| **View** | View 렌더링 (ex. JSP, Thymeleaf 등) |

---

## 4. 부가 컴포넌트

| 구성 요소 | 설명 |
|-----------|------|
| `@RequestMapping`, `@GetMapping` | 요청 URL과 메서드 매핑 |
| `@ResponseBody` | ViewResolver 생략, HTTP Body로 바로 응답 |
| `@ControllerAdvice` | 전역 예외 처리 담당 |
| `@ExceptionHandler` | 특정 예외를 해당 메서드에서 처리 |
| **Filter** | DispatcherServlet 이전에 요청/응답 필터링 (서블릿 수준) |
| **Interceptor** | Controller 호출 전/후 처리를 담당 |
| **ArgumentResolver** | 메서드 파라미터 주입 커스터마이징 |

---

## 5. 흐름 요약 다이어그램

```plaintext
Client
  ↓
DispatcherServlet
  ↓
HandlerMapping → HandlerAdapter
  ↓
Controller
  ↓
ModelAndView
  ↓
ViewResolver
  ↓
View (렌더링)
  ↓
DispatcherServlet
  ↓
Client Response
```

---
# 📡 Spring REST API 실행 흐름

## 1. REST API란?
- **View 없이 데이터(JSON/XML)**만 응답하는 HTTP 기반 아키텍처
- `@RestController`, `@ResponseBody`가 핵심
- **UI 렌더링 없음**, 대신 클라이언트(React, 앱 등)가 렌더링 담당

---

## 2. 실행 흐름 요약

```plaintext
[1] 클라이언트 요청 (HTTP)
   ↓
[2] DispatcherServlet
   ↓
[3] HandlerMapping
    - 어떤 Controller가 처리할지 결정
   ↓
[4] HandlerAdapter
    - 해당 Controller 호출
   ↓
[5] @RestController
    - 비즈니스 로직 실행
    - 자바 객체 반환 (ex: UserDto)
   ↓
[6] HttpMessageConverter
    - 반환 객체를 JSON (또는 XML)로 변환
   ↓
[7] DispatcherServlet이 응답 본문 반환
```

---

## 3. 핵심 컴포넌트

| 컴포넌트 | 역할 |
|----------|------|
| `@RestController` | View 없이 데이터를 바로 반환 |
| `@ResponseBody` | 반환 객체를 HTTP Body에 직렬화 |
| `HttpMessageConverter` | Java 객체 ↔ JSON 변환 담당 (Jackson 등) |
| `DispatcherServlet` | 요청 흐름 관리 |
| `HandlerMapping` / `HandlerAdapter` | Controller 연결 및 실행 지원 |

---

## 4. 기존 MVC와의 차이점

| 구분 | MVC | REST API |
|------|-----|----------|
| Controller | `@Controller` | `@RestController` |
| View 사용 | O (JSP, Thymeleaf) | X |
| 반환 타입 | ModelAndView, View 이름 | Java 객체 (DTO) |
| 응답 처리 | ViewResolver → View 렌더링 | HttpMessageConverter로 JSON 변환 |
| 목적 | 화면 렌더링 | 데이터 제공 (API) |

---

## 5. 예시 코드

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public UserDto getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

> 위 코드는 `UserDto` 객체를 반환하고, Spring은 이를 자동으로 JSON으로 직렬화해서 응답 본문에 포함한다.

---

## 6. 시야 전이: 왜 REST 구조를 사용할까?

- 프론트엔드/백엔드 분리 → API 기반 개발 필수
- 클라이언트 다양화 (웹, 모바일, IoT 등)
- SPA(React, Vue 등)와의 호환성
- 테스트 용이성, 확장성

➡ 현대 서비스에서는 REST API 구조가 **기본 구조**로 자리잡은 이유

# 🔄 Spring의 HttpMessageConverter 동작 원리

## 1. 역할
- 요청 바디(JSON/XML 등)를 자바 객체로 역직렬화
- 자바 객체를 JSON/XML 등으로 직렬화하여 응답 바디에 출력

---

## 2. 요청 흐름 (read)

```plaintext
클라이언트 요청 (application/json)
   ↓
DispatcherServlet
   ↓
HandlerMapping → HandlerAdapter
   ↓
HttpMessageConverter.read()
   ↓
@RequestBody 등 파라미터 바인딩
```

---

## 3. 응답 흐름 (write)

```plaintext
Controller (객체 반환)
   ↓
HandlerAdapter
   ↓
HttpMessageConverter.write()
   ↓
응답 바디(JSON 등)에 기록
```

---

## 4. 주요 MessageConverter 목록

| 미디어 타입 | 클래스 |
|-------------|--------|
| `application/json` | MappingJackson2HttpMessageConverter |
| `application/xml` | Jaxb2RootElementHttpMessageConverter |
| `text/plain` | StringHttpMessageConverter |
| `application/x-www-form-urlencoded` | FormHttpMessageConverter |
| `multipart/form-data` | MultipartHttpMessageConverter |

---

## 5. 동작 기준

- 요청 시: `Content-Type` 헤더 기준
- 응답 시: `Accept` 헤더 기준 또는 `produces` 속성

---

## 6. 확장 포인트

- 커스텀 MessageConverter 구현 (`implements HttpMessageConverter`)
- Spring 설정 시 `WebMvcConfigurer#extendMessageConverters()` 통해 등록 가능



