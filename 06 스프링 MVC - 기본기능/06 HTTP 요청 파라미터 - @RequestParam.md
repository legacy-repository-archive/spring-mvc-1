HTTP 요청 파라미터 - @RequestParam
==================================
`@RequestParam`을 사용하면 요청 파라미터를 매우 편리하게 사용할 수 있다.         
`@RequestParam(value="")`에 파라미터 이름(key)을 넣으면 해당 데이터를 바인딩 할 수 있다.     
   
## 일반적인 사용법 
```java 
@ResponseBody
@RequestMapping("/request-param-v2")
public String requestParamV2(@RequestParam("username") String memberName, @RequestParam("age") int memberAge) {
    log.info("username={}, age={}", memberName, memberAge);
    return "ok";
}
```
* @RequestParam 사용 : 파라미터 이름으로 바인딩        
* @ResponseBody 추가 : View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력         
  
## 동일 이름 생략 전략  

```java
@ResponseBody
@RequestMapping("/request-param-v3")
public String requestParamV3(@RequestParam String username, @RequestParam int age) {
    log.info("username={}, age={}", username, age);
    return "ok";
}
```
HTTP 파라미터 이름이 변수 이름과 같으면      
`@RequestParam(name="xx")`에서 **name/value 속성을 생략한 `@RequestParam`만 기입 가능하다.**               
        
## 기본 타입 생략 전략 
```java
@ResponseBody
@RequestMapping("/request-param-v4")
public String requestParamV4(String username, int age) {
 log.info("username={}, age={}", username, age);
 return "ok";
}
```
HTTP 파라미터 이름이 변수 이름과 같고, 파라미터 타입이 String , int , Integer 등의 단순 타입이면          
**@RequestParam 도 생략 가능하다.(기본 컨버터가 지원해주는 타입이여서 그렇다.)**           
    
**주의**
* @RequestParam 애노테이션을 생략하면 스프링 MVC는 내부에서 required=false 를 적용한다.

**참고**
* 이렇게 애노테이션을 완전히 생략해도 되는데, 너무 없는 것도 약간 과하다는 주관적 생각이 있다.
* @RequestParam 이 있으면 명확하게 요청 파리미터에서 데이터를 읽는 다는 것을 알 수 있다.
    
## 파라미터 필수 여부 - requestParamRequired

```java 
@ResponseBody
@RequestMapping("/request-param-required")
public String requestParamRequired(@RequestParam(required = true) String username,
                                   @RequestParam(required = false) Integer age) {
    log.info("username={}, age={}", username, age);
    return "ok";
}
```
`@RequestParam`의 required 속성을 이용해 필수로 존재해야할 파라미터를 정할 수 있다.         
`@RequestParam`선언을 한다면 기본값은 `(required = true)`이며 없으면 400 예외가 발생시킨다.       

* 필수 : @RequestParam(required = true)     
* 선택 : @RequestParam(required = false)    

**주의 사항**
* `/request-param?username=`
    * 빈문자로 에러 없이 통과
* `/request-param 요청`, `@RequestParam(required = false) int age` 
    * required = false 임에도 값을 안 넣으면 에러가 발생한다.(500 예외 발생)    
    * null을 int에 입력하는 것은 불가능하기 때문이다.      
    * 따라서 null 을 받을 수 있는 Integer 로 변경하거나, 또는 다음에 나오는 defaultValue 사용해야 한다.     
      
## 기본 값 적용 - requestParamDefault
```java
@ResponseBody
@RequestMapping("/request-param-default")
public String requestParamDefault(@RequestParam(required = true, defaultValue = "guest") String username,
                                  @RequestParam(required = false, defaultValue = "-1") int age) {
 log.info("username={}, age={}", username, age);
 return "ok";
}
```
파라미터에 값이 없는 경우 defaultValue 를 사용하면 기본 값을 적용할 수 있다.      
이미 기본 값이 있기 때문에 required 는 사실상 의미가 없다.      
       
**참고**     
* `/request-param?username=`      
defaultValue는 빈 문자의 경우에도 적용이 된다.   
    
## 파라미터를 Map으로 조회하기 - requestParamMap
```java
@ResponseBody
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
    log.info("username={}, age={}", paramMap.get("username"),
    paramMap.get("age"));
    return "ok";
}
```
파라미터를 `Map`, `MultiValueMap`으로 조회할 수 있다.     
    
**@RequestParam Map, MultiValueMap**
* `Map(key=value)`
* `MultiValueMap(key=[value1, value2, ...] ex) (key=userIds, value=[id1, id2])`
    
파라미터의 값이 1개가 확실하다면 Map 을 사용해도 되지만, 그렇지 않다면 MultiValueMap 을 사용하자.      
       
