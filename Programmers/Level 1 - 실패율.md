## 🚩 문제
><h4>문제설명</h4>
>
![](https://velog.velcdn.com/images/kimbad1992/post/c1b6a475-173a-4528-98d5-b305de5f381f/image.png)
>
슈퍼 게임 개발자 오렐리는 큰 고민에 빠졌다. 그녀가 만든 프랜즈 오천성이 대성공을 거뒀지만, 요즘 신규 사용자의 수가 급감한 것이다. 원인은 신규 사용자와 기존 사용자 사이에 스테이지 차이가 너무 큰 것이 문제였다.
>
이 문제를 어떻게 할까 고민 한 그녀는 동적으로 게임 시간을 늘려서 난이도를 조절하기로 했다. 역시 슈퍼 개발자라 대부분의 로직은 쉽게 구현했지만, 실패율을 구하는 부분에서 위기에 빠지고 말았다. 오렐리를 위해 실패율을 구하는 코드를 완성하라.
>
* 실패율은 다음과 같이 정의한다.
   - 스테이지에 도달했으나 아직 클리어하지 못한 플레이어의 수 / 스테이지에 도달한 플레이어 수
>
전체 스테이지의 개수 N, 게임을 이용하는 사용자가 현재 멈춰있는 스테이지의 번호가 담긴 배열 stages가 매개변수로 주어질 때, 실패율이 높은 스테이지부터 내림차순으로 스테이지의 번호가 담겨있는 배열을 return 하도록 solution 함수를 완성하라.
>
><h4>제한사항</h4>
>
* 스테이지의 개수 N은 `1` 이상 `500` 이하의 자연수이다.
* stages의 길이는 `1` 이상 `200,000` 이하이다.
* stages에는 `1` 이상 `N + 1` 이하의 자연수가 담겨있다.
   - 각 자연수는 사용자가 현재 도전 중인 스테이지의 번호를 나타낸다.
   - 단, `N + 1` 은 마지막 스테이지(N 번째 스테이지) 까지 클리어 한 사용자를 나타낸다.
* 만약 실패율이 같은 스테이지가 있다면 작은 번호의 스테이지가 먼저 오도록 하면 된다.
* 스테이지에 도달한 유저가 없는 경우 해당 스테이지의 실패율은 `0` 으로 정의한다.
>
----
><h4>입출력 예</h4>
>
|**N**|**stages**|**result**|
|---|---|---|
|5|\[2, 1, 2, 6, 2, 4, 3, 3]|\[3,4,2,1,5]|
|4|\[4,4,4,4,4]|\[4,1,2,3]|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

* 실패율을 구하기 위해 `해당 스테이지의 총 도전자 수`와 `현재 도전중인 스테이지`가 필요하다.
* 최종 스테이지까지 클리어한 경우 유저는 마지막 스테이지에서 빼준다.
* 실패율 순으로 정렬하고 실패율이 동일하면 낮은 스테이지가 앞으로 오도록 정렬.
   - 해당 스테이지 번호와 실패율을 동시에 가지고 있는 객체가 필요함.

 <br>
 <br>


## 📝 코드 작성
```java
import java.util.*;

class Solution {
    public int[] solution(int N, int[] stages) {
        int[] answer = new int[N];
        double[] challenge = new double[N+1]; // 해당 스테이지의 총 도전자 수
        double[] currentStage = new double[N+1]; // 도전중인 스테이지
        List<double[]> fail = new ArrayList<double[]>(); // 실패율
        
        // stages에서 총 유저수와 클리어하지 못한 유저 수 추출
        for (int i=0; i<=stages.length-1; i++) {
            // 스테이지 총 도전자 수
            for (int j=0; j<=stages[i]-1; j++) {
                challenge[j]++;
            }
            // 클리어하지 못한 도전자 수
            currentStage[stages[i]-1]++;
            if (stages[i] == N+1) { // 최종 스테이지까지 클리어한 사용자
                challenge[N-1]--; 
                // 최종 스테이지 클리어한 사용자는 빼준다
            }
        }
        
        // 스테이지별 실패율 추출
        for (int i=0; i<N; i++) {
            if(challenge[i] == 0) {
                fail.add(new double[]{i,0});
            } else {
                fail.add(new double[]{i,currentStage[i] / challenge[i]});
            }
        }
        
        fail.sort((a,b)->Double.compare(b[1],a[1]));
        
        for (int i=0; i<fail.size(); i++) {
            answer[i] = (int)fail.get(i)[0]+1;
        }
        
        return answer;
    }
}
````
처음에 한참동안 씨름한 코드
다 만들고 나서 케이스 3개만 통과를 못하길래 도대체 왜 이러나 싶어서
이틀을 고민했다.
아예 엎고 처음부터 다시 짜려고 슬슬 짜던 도중에 문득 떠오른 생각이 있어서 수정하니 문제가 해결됐는데, 깨닫고 나니 문제는 별거 아니었다.
처음 짤 때 내 생각은 최종 스테이지까지 클리어한 사용자는,
예를 들면 **N=4, stages[n]이 = 5인 경우**는 최종 스테이지까지 클리어한 유저니까
`challenge[N-1]--;`를 해줘야 한다고 생각했다.
실패율을 구할 때 divided by zero 에러가 나오지 않을까 생각해서 넣었던건데
최종 스테이지를 통과했어도 **스테이지에 도달한 플레이어 수**가 필요한 거라 빼줄 필요는 없었다.
저 부분만 약간 수정하고 나니 정상적으로 돌아갔다.

```java
import java.util.*;

class Solution {
    public int[] solution(int N, int[] stages) {
        int[] answer = new int[N];
        double[] currentStage = new double[N+1];
        double[] totalUser = new double[N+1];
        List<double[]> fail = new ArrayList<double[]>();
        
        for (int i=0; i<stages.length; i++) {
            currentStage[stages[i]-1]++; // 스테이지에 도달했으나 아직 클리어하지 못한 플레이어의 수
            for (int j=0; j<stages[i]; j++) {
                totalUser[j]++; // 스테이지에 도달한 플레이어 수
            }
        }
        
        // 스테이지별 실패율 추출
        for (int i=0; i<N; i++) {
            if(totalUser[i] == 0) {
                fail.add(new double[]{i,0});
            } else {
                fail.add(new double[]{i,currentStage[i] / totalUser[i]});
            }
        }
        
        fail.sort((a,b)->Double.compare(b[1],a[1]));
        
        for (int i=0; i<fail.size(); i++) {
            answer[i] = (int)fail.get(i)[0]+1;
        }
        
        return answer;
    }
}
```

<br>
<br>

## ✔️ 다른 사람의 코드
```java
class Solution {
    public int[] solution(int N, int[] stages) {
        int[] answer = new int[N];
        double[] tempArr = new double[N];
        int arrLength = stages.length;
        int idx = arrLength;
        double tempD = 0;
        int tempI = 0;
        for (int i = 0; i < arrLength; i++) {
            int stage = stages[i];
            if (stage != N + 1)
                answer[stage - 1] += 1;
        }
        for (int i = 0; i < N; i++) {
            int personNum = answer[i];
            tempArr[i] = (double) personNum / idx;
            idx -= personNum;
            answer[i] = i + 1;
        }

        for (int i = 0; i < N; i++) {
            for (int j = 1; j < N - i; j++) {
                if (tempArr[j - 1] < tempArr[j]) {
                    tempD = tempArr[j - 1];
                    tempArr[j - 1] = tempArr[j];
                    tempArr[j] = tempD;

                    tempI = answer[j - 1];
                    answer[j - 1] = answer[j];
                    answer[j] = tempI;
                }
            }
        }
        return answer;
    }
}
```
수많은 정답 중 import 없이, `sort()` method 없이 푼 문제가 있어서 인상 깊었다.
실패율이 같을 경우의 정렬이 문제인건데, 마지막의 2중 for문으로 정렬을 시켜준다.
나는 정렬 방법이 생각나지 않아서 인터넷의 도움을 받았지만
이 경우는 정렬이 크게 어려운 문제는 아니었던 것 같아서 나도 구현을 시도해봤으면 어땠을까?
하는 생각이 들었다. 정렬 알고리즘은 수십가지가 있으니, 그 중에 간단한 건 만들 수 있게 하는게 좋겠다.
간단한 공부용 알고리즘 몇 가지를 나무위키에서 찾아 등록해 놓았다.

정렬 알고리즘 - [[Sort]]