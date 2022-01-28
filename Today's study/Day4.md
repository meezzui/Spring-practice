### 오늘의 일기
---

#### DB의 슈퍼타입과 서브타입
+ 슈퍼타입 : 공통의 부분을 가지고 있는 타입의 테이블.
  + 장점: 테이블 전체 속성에 대한 검색이 쉽다. 무결성에 유리하다.
  + 단점: 서브타입별 속성이 많다면 지나치게 NULL이 많이 발생한다.(공간 낭비)
+ 서브타입 : 공통으로부터 상속받아 다른 엔티티와 차이가 있는 속성에 대해서는 별도의 서브엔티티로 구분.
  + 장점: NULL이 없거나 줄어든다.(공간의 낭비가 적다)
  + 단점: UNION 함수를 쓸 때 중복이 제거되기 때문에, PK의 유일성(Unique)을 잘 관리해야 한다. 비효율적인 조인이나 합집합, 교집합이 발생할 수 있다.(속도 저하)



[ 엔티티 설계시 주의점 ]  
#### 엔티티에는 가급적 Setter를 사용하지 말자!!
+ 실무에서는 가급적 Getter는 열어두고, Setter는 꼭 필요한 경우에만 사용하는 것을 추천!!!
+ Getter는 아무리 호출해도 호출 하는 것 만으로 어떤 일이 발생하지는 않는다. 
+ 하지만 Setter는 호출하면 데이터가 변한다. Setter를 막 열어두면 엔티티가 왜 변경되는지 추적하기 점점 힘들어진다.
+ 즉, 변경 포인트가 너무 많아서, 유지보수가 어렵다.

#### 모든 연관관계는 지연로딩으로 설정!!
+ 즉시로딩( EAGER )은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기 어렵다. 특히 JPQL을 실행할 때 N+1 문제가 자주 발생한다.
+ 실무에서 모든 연관관계는 지연로딩( LAZY )으로 설정해야 한다.
+ 연관된 엔티티를 함께 DB에서 조회해야 하면, fetch join 또는 엔티티 그래프 기능을 사용한다.

#### 컬렉션은 필드에서 초기화 하기!!
+ 컬렉션은 필드에서 바로 초기화 하는 것이 null문제에서 안전하다.
+ 하이버네이트는 엔티티를 영속화 할 때, 컬랙션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경한다. 
+ 만약 getOrders() 처럼 임의의 메서드에서 컬력션을 잘못 생성하면 하이버네이트 내부 메커니즘에 문제가 발생할 수 있다. 
+ 따라서 필드레벨에서 생성하는 것이 가장 안전하고, 코드도 간결하다.
```java
Member member = new Member();
System.out.println(member.getOrders().getClass());
em.persist(team);
System.out.println(member.getOrders().getClass());

//출력 결과
class java.util.ArrayList
class org.hibernate.collection.internal.PersistentBag
```


### 오늘의 어노테이션
---
#### @Embedded, @Embeddable
+ JPA Entity안의 Column을 하나의 객체로써 사용을 하고 싶을 때 사용한다.
+ 방법
  + 우선 하나로 표현하고자 하는 개념에 해당하는 클래스를 생성한 뒤 필드를 생성한다. 
  + 그 후, 클래스 상단에 @Embeddable 어노테이션을 입력한다.(생략도 가능)
  + 마지막으로 Entity 클래스에서 앞서 생성한 객체 필드를 생성하고, 필드 바로 위에  @Embedded 어노테이션을 붙여준다.
  ```java
  // Entity 클래스
  @Embedded
  private Address address;
  
  //표현하고자 하는 개념의 해당 클래스
  @Embeddable
  @Getter
  public class Address {
     private String city;
     private String street;
     private String zipcode;
    }
  ```

#### @Table
+ 엔티티와 매핑할 테이블을 지정
+ name 속성 :	매핑할 테이블 이름. 생략시 엔티티 이름(@Entity(name="~")) 사용
+ @Table에 name을 추가하면 디비에 테이블 이름이 name값으로 설정됨.

