# 🔐 HTTPS란? (Hypertext Transfer Protocol Secure)

## ✅ HTTP vs HTTPS

| 항목 | HTTP | HTTPS |
|------|------|-------|
| 데이터 전송 | **평문(Plain Text)** | **암호화된 데이터** |
| 보안성 | 없음 (도청·위조 위험) | SSL/TLS로 암호화 |
| 포트 | 80 | 443 |
| URL 스킴 | `http://` | `https://` |

---

## 🔍 HTTPS의 필요성

- HTTP는 데이터를 **암호화하지 않기 때문에** 중간자(MITM) 공격, 도청, 데이터 변조에 취약
- HTTPS는 **SSL/TLS 프로토콜을 사용하여 암호화 계층을 추가함**

---

## 🚀 HTTPS 적용 방법

1. **인증서 발급**
   - 인증 기관(CA, Certificate Authority)에서 **서버 인증서** 발급
   - Let’s Encrypt (무료), DigiCert, GlobalSign 등 사용 가능

2. **서버 설정**
   - 웹 서버 (Nginx, Apache 등)에 발급받은 인증서 및 개인 키 설정
   - 예: `ssl_certificate`, `ssl_certificate_key` 디렉티브 설정

3. **클라이언트 요청 시 HTTPS로 리디렉션**
   - HTTP 요청을 HTTPS로 리디렉션 처리 (301)

---

## 🔑 HTTPS 동작 원리 (TLS 핸드셰이크 중심)

### 1. Client Hello
- 클라이언트가 서버에게 아래 정보 전달:
  - TLS 버전
  - 암호화 방식 목록
  - 클라이언트 랜덤값 (Client Random)

### 2. Server Hello
- 서버가 아래 정보로 응답:
  - TLS 버전, 암호화 방식 선택
  - 서버 인증서 (공개키 포함)
  - 서버 랜덤값 (Server Random)

### 3. 인증서 검증
- 클라이언트는 서버 인증서를 **CA의 공개키**로 복호화하여 신뢰성 검증

### 4. Pre-Master Secret 전송
- 클라이언트는:
  - `Pre-Master Secret` 생성
  - 서버의 **공개키로 암호화**하여 전송

- 서버는:
  - 자신의 **개인키로 복호화**하여 Pre-Master Secret 확보

### 5. 세션 키 생성
- 양쪽은 아래 3개를 기반으로 **Master Secret** 생성:
  - Client Random
  - Server Random
  - Pre-Master Secret

- 이후 Master Secret으로부터 **세션 키(Session Key)** 파생

---

## 🔐 이후 통신 방식: 대칭키 암호화

- 초기 핸드셰이크만 공개키 기반 암호화 (비대칭 암호화)
- 이후 데이터 전송은 빠른 성능의 **대칭키 암호화 방식**으로 처리됨

---

## 🧠 백엔드 관점의 시야 전이

| 대상 | 고려 요소 |
|------|------------|
| WAS | HTTPS 직접 지원 가능 (Tomcat 등), 일반적으로는 리버스 프록시 뒤에서 동작 |
| 웹 서버 | Nginx에서 인증서 설정 및 HTTPS 핸들링 → WAS는 HTTP로 연결 |
| 보안 테스트 | HSTS, TLS 1.3 지원, 인증서 자동 갱신(Let's Encrypt + Certbot) 확인 필요 |
| DevOps | HTTPS 적용 후 `health check`, `redirect`, `SSL cert renewal` 자동화 체크 |

---

## 📚 추가 학습 자료

- [Let's Encrypt 공식 홈페이지](https://letsencrypt.org/)
- [SSL Labs - SSL Test](https://www.ssllabs.com/ssltest/)
- [Mozilla TLS 가이드라인](https://infosec.mozilla.org/guidelines/web_security#https-and-tls)

