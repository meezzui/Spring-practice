### ConcurrentHashMap
+ HashMap을 thread-safe 하도록 만든 클래스가 ConcurrentHashMap이다.
+ 하지만 HashMap과는 다르게 key, value에 null을 허용하지 않는다.
+ 또한 putIfAbsent라는 메소드를 가지고 있다.
+ putIfAbsent 메소드는 키값이 존재하면 기존의 값을 반환하고 없다면 입력한 값을 저장한 뒤 반환한다.
+ HashMap 은 동시 요청에 안전하지 않는 반면 ConcurrentHashMap은 동시 요청에 안전하다.🎃


### 오늘의 일기
---
#### 세션으로 로그인 처리하기
+ 세션이란?
  + 중요한 정보를 모두 서버에 저장해야 한다. 그리고 클라이언트와 서버는 추정 불가능한 임의의 식별자 값으로 연결해야 한다.
  + 이렇게 서버에 중요한 정보를 보관하고 연결을 유지하는 방법을 세션이라 한다.
+ 세션 동작 방식
  + 로그인
    + 사용자가 loginId , password 정보를 전달하면 서버에서 해당 사용자가 맞는지 확인한다.
      <img width="454" alt="image" src="https://user-images.githubusercontent.com/86812098/152146348-33a888c8-5c81-40ac-9c89-e1c21babcf04.png">
   
  + 세션 생성
    + 세션 ID를 생성하는데, 추정 불가능해야 한다.
    + UUID는 추정이 불가능하다. (Cookie: mySessionId=zz0101xx-bab9-4b92-9b32-dadb280f4b61)
    + 생성된 세션 ID와 세션에 보관할 값( memberA )을 서버의 세션 저장소에 보관한다.
    + 세션id를 응답 쿠키로 전달
    <img width="454" src="https://user-images.githubusercontent.com/86812098/152146911-12df0927-691a-4b76-899b-80975f4ed065.png">
  
  + 세션id를 응답 쿠키로 전달
    + 클라이언트와 서버는 결국 쿠키로 연결이 되어야 한다.
      + 서버는 클라이언트에 mySessionId 라는 이름으로 세션ID 만 쿠키에 담아서 전달한다.
      + 클라이언트는 쿠키 저장소에 mySessionId 쿠키를 보관한다.
    + 여기서 중요한 포인트!!!🎃
      + 회원과 관련된 정보는 전혀 클라이언트에 전달하지 않는다는 것이다.
      + 오직 추정 불가능한 세션 ID만 쿠키를 통해 클라이언트에 전달한다.
  + 클라이언트의 세션id 쿠키 전달
    + 클라이언트는 요청시 항상 mySessionId 쿠키를 전달한다.
    + 서버에서는 클라이언트가 전달한 mySessionId 쿠키 정보로 세션 저장소를 조회해서 로그인시 보관한 세션 정보를 사용한다.
+ 보안 문제 해결
  + 쿠키 값을 변조 가능 -> 예상 불가능한 복잡한 세션Id를 사용한다.
  + 쿠키에 보관하는 정보는 클라이언트 해킹시 털릴 가능성이 있다. -> 세션Id가 털려도 여기에는 중요한 정보가 없다.
  + 쿠키 탈취 후 사용 -> 해커가 토큰을 털어가도 시간이 지나면 사용할 수 없도록 서버에서 세션의 만료시간을 짧게(예: 30분) 유지한다. 또는 해킹이 의심되는 경우 서버에서 해당 세션을 강제로 제거하면 된다.
  
  
#### 세션관리가 제공하는 기능(세션 직접 만들어보기)
+ 세션 생성
  + sessionId 생성 (임의의 추정 불가능한 랜덤 값)
  + 세션 저장소에 sessionId와 보관할 값 저장
  + sessionId로 응답 쿠키를 생성해서 클라이언트에 전달
  ```java
  MockHttpServletResponse response = new MockHttpServletResponse();
  Member member = new Member();
  sessionManager.createSession(member, response);
  ```
+ 세션 조회
  + 클라이언트가 요청한 sessionId 쿠키의 값으로, 세션 저장소에 보관한 값 조회
  ```java
  //요청에 응답 쿠키 저장
   MockHttpServletRequest request = new MockHttpServletRequest();
   request.setCookies(response.getCookies());
   //세션 조회
   Object result = sessionManager.getSession(request);
   assertThat(result).isEqualTo(member);
  ```
+ 세션 만료
  + 클라이언트가 요청한 sessionId 쿠키의 값으로, 세션 저장소에 보관한 sessionId와 값 제거
  ```java
  sessionManager.expire(request);
  Object expired = sessionManager.getSession(request);
  assertThat(expired).isNull()
  ```
+ 사실 세션이라는 것이 뭔가 특별한 것이 아니라 단지 쿠키를 사용하는데, 서버에서 데이터를 유지하는 방법일 뿐이다.
+ 그런데 프로젝트마다 이러한 세션 개념을 직접 개발하는 것은 상당히 불편하다.
+ 그래서 서블릿도 세션개념을 지원한다.
+ 다음엔 서블릿이 공식 지원하는 세션을 알아볼 것이다. 서블릿이 공식 지원하는 세션은 세션을 일정시간 사용하지 않으면 해당 세션을 삭제하는 기능도 제공한다.

 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
