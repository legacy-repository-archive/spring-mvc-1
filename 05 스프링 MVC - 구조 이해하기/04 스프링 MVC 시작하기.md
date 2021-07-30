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
* **@Controller** 
    * 스프링이 자동으로 스프링 빈으로 등록한다.     
      (내부에 @Component 애노테이션이 있어서 컴포넌트 스캔의 대상이 됨)    
    * 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식한다.    
* **@RequestMapping**   
    * 요청 정보를 매핑한다.      
    * 해당 URL이 호출되면 이 메서드가 호출된다.           
    * 애노테이션을 기반으로 동작하기 때문에, 메서드의 이름은 임의로 지으면 된다.       
* **ModelAndView :**         
    * 모델과 뷰 정보를 담아서 반환하면 된다.      

**클래스에 `@Controller`** 를 붙이고 **메서드/클래스 레벨에 `@RequestMapping`** 을 선언하면 된다.                            
이 과정에서 더 이상 `FrontController`는 생성하지 않아도 된다.             

## 📖 RequestMappingHandlerMapping   

```java
    @Override
    protected boolean isHandler(Class<?> beanType) {
        return (AnnotatedElementUtils.hasAnnotation(beanType, Controller.class) || 
                AnnotatedElementUtils.hasAnnotation(beanType, RequestMapping.class));
    }
```

**RequestMappingHandlerMapping**의 `isHandler()`를 보면 재밌는점을 알 수 있는데            
**스프링 빈 중에서 `@RequestMapping` 또는 `@Controller`가 붙은 클래스의 매핑 정보를 인식한다.**   


**방법1**
```java
@Component   
@RequestMapping
public class SpringMemberFormControllerV1 {  

    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
    
}
```  
     
**방법2**   
```java
@RequestMapping
public class SpringMemberFormControllerV1 {  

    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
    
}
```
```java
@Configuration
public class TestConfiguration {
    
    @Bean
    TestController testController() {
        return new TestController();
    }
    
}
```
이론상 위와 같은 방법으로도 빈 등록후 Controller로써 사용은 가능하지만      
Best Practice는 아래와 같이 `@Controller`를 사용해서 클래스를 간편하게 만드는 것이다.   
   
**Best Pratice**    
```java
@Controller
public class SpringMemberFormControllerV1 {   

    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
    
}
```








