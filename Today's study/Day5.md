### 바인딩이란?
+ 바인딩이란 프로그램의 기본 단위(예시: 변수)에 해당 기본 단위가 가질 수 있는 속성 중에서 일부 필요한 속성만을 선택하여 연결해 주는 것을 말한다. 
+ 변수를 예로 들면, 변수를 구성하는 식별자(이름), 자료형 속성, 하나 이상의 주소(참조), 자료값에 구체적인 값으로 확정하는 것을 말한다.  
+ 예를 들어 `int a = 1;` 이렇게 코딩되어 있을 때 Data Type이 int라는 것으로 바인딩 되고, a라는 변수명이 바인딩 되고, 1이라는 값이 바인딩 되는 것이다.(그니까 코드 하나하나가 다 바인딩 되는거야.)
+ 동적 바인딩 vs 정적 바인딩
  + 동적 바인딩(Dynamic Binding)
    + 실행 이후에 값이 확정되는 것
    + 실제 참조하는 객체는 서브 클래스이니 서브 클래스의 메소드를 호출한다.
  + 정적 바인딩(Static Binding)
    + 실행 이전에 값이 확정되는 것
    + 변수의 타입이 수퍼 클래스이니 수퍼 클래스의 메소드를 호출한다.
  + 예시
    + `int a = 1;`로 보면 Data Type으로 int가 바인딩 되는 것은 프로그램을 컴파일 할 때, 메모리에 할당되므로 '정적 바인딩'이다.
    + 또한 a라는 변수명 또한 컴파일 할 때, 메모리에 할당되므로 '정적 바인딩'이다.
    + 그러나 값 1은 실행 시에 할당되므로 '동적 바인딩'이다.
  + 자바에서는 메소드를 기본적으로 동적 바인딩 하기 때문에 메소드 오버라이딩이 가능하다.
  + static으로 선언하는 것은 메모리를 한 번밖에 할당하지 않기 때문에 컴파일 시에 메모리에 할당된다. 따라서 static으로 선언된 것은 모두 정적 바인딩이다.
  + 동적 바인딩 장점
    + 값이 변할 수 있다!!
  + 동적 바인딩 단점
    + 어떤 값이 들어올지 몰라서 들어올 값보다 많은 메모리 공간을 차지하고 있기 때문에 메모리 공간이 낭비될 수 있다.
    + 들어오는 값이 메모리 타입에 맞는지 확인하기 때문에 속도가 늦어진다.
  + 정적 바인딩 장점
    + 실행시 효율이 높아진다. -  컴파일 시 이미 값이 확정되어있기 때문에 실행만 하면 끝이다.
    + 동적 바인딩 보다 속도가 빨라서 효율적이다.
    + 값이 변하지 않아서 안정적이다.


### 파라미터 바인딩
+ 종류
  + 이름 기준 파라미터
    +  앞에 ' : ' 을 사용한다.
    +  예시( :name 으로 이름기준 파라미터임을 명시)
    ```java
    TypedQuery<Parent> query = em.createQuery("" +
        "SELECT p FROM Parent p WHERE p.name = :name", Parent.class);
    query.setParameter("name", name);
    ```
  + 위치 기준 파라미터
    + '?' 를 사용한다.
    + 예시(?1 로 위치 정보를 넣어 위치 기준 파라미터 임을 명시)
    ```java
    TypedQuery<Parent> query = em.createQuery("" +
        "SELECT p FROM Parent p WHERE p.name = ?1", Parent.class);
    query.setParameter(1, name);
    ```
    + 위치 기준 파라미터는 사용하지 않는 것이 좋다!!!🎃
    + why?? 중간에 다른 검색 조건이 들어올 경우 순서가 망가져버리기 때문이다. 
+ 파라미터 바인딩 어노테이션
   + @RequestParam
     + 1개의 Http 파라미터를 바인딩 받기 위해서 사용됨
     + 필수 default가 true이기 때문에 필요없다면 false로 변경해줘야함
   + @ModelAttribute
     + Request Parameter를 클래스로 바인딩 받고 싶을 때 사용
     + 일일히 @RequestParam으로 나열하고 싶지 않을때 사용
     + HTTP Body 내용과 HTTP 파라미터들을 Setter를 통해 1대1로 객체에 바인딩
   + @RequestBody
     + Json으로 받은 HTTP Body 데이터를 MessageConverter를 통해 변환시킴
     + Reflection을 사용하여 주입 ( Setter가 없어도 됨 )
   + @PathVariable
     + URL의 값을 정의한 이름과 같은 이름의 인자랑 매핑시키고자 할 때 사용
     + null이나 공백이 들어오는 경우에는 사용하면 안된다.
     + '.'이 포함되어 있다면 그 뒤는 잘려서 들어온다.


### 오늘의 일기
---

### 매개변수 = 파라미터!!!


