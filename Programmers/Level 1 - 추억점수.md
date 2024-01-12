
## 📝 코드 작성

다른 사람의 정답율도 60%가 넘어가는 매우 쉬운 문제로 생각하는 그대로 짜면
통과할 수 있는 코드였다.

```java
import java.util.*;

class Solution {
    public int[] solution(String[] name, int[] yearning, String[][] photo) {
        int[] answer = new int[photo.length];
        HashMap<String, Integer> people = new HashMap<>();
        
        for (int i=0; i<name.length; i++) {
            people.put(name[i], yearning[i]);
        }
        
        for (int i=0; i<photo.length; i++) {
            int yearScore = 0;
            for (String nm : photo[i]) {
                yearScore += people.getOrDefault(nm, 0);
            }
            answer[i] = yearScore;
        }
        
        return answer;
    }
}
```

인물의 그리움 점수 Map을 만들고 합산하되
`getOrDefault()`로 그리워하는 인원이 아닌 경우의 점수를 0으로 주면 해결.