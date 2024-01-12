## 🚩 문제
><h4>문제설명</h4>
>
카카오에 입사한 신입 개발자 `네오`는 "카카오계정개발팀"에 배치되어, 카카오 서비스에 가입하는 유저들의 아이디를 생성하는 업무를 담당하게 되었습니다. "네오"에게 주어진 첫 업무는 새로 가입하는 유저들이 카카오 아이디 규칙에 맞지 않는 아이디를 입력했을 때, 입력된 아이디와 유사하면서 규칙에 맞는 아이디를 추천해주는 프로그램을 개발하는 것입니다.
다음은 카카오 아이디의 규칙입니다.
>
* 아이디의 길이는 3자 이상 15자 이하여야 합니다.
* 아이디는 알파벳 소문자, 숫자, 빼기(`-`), 밑줄(`_`), 마침표(`.`) 문자만 사용할 수 있습니다.
* 단, 마침표(`.`)는 처음과 끝에 사용할 수 없으며 또한 연속으로 사용할 수 없습니다.
>
"네오"는 다음과 같이 7단계의 순차적인 처리 과정을 통해 신규 유저가 입력한 아이디가 카카오 아이디 규칙에 맞는 지 검사하고 규칙에 맞지 않은 경우 규칙에 맞는 새로운 아이디를 추천해 주려고 합니다.
신규 유저가 입력한 아이디가 `new\_id` 라고 한다면,
>
----
>>1단계 new\_id의 모든 대문자를 대응되는 소문자로 치환합니다.
2단계 new\_id에서 알파벳 소문자, 숫자, 빼기(-), 밑줄(\_), 마침표(.)를 제외한 모든 문자를 제거합니다.
3단계 new\_id에서 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환합니다.
4단계 new\_id에서 마침표(.)가 처음이나 끝에 위치한다면 제거합니다.
5단계 new\_id가 빈 문자열이라면, new\_id에 "a"를 대입합니다.
6단계 new\_id의 길이가 16자 이상이면, new\_id의 첫 15개의 문자를 제외한 나머지 문자들을 모두 제거합니다.
     만약 제거 후 마침표(.)가 new\_id의 끝에 위치한다면 끝에 위치한 마침표(.) 문자를 제거합니다.
7단계 new\_id의 길이가 2자 이하라면, new\_id의 마지막 문자를 new\_id의 길이가 3이 될 때까지 반복해서 끝에 붙입니다.
>
----
>
예를 들어, new\_id 값이 "...!@BaT#*..y.abcdefghijklm" 라면, 위 7단계를 거치고 나면 new\_id는 아래와 같이 변경됩니다.
>
1단계 대문자 'B'와 'T'가 소문자 'b'와 't'로 바뀌었습니다.
`"...!@BaT#*..y.abcdefghijklm"` → `"...!@bat#*..y.abcdefghijklm"`
>
2단계 '!', '@', '#', '*' 문자가 제거되었습니다.
>
`"...!@bat#*..y.abcdefghijklm"` → `"...bat..y.abcdefghijklm"`
>
3단계 '...'와 '..' 가 '.'로 바뀌었습니다.
`"...bat..y.abcdefghijklm"` → `".bat.y.abcdefghijklm"`
>
4단계 아이디의 처음에 위치한 '.'가 제거되었습니다.
`".bat.y.abcdefghijklm"` → `"bat.y.abcdefghijklm"`
>
5단계 아이디가 빈 문자열이 아니므로 변화가 없습니다.
`"bat.y.abcdefghijklm"` → `"bat.y.abcdefghijklm"`
>
6단계 아이디의 길이가 16자 이상이므로, 처음 15자를 제외한 나머지 문자들이 제거되었습니다.
`"bat.y.abcdefghijklm"` → `"bat.y.abcdefghi"`
>
7단계 아이디의 길이가 2자 이하가 아니므로 변화가 없습니다.
`"bat.y.abcdefghi"` → `"bat.y.abcdefghi"`
>
따라서 신규 유저가 입력한 new\_id가 "...!@BaT#*..y.abcdefghijklm"일 때, 네오의 프로그램이 추천하는 새로운 아이디는 "bat.y.abcdefghi" 입니다.
>
>
><h4>제한사항</h4>
>
new\_id는 길이 1 이상 1,000 이하인 문자열입니다.
new\_id는 알파벳 대문자, 알파벳 소문자, 숫자, 특수문자로 구성되어 있습니다.
new\_id에 나타날 수 있는 특수문자는 `-_.~!@#$%^&*()=+[{]}:?,<>/` 로 한정됩니다.
>
----
><h4>입출력 예</h4>
>
|**no**|**new_id**|**result**|
|---|---|---|
|예1|"...!@BaT#*..y.abcdefghijklm"|"bat.y.abcdefghi"|
|예2|"z-+.^."|"z--"|
|예3|"=.="|	"aaa"|
|예4|"123_.def"|"123_.def"|
|예5|"abcdefghijklmn.p"|	"abcdefghijklmn"|
>
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

 * 정규식 생성 사이트가 필요하겠구나. 
 <br>
 <br>
 
