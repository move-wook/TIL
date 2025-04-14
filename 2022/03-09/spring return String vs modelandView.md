# Spring 리턴

- 스프링을 사용하다 보면 리턴 타입이 String인 경우와 ModelandView경우가 있다

- 둘다 기능은 동일하지만 String이 더 간결하게 사용 할 수 있다. 어차피 String으로 사용해도 mavContainer에 ViewName을 세팅한다.


## Spring ModelMap과 Model 

- 둘다 똑같은 기능을한다. Model은 인터페이스이고 ModelMap은 인터페이스를 구현한 클래스이다.

- 둘 다 addAttribute를 사용함

- Model or ModelMap에 데이터만 저장 후 View에서 사용목적

