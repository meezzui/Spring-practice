#### @Min(숫자) 어노테이션
+ 지정 값 이상인가

#### @ApiModelProperty 어노테이션
+ 설명(값), 이름, 데이터 유형, 예제 값 및 모델 속성에 대해 허용되는 값과 같은 Swagger 관련 정의를 제어할 수 있다.
+ 예) `@ApiModelProperty(notes = "등록 아이디", position = 2, dataType = "String", required = true)`
  + `position`은 문서에서의 위치 `required = true` 필수값. 즉, 값이 있어야만 실행됨
