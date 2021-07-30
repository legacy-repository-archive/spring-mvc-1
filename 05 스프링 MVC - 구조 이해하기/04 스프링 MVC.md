스프링 MVC 
==============    
# 📗 스프링 MVC 시작하기   
## 📖 @RequestMapping      
   
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
```java
@Controller
public class SpringMemberSaveControllerV1 {
    
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @RequestMapping("/springmvc/v1/members/save")
    public ModelAndView process(HttpServletRequest request, HttpServletResponse response) {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        Member member = new Member(username, age);
        
        memberRepository.save(member);
        
        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("member", member);
        return mv;
    }
}
```

참고로, 스프링이 제공하는 ModelAndView 를 통해 Model 데이터를 추가할 때는    
addObject() 를 사용하면 된다. 이 데이터는 이후 뷰를 렌더링 할 때 사용된다.  
```java
mv.addObject("member", member)
```
    
# 📘 스프링 MVC 컨트롤러 통합        
RequestHandlerMapping은 `@RequestMapping`을 기준으로만 동작을 한다.           
`@RequestMapping`은 주로 메서드 단위에 적용되는데          
이를 활용하면 **하나의 컨트롤러 클래스에서 여러 `@RequestMapping` 메세드를 가질 수 있다.**        

```java
@Controller
@RequestMapping("/springmvc/v2/members")
public class SpringMemberControllerV2 {
    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/new-form")
    public ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    @RequestMapping("/save")
    public ModelAndView save(HttpServletRequest request, HttpServletResponse
            response) {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        Member member = new Member(username, age);
        memberRepository.save(member);
        ModelAndView mav = new ModelAndView("save-result");
        mav.addObject("member", member);
        return mav;
    }

    @RequestMapping
    public ModelAndView members() {
        List<Member> members = memberRepository.findAll();
        ModelAndView mav = new ModelAndView("members");
        mav.addObject("members", members);
        return mav;
    }
}
```
클래스 레벨에서도 `@RequestMapping("/springmvc/v2/members")`을 선언할 수 있는데         
이럴 경우 **클래스 레벨 매핑 경로**와 **메서드 레벨 매핑 경로**가 조합된다.      

**조합 결과**    
* 클래스 레벨 `@RequestMapping("/springmvc/v2/members")`
    * 메서드 레벨 `@RequestMapping("/new-form")` -> `/springmvc/v2/members/new-form` 
    * 메서드 레벨 `@RequestMapping("/save")` -> `/springmvc/v2/members/save` 
    * 메서드 레벨 `@RequestMapping` -> `/springmvc/v2/members`





