### 캐싱이란?
+ `캐싱은 데이터에 대한 향후 요청에 신속하게 액세스할 수 있도록 데이터를 로컬에 저장하는 프로세스이다.`
+ 컴퓨팅에서 '캐시'는 일반적으로 일시적인 특징이 있는 데이터 하위 집합을 저장하는 고속 데이터 스토리지 계층이다.
+ 해당 데이터에 대한 요청이 있을 경우 데이터의 기본 스토리지 위치에 액세스할 때보다 더 빠르게 요청을 처리할 수 있다. 
+ 캐싱을 사용하면 이전에 검색하거나 계산한 데이터를 효율적으로 재사용할 수 있다. 

### JPA 영속성 컨텍스트란?
+ 엔티티를 영구 저장하는 환경이라는 뜻이다. 
+ 애플리케이션과 데이터베이스 사이에서 객체를 보관하는 가상의 데이터베이스 같은 역할을 한다.
+ 엔티티 매니저를 통해 엔티티를 저장하거나 조회하면 엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하고 관리한다.
+ ex) `em.persist(member);` 엔티티 매니저를 사용해 회원 엔티티를 영속성 컨텍스트에 저장한다는 의미.

### N+1 문제란?
+ 하나의 쿼리를 날리는데 조회되는 결과의 개수만큼의 쿼리가 추가적으로 나가는 것을 말한다.


### 오늘의 일기
---
#### gradle dependencies 추가 - `implementation 'org.springframework.boot:spring-boot-starter-devtools'`
+ devtools가 환경에 따라서 잘 안되는 경우도 종종 있다!!!🎃
+ 제공하는 기능
  + Property Defaults   
    🎗 Spring에서 제공하는 thymeleaf는 기본적으로 캐싱 기능을 사용한다.  
    🎗 개발과정에서 캐싱이 되어있다면, 우리가 thymeleaf 파일을 수정하더라도, 반영되지 않을 것이다.(Application cache 설정값을 false로 수정해야 함)    
    🎗 이러한 작업을 Spring Boot DevTools에서 제공한다. 예를 들어, 템플릿 엔진의 캐싱 기능을 개발 단계에서 자동으로 꺼주는 역할을 하는거쥐. 따라서 개발자는 설정 파일을 건드리지 않아도 된다.  
  + Automatic Restart  
    🎗 파일 수정 후 저장을 하면, Classpath에 존재하는 파일의 변경을 감지하고, 자동으로 서버를 restart 해준다.  
    🎗 설정을 통해 원하는 디렉터리만을 트리거로 설정할 수도 있다.  
  + Live Reload  
    🎗 자동으로 브라우저가 새로 고침 되는 기능  
    🎗 내부적으로 live reload 서버를 두고, 브라우저 확장프로그램과 통신하는 방식으로 동작하죠.  
    🎗 크롬 확장프로그램을 받고, enable을 하면 됩니다.  
    
    
#### 연관관계 맵핑(중요!!)
+ 단방향 관계 : 두 엔티티가 관계를 맺을 때, 한 쪽의 엔티티만 참조하고 있는 것을 의미한다.
+ 양방향 관계 : 두 엔티티가 관계를 맺을 때, 양 쪽이 서로 참조하고 있는 것을 의미한다.
+ 외래 키가 있는 곳을 연관관계의 주인으로 정해라!!!🎃
  + 회원과 주문이 있을 경우 일대다 / 다대일의 양방향 관계다. 따라서 연관관계의 주인을 정해야 한다.
  + 연관관계의 주인은 단순히 외래 키를 누가 관리하냐의 문제이지 비즈니스상 우위에 있다고 주인으로 정하면 안된다.
  + 연관관계의 주인을 정하는 방법은 `mappedBy` 속성을 사용하면 된다.(이 필드는 읽기 전용이 되는 것을 꼭 명심해야 함.)
    + 주인은 mappedBy 속성을 사용하지 않는다. 주인이 아니면 mappedBy 속성을 사용해서 속성의 값으로 연관관계의 주인을 정할 수 있다.
    + 예) 주인이 아닌 Category 엔티티의 books 필드에 mappedBy 속성을 사용하여 주인이 아님을 JPA에게 알려준다.
    ```java
    @OneToMany( mappedBy = "category“ )
    private List<Book> books = new ArrayList<Book>();
    ```
  + [ 잘못생각하기 쉬운점!! ] 회원이 주문을 하기 때문에, 회원이 주문리스트를 가지는 것이 맞다고 생각하겠지만, 객체 세상은 실제 세계와는 다르다. 실무에서는 회원이 주문을 참조하지 않고, 주문이 회원을 참조하는 것으로 충분하다.
  + 즉, 회원이 있어야 주문을 할 수 있다고 생각하겠지만 실제 코딩할 때는 주문을 할 때 회원이 필요하다고 생각하면 됨.🎃
