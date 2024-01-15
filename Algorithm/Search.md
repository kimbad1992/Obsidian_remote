
## 🔍 탐색 알고리즘
[출처 : bba-dda님 블로그](https://bba-dda.tistory.com/21)
### 선형 탐색 알고리즘 (Linear Search Algorithm)

맨 앞이나, 맨 뒤부터 순서대로 하나하나 찾아보는 알고리즘이다.

가장 단순하고 간단한 탐색 알고리즘이다.

맨 끝부터 하나하나 원하는 값을 찾아본다.
원하는 값을 찾으면, 탐색을 종료한다.

#### 예시
5를 찾을 때, 맨 왼쪽에 있는 1부터 시작해서 하나씩 탐색한다.

![](https://velog.velcdn.com/images/kimbad1992/post/1414cb11-d39b-46d2-a385-4f9b7c41d7e1/image.png)
![](https://velog.velcdn.com/images/kimbad1992/post/288028c3-1952-4fac-929e-44e5a1f9a1a2/image.png)

#### 시간 복잡도
길이 n짜리의 리스트를 탐색할 때,

최선의 경우

* 리스트의 첫 번째 원소가 정답인 경우 : 1번

최악의 경우

 * 리스트의 맨 마지막 원소가 정답이거나, 리스트에 정답이 없을 때 : n번
 
따라서 O(n)의 시간복잡도를 가진다.


### 이진 탐색 알고리즘 (Binary Search Algorithm)

중간지점을 기준으로 데이터를 반씩 나눠서 탐색하는 알고리즘이다.

중간지점을 선택한 뒤, 중간지점을 기준으로 왼쪽 혹은 오른쪽 부분만 남긴다.
남긴 부분 중에서 다시 중간지점을 선택한 뒤, 왼쪽 혹은 오른쪽만 남긴다.
위 과정을 원하는 값을 찾을 때 까지 반복한다.

#### 예시
5를 찾는 경우,
![](https://velog.velcdn.com/images/kimbad1992/post/452c9fcf-4f9f-411b-841b-29303a438ff6/image.png)

#### 시간 복잡도
최선의 경우

* 리스트의 중간부분이 정답일 때 : 1번

최악의 경우

* 리스트에 정답이 없는 경우 : log2(n) 번

따라서 O(logn)의 시간 복잡도를 가진다

선형탐색은 n이 증가함에 따라 시간복잡도가 선형적으로 증가하지만, 이진탐색은 n이 증가해도 시간복잡도가 아주 천천히 증가한다.

보통 정렬된 리스트에는 이진탐색을 사용하고,

정렬되지 않은 리스트에는 선형탐색을 사용한다.