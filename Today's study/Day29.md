#### 상세 정보 수정(UPDATE) - mybatis 사용
+ 상품의 상세 정보를 수정하는 API 만들기
+ 테이블 별로 업데이트를 해줘야 한다. => `공통 상세 / 이미지 `
+ 수정은 리턴값을 넘겨줄게 없기 때문에 리턴타입은 `void`로 한다🛑
+ 순서✅
  + `Request` 작성
    + `Request`의 형태는 `Response`의 형태와 비슷하다. 왜냐하면 등록된 것들을 수정하는 것이기 때문에 그 틀이 비슷하기 때문이다. 
    + 단, 수정할 때 어떤 것을 수정할 것인지 알아야 하기 때문에 수정할 해당 상품의 id값이 필요하다.😡
    ```node
    @ApiModel(value = "[Admin] 레저 상품 상세 수정")
    @Data
    @SuperBuilder
    @AllArgsConstructor
    @NoArgsConstructor
    public static class LeisGdDtlUpdate {
      @ApiModelProperty(notes = "기본 정보", position = 1, dataType = "ModLeisGood",required = true)
      private ModLeisGood leisGdsBasicInfo;
      @ApiModelProperty(notes = "이미지 목록", position = 2, dataType = "List", required = true)
      private List<LeisGdImgInfo> images;
    }
    ```
    + 기본 정보는 기존에 등록 `request`를 상속받아서 사용한다.
    ```node
    @ApiModel(value = "[Admin] 레저 상품 수정")
    @Data
    public static class ModLeisGood extends RegLeisGood{
      @ApiModelProperty(notes = "상품 아이디", position = 1, dataType = "String")
      private String leisGdsId;
    }
    ```
    + 이미지 목록과 상세 내용은 리스트 형태로 사용된다. => 리스트 형태이기 때문에 나중에 쿼리문을 작성할 때 반복문을 돌려줘야 한다.😎


  + `Controller` 작성
    + 수정은 리턴할게 없으니까 리턴타입은 `void`
    ```node
    @ApiOperation(value = "[Admin] 레저 상품 상세 수정")
    @PutMapping("/leisure/{leisGdsId}")
    @LoginRequired
    public void leisGoodsDetailUpdate(@RequestBody @Valid GoodsRequest.LeisGdDtlUpdate request, @PathVariable String leisGdsId , @ApiIgnore @Member MemberImpl member){
      request.getLeisGdsBasicInfo().setLeisGdsId(leisGdsId);
      request.getLeisGdsBasicInfo().setRegId(member.getMemberId());
      goodsService.leisGoodsDetailUpdate(request);
    }
    ```
    + `request.getLeisGdsBasicInfo().setLeisGdsId(leisGdsId);`
      + 이렇게 해준 이유는 현재 `goodsService.leisGoodsDetailUpdate(request);`이렇게 request만 보내서 `leisGdsId`값이 필요해서 추가해주었다.
    + `request.getLeisGdsBasicInfo().setRegId(member.getMemberId());`
      + 칼럼 중에 `MDFCN_ID`라는 등록 아이디 칼럼이 있는데 이것은 공통으로 빼놓은 커스텀 어노테이션에서 가져온다. => `@Member MemberImpl member` 
    + `@LoginRequired`: 이것 또한 커스텀 어노테이션이다. 이 어노테이션은 로그인 권한을 주는 어노테이션으로 이 어노테이션을 달아줘야 `Member`에 접근할 수 있다.


  + `Service` 작성
    + 참고📢 서비스단 로직 구현할 때 쿼리문을 먼저 작성해보면 어떤 것을 넘겨줘야 하는지 생각하기 더 쉽다.😉
    ```node
    public void leisGoodsDetailUpdate(GoodsRequest.LeisGdDtlUpdate request){

        // 기본 정보 수정
        GoodsRequest.ModLeisGood modLeisGood = request.getLeisGdsBasicInfo();
        goodsMapper.updateDtlInfo(modLeisGood);

        if(request.getImages().size() > 0) { // 이미지는 없을 수도 있으니까 이미지 크기가 0 보다 클 경우에만 실행되도록 if문 걸어주기
            // 이미지를 수정할 경우 기존에 있던 이미지를 삭제하고 다시 등록하는 방식으로 구현해야 한다.💢 => 왜냐!! 여러개를 업데이트할 수 있으니까아
            // 기존 이미지 삭제
            goodsMapper.delLeisImgInfo(modLeisGood.getLeisGdsId()); // 해당 상품 아이디로 삭제

            // 신규 이미지 등록
            goodsMapper.insertDtlImgInfo(request.getImages(), modLeisGood.getLeisGdsId()); // 이미지 리스트를 가져오기, 해당 상품 아이디값 넘겨주기
        }
    }
    ```
    
  + `Mapper` 작성
    ```node
    // 상품 공통 상세 수정
    void updateDtlInfo(GoodsRequest.ModLeisGood modLeisGood);
    
    // 상품 상세 이미지 삭제
    void delLeisImgInfo(@Param("leisGdsId") String leisGdsId);

    // 상품 상세 이미지 신규 등록
    void insertDtlImgInfo(@Param("images") List<GoodsRequest.LeisGdImgInfo> images, @Param("leisGdsId") String leisGdsId);
    ```
    + `@Param("details")` : `List<GoodsRequest.LeisGdDtlInfo> details`의 별칭이다.
      + @Param("") 안에는 넘겨주는 것의 별칭을 넣어주면 되는데 `List<GoodsRequest.LeisGdDtlInfo> details`이렇게 할 경우 쿼리문을 짤때 불편하고 에러가 날 수 있기 때문에 되도록이면 별칭을 사용하는 것이 좋다.

  + `xml` 파일 작성(쿼리문 작성- 매핑)
    + 레저 상품 공통 상세 수정☀️
    ```node
        <update id="updateDtlInfo">
        UPDATE JTS_LEIS_GDS_INFO LGI
        SET LGI.GDS_NM_CN           = #{gdsNmCn},
            LGI.STA_CD              = #{staCd},
            LGI.NTSL_BGNG_YMD       = #{ntslBgngYmd},
            LGI.NTSL_END_YMD        = #{ntslEndYmd},
    ..............................................(생략)..............................................
            LGI.MDFCN_ID            = #{regId},
            LGI.MDFCN_DT            = NOW()
        WHERE LEIS_GDS_ID = #{leisGdsId};
    </update>
    ```
    + 리턴타입이 없기 때문에 resultType은 필요없다🔥
    + `id="updateDtlInfo"`: Mapper에 작성한 이름과 동일하게 적용- 매핑!!
    + ` LGI.MDFCN_DT = NOW()`: datetime() 함수를 사용 
    + 레저 상품 상세 이미지 삭제☀️
    ```node
    <delete id="delLeisImgInfo">
        DELETE
        FROM JTS_LEIS_GDS_IMG_INFO
        WHERE LEIS_GDS_ID = #{leisGdsId}
    </delete>
    ```
    + 레저 상품 상세 이미지 새로 등록☀️
    ```node
    <insert id="insertDtlImgInfo">
        INSERT INTO JTS_LEIS_GDS_IMG_INFO
        ( LEIS_GDS_ID
        , IMG_URL_VL
        , EXPSR_SER_VL)
        VALUES
        <foreach item="item" collection="images" separator=",">
            (
                #{leisGdsId}
                ,#{item.imgUrlVl}
                ,#{item.expsrSerVl}
               )
        </foreach>
    </insert>
    ```
    + 여기서 유심히 봐야할 포인트🔥 : 바로 `<foreach>`문‼️
    + 리스트안에 객체를 반복문을 통해 넣어준다.  
☀️ 흐름!! ☀️ `Request` -> `Controller` -> `Service` -> `Mapper` -> `xml` 

    
    


