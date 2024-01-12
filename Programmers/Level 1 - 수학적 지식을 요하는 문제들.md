## 💡 기본적인 수학적 지식 부족...

Level 1 과정에 있는 짧은 알고리즘 문제들이 있었는데,
**행렬의 덧셈**이나 **최대공약수/최소공배수 구하기**같은
중학생, 고등학생 과정에서 배우는 수학의 기초를 요구하는 문제들이 가끔 있었다.
오히려 생각을 해서 좀 풀어 볼 수 있는 다른 문제들과는 달리 어떤 공식을 모르면
풀리지 않거나 시도조차 할 수 없는 경우도 있었다.
또 한번 내가 정말 아는게 없구나 느꼈던 순간들이었다. 생각보다 간단하게 풀린 문제들을 이외에
도저히 안되겠다 싶어 인터넷을 찾아 본 문제들도 많다.

혹여 나중에도 까먹고 못쓰게 될까봐 여기에 정리해놓으려고 한다.


## 🗂️ 찾아봐야 했던 문서들

#### 하샤드 수
  - [Java에서 Char를 Int로 변환하는 방법](https://www.delftstack.com/ko/howto/java/how-to-convert-char-to-int-in-java/)
  - [아스키 코드표](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FqOPNt%2FbtrAdcY26CF%2FKsn1qKzUqEaCql1Cbk6GG0%2Fimg.png)
#### 콜라츠 추측
  - [콜라츠 추측 - Veritasium(한국어 채널)](https://youtu.be/5dZKr-Z2FO4)
#### 최대 공약수와 최소 공배수
  - [유클리드 호제법 - 나무위키](https://namu.wiki/w/%EC%9C%A0%ED%81%B4%EB%A6%AC%EB%93%9C%20%ED%98%B8%EC%A0%9C%EB%B2%95#s-3.1)
  - [소인수 분해법 / 출처 - 수수니님 블로그](https://susuni11.tistory.com/8)
  - [소인수 분해법 / 출처 - 수학방](https://mathbang.net/202)
#### 정수 제곱근 판별
  - [제곱근의 뜻과 표현](https://mathbang.net/254)
#### 소수 구하기
  - [소수 구하기 / 출처 - loosie님 블로그](https://loosie.tistory.com/267)
    + [에라토스테네스의 체](https://velog.velcdn.com/images%2Fjy1999%2Fpost%2Fcbb1067c-6436-4a54-97ce-ad8921edada0%2F%EC%97%90%EB%9D%BC%ED%86%A0%EC%8A%A4%ED%85%8C%EB%84%A4%EC%8A%A4%EC%9D%98%20%EC%B2%B4.gif)
    + 소인수 분해법


 <br>
 <br>
 

## 📜 문제들

### 직사각형 별찍기

```java
import java.util.Scanner;

class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int x = sc.nextInt();
        int y = sc.nextInt();
        
        for (int i=0; i<y; i++) {
            for (int j=0; j<x; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```
입력 값에 따라 `*`을 출력하는 문제. 알고리즘 문제들 중 가장 기본으로 알고 있다.
정말 아무것도 몰라도 쓰고 지우고 테스트해보면 알 수 있는 문제.

<br>

### x만큼 간격이 있는 n개의 숫자
```java
class Solution {
    public long[] solution(int x, int n) {
        long[] answer = new long[n];
        long x1 = (long)x;
        
        for (int i=1; i<=n; i++) {
            answer[i-1] = x1 * i;
        }
        
        return answer;
    }
}
```
조건 상 최대로 나올 수 있는 값이 100억이라 시작부터 return 형태가 `long`으로 되어있다.
그냥 간단한 수열 문제인데, 왠지 모르겠는데 x를 `long` type으로 형변환을 해주지 않으면 계속 에러가 났다.
조건에서 `x`의 최대값은 $10,000,000$이라 `int` type의 최대치를 넘지 않는데도...
`x1 * i`의 값이 `int` type 최대치를 넘어가서 그런 것 같은데, 그 값은 `answer[n]`인 `long` type에 들어가니까 에러가 없어야 하는게 맞지 않나? 싶다.

<br>

### 배열의 덧셈
```java
class Solution {
    public int[][] solution(int[][] arr1, int[][] arr2) {
        int[][] answer = new int[arr1.length][arr1[0].length];
        
        for (int i=0; i<arr1.length; i++) {
            for (int j=0; j<arr1[i].length; j++) {
                answer[i][j] = arr1[i][j] + arr2[i][j];
            }
        }
        
        return answer;
    }
}
```
2차원 배열 덧셈 문제인데, 이게 엄청 헷갈렸다.
사실 문제 형태만 약간 바꿔놓고 다시 나오면 또 헤맬 것 같다.

<br>

### 하샤드 수
```java
class Solution {
    public boolean solution(int x) {
        int sum = 0;
        char[] arr = String.valueOf(x).toCharArray();
        
        for (char ch : arr) {
            sum += ch - '0';
        }
        
        if (x % sum == 0) {
            return true;
        } else {
            return false;
        }
    }
}
```
> 양의 정수 x가 하샤드 수이려면 x의 자릿수의 합으로 x가 나누어져야 합니다. 예를 들어 18의 자릿수 합은 1+8=9이고, 18은 9로 나누어 떨어지므로 18은 하샤드 수입니다. 자연수 x를 입력받아 x가 하샤드 수인지 아닌지 검사하는 함수, solution을 완성해주세요.

라고 친절하게 하샤드 수가 무엇인지 알려주고 구하라고 했다.
문제에서 요구하는건 하샤드 수에 대한 지식이 아니라,
숫자 데이터의 자릿수를 구하는 방법을 요구하는 것으로 보이고
지난번 다른 문제를 풀때 배웠던 `toCharArray()`를 통해 풀었다.
`for (char ch : arr) { sum += ch - '0'; }` 부분은 `char`를 `int`형으로 변환하는 편법 중 하나를 사용했다.
`char - '0'`을 하면 해당 아스키 코드 값의 10진수 형태를 반환하기 때문이다.
혹은 `Character.getNumericValue(ch)`를 사용하는 방법도 있었다.

<br>

### 평균 구하기
```java
class Solution {
    public double solution(int[] arr) {
        double answer = 0;
        for (int n : arr) {
            answer += (double)n;
        }
        answer /= arr.length;
        return answer;
    }
}
```
이건 뭐지? 싶었던 문제. 가볍게 +1점 먹고 갔다.
`return (int) Arrays.stream(array).average().orElse(0);` 로 한줄 처리를 한 사람도 있었다.

<br>

### 콜라츠 추측
```java
class Solution {
    public int solution(long n) {
        int answer = 0;
        
        if (n == 1) {
            return 0;
        } else {
            do {
                if (n % 2 == 0) {
                    n = n / 2;
                } else {
                    n = (n * 3) + 1;
                }
                answer++;
                if (answer >= 500) {
                    return -1;
                }
            } while (n != 1);
            
            return answer;
        }
    }
}
```

> #### 콜라츠 추측
1937년 Collatz란 사람에 의해 제기된 이 추측은, 주어진 수가 1이 될 때까지 다음 작업을 반복하면, 모든 수를 1로 만들 수 있다는 추측입니다. 작업은 다음과 같습니다.
>>1-1. 입력된 수가 짝수라면 2로 나눕니다. 
>>1-2. 입력된 수가 홀수라면 3을 곱하고 1을 더합니다. 
>>2. 결과로 나온 수에 같은 작업을 1이 될 때까지 반복합니다.

예전에 유튜브 추천으로 떠서, [Veritasium - 한국어 채널](https://www.youtube.com/channel/UCOgK3J7WTOl5f5Fd51kcoSg)에서 재밌게 본 기억이 났다.
'추측'인 이유는 아직  증명이 되지 않았기 때문이다.

문제 자체는 간단했다. 위 과정을 1이 될때까지 반복 시키면 되기 때문이다.
그리고 과정이 500번을 넘어가면 `-1`을 return한다.
또한 사소한 문제가 있었는데, 위 과정을 반복 중에 `n`의 값이 정수 최대치를 넘어가 오버플로우가 되는 경우가 있었다.
그걸 모른 상태로 돌리니 계속 잘못된 값이 나와서 왜 안되나 한참 헤맸었고, 질문하기에서 답을 얻었다.
덕분에 완전히 기억할 수 있는 케이스가 된 것 같다. 오히려 한참 헤매서 득이 됨.

<br>

### 최대 공약수와 최소 공배수
```java
class Solution {
    public int[] solution(int n, int m) {
        return new int[]{comDiv(n,m), comMul(n,m)};
    }
    
    public int comDiv(int a, int b){
		while(b!=0){
			int r = a%b;
			a= b;
			b= r;
		}
		return a;
	}
    
    public int comMul(int a, int b){
	    return a * b / comDiv(a,b);
	}
}
```
날 자괴감에 빠지게 만든 문제
공배수, 공약수 등 전부 초등학생, 중학생 때 배우는건데 기억도 안나고,
인터넷 찾아봐도 어떻게 구현할지 이해가 안되고 자괴감에 빠졌었다.
문제 자체가 너무 단순해서 더욱 그랬다.
찾아보니 **유클리드 호제법**이라는 방식이 있었는데,
최대공약수와 최소공배수 구하는 법 같은 경우는, 실제로도 쓰는일이 간혹 있다고 하니
완벽히 알아두는게 좋겠다.

<br>

### 짝수와 홀수
```java
class Solution {
    public String solution(int num) {
        return num % 2 == 0 ? "Even" : "Odd";
    }
}
```
평균 구하기보다 더한 문제.....

<br>


### 정수 제곱근 판별
```java
import java.util.*;

class Solution {
    public long solution(long n) {
        long x = (long)Math.sqrt(n);
        return Math.pow(x, 2) == n ? (long)Math.pow(x+1, 2) : -1;
    }
}
```
아마 검색하지 않았다면 한참 걸렸을 것 같은 문제로,
$\sqrt{n}$ 이 정수인지 아닌지를 구하는 문제인데,
$\sqrt{}$ 이거 어떻게 구하더라?????????????????
결국 인터넷을 뒤져서 `Math.sqrt()`라는 내장 함수를 찾았고, 이걸 이용하니 쉽게 풀렸으나
제곱근 구하는 법도 모르고 이래도 되나 싶어서 제곱근 정보를 좀 찾아봤다...

<br>

### 약수의 합
```java
class Solution {
    public int solution(int n) {
        int answer = 0;
        for (int i=1; i<=n; i++) {
            answer += n%i == 0 ? i : 0;
        }
        return answer;
    }
}
```
`i`로 나눈 나머지가 0이면 약수니까 카운트 증가. 간단한 문제


<br>

### 소수 찾기
```java
class Solution {
    public int solution(int n) {
        int answer = -1;
        
        for (int i=1; i<=n; i++) {
            if(isPrime(i)) {
                answer++;
            }
        }
        
        return answer;
    }
    
    public boolean isPrime(int num) {
        
        for(int i=2 ; i*i<=num; i++) {
            if(num%i ==0) {
                return false;
            }
        }
        
        return true;
    }
}
```
이것도 매우 한참동안 못 풀었는데, `1`부터 `n`사이의 숫자 중 소수가 몇 개인지를 구해야 한다.
소수 판별을 어떻게 하지? 조건을 여러개 달아야 하나?
`(n / i == 0 && n / 1 == 0)` 이런 식으로 해야하나 한참 씨름 했고,
결국 하다 하다 안되서 인터넷을 뒤져 '**에라토스테네스의 체**'라는 소수 판별법을 찾았다.
발상이 아주 간단한 데, 어떤 수의 배수는 소수가 아니라는거다.
에라토스테네스가 누군지는 몰라도, 프로그래밍을 했으면 아주 잘했을 사람이다.

<br>

### 소수 만들기
```java
class Solution {
    public int solution(int[] nums) {
        int answer = 0;
        
        for (int i=0; i<nums.length; i++) {
            for (int j=i+1; j<nums.length; j++) {
                for (int o=j+1; o<nums.length; o++) {
                    if (isPrime(nums[i]+nums[j]+nums[o])) {
                        answer++;
                    }
                }
            }
        }

        return answer;
    }
    
    public boolean isPrime(int num) {
        
        for(int i=2 ; i*i<=num; i++) {
            if(num%i ==0) {
                return false;
            }
        }
        
        return true;
    }
}
```
두개의 숫자는 해봤는데 세개의 숫자라니... 3중 for문을 쓰는건가? 어떻게 해야하지?
소수 판별은 이전에 했던 [소수 찾기](#소수-찾기)에서 해봤으니 그대로 쓰고.
3중 for문은 아예 짜본 경험이 없어서 어떻게 하는거지? 했는데
화장실에서 볼 일 보던 중 아 그냥 저렇게 하면 되겠구나 해서 짰다.

근데 말 그대로 $n$부터 $n+1$, $n+2$ 훑고, 쭉 반복하는거라
속도면에서 굉장히 손해를 보지 않을까 싶다. 아마 다른 방법도 있을 것 같은데...