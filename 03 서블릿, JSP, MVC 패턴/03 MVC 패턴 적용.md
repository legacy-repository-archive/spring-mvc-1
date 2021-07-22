MVC 패턴 적용
================
아래와 같은 구조로 MVC 패턴을 적용해보자.
    
* **Controller:** 서블릿            
* **View :** JSP       
* **Model :** HttpServletRequest 객체 사용         
        
`request`는 내부에 데이터 저장소를 가지고 있는데,         
`request.setAttribute()`와 `request.getAttribute()`를 사용하면 데이터를 보관하고 조회할 수 있다.           

# 📕 MVC 패턴 적용 예시 
## 📖 회원 등록 폼 이동 

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
          
**/WEB-INF**            
`/WEB-INF` 폴더는 외부에서 접근할 수 없도록 설정되어 있는 폴더다.               
해당 폴더에 접근하려면 애플리케이션 내부적으로 즉, Servlet을 통해서 접근을 해야한다.  
      
**redirect vs forward**    
리다이렉트는 **클라이언트에 응답이 나갔다가 클라이언트가 redirect 경로로 다시 요청한다.**                               
따라서 클라이언트가 인지할 수 있고, **URL 경로도 실제로 변경된다.**                     
반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.      

## 📖 회원 저장 
```java
@WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
public class MvcMemberSaveServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Member 저장 
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        Member member = new Member(username, age);
        memberRepository.save(member);
        
        // Model에 데이터를 보관
        request.setAttribute("member", member);
        
        // 특정 URL로 forward
        String viewPath = "/WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```
**Model**은 `HttpServletRequest request` 객체를 사용하는 것이다.**         
request가 제공하는 `setAttribute()`를 사용하면             
request 객체에 데이터를 보관해서 뷰에 전달할 수 있다.      
**View**는 `request.getAttribute()` 를 사용해서 데이터를 꺼내면 된다.      

## 📖 회원 목록 조회
```java
@WebServlet(name = "mvcMemberListServlet", urlPatterns = "/servlet-mvc/members")
public class MvcMemberListServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 회원 목록 조회 
        List<Member> members = memberRepository.findAll();
        
        // Model에 데이터를 보관 
        request.setAttribute("members", members);
        
        // 특정 URL로 forward
        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```
Model 역할인 `HttpServletRequest request` 객체는 보관시 다양한 데이터 타입을 지원한다.     
위와 같이 `List<Member> members`와 같은 컬렉션 타입도 보관할 수 있다.     
