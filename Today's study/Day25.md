#### LEFT JOIN 구현
+ 체험 상품 상세에 필요한 체험 시설에 있는 시설명을 가져와야 하는 경우가 발생하였다.
+ 이럴 경우 체험 시설 상세 테이블을 조인하여 시설명을 가져올 수 있다.
```node
SELECT LGI.LEIS_GDS_ID,
       LGI.GDS_NM_CN,
       LGI.LEIS_FCLT_ID,
       LGI.STA_CD,
       LGI.NTSL_BGNG_YMD,
       LGI.NTSL_END_YMD,
       LGI.VOCHR_VLD_TRM_SE_CD,
       LGI.VOCHR_VLD_TRM_VL_CN,
       LGI.TXTN_YN_CD,
       LGI.GDS_CLSF_CTGR_NO,
       LGI.XRT_INFO_VL,
       LGI.CSTMR_AMT_VL,
       LGI.NTSL_AMT_VL,
       LGI.SPLY_AMT_SE_CD,
       LGI.SPLY_AMT_SE_VL,
       LGI.INVR_MNG_YN_CD,
       LGI.GDS_QTY_VL,
       LGI.REG_ID,
       LGI.GDS_IMG_URL_VL,
       LGI.NTN_CSTMR_AMT_VL,
       LFI.LEIS_FCLT_NM_CN ->> '$.ko' AS LEIS_FCLT_NM_CN
FROM JTS_LEIS_GDS_INFO LGI
LEFT JOIN JTS_LEIS_FCLT_INFO LFI
ON LGI.LEIS_FCLT_ID = LFI.LEIS_FCLT_ID
WHERE LEIS_GDS_ID = '300000001';
```
+ `ON LGI.LEIS_FCLT_ID = LFI.LEIS_FCLT_ID` 이 부분 중요‼️
  + 처음에는 `ON LGI.LEIS_GDS_ID = LFI.LEIS_FCLT_ID` 이렇게 썼었다. 각 테이블에 시설 아이디와 상품 아이디가 일치하는 것을 찾아야 한다고 생각했었는데 완전 바보같은 생각이였다.(❌❌❌❌)
  + 각 테이블에 시설 아이디가 같은 걸 찾아야 해당 시설명을 가져다가 쓸 수 있다. 그래서 `ON LGI.LEIS_FCLT_ID = LFI.LEIS_FCLT_ID` 이렇게 해줘야 한다.
+ 그리고 SELECT 할 때 `LFI.LEIS_FCLT_NM_CN ->> '$.ko' AS LEIS_FCLT_NM_CN` 이렇게 해줘야 한다. 이게 조인한 테이블에 있는 시설명을 가져오는 쿼리이다.
+ `LFI.LEIS_FCLT_NM_CN ->> '$.ko' AS LEIS_FCLT_NM_CN` : `JTS_LEIS_FCLT_INFO`테이블에 있는 `LEIS_FCLT_NM_CN`라는 시설명을 검색하는데 
+ `'$.ko' AS LEIS_FCLT_NM_CN`이렇게 해줌으로써 한국어로된 이름만 검색한다는 의미이다.

#### mySQL 쿼리문이랑 프로젝트 안에 xml파일 안에 쓰는 mybatis 쿼리문 차이
+ 위에 코드가 데이터 그립. 즉, 디비 콘솔에 직접 쓴 코드이고 아래 코드는 xml 파일에서 쓰이는 mybatis 쿼리문이다.
```node
<!-- 상품 공통 상세 조회 -->
<select id="getLeisGdsDtlByLeisGdsId" resultType="leisGdsDtl">
  SELECT LGI.LEIS_GDS_ID,
       LGI.GDS_NM_CN,
       LGI.LEIS_FCLT_ID,
       LGI.STA_CD,
       LGI.NTSL_BGNG_YMD,
       LGI.NTSL_END_YMD,
       LGI.VOCHR_VLD_TRM_SE_CD,
       LGI.VOCHR_VLD_TRM_VL_CN,
       LGI.TXTN_YN_CD,
       LGI.GDS_CLSF_CTGR_NO,
       LGI.XRT_INFO_VL,
       LGI.CSTMR_AMT_VL,
       LGI.NTSL_AMT_VL,
       LGI.SPLY_AMT_SE_CD,
       LGI.SPLY_AMT_SE_VL,
       LGI.INVR_MNG_YN_CD,
       LGI.GDS_QTY_VL,
       LGI.REG_ID,
       LGI.GDS_IMG_URL_VL,
       LGI.NTN_CSTMR_AMT_VL,
       LGI.DSPLY_CTGRY_SEQ_NO,
       LFI.LEIS_FCLT_NM_CN ->> #{jsonLang} AS LEIS_FCLT_NM_CN
  FROM JTS_LEIS_GDS_INFO LGI
  LEFT JOIN JTS_LEIS_FCLT_INFO LFI
  ON LGI.LEIS_FCLT_ID = LFI.LEIS_FCLT_ID
  WHERE LEIS_GDS_ID = #{leisGdsId}
</select>
```
+ `id="getLeisGdsDtlByLeisGdsId"`: `id`는 `mapper`에 적어준 대로 그대로 써준다.
+ `resultType="leisGdsDtl"`: `resultType`은 VO에 있는 파일의 @Alias("")이 어노테이션에 적어준 이름값을 넣으면 된다.

  
