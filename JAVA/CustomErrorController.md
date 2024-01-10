```java
@Controller  
public class CustomErrorController implements ErrorController {  
  
    private final ErrorAttributes errorAttributes;  
  
    @Autowired  
    public CustomErrorController(ErrorAttributes errorAttributes) {  
        this.errorAttributes = errorAttributes;  
    }  
  
    @RequestMapping("/error")  
    public String error(WebRequest webRequest, Model model) {  
        ErrorAttributeOptions options = ErrorAttributeOptions  
                .of(ErrorAttributeOptions.Include.MESSAGE, ErrorAttributeOptions.Include.STACK_TRACE);  
        Map<String, Object> errorAttributes = this.errorAttributes.getErrorAttributes(webRequest, options);  
  
        // int status = (int) errorAttributes.get("status");  
  
        model.addAttribute("err", errorAttributes);  
        return "/error";  
    }  
}
```

`GlobalHandler`내에서 각 Exception Catch시 별개의 에러 페이지로 리다이렉트 시켜주는 방식을 사용했었는데, ErrorController를 통해 각 에러별 템플릿 리턴을 다르게 주거나(ex. return "/403" 등) 동일한 에러 페이지에서 처리하되 에러 정보를 표기함으로써 사용자에게 별개의 에러임을 전달할 수 있게 만든다.