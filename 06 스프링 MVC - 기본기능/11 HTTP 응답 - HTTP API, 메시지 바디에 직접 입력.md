HTTP 응답 - HTTP API, 메시지 바디에 직접 입력  
============================================
`HTTP API`를 제공하는 경우에는 **HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.**      
      
```java
@Slf4j
@Controller
//@RestController
public class ResponseBodyController {
 
     @ResponseBody
     @GetMapping("/response-body-string-v3")
     public String responseBodyV3() {
         return "ok";
     }
 
     @GetMapping("/response-body-json-v1")
     public ResponseEntity<HelloData> responseBodyJsonV1() {
         HelloData helloData = new HelloData();
         helloData.setUsername("userA");
         helloData.setAge(20);
         return new ResponseEntity<>(helloData, HttpStatus.OK);
     }
 
     @ResponseStatus(HttpStatus.OK)
     @ResponseBody
     @GetMapping("/response-body-json-v2")
         public HelloData responseBodyJsonV2() {
         HelloData helloData = new HelloData();
         helloData.setUsername("userA");
         helloData.setAge(20);
         return helloData;
     }
}
```
## HttpServletResponse   
```java
@Slf4j
@Controller
public class ResponseBodyController {
 
     @GetMapping("/response-body-string-v1")
     public void responseBodyV1(HttpServletResponse response) throws IOException {
         response.getWriter().write("ok");
     }
}
```

`HttpServletResponse` 객체를 통해서 HTTP 메시지 바디에 직접 ok 응답 메시지를 전달한다.

## ResponseEntity(HttpEntity)   

```java
@Slf4j
@Controller
public class ResponseBodyController {
 
     @GetMapping("/response-body-string-v2")
     public ResponseEntity<String> responseBodyV2() {
         return new ResponseEntity<>("ok", HttpStatus.OK);
     }
}
```
HttpEntity는 HTTP 메시지의 헤더, 바디 정보를 가지고 있다.   
ResponseEntity는 HttpEntity 를 상속 받고 있으며 추가적으로 HTTP 응답 코드를 설정할 수 있다.
만약, HttpStatus.CREATED 로 변경하면 201 응답이 나가는 것을 확인할 수 있다.     

## @ResponseBody     
```java
@Slf4j
@Controller
public class ResponseBodyController {
 
     @ResponseBody
     @GetMapping("/response-body-string-v3")
     public String responseBodyV3() {
         return "ok";
     }
}
```
   
@ResponseBody 를 사용하면 view를 사용하지 않고,     
HTTP 메시지 컨버터를 통해서 HTTP 메시지를 직접 입력할 수 있다.   

## responseBodyJsonV1
ResponseEntity 를 반환한다. HTTP 메시지 컨버터를 통해서 JSON 형식으로 변환되어서 반환된다.

## responseBodyJsonV2
ResponseEntity 는 HTTP 응답 코드를 설정할 수 있는데, @ResponseBody 를 사용하면 이런 것을
설정하기 까다롭다.
@ResponseStatus(HttpStatus.OK) 애노테이션을 사용하면 응답 코드도 설정할 수 있다.
물론 애노테이션이기 때문에 응답 코드를 동적으로 변경할 수는 없다. 프로그램 조건에 따라서 동적으로
변경하려면 ResponseEntity 를 사용하면 된다.
@RestController
@Controller 대신에 @RestController 애노테이션을 사용하면, 해당 컨트롤러에 모두
@ResponseBody 가 적용되는 효과가 있다. 따라서 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에
직접 데이터를 입력한다. 이름 그대로 Rest API(HTTP API)를 만들 때 사용하는 컨트롤러이다.
참고로 @ResponseBody 는 클래스 레벨에 두면 전체에 메서드에 적용되는데, @RestController
에노테이션 안에 @ResponseBody 가 적용되어 있다.
