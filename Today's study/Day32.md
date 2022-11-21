#### `source cannot be null`에러🛑
+ bootRun을 실행시키고 Swagger에서 send를 눌렀는데 response값이 안 나오고 `source cannot be null`라는 에러가 떴다.
+ 첨에는 해당 칼럼에 빈값이 있어서 그런 줄 알았는데 그 문제가 아니라 `@Alias("")`문제였다.
+ `@Alias("")`는 mybatis에서 매퍼에서 별칭으로 해당 클래스를 매핑해주는 기능을 한다.
+ 그런데 나는 처음에 코드를 아래와 같이 작성하였었다.
