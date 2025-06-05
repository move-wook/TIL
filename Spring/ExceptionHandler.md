# @ExceptionHandler 정리

## ✅ 개요

`@ExceptionHandler`는 Spring MVC에서 컨트롤러 내에서 발생한 예외를 처리할 수 있도록 해주는 어노테이션이다.  
특정 예외 타입을 지정해, 해당 예외 발생 시 실행할 메서드를 정의한다.

---

## ✅ 동작 원리

1. 컨트롤러 로직에서 예외가 발생함  
2. DispatcherServlet이 예외를 탐지  
3. 해당 예외를 처리할 `@ExceptionHandler(Exception.class)` 메서드를 찾음  
4. 예외 객체를 파라미터로 전달하며 메서드 실행  
5. 반환값(ResponseEntity, View 등)을 클라이언트에 응답으로 전달

---

## ✅ 선언 위치 및 범위

| 위치 | 설명 |
|------|------|
| 컨트롤러 클래스 내부 | 해당 컨트롤러에서만 예외 처리됨 |
| 전역 처리 클래스(@ControllerAdvice) | 전체 컨트롤러에서 발생한 예외를 처리함 |

---

## ✅ 기본 사용 예시

```java
@RestController
public class UserController {

    @GetMapping("/user/{id}")
    public User getUser(@PathVariable Long id) {
        if (id == 0) {
            throw new UserNotFoundException("유저를 찾을 수 없습니다.");
        }
        return userService.findById(id);
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

---

## ✅ 전역 예외 처리 예시 (@ControllerAdvice)

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(new ErrorResponse("USER_NOT_FOUND", ex.getMessage()));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(new ErrorResponse("INTERNAL_ERROR", "알 수 없는 오류 발생"));
    }
}
```

```java
public class ErrorResponse {
    private String code;
    private String message;

    public ErrorResponse(String code, String message) {
        this.code = code;
        this.message = message;
    }

    // Getter, Setter 생략
}
```

---

## ✅ 설계 

- **응답 포맷 통일**  
  → `ErrorResponse` DTO 구조를 정하고, 모든 예외에 적용
- **예외 계층 분리**  
  → `BusinessException`, `ValidationException` 등 커스텀 예외 도입
- **로깅 레벨 구분**  
  → 사용자 실수는 `INFO`, 서버 문제는 `ERROR`로 구분
- **상태코드 명확히 지정**  
  → 400, 403, 404, 500 등 상황에 맞게 `HttpStatus` 리턴

---

## ✅ 자주 묻는 질문 

| 질문 | 답변 |
|------|------|
| `@ExceptionHandler`가 안 먹어요 | 클래스에 `@RestController` 또는 `@ControllerAdvice`가 빠졌는지 확인 |
| 문자열만 리턴할 수 있나요? | 가능. 단, `@ResponseBody` 또는 `@RestController`가 필요함 |
| 404, 405 같은 에러는 왜 안 잡히나요? | DispatcherServlet까지 도달하지 못한 예외는 필터나 서블릿 컨테이너 레벨에서 처리해야 함 |

---

## 

`@ExceptionHandler`는 단순히 예외를 잡는 도구일 뿐이다.  
실제로 중요한 것은 예외를 어떻게 **분류**하고, 어떤 **기준으로 응답 구조를 통일**할지 결정하는 것이다.

→ 예외 코드, 사용자 메시지, 시스템 메시지를 분리해서 관리하고  
→ 프론트엔드와 협업하기 좋은 **일관된 에러 응답 규약**을 세우는 것이 핵심이다.
