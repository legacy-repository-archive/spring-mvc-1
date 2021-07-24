스프링 MVC 전체 구조
=====================

`직접 만든 MVC 프레임워크`와 `스프링 MVC 프레임워크`의 구조는 아래와 같다.   
    
**직접 만든 MVC 프레임워크**      
![custom-mvc](https://user-images.githubusercontent.com/50267433/126873927-e39e18b6-06a8-414b-9c21-9e6561d39ac5.PNG)

**스프링 MVC 프레임워크**    
![spring-mvc](https://user-images.githubusercontent.com/50267433/126873936-e90358e2-10ac-4b6e-9343-6d0c20c523fd.PNG)


둘간의 차이가 있을 줄 알았지만 사실 동일한 구조를 띄고 있다.(갓영한..)    
단, 몇몇 구성 요소들간의 이름 차이는 존재한다.     
  
|직접만든 MVC|스프링 MVC|
|-----------|----------|
|FrontController|DispatcherServlet|
|handlerMappingMap|HandlerMappingMap|
|MyHandlerAdapter|HandlerAdapter|
|ModelView|ModelAndView|
|viewResolver|ViewReolver|
|MyView|View|

# 📘 DispathcerServlet
```java
org.springframework.web.servlet.DispatcherServlet
```
    
스프링 MVC도 프론트 컨트롤러 패턴으로 구현되어 있다.       
스프링 MVC의 프론트 컨트롤러가 바로 디스패처 서블릿(DispatcherServlet)이다.    
그리고 이 **디스패처 서블릿이 바로 스프링 MVC의 핵심이다**   

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    ModelAndView mv = null;
    
    // 1. 핸들러 조회
    mappedHandler = getHandler(processedRequest);
    if (mappedHandler == null) {
        noHandlerFound(processedRequest, response);
        return;
    }
    
    // 2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

    // 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환
    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
    processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
}

private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, 
        HandlerExecutionChain mappedHandler, ModelAndView mv, Exception exception) throws Exception {
    
    // 뷰 렌더링 호출
    render(mv, request, response);
}

protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
    View view;
    String viewName = mv.getViewName();
   
    // 6. 뷰 리졸버를 통해서 뷰 찾기, 7. View 반환
    view = resolveViewName(viewName, mv.getModelInternal(), locale, request);

    // 8. 뷰 렌더링
    view.render(mv.getModelInternal(), request, response);
}
```
 
## 📖 DispacherServlet 서블릿 등록
`DispacherServlet`도 부모 클래스에서 `HttpServlet`을 상속 받아서 사용하고 **서블릿으로 동작한다.**   

* `DispatcherServlet` -> `FrameworkServlet` -> `HttpServletBean` -> `HttpServlet`    
        
**스프링 부트**는 DispacherServlet 을 서블릿으로 자동으로 등록하면서 
**모든 경로( urlPatterns="/" )에 대해서 매핑한다.**      
    
참고: 더 자세한 경로가 우선순위가 높다. 그래서 기존에 등록한 서블릿도 함께 동작한다.
      
## 📖 요청 흐름
서블릿이 호출되면 `HttpServlet`이 제공하는 **serivce()** 가 호출된다.
스프링 MVC는 DispatcherServlet 의 부모인 FrameworkServlet 에서 service() 를 오버라이드 해두었다.
FrameworkServlet.service() 를 시작으로 여러 메서드가 호출되면서
DispacherServlet.doDispatch() 가 호출된다.
지금부터 DispacherServlet 의 핵심인 doDispatch() 코드를 분석해보자. 최대한 간단히 설명하기
위해 예외처리, 인터셉터 기능은 제외했다.

