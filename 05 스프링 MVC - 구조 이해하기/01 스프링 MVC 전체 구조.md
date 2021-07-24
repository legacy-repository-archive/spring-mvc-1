스프링 MVC 전체 구조
=====================

`직접 만든 MVC 프레임워크`와 `스프링 MVC 프레임워크`의 구조는 아래와 같다.   
    
**직접 만든 MVC 프레임워크**      
![v5](https://user-images.githubusercontent.com/50267433/126873718-097ab3df-38ef-4f23-b2df-92dcd7ef2c8d.PNG)    
      
**스프링 MVC 프레임워크**    
![v5](https://user-images.githubusercontent.com/50267433/126873718-097ab3df-38ef-4f23-b2df-92dcd7ef2c8d.PNG)   
  
둘간의 차이가 있을 줄 알았지만 사실 동일한 구조를 띄고 있다.(갓영한..)    
단, 몇몇의 이름의 차이는 존재한다.   

|직접만든 MVC|스프링 MVC|
|-----------|----------|
|FrontController|DispatcherServlet|
|handlerMappgingMap|HandlerMappingMap|
|MyHandlerAdapter|HandlerAdapter|
|ModelView|ModelAndView|
|viewResolver|ViewReolver|
|MyView|View|



