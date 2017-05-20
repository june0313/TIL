# Spring4에서 SpringSecurity 설정하기

## 1. Gradle setting
SpringSecurity를 사용하기 위해서 먼저 spring-security-web, spring-security-config 모듈을 추가해야 한다. (웹 애플리케이션을 위한 최소한의 필수 모듈)
```groovy
dependencies {
	compile 'org.springframework.security:spring-security-web:4.2.2.RELEASE'
	compile 'org.springframework.security:spring-security-config:4.2.2.RELEASE'
}
```

## 2. 웹 요청에 대한 필터 등록하기
`springSecurityFilterChain` 필터를 등록한다. 고전적인 방법으로 web.xml 에 필터를 등록해도 되지만, 스프링 시큐리티가 제공하는 `AbstractSecurityWebApplicationInitializer` 클래스를 사용하여 필터를 등록할 수 있다.
```java
public class SecurityWebInitializer extends AbstractSecurityWebApplicationInitializer {

}
```
위 클래스를 작성하는것 만으로도 모든 URL 요청에 대해 `springSecurityFilterChain`필터가 등록된다.

## 3. Security Context 설정하기
아래처럼 자바 설정 클래스를 만든다.
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
}
```
- `@EnableWebSecurity` : 웹 보안을 활성화한다. 해당 클래스가 `WebSecurityConfigurer`를 구현하거나, `WebSecurityConfigurerAdapter`를 확장한 빈이어야 한다. 편의상 `WebSecurityConfigurerAdapter` 클래스를 상속받아 사용한다.

여기까지 설정하면 모든 요청에 대한 접근을 막고 내장된 로그인 페이지를 통해 인증 요청을 하게 된다.