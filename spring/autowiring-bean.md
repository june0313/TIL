# 자동으로 빈 와이어링 하기
>스프링을 사용하는 애플리케이션에서는 객체가 자신의 일을 하기 위해 필요한 다른 객체를 직접 찾거나 생성할 필요가 없다. 스프링 컨테이너가 협업할 개체에 대한 레퍼런스를 주기 때문이다. 
>
>Spring in action, 40p

애플리케이션 객체 간의 이러한 연관관계 형성 작업이 바로 종속객체 주입(DI) 개념의 핵심이며, 이를 보통 `와이어링(wiring)`이라 한다.

스프링은 두가지 방법으로 오토와이어링을 수행한다.
- `컴포넌트 스캐닝(Component Scanning)` - 애플리케이션에서 사용되는 빈을 자동으로 스캐닝한다.
- `오토와이어링(Autowiring)` - 자동으로 빈을 주입해준다.

## 발견 가능한 빈(Bean) 만들기
빈으로 만들어져아 하는 클래스에 `@Component` 어노테이션을 붙힌다.

```java
package soundsystem;

@Component
public class SgtPeppers implements CompactDisc {
    public void play() {
        System.out.println("Playing SgtPeppers");
    }
}
```

위의 클래스는 `@Component`어노테이션이 붙었으므로 스프링이 빈으로 만든다. 하지만 컴포넌트 스캐닝을 기본적으로 켜 있지 않다. `@Component`가 붙은 클래스를 찾기위해 명시적으로 설정을 해주어야 한다.

```java
package soundsystem;

@Congifuration
@ComponentScan
public class CDPlayerConfig {
}
```

설정 클래스에 `@ComponentScan` 어노테이션을 사용하면 동일한 패키지를 기본적으로 스캐닝하여 `@Component`가 붙은 클래스를 찾고 스프링 빈으로 만든다. (여기서는 soundsystem 패키지와 그 하위 패키지를 스캔)

## 컴포넌트 스캔된 빈 이름 지정하기
`@Component`어노테이션을 사용하면 기본적으로 클래스명의 첫 글자를 소문자로 바꾼 이름이 빈의 ID가 된다.
만약 빈에 다른 ID를 설정하고 싶으면 아래와 같이 적용한다.

```java
@Component("myBeanId")
```
혹은
```java
@Component(value = "myBeanId")
```

## 컴포넌트 스캐닝을 위한 베이스패키지 설정
`@ComponentScan`의 속성을 이용하여 컴포넌트를 스캐닝할 패키지(base package)를 설정할 수 있다.

```java
@ComponentScan("soundsystem")
```
혹은
```java
@ComponentScan(basePackages = "soundsystem")
```
여러개의 base package를 지정할 수 있다.
```java
@ComponentScan(basePackages = {"soundsystem", "video"})
```
패키지를 String으로 설정하지 않고 패키지 내의 클래스나 인터페이스를 지정할 수도 있다.
```java
@ComponentScan(basePackageClasses={CDPlayer.class, DVDPlayer.class})
```

## 빈 자동 주입하기
컴포넌트 스캔을 통해 생성된 빈을 필요한 곳에 자동 주입해주려면 `@Autowired`어노테이션을 사용한다.
아래 코드처럼 생성자에 `@Autowired`를 붙혀주면 CDPlayer빈을 생성할 때, CompactDisc에 대입 가능한 빈을 전달해준다.

```java
package soundsystem;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc cd;

    @Autowired
    public CDPlayer(CompactDisc cd) {
        this.cd = cd;
    }
}
```

`@Autowired` 애노테이션은 생성자로 한정되지 않는다. 프로퍼티 세터에도 사용된다.
```java
@Autowired
public void setCompactDisc(CompactDisc cd) {
    this.cd = cd;
}
```

세터가 아닌 어느 메소드에서도 사용 가능하다.
```java
@Autowired
public void insertDisc(CompactDisc cd) {
    this.cd = cd;
}
```

매칭되는 빈이 없으면 스프링은 애플리케이션 컨텍스트가 생성될 때 예외를 발생시킨다. 예외를 피하기 위해 `@Autowired(required = false)` 처럼 설정할 수 있다. 하지만 빈이 주입되지 않으므로 빈을 사용하는 코드에서 `NullPointerException`이 발생할 것을 유의해야 한다.