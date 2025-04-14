# Filter와 Interceptor 차이

- Filter는 DispatcherServlet이 실행되기 전, Interceptor는 DispathcerServlet이 실행된 후
- Fitler는 스프링이 관리 X
- Interceptor는 스프링이 관리한다

## 설정 위치

- Filter는 web.xml
- Interceptor는 spring-servlet.xml

## 구현방식 

- Filter는 web.xml에서 설정을 하면 구현이 가능하지만, Interceptor는 설정은 물론 메서드 구현이 필요하다.

## Interceptor

- 구현방식은 2가지
    - HandlerInterceptor 인터페이스를 구현하는 방법
    - HandlerInterceptorAdapter 클래스를 상속 받는 방법
    - 인터페이스를 구현하는 방식보단 클래스를 방속받는 방식을 더 많이 쓴다.
    
