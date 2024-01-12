## 🚩 문제
><h4>문제설명</h4>
>
카카오톡 게임별의 하반기 신규 서비스로 다트 게임을 출시하기로 했다. 다트 게임은 다트판에 다트를 세 차례 던져 그 점수의 합계로 실력을 겨루는 게임으로, 모두가 간단히 즐길 수 있다.
>
갓 입사한 무지는 코딩 실력을 인정받아 게임의 핵심 부분인 점수 계산 로직을 맡게 되었다. 다트 게임의 점수 계산 로직은 아래와 같다.
>
1. 다트 게임은 총 3번의 기회로 구성된다.
2.  각 기회마다 얻을 수 있는 점수는 0점에서 10점까지이다.
3.  점수와 함께 Single(`S`), Double(`D`), Triple(`T`) 영역이 존재하고 각 영역 당첨 시 점수에서 1제곱, 2제곱, 3제곱 (점수$¹$ , 점수$²$ , 점수$³$ )으로 계산된다.
4. 옵션으로 스타상(`*`) , 아차상(`#`)이 존재하며 스타상(`*`) 당첨 시 해당 점수와 바로 전에 얻은 점수를 각 2배로 만든다. 아차상(`#`) 당첨 시 해당 점수는 마이너스된다.
5. 스타상(`*`)은 첫 번째 기회에서도 나올 수 있다. 이 경우 첫 번째 스타상(`*`)의 점수만 2배가 된다. (예제 4번 참고)
6. 스타상(`*`)의 효과는 다른 스타상(`*`)의 효과와 중첩될 수 있다. 이 경우 중첩된 스타상(`*`) 점수는 4배가 된다. (예제 4번 참고)
7. 스타상(`*`)의 효과는 아차상(`#`)의 효과와 중첩될 수 있다. 이 경우 중첩된 아차상(`#`)의 점수는 -2배가 된다. (예제 5번 참고)
8. Single(`S`), Double(`D`), Triple(`T`)은 점수마다 하나씩 존재한다.
9. 스타상(`*`), 아차상(`#`)은 점수마다 둘 중 하나만 존재할 수 있으며, 존재하지 않을 수도 있다.
>
0~10의 정수와 문자 S, D, T, *, #로 구성된 문자열이 입력될 시 총점수를 반환하는 함수를 작성하라.
>
><h4>입력 형식</h4>
>"점수|보너스|[옵션]"으로 이루어진 문자열 3세트.
예) 1S2D*3T
>
* 점수는 0에서 10 사이의 정수이다.
* 보너스는 S, D, T 중 하나이다.
* 옵선은 *이나 # 중 하나이며, 없을 수도 있다.
>
><h4>출력 형식</h4>
>3번의 기회에서 얻은 점수 합계에 해당하는 정수값을 출력한다.
>
예) 37
>
----
![](https://velog.velcdn.com/images/kimbad1992/post/7116269e-fec7-4ca2-9bf3-c35ab7d788cb/image.png)
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

* 문제가 어렵다.
* 한 게임마다 점수/싱글,더블,트리플/(스타상,아차상)이니 한 게임당 점수를 구해야겠다.
* 계산 로직의 6번, 효과 중첩은 어렵게 생각하지 말고 누적된 점수에 적용시켜주면 될 것 같다.
* 보너스 구분을 어떻게 해야할까?

 <br>
 <br>


## 📝 코드 작성
```java
class Solution {
    public int solution(String dartResult) {
        int answer = 0;
        
        String str = "";
        for (int i=0; i<dartResult.length(); i++) {
            if (Character.isDigit(dartResult.charAt(i))) { // 점수인 부분 추출
                str += dartResult.charAt(i);
            } else if (dartResult.charAt(i) == '*') {
                answer = answer * 2; // 누적 점수 2배
            } else if (dartResult.charAt(i) == '#') {
                answer = answer * (-1); // 누적 점수 마이너스
            } else {
                int score = Integer.parseInt(str);
                switch (dartResult.charAt(i)) {
                    case 'S' : score = (int)Math.pow(score, 1); break;
                    case 'D' : score = (int)Math.pow(score, 2); break;
                    case 'T' : score = (int)Math.pow(score, 3); break;
                }
                System.out.println("점수 : "+str+""+dartResult.charAt(i));
                answer += score;
                str = "";
            }
        }
        return answer;
    }
}
````
어? 안되네?
왜 안될까 다시 좀 훑어보고 문제를 보니
보너스가 문제였다.
`1S2D*`이면 내가 짠 로직에서는 ($1¹$+$2²$) \* $2$가 되는 형태인데 이게 아니고
$1¹$ \* $2$ + $2²$ \* $2$가 되어야 한다.
각 회차때의 점수를 별도로 만들어야 할 것 같다.
for문을 돌 때 지금 구하는 숫자가 몇회차인지 구분해야 한다.


```java
import java.util.*;

class Solution {
    public int solution(String dartResult) {
        int answer = 0;
        int idx = 0; // 현재 세트
        int[] setScore = new int[3]; // 각 세트별 스코어
        
        String str = "";
        for (int i=0; i<dartResult.length(); i++) {
            if (Character.isDigit(dartResult.charAt(i))) { // 점수인 부분 추출
                str += dartResult.charAt(i);
            } else if (dartResult.charAt(i) == '*') {
                for (int j=0; j<idx; j++) {
                    setScore[j] *= 2;
                }
            } else if (dartResult.charAt(i) == '#') {
                for (int j=0; j<idx; j++) {
                    setScore[j] *= -1;
                }
            } else {
                int score = Integer.parseInt(str);
                switch (dartResult.charAt(i)) {
                    case 'S' : setScore[idx] = (int)Math.pow(score, 1); break;
                    case 'D' : setScore[idx] = (int)Math.pow(score, 2); break;
                    case 'T' : setScore[idx] = (int)Math.pow(score, 3); break;
                }
                idx++;
                str = "";
            }
        }
        
        for (int score : setScore) {
            answer += score;
        }
        
        return answer;
    }
    
}
```
idx로 현재 회차를 구분하고, `*` 혹은 `#`이 나올 시 현재 회차까지의 스코어에
보너스 점수를 가산한다.
어? 안되네?

문제를 다시보니 `*`은 이번 회차, 전 회차의 점수만 2배로 만들고,
`#`은 이번 회차의 점수만 마이너스로 만든다고 한다.
다시 수정....

```java
class Solution {
    public int solution(String dartResult) {
        int answer = 0;
        int idx = 0; // 현재 회차
        int[] setScore = new int[3]; // 각 회차별 스코어
        
        String str = "";
        for (int i=0; i<dartResult.length(); i++) {
            if (Character.isDigit(dartResult.charAt(i))) { // 점수인 부분 추출
                str += dartResult.charAt(i);
            } else if (dartResult.charAt(i) == '*') {
                // 현재 회차, 이전회차 점수 2배
                if (idx-2 >= 0) {
                    setScore[idx-1] *= 2;
                    setScore[idx-2] *= 2;
                } else {
                    setScore[idx-1] *= 2;
                }
            } else if (dartResult.charAt(i) == '#') {
                // 현재 회차 점수 마이너스
                setScore[idx-1] *= -1;
            } else {
                // 싱글, 더블, 트리플 합산
                int score = Integer.parseInt(str);
                switch (dartResult.charAt(i)) {
                    case 'S' : setScore[idx] = (int)Math.pow(score, 1); idx++; break;
                    case 'D' : setScore[idx] = (int)Math.pow(score, 2); idx++; break;
                    case 'T' : setScore[idx] = (int)Math.pow(score, 3); idx++; break;
                }
                // 점수 합산 후 초기화
                str = "";
            }
        }
        
        for (int score : setScore) {
            answer += score;
        }
        
        return answer;
    }
    
}
```
중복 if문 남발에 스위치까지 써서 가독성도 속도도 최악이다.




<br>
<br>

## ✔️ 다른 사람의 코드
```java
import java.util.*;

class Solution {
    public int solution(String dartResult) {
        Stack<Integer> stack = new Stack<>();
        int sum = 0;
        for (int i = 0; i < dartResult.length(); ++i) {
            char c = dartResult.charAt(i);
            if (Character.isDigit(c)) {
                sum = (c - '0');
                if (sum == 1 && i < dartResult.length() - 1 && dartResult.charAt(i + 1) == '0') {
                    sum = 10;
                    i++;
                }
                stack.push(sum);
            } else {
                int prev = stack.pop();
                if (c == 'D') {
                    prev *= prev;
                } else if (c == 'T') {
                    prev = prev * prev * prev;
                } else if (c == '*') {
                    if (!stack.isEmpty()) {
                        int val = stack.pop() * 2;
                        stack.push(val);
                    }
                    prev *= 2;
                } else if (c == '#') {
                    prev *= (-1);
                }
                // System.out.println(prev);
                stack.push(prev);
            }
        }
        int totalScore = 0;
        while (!stack.isEmpty()) {
            totalScore += stack.pop();
        }
        return totalScore;
    }
}
```
`Stack`을 사용한 코드
돌려보니 평균 0.1~0.2ms 나오는데
내 코드는 평균 10ms~13ms 나온다. 내 코드보다 뭐 평균 100배 이상 빠르단 소린데,
저번에 `Stack` 자료구조를 보니 속도면에서 이점이 있다더니만 진짜 장난 아닌가보다.

점수/보너스 구분에 정규식을 사용할까 생각했지만 크게 차이가 없을 것 같아 사용하지 않았는데,
정규식을 사용한 사람도 일부 있었다.

여기서 Stack을 사용할 수 있다는 발상조차 하지 못했는데,
역시 배우고 몇번 써봐야 감이 잡히나보다.