+ 연관 관계 매핑 종류
  + 일대일(1:1) - @OneToOne
    + @OneToOne 어노테이션으로 일대일 단방향 관계를 매핑하고, @JoinColumn을 넣어준다.
    ```java
    @OneToOne
    @JoinColumn(name = "locker_id")
    private Locker locker;
    ```
    + @JoinColumn(name=" ") : 외래 키 매핑 시 사용(name : 매핑할 외래 키 이름)
    + 반대편에 mappedBy를 적용시켜주면 일대일 양방향 관계 매핑이 된다.
    ```java
    @OneToOne(mappedBy = "locker")
    private Member member;
    ```
  + 다대일(N:1) - @ManyToOne
    + 여러 개의 테이블이 하나의 테이블과 연관 관계를 맺을 때 사용한다.
    + @JoinColumn(name=" ") : 연관 관계를 맺을 해당 객체의 컬럼 값을 넣어준다.
  + 일대다(1:N) - @OneToMany
    + @ManyToOne의 반대 입장이라고 생각하면 된다.
  + 다대다(N:M) - @ManyToMany
    + JPA @ManyToMany 어노테이션을 사용하고 @JoinTable로 연결 테이블을 지정해줄 수 있다.
    + 실무에선 사용하지 않는다!!!🎃
    + 왜?? 관계형 데이터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없다.  
    [ 그럼 어떻게 해야해?? ]    
    + 연결 테이블(조인 테이블)을 추가해서 일대다, 다대일 관계로 풀어내야한다. 
    + 그 추가된 곳에서 다대다로 하려고 했던 엔티티들을 매핑한다.(그럼 두개가 맵핑되겠지??)
    
    
#### 지연 로딩(LAZY) VS 즉시 로딩(EAGER)
+ 지연 로딩(LAZY)
  + 객체 A를 조회할 때는 A만 가져오고 연관된 애들은 프록시 초기화 방법으로 가져온다.
  + @OneToMany, @ManyToMany처럼 Many로 끝나는 것들은 기본값이 지연 로딩이다.
  + 엔티티의 연관관계가 깊어지면 즉시 로딩이 연관된 모든 엔티티들을 조회하게 된다. 
  + 그리고 그 엔티티들을 조회할 때 따로따로 조회하므로 N + 1문제 가 발생한다.
  + 그러므로 가급적 모든 관계는 `지연 로딩`으로 설정해야 한다!!!🎃
+ 즉시 로딩(EAGER)
  + 객체 A를 조회할 때 A와 연관된 객체들을 한 번에 가져오는 것이다.
  + @ManyToOne, @OneToOne처럼 One으로 끝나는 것들은 기본값이 즉시 로딩이다.
  +  N + 1문제 가 발생하므로 되도록 쓰지 않는 것이 좋다.


#### JPA의 Fetch
+ Fetch는 JPA 가 하나의 Entity 를 조회할 때, 연관관계에 있는 객체들을 어떻게 가져올 것이냐를 나타내는 설정값이다.
+ Fetch 타입 종류
  + Eager전략 
    + 연관 관계에 있는 Entity 들 모두 가져온다.
    + 형태 `FetchType=EAGER `
  + Lazy 전략
    + 연관 관계에 있는 Entity 가져오지 않고, getter 로 접근할 때 가져온다.
    + 형태 `FetchType=LAZY`
+ N+1 문제가 발생하는 경우
  + ManyToOne, OneToOne 컬럼의 FetchType 을 LAZY 로 하였을 경우 발생한다.
+ N+1 해결방법
  + JPQL 의 Fetch Join 을 이용합니다. (QueryDSL 과 같은 쿼리빌더의 도움을 받을 수도 있다.)
    + Query Builder 를 이용하는 경우, OneToOne 은 Fetch Join 걸어주는게 좋다!!!
    + 예) queryDSL 기준으로  Order List 를 가져오는 쿼리.
    + `List<Order> orders = jpaQueryFactory.select(qOrder).from(qOrder).fetch();` 
  + ManyToOne, OneToOne 의 FetchType = LAZY → EAGER 로 변경한다.
    + OneToOne 컬럼의 FetchType=LAZY 는 사용자가 의도한대로 동작하지도 않는다!!
    + OneToOne 의 FetchType=LAZY 는 컬럼을 조회하지 않아도 무조건 쿼리가 날라간다.
    + 그래서 무조건 날라가는 EAGER로 바꿔치기 해주는 것이다.
  + @EntityGraph 를 이용해, 한 쿼리에 대해서만 EAGER load 를 지정한다.
+ Fetch join
  + JPQL에서 지원하는 기능으로 '성능 최적화'를 위해 사용한다. 
  + JPA에서 일반적인 조인을 사용하면 연관된 엔티티는 함께 조회하지 않는다. 그렇기 때문에 N+1 문제가 발생할 수 있는데, 페치 조인을 사용하면 연관된 엔티티를 한 번의 쿼리로 모두 가져올 수 있다.
  + 즉, Fetch Join은 지연로딩을 하지 않고 즉시 로딩(EAGER)로 가져오는 쿼리이다.
  + 사용방법
    + 사용 방법은 join 키워드를 사용하는 곳에 join fetch 키워드를 사용하면 된다. 
    ```java
    //페치 조인을 사용하여 조회. 
    List<Member> resultList = em.createQuery("select m from Member m join fetch m.team", Member.class).getResultList();
    ```
    

    
    
