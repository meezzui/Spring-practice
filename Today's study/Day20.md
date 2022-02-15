### WebSecurityConfigurerAdapter란?
+ 사용자 지정 자체 보안 구성을 원할 때, `WebSecurityConfigurerAdapter` 을 상속받아 구현하면 된다. 
+ 이렇게 하면 기본 자동 구성이 비활성화되고 사용자 지정 보안 구성이 활성화 된다.
+ `@EnableWebSecurity` 어노테이션을 사용하여 이 기능을 활성화 한다.
+ 기능⚙
  + 애플리케이션 내의 URL에 액세스하기 전에 사용자가 인증을 받아야한다.
  + 사용자 이름(username) ‘user’, 비밀번호 ‘password’, ‘ROLE_USER’역할의 사용자를 만든다.
  + HTTP 기본 및 form 기반 인증을 가능하게 한다.
  + pring security가 자동으로 login 페이지와 logout 성공 페이지를 자동으로 제공한다.

### BCrypt란?
+ 비밀번호를 암호화하는데 사용된다.
+ BCrypt는 단방향 해시 함수로 암호화는 가능하지만 복호화는 불가능하다. 
+ 따라서 원본을 알 수 없기에, 패스워드를 바로 데이터베이스에 저장하지 않고 암호화된 '다이제스트'를 데이터베이스에 저장하는 것이 일반적이다.
+ 데이터베이스에 비밀번호가 저장되는 순서
  + `비밀번호 -> 해시 함수 -> 다이제스트`
+ 다이제스트 : 임의 길이의 데이터 블록을 고정 길이로 변환하는 해시 알고리즘에서의 해시값을 가리킨다.

### 오늘의 일기
---
#### 스프링 시큐리티(Spring Security)
+ Spring Security는 보안 솔루션을 제공해주는 Spring 기반의 스프링 하위 프레임워크이다.
+ 개발자가 보안 관련 로직을 짤 필요가 없어 굉장히 간편하다.
+ 인증과 권한
  + 인증은 자신이 '누구'라고 주장하는 사람을 정말 '누구'가 맞는지 확인하는 작업이다.
  + 권한은 특정 부분에 접근할 수 있는지에 대한 여부를 확인하는 작업이다.

+ `build.gradle`에 의존성 추가
  + `implementation 'org.springframework.boot:spring-boot-starter-security'` : 스프링 시큐리티를 사용하기 위해 추가
	+ `implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity5'`: 뷰 단에서 현재 로그인된 사용자의 정보를 가져오기 위해 추가

+ Config 파일 작성
  + Config 파일은 `WebSecurityConfigurerAdapter`를 상속받아 구현한다.
  + `@EnableWebSecurity`사용 : Spring Security를 활성화한다.
  ```@Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/css/**", "/js/**", "/img/**");
    }
  ```
  + `WebSecurityConfigurerAdapter`를 상속받으면 오버라이드할 수 있다.
  + 인증을 무시할 경로들을 설정해놓을 수 있다.
    + static 하위 폴더 (css, js, img)는 무조건 접근이 가능해야하기 때문에 인증을 무시해야한다.
    + `antMatchers()` : 특정 리소스에 대해서 권한을 설정한다.
  ```
  @Override
  protected void configure(HttpSecurity http) throws Exception {
  
  }
  ```
  + WebSecurityConfigurerAdapter를 상속받으면 오버라이드할 수 있다.
    + http 관련 인증 설정이 가능하다.
    + `permitAll()` : antMatchers 설정한 리소스의 접근을 인증절차 없이 허용한다는 의미이다.(누구나 접근 가능)
    + `hasAnyRole(" ") -> antMatchers("/aa/**").hasAnyRole("AA")`: 리소스 aa으로 시작하는 모든 URL은 인증후 AA 레벨의 권한을 가진 사용자만 접근을 허용한다는 의미이다.(특정권한이 있는 사람만 접근 가능)
    + `authenticated()` : 권한이 있으면 무조건 접근 가능하다.
    + `anyRequest` : anyMatchers에서 설정하지 않은 나머지 경로를 의미한다.
  + `.formLogin()`: 로그인에 관한 설정을 의미한다.
    + `loginPage()` : 로그인 페이지 링크 설정
    + `defaultSuccessUrl()` : 로그인 성공 후 리다이렉트할 주소 설정
  + `.logout()`: 로드아웃에 관한 설정을 의미한다.
    + `logoutSccessUrl()` : 로그아웃 성공 후 리다이렉트할 주소 설정
    + `invalidateHttpSession()` : 로그아웃 이후 세션 전체 삭제 여부 설정(true- 세션 날리기)
  ```
  @Override
  public void configure(AuthenticationManagerBuilder auth) throws Exception {
    
   }
   ```
   + 유저 정보를 가져오는 서비스를 지정한다.
   + 패스워드 인코더는 passwordEncoder()를 사용한다.(BCrypt)
  
  
  
  
  
  
  
  
  
  

