#### StringUtils.equals()
+ `equals()`는 `null` 일 경우 `NullPointerException` 예외가 발생하지만
+ `StringUtils.equals`
  + 거의 대부분의 문자열 처리를 수행.
  + 파라미터 값으로 `null`을 주더라도 절대 `NullPointException`을 발생시키지 않음.(null이 입력되는 경우, 메소드에 따라 알맞은 결과를 리턴.)


#### throw new ServerException
+ `throw`는 인위적으로 예외를 발생시키는 명령어 이다.
+ `throw` 뒤에는 예외 정보를 가지고 있는 예외 클래스가 위치한다.
+ 실행되는 `catch` 구문에서 다양한 예외 상황의 원인에 대한 정보를 얻을 수 있다.
+ 적용사례
  ```node
  catch (Exception e) {
      log.error(messageSource.getMessage("해당 에러 메시지 코드란"));
      throw new ServerException("해당 에러 메시지 코드란", messageSource.getMessage("해당 에러 메시지 코드란"));
  }
  ```
  
 #### stream() 함수
 + 컬렉션에 저장되어 있는 요소를 하나씩 참조하여 람다식으로 처리할 수 있도록 도와주는 반복자이다.
 + 람다식과 함께 사용되어 데이터에 대한 처리를 간결하게 작성할 수 있다는 장점이 있지만 내부 반복자라는 것을 사용하기 때문에 병렬처리가 쉽지 않다는 단점이 있다.
 + `stream().map`: 요소들을 조건에 맞게 변환할 수 있다.
