### DefaultConversionService란?
+ `ConversionService`의 구현체 중의 하나인 클래스이다.
+ 이 클래스는 두 가지 기능을 가진다.
  + `FormatterRegistry` : Formatter(Converter)를 등록하는 기능
  + `ConversionService` : Convert를 수행하는 기능

### 인터페이스 분리 원칙 - ISP(Interface Segregation Principal)
+ 인터페이스 분리 원칙은 `클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다.`라는 것을 말한다.
+ 클라이언트들이 꼭 필요한 메서드들만 이용할 수 있게 한다. 


### 오늘의 일기
---
#### 스프링 타입 컨버터
+ 문자를 숫자로 변환하거나, 반대로 숫자를 문자로 변환해야 하는 것 처럼 애플리케이션을 개발하다 보면 타입을 변환해야 하는 경우가 상당히 많다.
+ HTTP 요청 파라미터는 모두 문자로 처리된다.
+ 예) `http://localhost:8080/converter?data=10` -> HTTP 쿼리 스트링으로 전달하는 `data=10` 부분에서 10은 숫자 10이 아니라 문자 10이다.
+ 요청 파라미터를 자바에서 다른 타입으로 변환해서 사용하고 싶으면 숫자 타입으로 변환하는 과정을 거쳐야 한다.
```java
String data = request.getParameter("data"); //문자 타입 조회
Integer intValue = Integer.valueOf(data); //숫자 타입으로 변경
```
+ 그러나 스프링 MVC가 제공하는 기능으로 편리하게 바꿀 수 있다.
  + 스프링 MVC 요청 파라미터: `@RequestParam` , `@ModelAttribute` , `@PathVariable`
  + `@RequestParam` 예시(문자를 숫자로 바꿈)
  ```java
  @GetMapping("/converter")
  public String converter(@RequestParam Integer data) {
     System.out.println("data = " + data);
     return "ok";
  }
  ```
  
#### 컨버터 인터페이스
```java
package org.springframework.core.convert.converter;

public interface Converter<S, T> {
   T convert(S source);
}
```
+ 스프링은 확장 가능한 컨버터 인터페이스를 제공한다.
+ 스프링에 추가적인 타입 변환이 필요하면 이 컨버터 인터페이스를 구현해서 등록하면 된다.
+ 컨버터 인터페이스는 모든 타입에 적용할 수 있다.
+ 예를 들어서 문자로 "true" 가 오면 Boolean 타입으로 받고 싶으면 String -> Boolean 타입으로 변환되도록 컨버터 인터페이스를 만들어서 등록하고, 반대로 적용하고 싶으면 Boolean -> String 타입으로 변환되도록 컨버터를 추가로 만들어서 등록하면 된다. 
+ `T`가 뭐로 바꿀지 정하는 타입위치, `S`가 바뀌게 될 타입위치!!!🎊
+ 참고📢  과거에는 `PropertyEditor` 라는 것으로 타입을 변환했다. `PropertyEditor` 는 동시성 문제가 있어서 타입을 변환할 때 마다 객체를 계속 생성해야 하는 단점이 있다. 지금은 `Converter` 의 등장으로 해당 문제들이 해결되었다.
+ import문은 `org.springframework.core.convert.converter.Converter` 를 사용해야 한다.(`Converter`라는 인터페이스가 많으니 주의😉)
+ 예) 문자를 숫자로 변환하는 타입 컨버터 
```java
import org.springframework.core.convert.converter.Converter;

public class Converter1 implements Converter<String, Integer> {
 @Override
 public Integer convert(String source) {
   return Integer.valueOf(source);
   }
}
```
+ String -> Integer 로 변환하기 때문에 소스가 String 이 된다. 이 문자를 `Integer.valueOf(source)` 를 사용해서 숫자로 변경한 다음에 변경된 숫자를 반환하면 된다. 
+ 스프링은 용도에 따라 다양한 방식의 타입 컨버터를 제공한다. 🎁[참고 문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#coreconvert)🎁
  + `Converter` 기본 타입 컨버터
  + `ConverterFactory` 전체 클래스 계층 구조가 필요할 때
  + `GenericConverter` 정교한 구현, 대상 필드의 애노테이션 정보 사용 가능
  + `ConditionalGenericConverter` 특정 조건이 참인 경우에만 실행
  
#### 컨버전 서비스 - ConversionService
+ 타입 컨버터를 하나하나 직접 찾아서 타입 변환에 사용하는 것은 매우 불편하다.
+ 그래서 스프링은 개별 컨버터를 모아두고 그것들을 묶어서 편리하게 사용할 수 있는 기능을 제공하는데, 이것이 바로 `컨버전 서비스( ConversionService )`이다.
+ 컨버전 서비스 인터페이스는 단순히 컨버팅이 가능한가? 확인하는 기능과 컨버팅 기능을 제공한다.
```java
void conversionService() {
//등록
DefaultConversionService conversionService = new DefaultConversionService();
conversionService.addConverter(new Converter1());

//사용
assertThat(conversionService.convert("10",Integer.class)).isEqualTo(10);
=====================================================================================
//컨버전 서비스 사용
Integer value = conversionService.convert("10", Integer.class)
```
+ `DefaultConversionService` 는 인터페이스 분리 원칙에 의해 두 인터페이스를 구현했다.
  + `ConversionService` : 컨버터 사용에 초점
  + `ConverterRegistry` : 컨버터 등록에 초점
+ 이렇게 인터페이스를 분리하면 컨버터를 사용하는 클라이언트와 컨버터를 등록하고 관리하는 클라이언트의 관심사를 명확하게 분리할 수 있다. 즉, 컨버터를 사용하는 클라이언트는 꼭 필요한 메서드만 알게된다. 

#### 스프링에 적용하기
+ 스프링은 내부에서 `ConversionService` 를 제공한다. 
+ `WebMvcConfigurer`가 제공하는 `addFormatters()` 를 사용해서 추가하고 싶은 컨버터를 등록하면 된다. 
+ 이렇게 하면 스프링은 내부에서 사용하는 `ConversionService` 에 컨버터를 추가해준다.




