HTTP 요청 파라미터 - @ModelAttribute
=======================================

실제 개발을 하면 요청 파라미터를 받아서 필요한 객체를 만들고 그 객체에 값을 넣어주어야 한다. 보통
다음과 같이 코드를 작성할 것이다.
@RequestParam String username;
@RequestParam int age;
HelloData data = new HelloData();
data.setUsername(username);
data.setAge(age);
스프링은 이 과정을 완전히 자동화해주는 @ModelAttribute 기능을 제공한다.
먼저 요청 파라미터를 바인딩 받을 객체를 만들자.
HelloData
package hello.springmvc.basic;
import lombok.Data;
@Data
public class HelloData {
 private String username;
 private int age;
}
롬복 @Data
@Getter , @Setter , @ToString , @EqualsAndHashCode , @RequiredArgsConstructor 를
자동으로 적용해준다.
@ModelAttribute 적용 - modelAttributeV1
/**
 * @ModelAttribute 사용
 * 참고: model.addAttribute(helloData) 코드도 함께 자동 적용됨, 뒤에 model을 설명할 때
자세히 설명
 */
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
 log.info("username={}, age={}", helloData.getUsername(),
helloData.getAge());
 return "ok";
}
마치 마법처럼 HelloData 객체가 생성되고, 요청 파라미터의 값도 모두 들어가 있다.
스프링MVC는 @ModelAttribute 가 있으면 다음을 실행한다.
HelloData 객체를 생성한다.
요청 파라미터의 이름으로 HelloData 객체의 프로퍼티를 찾는다. 그리고 해당 프로퍼티의 setter를
호출해서 파라미터의 값을 입력(바인딩) 한다.
예) 파라미터 이름이 username 이면 setUsername() 메서드를 찾아서 호출하면서 값을 입력한다.
프로퍼티
객체에 getUsername() , setUsername() 메서드가 있으면, 이 객체는 username 이라는 프로퍼티를
가지고 있다.
username 프로퍼티의 값을 변경하면 setUsername() 이 호출되고, 조회하면 getUsername() 이
호출된다.
class HelloData {
 getUsername();
 setUsername();
}
바인딩 오류
age=abc 처럼 숫자가 들어가야 할 곳에 문자를 넣으면 BindException 이 발생한다. 이런 바인딩 오류를
처리하는 방법은 검증 부분에서 다룬다.
@ModelAttribute 생략 - modelAttributeV2
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
