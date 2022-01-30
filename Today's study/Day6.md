### 역직렬화란?
+ 생성자를 거치지 않고 리플렉션을 통해 객체를 구성하는 메커니즘이다.

### 리플렉션이란?
+ 구체적인 클래스 타입을 알지 못해도, 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API이다.
+ 리플렉션은 애플리케이션 개발에서보다는 프레임워크, 라이브러리에서 많이 사용된다.
+ 프레임워크, 라이브러리는 사용하는 사람이 어떤 클래스를 만들지 모른다. 이럴 때 동적으로 해결해주기 위해서 리플렉션을 사용한다.(자바는 정적언어-컴파일 시점에 타입을 결정)
+ 사용 예)  스프링의 DI(dpendency injection), Proxy, ModelMapper 등..


### 오늘의 일기
---
#### @Valid , @NotEmpty
+ 회원등록을 할때 POST를 하는데 속성값을 다 적지 않고 비어놔두 실행이 잘 되고 적지 않은 속성값은 null로 들어갔다.
+ 회원등록을 하는데 정확한 값들이 들어가야 하지 않나?? 
+ 그래서 엔티티 클래스에서 해당 속성에 제약조건인 @NotEmpty를 걸어줘야 했다.
+ 그러면 그 해당 속성들은 유효성 검사를 거치고 모든 속성의 데이터가 유효하다면 아무일도 일어나지 않고 유효하지 않을 경우 에러가 발생한다.

#### 엔티티 대신에 DTO를 RequestBody에 매핑해야 하는 이유
```java
@PostMapping("/api/member_regist")
 public CreateMemberResponse saveMemberV2(@RequestBody @Valid
CreateMemberRequest request){
  ...
 }
```
+ 위에 코드처럼 RequestBody에 매핑할 때 엔티티를 Request Body에 직접 매핑하지 않고 CreateMemberRequest처럼 DTO를 만들어서 매핑해줘야 한다.
+ 그래야 엔티티가 변경되도 API 스펙이 변하지 않는다.
+ why? 
  + 실무에서는 회원 엔티티를 위한 API가 다양하게 만들어지는데, 한 엔티티에 각각의 API를 위한 모든 요청 요구사항을 담기는 어렵다.
  + 엔티티가 변경되면 API 스펙이 변한다.(이건 매우 위험한 일!!!)
+ 즉, 실무에서는 엔티티를 API 스펙에 노출하면 안된다!!!🎃🎃🎃

#### 로그인의 핵심
+ 로그인의 핵심 로직은 회원을 조회한 다음에 파라미터로 넘어온 password와 비교해서 같으면회원을 반환하고, 만약 password가 다르면 null 을 반환하는 것이다.
```java
public Member login(String loginId, String password) {
 return memberRepository.findByLoginId(loginId)
 .filter(m -> m.getPassword().equals(password))
 .orElse(null);
 }
 ```
 
 #### 로그인 컨트롤러
 + 로그인 컨트롤러는 로그인 서비스를 호출해서 로그인에 성공하면 홈 화면으로 이동하고, 로그인에 실패하면 bindingResult.reject() 를 사용해서 에러 메시지를 전달한다. 

#### BindingResult.hasErrors() 
+ form을 통해 입력한 입력값에 대한 검증을 해서 에러가 발생하는지 체크한다. 에러가 있으면 그 경우에 따른 동작을 수행한다.


### 오늘의 어노테이션
---
#### @Valid  VS  @Validated
+ request 후에 서버측에서 데이터를 바인딩할때, 데이터가 유효한지(ex. 누락, 최대 크기 초과 등) 검사해야 하는 경우가 있을 수 있다. 
+ 그럴 때는 @Valid 또는 @Validated 어노테이션을 이용하여 데이터 유효성 검증을 할 수 있다.
+ @Valid
  + 자바 표준 스펙
  + 컨트롤러 메소드의 유효성 검증을 위해서만 사용된다.(요청 파라미터의 유효성 검증은 컨트롤러에서 처리하고 서비스나 레포지토리 계층에서는 유효성 검증을 하지 않는 것이 바람직하다.)🎃
  + 유효성 검증에 실패할 경우 `MethodArgumentNotValidException`이 발생한다.
  + 적용방법(@RequestBody를 통해 데이터를 바인딩하는 경우)
    + @RequestBody 옆에 @Valid 어노테이션을 추가한다. (@RequestBody의 오른쪽, 왼쪽 어디든 상관없다.)
    + 해당 엔티티의 속성에 제약조건 @NotEmpty를 걸어준다. (@NotEmpty: 해당 값이 null이 아니고, 빈 스트링("") 아닌지 검증함(" "은 허용됨))
    + 제약조건은 @NotEmpty 말고도 여러가지가 있다.
+ @Validated(잘 사용하지 않음!!)
  + 스프링 프레임워크가 제공하는 기능
  + 불가피하게 서비스 계층 등에서 파라미터를 검증해야 할 때 사용한다.
  + 조건이 적용될 검증 그룹을 지정하여 적용할 때 사용한다.
  + 스프링 빈의 유효성 검증을 위해 사용되며 클래스에 @Validated를 메소드에 @Valid를 붙여주어야 한다.
  + 유효성 검증에 실패할 경우 `ConstraintViolationException`이 발생한다.


#### @RequestBody VS @ModelAttribute
+ @RequestBody와 @ModelAttribute 공통점 : 클라이언트 측에서 보낸 데이터를 Java 코드에서 사용할 수 있는 오브젝트로 만들어준다. 하지만 세부적인 특징에서 차이가 있다.
+ @RequestBody
  + 요청 본문의 JSON, XML, Text 등의 데이터가 적합한 HttpMessageConverter를 통해 파싱되어 Java 객체로 변환 된다.🎈
  + @RequestBody를 사용할 객체는 필드를 바인딩할 생성자나 setter 메서드가 필요없다.
    + How?  
    + JSON 값을 Java 객체로 역직렬화하기 때문이다.
    + 단, 데이터 바인딩을 위한 필드명을 알아내기 위해 getter나 setter 중 1가지는 정의되어 있어야 한다.(getter를 사용하는 것이 좋다!!)
  + 그러나 직렬화를 위해 기본 생성자는 필수다.
    + @RequestBody에 사용하려는 DTO가 기본 생성자를 정의하지 않으면 데이터 바인딩에 실패한다.
+ @ModelAttribute
  + @ModelAttribute를 사용하면 HTTP 파라미터 데이터를 Java 객체에 맵핑한다.
  + 따라서 객체의 필드에 접근해 데이터를 바인딩할 수 있는 생성자 혹은 setter 메서드가 필요하다.
  + Query String 및 Form 형식이 아닌 데이터는 처리할 수 없다.🎈







