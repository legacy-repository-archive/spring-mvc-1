HTTP 요청 데이터 
====================
# 📘 개요
`HTTP 요청 메시지`를 통해 데이터를 전달할때 주로 3가지 방법을 사용한다.

* **GET - 쿼리 파라미터**  
  * `/url?username=hello&age=20`
  * 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
  * 예) 검색, 필터, 페이징등에서 많이 사용하는 방식
* **POST - HTML Form** 
  * `content-type: application/x-www-form-urlencoded`
  * 메시지 바디에 쿼리 파리미터 형식으로 전달 username=hello&age=20
  * 예) 회원 가입, 상품 주문, HTML Form 사용
* **✔ HTTP message body에 데이터를 직접 담아서 요청** 
  * HTTP API에서 주로 사용, JSON, XML, TEXT
  * 데이터 형식은 주로 JSON 사용
  * POST, PUT, PATCH

## 📖 HTTP 요청 데이터 - GET 쿼리 파라미터   
`GET 방식`은 **메시지 바디 없이, URL의 `쿼리 파라미터`를 사용해서 데이터를 전달하는 방식**으로    
주로 `검색`, `필터`, `페이징`등에서 많이 사용하는 방식이다.       

```url
http://localhost:8080/request-param?username=hello&age=20
```  
```http
GET /test?username=hello&age=20 HTTP/1.1
Host: localhost:8080  
Content-Type: application/x-www-form-urlencoded          

username=hello&age=20   
```

`쿼리 파라미터`는 URL에 `?`를 시작으로 입력 및 `&` 로 구분하는 파라미터를 의미한다.     
서버에서는 `HttpServletRequest`메서드를 통해 쿼리 파라미터를 편리하게 조회할 수 있다.    

### 📄 GET 쿼리 파라미터 조회 
**단일 파라미터 조회**   
```java
String username = request.getParameter("username"); 
```

**파라미터 이름들모두 조회**   
```java
Enumeration<String> parameterNames = request.getParameterNames();
```
```java
request.getParameterNames().asIterator()
 .forEachRemaining(paramName -> System.out.println(paramName + "=" + request.getParameter(paramName)));
```
   
**파라미터를 Map으로 조회**   
```java
Map<String, String[]> parameterMap = request.getParameterMap(); 
```
  
**복수 파라미터 조회**   
```java
String[] usernames = request.getParameterValues("username"); 
```
   
**복수 파라미터에서 단일 파라미터 조회**      
여러 값이 들어왔는데 `request.getParameter()`를 사용하면     
`request.getParameterValues()`의 첫 번째 값을 반환한다.       
       
## 📖 HTTP 요청 데이터 - POST HTML Form          
`POST 방식`은 **메시지 바디에 `쿼리 파리미터` 형식으로 데이터를 전달하는 방식이다.**      
주로 `회원 가입`, `상품 주문` 등에서 사용하는 방식이다.              
                      
**특징**        
* `content-type: application/x-www-form-urlencoded`    
* **메시지 바디에 쿼리 파리미터 형식**으로 데이터를 전달한다. `username=hello&age=20`    
* `src/main/webapp/basic/hello-form.html`생성   

POST의 HTML Form을 전송하면 웹 브라우저는 다음 형식으로 HTTP 메시지를 만든다. (웹 브라우저 개발자 모드 확인)
   
```url
http://localhost:8080/request-param
```
```http
POST /test HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded        

username=hello&age=20
```

* 요청 URL: `http://localhost:8080/request-param`
* content-type: `application/x-www-form-urlencoded`
     
클라이언트(웹 브라우저) 입장에서는 두 방식에 차이가 있지만, 서버 입장에서는 둘의 형식이 동일하므로,    
`request.getParameter()` 는 GET URL 쿼리 파라미터 형식도 지원하고, POST HTML Form 형식도 둘 다 지원한다      

**참고**  
* **content-type**은 **HTTP 메시지 바디의 데이터 형식을 지정하는 것**이다.      
* `GET URL 쿼리 파라미터` 형식으로 데이터를 전달할 때는 **HTTP 메시지 바디를 사용하지 않기에 content-type이 없다.**    
* `POST HTML Form` 형식으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기에      
  바디에 포함된 데이터가 어떤 형식인지 **content-type을 꼭 지정해야 한다.**       
* 이렇게 폼으로 데이터를 전송하는 형식을 `application/x-www-form-urlencoded`라 한다.  

## 📖 HTTP 요청 데이터 - API 메시지 바디 - 단순 텍스트
**`HTTP message body`에 데이터를 직접 담아서 요청하는 방식**도 존재한다.       
`HTTP API`에서 주로 사용하며 `JSON`, `XML`, `TEXT` 데이터 형식들이 있으며 **주로 JSON 사용한다.**           
또한, `POST`, `PUT`, `PATCH`와 같은 다양한 `HTTP 메서드`를 이용할 수 있다.         

서버에서는 HTTP 메시지 바디의 데이터를 `InputStream`을 사용해서 직접 읽을 수 있다.      

```java
@WebServlet(name = "requestBodyStringServlet", urlPatterns = "/request-bodystring")
public class RequestBodyStringServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        System.out.println("messageBody = " + messageBody);
        response.getWriter().write("ok");  
    }
}
```   
**참고**       
* inputStream은 byte 코드를 반환한다.    
* byte 코드를 우리가 읽을 수 있는 문자(String)로 보려면 문자표(Charset)를 지정해주어야 한다.   
* 여기서는 UTF_8 Charset을 지정해주었다.    

단순 텍스트이기에 포스트맨으로 테스트 가능하다.    
      
## 📖 HTTP 요청 데이터 - API 메시지 바디 - JSON    
이번에는 HTTP API에서 주로 사용하는 JSON 형식으로 데이터를 전달해보자.   
         
**JSON 형식 전송**   
```http   
POST http://localhost:8080/request-body-json   
Host: localhost:8080    
content-type: application/json   

{"username": "hello", "age": 20}   
```
* 결과: messageBody = {"username": "hello", "age": 20}
        
```java
@Getter @Setter
class HelloData {
    private String username;
    private int age;
}

@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-bodyjson")
public class RequestBodyJsonServlet extends HttpServlet {
    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        System.out.println("messageBody = " + messageBody);
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
        System.out.println("helloData.username = " + helloData.getUsername());
        System.out.println("helloData.age = " + helloData.getAge());
        response.getWriter().write("ok");
    }
}
```
   
**참고**     
* JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jackson, Gson 같은 JSON 라이브러리를 사용해야한다.              
* **스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리(✔ ObjectMapper)를 함께 제공한다.**           
* 재밌는 글을 발견했는데 `ObjectMapper`는 Post 요청시 `setter`를 사용하지 않아도 값을 매핑해준다.     
* 이는 곧 `@RequestBody`랑도 직결되는 것이여서 `Setter` 사용을 줄여보면 좋을 것 같다.(GSON은 그냥 가능)    
* [이동욱님의 블로그](https://jojoldu.tistory.com/407)        
     
**참고**      
* HTML form 데이터도 메시지 바디를 통해 전송되므로 InputStream을 통해 직접 읽을 수 있다.      
* 하지만 편리한 파리미터 조회기능(request.getParameter(...))을 이미 제공하기 때문에 파라미터 조회 기능을 사용하면 된다.       

