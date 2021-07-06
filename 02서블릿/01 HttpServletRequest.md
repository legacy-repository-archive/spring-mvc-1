HttpServletRequest
=====================
# 📘 HttpServletRequest 역할
서블릿은 **`HTTP 요청 메시지를 파싱`하고 그 결과를 `HttpServletRequest 객체`에 담아서 제공한다.**       
   
**HTTP 요청 메시지**  
```http
POST /save HTTP/1.1                                     # START LINE
Host: localhost:8080                                    # HEADER
Content-Type: application/x-www-form-urlencoded         # HTML FORM 으로 전송  

username=kim&age=20                                     # BODY
```
HttpServletRequest를 사용하면 다음과 같은 HTTP 요청 메시지를 편리하게 조회할 수 있다.
    
* **START LINE**   
    * HTTP 메소드
    * URL
    * 쿼리 스트링
    * 스키마, 프로토콜
* **헤더**   
    * 헤더 조회
* **바디**   
    * form 파라미터 형식 조회   
    * message body 데이터 직접 조회   
  
HttpServletRequest 객체는 추가로 여러가지 부가기능도 함께 제공한다.
    
1. 임시 저장소 기능     
2. 세션 관리 기능        

**임시 저장소 기능**    
해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능을 가지고 있다.  
사용 방법은 `Session` 과 비슷하다.   
        
* **저장 :** request.setAttribute(name, value)    
* **조회 :** request.getAttribute(name)    
          
**세션 관리 기능**   
```java
request.getSession(create: true)

// 연결된 세션이 있다면 반환
  // 없으면
  // true -> 새로 반환 : request.getSession(true);
  // false -> null 반환 : request.getSession(false);
```

`HttpServletRequest`, `HttpServletResponse`는            
HTTP 요청 메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 객체다.      
따라서 깊이있는 이해를 하려면 HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해면 좋다.     
