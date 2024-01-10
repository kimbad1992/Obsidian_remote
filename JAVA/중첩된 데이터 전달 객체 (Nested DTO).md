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

이 Form 안에서 하나의 `Employee`에 대한 데이터를 입력하고,

```javascript
const form = document.querySelector('form');  
const formData = new FormData(form);  
  
common.callFetch('/employee/employeeRegister.do', formData)
```

이 formData를 POST로 전송하고

```java
@PostMapping("/employeeRegister.do")  
@ResponseBody  
public ResponseEntity<?> employeeRegister(@Valid @ModelAttribute Employee employee, BindingResult bindingResult) {  
    if (bindingResult.hasErrors()) {  
        return new ErrorResponse(bindingResult).build();  
    }  
  
    employeeService.employeeRegister(employee);  
    ...
}
```

하나의 `Employee` 객체로 받는다고 하자.
`@Valid` 어노테이션을 통해 유효성 검증을 진행한다.

```java
@Data  
public class Employee {  
    private Integer personId;  
  
    @NotBlank(message = "소속명은 필수 입력 사항입니다.")  
    private String affiliation;  
  
    @NotBlank(message = "이름은 필수 입력 사항입니다.")  
    private String name;  
    
    @ValidResidentNo(message = "잘못된 형식의 주민번호입니다.")  
    private ResidentNumber residentNo = new ResidentNumber();
    
    ...
}
```