## 📝 코드 작성

```java
class Solution {
    public String solution(String new_id) {
        String id = new_id;
        String test = "";
        // 1단계 - 대문자를 소문자로
        id = id.toLowerCase();
        // 2단계 소문자, 숫자, -, _, . 제외한 나머지 제거
        id = id.replaceAll("[^-_.a-z0-9]","");
        // 3단계 마침표(.) 2번 이상이면 1개로 변경
        id = id.replaceAll("[.]{2,}",".");
        // 4단계 마침표가 처음이나 끝이면 제거
        id = id.replaceAll("^[.]|[.]$", "");
        // 5단계 new_id가 공백이면 a를 대입
        if (id.length() == 0) {
            id = "a";
        }
        // 6단계 16자 이상이면 15자로,
        // 15자로 변경 후 마지막 글자가 마침표(.)면 마침표 제거
        if (id.length() >= 16) {
            id = id.substring(0, 15);
            id = id.replaceAll("[.]$", "");
        }
        // 7단계 new_id가 2자 이하면 마지막 문자를 길이 3까지 복사
        if (id.length() <= 2) {
            for (int i=id.length(); i<3; i++) {
                id += id.charAt(id.length()-1);
            }
        }
        return id;
    }
}
````
정규식을 활용하라고 만든 문제 같은데, 정규식 만드는것도 참 복잡하고 어렵다.

## 🔍 정규식 만드는데 참조한 사이트
 * https://regexr.com/
 * https://regex101.com/

<br>
<br>

## ✔️ 다른 사람의 코드
```java
class Solution {
    public String solution(String new_id) {

        String s = new KAKAOID(new_id)
                .replaceToLowerCase()
                .filter()
                .toSingleDot()
                .noStartEndDot()
                .noBlank()
                .noGreaterThan16()
                .noLessThan2()
                .getResult();


        return s;
    }

    private static class KAKAOID {
        private String s;

        KAKAOID(String s) {
            this.s = s;
        }

        private KAKAOID replaceToLowerCase() {
            s = s.toLowerCase();
            return this;
        }

        private KAKAOID filter() {
            s = s.replaceAll("[^a-z0-9._-]", "");
            return this;
        }

        private KAKAOID toSingleDot() {
            s = s.replaceAll("[.]{2,}", ".");
            return this;
        }

        private KAKAOID noStartEndDot() {
            s = s.replaceAll("^[.]|[.]$", "");
            return this;
        }

        private KAKAOID noBlank() {
            s = s.isEmpty() ? "a" : s;
            return this;
        }

        private KAKAOID noGreaterThan16() {
            if (s.length() >= 16) {
                s = s.substring(0, 15);
            }
            s = s.replaceAll("[.]$", "");
            return this;
        }

        private KAKAOID noLessThan2() {
            StringBuilder sBuilder = new StringBuilder(s);
            while (sBuilder.length() <= 2) {
                sBuilder.append(sBuilder.charAt(sBuilder.length() - 1));
            }
            s = sBuilder.toString();
            return this;
        }

        private String getResult() {
            return s;
        }
    }
}


```
객체지향적으로 푼 사람이 있었다.
실무에서는 이런 형태로 사용하는게 좋을까?
