## 🚩 문제
><h4>문제설명</h4>
배열 arr가 주어집니다. 배열 arr의 각 원소는 숫자 0부터 9까지로 이루어져 있습니다. 이때, 배열 arr에서 연속적으로 나타나는 숫자는 하나만 남기고 전부 제거하려고 합니다. 단, 제거된 후 남은 수들을 반환할 때는 배열 arr의 원소들의 순서를 유지해야 합니다. 예를 들면,
>
 * arr = [1, 1, 3, 3, 0, 1, 1] 이면 [1, 3, 0, 1] 을 return 합니다.
 * arr = [4, 4, 4, 3, 3] 이면 [4, 3] 을 return 합니다.
>
배열 arr에서 연속적으로 나타나는 숫자는 제거하고 남은 수들을 return 하는 solution 함수를 완성해 주세요.
>
><h4>제한사항</h4>
>
* 배열 arr의 크기 : 1,000,000 이하의 자연수
* 배열 arr의 원소의 크기 : 0보다 크거나 같고 9보다 작거나 같은 정수
>
><h4>입출력 예</h4>
>
|**arr**|**answer**|
|---|---|
|[1,1,3,3,0,1,1]|[1,3,0,1]|
|[4,4,4,3,3]|[4,3]|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각
 * answer의 첫 값은 arr의 첫 값과 동일하다. (대조할 필요가 없음)
 * `arr[n-1]` == `arr[n]`이면 연속적인 값이다.
 
 
 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

public class Solution {
    public List<Integer> solution(int []arr) {
        List<Integer> list = new ArrayList<Integer>();
        list.add(arr[0]); // 첫 값
        
        for (int i=1; i<arr.length; i++) {
            if (arr[i-1] != arr[i]) {
                list.add(arr[i]);
            }
        }
        
        return list;
    }
}
````
크게 생각하지 않고 생각나는 대로 풀어도 되는 Level 1다운 문제
<br>
<br>

## ✔️ 다른 사람의 코드
```java
import java.util.*;

public class Solution {
    public int[] solution(int []arr) {
        ArrayList<Integer> tempList = new ArrayList<Integer>();
        int preNum = 10;
        for(int num : arr) {
            if(preNum != num)
                tempList.add(num);
            preNum = num;
        }       
        int[] answer = new int[tempList.size()];
        for(int i=0; i<answer.length; i++) {
            answer[i] = tempList.get(i).intValue();
        }
        return answer;
    }
}
```
배열의 인덱스로 값을 뽑아서 대조하지 않고,
직전 값을 넣어 임시 변수를 만들어서 대조한 사람도 있었다.
원소의 크기는 1~9까지 나오니 preNum을 10으로 선언한 것도 정말 똑똑하다.