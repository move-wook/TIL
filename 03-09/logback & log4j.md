# log4j(Log for java) 특징

- 주요 기능 및 특징
    1. 속도와 유연성을 고려하여 디자인되었으며, 속도에 최적화 되어 있다

    2. 멀티스레드 환경에서 사용해도 안전하다.

    3. 계층적인 로그 설정과 처리를 지원한다.

    4. 출력을 파일,콘솔,java.iO.OutputStream,Java.io.Writer,TCP,등등 여러가지로 보낼 수 있고 e-mail로 보낼 수도 있다. 

    5. 계층적인 6가지의 로그 메시지 레벨을 사용한다.(Trace,debug,info,warn,error,fatal)

- log4j 구조

    1. Logger:log4j에서 지원하는 핵심 클래스로 로깅이 일어나는 부분을 그룹화해, 필요한 그룹의 로그만을 출력하거나, 카테고리에 우선순위를 부여함으로써 여러가지 출력 방법을 지원한다.
    
    2. Appender : 로그의 출력 위치를 지정한다. Appender로 설정 가능한 출력은 콘솔, 파일 
    ConsoleAppender : 콘솔에 로그 메시지를 출력한다.
    FileAppender : 파일에 로그 메시지를 출력한다.
    RollingFileAppender : 로그의 크기가 지정한 용량 이상이 되면 다른 이름의 파일을 출력한다.
    DailyRollingFileAppender : 하루를 단위로 로그 메시지를 파일에 출력한다.
    SMTPAppender : 로그 메시지를 이메일로 보낸다.
    NTEventLogAppender : 윈도우의 이벤트 로그 시스템에 기록한다.

    3. Layout: 로그의 출력 포멧을 지정한다. 단순한 텍스트 출력, 포맷을 직접 지정한 패턴, HTML문서 형식 등을 사용할 수 있다,

- 장/단점
    - 장점

    1. 프로그램 문제 파악이 용이하다.
    
    2. 빠른 디버깅이 가능하다.
    
    3. 로그 파악이 쉽다.

    4. 로그 이력을 파일, DB등으로 남길 수 있다

    5. 효과적으로 디버깅이 가능하다.

    - 단점

    1. 로그에 대한 디바이스 입출력으로 인해 런타임 오버헤드가 발생한다

    2. 로깅을 위한 추가 코드로 인해 전체 코드 사이즈가 증가한다.

    3. 심하게 생성되는 로그는 혼란을 일으킬 수 있다

    4. 심하게 생성되는 로그는 어플리케이션 성능에 영향을 미칠 수 있다

    5. 개발 중간에 로깅 코드를 추가하기 어렵다.


# Logback

- log4j를 토대로 새롭게 만든 Loggin 라이브러리이다.

    logback을 이용하여 로깅을 수행하기 위해서 필요한 주요 설정 요소로는 Logger,Appender,Encoder 3가지가 있다.

    logback은 구성요소 측면에서 log4j와 유사한 구조를 가지오 있음.
    logback을 이용하여 로깅을 수행하기 위해서 필요한 주요 설정요소는 Logger,Appender,Encoder의 3가지가 있음
    •Logger : 실제 로깅을 수행하는 구성요소로 Level 속성을 통해서 출력할 로그의 레벨을 조절할 수 있다.
    •Appender : 로그 메시지가 출력될 대상을 결정하는 요소
    •Encoder : Appender에 포함되어 사용자가 지정한 형식으로 표현 될 로그메시지를 변환하는 역할을    담당하는 요소
    Logback에서 설정파일을 작성하는 방법은 크게 두 가지가 있다.
    •XML을 이용한 설정방법 : logback.xml로 설정 파일 작성 후 해당 파일을 클래스패스에 위치시킨다.
    •Groovy 언어를 이용한 설정방법 : logback.groovy로 설정 파일 작성 후 해당 파일을 클래스패스에    위치시킨다.

- 장점

    1. log4j보다 10배 정도 빠르게 수행되도록 내부가 변경되었으며, 메모리 효율성도 좋아졌다.

    2. log4j때부터 광범위한 테스트를 진행한 경험을 가지고 있으며 logback은 더욱 높은 레벨의 테스트를 통해 검증되었다.

    3. 문서화가 잘 되어 있다,.
    
    4. 설정 파일을 변경하였을 경우, 서버 재기동 없이 변경 내용이 자동으로 갱신된다. 

    5. 서버 중지 없이 I/O Faliure에 대한 복구를 지원한다.

    6. RollingFileAppender를 사용할 경우 자동적으로 오래된 로그를 지워주며 Rolling 백업을 처리한다.

- logback 우선 순위

    1. logback.groovy
    
    2. logback-test.xml

    3. logback.xml

    4. 모두 없다면 기본 설정 전역을 따른다.


3.5 logback 로깅 레벨

log4j와 다르게 5단계라고 하는데 확인해보자...

TRACE라고 선언하면 모든 LEVEL 포함. INFO로 설정하면 info, warn, error 포함
    ① ERROR : 일반 에러가 일어 났을 때 사용한다.

    ② WARN : 에러는 아니지만 주의할 필요가 있을 때 사용한다.

    ③ INFO : 일반 정보를 나타낼 때 사용한다.**************

    ④ DEBUG : 일반 정보를 상세히 나타낼 때 사용한다.

    ⑤ TRACE : 경로추적을 위해 사용한다.

3.7 logback 정렬

3.7.1나와 같은경우 log4jdbc-log4j2를 이용하였다.

Dependency 추가

<dependency>
     <groupId>org.bgee.log4jdbc-log4j2</groupId>
     <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
     <version>1.16</version>
</dependency>












출처: https://goddaehee.tistory.com/45 [갓대희의 작은공간]