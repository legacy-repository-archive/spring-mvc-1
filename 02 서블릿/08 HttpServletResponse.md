HttpServletResponse
======================  
# 📘 HttpServletResponse 개요
## 📖 HttpServletResponse 역할  
`HttpServletResponse` 는 응답과 관련된 처리를 할 수 있으며       
`응답 메시지를 생성` 및 `편의 기능을 제공`해준다.        
   
**HTTP 응답 메시지 생성**  
* HTTP 응답코드 지정 
* 헤더 생성
* 바디 생성
   
**편의 기능 제공**
* Content-Type
* 쿠키  
* Redirect   

## 📗 HttpServletResponse 기본 사용법   
### 📖 일반적인 작성 방법 
```java
@WebServlet(name = "responseHeaderServlet", urlPatterns = "/response-header")
public class ResponseHeaderServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //[status-line]
        response.setStatus(HttpServletResponse.SC_OK); //200
        
        //[response-headers]
        response.setHeader("Content-Type", "text/plain;charset=utf-8");
        response.setHeader("Cache-Control", "no-cache, no-store, mustrevalidate");
        response.setHeader("Pragma", "no-cache");
        response.setHeader("my-header", "hello");
        
        //[message body]
        PrintWriter writer = response.getWriter();
        writer.println("ok");
    }
}
```  
응답은 요청과 다르게 데이터를 매핑하는 작업은 없고       
간단히 `응답코드` + `헤더 설정` + `바디(데이터)`의 작업만 수행하면 된다.          
물론, `헤더 설정` 안에 `캐시 설정`, `콘텐츠 타입 설정`와 같은 설정들을 넣을 수 있다.      
    
### 📖 Header 편의 메서드   
```java
@WebServlet(name = "responseHeaderServlet", urlPatterns = "/response-header")
public class ResponseHeaderServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //[status-line]
        response.setStatus(HttpServletResponse.SC_OK); //200
        
        //[Header 편의 메서드]
        content(response);
        cookie(response);
        redirect(response);
        
        //[message body]
        PrintWriter writer = response.getWriter();
        writer.println("ok");
    }
}

private void content(HttpServletResponse response) {
    //Content-Type: text/plain;charset=utf-8
    //Content-Length: 2
    
    response.setContentType("text/plain");
    response.setCharacterEncoding("utf-8");
    response.setContentLength(2);                     // 길이 만큼의 데이터만 응답, 생략시 길이에 맞는 값으로 자동 생성
}

private void cookie(HttpServletResponse response) {
    //Set-Cookie: myCookie=good; Max-Age=600;
    //response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
    
    Cookie cookie = new Cookie("myCookie", "good");   // Key-Value 데이터  
    cookie.setMaxAge(600);                            // MaxAge 600초
    response.addCookie(cookie);                       // 쿠키를 담는다.   
}

private void redirect(HttpServletResponse response) throws IOException {
    //Status Code 302
    //Location: /basic/hello-form.html
    //response.setStatus(HttpServletResponse.SC_FOUND);         // 302
    //response.setHeader("Location", "/basic/hello-form.html"); // 302 응답코드와 같이 사용시 이동 url 표현  
    
    response.sendRedirect("/basic/hello-form.html");            // 해당 url로 리다이렉트를 시킨다.  
}
```
`Header 편의 메서드`를 통해        
`setHeader()` 말고 의미있는 이름의 메서드를 사용할 수 있다.       
  

