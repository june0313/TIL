# RxJava를 활용한 리액티브 프로그래밍

- RxJava는 자바와 안드로이드를 위한 리액티브 프로그래밍 구현체이다.
- 함수형 프로그래밍의 영향을 받아서 전역 상태나 사이드 이펙트를 피한다.
- 비동기나 이벤트 기반 프로그램을 작성할 때 스트림 방식으로 생각한다.
- 생산자/소비자 콜백을 사용한 옵저버 패턴을 시작으로 구성과 변환, 스케줄링, 스로틀링, 오류 처리, 생명주기 관리를 할 수 있는 수 많은 연산자를 지원한다.

## 리액티브 프로그래밍과 RxJava
- 리액티브 프로그래밍이란 데이터나 이벤트 변화의 반응에 초점을 맞춘 프로그래밍을 뜻한다.
- 리액티브 함수형 프로그래밍 (RFP) ? https://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming/1030631#1030631
- RFP란 명령형 시스템상의 추상화이다.
- RFP를 사용하면 비동기 방식이나 이벤트 기반 요구사항을 구현할 때 컴퓨터처럼(명령형) 생각하지 않아도 되며, 상태들의 복잡한 상호작용을 명령형으로 정의하지 않아도 된다.
- RFP란 결국 `동시성`과 `병렬성` 해결이다. (비동기 요구사항을 명령형으로 만을었을 때의 문제점인 `콜백 지옥 문제`를 해결)

## 언제 리액티브 프로그래밍이 필요한가
다음 경우에는 리액티브 프로그래밍이 유용하다.
- 마우스 움직임, 클릭, 키보드 타이핑, GPS 신호, 자이로스코프 신호, 터치 이벤트 등을 처리할 때
- 본질적으로 비동기성을 띠는 디스크나 네트워크 지연 등 I/O 이벤트에 응답할 때
- 통제 불가능한 애플리케이션에서 발생하는 이벤트나 데이터를 다룰 때

단 하나의 이벤트 스트림만 처리하는 경우라면 `콜백 기반의 명령형 리액티브 프로그래밍`도 괜찮다.

하지만 대부분의 문제는 이벤트들을 묶어야 할 경우에 벌어지는데, 이런곳에서 명령형 리액티브 프로그래밍을 사용하면 복잡합이 극도로 증가하는 반면, 함수형 리액티브 프로그래밍은 빛을 발하기 시작한다. 함수형 리액티브 프로그래밍은 초기 러닝커브와 진입 장벽이 높지만 복잡도는 명령형에 비해 상당히 낮다.

`RxJava`는 비동기 이벤트 기반 프로그램을 작성하기 위한 라이브러리이다. RxJava는 함수형 프로그래밍과 데이터플로 프로그래밍에 영향을 받아 리액티브 프로그래밍 원칙들을 구현한 구현체이다.

## RxJava는 어떻게 동작하는가?
- RxJava의 핵심은 데이터나 이벤트의 스트림을 나타내는 `Observable`타입
- push 방식과 pull 방식을 모두 지원
- 지연 실행
- 동기/비동기 모두 사용 가능
- 0개, 1개, 다수, 무한개의 이벤트를 다룰 수 있음

### 밀어내기(push)와 끌어오기(pull)
- RxJava가 리액티브이기 위한 핵심은 밀어내기(push) 지원 여부이다.
- `Observable`과 `Observer` 타입은 이벤트 push를 지원한다.
- Observable은 데이터 스트림이며 Observer를 통해 구독(Subscribe)할 수 있다.

```java
interface Observable<T> {
    Subscription subscribe(Observer s);
}
```

Observer는 구독을 통해 3가지 이벤트를 받는다.
- `onNext` 함수를 통한 데이터
- `onError` 함수를 통한 오류(Exception or Throwable)
- `onComplated` 함수를 통한 스트림 완료 통보

```java
interface Observer<T> {
    void onNext(T t);
    void onError(Throwable t);
    void onCompleted();
}
```
onNext 메서드는 0번, 한번, 여러번, 무한히 호출될 수 있다. onError와 onCompleted는 종료 이벤트로 둘 중 하나만 단 한 번 호출될 수 있다. 종료 이벤트가 호출되면 Observable스트림은 끝나고 더이상 이벤트를 보낼 수 없다.

### 비동기와 동기
- 일반적인 Observable은 비동기로 동작하지만 꼭 비동기일 필요는 없음
- 명시적으로 비동기로 동작하도록 요청하지 않으면 동기로 실행된다
- **Observable 이벤트 생성의 중요한 기준은 블로킹/논블로킹 여부이지 동기/비동기가 아니다!**

### 동시성과 병렬성
- `동시성` : 여러 작업을 합성하거나 번갈아가면서 수행(interleaving)
- `병렬성` : 서로 다른 CPU나 기기에서 동시에 수행

RxJava 규약에 의하면 onNext, onError, onCompleted 이벤트는 동시에 방출되지 않는다. 즉, 하나의 Observable 스트림은 항상 직렬화되어 스레드에 안전해야한다.

RxJava에서 동시성 혹은 병렬성의 이득을 보려면 여러개의 Observable을 구성(compose)해야 한다. 하나의 Observable 스트림은 항상 직렬화 되어있지만, 각각의 Observable 스트림을 서로 독립적으로 조작할 수 있기 때문에 동시에 병렬 수행 가능하다. 이를 위해 RxJava에서는 merge연산자와 flatMap 연산자등을 사용한다.

### 느긋함과 조급함 (Lazy and Eager)
Observable이 느긋하다는 뜻은(lazy) 누군가 구독하지 않으면 아무것도 하지 않는다는 뜻이다. 

### 쌍대썽 (duality)
Observable은 Iterable과 `쌍대(dual)`이다.

| pull(Observable)  | push(Iterable)     |
| ----------------- | ------------------ |
| T next()          | onNext(T)          |
| throws Excemption | onError(Throwable) |
| returns           | onCompleted()      |

### 카디널리티 (Cardinality)
|              | 한 개               | 여러개                  |
| ------------ | ------------------- | ----------------------- |
| Synchronous  | T getData()         | Iterable<T> getData()   |
| Asynchronous | Future<T> getData() | Observable<T> getData() |

Future 대신 Observable이 쓸모있는 이유는 다중 값 응답을 다룰 수 있기 때문이다. 컬렉션 전체가 도착할 때 까지 기다리지 않고 항목을 받는 대로 처리할 수 있다.


## 리액티브 추상화
궁극적으로 RxJava의 타입과 연산자는 명령형 콜백 위에 쌓아올린 추상화이다. 