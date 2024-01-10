특정 회원가입 Form에서 주민등록번호를 입력 받는다고 하자.

![[Pasted image 20240110171530.png]]

대충 이러한 형태에서 입력을 받게 되면, 이 주민등록번호를 앞자리 뒷자리 변수를 별도로 할당하여(2개로 분리된) 서버로 전송하거나, 아니면 이 두 값 사이에 하이픈을 넣어 하나의 문자열 형태로 전송할 것이다.

나는 조금 다른 방법을 쓰고 싶다.

```html
<form th:action="@{/employee/register}" method="post">

...

<div class="form-group">  
    <label>주민번호<sup class="text-danger">*</sup></label>  
    <div class="d-flex">  
        <input type="text" class="form-control" id="residentNo.frontNumber" name="residentNo.frontNumber" maxlength="6" required style="width: 40%;">  
        <span class="mx-2">-</span>  
        <input type="password" class="form-control" id="residentNo.rearNumber" name="residentNo.rearNumber" maxlength="7" required style="width: 40%;">  
    </div>  
</div>

...

</form>
```

이 Form 안에서 하나의 Employee에 대한 데이터를 입력하고,

```javascript
const form = document.querySelector('form');  
const formData = new FormData(form);  
  
common.callFetch('/employee/employeeRegister.do', formData)
```
