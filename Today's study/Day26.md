#### mybatis foreach 반복문 
+ `<foreach collection="details" item="item" separator=",">`
  + `collection` : parameterType으로 넘어온 별칭을 적어준다.
  + `item` : `collection`을 사용할 변수 명("내 맘대로 이름 짓기") 
  + `seperator` : 반복 문자열을 구분 할 문자 예) `separator=","` 이렇게 할 경우 `','` 단위로 나눠진다는 이야기이다.
  ```node
  "details": [
    {
      "dtlCnSeCd": "D",
      "dtlInfoCn": "ko",
      "ntnCd": "한국어1"
    },
    {
      "dtlCnSeCd": "D",
      "dtlInfoCn": "en",
      "ntnCd": "영어1"
    }
  ]
  ```
+ 보통 INSERT문은
```node
INSERT INTO JTS_LEIS_GDS_DTL_INFO (LEIS_GDS_ID,NTN_CD,DTL_INFO_CN,DTL_CN_SE_CD)
VALUES ('300000001', 'cn', '중국어', 'D');
```
+ 이렇게 쓰는데 리스트에 인서트 해야하기 때문에 객체를 반복문을 돌려줘야 한다. 그래서 `foreach`문을 쓰는데 이 `foreach`문은 `VALUES()` 자리에 쓰면 된다.
```node
INSERT INTO INSERT INTO JTS_LEIS_GDS_DTL_INFO
(
  LEIS_GDS_ID,
  NTN_CD,
  DTL_INFO_CN,
  DTL_CN_SE_CD
)
VALUES
  <foreach collection="details" item="item" separator=",">
  (
    #{leisGdsId},
    #{item.ntnCd},
    #{item.dtlInfoCn},
    #{item.dtlCnSeCd}
  )
  </foreach>
```

#### 에러일지🛑
+ `Caused by: java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''300000001',`
+ 위와 같은 에러가 발생하였다.
+ 이러한 에러가 발생한 이유는 `mybatis`에 `foreach`문을 잘 살펴보면 된다.
![KakaoTalk_Photo_2022-11-14-15-07-45](https://user-images.githubusercontent.com/86812098/201587609-b250feef-97e7-4e04-9f20-ea1d0b656cf1.png)
+ 위에 사진에서 문제가 뭘까⁉️ 바로 `VALUES()` 부분이다.
+ `foreach`문이 `()`괄호 밖에서 돌면서 그 객체를 하나씩 생성해주어야 하는데 괄호가 빠졌기 때문이다.


#### 코드설명✅
```node
VALUES
  <foreach collection="details" item="item" separator=",">
  (
    #{leisGdsId},
    #{item.ntnCd},
    #{item.dtlInfoCn},
    #{item.dtlCnSeCd}
  )
  </foreach>
```
+ 위에 코드를 보면 `#{leisGdsId}`이랑 나머지 아래 코드들인 `#{item.ntnCd}` 보면 좀 다르다는 것을 알 수 있을 것이다.
+ 왜 다른걸까🧐
  + `item`은 전달받은 인자 값을 `alias`명으로 대체 대체하는 속성을 가지고 있는데 GoodsMapper파일을 보면 
  + `void insertLeisGdsDtlInfo(@Param("details") List<GoodsRequest.LeisGdDtlInfo> details, @Param("leisGdsId") String leisGdsId)`
  + `#{item.ntnCd}`은 이렇게 `details`라는 속성 안에 있는 것을 `item`이라는 별칭으로 해서 그 안에 있는 것을 가져오는 것이고 `#{leisGdsId}`은 `@Param("leisGdsId")`으로 넘겨준 값을 사용한다고 생각하면 된다.


