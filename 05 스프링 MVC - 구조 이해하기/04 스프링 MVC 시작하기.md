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


* **@Controller** 
    * 스프링이 자동으로 스프링 빈으로 등록한다.     
      (내부에 @Component 애노테이션이 있어서 컴포넌트 스캔의 대상이 됨)    
    * 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식한다.    
* **@RequestMapping**   
    * 요청 정보를 매핑한다. 해당 URL이 호출되면 이 메서드가 호출된다.        
    * 애노테이션을 기반으로 동작하기 때문에, 메서드의 이름은 임의로 지으면 된다.  
* **ModelAndView :**     
    * 모델과 뷰 정보를 담아서 반환하면 된다.   








