HttpServletRequest
=====================
# 📘 HttpServletRequest 개요 
## 📖 HttpServletRequest 역할
서블릿은 **`HTTP 요청 메시지를 파싱`하고 그 결과를 `HttpServletRequest 객체`에 담아서 제공한다.**       
   
**HTTP 요청 메시지**  
```http
POST /save HTTP/1.1                                     # START LINE
Host: localhost:8080                                    # HEADER
Content-Type: application/x-www-form-urlencoded         # HTML FORM 으로 전송  

username=kim&age=20                                     # BODY
```
즉, 서블릿은 위와 같은 `HTTP 요청 메시지` 파싱해서 `HttpServletRequest 객체`를 만들고  
`HttpServletRequest 객체`를 통해 우리는 아래와 같은 HTTP 요소들을 편리하게 조회할 수 있다.
       
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

`HttpServletRequest`, `HttpServletResponse`는              
HTTP 요청 메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 객체다.        
따라서 깊이있는 이해를 하려면 HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해면 좋다.     
     
참고로, `HttpServletRequest` 객체는 추가로 여러가지 부가기능도 함께 제공한다.   
    
1. 임시 저장소 기능     
2. 세션 관리 기능        

### 📄 임시 저장소 기능    
해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능을 가지고 있다.  
사용 방법은 `Session` 과 비슷하다.   
        
* **저장 :** request.setAttribute(name, value)    
* **조회 :** request.getAttribute(name)    
           
### 📄 세션 관리 기능  
`request`로부터 세션 생성과 관련된 기능을 제공해준다.   
   
```java
request.getSession(create: true)
```
`request.getSession()`은 기존에 이미 연결된 세션이 있다면 해당 세션을 반환해준다.  
만약, 없으면 아래와 같은 동작을 한다.   

* `request.getSession(true);` : 새로 반환 
* `request.getSession(false);` : null 반환 

# 📗 HttpServletRequest - 기본 사용법
```java
    @WebServlet(name = "requestHeaderServlet", urlPatterns = "/request-header")
    public class RequestHeaderServlet extends HttpServlet {
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse
                response)
                throws ServletException, IOException {
            printStartLine(request);
            printHeaders(request);
            printHeaderUtils(request);
            printEtc(request);
            response.getWriter().write("ok");
        }
    }
```
## START LINE 
```java
    private void printStartLine(HttpServletRequest request) {
        // http://localhost:8080/request-header?username=hello
        System.out.println("--- REQUEST-LINE - start ---");
        System.out.println("request.getMethod() = " + request.getMethod());             // GET
        System.out.println("request.getProtocal() = " + request.getProtocol());         // HTTP / 1.1
        System.out.println("request.getScheme() = " + request.getScheme());             // http
        
        System.out.println("request.getRequestURL() = " + request.getRequestURL());     // http://localhost:8080/request-header?username=hello 
        System.out.println("request.getRequestURI() = " + request.getRequestURI());     // /request-header 
        System.out.println("request.getQueryString() = " + request.getQueryString());   // username=hello
        System.out.println("request.isSecure() = " + request.isSecure());               // https 사용 유무
        System.out.println("--- REQUEST-LINE - end ---");
        System.out.println();
    }
```
```sh
--- REQUEST-LINE - start ---
request.getMethod() = GET
request.getProtocal() = HTTP/1.1
request.getScheme() = http
request.getRequestURL() = http://localhost:8080/request-header
request.getRequestURI() = /request-header
request.getQueryString() = username=hello
request.isSecure() = false
--- REQUEST-LINE - end ---
```

## HEADER

