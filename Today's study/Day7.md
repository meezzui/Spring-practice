### REST란?
+ Representational State Transfer의 약자이다.
+ 구성: 자원(Resource): URI, 행위(Verb): HTTP Method, 표현(Representations)
+ 즉 REST는 URI를 통해 자원을 표시하고, HTTP Method를 이용하여 해당 자원의 행위를 규정하여 그 결과를 받는 것을 말한다.
+ HTTP Method는 크게 GET, POST, PUT, DELETE가 대표적이며, 보통 CRUD에서 조회는 GET, 등록은 POST, 수정은 PUT, 삭제는 DELETE를 이용한다.
+ GET과 DELETE는 비교적 그 행위가 명확하지만, POST와 PUT을 구분하기 위해서는 멱등성의 개념을 알아야 한다.

### 멱등성(Idempotence)이란?
+ 여러번 수행해도 결과가 같음을 의미한다.
+ HTTP 메소드를 예를 들자면, GET, PUT, DELETE는 같은 경로로 여러 번 호출해도 결과가 같다.
+ 그러나 POST는 매 호출마다 새로운 데이터가 추가된다. 
+ 따라서, POST 연산은 결과가 Idempotent하지 않지만, PUT은 반복 수행해도 그 결과가 Idempotent 하다. 
+ PUT vs POST
  + PUT
    + PUT메서드는 멱등원 이다.
    + 요청을 여러 번 재시도하는 경우 단일 요청 호출과 동일해야 한다.
    + 이미 리소스 컬렉션의 일부인 단일 리소스를 수정하려는 경우 사용 한다.
    + 즉,PUT은 리소스 전체를 대체한다. 전체적인 수정이 필요할 때만 사용한다.🎃
    + 요청이 리소스의 일부를 업데이트하는 경우 PATCH를 사용한다.🎃
    + 일반적으로 실제로는 UPDATE 작업에 PUT사용 한다.

  + POST
    + POST멱 등성이 아니다.
    + 요청을 N번 재시도하면 서버에 생성된 N개의 서로 다른 URI가 있는 N개의 리소스가 생긴다.
    + 리소스 컬렉션 아래에 자식 리소스를 추가하려는 경우 사용한다.
    + 항상 CREATE 작업 에 사용 POST합니다 .


### PUT VS PATCH
+ PUT은 전체 내용을 수정할 때 사용한다.
+ PUT 요청 시 요청을 일부분만 보낸 경우 나머지는 default 값으로 수정되는게 원칙이므로, 바뀌지 않는 속성도 모두 보내야 한다.
+ 만약 전체가 아닌 일부만 전달할 경우, 전달한 필드외 모두 null 혹은 default 값처리되니 주의해야한다.
```java
//수정된 값만 보낼 경우, 보내지 않은 데이터는 null로 변경되어 버린다.

PUT /users/1 
{ "age": 15 } 

HTTP/1.1 200 OK 
{ "name": null, "age": 15 }
======================================================================

//변경되지 않는 데이터도 모두 전달해야한다. 
PUT /users/1 
{ "name": "gildong" "age": 15 } 

HTTP/1.1 200 OK 
{ "name": "gildong", "age": 15 }
```
+ PATCH은 자원의 일부를 수정할 때 사용한다.
```java
PATCH /users/1
{ "age": 15 } 

HTTP/1.1 200 OK 
{ "name": "gildong", "age": 15 }
```

### REST API 기본규칙
---
#### REST API란?
+ 리소스 중심으로 설계하고 기능에 맞게 HTTP Method 사용하여 설계된 API이다.
+ HTTP Method 
  + GET : 지정된 URL에서 리소스의 표현을 조회
  + POST : 지정된 URL에 신규 리소스를 생성
  + PUT : 지정된 URL에 리소스를 생성하거나 업데이트
  + PATCH : 리소스의 부분 업데이트
  + DELETE : 지정된 URL의 리소스를 제거
+ 그 밖에 메소드
  + HEAD: GET과 유사하나 HTTP 메시지 헤더만 반송하고 데이터 내용을 돌려보내지 않음(헤더만 보겠다)
  + OPTION: 통신 옵션을 통지/조사
  + TRACE: 리퀘스트 라인과 헤더를 그대로 클라이언트에 반송(리퀘스트 치환상태를 조사할 때 사용)
  + CONNECT: 암호화된 메시지를 프록시로 전송

