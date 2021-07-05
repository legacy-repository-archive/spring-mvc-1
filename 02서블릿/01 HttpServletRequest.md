HttpServletRequest
=====================
# 개요
## HttpServletRequest 역할
서블릿은 **`HTTP 요청 메시지를 파싱`하고 그 결과를 `HttpServletRequest 객체`에 담아서 제공한다.**       
   
**HTTP 요청 메시지**  
```http
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
username=kim&age=20
```
HttpServletRequest를 사용하면 다음과 같은 HTTP 요청 메시지를 편리하게 조회할 수 있다.

* START LINE
* HTTP 메소드
* URL
* 쿼리 스트링
* 스키마, 프로토콜
* 헤더
* 헤더 조회
* 바디
* form 파라미터 형식 조회
* message body 데이터 직접 조회
  
HttpServletRequest 객체는 추가로 여러가지 부가기능도 함께 제공한다.
임시 저장소 기능
해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능
저장: request.setAttribute(name, value)
조회: request.getAttribute(name)
세션 관리 기능
request.getSession(create: true)
> 중요
> HttpServletRequest, HttpServletResponse를 사용할 때 가장 중요한 점은 이 객체들이 HTTP 요청
메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 객체라는 점이다. 따라서 이 기능에 대해서
깊이있는 이해를 하려면 HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해야 한다