```java
    //Header 모든 정보
    private void printHeaders(HttpServletRequest request) {
        System.out.println("--- Headers - start ---");
/*
        Enumeration<String> headerNames = request.getHeaderNames();
        while (headerNames.hasMoreElements()) {
            String headerName = headerNames.nextElement();
            System.out.println(headerName + ": " + request.getHeader(headerName));
        }
*/
        request.getHeaderNames().asIterator()
                .forEachRemaining(headerName -> System.out.println(headerName + ":
                        " + request.getHeader(headerName)));
                        System.out.println("--- Headers - end ---");
        System.out.println();
    }
```
```sh
--- Headers - start ---
host: localhost:8080
connection: keep-alive
cache-control: max-age=0
sec-ch-ua: "Chromium";v="88", "Google Chrome";v="88", ";Not A Brand";v="99"
sec-ch-ua-mobile: ?0
upgrade-insecure-requests: 1
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 11_2_0) AppleWebKit/537.36
(KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/
webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
sec-fetch-site: none
sec-fetch-mode: navigate
sec-fetch-user: ?1
sec-fetch-dest: document
accept-encoding: gzip, deflate, br
accept-language: ko,en-US;q=0.9,en;q=0.8,ko-KR;q=0.7
--- Headers - end ---
```

## HEADER 편리한 조회
```java
    //Header 편리한 조회
    private void printHeaderUtils(HttpServletRequest request) {
        System.out.println("--- Header 편의 조회 start ---");
        System.out.println("[Host 편의 조회]");
        System.out.println("request.getServerName() = " +
                request.getServerName()); //Host 헤더
        System.out.println("request.getServerPort() = " +
                request.getServerPort()); //Host 헤더
        System.out.println();
        System.out.println("[Accept-Language 편의 조회]");
        request.getLocales().asIterator()
                .forEachRemaining(locale -> System.out.println("locale = " +
                        locale));
        System.out.println("request.getLocale() = " + request.getLocale());
        System.out.println();
        System.out.println("[cookie 편의 조회]");
        if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            }
        }
        System.out.println();
        System.out.println("[Content 편의 조회]");
        System.out.println("request.getContentType() = " +
                request.getContentType());
        System.out.println("request.getContentLength() = " +
                request.getContentLength());
        System.out.println("request.getCharacterEncoding() = " +
                request.getCharacterEncoding());
        System.out.println("--- Header 편의 조회 end ---");
        System.out.println();
    }
```
```sh
--- Header 편의 조회 start ---
[Host 편의 조회]
request.getServerName() = localhost
request.getServerPort() = 8080
[Accept-Language 편의 조회]
locale = ko
locale = en_US
locale = en
locale = ko_KR
request.getLocale() = ko
[cookie 편의 조회]
[Content 편의 조회]
request.getContentType() = null
request.getContentLength() = -1
request.getCharacterEncoding() = UTF-8
--- Header 편의 조회 end ---
```

## 기타 조회
```java
    //기타 정보
    private void printEtc(HttpServletRequest request) {
        System.out.println("--- 기타 조회 start ---");
        System.out.println("[Remote 정보]");
        System.out.println("request.getRemoteHost() = " +
                request.getRemoteHost()); //
        System.out.println("request.getRemoteAddr() = " +
                request.getRemoteAddr()); //
        System.out.println("request.getRemotePort() = " +
                request.getRemotePort()); //
        System.out.println();
        System.out.println("[Local 정보]");
        System.out.println("request.getLocalName() = " +
                request.getLocalName()); //
        System.out.println("request.getLocalAddr() = " +
                request.getLocalAddr()); //
        System.out.println("request.getLocalPort() = " +
                request.getLocalPort()); //
        System.out.println("--- 기타 조회 end ---");
        System.out.println();
    }
```
```sh
--- 기타 조회 start ---
[Remote 정보]
request.getRemoteHost() = 0:0:0:0:0:0:0:1
request.getRemoteAddr() = 0:0:0:0:0:0:0:1
request.getRemotePort() = 54305
[Local 정보]
request.getLocalName() = localhost
request.getLocalAddr() = 0:0:0:0:0:0:0:1
request.getLocalPort() = 8080
--- 기타 조회 end ---
```
