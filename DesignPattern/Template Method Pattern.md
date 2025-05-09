#  템플릿 메서드 패턴 (Template Method Pattern)

템플릿 메서드 패턴은 **상위 클래스가 알고리즘의 전체 흐름(템플릿)을 정의**하고,  
**하위 클래스가 일부 단계만 오버라이드하여 구체적인 동작을 구현**하는 디자인 패턴입니다.

---

## 핵심 개념

| 구성 요소        | 설명 |
|------------------|------|
| 템플릿 메서드     | 전체 알고리즘 흐름을 고정하는 메서드 (`final`일 수 있음) |
| Hook 메서드      | 하위 클래스에서 오버라이드하여 구현할 수 있는 유동적인 단계 |

---

## 실전 예시: `HttpServlet` (Java Servlet API)

### 구조 개요

`HttpServlet`은 요청 처리 흐름을 고정하고, 세부 동작만 서브클래스에 위임합니다.

```java
public abstract class HttpServlet {
    protected void service(HttpServletRequest req, HttpServletResponse resp) {
        String method = req.getMethod();
        if (method.equals("GET")) {
            doGet(req, resp);
        } else if (method.equals("POST")) {
            doPost(req, resp);
        }
        // ...
    }

    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED);
    }

    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED);
    }
}

public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        resp.getWriter().write("Hello Template Method Pattern!");
    }
}
```
## 템플릿 메서드 패턴의 장점

- 중복 제거
  알고리즘의 공통 흐름을 상위 클래스에서 정의하여 중복 코드를 제거할 수 있음.

- 역할 분리
  상위 클래스는 "무엇을 할지", 하위 클래스는 "어떻게 할지"에 집중 → 관심사 분리.

- 구조 일관성 유지  
  템플릿 메서드 덕분에 전체 알고리즘 구조가 일관되게 유지됨.

- 재사용성 향상
  여러 구현체에서 공통 처리 흐름을 재사용 가능.
---
## 템플릿 메서드 패턴의 단점

- 유연성 제한
  상속 기반이기 때문에 동적으로 알고리즘 구조를 변경하기 어려움.

- 하위 클래스 의존 증가
  하위 클래스가 너무 많아질 경우 관리가 어려워질 수 있음.

- 다중 상속 불가(Java)
  Java에선 클래스 단일 상속만 지원하므로 템플릿 클래스를 하나만 상속할 수 있음.

