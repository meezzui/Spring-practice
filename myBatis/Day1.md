#### JPA와 myBatis 차이✔
+ `JPA`와 `myBatis` 차이는 불필요한 칼럼의 중복이다❗❗
+ `JPA`는 칼럼이 중복되는데 `myBatis`는 불필요한 칼럼의 중복을 없앨 수 있다.
+ 예를들어, `JPA`는 회원테이블이 있고 거기에 회원 이름 칼럼이 있다고 하자. 그런데 주문조회를 할 때 주문테이블에 또 회원 이름의 칼럼이 들어간다.
+ 그러나 `myBatis`는 회원테이블에 회원이름이 있다면 주문 테이블에는 회원이름이 없어도 된다. 그냥 회원테이블을 `join`해서 사용하면 된다.  


#### DTO란❓❓
+ DTO는 데이터를 전달하기 위한 객체이다. 즉, 데이터를 전달하는 용도로만 사용된다.
+ 데이터를 주고 받을 때, 데이터를 담아서 전달하는 바구니로 생각하면 된다.
+ `setter`를 이용하여 값을 담아 전달하고, 받는 쪽에서는 `getter`를 이용하여 값을 꺼내 쓰게 된다.
+ 크롬에서 F12 눌러서 네트워크에 `페이로드`로 보여지는 부분❗❗ 


#### VO란❓❓
+ `resultType`을 `VO`로 지정해 주면 알아서 `setter`를 통해 값이 들어가게 된다.
+ 그리고 이름은 `Camel Case`를 사용한다. => `Camel Case`란?? `_`를 쓰지않고 대문자로 단어를 구분한다. 예) catBox 이런 식
+ 예시)
```node
// .xml 파일에 작성
<select id="getTagsByPlaceNo" resultType="tagVo">
  // 여기는 SQL 쿼리문 들어가는 곳!!
</select>
  
// TagVO.class
@Alias("tagVo")
public class TagVO {
	private int tagNo;
	private String tagNm;
	private String ctrCd;
}
```
+ 이렇게 쿼리문을 쓴 곳에서 `resultType` 값을 정해주면 `@Alias`로 그 값을 `VO`에서 적어주면 해당 이름을 찾아 쿼리문을 `VO`에 적은 타입으로 반환해 준다.

#### @Alias 어노테이션
+ `@Alias`는 `mybatis`에서 지원하는 어노테이션으로 별칭을 지정할 때 사용한다.

