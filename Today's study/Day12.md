### 오늘의 일기
---
#### 스프링 인터셉터
+ 스프링 인터셉터도 서블릿 필터와 같이 웹과 관련된 공통 관심 사항을 효과적으로 해결할 수 있는 기술이다.
+ 서블릿 필터가 서블릿이 제공하는 기술이라면, 스프링 인터셉터는 스프링 MVC가 제공하는 기술이다. 
+ 둘다 웹과 관련된 공통 관심 사항을 처리하지만, 적용되는 순서와 범위, 그리고 사용방법이 다르다.

#### 스프링 인터셉터 흐름
+ `HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러`
+ 스프링 인터셉터는 디스패처 서블릿과 컨트롤러 사이에서 컨트롤러 호출 직전에 호출 된다.
+ 스프링 인터셉터는 스프링 MVC가 제공하는 기능이기 때문에 결국 디스패처 서블릿 이후에 등장하게 된다. 
+ 스프링 MVC의 시작점이 디스패처 서블릿이라고 생각하면 이해가 편하다.
+ 스프링 인터셉터에도 URL 패턴을 적용할 수 있는데, 서블릿 URL 패턴과는 다르고, 매우 정밀하게 설정할 수 있다.

#### 스프링 인터셉터 제한
```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러 //로그인 사용자
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터(적절하지 않은 요청이라 판단, 컨트롤러 호출 X) // 비 로그인 사용자
```
+ 인터셉터에서 적절하지 않은 요청이라고 판단하면 거기에서 끝을 낼 수도 있다. 
+ 그래서 로그인 여부를 체크하기에 딱 좋다.(굿이에유😉)

#### 스프링 인터셉터 체인
+ `HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러`
+ 스프링 인터셉터는 체인으로 구성되는데, 중간에 인터셉터를 자유롭게 추가할 수 있다. 
+ 예를 들어서 로그를 남기는 인터셉터를 먼저 적용하고, 그 다음에 로그인 여부를 체크하는 인터셉터를 만들 수 있다.  

🎈 이처럼 서블릿 필터와 호출 되는 순서만 다르고, 제공하는 기능은 비슷하다. 그러나 스프링 인터셉터는 서블릿 필터보다 편리하고, 더 정교하고 다양한 기능을 지원한다.  


#### 스프링 인터셉터 인터페이스
+ 스프링의 인터셉터를 사용하려면 `HandlerInterceptor `인터페이스를 구현하면 된다.
+ HandlerInterceptor란?
  + 특정한 URI 호출을 '가로채는' 역할을 한다. 이를 이용하여 기존 컨트롤러의 로직을 수정하지 않고도, 사전이나 사후 제어가 가능하다.
  + 서블릿 필터의 경우 단순하게 doFilter() 하나만 제공된다.
  + 그러나 인터셉터는 컨트롤러 호출 전( preHandle ), 호출 후( postHandle ), 요청 완료 이후(afterCompletion)와 같이 단계적으로 잘 세분화 되어 있다.
    + `preHandle(request, response, handler)` : 지정된 컨트롤러의 동작 이전에 수행할 동작 (사전 제어).
    + `postHandle(request, response, handler, modelAndView)` : 지정된 컨트롤러의 동작 이후에 처리할 동작 (사후 제어). Spring MVC의 Dispatcher Servlet이 화면을 처리하기 전에 동작.
    + `afterCompletion(request, reponse, handler, exception)` : Dispatcher Servlet의 화면 처리가 완료된 이후 처리할 동작.
  + 서블릿 필터의 경우 단순히 request , response 만 제공했지만, 인터셉터는 어떤 컨트롤러( handler )가 호출되는지 호출 정보도 받을 수 있다. 
  + 그리고 어떤 modelAndView 가 반환되는지 응답 정보도 받을 수 있다.

#### 스프링 인터셉터 호출 흐름
<img width="500" src="https://user-images.githubusercontent.com/86812098/152630563-95453742-d1f0-4836-a09c-1bba12ea6322.png">

+ 정상 흐름
  + `preHandle` : 컨트롤러 호출 전에 호출된다. (더 정확히는 핸들러 어댑터 호출 전에 호출된다.)
    + preHandle 의 응답값이 true 이면 다음으로 진행하고, false 이면 더는 진행하지 않는다. 
    + false인 경우 나머지 인터셉터는 물론이고, 핸들러 어댑터도 호출되지 않는다. 그림에서 1번에서 끝나 버린다.
  + `postHandle` : 컨트롤러 호출 후에 호출된다. (더 정확히는 핸들러 어댑터 호출 후에 호출된다.)
  + `afterCompletion` : 뷰가 렌더링 된 이후에 호출된다.

+ 스프링 인터셉터 예외 상황
<img width="500" src="https://user-images.githubusercontent.com/86812098/152630657-d11fbb59-6152-4a87-b64d-e048c57a9e16.png">

