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
          
**`/WEB-INF`**            
`/WEB-INF` 폴더는 외부에서 접근할 수 없도록 설정되어 있는 폴더다.               
해당 폴더에 접근하려면 애플리케이션 내부적으로 즉, Servlet을 통해서 접근을 해야한다.  
      
**`redirect vs forward`**    
리다이렉트는 **클라이언트에 응답이 나갔다가 클라이언트가 redirect 경로로 다시 요청한다.**                          
따라서 클라이언트가 인지할 수 있고, **URL 경로도 실제로 변경된다.**                     
반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.           
