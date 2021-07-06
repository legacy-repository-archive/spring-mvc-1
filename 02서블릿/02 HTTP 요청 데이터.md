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

# HTTP 요청 데이터 - GET 쿼리 파라미터   
`GET 방식`은 **메시지 바디 없이, URL의 `쿼리 파라미터`를 사용해서 데이터를 전달하는 방식**으로    
주로 `검색`, `필터`, `페이징`등에서 많이 사용하는 방식이다.       

```url
http://localhost:8080/request-param?username=hello&age=20
```  
`쿼리 파라미터`는 URL에 `?`를 시작으로 입력 및 `&` 로 구분하는 파라미터를 의미한다.     
서버에서는 `HttpServletRequest`메서드를 통해 쿼리 파라미터를 편리하게 조회할 수 있다.    
   
```java
String username = request.getParameter("username"); //단일 파라미터 조회
```
```java
Enumeration<String> parameterNames = request.getParameterNames(); //파라미터 이름들모두 조회
```
```java
Map<String, String[]> parameterMap = request.getParameterMap(); //파라미터를 Map으로 조회
```
```java
String[] usernames = request.getParameterValues("username"); //복수 파라미터 조회
```

