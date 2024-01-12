## 🚩 문제
><h4>문제설명</h4>
>
카카오톡 오픈채팅방에서는 친구가 아닌 사람들과 대화를 할 수 있는데, 본래 닉네임이 아닌 가상의 닉네임을 사용하여 채팅방에 들어갈 수 있다.
>
신입사원인 김크루는 카카오톡 오픈 채팅방을 개설한 사람을 위해, 다양한 사람들이 들어오고, 나가는 것을 지켜볼 수 있는 관리자창을 만들기로 했다. 채팅방에 누군가 들어오면 다음 메시지가 출력된다.
>
"[닉네임]님이 들어왔습니다."
>
채팅방에서 누군가 나가면 다음 메시지가 출력된다.
>
"[닉네임]님이 나갔습니다."
>
채팅방에서 닉네임을 변경하는 방법은 다음과 같이 두 가지이다.
>
* 채팅방을 나간 후, 새로운 닉네임으로 다시 들어간다.
* 채팅방에서 닉네임을 변경한다
>
닉네임을 변경할 때는 기존에 채팅방에 출력되어 있던 메시지의 닉네임도 전부 변경된다.
>
예를 들어, 채팅방에 "Muzi"와 "Prodo"라는 닉네임을 사용하는 사람이 순서대로 들어오면 채팅방에는 다음과 같이 메시지가 출력된다.
>
"Muzi님이 들어왔습니다."
"Prodo님이 들어왔습니다."
>
채팅방에 있던 사람이 나가면 채팅방에는 다음과 같이 메시지가 남는다.
>
"Muzi님이 들어왔습니다."
"Prodo님이 들어왔습니다."
"Muzi님이 나갔습니다."
>
Muzi가 나간후 다시 들어올 때, Prodo 라는 닉네임으로 들어올 경우 기존에 채팅방에 남아있던 Muzi도 Prodo로 다음과 같이 변경된다.
>
"Prodo님이 들어왔습니다."
"Prodo님이 들어왔습니다."
"Prodo님이 나갔습니다."
"Prodo님이 들어왔습니다."
>
채팅방은 중복 닉네임을 허용하기 때문에, 현재 채팅방에는 Prodo라는 닉네임을 사용하는 사람이 두 명이 있다. 이제, 채팅방에 두 번째로 들어왔던 Prodo가 Ryan으로 닉네임을 변경하면 채팅방 메시지는 다음과 같이 변경된다.
>
"Prodo님이 들어왔습니다."
"Ryan님이 들어왔습니다."
"Prodo님이 나갔습니다."
"Prodo님이 들어왔습니다."
>
채팅방에 들어오고 나가거나, 닉네임을 변경한 기록이 담긴 문자열 배열 record가 매개변수로 주어질 때, 모든 기록이 처리된 후, 최종적으로 방을 개설한 사람이 보게 되는 메시지를 문자열 배열 형태로 return 하도록 solution 함수를 완성하라.
>
---
><h4>제한 사항</h4>
>
* record는 다음과 같은 문자열이 담긴 배열이며, 길이는 1 이상 100,000 이하이다.
* 다음은 record에 담긴 문자열에 대한 설명이다.
  - 모든 유저는 [유저 아이디]로 구분한다.
  - [유저 아이디] 사용자가 [닉네임]으로 채팅방에 입장 - "Enter [유저 아이디] [닉네임]" (ex. "Enter uid1234 Muzi")
  - [유저 아이디] 사용자가 채팅방에서 퇴장 - "Leave [유저 아이디]" (ex. "Leave uid1234")
  - [유저 아이디] 사용자가 닉네임을 [닉네임]으로 변경 - "Change [유저 아이디] [닉네임]" (ex. "Change uid1234 Muzi")
  - 첫 단어는 Enter, Leave, Change 중 하나이다.
  - 각 단어는 공백으로 구분되어 있으며, 알파벳 대문자, 소문자, 숫자로만 이루어져있다.
  - 유저 아이디와 닉네임은 알파벳 대문자, 소문자를 구별한다.
  - 유저 아이디와 닉네임의 길이는 1 이상 10 이하이다.
  - 채팅방에서 나간 유저가 닉네임을 변경하는 등 잘못 된 입력은 주어지지 않는다.
