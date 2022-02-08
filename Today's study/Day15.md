### 오늘의 일기
---
#### API 예외 처리 - 스프링이 제공하는 ExceptionResolver
+ 스프링 부트가 기본으로 제공하는 'ExceptionResolver'
+ `HandlerExceptionResolverComposite` 에 다음 순서로 등록한다.
  + `ExceptionHandlerExceptionResolver`
    + @ExceptionHandler 을 처리한다. API 예외 처리는 대부분 이 기능으로 해결한다. 
  + `ResponseStatusExceptionResolver`
    + HTTP 상태 코드를 지정해준다. 
    + 예) @ResponseStatus(value = HttpStatus.NOT_FOUND)
  + `DefaultHandlerExceptionResolver` 
    + 우선 순위가 가장 낮다.
    + 스프링 내부 기본 예외를 처리한다.


#### ResponseStatusExceptionResolver
+ ResponseStatusExceptionResolver 는 예외에 따라서 HTTP 상태 코드를 지정해주는 역할을 한다.
+ 두 가지 경우가 있다.
  + `@ResponseStatus`가 달려있는 예외
    + HTTP 상태 코드를 변경해준다.
    + Controller나 Exception에 사용하여 status 정보를 설정하여 리턴해 준다.
  + `ResponseStatusException` 예외
    + @ResponseStatus 는 개발자가 직접 변경할 수 없는 예외에는 적용할 수 없다. (애노테이션을 직접 넣어야 하는데, 내가 코드를 수정할 수 없는 라이브러리의 예외 코드 같은 곳에는 적용할 수 없다.)
    + 추가로 애노테이션을 사용하기 때문에 조건에 따라 동적으로 변경하는 것도 어렵다. 
    + 이때는 ResponseStatusException 예외를 사용하면 된다.

#### DefaultHandlerExceptionResolver
+ DefaultHandlerExceptionResolver 는 스프링 내부에서 발생하는 스프링 예외를 해결한다.
+ 대표적으로 파라미터 바인딩 시점에 타입이 맞지 않으면 내부에서 `TypeMismatchException` 이 발생하는데, 이 경우 예외가 발생했기 때문에 그냥 두면 서블릿 컨테이너까지 오류가 올라가고, 결과적으로 500 오류가 발생한다.
+ 그런데 파라미터 바인딩은 대부분 클라이언트가 HTTP 요청 정보를 잘못 호출해서 발생하는 문제이다. 
+ HTTP 에서는 이런 경우 HTTP 상태 코드 400을 사용하도록 되어 있다.
+ `DefaultHandlerExceptionResolver` 는 이것을 500 오류가 아니라 HTTP 상태 코드 400 오류로 변경한다.


#### HTML 화면 오류 vs API 오류
+ 웹 브라우저에 HTML 화면을 제공할 때는 오류가 발생하면 `BasicErrorController` 를 사용하는게 편하다.
+ 이때는 단순히 5xx, 4xx 관련된 오류 화면을 보여주면 된다. BasicErrorController 는 이런 메커니즘을 모두 구현해두었다.
+ 그런데 API는 각 시스템 마다 응답의 모양도 다르고, 스펙도 모두 다르다. 예외 상황에 단순히 오류 화면을 보여주는 것이 아니라, 예외에 따라서 각각 다른 데이터를 출력해야 할 수도 있다. 
+ 그리고 같은 예외라고 해도 어떤 컨트롤러에서 발생했는가에 따라서 다른 예외 응답을 내려주어야 할 수 있다. 한마디로 매우 세밀한 제어가 필요하다.
+ 예를 들어,상품 API와 주문 API는 오류가 발생했을 때 응답의 모양이 완전히 다를 수 있다.
+ 즉, BasicErrorController 를 사용하거나 HandlerExceptionResolver 를 직접 구현하는 방식으로 API 예외를 다루기는 쉽지 않다는 것이다.

#### @ExceptionHandler
+ 스프링은 API 예외 처리 문제를 해결하기 위해 `@ExceptionHandler` 라는 애노테이션을 사용하는 매우 편리한 예외 처리 기능을 제공하는데, 이것이 바로 `ExceptionHandlerExceptionResolver` 이다. 
+ 스프링은 `ExceptionHandlerExceptionResolver` 를 기본으로 제공하고, 기본으로 제공하는 `ExceptionResolver` 중에 우선순위도 가장 높다. 
+ 실무에서 API 예외 처리는 대부분 이 기능을 사용한다.🎃🎃🎃
+ `@ExceptionHandler` 예외 처리 방법
  + @ExceptionHandler 애노테이션을 선언하고, 해당 컨트롤러에서 처리하고 싶은 예외를 지정해주면 된다. 
  + 해당 컨트롤러에서 예외가 발생하면 이 메서드가 호출된다.
  + 지정한 예외 또는 그 예외의 자식 클래스는 모두 잡을 수 있다.🎃

+ 우선순위
  + 스프링의 우선순위는 항상 자세한 것이 우선권을 가진다.
  + 예)
  ```java
  @ExceptionHandler(부모예외.class)
  public String 부모예외처리()(부모예외 e) {}
  @ExceptionHandler(자식예외.class)
  public String 자식예외처리()(자식예외 e) {}
  ```
  + `@ExceptionHandler` 에 지정한 부모 클래스는 자식 클래스까지 처리할 수 있다. 
  + 따라서 자식예외가 발생하면 부모예외처리() , 자식예외처리() 둘다 호출 대상이 된다. 
  + 그런데 둘 중 더 자세한 것이 우선권을 가지므로 자식예외처리() 가 호출된다. 
  + 물론 부모예외가 호출되면 부모예외처리() 만 호출 대상이 되므로 부모예외처리() 가 호출된다.  

🎈 `@ExceptionHandler`를 사용해서 예외를 깔끔하게 처리할 수 있게 되었지만, 정상 코드와 예외 처리 코드가 하나의 컨트롤러에 섞여 있다.  
🎈 `@ControllerAdvice `또는 `@RestControllerAdvice`를 사용하면 둘을 분리할 수 있다.  

#### @ControllerAdvice
+ `@ControllerAdvice` 는 대상으로 지정한 여러 컨트롤러에 `@ExceptionHandler` , `@InitBinder` 기능을 부여해주는 역할을 한다.
+ `@ControllerAdvice` 에 대상을 지정하지 않으면 모든 컨트롤러에 적용된다. (글로벌 적용)
+ `@RestControllerAdvice` 는 `@ControllerAdvice` 와 같고, `@ResponseBody` 가 추가되어 있다. 
+ 즉, `@Controller`와 `@RestController`의 차이와 같다.  

🎉 정리하자면 `@ExceptionHandler` 와 `@ControllerAdvice` 를 조합하면 예외를 깔끔하게 해결할 수 있다!!!! 🎉




