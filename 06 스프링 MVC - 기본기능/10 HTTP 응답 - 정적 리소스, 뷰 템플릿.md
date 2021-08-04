HTTP 응답 - 정적 리소스, 뷰 템플릿  
==================================== 
스프링(서버)에서 응답 데이터를 만드는 방법은 크게 3가지이다.

* **정적 리소스**  
    웹 브라우저에 정적인 HTML, css, js을 제공할 때는, 정적 리소스를 사용한다.
* **뷰 템플릿 사용**    
    웹 브라우저에 동적인 HTML을 제공할 때는 뷰 템플릿을 사용한다.
* **HTTP 메시지 사용**   
    HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로,   
    HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.

# 정적 리소스  
> 정적 리소스는 해당 파일을 변경 없이 그대로 서비스하는 것     
   
스프링 부트는 클래스패스의 다음 디렉토리에 있는 정적 리소스를 제공한다.   
            
* **src/main/resources 하위** : `/static` , `/public` , `/resources` , `/META-INF/resources`  
          
`src/main/resources`는 리소스를 보관하는 곳이고, **또 클래스패스의 시작 경로이다.**      
따라서 **`src/main/resources`에 리소스를 넣어두면 스프링 부트가 정적 리소스로 서비스를 제공한다.**      
      
**정적 리소스 반환**   
`src/main/resources/static` 경로에 파일이 들어있으면 정적 리소스를 반환해준다.     
즉, `src/main/resources/static/basic/hello-form.html`을 입력하면 `hello-form.html`를 바로 반환한다.            
사실 정적 리소스 반환이란, **Controller 를 통해 매핑되지 않은 경로로 데이터 요청시 바로 리소스를 반환하는 것을 말한다.**           

# 뷰 템플릿   
> 뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어서 전달한다.      
    
스프링 부트는 기본 뷰 템플릿 경로를 제공한다.        
**뷰 템플릿 경로 :** `src/main/resources/templates`         
   
**src/main/resources/templates/response/hello.html**    
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <p th:text="${data}">empty</p>
</body>
</html>
```
   
**ResponseViewController - 뷰 템플릿을 호출하는 컨트롤러**
```java
@Controller
public class ResponseViewController {
     
     @RequestMapping("/response-view-v1")
     public ModelAndView responseViewV1() {
         ModelAndView mav = new ModelAndView("response/hello").addObject("data", "hello!");
         return mav;
     }
     
     @RequestMapping("/response-view-v2")
     public String responseViewV2(Model model) {
         model.addAttribute("data", "hello!!");
         return "response/hello";
     }
     
     @RequestMapping("/response/hello")
     public void responseViewV3(Model model) {
         model.addAttribute("data", "hello!!");
     }
}
```        
**String을 반환하는 경우 - View or HTTP 메시지**         
* `@ResponseBody`가 없으면 `response/hello`로 뷰 리졸버가 실행되어서 뷰를 찾고, 렌더링 한다.      
* `@ResponseBody`가 있으면 **뷰 리졸버를 실행하지 않고, HTTP 메시지 바디에 직접 `response/hello`라는 문자가 입력된다.**      
        
**Void를 반환하는 경우**      
* `@Controller`를 사용하고 `HttpServletResponse` , `OutputStream(Writer)`과 같은     
  **HTTP 메시지 바디를 처리하는 파라미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 사용한다.**      
  * **요청 URL:** `/response/hello`     
  * **실행 URL:** `templates/response/hello.html`
* 참고로 이 방식은 명시성이 너무 떨어지고 딱 맞는 경우도 많이 없어서 권장하지 않는다.        
    
# HTTP 메시지      
`@ResponseBody`, `HttpEntity`를 사용하면       
뷰 템플릿을 사용이 아닌, **HTTP 메시지 바디에 직접 응답 데이터를 출력할 수 있다.**        

**Thymeleaf 스프링 부트 설정**
```gradle
// build.gradle  
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```      
스프링 부트가 자동으로 ThymeleafViewResolver 와 필요한 스프링 빈들을 등록한다.      
그리고 **properties 설정**을 하는데 아래 설정은 기본 값으로 변경이 필요할 때 바꾸면 된다.       
    
```properties
# application.properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```  
      
**참고**       
스프링 부트의 타임리프 관련 추가 설정은 다음 [공식 사이트](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-applicationproperties.html#common-application-properties-templating)를 참고하자. (페이지 안에서 thymeleaf 검색)
    
