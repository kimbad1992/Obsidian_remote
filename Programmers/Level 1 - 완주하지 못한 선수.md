## 🚩 문제
><h4>문제설명</h4>
>
수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.
>
마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.
>
>
><h4>제한사항</h4>
>
* 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
* completion의 길이는 participant의 길이보다 1 작습니다.
* 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
* 참가자 중에는 동명이인이 있을 수 있습니다.
>
----
><h4>입출력 예</h4>
>
|**participant**|**completion**|**return**|
|---|---|---|
|["leo", "kiki", "eden"]|["eden", "kiki"]|"leo"|
|["marina", "josipa", "nikola", "vinko", "filipa"]|["josipa", "filipa", "marina", "nikola"]|"vinko"|
|["mislav", "stanko", "mislav", "ana"]|["stanko", "ana", "mislav"]|"mislav"|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

 * 2중 for문으로 `completion` 원소랑 `participant` 원소랑 동일한 경우만 제거해주면 되는 거 아냐?  
 라고 생각했는데 돌려보니 동명이인까지 제거를 해버려서 안된다.
 * `new int[participant.length]`를 만들어 참여자는 +1, 완주자는 -1을 해서 구분 하려 했으나, 인덱스만으로는 `completion`과 `participant`의 같은 원소를 구분할 수가 없었다.
 * 한참 고민했는데 방법이 떠오르질 않아서 찾아보기로 했다.
 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Map<String, Integer> result = new HashMap<String, Integer>();
        String answer = "";
        
        for (String player : participant) {
            result.put(player, result.getOrDefault(player, 0) + 1);
        }
        
        for (String comp : completion) {
            result.put(comp, result.get(comp) - 1);
        }
        
        for (String key : result.keySet()) {
            if (result.get(key) == 1) {
                answer = key;
            }
        }
        
        return "";
    }
}
````
`Map`의 `getOrDefault()` method를 활용하는 방법이 있었다.
동명이인이 있고, 그 중 한명만 완주 했을 경우 2번 카운팅이 되고 -> 한명 완주로 -1이 되니
1이 남아서 완주하지 못한 사람의 이름을 return 할 수 있게 된다.
나도 저 부분이 고민이었는데, `Map`의 `get()`을 호출했을 때 값이 null이면
`NullPointerException`이 발생하는 문제 때문에 아예 시도할 생각을 안했다.
하지만 `getOrDefault()` method를 활용하면 값이 존재하면 get()으로 가져오고,
값이 존재하지 않으면 default 값을 가져오게 되니 에러 없이 카운팅을 할 수 있었다.

<br>
<br>

## ✔️ 다른 사람의 코드
```java
import java.util.Arrays;
import java.util.Iterator;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

class Solution {
    public String solution(String[] participant, String[] completion) {

        Map<String, Long> participantMap = Arrays.asList(participant).stream()
                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));

        for(String name : completion) {

            Long value = participantMap.get(name) - 1L;

            if(value == 0L) {
                participantMap.remove(name);
            } else {
                participantMap.put(name, value);
            }
        }

        return participantMap.keySet().iterator().next();
    }
}
```
댓글에 가장 현대적인 문법이라고 칭찬하는 사람이 있어 가져왔다.
