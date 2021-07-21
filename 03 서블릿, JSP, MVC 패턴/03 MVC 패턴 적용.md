MVC 패턴 적용
================
아래와 같은 구조로 MVC 패턴을 적용해보자.

* **컨트롤러 :** 서블릿   
* **뷰 :** JSP 
   
Model은 HttpServletRequest 객체를 사용한다. request는 내부에 데이터 저장소를 가지고 있는데,     
request.setAttribute() , request.getAttribute() 를 사용하면 데이터를 보관하고, 조회할 수 있다.    
