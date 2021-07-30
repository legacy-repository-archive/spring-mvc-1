# 스프링 MVC 시작하기   
## @RequestMapping      
   
* **RequestMappingHandlerMapping**        
* **RequestMappingHandlerAdapter**        
    
스프링 MVC에서 가장 우선순위가 높은 `핸들러 매핑`과 `핸들러 어댑터`는      
**`RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`** 이다.   
   
이들은, **@RequestMapping** 기반의 컨트롤러를 지원하는 핸들러 매핑과 어댑터로     
실무에서는 99.9% 이 방식의 컨트롤러를 사용한다.   
   
```java
@Controller
public class SpringMemberFormControllerV1 {   

    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
    
}
```      
Controller 클래스에 **@Controller**를 붙이고             
메서드/클래스 레벨에 **@RequestMapping**을 선언하면 된다.                          
이 과정에서 `FrontController`를 더 이상 생성하지 않아도 된다.         








