### HQL 쿼리란?
+ HQL은 SQL과는 거의 비슷하지만 테이블명이나 컬럼명을 쓰는 것이 아니라 맵핑 된 클래스명과 멤버변수명을 쓰는 것이 다르다.

### 동적쿼리란?
+ 동적쿼리란 실행시에 쿼리문장이 만들어져 실행되는 쿼리문을 말한다.
+ 쿼리문이 변하지 않으면 정적쿼리 변하면 동적쿼리로 생각하면 된다.


### 오늘의 일기
---
#### QueryDSL
+ QueryDSL이란?
  + Querydsl 이란 이전에 Spring boot + Spring data jpa 기술들의 조합으로 해결하지 못하는 한계점을 해결하기 위해 나온 프레임워크이다.(JPQL 빌더 역할을 해주는 오픈소스 프로젝트)
  + 여기서 한계점이란 실무에서 복잡한 쿼리를 다루고 조건에 따라 복잡한 동적 쿼리들을 생성할 일이 많아질 수 있다는 것이다.
  + Querydsl의 큰 장점은 쿼리를 "자바 코드"로 작성하여 문법 오류를 컴파일 시점에 확인할 수 있다는 것이다.
  
+ Gradle 설정하기
  + plugins에 `id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"` 추가
  + dependencies에 `implementation 'com.querydsl:querydsl-jpa'`추가
  + QueryDSL 사용 경로 설정
  ```
  def querydslDir = "$buildDir/generated/querydsl"

  querydsl {
      jpa = true
      querydslSourcesDir = querydslDir
  }

  sourceSets {
      main.java.srcDir querydslDir
  }

  configurations {
      querydsl.extendsFrom compileClasspath
  }

  compileQuerydsl {
      options.annotationProcessorPath = configurations.querydsl
  }
  ```
  + `def querydslDir = "$buildDir/generated/querydsl"`: querydsl에서 사용할 경로를 선언
  + `querydsl { jpa = true  querydslSourcesDir = querydslDir }` : querydsl 설정을 추가. JPA 사용 여부와 사용할 경로를 지정
  + `sourceSets { main.java.srcDir querydslDir}`: build시 사용할 sourceSet을 추가
  + `configurations { querydsl.extendsFrom compileClasspath }` : querydsl이 compileClassPath를 상속하도록 설정
  + `compileQuerydsl { options.annotationProcessorPath = configurations.querydsl }` : querydsl 컴파일시 사용할 옵션을 설정

+ Q클래스 생성  
  
  
  
  
  
  
  
  
  
  
  
  
