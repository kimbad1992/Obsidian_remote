## 🚩 문제
><h4>문제설명</h4>
>
게임개발자인 "죠르디"는 크레인 인형뽑기 기계를 모바일 게임으로 만들려고 합니다.
"죠르디"는 게임의 재미를 높이기 위해 화면 구성과 규칙을 다음과 같이 게임 로직에 반영하려고 합니다.
>
![](https://velog.velcdn.com/images/kimbad1992/post/11ac56d9-fd47-4d33-ae66-0219d2deee53/image.png)
>
>
게임 화면은 "1 x 1" 크기의 칸들로 이루어진 "N x N" 크기의 정사각 격자이며 위쪽에는 크레인이 있고 오른쪽에는 바구니가 있습니다. (위 그림은 "5 x 5" 크기의 예시입니다). 각 격자 칸에는 다양한 인형이 들어 있으며 인형이 없는 칸은 빈칸입니다. 모든 인형은 "1 x 1" 크기의 격자 한 칸을 차지하며 격자의 가장 아래 칸부터 차곡차곡 쌓여 있습니다. 게임 사용자는 크레인을 좌우로 움직여서 멈춘 위치에서 가장 위에 있는 인형을 집어 올릴 수 있습니다. 집어 올린 인형은 바구니에 쌓이게 되는 데, 이때 바구니의 가장 아래 칸부터 인형이 순서대로 쌓이게 됩니다. 다음 그림은 [1번, 5번, 3번] 위치에서 순서대로 인형을 집어 올려 바구니에 담은 모습입니다.
>
![](https://velog.velcdn.com/images/kimbad1992/post/feee7640-8f88-4df8-abec-af40232b580d/image.png)
>
만약 같은 모양의 인형 두 개가 바구니에 연속해서 쌓이게 되면 두 인형은 터뜨려지면서 바구니에서 사라지게 됩니다. 위 상태에서 이어서 [5번] 위치에서 인형을 집어 바구니에 쌓으면 같은 모양 인형 두 개가 없어집니다.
>
![](https://velog.velcdn.com/images/kimbad1992/post/7aa38e34-8b03-46c6-a19c-8e585dc6c70f/image.gif)
>
크레인 작동 시 인형이 집어지지 않는 경우는 없으나 만약 인형이 없는 곳에서 크레인을 작동시키는 경우에는 아무런 일도 일어나지 않습니다. 또한 바구니는 모든 인형이 들어갈 수 있을 만큼 충분히 크다고 가정합니다. (그림에서는 화면표시 제약으로 5칸만으로 표현하였음)
>
게임 화면의 격자의 상태가 담긴 2차원 배열 board와 인형을 집기 위해 크레인을 작동시킨 위치가 담긴 배열 moves가 매개변수로 주어질 때, 크레인을 모두 작동시킨 후 터트려져 사라진 인형의 개수를 return 하도록 solution 함수를 완성해주세요.
>
><h4>제한사항</h4>
>
* board 배열은 2차원 배열로 크기는 "5 x 5" 이상 "30 x 30" 이하입니다.
* board의 각 칸에는 0 이상 100 이하인 정수가 담겨있습니다.
  - 0은 빈 칸을 나타냅니다.
  - 1 ~ 100의 각 숫자는 각기 다른 인형의 모양을 의미하며 같은 숫자는 같은 모양의 인형을 나타냅니다.
* moves 배열의 크기는 1 이상 1,000 이하입니다.
* moves 배열 각 원소들의 값은 1 이상이며 board 배열의 가로 크기 이하인 자연수입니다.
>
----
><h4>입출력 예</h4>
>
|**board**|**moves**|**result**|
|---|---|---|
|[[0,0,0,0,0],[0,0,1,0,3],[0,2,5,0,1],[4,2,4,4,2],[3,5,1,3,1]]|[1,5,3,5,1,2,1,4]|4|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

 * 인형을 바구니에 옮길 시 밑부터 쌓이는 구조(인덱스를 뒤집어서 생각해야 하나?)
   - 인형이 위부터 적재되는 구조이므로,
   인형 1 2 3 4 5가 쌓이면, 인덱스 0,1,2,3,4가 아닌 4,3,2,1,0으로 검사해야 한다.
 * 인형이 연쇄 폭파 되는 경우는 어떻게 해야하나 생각했는데, 
 2개가 중복되면 사라지는 구조라 연쇄폭파 되는 경우는 절대 없다.
 한번 옮길 때 한번씩만 검사하면 된다.
 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public int solution(int[][] board, int[] moves) {
        Board bd = new Board(board);
        for (int move : moves) {
            bd.Grab(move-1);
        }
        return bd.getCount();
    }
    
    class Board {
        int [][] board;
        List<Integer> bucket;
        int count = 0;
        
        public Board (int[][] board) {
            this.board = board;
            this.bucket = new ArrayList<Integer>();
        }
        
        public void Grab (int move) {
            loop:
            for (int[] line : board) {
                if (line[move] != 0) { // 해당 열에 인형이 있을 경우
                    bucket.add(line[move]); // 바구니를 해당 인형으로 채운다
                    line[move] = 0; // 인형 빠진 자리
                    checkExplode();
                    break loop;
                }    
            }
        }
        
        public void checkExplode() {
            
            for (int i=1; i<bucket.size(); i++) {
                if (bucket.get(i) == bucket.get(i-1)) {
                    bucket.remove(i);
                    bucket.remove(i-1 <= 0 ? 0 : i-1);
                    count += 2;
                }
            }
            
            // BoardOnScreen();
        }
        
        public int getCount() {
            return this.count;
        }
        
        public void BoardOnScreen() {
            //현재 보드 확인용
            int idx = 0;
            for (int [] line : board) {
                System.out.println();
                for (int num : line) {
                    System.out.print(num);
                }
                if (line.length-idx-1 < bucket.size()) {
                    if (line.length-idx > idx) {
                        System.out.print(" "+bucket.get((line.length-idx)-1));
                    } else {
                        System.out.print(" "+bucket.get((line.length-idx)-1));
                    }
                }
                idx++;
            }
            System.out.println("\n--------------------");
        }
    } 
}
````
`int [][] board`는 2차원 정수 array로, 이전에 풀었던 키패드 문제랑 비슷한 느낌이었다.
* `moves`의 원소는 board의 열 번호를 지정하고,
board를 한줄씩 검사하면서 해당 열에 인형이 있는지를 찾는다.
  - `line[move]!=0` 이라면 해당 값은 인형의 값이 된다.
  - `line[move]=0` 이면 해당 열엔 인형이 없다는 의미이므로 그냥 통과한다.
  - `line[move]!=0` 이라면 `bucket`에 해당 인형을 채우고 인형 중첩을 검사한다.
    + `i`가 1부터 시작하므로 인형이 1개 이상 쌓인 상태일때만 검사를 한다.
    + 현재 들어온 인형과 이전 칸에 쌓여있던 인형이 동일한 인형이면 둘을 지우고
    지운 갯수만큼 count에 더해준다.
 * `getCount()`는 현재까지 쌓인 count를 return한다.
    
머릿속으로 도무지 그려지지가 않아서 일단 눈으로 확인해보려고
콘솔에 출력하는 method를 먼저 만들었다.
맨 처음엔 변수 `bucket`을 그냥 1차원 정수 array 형태로 만들고,
크기를 board와 동일하게 줬었는데(그림을 보고 바구니 크기도 같을거라 지레 생각함)
채점시 테스트 케이스에서 계속 오류가 나서 문제를 다시 읽어보니까
>또한 바구니는 모든 인형이 들어갈 수 있을 만큼 충분히 크다고 가정합니다. (그림에서는 화면표시 제약으로 5칸만으로 표현하였음)

라는 항목이 있었다. 역시 문제를 잘 읽어야한다.
그래서 문제에 있는 `board`의 최대 크기인 30 x 30 크기에
인형이 꽉 차있다고 생각하면 900개니까, `new int[900]`으로 만들어서 채점하니 통과했다.
근데 그냥 크기가 가변적인 `ArrayList`로 만들면 되지 않나 싶어서 `ArrayList`로 수정했다.


<br>
<br>

## ✔️ 다른 사람의 코드
```java
import java.util.Stack;

class Solution {
    public int solution(int[][] board, int[] moves) {
        int answer = 0;
        Stack<Integer> stack = new Stack<>();
        for (int move : moves) {
            for (int j = 0; j < board.length; j++) {
                if (board[j][move - 1] != 0) {
                    if (stack.isEmpty()) {
                        stack.push(board[j][move - 1]);
                        board[j][move - 1] = 0;
                        break;
                    }
                    if (board[j][move - 1] == stack.peek()) {
                        stack.pop();
                        answer += 2;
                    } else
                        stack.push(board[j][move - 1]);
                    board[j][move - 1] = 0;
                    break;
                }
            }
        }
        return answer;
    }
}
```
`Stack`이라는 클래스를 사용해서 푼 사람이 있었다.
좀 찾아보니 `Stack`과 `Queue`라는 비슷하지만 약간 다른 형태의 자료구조가 있었고,
`Stack`은 `LIFO(Last In, First Out)`구조를 가지고 있는데, 딱 여기서 bucket에 쓰면 될 것 같은 형태다.
![](https://velog.velcdn.com/images/kimbad1992/post/f01091ba-e846-4ad0-b180-eefb320cea29/image.png)
자료구조에 대해서도 공부를 좀 해야겠다 싶다.