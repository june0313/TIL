# 컨테이너
스프링 컨테이너는 객체를 생성하고, 엮어주면서 라이프사이클을 관리한다.

## 스프링 컨테이너의 두가지 분류
### 1. 빈팩토리
- `org.springframework.beans.factory.BeanFactory`
- DI에 대한 기본적인 지월을 제공하는 가장 단순한 컨테이너

### 2. 애플리케이션 컨텍스트
- `org.springframework.context.ApplicationContext`
- 빈 팩토리의 확장
- 빈 팩토리와 유사하지만 좀 더 많은 기능을 제공

## 애플리케이션 컨텍스트
스프링에는 다양한 애플리케이션 컨텍스트가 있다.
- AnnotationConfigApplicationContext : 하나 이상의 자바 기반 설정 클래스에서 스프링 애플리케이션 컨텍스트를 로드
- AnnotationConfigWebApplicationContext : 하나 이상의 자바 기반 설정 클래스에서 스프링 웹 애플리케이션 컨텍스트를 로드
- ClassPathXmlApplicationContext : 클래스패스에 위치한 XML 파일에서 컨텍스트를 로드
- FileSystemXmlApplicationContext : 파일 경로로 지정된 XML 파일에서 컨텍스트를 로드
- XmlWebApplicationContext : 웹 애플리케이션에 포함된 XML 파일에서 컨텍스트를 로드

## 애플리케이션 컨텍스트를 로드하는 예
파일 시스템 XML
```java
ApplicationContext context = new FileSystemXmlApplicationContext("/Users/wayne/knight.xml");
```

클래스패스 XML
```java
ApplicationContext context = new ClassPathXmlApplicationContext("knight.xml");
```

Java Config
```java
ApplicationContext context = new AnnotationConfigApplicationContext(KnightConfig.class);
```

애플리케이션 컨텍스트를 얻은 다음, `context.getBean()` 메소드를 호출하여 스프링 컨테이너에서 빈을 조회한다.