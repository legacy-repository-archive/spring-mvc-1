HTTP 요청 파라미터 - @ModelAttribute
=======================================
실제 개발을 하면 **요청 파라미터를 받아서 필요한 객체를 만들고 그 객체에 값을 넣어주어야 한다.**        
**스프링은 이 과정을 완전히 자동화해주는 @ModelAttribute 기능을 제공한다.**       

**파라미터를 바인딩 받을 객체**   
```java
@Data
public class HelloData {
    private String username;
    private int age;
}
```  
**롬복 @Data**   
`@Getter` , `@Setter` , `@ToString` , `@EqualsAndHashCode` , `@RequiredArgsConstructor` 자동 적용  

## @ModelAttribute 적용 - modelAttributeV1
```java
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
    log.info("username={}, age={}", helloData.getUsername(),
    helloData.getAge());
    return "ok";
}
```
스프링MVC는 `@ModelAttribute`가 있으면 **요청 파라미터에 알맞는 객체를 생성한다.**      
단, `주입`을 기반으로 동작하기에 **생성자 or setter가 필수로 존재해야한다.**              
   
**동작 과정**
1. 요청 파라미터의 이름으로 HelloData 객체의 프로퍼티를 찾는다.   
2. 해당 프로퍼티의 생성자 or setter를 호출해서 파라미터의 값을 입력(바인딩) 한다.
        
**바인딩 오류**   
age=abc 처럼 숫자가 들어가야 할 곳에 문자를 넣으면 BindException 이 발생한다.    
이런 바인딩 오류를 처리하는 방법은 검증 부분에서 다룬다.
   
**참고**   
`model.addAttribute(helloData)` 코드도 함께 자동 적용된다.        
즉, 자동으로 모델을 등록한다.      
   
## @ModelAttribute 생략 - modelAttributeV2
/**
 * @ModelAttribute 생략 가능
 * String, int 같은 단순 타입 = @RequestParam
 * argument resolver 로 지정해둔 타입 외 = @ModelAttribute
 */
@ResponseBody
@RequestMapping("/model-attribute-v2")
public String modelAttributeV2(HelloData helloData) {
 log.info("username={}, age={}", helloData.getUsername(),
helloData.getAge());
 return "ok";
}
@ModelAttribute 는 생략할 수 있다.
그런데 @RequestParam 도 생략할 수 있으니 혼란이 발생할 수 있다.
스프링은 해당 생략시 다음과 같은 규칙을 적용한다.
String , int , Integer 같은 단순 타입 = @RequestParam
나머지 = @ModelAttribute (argument resolver 로 지정해둔 타입 외)
> 참고
> argument resolver는 뒤에서 학습한다.
