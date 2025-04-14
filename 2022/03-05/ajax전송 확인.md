# ajax 전송 확인

- 서버단에서 ajax통신을 할때 request통신인지 ajax로 통신하는지 확인해야함 
why? 오류나 에러 처리를 다르게 구성하기 위해서 
ajax로 전송했는지 확인하는 방법은 request header에 x-requestd-with값이 XMLHttpRequest인지 확인 
맞으면 ajax 아니면 그냥 request통신방법