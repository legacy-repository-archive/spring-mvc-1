HTTP 요청 메시지 - JSON 
========================   

이번에는 HTTP API에서 주로 사용하는 JSON 데이터 형식을 조회해보자.
기존 서블릿에서 사용했던 방식과 비슷하게 시작해보자.
RequestBodyJsonController
package hello.springmvc.basic.request;
import com.fasterxml.jackson.databind.ObjectMapper;
import hello.springmvc.basic.HelloData;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.ResponseBody;
import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
/**
 * {"username":"hello", "age":20}
 * content-type: application/json
 */
@Slf4j
@Controller
public class RequestBodyJsonController {
 private ObjectMapper objectMapper = new ObjectMapper();
 @PostMapping("/request-body-json-v1")
 public void requestBodyJsonV1(HttpServletRequest request,
HttpServletResponse response) throws IOException {
 ServletInputStream inputStream = request.getInputStream();
 String messageBody = StreamUtils.copyToString(inputStream,
StandardCharsets.UTF_8);
 log.info("messageBody={}", messageBody);
 HelloData data = objectMapper.readValue(messageBody, HelloData.class);
 log.info("username={}, age={}", data.getUsername(), data.getAge());
 response.getWriter().write("ok");
 }
}
HttpServletRequest를 사용해서 직접 HTTP 메시지 바디에서 데이터를 읽어와서, 문자로 변환한다.
문자로 된 JSON 데이터를 Jackson 라이브러리인 objectMapper 를 사용해서 자바 객체로 변환한다.
Postman으로 테스트
POST http://localhost:8080/request-body-json-v1
raw, JSON, content-type: application/json
{"username":"hello", "age":20}
requestBodyJsonV2 - @RequestBody 문자 변환
/**
 * @RequestBody
 * HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 *
 * @ResponseBody
 * - 모든 메서드에 @ResponseBody 적용
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 */
@ResponseBody
@PostMapping("/request-body-json-v2")
public String requestBodyJsonV2(@RequestBody String messageBody) throws
IOException {
 HelloData data = objectMapper.readValue(messageBody, HelloData.class);
 log.info("username={}, age={}", data.getUsername(), data.getAge());
 return "ok";
}
이전에 학습했던 @RequestBody 를 사용해서 HTTP 메시지에서 데이터를 꺼내고 messageBody에
저장한다.
문자로 된 JSON 데이터인 messageBody 를 objectMapper 를 통해서 자바 객체로 변환한다.
문자로 변환하고 다시 json으로 변환하는 과정이 불편하다. @ModelAttribute처럼 한번에 객체로
변환할 수는 없을까?
requestBodyJsonV3 - @RequestBody 객체 변환
/**
 * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
 * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (contenttype: application/json)
 *
 */
@ResponseBody
@PostMapping("/request-body-json-v3")
public String requestBodyJsonV3(@RequestBody HelloData data) {
 log.info("username={}, age={}", data.getUsername(), data.getAge());
 return "ok";
}
@RequestBody 객체 파라미터
@RequestBody HelloData data
@RequestBody 에 직접 만든 객체를 지정할 수 있다.
HttpEntity , @RequestBody 를 사용하면 HTTP 메시지 컨버터가 HTTP 메시지 바디의 내용을 우리가
원하는 문자나 객체 등으로 변환해준다.
HTTP 메시지 컨버터는 문자 뿐만 아니라 JSON도 객체로 변환해주는데, 우리가 방금 V2에서 했던 작업을
대신 처리해준다.
자세한 내용은 뒤에 HTTP 메시지 컨버터에서 다룬다.
@RequestBody는 생략 불가능
@ModelAttribute 에서 학습한 내용을 떠올려보자.
스프링은 @ModelAttribute , @RequestParam 해당 생략시 다음과 같은 규칙을 적용한다.
String , int , Integer 같은 단순 타입 = @RequestParam
나머지 = @ModelAttribute (argument resolver 로 지정해둔 타입 외)
따라서 이 경우 HelloData에 @RequestBody 를 생략하면 @ModelAttribute 가 적용되어버린다.
HelloData data @ModelAttribute HelloData data
따라서 생략하면 HTTP 메시지 바디가 아니라 요청 파라미터를 처리하게 된다.
> 주의
> HTTP 요청시에 content-type이 application/json인지 꼭! 확인해야 한다. 그래야 JSON을 처리할 수
있는 HTTP 메시지 컨버터가 실행된다.
물론 앞서 배운 것과 같이 HttpEntity를 사용해도 된다.
requestBodyJsonV4 - HttpEntity
@ResponseBody
@PostMapping("/request-body-json-v4")
public String requestBodyJsonV4(HttpEntity<HelloData> httpEntity) {
 HelloData data = httpEntity.getBody();
 log.info("username={}, age={}", data.getUsername(), data.getAge());
 return "ok";
}
requestBodyJsonV5
/**
 * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
 * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (contenttype: application/json)
 *
 * @ResponseBody 적용
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter 적용
(Accept: application/json)
 */
@ResponseBody
@PostMapping("/request-body-json-v5")
public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
 log.info("username={}, age={}", data.getUsername(), data.getAge());
 return data;
}
@ResponseBody
응답의 경우에도 @ResponseBody 를 사용하면 해당 객체를 HTTP 메시지 바디에 직접 넣어줄 수 있다.
물론 이 경우에도 HttpEntity 를 사용해도 된다.
@RequestBody 요청
JSON 요청 HTTP 메시지 컨버터 객체
@ResponseBody 응답
객체 HTTP 메시지 컨버터 JSON 응답
