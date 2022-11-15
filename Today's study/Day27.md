#### @SuperBuilder 어노테이션
+ `@Builder`처럼 어떤 객체의 필드값들을 편하게 지정하기 위해서 사용한다. 
+ 다만, `super`라는 말에서처럼 부모 객체를 상속받는 자식 객체를 만들 때, 부모 객체의 필드값도 지정할 수 있게 하기 위해서 사용한다.
+ 적용사례🔅
  ```node
  // 상품 컨트롤러
  @ApiOperation(value = "[Admin] 레저 상품 상세 수정")
	@PutMapping("/leisure/{leisGdsId}")
	@LoginRequired
	public void leisGoodsDetailUpdate(@RequestBody @Valid GoodsRequest.LeisGdDtlUpdate request, @PathVariable String leisGdsId , @ApiIgnore @Member MemberImpl member){
		request.getLeisGdsBasicInfo().setLeisGdsId(leisGdsId);
		request.getLeisGdsBasicInfo().setRegId(member.getMemberId());
		goodsService.leisGoodsDetailUpdate(request);
	}
  ```
  + 컨트롤러에서 레저 상품 상세 수정 할 코드를 작성해주었다.
  + `set()`함수를 사용하여 `leisGdsId`와 `regId`를 셋팅해주려고 하는데 계속 에러가 났다.🛑
  + 그 이유를 보니까 `Request`부분에서 아래와 같이 상속받아서 쓰고 있기 때문이였다.
  ```node
  @ApiModel(value = "[Admin] 레저 상품 상세 수정")
	@Data
	@SuperBuilder
	@AllArgsConstructor
	@NoArgsConstructor
	public static class LeisGdDtlUpdate {
		@ApiModelProperty(notes = "기본 정보", position = 1, dataType = "ModLeisGood",required = true)
		private ModLeisGood leisGdsBasicInfo;
	}
  
  =============================================================================================================
  
  @ApiModel(value = "[Admin] 레저 상품 수정")
	@Data
	public static class ModLeisGood extends RegLeisGood{
		@ApiModelProperty(notes = "상품 아이디", position = 1, dataType = "String")
		private String leisGdsId;
	}
  ```
  + 상품 상세를 수정하는데 필요한 `Request`를 작성하는데 보통 수정은 등록되는 것과 틀이 비슷하기 때문에 등록할 때 사용되는 `Request`를 상속받고 더 필요한 부분을 추가해서 쓰는 방식으로 작성하였다.
  + 이처럼 상속을 받았기 때문에 그냥 `@Builder`가 아닌 `@SuperBuilder`를 사용해야 한다.
