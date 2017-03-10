# ServletContextListener

## Listener(리스너)
서블릿 컨테이너는 웹 애플리케이션의 상태를 모니터링 할 수 있도록 주요 이벤트에 대한 알림 기능을 제공한다. 이벤트가 발생했을 때 알림을 받는 객체를 `Listener(리스너)`라고 한다. 웹 애플리케이션에서 이벤트가 발생하면 서블릿 컨테이너는 리스너의 메소드를 호출한다.

## Listener 파일의 배치
웹 애플리케이션의 특정 이벤트를 감시하고 처리하려면 서버에 Listener를 배치해야 한다. 배치하는 방법은 두가지가 있다.
1. 리스너를 쿠현한 클래스 선언 위에 `@WebListener` 어노테이션을 붙인다.
2. DD파일(web.xml)에 XML 태그로 선언한다.

어노테이션 예시
 ```java
 @WebListener
 public class ContextLoaderListener implements ServletContextListener {
     //...
 }
 ```

DD파일 선언 예시
```xml
<listener>
    <listener-class>ServletContextListener를 구현한 클래스</listener-class>
</listener>
```

## ServletContextListener
- 웹 애플리케이션의 **시작**과 **종료** 이벤트를 받아 처리할 수 있는 리스너
- `contextInitialized()`는 웹 애플리케이션이 시작될 때 호출된다.
- `contextDestroyed()`는 웹 애플리케이션이 종료되지 전에 호출된다.

## ServletContextListener의 활용
DAO 처럼 여러 서블릿이 사용하는 객체를 ServletContext에 저장하여 공유할 수 있도록 한다.


