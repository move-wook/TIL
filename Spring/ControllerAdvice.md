# 📘 Spring `@ControllerAdvice` 정리

## 1. 개요

`@ControllerAdvice`는 Spring MVC에서 **전역적인 예외 처리, 바인딩 설정, 모델 속성 추가** 등을 담당하는 **AOP 기반의 컨트롤러 보조 컴포넌트**이다.

- 공통 로직을 모듈화하여 **관심사 분리 (Separation of Concerns)** 실현
- 실무에선 주로 **예외 처리 전용 핸들러**로 사용됨

---

## 2. 주요 기능

| 어노테이션            | 기능 설명 |
|----------------------|-----------|
| `@ExceptionHandler`  | 특정 예외 발생 시 실행할 메서드 정의 |
| `@InitBinder`        | 요청 파라미터 바인딩 커스터마이징 |
| `@ModelAttribute`    | 모든 컨트롤러에 공통 데이터 주입 |

---

## 3. 간단한 사용 예시

~~~java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArg(IllegalArgumentException e) {
        return ResponseEntity
            .badRequest()
            .body("잘못된 요청입니다: " + e.getMessage());
    }
}
~~~

---

## 4. 왜 써야 하나?

- 컨트롤러마다 중복되는 `try-catch` 제거 가능
- 예외 처리 로직을 비즈니스 흐름에서 분리
- 일관된 에러 응답 포맷 제공 가능 (`ApiErrorResponse` 등)
- **유지보수성**, **가독성**, **확장성** 향상

---

## 5. 실전 구조 예시

### (1) ErrorCode enum

~~~java
public enum ErrorCode {
    INVALID_INPUT(HttpStatus.BAD_REQUEST, "잘못된 입력입니다."),
    INTERNAL_ERROR(HttpStatus.INTERNAL_SERVER_ERROR, "서버 오류");

    private final HttpStatus status;
    private final String message;

    ErrorCode(HttpStatus status, String message) {
        this.status = status;
        this.message = message;
    }

    public HttpStatus getStatus() { return status; }
    public String getMessage() { return message; }
}
~~~

---

### (2) CustomException

~~~java
public class CustomException extends RuntimeException {
    private final ErrorCode errorCode;

    public CustomException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }

    public ErrorCode getErrorCode() {
        return errorCode;
    }
}
~~~

---

### (3) ApiErrorResponse

~~~java
public class ApiErrorResponse {
    private int status;
    private String message;

    public ApiErrorResponse(ErrorCode errorCode) {
        this.status = errorCode.getStatus().value();
        this.message = errorCode.getMessage();
    }

    // Getter, Setter 생략
}
~~~

---

### (4) 전역 예외 핸들러

~~~java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(CustomException.class)
    public ResponseEntity<ApiErrorResponse> handleCustom(CustomException e) {
        return ResponseEntity
            .status(e.getErrorCode().getStatus())
            .body(new ApiErrorResponse(e.getErrorCode()));
    }
}
~~~

---

## 6. 확장 포인트 (시야 전이)

- ✅ 에러 코드 체계화 (`enum ErrorCode`)
- ✅ 로깅 연계 (Sentry, Slack, 카카오 알림 등)
- ✅ HTTP 상태코드 명시적 설계 (`@ResponseStatus` or `ResponseEntity`)
- ✅ 클라이언트 응답 포맷 표준화 (Swagger 연계)

---

## 7. 마무리

`@ControllerAdvice`는 예외 처리의 기술적 관심사를 도메인 로직과 분리하는 가장 대표적인 Spring의 도구다.  
잘 설계된 예외 처리 구조는 유지보수성과 협업 효율을 동시에 끌어올릴 수 있다.
