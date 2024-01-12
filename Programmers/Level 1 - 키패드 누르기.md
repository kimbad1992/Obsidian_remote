## 🚩 문제
><h4>문제설명</h4>
스마트폰 전화 키패드의 각 칸에 다음과 같이 숫자들이 적혀 있습니다.
>
![](https://velog.velcdn.com/images/kimbad1992/post/ee39f41f-7831-4443-987b-50fd1cc07afb/image.png)
이 전화 키패드에서 왼손과 오른손의 엄지손가락만을 이용해서 숫자만을 입력하려고 합니다.
맨 처음 왼손 엄지손가락은 * 키패드에 오른손 엄지손가락은 # 키패드 위치에서 시작하며, 엄지손가락을 사용하는 규칙은 다음과 같습니다.
>
1. 엄지손가락은 상하좌우 4가지 방향으로만 이동할 수 있으며 키패드 이동 한 칸은 거리로 1에 해당합니다.
2. 왼쪽 열의 3개의 숫자 1, 4, 7을 입력할 때는 왼손 엄지손가락을 사용합니다.
3. 오른쪽 열의 3개의 숫자 3, 6, 9를 입력할 때는 오른손 엄지손가락을 사용합니다.
4. 가운데 열의 4개의 숫자 2, 5, 8, 0을 입력할 때는 두 엄지손가락의 현재 키패드의 위치에서 더 가까운 엄지손가락을 사용합니다.
4-1. 만약 두 엄지손가락의 거리가 같다면, 오른손잡이는 오른손 엄지손가락, 왼손잡이는 왼손 엄지손가락을 사용합니다.
>
순서대로 누를 번호가 담긴 배열 numbers, 왼손잡이인지 오른손잡이인 지를 나타내는 문자열 hand가 매개변수로 주어질 때, 각 번호를 누른 엄지손가락이 왼손인 지 오른손인 지를 나타내는 연속된 문자열 형태로 return 하도록 solution 함수를 완성해주세요.
><h4>제한사항</h4>
numbers 배열의 크기는 1 이상 1,000 이하입니다.
numbers 배열 원소의 값은 0 이상 9 이하인 정수입니다.
hand는 "left" 또는 "right" 입니다.
"left"는 왼손잡이, "right"는 오른손잡이를 의미합니다.
왼손 엄지손가락을 사용한 경우는 L, 오른손 엄지손가락을 사용한 경우는 R을 순서대로 이어붙여 문자열 형태로 return 해주세요.
><h4>입출력 예</h4>
>
|**numbers**|**hand**|**result**|
|---|---|---|
|[1, 3, 4, 5, 8, 2, 1, 4, 5, 9, 5]|"right"|"LRLLLRLLRRL"
|[7, 0, 8, 2, 8, 3, 1, 5, 7, 6, 2]|"left"|"LRLLRRLLLRR"
|[1, 2, 3, 4, 5, 6, 7, 8, 9, 0]|"right"|"LLRLLRLLRL"
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각
 - numbers의 다음 번호가 현재 어느손에서 더 가까운지 알아야 한다.
 - 키패드의 위치는 2차원 좌표로 나타낼 수 있다.
 맨 처음엔 키패드 번호를 1~12까지로 놓고 현재 손의 위치의 키패드 번호를 빼는 식의 규칙성이 있는지 찾아봤으나
 그런건 없었고, 인터넷을 찾아보니 2차원 좌표의 거리를 구하는 공식이 있었다.
 - 키패드 번호가 들어오면 해당 키패드의 2차원 좌표를 반환하는 method가 필요하다.
 - 왼손과 오른손이 거리가 동일하면 hand를 따라감
 
 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public String solution(int[] numbers, String hand) {
        String answer = "";
        Coordinate pos = new Coordinate(hand);
        for (int num : numbers) {
            answer += pos.move(num);
        }
        return answer;
    }
    
    class Coordinate {
        private final int[][] keypad = new int[][] {
            {1,2,3},
            {4,5,6},
            {7,8,9},
            {-1,0,-2}
        };
        private int left = -1;
        private int right = -2;
        private String hand;
        
        public Coordinate (String hand) {
            this.hand = hand;
        }
        
        private int[] getPosition(int number) {
            int[] pos = new int[] { 0, 0 };

            for (int i = 0; i < keypad.length; i++)
            {
                for (int j = 0; j < keypad[i].length; j++)
                {
                    if (keypad[i][j] == number)
                    {
                        pos[0] = i;
                        pos[1] = j;
                    }
                }
            }
            
            return pos;
        }
        
        private String move (int num) {
            int [] pos, left, right;
            pos = getPosition(num);
            left = getPosition(this.left);
            right = getPosition(this.right);
            
            if (num == 1 || num == 4 || num == 7) { // 1,4,7일 경우 왼손
                this.left = keypad[pos[0]][pos[1]];
                return "L";
            } else if (num == 3 || num == 6 || num == 9) { // 3,6,9일 경우 오른손
                this.right = keypad[pos[0]][pos[1]];
                return "R";
            }
           
           // 2,5,8,0일 경우
           int fromLeft = Math.abs(left[0] - pos[0]) + Math.abs(left[1] - pos[1]);
           int fromRight = Math.abs(right[0] - pos[0]) + Math.abs(right[1] - pos[1]);
           
           if (fromLeft == fromRight) { // 거리 동일
                if (this.hand.equals("right")) {
                    this.right = keypad[pos[0]][pos[1]];
                    return "R";
                } else {
                    this.left = keypad[pos[0]][pos[1]];
                    return "L";
                }
            } else if (fromLeft < fromRight) { // 왼손과 가까울 시
                this.left = keypad[pos[0]][pos[1]];
                return "L";
            } else { // 오른손과 가까울 시
                this.right = keypad[pos[0]][pos[1]];
                return "R";
            }
        }
    }
}
````
이전에 다른 연습 문제에서 객체 지향으로 푼 사람의 코드가 있어 나도 약간 따라해보기로 했다.
키패드의 최초 상태를 2차원 정수 array로 만들어주었다.
그리고 키패드 번호가 들어오면 해당 번호의 인덱스(그러니까 좌표가 될)를 반환하는 method를 만들어주었다.
```move(num)```은 num이 1,4,7 이면 "L", 3,6,9면 "R", 그 외의 경우는 손으로부터의 거리에 따라 L,R를 반환하게 한다.
좌표를 구하는데 사용한 ```Math.abs()```는 괄호안 값의 절대값을 반환한다. 음수여도 양수가 나온다. 두 점 사이의 거리가 마이너스일 순 없으니까.
<br>
<br>

## ✍🏻 참조할 점
```java
int[][] numpadPos = {
            {3,1}, //0
            {0,0}, //1
            {0,1}, //2
            {0,2}, //3
            {1,0}, //4
            {1,1}, //5
            {1,2}, //6
            {2,0}, //7
            {2,1}, //8
            {2,2}  //9
    };
```
이런식으로 해당 키패드 번호의 인덱스에 직접 좌표를 지정한 형태도 있었다.
다시 생각해보니 *, #은 최초에 왼손 오른손이 위치한 자리일 뿐 더 이상 사용되지 않기 때문에 이 형태가 더 좋아보인다.
