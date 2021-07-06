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

Query String Parameter
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

Form data
username: hello
age: 20
```

* 요청 URL: http://localhost:8080/request-param
* content-type: application/x-www-form-urlencoded
     
클라이언트(웹 브라우저) 입장에서는 두 방식에 차이가 있지만, 서버 입장에서는 둘의 형식이 동일하므로,    
`request.getParameter()` 는 GET URL 쿼리 파라미터 형식도 지원하고, POST HTML Form 형식도 둘 다 지원한다      
