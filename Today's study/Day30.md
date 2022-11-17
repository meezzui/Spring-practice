#### 커스텀 어노테이션 만들기
+ 커스텀 어노테이션은 메타 어노테이션을 사용한다.
+ 구조
```node
@Target({ElementType.[적용대상]})
@Retention(RetentionPolicy.[정보유지되는 대상])
public @interface [어노테이션명]{
	public 타입 elementName() [default 값]
    ...
}
```
+ 메타 어노테이션의 종류
  + `@Retention` : 컴파일러가 어노테이션을 다루는 방법을 기술, 어느 시점까지 영향을 미치는지를 결정
    + `RetentionPolicy.SOURCE` : 컴파일 전까지만 유효
    + `RetentionPolicy.CLASS` : 컴파일러가 클래스를 참조할 때까지 유효
    + `RetentionPolicy.RUNTIME` : 컴파일 이후 런타임 시기에도 JVM에 의해 참조가 가능(리플렉션)

  + `@Target` : 어노테이션 적용할 위치 선택
    + `ElementType.PACKAGE` : 패키지 선언
    + `ElementType.TYPE` : 타입 선언
    + `ElementType.ANNOTATION_TYPE` : 어노테이션 타입 선언
    + `ElementType.CONSTRUCTOR` : 생성자 선언
    + `ElementType.FIELD` : 멤버 변수 선언
    + `ElementType.LOCAL_VARIABLE` : 지역 변수 선언
    + `ElementType.METHOD` : 메서드 선언
    + `ElementType.PARAMETER` : 전달인자 선언
    + `ElementType.TYPE_PARAMETER` : 전달인자 타입 선언
    + `ElementType.TYPE_USE` : 타입 선언
  + `@Documented` : 해당 어노테이션을 Javadoc에 포함시킴
  + `@Inherited` : 어노테이션의 상속을 가능하게 함
  + `@Repeatable` : Java8 부터 지원하며, 연속적으로 어노테이션을 선언할 수 있게 함
+ 적용✅
```node
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface Member {
}
```
+ 동작 원리
  + 어노테이션 타입 선언은 특별한 종류의 인터페이스로 친다. 일반적인 인터페이스와 구분하기 위해 `@기호 + interface`를 붙여 선언한다. 
  + 기술적으로 이 둘은 공백으로 분리가 가능하지만 스타일 문제로 분리하지 않는 것을 권장한다고 한다.
  + `@interface`는 자동으로 `Annotation` 클래스를 상속(확장)하며, 내부의 메소드들은 `abstract` 키워드가 자동으로 붙게 된다. 
  + 따라서 어노테이션 인터페이스는 `extends`절을 가질 수 없다.
  + 제약❌
    + 어노테이션 타입 선언은 제네릭일 수 없다.
    + 메소드는 매개변수를 가질 수 없다.
    + 메소드는 타입 매개변수를 가질 수 없다.
    + 메소드 선언은 `throws` 절을 가질 수 없다.


