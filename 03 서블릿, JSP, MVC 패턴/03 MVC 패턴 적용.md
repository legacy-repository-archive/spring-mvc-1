MVC 패턴 적용
================
아래와 같은 구조로 MVC 패턴을 적용해보자.
    
* **Controller:** 서블릿            
* **View :** JSP       
* **Model :** HttpServletRequest 객체 사용         
        
`request`는 내부에 데이터 저장소를 가지고 있는데,         
`request.setAttribute()`와 `request.getAttribute()`를 사용하면 데이터를 보관하고 조회할 수 있다.           

```java
@WebServlet(name = "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
public class MvcMemberFormServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```
|메서드|설명|
|------|---|
|request.getRequestDispatcher(경로)|해당 경로를 저장하는 `RequestDispatcher` 를 반환한다.|    
|dispatcher.forward()|다른 서블릿이나 JSP로 이동할 수 있는 기능이다<br>서버 내부에서 다시 호출이 발생한다|    
