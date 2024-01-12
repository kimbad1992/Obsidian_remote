## 🚩 문제
><h4>문제설명</h4>
>
숫자나라 기사단의 각 기사에게는 1번부터 `number`까지 번호가 지정되어 있습니다. 기사들은 무기점에서 무기를 구매하려고 합니다.
>
각 기사는 자신의 기사 번호의 약수 개수에 해당하는 공격력을 가진 무기를 구매하려 합니다. 단, 이웃나라와의 협약에 의해 공격력의 제한수치를 정하고, 제한수치보다 큰 공격력을 가진 무기를 구매해야 하는 기사는 협약기관에서 정한 공격력을 가지는 무기를 구매해야 합니다.
>
예를 들어, 15번으로 지정된 기사단원은 15의 약수가 1, 3, 5, 15로 4개 이므로, 공격력이 4인 무기를 구매합니다. 만약, 이웃나라와의 협약으로 정해진 공격력의 제한수치가 3이고 제한수치를 초과한 기사가 사용할 무기의 공격력이 2라면, 15번으로 지정된 기사단원은 무기점에서 공격력이 2인 무기를 구매합니다. 무기를 만들 때, 무기의 공격력 1당 1kg의 철이 필요합니다. 그래서 무기점에서 무기를 모두 만들기 위해 필요한 철의 무게를 미리 계산하려 합니다.
>
기사단원의 수를 나타내는 정수 `number`와 이웃나라와 협약으로 정해진 공격력의 제한수치를 나타내는 정수 `limit`와 제한수치를 초과한 기사가 사용할 무기의 공격력을 나타내는 정수 `power`가 주어졌을 때, 무기점의 주인이 무기를 모두 만들기 위해 필요한 철의 무게를 return 하는 solution 함수를 완성하시오.
>
>
><h4>제한사항</h4>
>
* 1 ≤ `number` ≤ 100,000
* 2 ≤ `limit` ≤ 100
* 1 ≤ `power` ≤ limit
>
----
><h4>입출력 예</h4>
>
|**number**|**limit**|**power**|**result**|
|---|---|---|---|
|5|3|2|10|
|10|3|2|21|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

 * 약수를 구하는 메서드를 만들어 분리시키고, `number` 만큼 for문을 돌려 약수의 갯수를 합산하자.
 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public int solution(int number, int limit, int power) {
        int answer = 0;
        
        for (int i=1; i<=number; i++) {
            int n = getCommonFactor(i);
            int iron = 0;
            iron = n > limit ? power : n;
            answer += iron;
        }
        
        return answer;
    }
    
        public int getCommonFactor(int n) {
        int ret = 0;
        for (int i = 1; i <= n; i++) {
            if (n % i == 0) {
                ret++;
            }
        }
        return ret;
    }
}
```
레벨 1짜리라 그런가 어 좀 쉬운데? 그냥 생각나는대로 쭉 짜면 되는거 아냐?
라고 생각하지만 언제나 당하는건 나였다

2중 for문이 돌아가는 형태인건데, `number`가 높은 값이 올수록 for문을 진행시간이 길어져
시간초과로 통과하지 못했다.

인터넷에서 약수 구하기 알고리즘에서 ( `n % i == 0`은 거의 동일하다.)
제곱근과 거듭제곱을 이용해 for문의 길이를 줄이는 방법을 찾았다.
<br>

```java
import java.util.*;

class Solution {
    public int solution(int number, int limit, int power) {
        int answer = 0;
        
        for (int i=1; i<=number; i++) {
            int n = getCommonFactor(i);
            int iron = 0;
            iron = n > limit ? power : n;
            answer += iron;
        }
        
        return answer;
    }
    
        public int getCommonFactor(int n) {
        int ret = 0;
        for (int i = 1; i <= Math.sqrt(n); i++) {
            if (n % i == 0) {
                ret = Math.pow(i, 2) == n ? ret+1 : ret+2;
            }
        }
        return ret;
    }
}
```


<br>
<br>

## ✔️ 다른 사람의 코드
```java
class Solution {

    public int solution(int number, int limit, int power) {
        int[] count = new int[number + 1];    
        for (int i = 1; i <= number; i++) {
            for (int j = 1; j <= number / i; j++) {
                count[i * j]++;
            }
        }
        int answer = 0;
        for (int i = 1; i <= number; i++) {
            if (count[i] > limit) {
                answer += power;
            } else {
                answer += count[i];
            }
        }
        return answer;
    }
}
```