#### JPQL(Java Persistence Query Language)
+ JPQL은 엔티티 객체를 조회하는 객체지향 쿼리다. 
+ JPQL은 특정 데이터베이스에 의존하지 않는다.
+ 예시 코드 `SELECT m FROM Member AS m where m.username = 'Hello'`
+ 엔티티와 속성은 대소문자를 구분한다. 
  + 엔티티 이름
    + JPQL에서 사용한 Member는 클래스 명이 아닌 엔티티 명이다. @Entity(name = "xxx")로 지정된 값을 의미한다. 엔티티 명을 지정하지 않으면 클래스명을 기본값으로 한다.
+ 하지만 SELECT, FROM, AS등과 같은 JPQL 키워드는 대소문자를 구분하지 않는다.
+ `Member AS m`와 같이 별칭을 필수로 사용해야 한다.(별칭 없이 사용하면 잘못된 문법이라는 오류가 발생한다. 단, as는 생략 가능)
+ TypeQuery<T> 와 Query
  + TypeQuery<T> : 반환 타입이 명확할 때 사용, 하나만 조회
    + 객체를 통째로 조회
    ```java
    // Member 객체를 통째로 조회
    TypedQuery<Member> query = em.createQuery("select m from Member m", Member.class);
    ```
    + 객체의 속성 하나만 조회
    ```java
    // Member의 username 하나만 조회. 타입은 String
    TypedQuery<String> query = em.createQuery("select m.username from Member m", String.class);
    ```
  + Query: 반환 타입이 명확하지 않을 때 사용, 여러 데이터들 조회
  ```java
  // 서로 타입이 다른(String, int) username, age를 조회
  Query query = em.createQuery("select m.username, m.age from Member m");
  ```
  + 엔티티 객체의 여러 속성을 한 번에 조회하여 쿼리 타입을 특정할 수 없을 때는 아래와 같이 Object[]로 받는다.
  ```java
  List<Object[]> resultList = em.createQuery("select m.username, m.age from Member m").getResultList();
  Object[] result = resultList.get(0);
  String name = result[0];
  int age = result[1];
  ```
  + 꾝 필요한 데이터들만 선택해서 조회해야 할 때도 있다. 이 때에는 TypedQuery가 아닌 Query를 사용해야 한다.🎃
  + 결과 조회 API
    + query.getResultList(): 결과가 하나 이상일 때, 리스트 반환. 결과가 없으면 빈 리스트 반환. 조회된 행이 없을 시 'null' 반환.
    + query.getSingleResult(): 결과가 정확히 하나, 단일 객체 반환 
    ```
    //에러발생
    결과가 없으면: javax.persistence.NoResultException
    둘 이상이면: javax.persistence.NonUniqueResultException
    ```
+ 프로젝션
  + SELECT 절에 조회할 대상을 지정하는 것을 '프로젝션'이라고 한다.
  + 지정방법 : `SELECT {프로젝션 대상} FROM]`
  + 프로젝션 대상 : 엔티티, 임베디드 타입, 스칼라 타입(숫자, 문자 등 기본 데이터 타입)
  + 엔티티 프로젝션
    + 원하는 객체를 바로 조회
    + 엔티티 프로젝션으로 조회한 엔티티는 영속성 컨텍스트에서 관리
    ```java
    SELECT m FROM Member m        // 회원
    SELECT m.team FROM Member m // 팀
    ```
  + 임베디드 타입 프로젝션
    + `SELECT m.address FROM Member m`
    + 임베디드 타입이 속해있는 Entity를 명시해줘야함 (예: Member)
  + 스칼라 타입 프로젝션
    + 문자, 숫자와 같은 기본 데이터 타입 `SELECT m.username, m.age FROM Member m`
    + DISTINCT로 중복 제거
    + 통계 쿼리와 같은 것들이 주로 스칼라 타입으로 조회된다.
+ New 명령어
  + SELECT 다음 NEW 명령어 사용 시 반환받을 클래스 지정 가능하다.(이 클래스의 생성자에 JPQL 조회 결과를 넘겨줄 수 있음)
  + NEW 명령어를 사용한 클래스로 TypeQuery를 사용할 수 있어 객체 변환 작업을 줄일 수 있다.
  + 명령어 사용 시 주의사항
    + 패지키 명을 포함한 전체 클래스 명 기입
    + 순서와 타입이 일치하는 생성자 필요
  ```java
  TypeQuery<UserDTO> query =
    em.createQuery("SELECT new test.jpql.UserDTO(m.username, m.age)    
                    FROM Member m", UserDTO.class);
  ```
 
+ [여기서 잠깐!!!✋] JPQL을 사용하는 것보다 QueryDSL을 사용하는 것이 더 효율적이다!!🎃
  + JPQL은 new 명령어 뒤에 패키지 명을 포함한 전체 클래스 명을 모두 기입해줘야 하지만 QueryDSL을 사용하면 클래스를 Import하여 쓸 수 있기 때문에 코드가 더 간결해진다.
  + JPQL에서는 직접 쿼리를 스트링으로 입력해줘야하는데, 만약 스트링에서 오타가 나면 런타임시 에러가 난다. 런타임 에러는 잡기가 쉽지 않고, 크리티컬하다.
  + 그러나 QueryDSL은 자바코드이다 보니깐 컴파일 시점에서 에러가 나서 개발단계에서 에러를 잡을 수 있다.(아주 굿이쥬??)



