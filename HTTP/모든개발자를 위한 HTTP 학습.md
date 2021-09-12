# 모든 개발자를 위한 HTTP 학습 강의 정리 

## IP(인터넷 프로토콜)

 - IP는 주소 역할을 한다.

 - 편지를 주고 받거나 택배를 보낼때 주소가 필요한 것 처럼 네트워크 통신을 할 때도 어느 주소로 데이터를 보낼지에 대한 주소가 필요하다 그 역할이 IP이다.

 - 패킷(규칙)이라는 통신 단위로 데이터 전달을 한다.

 * 한계  

 - 비연결성   

    - 패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷 전송

 - 비신뢰성  

    - 중간에 패킷이 사라지면?  

    - 패킷이 순서대로 안오면?

 - 프로그램 구분

    - 같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이면?

## TCP/UDP

  - 인터넷 프로토콜 스택 4계층
    - 애플리케이션 계층 : HTTP,FTP
    - 전송계층 : TCP,uDP
    - 인터넷계층 : IP
    - 네트워크계층


    - TCP 특징  
        - 전송 제어 프로토콜

        - 연결지향  : 연결을 하고 데이터를 전송한다.
            - TCP 3 way handshake(가상 연결)
            1. SYN : 접속 요청
            2. SYN + ACK :요청 수락
            3. ACK
        - 데이터 전달 보증
        - 순서 보장
        - 신뢰할 수 있는 프로토콜(신뢰성)

    - UDP 특징
    
        - 사용자 데이터그램 프로토콜
        - 단순하고 빠름
        - 기능이 거의 없음
        - IP와 거의 같고 + PORT가 추가됨 + 체크섬
        - 애플리케이션에서 추가 작업 필요

## PORT

    - 같은 IP 내에서 프로세스를 구분
    
        - 0 ~ 65535 할당가능
        - 0 ~ 1023: 잘 알려진 포트, 사용하지 않는 것이 좋음
            - FTP : 20,21
            - TELNET : 23
            - HTTP : 80
            - HTTPS : 443

## DNS

    - 도메인 네임 시스템 
    + IP는 기억하기 힘들다 
    + IP는 변경될 수 있다
     IP-> 도메인 네임으로 변환하면 도메인 네임으로 접근이 가능함

     - 도메인명을 IP주소로 변환


## URI(Uniform Resource Identifier)

    - URI? URL? URN?

    - URI 는 locator,name 또는 둘다 추가로 분류될 수 있다.


        - URL : Resource Locator  
             - foo://example.com:8042/over/there?name=ferret#nose
            foo: scheme
            example.com:8042 : authority
            over/there? : path
            name=ferret : query
            nose : fragment

            - Locator: 리소스가 있는 위치를 지정


        - URN : Resource Name 
            - urn:example:animal:ferret:nose

            - Name : 리소스에 이름을 부여
             
    - 단어의 뜻
    
    - Uniform: 리소스 식별하는 통일된 방식
    - Resource : 자원,URI로 식별할 수 있는 모든것
    - Identifier : 다른 항목과 구분하는데 필요한 정보

    - 전체 문법
     - scheme://[uerinfo@]host[:port][/path][?query][#fragment]

     - 프로토콜 : http
     - 호스트명 : www.google.com
     - 포트 번호 : 433
     - 패스 :/search
     - 쿼리 파라미터 : q=hello&hl=ko


## 웹 브라우저 요청 흐름

    https://www.google.com:443/search?q=hello&hl=ko

    - DNS 조회 : www.google.com
    - HTTP 요청 메세지 생성
    [
        GET/search?q=hello&hl=ko HTTP/1.1
        Host:www.google.com
    ]
    
    HTTP 메시지 전송
        
        1. 웹 브라우저가 HTTP 메시지 생성
        2. SOCKET 라이브러리를 통해 전달
            - A : TCP/IP 연결
            - B : 데이터 전달
        3. TCP/IP 패킷 생성, HTTP 메시지 포함
       

## HTTP
    
     - 모든것이 HTTP  

        HTTP 메시지에 모든 것을 전송
         - HTML, TEXT
         - IMAGE,음성,영상,파일
         - JSON,XML
         - 거의 모든 형태의 데이터 전송 가능
         - 서버간에 데이터를 주고 받을 때도 대부분  HTTP 사용
         - HTTP 시대

        특징 
            - 클라이언트 서버 구조
                
                [
                    - Request Response 구조
                    - 클라이언트는 서버에 요청을 보내고 ,응답을 대기
                    - 서버가 요청에 대한 결과를 만들어서 응답
                ]
                
            - 무상태 프로토콜

                스테이스리스
                [
                    - 서버가 클라이언트의 상태를 보존하지 않는다
                    - 장점 : 서버 확장성이 높다
                    - 단점 : 클라이언트가 추가 데이터 전송
                ]
                무상태는 응답 서버를 쉽게 바꿀 수 있다 - >무한한 서버 증설 가능

                비연결성
                [
                    - HTTP는 기본이 연결을 유지하지 않는 모델
                    - 일반적으로 초 단위 이하 빠른 속도로 응답
                    - 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 작음
                    - 서버 자원을 매우 효율적으로 사용 가능

                    한계와 극복
                    - TCP/IP 연결을 새로 맺어야 함 
                    - 웹 브라우저로 사이트를 요청하면 여러가지 데이터 자원을 함께 다운로드
                    - 지금은 HTTP 지속 연결로 문제 해결
                ]

                
            - HTTP 메시지

            
            - 단순함, 확장 가능










     - 클라이언트 서버 구조
     
     - Stateful, Stateless

     - 비 연결성  

     - HTTP 메시지

     
