#### `source cannot be null`에러🛑
+ bootRun을 실행시키고 Swagger에서 send를 눌렀는데 response값이 안 나오고 `source cannot be null`라는 에러가 떴다.
+ 첨에는 해당 칼럼에 빈값이 있어서 그런 줄 알았는데 그 문제가 아니라 `@Alias("")`문제였다.
+ `@Alias("")`는 mybatis에서 매퍼에서 별칭으로 해당 클래스를 매핑해주는 기능을 한다.
+ 그런데 나는 처음에 코드를 아래와 같이 작성하였었다.
```node
@Data
@Alias("reviews")
public class Review {
  public static class Rvm extends ComnMbrRvwHis {
    private long dsplyCtgrySeqNo;
    private String gdsNmCn;
    private String ctgryNmCn;
  }
}
```
+ 이렇게 하면 `Rvm`에 @Alias("reviews") 어노테이션이 적용되지 않아서 쿼리문과 맵핑이 되지 않는다.
+ `Rvm`클래스 바로 위에 어노테이션을 주어야 한다.
+ 그런데 클래스가 여러개가 필요하지 않는 이상 저렇게 따로 빼지 않고
```node
@Data
@Alias("reviews")
public class Review extends ComnMbrRvwHis{
    private long dsplyCtgrySeqNo;
    private String gdsNmCn;
    private String ctgryNmCn;
    private String expsrYn;

}
```
+ 이렇게 해주는게 더 효율적이다‼️
