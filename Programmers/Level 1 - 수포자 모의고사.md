## 🚩 문제
><h4>문제설명</h4>
수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.
>
>	1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
	2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
	3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...
>
>1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.
><h4>제한 조건</h4>
시험은 최대 10,000 문제로 구성되어있습니다.
문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.
><h4>입출력 예</h4>
>
|**answers**|**return**|
|---|---|
|[1,2,3,4,5]|[1]|
|[1,3,2,4,2]|[1,2,3]|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각
 - 찍기 패턴을 반복하는건 해당 패턴의 배열을 생성하고, 
 배열의 길이를 초과하는 값은 배열의 길이로 나눴을때의 나머지로 계산한다.
 ex) 1번 수포자가 6번 문제를 찍는 경우 -> 6%5 = 1
 - 정답과 대조해서 맞춘 문제의 갯수만큼 더한다
 - 3명의 점수를 비교해 최고 득점자의 점수를 maxScore에 대입
 - 3명의 점수를 maxScore와 비교해 List에 추가
 - 정답자가 없을 경우 {1,2,3} 오름차순 정렬하여 return
 <br>
 <br>
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        final int[] one = {1,2,3,4,5};
        final int[] two = {2,1,2,3,2,4,2,5};
        final int[] three = {3,3,1,1,2,2,4,4,5,5};
        int maxScore = 0;
        Map<String, Integer> count = new HashMap<String, Integer>();
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == one[i%one.length]) {
                count.put("one", count.getOrDefault("one",0)+1);
            }
        }
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == two[i%two.length]) {
                count.put("two", count.getOrDefault("two",0)+1);
            }
        }
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == three[i%three.length]) {
                count.put("three", count.getOrDefault("three",0)+1);
            }
        }
        
        List<Integer> list = new ArrayList<Integer>();
        for (String key : count.keySet()) {
            // System.out.println(key+"는 "+count.get(key)+"문제를 맞췄습니다.");
            maxScore = maxScore < count.get(key) ? count.get(key) : maxScore;
        }
        
        int idx = 1;
        for (String key : count.keySet()) {
            if (maxScore == count.get(key)) {
                list.add(idx);
            }
            idx++;
        }
        
        if (maxScore == 0) {
            return new int[] {1,2,3};
        }
        
        return list.stream()
                .mapToInt(i -> i)
                .toArray();
    }
}
````


최초 작성한 코드인데, 채점하니 테스트 케이스 1,2번에서만 계속 오류가 났다.
프로그래머스에선 채점 시 테스트케이스는 공개하지 않으니 내가 방법을 찾아야 하는데,
아무리 생각해봐도 모르겠어서 질문을 했다.
얼마 지나지 않아서 답변이 달렸고 그걸 보니 그제서야 이해가 됐다.
#### A : "1번 수포자가 한 문제도 못 맞춘 경우 hashMap에 들어가지 않아서 idx에서 오류가 발생하는 것 같습니다."

#### ❌  1번 수포자가 0점이고, 2,3번 수포자만 맞춘 경우
 count HashMap에 {two=정답수, three=정답수} 가 들어가게 되어
 count의 크기는 2가 되는것도 문제고, 각 점수와 maxScore를 비교하면
 keySet을 돌릴때 처음 시작은 two인데도 idx는 1로 들어가고 three는 2로 들어가게 된다
 원래 정답은 {2,3,1}이 return이 되어야 하지만 {1,2}가 return이 되어 에러 발생
 0점자가 있을 경우는 무조건 틀리게 되는 것이었다
 <br>
 <br>
 
 ## ✔️ 수정
```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        final int[] one = {1,2,3,4,5};
        final int[] two = {2,1,2,3,2,4,2,5};
        final int[] three = {3,3,1,1,2,2,4,4,5,5};
        int maxScore = 0;
        Map<String, Integer> count = new HashMap<String, Integer>();
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == one[i%one.length]) {
                count.put("one", count.getOrDefault("one",0)+1);
            }
        }
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == two[i%two.length]) {
                count.put("two", count.getOrDefault("two",0)+1);
            }
        }
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == three[i%three.length]) {
                count.put("three", count.getOrDefault("three",0)+1);
            }
        }
        
        List<Integer> list = new ArrayList<Integer>();
        for (String key : count.keySet()) {
            // System.out.println(key+"는 "+count.get(key)+"문제를 맞췄습니다.");
            maxScore = maxScore < count.get(key) ? count.get(key) : maxScore;
        }
        
//        int idx = 1;
//        for (String key : count.keySet()) {
//           if (maxScore == count.get(key)) {
//                list.add(idx);
//            }
//            idx++;
//        }
        
        for (String key : count.keySet()) {
            System.out.println(key+"는 "+count.get(key));
            if (maxScore == count.get(key)) {
                if (key.equals("one")) {
                    list.add(1);
                } else if (key.equals("two")) {
                    list.add(2);
                } else if (key.equals("three")) {
                    list.add(3);
                }
            }
        }
        
        if (maxScore == 0) {
            return new int[] {1,2,3};
        }
        
        return list.stream()
                .mapToInt(i -> i)
                .toArray();
    }
}
```
따라서 key값에 따라 List의 값을 다르게 주니 정답처리가 되었다.
그러나 count 변수를 정수 array를 쓰는 형태가 약 10배가량 속도가 빨랐고,
시간복잡도 면에서 이것보다 정수 array를 쓰는게 정답이다.
다시 생각해보면 왜 굳이 HashMap을 썼는지 모르겠다.
정수 array를 쓰는게 코드도 훨씬 깔끔하다.
<br>
<br>

## ✔️ 정수 Array를 쓴 형태
```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        final int[] one = {1,2,3,4,5};
        final int[] two = {2,1,2,3,2,4,2,5};
        final int[] three = {3,3,1,1,2,2,4,4,5,5};
        int maxScore = 0;
        int[] count = {0,0,0};
        
        for (int i=0; i<answers.length; i++) {
            if (answers[i] == one[i%one.length]) {
                count[0] += 1;
            }
            if (answers[i] == two[i%two.length]) {
                count[1] += 1;
            }
            if (answers[i] == three[i%three.length]) {
                count[2] += 1;
            }
        }
        
        List<Integer> list = new ArrayList<Integer>();
        for (int num : count) {
            maxScore = maxScore < num ? num : maxScore;
        }
        
        int idx = 1;
        for (int num : count) {
            if (maxScore == num) {
                list.add(idx);
            }
            idx++;
        }
        
        return list.stream()
                .mapToInt(i -> i)
                .toArray();
    }
}
```