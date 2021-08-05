HTTP 메시지 컨버터
======================  
  
뷰 템플릿으로 HTML을 생성해서 응답하는 것이 아니라,    
HTTP API처럼 JSON 데이터를 HTTP 메시지 바디에서 직접 읽거나 쓰는 경우 HTTP 메시지 컨버터를 사용하면 편리하다.   
  
# @ResponseBody 사용 원리    

![http-converter](https://user-images.githubusercontent.com/50267433/128208586-83a1086f-a38d-4851-961d-068008d77fcb.PNG)  
  
**@ResponseBody**          
`@ResponseBody`는 핸들러로부터 반환된 데이터를 `Http body`에 문자로 반환을 한다.              
이 과정에서 **데이터를 `Http body`에 넣기 위해 HttpMessageConverter 구현체를 사용한다.**                  
     
* **기본 문자처리:** `StringHttpMessageConverter`     
* **기본 객체처리:** `MappingJackson2HttpMessageConverter`        
* **byte 처리 및 기타 등등:** `HttpMessageConverter`(사실 가장 기본)       
       
**참고**   
응답의 경우 `클라이언트의 HTTP Accept`헤더와 `서버의 컨트롤러 반환 타입 정보`,    
이 둘을 조합해서 `HttpMessageConverter`가 선택된다.          
       
스프링 MVC는 다음의 경우에 HTTP 메시지 컨버터를 적용한다.   
* **HTTP 요청 :** `@RequestBody`, `HttpEntity(RequestEntity)`   
* **HTTP 응답 :** `@ResponseBody`, `HttpEntity(ResponseEntity)`       
  
# HTTP 메시지 컨버터 인터페이스
**HttpMessageConverter**  
```java
package org.springframework.http.converter;

    public interface HttpMessageConverter<T> {
    
    boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);
    
    boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);
    
    List<MediaType> getSupportedMediaTypes();
    
    T read(Class<? extends T> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException;
    
    void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException;
}
```    
HTTP 메시지 컨버터는 `HTTP 요청`, `HTTP 응답` 두 곳에서 모두 사용된다.    
   
* `canRead()`, `canWrite()` : **특정 `클래스 타입`, `미디어 타입`을 지원하는지 체크(Converter 가능 여부 체크)**        
* `read()`, `write()` : **메시지를 읽고 쓰는 기능으로 `실제 변환 로직` 메서드**                  
     
**스프링 부트 기본 메시지 컨버터**     
|우선순위|컨버터 종류|   
|-------|----------|   
|0|ByteArrayHttpMessageConverter|
|1|StringHttpMessageConverter|
|2|MappingJackson2HttpMessageConverter|
   
스프링 부트는 다양한 메시지 컨버터를 제공하는데,     
**`대상 클래스 타입`과 `미디어 타입` 둘을 체크해서 사용 여부를 결정한다.**          
만약 만족하지 않으면 다음 메시지 컨버터로 우선순위가 넘어간다.    

# ByteArrayHttpMessageConverter  
> byte[] 데이터를 처리한다.   
  
* **클래스 타입:** `byte[]`  
* **미디어타입:** `*/*` 
     
👉 요청 예) @RequestBody byte[] data       
👉 응답 예) @ResponseBody return byte[] / 쓰기 미디어타입 application/octet-stream       


# StringHttpMessageConverter
> String 문자로 데이터를 처리한다.   
   
* **클래스 타입:** `String` 
* **미디어타입:** `*/*`   
   
👉 요청 예) @RequestBody String data      
👉 응답 예) @ResponseBody return "ok" / 쓰기 미디어타입 text/plain       
  
# MappingJackson2HttpMessageConverter   
> application/json
  
* **클래스 타입:** `객체 또는 HashMap`        
* **미디어타입:** `application/json 관련`       
  
👉 요청 예) @RequestBody HelloData data      
👉 응답 예) @ResponseBody return helloData / 쓰기 미디어타입 application/json 관련     

# 예시  
**StringHttpMessageConverter**  
```http
content-type: application/json
```
```java
@RequestMapping
void hello(@RequetsBody String data) {}
```

**MappingJackson2HttpMessageConverter**  
```http
content-type: application/json
```
```java
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```
 
**?(못참음 - 에러)**
```http
content-type: text/html
```
```java  
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```

# HTTP 요청 데이터 읽기
HTTP 요청이 오고, 컨트롤러에서 @RequestBody , HttpEntity 파라미터를 사용한다.
메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 canRead() 를 호출한다.
대상 클래스 타입을 지원하는가.
예) @RequestBody 의 대상 클래스 ( byte[] , String , HelloData )
HTTP 요청의 Content-Type 미디어 타입을 지원하는가.
예) text/plain , application/json , */*
canRead() 조건을 만족하면 read() 를 호출해서 객체 생성하고, 반환한다.
HTTP 응답 데이터 생성
컨트롤러에서 @ResponseBody , HttpEntity 로 값이 반환된다.
메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 canWrite() 를 호출한다.
대상 클래스 타입을 지원하는가.
예) return의 대상 클래스 ( byte[] , String , HelloData )
HTTP 요청의 Accept 미디어 타입을 지원하는가.(더 정확히는 @RequestMapping 의 produces )
예) text/plain , application/json , */*
canWrite() 조건을 만족하면 write() 를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다.
