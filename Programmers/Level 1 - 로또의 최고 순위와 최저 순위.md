## 🚩 문제
><h4>문제설명</h4>
>
`로또 6/45`(이하 '로또'로 표기)는 1부터 45까지의 숫자 중 6개를 찍어서 맞히는 대표적인 복권입니다. 아래는 로또의 순위를 정하는 방식입니다.
>
|순위|당첨내용|
|---|---|
|1|6개 번호가 모두 일치|
|2|5개 번호가 일치|
|3|4개 번호가 일치|
|4|3개 번호가 일치|
|5|2개 번호가 일치|
|6(낙첨)|그 외|
로또를 구매한 민우는 당첨 번호 발표일을 학수고대하고 있었습니다. 하지만, 민우의 동생이 로또에 낙서를 하여, 일부 번호를 알아볼 수 없게 되었습니다. 당첨 번호 발표 후, 민우는 자신이 구매했던 로또로 당첨이 가능했던 최고 순위와 최저 순위를 알아보고 싶어 졌습니다.
알아볼 수 없는 번호를 `0`으로 표기하기로 하고, 민우가 구매한 로또 번호 6개가 `44, 1, 0, 0, 31 25`라고 가정해보겠습니다. 당첨 번호 6개가 `31, 10, 45, 1, 6, 19`라면, 당첨 가능한 최고 순위와 최저 순위의 한 예는 아래와 같습니다.
>
|당첨 번호|31|10|45|1|6|19|결과|
|---|---|---|---|---|---|---|---|
|최고 순위 번호|**<u>31</u>**|0->**<u>10</u>**|44|**<u>1</u>**|0->**<u>6</u>**|25|4개 번호 일치, 3등|
|최저 순위 번호|**<u>31</u>**|0->11|44|**<u>1</u>**|0->7|25|2개 번호 일치, 5등|
> * 순서와 상관없이, 구매한 로또에 당첨 번호와 일치하는 번호가 있으면 맞힌 걸로 인정됩니다.
 * 알아볼 수 없는 두 개의 번호를 각각 10, 6이라고 가정하면 3등에 당첨될 수 있습니다.
   - 3등을 만드는 다른 방법들도 존재합니다. 하지만, 2등 이상으로 만드는 것은 불가능합니다.
* 알아볼 수 없는 두 개의 번호를 각각 11, 7이라고 가정하면 5등에 당첨될 수 있습니다.
   - 5등을 만드는 다른 방법들도 존재합니다. 하지만, 6등(낙첨)으로 만드는 것은 불가능합니다.
>
>
>
민우가 구매한 로또 번호를 담은 배열 lottos, 당첨 번호를 담은 배열 win_nums가 매개변수로 주어집니다. 이때, 당첨 가능한 최고 순위와 최저 순위를 차례대로 배열에 담아서 return 하도록 solution 함수를 완성해주세요.
><h4>제한사항</h4>
>입력으로 지도의 한 변 크기 n 과 2개의 정수 배열 arr1, arr2가 들어온다.
>
* lottos는 길이 6인 정수 배열입니다.
* lottos의 모든 원소는 0 이상 45 이하인 정수입니다.
   - 0은 알아볼 수 없는 숫자를 의미합니다.
   - 0을 제외한 다른 숫자들은 lottos에 2개 이상 담겨있지 않습니다.
   - lottos의 원소들은 정렬되어 있지 않을 수도 있습니다.
* win_nums은 길이 6인 정수 배열입니다.
* win_nums의 모든 원소는 1 이상 45 이하인 정수입니다.
   - win_nums에는 같은 숫자가 2개 이상 담겨있지 않습니다.
   - win_nums의 원소들은 정렬되어 있지 않을 수도 있습니다.
----
><h4>입출력 예</h4>
>
|**lottos**|**win_nums**|**result**|
|---|---|---|
|\[44, 1, 0, 0, 31, 25]|\[31, 10, 45, 1, 6, 19]|\[3,5]
|\[0, 0, 0, 0, 0, 0]|\[38, 19, 20, 40, 15, 25]|\[1,6]
|\[45, 4, 35, 20, 3, 9]|\[20, 9, 3, 45, 4, 35]|\[1,1]
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

* 맞은 갯수 `corr`과 알아볼 수 없는 번호 `zero`의 갯수를 센다.
* 2중 for문을 돌릴 때 0이 아닌 것들만 대조하면 시간복잡도 측면에서 이득일까?
큰 차이가 없을까?
* 왠지 `stream`으로 한방에 풀 수 있을 것 같은 느낌이다.
* 알아볼 수 없는 번호는 다 틀릴수도 있고, 다 맞을수도 있는 무적의 번호다.

 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public int[] solution(int[] lottos, int[] win_nums) {
        int zero = 0;
        int corr = 0;
        for (int n1 : lottos) {
            if (n1 != 0) {
                for (int n2 : win_nums) {
                    if (n1 == n2) {
                        corr++;
                    }
                }
            } else {
                zero++;
            }
        }
        int maxRank = 7 - (corr+zero);
        int minRank = 7 - corr;
        if (maxRank > 6) maxRank = 6;
        if (minRank > 6) minRank = 6;
        return new int[]{maxRank, minRank};
    }
}
````
`maxRank`에서는 `zero`는 다 맞았다고 가정하니 `corr+zero`를 7에서 빼준게 등수가 된다.
`minRank`는 `zero`가 다 틀렸다고 가정하니 더할 필요도 뺄 필요도 없다.
만약 맞은게 하나도 없고, `zero`의 갯수도 0인 경우는 7이 나오게 되니
그때만 강제로 6으로 바꿔준다.


<br>
<br>

## ✔️ 다른 사람의 코드
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] solution(int[] lottos, int[] win_nums) {
        Map<Integer, Boolean> map = new HashMap<Integer, Boolean>();
        int zeroCount = 0;

        for(int lotto : lottos) {
            if(lotto == 0) {
                zeroCount++;
                continue;
            }
            map.put(lotto, true);
        }


        int sameCount = 0;
        for(int winNum : win_nums) {
            if(map.containsKey(winNum)) sameCount++;
        }

        int maxRank = 7 - (sameCount + zeroCount);
        int minRank = 7 - sameCount;
        if(maxRank > 6) maxRank = 6;
        if(minRank > 6) minRank = 6;

        return new int[] {maxRank, minRank};
    }
}

```
`Boolean` 타입을 쓰면 속도가 빠르다고 한다.
실제로 돌려보니 내 코드보다 0.01~0.02ms 빠르다.