+ 예외가 발생시
  + `preHandle` : 컨트롤러 호출 전에 호출된다.
  + `postHandle` : 컨트롤러에서 예외가 발생하면 `postHandle`은 호출되지 않는다.
  + `afterCompletion` : `afterCompletion` 은 항상 호출된다. 이 경우 예외를 파라미터로 받아서 어떤 예외가 발생했는지 로그로 출력할 수 있다.
    + 예외가 발생하면 `postHandle()` 는 호출되지 않으므로 예외와 무관하게 공통 처리를 하려면 `afterCompletion()` 을 사용해야 한다.
    + 예외가 발생하면 `afterCompletion()`에 예외 정보를 포함해서 호출된다.  
🎈 즉, 인터셉터는 스프링 MVC 구조에 특화된 필터 기능을 제공한다고 이해하면 된다. 스프링 MVC를 사용하고, 특별히 필터를 꼭 사용해야 하는 상황이 아니라면 인터셉터를 사용하는 것이 더 편리하다.  

#### 스프링 인터셉터 - 요청 로그
+ `String uuid = UUID.randomUUID().toString()` 요청 로그를 구분하기 위한 uuid 를 생성한다.
+ `request.setAttribute(LOG_ID, uuid)` 
  + 서블릿 필터의 경우 지역변수로 해결이 가능하지만, 스프링 인터셉터는 호출 시점이 완전히 분리되어 있다. 
  + 따라서 preHandle 에서 지정한 값을 postHandle , afterCompletion 에서 함께 사용하려면 어딘가에 담아두어야 한다.(오호라🧐)
  + LogInterceptor 도 싱글톤 처럼 사용되기 때문에 맴버변수를 사용하면 위험하다.🎃
  + 그래서 request 에 담아두었다. 이 값은 afterCompletion 에서 `request.getAttribute(LOG_ID)` 로 찾아서 사용한다.
```java
if (handler instanceof HandlerMethod) {
     HandlerMethod hm = (HandlerMethod) handler; //호출할 컨트롤러 메서드의 모든 정보가 포함되어 있다.
   }
     log.info("REQUEST [{}][{}][{}]", uuid, requestURI, handler);
     return true; //true 면 정상 호출이다. 다음 인터셉터나 컨트롤러가 호출된다.(false면 진행안함)
```

#### HandlerMethod
+ 핸들러 정보는 어떤 핸들러 매핑을 사용하는가에 따라 달라진다. 
+ 스프링을 사용하면 일반적으로 @Controller , @RequestMapping 을 활용한 핸들러 매핑을 사용하는데, 이 경우 핸들러 정보로 HandlerMethod 가 넘어온다.

#### ResourceHttpRequestHandler
+ @Controller 가 아니라 /resources/static 와 같은 정적 리소스가 호출 되는 경우 ResourceHttpRequestHandler가 핸들러 정보로 넘어오기 때문에 타입에 따라서 처리가 필요하다.

#### postHandle, afterCompletion
+ 종료 로그를 postHandle 이 아니라 afterCompletion 에서 실행한 이유는, 예외가 발생한 경우 postHandle 가 호출되지 않기 때문이다. 
+ afterCompletion 은 예외가 발생해도 호출 되는 것을 보장한다.

#### 인터셉터 등록
+ `addInterceptors` 메서드를 구현을 할 때 위와 같이 구현을 하며, 인터셉터 구현 시 메서드 체이닝을 되어있어 연속적으로 다른 추가 패턴도 등록할 수 있다.
  + `registry.addInterceptor(new LogInterceptor())` : 인터셉터를 등록한다.
+ `addPathPatterns` 적용할 url 패턴을 설정한다. *, **등을 사용한 URI Pattern String을 전송하거나, URI Pattern 배열을 전송할 수 있다.
  + `addPathPatterns("/**")` : 인터셉터를 적용할 URL 패턴을 지정한다.
+ `excludePathPatterns` 인터셉터를 제외할 url 패턴을 등록하는 메서드로써 해당 url로 접근 시에는 인터셉터를 적용하지 않게 된다.

#### 스프링의 URL 경로
+ 스프링이 제공하는 URL 경로는 서블릿 기술이 제공하는 URL 경로와 완전히 다르다. 
+ 더욱 자세하고, 세밀하게 설정할 수 있다. 
+ [참고문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html)

#### 스프링 인터셉터 - 인증 체크
+ 서블릿 필터와 비교해서 코드가 매우 간결하다. 
+ 인증이라는 것은 컨트롤러 호출 전에만 호출되면 된다. 따라서 preHandle 만 구현하면 된다.
+ 인터셉터를 적용하거나 하지 않을 부분은 addPathPatterns 와 excludePathPatterns 에 작성하면 된다.  

💙정리하자면 서블릿 필터와 스프링 인터셉터는 웹과 관련된 공통 관심사를 해결하기 위한 기술이다. 그런데 스프링 인터셉터가 개발자 입장에서 훨씬 편리하다. 특별한 문제가 없다면 인터셉터를 사용하는 것이 좋다.(이거쥐😎)💙  
