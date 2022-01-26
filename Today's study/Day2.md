### 자바에서 의존성 관계란?
+ 프로그래밍에서 의존 관계는 어떤 클래스에서 다른 클래스가 new를 통해서 인스턴스되느냐에 따라 결정된다.
+ 어떤 클래스의 기능이 다른 클래스의 기능에 의존하는 것이다.
+ 프로그래밍에서 의존 관계는 `new` 로 표현된다!!!

### 쿼리 스트링이란?
+ 사용자가 입력 데이터를 전달하는 방법중의 하나로, url 주소에 미리 협의된 데이터를 파라미터를 통해 넘기는 것을 말한다.
+ query parameters( 물음표 뒤에 = 로 연결된 key value pair 부분)을 url 뒤에 덧붙여서 추가적인 정보를 서버 측에 전달하는 것이다. 
+ 정해진 엔드포인트 주소 이후에 ?를 쓰는것으로 쿼리스트링이 시작함을 알린다
+ 파라미터가 여러개일 경우 &를 붙여 여러개의 파라미터를 넘길 수 있다. `엔드포인트주소/엔드포인트주소?파라미터=값&파라미터=값`
+ = 로 key 와 value 가 구분된다. 



### 오늘의 어노테이션
---
#### @RequiredArgsConstructor VS @Autowired
+ 공통점 : 둘다 의존성을 주입함.
+ @RequiredArgsConstructor :  어노테이션은 생성자 주입을 편리하게 도와주는 lombok 어노테이션이다.
+ @Autowired : 알아서 의존 객체(Bean) 를 찾아서 주입한다. 보통, 인터페이스 선언 시 사용된다.
+ `@RequiredArgsConstructor`를 쓰는 것을 더 권장!!!
  + 왜??
  + 순환 참조 방지- 개발을 하다 보면 여러 컴포넌트 간에 의존성이 생기는데 그중에서도 A가 B를 참조하고 B가 다시 A를 참조하는 순환 참도 발생할 수 있습니다.
  ```
  @Autowired 를 사용하면 'java.lang.StackOverflowError: null' 이런 오류 메시지가 전달되지만 실행은 된다.
  @RequiredArgsConstructor 을 사용하면 'BeanCurrentlyInCreationException' 오류가 발생하며 실행조차 되지 않는다.
  ```
  + 테스트 코드 작성 용이
  + 코드 악취 제거
  + 객체 변이 방지 (final 가능)

#### @PathVariable VS @RequestParam
```
Type 1 => http://127.0.0.1?index=1&page=2 -> 파라메터의 값과 이름을 함께 전달하는 방식으로 게시판 등에서 페이지 및 검색 정보를 함께 전달하는 방식을 사용할 때 많이 사용.
Type 2 => http://127.0.0.1/index/1 -> Rest api에서 값을 호출할 때 주로 많이 사용.
```
+ @RequestParam : 단일 파라미터를 전달받을 때 사용.
  + Type1 경우
  + Controller 단에서 사용.
  + @RequestParam 어노테이션의 괄호 안의 경우 전달인자 이름(실제 값을 표시)을 의미한다.
  + url 뒤에 붙는 파라메터의 값을 가져올 때 사용.( = 쿼리 스트링에서 값을 가져온다.)
+ @PathVariable : URI 경로의 일부를 파라미터로 사용할 때 이용(URI 경로에서 값을 가져온다)
  + Type2 경우
  + url에서 각 구분자에 들어오는 값을 처리해야 할 때 사용

#### @RequestBody
+ JSON 데이터를 원하는 타입의 객체로 변환해야 하는 경우에 사용

### 오늘의 일기
---
 #### @Data 사용    
+ request 파일에 @Data 어노테이션을 해주지 않으면 Service쪽에서 CRUD를 만들때 get으로 해당 request 파일 안에 변수를 사용할 수 없었다.
+ Entity 파일에 @Data 어노테이션을 해주지 않으면 Service쪽에서 response를 만들때 get으로 해당 엔티티 안에 변수를 사용할 수 없었다.
+ why? @Data 어노테이션에는 @getter, @setter 어노테이션이 포함되어 있기 때문이다.
+ 즉, getter, setter를 사용해야 하는 곳엔 @Data 어노테이션을 해주어야 한다구!!

#### Header 파일
+ JPA + SpringBoot + Mysql/Oracle 연동해서 사용하려면 모든 Http 통신에 Header라는 공통 부분이 들어가야 한다.
+ 제네릭을 사용하기 위해 모든 통신을 Header라는 클래스에 담아서 Request와 Response를 수행한다.
+ Header에는 다음과 같은 내용이 담겨있다.
  + transaction_time (통신 시각)
  + status (통신 상태 코드 OK, ERROR)
  + description (세부 내용:: 통신이 발생한 서버 주체)
  + data (데이터)

#### Optional을 사용해야 할 때  
+ 어떤 메서드가 Object를 리턴하는데 null일 가능성이 있다? 그렇다면 Optional로 래핑하는 것을 권장.

#### orElseGet()  
+ Optional 클래스의 메소드다.
+ 스프링에선 DB에서 데이터를 꺼낼 때, 찾는 데이터가 없을 경우가 있기 때문에 Optional 타입으로 꺼낸다.
+ 아래처럼 람다식을 통해 데이터를 처리하고 반환한다.
```java
 Optional<MiniBoard> miniBoardOptional = baseRepository.findById(id);
        return miniBoardOptional.map(miniBoard -> {
            baseRepository.delete(miniBoard);
            return Header.OK();
        }).orElseGet(() -> Header.ERROR("데이터 없음"));
```
+ .orElse VS .orElseGet 차이점
  + orElse() : Optional 내부 객체의 상태와 상관없이 무조건 실행됨. - Optional 내부 객체가 not null이라서 orElse가 결과값을 반환하지는 않더라도 실행된다.
  + orElseGet() : 내부 객체가 null인 경우에만 실행된다.

#### CrudInterface 인터페이스 파일
+ 공통 부분인 CRUD를 하나의 CrudInterface로 관리하기 위해 CrudInterface 인터페이스를 생성한다.
+ 제네릭을 사용하면 특정 객체만 들어올 수 있게 제한할 수 있다.
+ Request가 들어오면 Response로 반환하는 것이 기본 매커니즘이다.
+ `public interface CrudInterface<Req, Res> { }`

#### 패키지 안에 구성요소
+ model 패키지 : DB에 매핑되거나 통신의 Body 부분에 대응하는 객체가 담길 패키지이다.
  + entity : 실제 DB와 매핑될 JPA 엔티티 클래스
  + request : Header에 Data(Entity)가 결합되어 실제 통신에 필요한 정보를 담고 있는 Request 클래스
  + response : Request가 서비스 로직에 의해 가공된 Data를 Header와 결합된 Response 클래스
  + header : 요청에 필요한 Header 정보를 담고 있는 클래스
+ ifs 패키지 : CrudInterface라는 인터페이스가 존재할 인터페이스 패키지
+ controller 패키지 : controller 클래스들이 위치할 패키지
+ service 패키지 : controller에서 사용하게 될 service logic이 모여있는 패키지
+ Repository 패키지 : Repository 클래스들이 모여 있는 패키지.
  + Entity에 의해 생성된 DB에 접근하는 메서드 `예-findAll()` 들을 사용하기 위한 인터페이스이다.
  + extends를 통해서 상속받으면 해당 레포지토리의 객체를 이용해서 기본적으로 제공되는 메서드(save(), findAll(), get()) 등을 사용할 수 있게 된다.