>  
----
><h4>입출력 예</h4>
>
|**record**|**result**|
|---|---|---|
|["Enter uid1234 Muzi", "Enter uid4567 Prodo","Leave uid1234","Enter uid1234 Prodo","Change uid4567 Ryan"]|["Prodo님이 들어왔습니다.", "Ryan님이 들어왔습니다.", "Prodo님이 나갔습니다.", "Prodo님이 들어왔습니다."]|
>_출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges_

<br>
<br>

## 🌌 생각

* 문자열을 `split()` 처리
* 닉네임은 최종적으로 바뀐 상태 한번만 처리하면 된다.
* `UID`는 각 유저에 대한 고유한 값이므로 `Key=UID`, `Value=닉네임`인 HashMap을 사용하면 될 것 같다.


 <br>
 <br>


## 📝 코드 작성
```java
import java.util.*;

class Solution {
    public String[] solution(String[] record) {
        List<String> list = new ArrayList<String>();
        Map<String, String> info = inputData(record);
        
        for (String dat : record) {
            String acc = dat.split(" ")[0];
            String uid = dat.split(" ")[1];
            
            switch (acc) {
                case "Enter" : list.add(info.get(uid)+"님이 들어왔습니다."); break;
                case "Leave" : list.add(info.get(uid)+"님이 나갔습니다."); break;
            }
        }
        
        return list.toArray(new String[list.size()]);
    }
    
    public Map<String, String> inputData(String[] record) {
        Map<String, String> info = new HashMap<String, String>();
        
        for (String dat : record) {
            if (dat.split(" ").length != 2) {f
                String uid = dat.split(" ")[1];
                String nickName = dat.split(" ")[2];
                info.put(uid, nickName);
            }
        }
        
        return info;
    }
}
````
처음 풀어본 Level 2짜리 문제였는데, 그 중에서도 쉬운 문제인 것 같다.
다만 문제가 Level 2인것을 보면, 요구하는 형태나 사용해야하는 자료구조 같은게 따로 있는게 아닐까 싶은데, 아직 배움이 짧고 아는게 없다보니 늘상 만들던 형태로 작업하게 된다.
다른 사람의 코드를 많이 봐야한다.





<br>
<br>

## ✔️ 다른 사람의 코드

```java
import java.util.ArrayList;
import java.util.HashMap;

class Solution {
    private static final String ENTER_FORMAT = "%s님이 들어왔습니다.";
    private static final String LEAVE_FORMAT = "%s님이 나갔습니다.";

    private HashMap<String, UserInfo> userMap = new HashMap<>();

    private class UserInfo {
        public String userId;
        public String nickName;

        public UserInfo(String userId, String nickName) {
            this.userId = userId;
            this.nickName = nickName;
        }

    }

    private class Command {
        public char command;
        public String userId;

        public Command(char command, String userName) {
            this.command = command;
            this.userId = userName;
        }
    }


    public String[] solution(String[] records) {
        ArrayList<Command> commandList = new ArrayList<>();

        for (String record : records) {
            String[] split = record.split(" ");
            String command = split[0];
            String userId = split[1];
            String nickName = null;

            switch(command.charAt(0)) {
                case 'E': // Enter
                    nickName = split[2];
                    if(userMap.containsKey(userId) == false) {
                        userMap.put(userId, new UserInfo(userId, nickName));
                    } else {
                        userMap.get(userId).nickName = nickName;
                    }

                    commandList.add(new Command(command.charAt(0), userId));
                    break;
                case 'L': // Leave
                    commandList.add(new Command(command.charAt(0), userId));
                    break;
                case 'C': // Change
                    nickName = split[2];
                    userMap.get(userId).nickName = nickName;
                    break;
            }
        }

        return commandList.stream()
                .map(cmd -> String.format( cmd.command == 'E' ? ENTER_FORMAT : LEAVE_FORMAT , userMap.get(cmd.userId).nickName))
                .toArray(ary -> new String[commandList.size()]);
    }
}

```
아직 초보라 잘은 모르겠지만, 왠지 자바 숙련도가 높아 보이는 코드
객체 지향적이고, 마지막 return을 `stream()`으로 한줄에 처리한 것도 좋다.