## 피보나치 수열
- 첫번째 항과 두번째 항이 1이며 그 뒤의 모든 항은 바로 앞 두 항의 합인 수열
- 식으로는 f(n) = f(n-1) + f(n-2);

## 프로그래머스 피보나치 수열테스트 연습
- 해당 식으로 기반으로 코드를 작성했는데 문제가 발생
| 재귀함수를 생각해서 구성한 코드 
```java
    class Solution {
    public int solution(int n) {   
        return fibonacci(n % 1234567);
    }
    public int fibonacci(int n){
        if(n < 2){
            return n;
        }else {
            return fibonacci(n - 2) + fibonacci(n - 1);
        }
    }
}
```

- 일반코드 실행은 테스트를 통과한다 하지만 제출 하고 여러개의 테스트 코드를 통과할때 테스트7 부터는 실패 메세지와 시간초과를 발생시킨다.

- https://school.programmers.co.kr/learn/courses/14743/lessons/116433
- 해당 이슈와 동일한 관계로 재귀함수를 동적 DP로 풀어야한다는 해결방안


## DP란
- 잡한 문제를 간단한 여러 개의 문제로 나누어 푸는 방법을 말한다. 이것은 부분 문제 반복과 최적 부분 구조를 가지고 있는 알고리즘을 일반적인 방법에 비해 더욱 적은 시간 내에 풀 때 사용한다.

|DP를 통한 코드
```java
class Solution {
    public int solution(int n) {   
        return fibonacci(n) % 1234567;
    }
    public static int fibonacci(int n) {
        int[] dp = new int[n + 1];
        int m = 1234567;
        dp[0] = 0;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i]  = dp[i - 1]  + dp[i - 2] ;
        }
        return dp[n] ;
    }
}

```
- 해당 코드로 구현했을 경우 문제가 다시 발생
- % 1234567를 딱 한 번만 하셨나요? -> 네 한번만 수행했습니다.
- 한번만 수행하면 오버플로우가 발생한다고 한다.
- 파라미터가 매우 큰 경우 자료형의 범위를 넘어가, 오버플로우가 발생한다고 한다.
- 해결방안 모든 계산식에 나머지 연산을 수행해준다.

```java
F(n) % m 
= (F(n-1) + F(n-2)) % m
= (F(n-1) % m + F(n-2) % m) % m
```


```java
//최종 코드
class Solution {
    public int solution(int n) {   
        return fibonacci(n);
    }
    public static int fibonacci(int n) {
        int[] dp = new int[n + 1];
        int m = 1234567;
        dp[0] = 0; 
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i]  = (dp[i - 1] % m ) + (dp[i - 2] % m );
        }
        return dp[n] % m ;
    }   
}
```
| 해당 코드는 하위에서부터 문제를 해결해나가면서 먼저 계산했던 문제들의 값을 활용해서 상위 문제를 해결해나가는 방식으로 DP의 전형적인 형태는 Bottom-up이다. 여기서 사용되는 문제 결과 값 저장용 리스트는 DP 테이블이라고 부른다.


