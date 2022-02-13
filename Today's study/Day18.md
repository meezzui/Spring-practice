### 오늘의 일기
---
#### 포맷터(Formatter)
+ `Converter` : 입력과 출력 타입에 제한이 없는, 범용 타입 변환 기능을 제공한다.(객체 -> 객체)
+ `Formatter` : 객체를 문자로 변경하고, 문자를 객체로 변경하는 두 가지 기능을 수행한다. 문자에 특화(객체 -> 문자, -> 문자 객체) + 현지화(Locale - 날짜 숫자의 표현)


#### Formatter 인터페이스
```java
public interface Printer<T> {
  String print(T object, Locale locale);
}
public interface Parser<T> {
  T parse(String text, Locale locale) throws ParseException;
}
  public interface Formatter<T> extends Printer<T>, Parser<T> {
}
```

#### 포맷터(Formatter) 만들기
+ `String print(T object, Locale locale)` : 객체를 문자로 변경한다.
+ `T parse(String text, Locale locale)` : 문자를 객체로 변경한다.

``` java
@Override
 public Number parse(String text, Locale locale) throws ParseException {
   NumberFormat format = NumberFormat.getInstance(locale);
   return format.parse(text);
 }
 @Override
   public String print(Number object, Locale locale) {
   return NumberFormat.getInstance(locale).format(object);
 }
 ```
🎗 "23,000" 처럼 숫자 중간의 쉼표를 적용하려면 자바가 기본으로 제공하는 `NumberFormat` 객체를 사용하면 된다. 이 객체는 Locale 정보를 활용해서 나라별로 다른 숫자 포맷을 만들어준다.  
+ parse() 를 사용해서 문자를 숫자로 변환한다. 
+ 참고로 Number 타입은 Integer , Long 과 같은 숫자 타입의 부모 클래스이다.
+ print() 를 사용해서 객체를 문자로 변환한다.  

🎗 스프링은 용도에 따라 다양한 방식의 포맷터를 제공한다. [참고문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#format)  

#### 포맷터를 지원하는 컨버전 서비스
+ 컨버전 서비스에는 컨버터만 등록할 수 있고, 포맷터를 등록할 수 는 없다.
+ 포맷터를 지원하는 컨버전 서비스를 사용하면 컨버전 서비스에 포맷터를 추가할 수 있다. 
+ 내부에서 어댑터 패턴을 사용해서 Formatter 가 Converter 처럼 동작하도록 지원한다.
+ 포맷터를 지원하는 컨버전 서비스 : `FormattingConversionService`
+ `DefaultFormattingConversionService` : `FormattingConversionService` 에 기본적인 통화, 숫자 관련 몇가지 기본 포맷터를 추가해서 제공


#### 스프링이 제공하는 기본 포맷터
+ 스프링은 자바에서 기본으로 제공하는 타입들에 대해 수 많은 포맷터를 기본으로 제공한다.
+ 그런데 포맷터는 기본 형식이 지정되어 있기 때문에, 객체의 각 필드마다 다른 형식으로 포맷을 지정하기는 어렵다.
+ 스프링은 이런 문제를 해결하기 위해 애노테이션 기반으로 원하는 형식을 지정해서 사용할 수 있는 매우 유용한 포맷터 두 가지를 기본으로 제공한다.
  + `@NumberFormat` : 숫자 관련 형식 지정 포맷터 사용
  + `@DateTimeFormat` : 날짜 관련 형식 지정 포맷터 사용


#### 컨버터 VS 포메터 등록/사용 코드비교
+ 컨버터 등록
  + `conversionService.addConverter(new 해당 클래스 이름());`
+ 컨버터 사용(사용자가 만든 클래스로 변환하게 구현- 문자를 숫자로 변환하기)
  ```
  사용자가만든클래스 이름지정 = conversionService.convert("해당 문자", 만든 클래스.class);
  assertThat(지정한이름).isEqualTo(new 만든 클래스("문자", 숫자));
  ```
+ 포맷터 등록
  + conversionService.addFormatter(new 해당 클래스 이름());
+ 포맷터 사용(예- 23,000 문자변환)
  + `assertThat(conversionService.convert(23,000,String.class)).isEqualTo("23,000");`  
 
🎉 정리 🎉   
+ 컨버터와 포맷터는 등록 방법은 다르지만, 사용할 때는 컨버전 서비스를 통해서 사용할 수 있다. 
+ 컨버전 서비스는 @RequestParam , @ModelAttribute , @PathVariable , 뷰 템플릿 등에서 사용할 수 있다.

+ 참고📢 
  + 메시지 컨버터 `HttpMessageConverter`에는 컨버전 서비스가 적용되지 않는다.
  + 특히 객체를 JSON으로 변환할 때 메시지 컨버터를 사용하면서 오해하는 경우가 있다.
  + `HttpMessageConverter`의 역할은 HTTP 메시지 바디의 내용을 객체로 변환하거나 객체를 HTTP 메시지 바디에 입력하는 것이다.
  + 즉, JSON 결과로 만들어지는 숫자나 날짜 포맷을 변경하고 싶으면 해당 라이브러리가 제공하는 설정을 통해서 포맷을 지정해야 한다. 
  + 결과적으로 컨버전 서비스와 전혀 관계가 없다!!!