#### 상속관계 매핑 `@Inheritance(strategy=InheritanceType.XXX)`
+ 상속관계 매핑 : 객체의 상속 구조와 DB의 슈퍼타입 / 서브타입 관계를 매핑하는 것이다.
+ 순서
  + @Inheritance(strategy=InheritanceType.XXX)의 stategy를 설정해준다.
  + @DiscriminatorColumn(name="DTYPE") 어노테이션 입력 
    + 부모 클래스에 선언한다.(@Inheritance(strategy=InheritanceType.XXX)어노테이션을 해준 곳에 입력해준다.)  
    + 하위 클래스를 구분하는 용도의 컬럼이다.
  + @DiscriminatorValue("XXX") 어노테이션 입력
    + 하위 클래스에 선언한다. 
    + 엔티티를 저장할 때 슈퍼타입의 구분 컬럼에 저장할 값을 지정한다.
    + 어노테이션을 선언하지 않을 경우 기본값으로 클래스 이름이 들어간다.
    + ()괄호 안에는 지정해주고 싶은 원하는 클래스 이름을 넣어주면 된다.
+ InheritanceType 종류
  + JOINED
    + 형태 `@Inheritance(strategy = InheritanceType.JOINED)`
    + 각각의 테이블로 변환하는 조인 전략
    + 가장 정규화 된 방법으로 구현하는 방식이다.
    + 즉, 각자의 테이블에 해당하는 데이터만 저장되는 방식.
    + @DiscriminatorColumn을 선언하지 않으면 DTYPE 컬럼이 생성되지 않는다.

  + SINGLE_TABLE
    + 형태 `@Inheritance(strategy = InheritanceType.SINGLE_TABLE)`
    + 통합 테이블로 변환하는 단일 테이블 전략
    + 서비스 규모가 크지 않고, 굳이 조인 전략을 선택해서 복잡하게 갈 필요가 없을 때 한 테이블에 다 저장하고, DTYPE으로 구분하는 단일 테이블 전략을 사용한다.
    + 조인할 필요가 없고, 성능이 좋다.
    + 단일 테이블 전략에서는 @DiscriminatorColumn을 선언해 주지 않아도, 기본으로 DTYPE 컬럼이 생성된다.
    + 한 테이블에 모든 컬럼을 저장하기 때문에, DTYPE 없이는 테이블을 판단할 수 없다.

  + TABLE_PER_CLASS
    + 서브타입 테이블로 변환하는 구현 클래스마다 테이블을 생성하는 전략
    + 조인 전략과 유사하지만, 슈퍼 타입의 컬럼들을 서브 타입으로 내린다.(컬럼의 중복을 허용하는 전략)
    + 구현 클래스마다 테이블 생성 전략 적용  
    🎗 @Id 생성 전략 GenerationType.AUTO를 사용하는 경우 strategy를 TABLE_PER_CLASS로 변경 -> 이때, 실제 생성되는 테이블이 아닌 엔티티는 abstract 클래스여야 하고, @DiscriminatorColumn도 필요가 없어진다.  
    🎗 @Id 생성 전략 GenerationType.IDENTITY를 사용하는 경우 문제 발생!!🎃  
    => @Inheritance의 TABLE_PER_CLASS와 @Id의 GenerationType을 IDETITY를 같이 사용할 경우 `Cannot use identity column key generation with <union-subclass> mapping for`에러 발생  
    [해결방법]   
    🎗 @Id의 GenerationType을 TABLE 타입으로 변경    
    🎗 시퀀스 테이블이 생성되므로 이것에 대한 매핑까지 추가해줘야 완벽히 해결이 된다.  


#### @Enumerated
+ 형태 : `@Enumerated(EnumType.타입)`
+ EnumTyp 종류
  + EnumType.ORDINAL : enum 순서 값을 DB에 저장. DB에 저장되는 값은 숫자이다. 1이나 2.
  + EnumType.STRING : enum 이름을 DB에 저장. 문자열 자체가 저장된다.
+ @Enumerated 어노테이션을 사용하는 것은 성능상 좋지 못하다!! 🎃
  + 코드의 중복이 발생하여 유지보수에 어려움을 줄 수 있다.
  + 이를 대체하여 `Attribute Converter`사용하기
  + @Converter를 사용하면 엔티티의 데이터를 변환해서 데이터베이스에 저장할 수 있다.
  + 컨버터 구현은 AttributeConverter 인터페이스를 구현하면 된다.
  + `convertToDatabaseColumn` 메서드가 DB로 저장해야 하는 데이터를 컨버터 하는 역할을 하고 `convertToEntityAttribute`메서드가 DB에 저장되어 있는 데이터를 속성으로 변환해 주는 역할을 한다.
  + [ @Converter 설명 ](https://cherrypick.co.kr/jpa-converter/)