#### Rest API 세부규칙
+ 슬래시(/)는 계층 관계를 나타낸다. `http://www.example.com/fruits/banana`
+ URI의 마지막엔 슬래시(/)를 사용하지 않는다. 
  + URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며 URI가 다르다는 것은 리소스가 다르다는 것이고, 역으로 리소스가 다르면 URI도 달라져야 한다.
  + REST API는 분명한 URI를 만들어 통신을 해야 하기 때문에 혼동을 주지 않도록 URI 경로의 마지막에는 슬래시(/)를 사용하지 않는다.
  `http://www.example.com/fruits/banana/ (X)`
+ 가독성을 높이기 위해 하이픈(-)을 사용할 수 있으나 언더바(_)를 사용하진 않는다.
```
http://www.example.com/tropical_fruits/banana/ (X)
http://www.example.com/tropical-fruits/banana/ (O)
```
+ URI 경로에는 소문자를 쓴다.
```
http://api.example.restapi.org/my-folder/my-doc
HTTP://API.EXAMPLE.RESTAPI.ORG/my-folder/my-doc

위의 두 URI는 같은 URI이다. 호스트에서는 대소문자를 구별하지 않기 때문이다.

http://api.example.restapi.org/my-folder/my-doc
http://api.example.restapi.org/My-Folder/my-doc

하지만 위의 두 URI는 다른 URI이다. 뒤에 붙는 path가 대소문자로 구분되기 때문이다.
```
+ 카멜방식이 아닌 소문자를 사용한다.(주소에선 대소문자를 구별함)
```
//안좋은 예
https://localhost:8080/ddoriya/user/getUsers 

//좋은 예 
https://localhost:8080/ddoriya/user/get-users
```
+ 행위는 포함하지 않는다.
  + 행위는 URL대신 Method를 사용하여 전달한다.(GET, POST, PUT, DELETE 등)
  ```
  //나쁜 예
  POST http://restapi.example.com/users/1/delete-post/1
  
  //좋은 예
  DELETE http://restapi.example.com/users/1/posts/1
  ```
+ 파일 확장자는 URI에 포함시키지 않는다. `http://restapi.example.com/members/soccer/345/photo.jpg (X)`
+ 리소스 간의 관계를 표현하는 방법
```
/리소스명/리소스 ID/관계가 있는 다른 리소스명
GET : /users/{userid}/devices 

GET : /users/{userid}/likes/devices (관계명이 애매하거나 구체적 표현이 필요할 때)
```
+ 가급적 전달하고자하는 자원의 명사를 사용하되, 컨트롤 자원을 의미하는 경우 예외적으로 동사를 허용한다.
```
//나쁜 예
http://restapi.example.com/posts/duplicating

//좋은 예
http://restapi.example.com/posts/duplicate
```
#### Header 설정하기
+ 헤더를 설정하는 부분은 필수적이지는 않지만, 응답으로 돌려줄 때 프론트엔드에서 참고하기 좋다.
+ HATEOAS(Hypermedia as the Engine of Application State)
  + 어떤 자원에 대해 처리를 했을 때, 그에 대한 응답으로 프론트엔드에서 처리하기 용이하도록 자원에 대한 `Links`를 제공한다는 것이다.
  + 예를들어 `POST /posts {"title":" Hello, world"}`요청을 보냈다고 가정해보자. 
  + 이 요청은 새로운 포스트를 만들라는 의미이며 아래와 같이 링크정보를 포함하여 응답할 수 있게된다. 
  + 이를 사용하면 RESTful API 스타일을 따르는 자원 접근방식을 통해 클라이언트가 자원을 조작할 수 있게되고 클라이언트에서 URL 을 직접 구성할 필요가 사라진다. 
  + 그저 응답에서 제공하는 정보만 참조하면 되기 때문이다.
  ```java
  { 
    "id" : 1,
    "title : "Hello, world",
    "createdAt" : "2021-09-15 12:00:00",
    "links":[
        {
          "rel":self,
          "href":"http://api.test.com/posts/1",
          "method": "GET"
         }
      ]
  }
  ```
  + rel부분은 self를 제외하곤 임의로 정해서 사용할 수 있으며 이외에도 더 자세한 정보를 제공하는 링크인 more_info, 요청 예시를 제공하는 body등을 사용할 수 있다.







