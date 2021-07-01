# 오류사항
## 로그인 후 세션 만료후 로그아웃 상황에서

-  로그인을 한 세션이 세션타임으 종료되고 만료후에 새로고침시에
 톰켓에서 브라우저랑 세션을 다시 연결하기 위해서 sessinoCreated메소드를 실행함에 있어서 Created 메서드 안에 선언한 request를 가져오는 소스에서 null을 반환해 오류가 발생함 

 - 문제점은  ServletRequestAttributes sra=(ServletRequestAttributes) RequestContextHolder.getRequestAttributes(); 해당 메서드에서 
    - getRequestAttributes()는 현제 스레드에 바인딩된 RequestAttributes를 가져온다 가져옴에 있어서 반환값이 없으면 null을 반환함 그리고
    request를 사용하기 위해서 getRequest()를 호출함 이부분에서 null일 경우에 getRequest에서 null예외가 발생하는거였음 

    - 해결방안
    - 기존 소스 : HttpServletRequest request=(ServletRequestAttributes) RequestContextHolder.getRequestAttributes().getRequest();
    - 해결 소스 : ServletRequestAttributes sra=(ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
                : if(sra!=null) {
                    HttpServletRequest request = sra.getRequest();
                }
        - getRequestAttributes를 먼저 가져온후에 가져온 값에 조건문으로 null을 체크 하고 null이 아닐경우에 다음 로직을 수행하도록