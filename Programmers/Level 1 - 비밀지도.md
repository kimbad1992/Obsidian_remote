## 🚩 문제
><h4>문제설명</h4>
>
네오는 평소 프로도가 비상금을 숨겨놓는 장소를 알려줄 비밀지도를 손에 넣었다. 그런데 이 비밀지도는 숫자로 암호화되어 있어 위치를 확인하기 위해서는 암호를 해독해야 한다. 다행히 지도 암호를 해독할 방법을 적어놓은 메모도 함께 발견했다.
>
1. 지도는 한 변의 길이가 n인 정사각형 배열 형태로, 각 칸은 "공백"(" ") 또는 "벽"("#") 두 종류로 이루어져 있다.
2. 전체 지도는 두 장의 지도를 겹쳐서 얻을 수 있다. 각각 "지도 1"과 "지도 2"라고 하자. 지도 1 또는 지도 2 중 어느 하나라도 벽인 부분은 전체 지도에서도 벽이다. 지도 1과 지도 2에서 모두 공백인 부분은 전체 지도에서도 공백이다.
3. "지도 1"과 "지도 2"는 각각 정수 배열로 암호화되어 있다.
4. 암호화된 배열은 지도의 각 가로줄에서 벽 부분을 1, 공백 부분을 0으로 부호화했을 때 얻어지는 이진수에 해당하는 값의 배열이다.
>
![](https://velog.velcdn.com/images/kimbad1992/post/b37830a5-874d-4096-b598-d027bf0caf41/image.png)
>네오가 프로도의 비상금을 손에 넣을 수 있도록, 비밀지도의 암호를 해독하는 작업을 도와줄 프로그램을 작성하라.
>
><h4>입력 형식</h4>
>입력으로 지도의 한 변 크기 n 과 2개의 정수 배열 arr1, arr2가 들어온다.
>
* 1 ≦ n ≦ 16
* arr1, arr2는 길이 n인 정수 배열로 주어진다.
* 정수 배열의 각 원소 x를 이진수로 변환했을 때의 길이는 n 이하이다. 즉, 0 ≦ x ≦ 2n - 1을 만족한다.
>
><h4>출력 형식</h4>
>
>입력으로 지도의 한 변 크기 n 과 2개의 정수 배열 arr1, arr2가 들어온다.
>원래의 비밀지도를 해독하여 '#', 공백으로 구성된 문자열 배열로 출력하라.
>
----
><h4>입출력 예</h4>
>
| **매개 변수** | **값** | 
| --- | --- |
| n | 5 |
|arr1|\[9, 20, 28, 18, 11]|
|arr2|\[30, 1, 21, 17, 28]|
|출력|\["#####","# # #", "### #", "# ##", "#####"]|
><br>
>
|**매개 변수**|**값**|
|---|---|
|n|6|
|arr1|\[46, 33, 33 ,22, 31, 50]
|arr2|\[27 ,56, 19, 14, 14, 10]
|출력|\["######", "### #", "## ##", " #### ", " #####", "### # "]
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

* `arr1`과 `arr2`를 해독(2진수 변환)하는 과정은 똑같다.
* 2진수 변환 후 -> 2개를 합치고 -> 1은 #, 0은 공백으로 변환한다.
* 2진수로 변환 후 자릿수가 부족할 경우 앞쪽을 0으로 채워야 한다.

 <br>
 <br>
 
## 📝 코드 작성

```java
import java.util.*;

class Solution {
    public String[] solution(int n, int[] arr1, int[] arr2) {
        String[] answer = new String[n];
        String[] arr1Decode = decode(arr1, n);
        String[] arr2Decode = decode(arr2, n);
        
        for (int i=0; i<n; i++) {
            String str = "";
            for (int j=0; j<n; j++) {
                if (arr1Decode[i].charAt(j) == arr2Decode[i].charAt(j)) {
                    if (arr1Decode[i].charAt(j) == '1') {
                        str += "#";
                    } else {
                        str += " ";
                    }
                } else { // 다르면 무조건 1 (벽)
                    str += "#";
                }
            }
            answer[i] = str;
        }
        
        return answer;
    }
    
    public String[] decode (int[] arr, int n) {
        String[] arrDecode = new String[n];
        
        for (int j=0; j<arr.length; j++) {
            String str = "";
            int num = arr[j];
            for (int i=0; i<=n; i++) {
                if (num > 0) { // 2진수 변환
                    str = (num % 2) + str;
                    num /= 2;
                } else if (num <= 0 && str.length() < n) { // 한 변의 길이 맞추기
                    str = "0" + str;
                }
            }
            arrDecode[j] = str;
        }
        
        return arrDecode;
    }
}
````
각 `arr`을 2진수로 변환하는 method `decode()`를 만들어 주었다.
2진수로 변환 한 `arr1`과 `arr2`를 2중 for문을 돌려(현실이라면 지도를 겹치는 과정)
바로 `"#"`과 공백으로 바꿔주었다.


<br>
<br>

## ✔️ 다른 사람의 코드
```java
class Solution {
  public String[] solution(int n, int[] arr1, int[] arr2) {
        String[] result = new String[n];
        for (int i = 0; i < n; i++) {
            result[i] = Integer.toBinaryString(arr1[i] | arr2[i]);
        }

        for (int i = 0; i < n; i++) {
            result[i] = String.format("%" + n + "s", result[i]);
            result[i] = result[i].replaceAll("1", "#");
            result[i] = result[i].replaceAll("0", " ");
        }

        return result;
    }
}
```
`Integer` 클래스에 `toBinaryString()`이라는 10진수를 2진수로 변환하는 함수가 내장되어 있었다.