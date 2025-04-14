# 자바스크립트 변수 선언 방법

 - 자바스크립트에는 변수를 지정하는 선언 방법이 세 가지 존재한다
  1. var 현재는 많이 사용하지 않는다고 들었다. (하지만,, 현재 나는 si프로젝트로 해당 선언 방법을 많이 사용한다) 
  2. const   ECMAScript 6 에 나온 선언 방법이다.
  3. let ECMAScript  6 에 나온 선언 방법이다.

  - var는 오래된 스크립트의 변수 선언 방법이다 .
  - var는 블록 스코프가 존재하지 않는다. var는 함수 스코프이거나 전역 스코프이다. 그래서 블록 스코프 범위 밖에서 접근이 가능하다.  
            if(true){
                var test = true;
            }
            alert(test); // if문의 블록스코프가 종료되어도 test라는 블록스코프 범위 안에있는 변수에 접근을 할 수 있다.  

  - 코드 블록이 함수 안에 있으면 var는 함수 레벨 변수가 된다.  
          function varTest(){
              if(true){
                  var hello = 'hello';
              }
            alert(hello) //-> hello로 출력
          }
    varTest();//varTest라는 함수를 실행
    alert(hello); //varTest라는 함수 안에 있는 var 변수에 접근을 하면 에러가 발생한다.
