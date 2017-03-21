# 어노테이션 유지 정책
어노테이션을 정의할 때는 어노테이션 정보를 언제까지 유지할 것인지 설정해야 한다.

정책 | 설명
---- | ----
`RetentionPolicy.SOURCE` | 소스파일에서만 유지하고 컴파일 할 때 제거됨. 즉 클래스 파일에는 어노테이션 정보가 남아있지 않는다.
`RetentionPolicy.CLASS` | 클래스 파일에 기록되고 실행시에는 유지 되지 않는다. 즉 실행중에는 클래스에 기록된 어노테이션 값을 꺼낼 수 없음.(기본 정책)
`RetentionPolicy.RUNTIME` | 클래스 파일에 기록되고 실행시에도 유지된다. 즉, 실행 중에 클래스에 기록된 어노테이션 값을 참조할 수 있음.

어노테이션의 유지 정책을 지정하지 않으면 기본적으로 `RetentionPolicy.CLASS`가 된다.


커스텀 어노테이션을 정의하는 예
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    String value() default = "";
}
```