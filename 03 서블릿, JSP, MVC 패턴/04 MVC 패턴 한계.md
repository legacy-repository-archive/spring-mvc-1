MVC 패턴 한계
==================   
MVC 패턴을 적용함으로써 비즈니스 로직과 뷰 로직이 분리가 되었다.      
다만, 여러 컨트롤러를 봤을 때 중복된 코드가 많고, 필요하지 않는 코드들도 보인다.       
  
# MVC 컨트롤러의 단점    

```java

```  
위와 같은 코드를 기준으로 어떠한 문제점이 발생하는지 알아보고자 한다.              
단, 해당 코드만에서'만' 발생하는 문제가 아니다.           
비슷한 유형의 코드도 동일하게 발생하는 문제를 다룰 것이다.         
  
## ViewPath에 중복
```java
String viewPath = "/WEB-INF/views/new-form.jsp";
```
각각의 컨트롤러에서는 데이터 흐름의 이동을 위한 **경로 문자열을 가진다.**    
허나 자세히 보면 아래와 같은 중복을 가지는 것을 알 수 있다.    
  
* **prefix :** `/WEB-INF/views/`
* **suffix :** `.jsp`
      
이러한 중복은 **경로가 바뀌었을 때 모든 ViewPath를 수정해야한다는 단점이 있다.**     
즉, jsp가 아닌 thymeleaf 같은 다른 뷰로 변경한다면 전체 코드를 다 변경해야 한다.   

* **jsp :** `/WEB-INF/`
* **thymeleaf :** `/resource/templates`  
    
## 포워드 중복

```java
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```
              
**View로 이동하는 코드가 항상 중복 호출되어야 하며 그 모습 또한 동일하다.**                 
물론 이 부분을 메서드로 공통화해도 되지만, 해당 메서드도 항상 직접 호출해야 한다.     
     
## 사용하지 않는 코드
```java
HttpServletRequest request, HttpServletResponse response
```

* request : 요청 데이터를 가져올 때 사용한다.(모델, 인코딩 등등) 
* response : 응답 데이터를 추가할 때 사용한다.(

 
`HttpServletRequest`, `HttpServletResponse` 객체는 사용할 때도 있고 사용하지 않을 때도 있다.      
또한 `HttpServletRequest` , `HttpServletResponse` 클래스는         
개발자가 직접 생성하고 다루는 대상이 아니다보니 테스트 케이스를 작성하기도 어렵다.          
    
## 공통 처리가 어렵다.
   
앞서 보았던 많은 단점들은 전부 `공통`과 `중복`이라는 키워드로 설명할 수 있다.     
기능이 복잡해질 수 록 컨트롤러에서 공통으로 처리해야 하는 부분이 점점 더 많이 증가할 것이다.   
단순히 공통 기능을 메서드로 뽑으면 될 것 같지만,    
결과적으로 해당 메서드를 항상 호출해야 하고, 실수로 호출하지 않으면 문제가 될 것이다.   
그리고 호출하는 것 자체도 중복이다. 

정리하면 공통 처리가 어렵다는 문제가 있다.
이 문제를 해결하려면 컨트롤러 호출 전에 먼저 공통 기능을 처리해야 한다. 소위 수문장 역할을 하는 기능이
필요하다. 프론트 컨트롤러(Front Controller) 패턴을 도입하면 이런 문제를 깔끔하게 해결할 수 있다.
(입구를 하나로!)
스프링 MVC의 핵심도 바로 이 프론트 컨트롤러에 있다.
