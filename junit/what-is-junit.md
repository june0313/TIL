## 유닛 테스트(Unit Test)란?
- 컴퓨터 프로그래밍에서 소스 코드의 특정 모듈(클래스, 메서드 등)이 의도된 대로 정확히 작동하는지 검증하는 절차

## 유닛 테스트를 하는 이유
- 언제라도 코드 변경으로 인해 문제가 발생할 경우, 단시간 내에 이를 파악하고 바로 잡을 수 있도록 해줌
- 유닛 테스트를 믿고 리팩토링을 할 수 있다

## JUnit 이란?
- Java용 유닛 테스트 프레임워크
- 어노테이션 지원
- Eclipse와 Intellij 같은 IDE와 GUI 통합이 잘 되어있음
- 성공은 초록색, 실패는 붉은색

## JUnit 사용해보기(Gradle 환경)
### Gradle Dependency 추가
build.gradle 파일에 아래 코드를 추가한다. (JUnit 4 기준)
```groovy
apply plugin: 'java'

dependencies {
  testCompile 'junit:junit:4.12'
}
```

### 테스트 실행하기
커맨드 라인으로 테스트를 실행하려면 아래와 같이 입력한다.
```
./gradlew test
```
IDE를 사용한다면 IDE에 통합된 기능을 사용하여 테스트를 실행시킬 수 있다.

## JUnit 테스트 코드 예제

```java
public class ZTest {
    private Z sut;

    @Before
    public void setUp() {
        sut = new Z();
    }

    @Test
    public void testSmallestCase() {
        assertThat(sut.getOrder(1, 0, 0)).isEqualTo(0);
        assertThat(sut.getOrder(1, 0, 1)).isEqualTo(1);
        assertThat(sut.getOrder(1, 1, 0)).isEqualTo(2);
        assertThat(sut.getOrder(1, 1, 1)).isEqualTo(3);
    }

    @Test
    public void testVisitAllQuartile() {
        assertThat(sut.getOrder(4, 3, 5)).isEqualTo(27);
    }

    @Test(timeout = 2000)
    public void testWorstCase() {
        assertThat(sut.getOrder(15, 32767, 32767)).isEqualTo(1073741823);
    }
}
```

### @Test
- 테스트 대상 메소드를 의미한다.
- `@Test(timeout = 5000)` 처럼 메서드 수행 시간을 제한할 수 있다. 5000ms를 넘기면 테스트는 실패한다.
- `@Test(expected = RunntimeException.class)` 처럼 발생하길 기대하는 Exception 을 설정할 수 있다. 여기서는 RuntimeException 이 발생해야 테스트 성공, 그렇지 않으면 실패한다.
- 각 테스트 케이스의 순서는 보장되지 않으며 서로 독립적이어야 한다.

### @BeforeClass, @AfterClass
- `@BeforeClass` 어노테이션이 선언된 메소드는 테스트 실행 주기 안에서 가장 처음에 딱 한번만 실행된다.
- `@AfterClass` 어노테이션이 선언된 메소드는 테스트 실행 주기 안에서 가장 마지막에 딱 한번만 실행된다. 

### @Before, @After
- `@Before` 어노테이션이 선언된 메소드는 `@Test` 메소드가 실행되기 전에 매번 실행된다.
- `@After` 어노테이션이 선언된 메소드는 `@Test` 메소드가 실행된 후에 매번 실행된다.

### 테스트 생명 주기 다이어그램
![](https://www.safaribooksonline.com/library/view/java-ee-6/9781849683166/graphics/3166_04_03.jpg)

### 단정문 (Assertions)
- 테스트의 성공과 실패를 판단하도록 해주는 코드
- JUnit 에서 제공해주는 Assertions 말고 다른 라이브러리를 추가해 사용할 수 있다. (AssertJ 등..)

#### JUnit 기본 단정문의 종류
- `assertTrue(a)` : a가 true 임을 확인
- `assertEquals(a, b)` : a와 b의 값이 같은지 확인 (동등성 확인)
- `assertSame(a, b)` : a와 b가 같은 객체인지 확인 (동일성 확인)
- `assertNull(a)` : a가 null인지 확인
- `assertNotNull(a)` : a가 null이 아닌지 확인\
- ...
