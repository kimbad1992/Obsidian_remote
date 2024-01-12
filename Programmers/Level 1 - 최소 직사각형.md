
## 🌌 생각
 * "가로로 눕혔을 때"... 모든 원소를 뒤집어서 대조해야 하나?
 * 가로보다 세로가 큰 경우만 뒤집어서 비교하기로 했다.
 * 결국엔 명함들을 각잡아서 놨을 때 모든 명함이 들어가는 지갑이 필요하다.
 * 원소들 중 (가로에서 제일 큰 값 * 세로에서 제일 큰 값)을 계산하면 될 것 같다.
 
 <br>
 <br>
 
## 📝 코드 작성

```java
class Solution {
    public int solution(int[][] sizes) {
        int[] max = new int[2];
        for (int i=0; i<sizes.length; i++) {
            if (sizes[i][0] < sizes[i][1]) { // 세로가 더 큰 경우 뒤집는다
                int tmp = 0;
                tmp = sizes[i][0];
                sizes[i][0] = sizes[i][1];
                sizes[i][1] = tmp;
            }
            if (max[0] <= sizes[i][0]) {
                max[0] = sizes[i][0];
            }
            if (max[1] <= sizes[i][1]) {
                max[1] = sizes[i][1];
            }
        }
        
        return max[0] * max[1];
    }
}
````
사이즈 다른 명함 겹쳐놓고 얘네가 다 들어갈 수 있는 크기의 지갑을 만들어야 하니
가장 큰 값만 가져와 넓이를 구해주면 되는거였다.

<br>
<br>

## ✔️ 다른 사람의 코드
```java
class Solution {
    public int solution(int[][] sizes) {
        int length = 0, height = 0;
        for (int[] card : sizes) {
            length = Math.max(length, Math.max(card[0], card[1]));
            height = Math.max(height, Math.min(card[0], card[1]));
        }
        int answer = length * height;
        return answer;
    }
}

```
`Math.max`와 `Math.min`을 사용한 간결한 코드
이런 풀이를 보면 내 수학적 소양이 많이 부족함을 다시금 느끼게 된다
