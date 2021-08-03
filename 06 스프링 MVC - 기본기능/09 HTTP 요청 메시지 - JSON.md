HTTP 요청 메시지 - JSON 
========================   
```json
{"username":"hello", "age":20}
content-type: application/json
```

# 기본 
> 기존 서블릿에서 사용했던 방식과 비슷하게 시작해보자.

```java
@Slf4j
@Controller
public class RequestBodyJsonController {
    private ObjectMapper objectMapper = new ObjectMapper();
    
    @PostMapping("/request-body-json-v1")
    public void requestBodyJsonV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("messageBody={}", messageBody);
        
        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        
        response.getWriter().write("ok");
    }
}
```
HttpServletRequest를 사용해서 직접 HTTP 메시지 바디에서 데이터를 읽어와서, 문자로 변환한다.    
문자로 된 JSON 데이터를 Jackson 라이브러리인 objectMapper 를 사용해서 자바 객체로 변환한다.

# @RequestBody 문자 변환
```java
@ResponseBody
@PostMapping("/request-body-json-v2")
public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {
    HelloData data = objectMapper.readValue(messageBody, HelloData.class);
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return "ok";
}
```
`@RequestBody`는 문자로 된 JSON 데이터인 messageBody 를 objectMapper 를 통해서 자바 객체로 변환한다.    
위 예시에서는 문자로 된 JSON 데이터를 별다른 컨버터/매핑없이 String으로만 변환했다.          
      
**@RequestBody**     
* HttpMessageConverter 사용 -> StringHttpMessageConverter 적용     
      
**@ResponseBody**
* 메시지 바디 정보 직접 반환(view 조회X)    
* HttpMessageConverter 사용 -> StringHttpMessageConverter 적용    
       
# @RequestBody 객체 변환
```java
@ResponseBody
@PostMapping("/request-body-json-v3")
public String requestBodyJsonV3(@RequestBody HelloData data) {
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return "ok";
}
```
  
**@RequestBody**           
* @RequestBody 에 직접 만든 객체를 지정할 수도 있다.(getter/setter 역직렬화로 동작 -> 문자열에서 객체 생성하는 작업)               
* @RequestBody는 생략 불가능하다. `@ModelAttribute`가 적용되어 버리기 때문이다.           
* HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (contenttype: application/json)        
   
HttpEntity, @RequestBody 를 사용하면      
`HTTP 메시지 컨버터`가 **HTTP 메시지 바디의 내용을 우리가 원하는 문자나 객체 등으로 변환해준다.**        
`HTTP 메시지 컨버터`는 **문자열 뿐만 아니라 JSON도 객체로 변환해준다.(역직렬화 방식으로)**        
`역직렬화 방식`은 **기본 생성자랑 getter/setter 둘 중 하나가 필수적으로 존재해야한다.**      
     
**@RequestBody는 생략 불가능**             
파라미터에 `@ModelAttribute` , `@RequestParam` 생략시 다음과 같은 규칙을 적용한다.     
     
* **@RequestParam :** String , int , Integer 같은 단순 타입   
* **@ModelAttribute :** 나머지 (argument resolver 로 지정해둔 타입 외)    
    
따라서 `@RequestBody`를 생략하면 HTTP 메시지 바디가 아니라 요청 파라미터를 처리하게 된다.       
즉, `@RequestBody`를 생략하면 `@ModelAttribute`나 `@RequestParam`가 적용되어버린다.        
       
**주의**   
객체 변환시에는 HTTP 요청시에 content-type이 application/json인지 확인해야 한다.     
그래야 JSON을 처리할 수 있는 HTTP 메시지 컨버터가 실행된다.            
물론 앞서 배운 것과 같이 HttpEntity를 사용해도 된다.            
  
# HttpEntity
```java
@ResponseBody
@PostMapping("/request-body-json-v4")
public String requestBodyJsonV4(HttpEntity<HelloData> httpEntity) {
 HelloData data = httpEntity.getBody();
 log.info("username={}, age={}", data.getUsername(), data.getAge());
 return "ok";
}
```   
`HttpEntity`를 이용해서 요청 파라미터를 처리할 수 있다.        
이전에 언급했듯이 Http 바디는 물론 헤더에 관한 처리도 할 수 있다.      
   
# @RequestBody  
```java
@ResponseBody
@PostMapping("/request-body-json-v5")
public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return data;
}
```      
위 코드는 객체를 바로 반환하는 형태로 동작하고 있다.   
  
**@ResponseBody**     
응답의 경우에도 @ResponseBody 를 사용하면 해당 객체를 HTTP 메시지 바디에 직접 넣어줄 수 있다.    
물론 이 경우에도 HttpEntity 를 사용해도 된다.   
   
**@RequestBody 요청**
* @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
* HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (contenttype: application/json)
    
**@ResponseBody 응답**      
* - 메시지 바디 정보 직접 반환(view 조회X)   
* - HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter 적용    
 
