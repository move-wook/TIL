# 🐱 Apache Tomcat 정리

## 1. Tomcat이란?

**Apache Tomcat**은 자바 웹 애플리케이션을 실행하기 위한 **웹 서버이자 서블릿 컨테이너**이다.

- HTTP 요청/응답 처리 (웹 서버 역할)
- Servlet / JSP 실행 (서블릿 컨테이너 역할)
- 가벼운 WAS(Web Application Server)의 대표격

---

## 2. Tomcat의 핵심 구성 요소

| 구성 요소 | 설명 |
|-----------|------|
| **Catalina** | 서블릿 컨테이너의 핵심. 요청을 받아 서블릿 실행 |
| **Coyote** | HTTP 커넥터. 클라이언트와의 통신을 담당 |
| **Jasper** | JSP를 서블릿으로 변환해주는 JSP 컴파일러 |
| **Engine / Host / Context** | 가상 호스트 및 서블릿 실행 환경 구성 요소들 |

---

## 3. Tomcat의 실행 흐름

```
[1] 브라우저 → HTTP 요청 (/login.jsp)
[2] Coyote가 요청 수신
[3] Catalina가 URL 매핑 확인
[4] JSP라면 → Jasper가 Servlet으로 컴파일
[5] Servlet이 로직 처리 및 응답 생성
[6] 결과 HTML → 브라우저로 전송
```

---

## 4. Tomcat의 필요성

- 브라우저는 Java 코드를 직접 실행할 수 없음
- Java 웹 로직(서블릿, JSP 등)을 **서버에서 실행**해주는 환경이 필요
- Tomcat은 이러한 실행 환경을 제공함

---

## 5. 사용 예시

- Spring MVC 기반 프로젝트
- JSP 기반 관리자 페이지
- Spring Boot 내장 톰캣 (별도 설치 필요 없음)
- Nginx 등과 리버스 프록시 구성 시 백엔드 WAS 역할

---

## 6. Tomcat의 한계

- **JEE 풀 스펙 지원 X**
  - EJB, JMS 등은 미지원
- 대형 WAS(JBoss, WebLogic 등)에 비해 **경량화되어 있음**

---

## 7. 사고 확장 포인트

- Spring Boot에서 **왜 톰캣을 내장 서버로 쓰는가?**
- **Jetty, Undertow** 등 다른 서블릿 컨테이너와의 차이는?
- Tomcat의 **성능 튜닝**은 어떻게 접근해야 할까?
