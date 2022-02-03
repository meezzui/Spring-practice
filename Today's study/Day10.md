### 로그인 처리하기 - 서블릿 HTTP 세션
---
+ 세션이라는 개념은 대부분의 웹 애플리케이션에 필요한 것이다.
+ 서블릿은 세션을 위해 HttpSession 이라는 기능을 제공한다.

#### HttpSession 소개
+ 서블릿을 통해 HttpSession 을 생성하면 다음과 같은 쿠키를 생성한다. 
+ 쿠키 이름이 JSESSIONID 이고, 값은 추정 불가능한 랜덤 값이다.  
`Cookie: JSESSIONID=5B78E23B513F50164D6FDD8C97B0AD05`

#### HttpSession 사용1
```java
//로그인 성공 처리
//세션이 있으면 있는 세션 반환, 없으면 신규 세션 생성
 HttpSession session = request.getSession();

//세션에 로그인 회원 정보 보관
 session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember);
 return "redirect:/";
```
+ 세션 생성과 조회
  + 세션을 생성하려면 `request.getSession(true)` 를 사용하면 된다.
  + `request.getSession()` : 신규 세션을 생성하는 `request.getSession(true)` 와 동일하다.
  + `request.getSession(true)`
    + 세션이 있으면 기존 세션을 반환한다.
    + 세션이 없으면 새로운 세션을 생성해서 반환한다.
  + `request.getSession(false)`
    + 세션이 있으면 기존 세션을 반환한다.
    + 세션이 없으면 새로운 세션을 생성하지 않는다. null 을 반환한다.

+ 세션에 로그인 회원 정보 보관
  + `session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember);`
  + 세션에 데이터를 보관하는 방법은 request.setAttribute(..) 와 비슷하다. 하나의 세션에 여러 값을 저장할 수 있다.

+ 세션 삭제
```java
//세션을 삭제한다.
 HttpSession session = request.getSession(false);
   if (session != null) {
      session.invalidate();
   }
```
+ session.invalidate() : 세션을 제거한다.

#### HttpSession 사용2
+ @SessionAttribute
  + 스프링은 세션을 더 편리하게 사용할 수 있도록 @SessionAttribute 을 지원한다.
  + 세션을 찾고, 세션에 들어있는 데이터를 찾는 번거로운 과정을 스프링이 한번에 편리하게 처리해준다.
  + 이미 로그인 된 사용자를 찾을 때는 다음과 같이 사용하면 된다. 참고로 이 기능은 세션을 생성하지 않는다.  
  + `@SessionAttribute(name = "loginMember", required = false) Member loginMember`

+ jsessionid 감추기
  + jsessionid를 탈취당하면 사용자 ID, 비밀번호를 몰라도 접근이 가능하게 된다.
  + 로그인을 처음 시도하면 URL이 jsessionid를 포함하고 있다.
  + 서버 입장에서 웹 브라우저가 쿠키를 지원하는지 하지 않는지 최초에는 판단하지 못하므로, 쿠키 값도 전달하고, URL에 jsessionid도 함께 전달한다.
  + URL 전달 방식을 끄고 항상 쿠키를 통해서만 세션을 유지하고 싶으면 `application.properties`에 `server.servlet.session.tracking-modes=cookie`를 추가해주면 된다.
  + 이렇게 하면 URL에 jsessionid가 노출되지 않는다.

+ 세션 타임아웃 설정
  + 세션은 사용자가 로그아웃을 직접 호출해서 session.invalidate() 가 호출 되는 경우에 삭제된다. 
  + 그런데 대부분의 사용자는 로그아웃을 선택하지 않고, 그냥 웹 브라우저를 종료한다. 
  + 문제는 HTTP가 비연결성(ConnectionLess)이므로 서버 입장에서는 해당 사용자가 웹 브라우저를 종료한 것인지 아닌지를 인식할 수 없다. 
  + 따라서 서버에서 세션 데이터를 언제 삭제해야 하는지 판단하기가 어렵다.
  + 이 경우 남아있는 세션을 무한정 보관하면 다음과 같은 문제가 발생할 수 있다. 
    + 세션과 관련된 쿠키( JSESSIONID )를 탈취 당했을 경우 오랜 시간이 지나도 해당 쿠키로 악의적인 요청을 할 수 있다.
    + 세션은 기본적으로 메모리에 생성된다. 메모리의 크기가 무한하지 않기 때문에 꼭 필요한 경우만 생성해서 사용해야 한다. 10만명의 사용자가 로그인하면 10만개의 세션이 생성되는 것이다.
+ 세션의 종료 시점
  + 세션의 종료 시점을 어떻게 정하면 좋을까? 세션 생성 시점으로부터 30분 정도로 잡으면 될 것 같다. 
  + 그런데 문제는 30분이 지나면 세션이 삭제되기 때문에, 열심히 사이트를 돌아다니다가 또 로그인을 해서 세션을 생성해야 한다.(그러니까 30분 마다 계속 로그인해야 하는 번거로움이 발생한다.)
  + 더 나은 대안은 세션 생성 시점이 아니라 사용자가 서버에 최근에 요청한 시간을 기준으로 30분 정도를 유지해주는 것이다. 
  + 이렇게 하면 사용자가 서비스를 사용하고 있으면, 세션의 생존 시간이 30분으로 계속 늘어나게 된다. 따라서 30분 마다 로그인해야 하는 번거로움이 사라진다.


+ 세션 타임아웃 설정 방법
  + 스프링 부트로 글로벌 설정
    + `application.properties`에 `server.servlet.session.timeout=60` : 60초, 기본은 1800(30분)
    + 글로벌 설정은 분 단위로 설정해야 한다. 60(1분), 120(2분), ...
  + 특정 세션 단위로 시간 설정
    + `session.setMaxInactiveInterval(1800); //1800초`

+ 세션 타임아웃 발생
  + 세션의 타임아웃 시간은 해당 세션과 관련된 JSESSIONID 를 전달하는 HTTP 요청이 있으면 현재 시간으로 다시 초기화 된다. 
  + 이렇게 초기화 되면 세션 타임아웃으로 설정한 시간동안 세션을 추가로 사용할 수 있다.
  + `session.getLastAccessedTime()` : 최근 세션 접근 시간
  + `LastAccessedTime` 이후로 timeout 시간이 지나면, WAS가 내부에서 해당 세션을 제거한다.

+ 이처럼 서블릿의 HttpSession 이 제공하는 타임아웃 기능 덕분에 세션을 안전하고 편리하게 사용할 수 있다. 
   
+ 실무에서 주의할 점
  + 세션에는 최소한의 데이터만 보관해야 한다는 점이다.
    + 보관한 데이터 용량 * 사용자수로 세션의 메모리 사용량이 급격하게 늘어나서 장애로 이어질 수 있다. 
  + 세션의 시간을 적당한 시간으로 선택하는 것이 필요하다.
    + 세션의 시간을 너무 길게 가져가면 메모리 사용이 계속 누적 될 수 있다.
    + 기본이 30분이라는 것을 기준으로 고민하면 된다.









