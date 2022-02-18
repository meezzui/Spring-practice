### URI 과 URL 차이점
+ URL은 특정 리소스의 위치를 가리키고 URI는 특정 리소스를 식별하는 식별자이다.
+ 즉, URL은 웹 주소를 가리킨다. 그리고 URI는 내 위치나 비밀번호와 같은 정보를 알 수 없다.
+ 포함관계 -> `URL⊂ URI`
+ 예) `http://opentutorials.org:3000/main?id=HTML&page=12` 이 주소에서  `http://opentutorials.org:3000/main`는 URL이라고 하고 `http://opentutorials.org:3000/main?id=HTML&page=12`를 URI라고 한다. 
+ `?id=HTML&page=12` 이 부분이 위치를 나타내는 것이 아니라 id값이 HTML이고 page가 12인 것을 나타내주는 식별하는 부분이기 때문이다.
+ 보통은 `http://opentutorials.org:3000/main?id=HTML&page=12`이 전체를 url이라고 부르는데 uri라고 부르는 것이 더 정확한 표현이다.
  
### JDBC란?
+ JDBC는 DB에 접근할 수 있도록 자바에서 제공하는 API이다.

### JPA란?
+ JPA는 JAVA 애플리케이션과 JDBC 사이에서 동작한다.
+ 개발자가 JPA를 사용하면 JPA 내부에서 JDBC API를 사용하여 SQL을 호출하여 DB와 통신한다.
+ 자바 객체와 DB 테이블 사이의 매핑 설정을 통해 SQL을 생성한다.
+ 객체를 통해 쿼리를 작성할 수 있는 JPQL(Java Persistence Query Language)를 지원

참고📢 SQL에서 `DISTINCT(중복제거)`와 비슷한 기능을 하는 것은 `GROUP BY`이다.   
참고📢 Oracle 과 MySQL 차이점  
+ oracle보다 mysql이 더 편리하다.
+ 예를들어, 오라클에서는 페이징 처리를 위해 rownum 으로 레코드에 번호를 부여하고 WHERE 절에 조건을 걸어서 일일이 페이징 처리하는 반면에 mysql은 limit키워드를 사용하여 간단하게 처리할 수 있다.
+ limit 사용방법
  + `LIMIT 행_갯수 OFFSET 시작행;` : 첫 행은 0부터 시작, OFFSET은 필수가 아니므로 넣지 않으면 0으로 처리되서 첫 행부터 나오게 된다.
  + `LIMIT 시작행, 끝행` : 예) `LIMIT 3, 4;` 이렇게 사용해주면 4번째와 5번째 행만 나오게 된다. why? 첫 행은 0부터 시작하기 때문에 한칸씩 플러스된다고 생각하면 된다.😉



### 오늘의 일기
---
#### API란?
+ API는 프로그램들이 서로 상호작용하는 것을 도와주는 매개체 역할을 한다.
+ 어떤 서버의 특정한 부분에 접속해서 그 안에 있는 데이터와 서비스를 이용할 수 있게 해준다.

#### REST란?
+ 네트워크 상에서 Client와 Server 사이의 통신 방식 중 하나로 문서,그림,데이터 등을 JSON이나 XML로 데이터를 주고 받는 것을 의미한다.
+ 예) 예를 들어 '/boards/12'은 게시물 중에서 12번이라는 고유한 의미를 가지도록 설계하고, 이에 대한 처리는 GET, PUT과 같은 방식으로 전달함
+ 즉, Http Protocol을 이용하는 잘 짜여진 연결장치??라고 생각하면 된다.(이건 내 개인적인 생각..🙄)

#### REST 구성 요소
+ URL
  + Client는 URL을 이용해 자원을 지정, 해당 자원의 상태에 대한 조작을 Server에 요청
+ HTTP Method
  + HTTP 프로토콜의 Method를 사용
  + 메서드 종류 - GET, POST, PUT, DELETE
+ JSON 혹은 XML을 통해 데이터 교환


#### RestAPI란?
+ REST의 원리를 따르는 API를 의미한다.
+ 상태전달을 목적으로 한다.


#### RestfulAPI이란?
+ Rest의 구현원칙을 지키면서 Http 메소드와 URI를 사용하는 것이다.
+ 이 뿐만 아니라 Rest의 제약조건을 잘 지킨 API를 'RestfulAPI'라고 한다.
+ Rest의 제약조건
  + `클라이언트-서버 형태` : 서버와 클라이언트의 역할을 분명히 함으로써 서버의 구성 요소가 단순화 되고 확장성이 향상되어 여러 플랫폼을 지원할 수 있다.
  + `무상태성` : 서버에 클라이언트 상태 정보를 저장하지 않는 것을 말한다. 단순히 들어오는 요청만을 처리하여 구현한다. 클라이언트의 모든 요청은 서버가 요청을 알아듣는 데 필요한 모든 정보를 알고 있어야 한다. 
  + `캐시 기능` : HTTP의 기능 중 캐시 기능을 말한다. 
 



















