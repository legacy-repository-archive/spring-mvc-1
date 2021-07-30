스프링 MVC 
==============    
# 📗 스프링 MVC - 시작하기   
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
    
# 📘 스프링 MVC - 컨트롤러 통합        
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
  
# 📕 스프링 MVC - 실용적인 방식
스프링 MVC는 개발자가 편리하게 개발할 수 있도록 수 많은 편의 기능을 제공한다.       
특히 **핸들러에 정의된 메서드 파라미터 및 반환값을 유연하게 설정할 수 있도록 해준다.**                        
참고로 SpringMVC의 핸들러란, `@RequestMapping`이 정의된 메서드를 의미한다.(Servlet에서는 Controller 자체)        
    
## 📖 변경 전
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

## 📖 변경 후  
```java
@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {
    
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @GetMapping("/new-form")
    public String newForm() {
        return "new-form";
    }
    
    @PostMapping("/save")
    public String save(
            @RequestParam("username") String username,
            @RequestParam("age") int age, Model model) {
    
       Member member = new Member(username, age);
       memberRepository.save(member);
       model.addAttribute("member", member);
       return "save-result";
    }
    
    @GetMapping
    public String members(Model model) {
        List<Member> members = memberRepository.findAll();
        model.addAttribute("members", members);
        return "members";
    }
}
```
   
* **ModelAndView**      
    * 스프링이 제공해주는 ModelAndView 객체를 파라미터 받거나 반환값으로 설정할 수 있다.     
* **Model**      
    * 스프링이 제공해주는 Model 객체를 파라미터로 받을 수 있다.         
    * 주로 `addAttribute("key", value);`를 통해 값을 request 범위로 저장한다.
* **View**   
    *         
* **ViewName 을 String 타입으로 직접 반환**
    * 뷰의 논리 이름을 String 타입으로 직접 반환할 수 있다.

**@RequestParam 사용**     
    * HTTP 요청 파라미터를 **@RequestParam**으로 받을 수 있다.       
    * @RequestParam("username") 은 request.getParameter("username") 와 거의 같은 코드다.     
    * 물론 GET 쿼리 파라미터, POST Form 방식을 모두 지원한다.

* **@RequestMapping -> @GetMapping, @PostMapping**
@RequestMapping 은 URL만 매칭하는 것이 아니라, HTTP Method도 함께 구분할 수 있다.
예를 들어서 URL이 /new-form 이고, HTTP Method가 GET인 경우를 모두 만족하는 매핑을 하려면
다음과 같이 처리하면 된다.
@RequestMapping(value = "/new-form", method = RequestMethod.GET)
이것을 @GetMapping , @PostMapping 으로 더 편리하게 사용할 수 있다.
참고로 Get, Post, Put, Delete, Patch 모두 애노테이션이 준비되어 있다.
@GetMapping 코드를 열어서 @RequestMapping 애노테이션을 내부에 가지고 있는 모습을 확인하자









