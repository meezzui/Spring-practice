### 오늘의 일기
---
#### 스프링 부트 - 오류 페이지1
+ ErrorPage 를 자동으로 등록한다. 이때 /error 라는 경로로 기본 오류 페이지를 설정한다.
  + `new ErrorPage("/error")` , 상태코드와 예외를 설정하지 않으면 기본 오류 페이지로 사용된다.
  + 서블릿 밖으로 예외가 발생하거나, response.sendError(...) 가 호출되면 모든 오류는 /error 를 호출하게 된다. 
+ `BasicErrorController` 라는 스프링 컨트롤러를 자동으로 등록한다.
  + ErrorPage 에서 등록한 /error 를 매핑해서 처리하는 컨트롤러다.
+ 참고📢 `ErrorMvcAutoConfiguration`이라는 클래스가 오류 페이지를 자동으로 등록하는 역할을 한다.
+ 개발자는 오류 페이지만 등록!!
  + `BasicErrorController` 는 기본적인 로직이 모두 개발되어 있다.
  + 개발자는 오류 페이지 화면만 `BasicErrorController` 가 제공하는 룰과 우선순위에 따라서 등록하면 된다. 
  + 정적 HTML이면 정적 리소스, 뷰 템플릿을 사용해서 동적으로 오류 화면을 만들고 싶으면 뷰 템플릿 경로에 오류 페이지 파일을 만들어서 넣어두기만 하면 된다.

+ 뷰 선택 우선순위(`BasicErrorController` 의 처리 순서)
  + 뷰 템플릿
    + resources/templates/error/500.html
    + resources/templates/error/5xx.html
  + 정적 리소스( static , public )
    + resources/static/error/404.html
    + resources/static/error/4xx.html
  + 적용 대상이 없을 때 뷰 이름( error )
    + resources/templates/error.html
+ 해당 경로 위치에 HTTP 상태 코드 이름의 뷰 파일을 넣어두면 된다.
+ 뷰 템플릿이 정적 리소스보다 우선순위가 높고, 404, 500처럼 구체적인 것이 5xx처럼 덜 구체적인 것 보다 우선순위가 높다.
+ 5xx, 4xx 라고 하면 500대, 400대 오류를 처리해준다.

#### 스프링 부트 - 오류 페이지2
+ `BasicErrorController`
  + `BasicErrorController` 컨트롤러는 다음 정보를 model에 담아서 뷰에 전달한다. 
  + 뷰 템플릿은 이 값을 활용해서 출력할 수 있다.
  + 오류 관련 내부 정보들을 고객에게 노출하는 것은 좋지 않다. 
  + 고객이 해당 정보를 읽어도 혼란만 더해지고, 보안상 문제가 될 수도 있다.
  + 그래서 `BasicErrorController` 오류 컨트롤러에서 다음 오류 정보를 model 에 포함할지 여부 선택할 수 있다.

+ `application.properties`에 추가 
  + `server.error.include-exception=false` : exception 포함 여부( true , false )
  + `server.error.include-message=never` : message 포함 여부
  + `server.error.include-stacktrace=never` : trace 포함 여부
  + `server.error.include-binding-errors=never` : errors 포함 여부

+ 기본 값이 `never`인 부분은 다음 3가지 옵션을 사용할 수 있다.
  + `never`, `always`, `on_param`
  + `never` : 사용하지 않음
  + `always` :항상 사용
  + `on_param` : 파라미터가 있을 때 사용
    + on_param 은 파라미터가 있으면 해당 정보를 노출한다. 디버그 시 문제를 확인하기 위해 사용할 수 있다. 
    + 그런데 이 부분도 개발 서버에서 사용할 수 있지만, 운영 서버에서는 권장하지 않는다.
    + on_param 으로 설정하고 다음과 같이 HTTP 요청시 파라미터를 전달하면 해당 정보들이 model 에 담겨서 뷰 템플릿에서 출력된다.
    + 예) `message=&errors=&trace=` -> `http://localhost:8080/error-ex?message=&errors=&trace=`
+ 실무에서는 이것들을 노출하면 안된다!! 
  + 사용자에게는 이쁜 오류 화면과 고객이 이해할 수 있는 간단한 오류메시지를 보여주고 오류는 서버에 로그로 남겨서 로그로 확인해야 한다.🎃

#### 스프링 부트 오류 관련 옵션
+ `server.error.whitelabel.enabled=true` : 오류 처리 화면을 못 찾을 시, 스프링 `whitelabel` 오류페이지 적용
+ `server.error.path=/error` : 오류 페이지 경로, 스프링이 자동 등록하는 서블릿 글로벌 오류 페이지 경로와 `BasicErrorController` 오류 컨트롤러 경로에 함께 사용된다.
+ 참고📢 에러 공통 처리 컨트롤러의 기능을 변경하고 싶으면 `ErrorController` 인터페이스를 상속 받아서 구현하거나 `BasicErrorController` 상속 받아서 기능을 추가하면 된다.  

🎈정리하자면 스프링 부트가 기본으로 제공하는 오류 페이지를 활용하면 오류 페이지와 관련된 대부분의 문제는 손쉽게 해결할 수 있다.🎈  









