HTTP 요청 - 기본, 헤더 조회
=============================  
어노테이션 기반의 스프링 컨트롤러는 다양한 파라미터를 지원한다.    
이번에는 HTTP 헤더 정보를 조회하는 방법을 알아보고자 한다.         
   
```java
@Slf4j
@RestController
public class RequestHeaderController {
    @RequestMapping("/headers")
    public String headers(
            HttpServletRequest request,
            HttpServletResponse response,
            HttpMethod httpMethod,
            Locale locale,
            @RequestHeader MultiValueMap<String, String> headerMap,
            @RequestHeader("host") String host,
            @CookieValue(value = "myCookie", required = false) String cookie) {
        
        log.info("request={}", request);
        log.info("response={}", response);
        log.info("httpMethod={}", httpMethod);
        log.info("locale={}", locale);
        log.info("headerMap={}", headerMap);
        log.info("header host={}", host);
        log.info("myCookie={}", cookie);
        
        return "ok";
    }
}
```  
**HttpServletRequest**   
Servlet에서 제공하는 HttpServletRequest 객체   

**HttpServletResponse**   
Servlet에서 제공하는 HttpServletResponse 객체   

**HttpMethod**    
`org.springframework.http.HttpMethod`   
HTTP 메서드(GET/POST 등등)를 조회할 때 사용하는 객체   
   
**Locale**  
Locale(지역) 정보를 조회하고 주로 국제화할 때 사용한다.        
   
**@RequestHeader MultiValueMap<String, String> headerMap**
모든 HTTP 헤더를 Key와 다중 Value인, MultiValueMap 형식으로 조회한다.         

**@RequestHeader("host") String host**   
특정 HTTP 헤더를 조회한다.
* **속성**
 * 필수 값 여부: required
 * 기본 값 속성: defaultValue

**@CookieValue(value = "myCookie", required = false) String cookie**       
특정 쿠키를 조회한다.  
* **속성**
    * 필수 값 여부: required
    * 기본 값: defaultValue

* MultiValueMap
MAP과 유사한데, 하나의 키에 여러 값을 받을 수 있다.
HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용한다.
keyA=value1&keyA=value2
MultiValueMap<String, String> map = new LinkedMultiValueMap();
map.add("keyA", "value1");
map.add("keyA", "value2");
//[value1,value2]
List<String> values = map.get("keyA");
@Slf4j
다음 코드를 자동으로 생성해서 로그를 선언해준다. 개발자는 편리하게 log 라고 사용하면 된다.
private static final org.slf4j.Logger log =
org.slf4j.LoggerFactory.getLogger(RequestHeaderController.class);
> 참고
> @Conroller 의 사용 가능한 파라미터 목록은 다음 공식 메뉴얼에서 확인할 수 있다.
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-annarguments
> 참고
> @Conroller 의 사용 가능한 응답 값 목록은 다음 공식 메뉴얼에서 확인할 수 있다.
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-annreturn-types
