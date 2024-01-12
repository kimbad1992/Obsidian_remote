## 🚩 문제

><h4>문제 설명</h4>
얀에서는 매년 달리기 경주가 열립니다. 해설진들은 선수들이 자기 바로 앞의 선수를 추월할 때 추월한 선수의 이름을 부릅니다. 예를 들어 1등부터 3등까지 "mumu", "soe", "poe" 선수들이 순서대로 달리고 있을 때, 해설진이 "soe"선수를 불렀다면 2등인 "soe" 선수가 1등인 "mumu" 선수를 추월했다는 것입니다. 즉 "soe" 선수가 1등, "mumu" 선수가 2등으로 바뀝니다.
>
선수들의 이름이 1등부터 현재 등수 순서대로 담긴 문자열 배열 players와 해설진이 부른 이름을 담은 문자열 배열 callings가 매개변수로 주어질 때, 경주가 끝났을 때 선수들의 이름을 1등부터 등수 순서대로 배열에 담아 return 하는 solution 함수를 완성해주세요.
>
---
> <h4>제한사항</h4>
5 ≤ players의 길이 ≤ 50,000
players[i]는 i번째 선수의 이름을 의미합니다.
players의 원소들은 알파벳 소문자로만 이루어져 있습니다.
players에는 중복된 값이 들어가 있지 않습니다.
3 ≤ players[i]의 길이 ≤ 10
2 ≤ callings의 길이 ≤ 1,000,000
callings는 players의 원소들로만 이루어져 있습니다.
경주 진행중 1등인 선수의 이름은 불리지 않습니다.
>
----
<h4>입출력 예</h4>
>
|players|callings|result|
|---|---|---|
|["mumu", "soe", "poe", "kai", "mine"]|["kai", "kai", "mine", "mine"|["mumu", "kai", "mine", "soe", "poe"]|
>
<h4>입출력 예 설명</h4>
입출력 예 #1
>
4등인 "kai" 선수가 2번 추월하여 2등이 되고 앞서 3등, 2등인 "poe", "soe" 선수는 4등, 3등이 됩니다. 5등인 "mine" 선수가 2번 추월하여 4등, 3등인 "poe", "soe" 선수가 5등, 4등이 되고 경주가 끝납니다. 1등부터 배열에 담으면 ["mumu", "kai", "mine", "soe", "poe"]이 됩니다.
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_


## 📝 코드 작성

```java
class Solution {
    
    String[] rankedList;
    
    public String[] solution(String[] players, String[] callings) {
        rankedList = players;
        
        for (String call : callings) {
            calledName(players, call);
        }
        
        return rankedList;
    }
    
    public void calledName(String[] players, String name) {
        // 현재 등수
        int rank = findIndex(players, name);
        // 임시
        String tmp = players[rank-1];
        // 등수 교체
        players[rank-1] = name;
        players[rank] = tmp;
        rankedList = players;
    }
    
    public int findIndex(String[] players, String name) {
        int idx = 0;
        for (String player : players) {
            if (player.equals(name)) {
                return idx;
            }
            idx++;
        }
        return idx;
    }
    
}
```

각 로직별 메서드 분리를 하고, 생각나는대로 쭉 짜고 돌리니
처음 테스트 케이스에서는 통과하나 채점 시 속도 문제로 통과되지 않았다.

이미 짜면서도 `findIndex()`에서 이중 for문 형태가 되는게 거슬렸는데
아니나 다를까 불합격되었다.

이중 for문의 구조상 시간복잡도가 선형적으로 늘어나므로($O$($n$ X $m$)의 형태)
호명된 선수의 인덱스를 찾는 for문을 없애고, 선수의 현재 등수를 저장하는 Map으로 바로 접근할 수 있게 하였다.


```java
import java.util.HashMap;

public class Solution {
    public String[] solution(String[] players, String[] callings) {
        HashMap<String, Integer> currentRank = new HashMap<>();

        for (int i = 0; i < players.length; i++) {
            currentRank.put(players[i], i);
        }

        for (String call : callings) {
            // 호명된 선수의 순위
            int rank = currentRank.get(call);
            // 추월당한 선수와 호명된 선수의 순위 교체
            currentRank.put(players[rank-1], rank);
            currentRank.put(call, rank-1);
            
            // 등수 배열 순서 교체
            players[rank] = players[rank-1];
            players[rank-1] = call;
        }

        return players;
    }
}
```

조금 더 간결해지고, 속도도 빨라져 통과되었다.

