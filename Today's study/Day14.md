### 오늘의 일기
---
#### API 예외 처리 - 시작
+ HTML 페이지의 경우 지금까지 설명했던 것 처럼 4xx, 5xx와 같은 오류 페이지만 있으면 대부분의 문제를 해결할 수 있다.
+ 그런데 API의 경우에는 생각할 내용이 더 많다. 
+ 오류 페이지는 단순히 고객에게 오류 화면을 보여주고 끝이지만, API는 각 오류 상황에 맞는 오류 응답 스펙을 정하고, JSON으로 데이터를 내려주어야 한다.

#### API 예외 처리 - 스프링 부트 기본 오류 처리
+ API 예외 처리도 스프링 부트가 제공하는 기본 오류 방식을 사용할 수 있다.
```java
@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse 
response) {}
@RequestMapping
public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {}
```
+ errorHtml() : produces = MediaType.TEXT_HTML_VALUE : 클라이언트 요청의 Accept 해더 값이 text/html 인 경우에는 errorHtml() 을 호출해서 view를 제공한다.
+ error() : 그외 경우에 호출되고 ResponseEntity 로 HTTP Body에 JSON 데이터를 반환한다.
+ 스프링 부트는 `BasicErrorController` 가 제공하는 기본 정보들을 활용해서 오류 API를 생성해준다.
+ 옵션들을 설정하면 더 자세한 오류 정보를 추가할 수 있다.
  + server.error.include-binding-errors=always
  + server.error.include-exception=true
  + server.error.include-message=always
  + server.error.include-stacktrace=always
  + 오류 메시지는 이렇게 막 추가하면 보안상 위험할 수 있다. 간결한 메시지만 노출하고, 로그를 통해서 확인하자.🎃


#### Html 페이지 vs API 오류
+ `BasicErrorController` 를 확장하면 JSON 메시지도(JSON 오류 메시지도) 변경할 수 있다. 그런데 API 오류는 `@ExceptionHandler` 가 제공하는 기능을 사용하는 것이 더 나은 방법이다.
+ 스프링 부트가 제공하는 `BasicErrorController` 는 HTML 페이지를 제공하는 경우에는 매우 편리하다. 4xx, 5xx 등등 모두 잘 처리해준다. 
+ 그런데 API 오류 처리는 다른 차원의 이야기이다. API 마다, 각각의 컨트롤러나 예외마다 서로 다른 응답 결과를 출력해야 할 수도 있다. 
+ 예를 들어서 회원과 관련된 API에서 예외가 발생할 때 응답과, 상품과 관련된 API에서 발생하는 예외에 따라 그 결과가 달라질 수 있다. 
+ 결과적으로 매우 세밀하고 복잡하다. 따라서 이 방법은 HTML 화면을 처리할 때 사용하고, API 오류처리는 `@ExceptionHandler` 를 사용하자

#### API 예외 처리 - HandlerExceptionResolver (직접 ExceptionResolver를 구현하는 방법)
+ 목표
  + 예외가 발생해서 서블릿을 넘어 WAS까지 예외가 전달되면 HTTP 상태코드가 500으로 처리된다. 
  + 발생하는 예외에 따라서 400, 404 등등 다른 상태코드도 처리하고 싶다.
  + 오류 메시지, 형식등을 API마다 다르게 처리하고 싶다
+ 스프링 MVC는 컨트롤러(핸들러) 밖으로 예외가 던져진 경우 예외를 해결하고, 동작을 새로 정의할 수 있는 방법을 제공한다. 
+ 컨트롤러 밖으로 던져진 예외를 해결하고, 동작 방식을 변경하고 싶으면 `HandlerExceptionResolver`를 사용하면 된다. 줄여서 `ExceptionResolver`라 한다.
+ `ExceptionResolver` 적용 전
 <img width="500" src="https://user-images.githubusercontent.com/86812098/152725576-7d9d0610-41d1-4ddf-b0c3-f0fc23a8e937.png">
 
+ `ExceptionResolver`적용 후
<img width="500" src="https://user-images.githubusercontent.com/86812098/152725732-24fbaa00-2746-4cbf-b4a1-3b99ba015c32.png">

+ 참고📢 `ExceptionResolver`로 예외를 해결해도 `postHandle()` 은 호출되지 않는다.


```java
@Override
 public ModelAndView resolveException(HttpServletRequest request,
  HttpServletResponse response, Object handler, Exception ex) {
   try {
       if (ex instanceof IllegalArgumentException) {
       log.info("IllegalArgumentException resolver to 400");
       response.sendError(HttpServletResponse.SC_BAD_REQUEST,
      ex.getMessage());
       return new ModelAndView();
       }
   } catch (IOException e) {
      log.error("resolver ex", e);
   }
      return null;
   }
```
+ IllegalArgumentException 이 발생하면 response.sendError(400) 를 호출해서 HTTP 상태 코드를 400으로 지정하고, 빈 ModelAndView 를 반환한다.
+ ExceptionResolver 가 ModelAndView 를 반환하는 이유
  + 마치 try, catch를 하듯이, Exception 을 처리해서 정상 흐름 처럼 변경하는 것이 목적이다. 이름 그대로 Exception 을 Resolver(해결)하는 것이 목적이다.

#### 반환 값에 따른 동작 방식
+ `HandlerExceptionResolver` 의 반환 값에 따른 `DispatcherServlet` 의 동작 방식은 다음과 같다.
  + 빈 ModelAndView
    + new ModelAndView() 처럼 빈 ModelAndView 를 반환하면 뷰를 렌더링 하지않고, 정상 흐름으로 서블릿이 리턴된다.
  + ModelAndView 지정
    + ModelAndView 에 View , Model 등의 정보를 지정해서 반환하면 뷰를 렌더링 한다.
  + null
    + null 을 반환하면, 다음 ExceptionResolver 를 찾아서 실행한다. 만약 처리할 수 있는 ExceptionResolver 가 없으면 예외 처리가 안되고, 기존에 발생한 예외를 서블릿 밖으로 던진다.

#### ExceptionResolver 활용
+ 예외를 `response.sendError(xxx)` 호출로 변경해서 서블릿에서 상태 코드에 따른 오류를 처리하도록 위임
+ 이후 WAS는 서블릿 오류 페이지를 찾아서 내부 호출
  + 예를 들어서 스프링 부트가 기본으로 설정한 /error 가 호출됨.
+ 뷰 템플릿 처리
  + ModelAndView 에 값을 채워서 예외에 따른 새로운 오류 화면 뷰 렌더링 해서 고객에게 제공
+ API 응답 처리
  + `response.getWriter().println("hello");` 처럼 HTTP 응답 바디에 직접 데이터를 넣어주는 것도 가능하다. 여기에 JSON 으로 응답하면 API 응답 처리를 할 수 있다.

#### 정리
+ `ExceptionResolver` 를 사용하면 컨트롤러에서 예외가 발생해도 ExceptionResolver 에서 예외를 처리해버린다.
+ 따라서 예외가 발생해도 서블릿 컨테이너까지 예외가 전달되지 않고, 스프링 MVC에서 예외 처리는 끝이 난다.
+ 결과적으로 WAS 입장에서는 정상 처리가 된 것이다. 이렇게 예외를 이곳에서 모두 처리할 수 있다는 것이 핵심이다.🎃🎃🎃
+ 서블릿 컨테이너까지 예외가 올라가면 복잡하고 지저분하게 추가 프로세스가 실행된다. 
+ 반면에 `ExceptionResolver` 를 사용하면 예외처리가 상당히 깔끔해진다.
+ 스프링이 제공하는 `ExceptionResolver` 들이 있다.








