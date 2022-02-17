### JPQL VS SQL 차이점
+ JPQL은 JPA의 entity를 기반으로 하는 쿼리를 생성해주기 위한 문법이다.
+ SQL은 테이블을 대상으로 쿼리를 생성해주기 위한 문법이다.


### 오늘의 일기
----
#### @Query란?
+ 사용자가 정의한 대로 쿼리가 생성되는데 사용된다.
+ @Query는 더 구체적인 쿼리 메서드를 작성하기 위해 사용하는 쿼리 메서드의 custom버전이다.
+ 쿼리는 기본적으로 JPA에서 사용하는 쿼리 문법인 JPQL을 사용한다.

#### Query Method의 단점 보완
+ 조건이 많아지게 된다면 method의 이름이 어마어마하게 길어지게 된다.
+ @Qeury를 사용하면 메서드 명을 줄일 수 있고 더욱 더 가독성 있는 메서드를 만들 수 있다.

#### @Query 어노테이션 사용하기
+ @Query는 JpaRepository 를 상속하는 인터페이스에서 사용한다.
+ @Query 어노테이션은 Repository 인터페이스 안에서 메서드 위에 @Query를 붙이며 만들 수 있다. -> `@Query("쿼리문")`

#### 쿼리문 작성 방법
+ 쿼리를 직접 쓰는 경우
  + `@Query("SELECT u FROM User u WHERE u.status = 1")`
  + From table 별칭을 Select문안에 넣어야 한다.(안 그러면 오류남)

+ DB에서 쓰는 것처럼 직접 쿼리를 작성
  + `@Query(value = "SELECT * FROM USERS u WHERE u.status = 1",nativeQuery = true)`
  + `nativeQuery = true`를 옵션으로 줘야한다.
  + 이렇게 하면 DB에서 쓰는 SQL문 형식으로 사용이 가능하다.

#### where조건에 parameter를 넣는 방법
+ ?를 통한 경우(위치기반)
  + parameter의 위치에 따른 숫자를 넣어주면 된다.
  + 예) ?1 -> parameter 첫번째 자리에 있는걸 넣겠다는 뜻.
  + 순서에 맞게 지정해주는 것이 좋다.

+ :name(이름기반)
  + 파라미터의 이름으로 검색하는 경우
  + @Param으로 지정해줘도 되지만 Spring 4버전, java8 이후부터는 @Param주석 없이도 파라미터의 이름 기준으로 알아서 잘 찾아준다.  

🎈 가독성을 위해 "이름" 기반을 사용하는 것이 더 좋다!! 🎈  

#### 특정 컬럼만 추출하는 방법
+ 인터페이스로 특정 컬럼 추출하기
  + interface를 선언해주고 get으로 메서드를 통해 해당 column의 값만 가져올 수 있다.
  ```java
  public interface CustomerRepository extends JpaRepository<Customer, Long>{
    String getEmail();
  }
  ```
#### Native Query란?
+ DB에서 사용하는 SQL문을 그대로 사용하게 해준다.
+ jpa의 쿼리메서드는 여러 데이터를 업데이트할때 여러번의 update query가 실행되지만 native query를 사용하면 한번의 query로 업데이트가 가능하다.
+ Native Query 옵션을 true를 사용하면 entity가 아닌 table을 사용하게 된다. 필드의 이름도 테이블의 column명을 써야한다.(createdAt이 아닌 created_At)

#### Native Query를 사용하여 값을 변경하는 경우
+ Native Query를 사용하면 한번의 query로 업데이트가 가능하다.
+ jpa의 쿼리메서드의 save같은 경우는 save자체에 @Transactional이 붙어있지만 native query는 데이터 조작을 할 때는 직접 붙여줘야한다.
+ update, delete와 같은 DML작업에서는 @Modyfying이라는 것을 붙여 update가 되었다는 것을 확인시켜줘야 return 값을 받을 수 있다.  

🎃 Native Query 주의사항!!🎃  
+ DB종류가 바껴도 DB에 맞게 자동으로 쿼리를 바꿔준다는 JPA의 장점에서 빗겨나가게 된다.
+ 조건절에 대한 동적 할당이 불가능하다.(하나하나 별도로 만들어줘야 함)
+ 경우에 따라 JPA specification로 일부 동적 쿼리를 만들 수도 있다. 하지만 아쉽게도 실무에서 사용하기엔 다소 부족한 기능이다. 
+ 복잡한 쿼리는 수용할 수 없다.






