# Spring4 에서 Spring Data JPA 설정하기

## 1. Gradle configuration
spring-data-jpa 를 사용하려면 spring-data-jpa 프로젝트와 JPA 구현체 라이브러리가 필요하다.(여기서는 hibernate 사용) 아래래 라이브러리들를 build.gradle dependencies()에 추가해준다.
```groovy
compile group: 'org.springframework.data', name: 'spring-data-jpa', version: '1.11.3.RELEASE'
compile group: 'org.hibernate', name: 'hibernate-core', version: '5.2.10.Final'
```

각 라이브러리의 최신 버전은 아래 링크를 참고한다.
- `hibernate-core` : [http://mvnrepository.com/artifact/org.hibernate/hibernate-core](http://mvnrepository.com/artifact/org.hibernate/hibernate-core)
- `spring-data-jpa` : [http://mvnrepository.com/artifact/org.springframework.data/spring-data-jpa](http://mvnrepository.com/artifact/org.springframework.data/spring-data-jpa)

## 2. Bean configuration
spring-data-jpa를 사용하기 위해 아래와 같이 Bean들을 설정해준다.

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(basePackageClasses = {Repository.class})
public class JpaConfig {

	@Bean
	public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource, JpaVendorAdapter jpaVendorAdapter) {
		LocalContainerEntityManagerFactoryBean entityManagerFactoryBean = new LocalContainerEntityManagerFactoryBean();
		entityManagerFactoryBean.setDataSource(dataSource);
		entityManagerFactoryBean.setJpaVendorAdapter(jpaVendorAdapter);
		entityManagerFactoryBean.setJpaProperties(additionalProperties());
		// entity 클래스들이 정의되어 있는 패키지 등록
		entityManagerFactoryBean.setPackagesToScan("package");
		return entityManagerFactoryBean;
	}

	@Bean
	public JpaVendorAdapter jpaVendorAdapter() {
		HibernateJpaVendorAdapter jpaVendorAdapter = new HibernateJpaVendorAdapter();
		jpaVendorAdapter.setShowSql(true);
		return jpaVendorAdapter;
	}

	@Bean
	public DataSource dataSource() {
		DriverManagerDataSource dataSource = new DriverManagerDataSource();
		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
		dataSource.setUrl("jdbc:mysql://localhost:3306/testdb");
		dataSource.setUsername("username");
		dataSource.setPassword("password");
		return dataSource;
	}

	@Bean
	public PersistenceExceptionTranslationPostProcessor exceptionTranslation(){
		return new PersistenceExceptionTranslationPostProcessor();
	}

	@Bean
	public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory){
		JpaTransactionManager transactionManager = new JpaTransactionManager();
		transactionManager.setEntityManagerFactory(entityManagerFactory);
		return transactionManager;
	}

	private Properties additionalProperties() {
		Properties properties = new Properties();
		properties.setProperty("hibernate.hbm2ddl.auto", "create-drop");
		properties.setProperty("hibernate.dialect", "org.hibernate.dialect.MySQL5Dialect");
		return properties;
	}

}
```

- `LocalContainerEntityManagerFactoryBean` : JPA를 스프링 컨테이너에서 사용할 수 있도록 스프링 프레임워크가 제공하는 EntityManagerFactoryBean. `spring-orm` 라이브러리가 제공한다.
- `JpaVendorAdapter` : 사용하고자 하는 JPA의 벤더를 설정한다. 여기서는 하이버네이트를 구현체로 하용하므로 `HibernateJpaVendorAdapter`를 사용함.
- `DataSource` : 사용할 DataSource. 여기서는 DriverManagerDataSource + MySQL 사용.
- `PlatformTransactionManager` : 트랜잭션 관리자 등록. JPA를 사용하려면 `JpaTransactionManager`를 등록해야 한다. `DataSourceTransactionManager`가 하던 역할도 수행하므로 JPA뿐 만 아니라 JdbcTemplate, MyBatis와 함께 사용할 수 있다.
- `PersistenceExceptionTranslationPostProcessor` : @Repository 어노테이션이 붙어있는 스프링 빈에 예외 변환 AOP를 적용한다. JPA예외를 스프링 프레임워크가 추상화한 예외로 변환해준다.