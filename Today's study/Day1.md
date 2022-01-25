### Lombok 이란?
---
+ Lombok 프로젝트는 자바 라이브러리로 코드 에디터나 빌드 툴(IntelliJ, Eclipse, XCode 등)에 추가하여 코드를 효율적으로 작성할 수 있도록 도와준다. 
+ class명 위에 어노테이션을 명시해줌으로써 getter나 setter, equals와 같은 method를 따로 작성해야 하는 번거로움을 덜어준다.
+ lombok 어노테이션 중 가장 자주 사용되는 어노테이션은 @Data, @NonNull, @NoArgsConstructor/@AllArgsConstructor이다.

### 오늘의 어노테이션
---
#### @MappedSuperclass
+ 엔티티의 공통 매핑 정보가 필요할 때 주로 사용한다. 
+ 부모 클래스(엔티티)에 필드를 선언하고 단순히 속성만 받아서 사용하고싶을 때 사용하는 방법이다.

#### @EntityListeners(AuditingEntityListener.class)
+ 엔티티를 DB에 적용하기 전, 이후에 커스텀 콜백을 요청할 수 있는 어노테이션이다.
+ @EntityListeners 의 인자로 커스텀 콜백을 요청할 클래스를 지정해주면 되는데, Auditing 을 수행할 때는 JPA 에서 제공하는 AuditingEntityListener.class 를 인자로 넘기면 된다.

#### @Column
+ 객체 필드와 DB 테이블 컬럼을 맵핑한다.
+ updateable : 엔티티 수정시 이 필드를 함께 수정 `@Column(updatable = false)`

#### @CreatedDate
+ 해당 엔티티가 생성될 때, 생성하는 시각을 자동으로 삽입해준다.

#### @Data
+ @ToString, @EqualsAndHashCode ,@Getter : 모든 필드 ,@Setter : 정적 필드가 아닌 모든 필드, @RequiredArgsConstructor
+ @Data와 함께 포함되어 있는 lombok의 설정 예를 들어, callSuper, includeFieldNames 그리고 exclude 등을 지정할 수는 없다.
+ 따라서 개별 어노테이션의 설정 값을 기본값이 아닌 값을 사용할 때에는 @Data 대신 개별 어노테이션을 사용하도록 한다.  
🎉 @Data 사용 지양 🎉  
1. 무분별한 Setter 남용 - Setter는 그 의도가 분명하지 않고 객체를 언제든지 변경할 수 있는 상태가 되어서 객체의 안전성이 보장받기 힘들다.
2. @ToString : 양방향 연관관계 시 순환 참조 - Member 와 Coupon 이 1:N 양방향으로 매핑되어 있는 상황을 가정할 수 있다. 이때, ToString을 호출하면 무한 순환 참조가 발생한다. 이러한 문제를 해결하기 위해서는 @ToString(exclude = "coupons") 처럼 어노테이션을 사용해서 특정 항목을 제외시키는 방법을 사용할 수 있다.  

#### @EqualsAndHashCode
+ equals, hashCode 자동 생성
+ equals : 두 객체의 내용이 같은지, 동등성(equality) 를 비교하는 연산자
+ hashCode : 두 객체가 같은 객체인지, 동일성(identity) 를 비교하는 연산자
+ @EqualsAndHashCode(callSuper = true)로 설정시 부모 클래스 필드 값들도 동일한지 체크하며, false(기본값)일 경우 자신 클래스의 필드 값만 고려한다.

#### @AllArgsConstructor
+ 객체 내부의 인스턴스멤버들을 모두 가지고 있는 생성자를 생성하는 Lombok 어노테이션
+ 이 어노테이션은 인스턴스 멤버의 순서가 중요하다. 왜냐하면 인스턴스 멤버의 순서가 생성자의 파라미터의 순서와 동일하기 때문이다.   
🎉 @AllArgsConstructor 사용 지양 🎉  
+ 생성자를 직접 만들고 필요한 경우에 직접 만든 생성자에 `@Builder` 어노테이션을 붙이는 것을 권장한다. 
+ 그럼 파라미터 순서가 아닌 이름으로 값을 설정하기 때문에 리팩토링에 유연하게 대응할 수 있다.

#### @Builder
+ 빌드 패턴을 사용하여 객체를 생성할 수 있으며 객체 생성 후 setter를 통한 접근을 제한 할수 있어서 불변형 객체를 만들 수 있다.

#### @Autowired 
+ 생성자, 필드(프로퍼티), 설정메서드(set메서드)에 지정 가능
+ 타입을 이용해서 자동적으로 프로퍼티 값을 설정하기 때문에 해당 타입의 빈 객체가 존재하지 않거나 빈 객체가 두 개 이상 존재할 경우 예외 발생.
+ `@Autowired(required=false)`로 지정 - 기본값은 `@Autowired(required=true)` false로 하면 빈 객체가 존재하지 않더라도 예외처리를 발생시키지 않는다.

#### @Bean vs @Component
+ @Bean : 어노테이션의 경우 개발자가 직접 제어가 불가능한 외부 라이브러리등을 `Bean`으로 만들려고 할 때 쓰인다.
  + ArrayList 같은 라이브러리등을 Bean으로 등록하기 위해서는 별도로 해당 라이브러리 객체를 반환하는 Method를 만들고 어노테이션을 붙여주면 된다.
+ @Component : 개발자가 직접 작성한 Class를 Bean으로 등록하기 위한 어노테이션이다. 
  + MiniBoard Class는 개발자가 사용하기 위해서 직접 작성한 Class이다. 이러한 클래스를 Bean으로 등록하기 위해 상단에 @Component 어노테이션을 사용한다.
  + @Component을 구체화하여 표현한 것이 @Controller, @Service, @Repository 이다.

#### @Controller
+ 컨트롤러 클래스에 @Controller 어노테이션을 작성한다. 
+ 해당 어노테이션이 적용된 클래스는 "Controller"임을 나타나고, bean으로 등록되며 해당 클래스가 Controller로 사용됨을 Spring Framework에 알린다.

#### @RestController
+ @Controller + @ResponseBody 이며, 메소드의 return(반환 결과값)을 문자열(JSON) 형태로 반환한다. 
+ view가 필요없는 API만 지원하는 클래스에 사용되며, json 이나 xml 과 같은 문자열의 return이 주목적이다.














