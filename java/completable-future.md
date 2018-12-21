# CompletableFuture

## CompletableFuture.supplyAsync()
CompletableFuture의 팩토리 메서드인 `supplyAsync()`를 사용하면 간단하게 CompletableFuture를 만들 수 있다.

supplyAsync() 메서드는 파라미터로 받은 Supplier 코드를 별도의 스레드에서 실행시키기 위해 Executor를 사용하는데, 내부적으로 설정된 Default Executor를 사용할 수도 있고, 사용자가 정의한 Executor를 직접 넘겨줄 수도 있다.

### Default Executor를 사용하는 supplyAsync()
```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {
    return asyncSupplyStage(asyncPool, supplier);
}
```
supplyAsync() 메서드르 호출할 때 Supplier만 파라미터로 넘기면 내부적으로 정의된 `asyncPool`을 사용한다. `asyncPool`은 아래처럼 선언되어 있다.
```java
/**
* Default executor -- ForkJoinPool.commonPool() unless it cannot
* support parallelism.
*/
private static final Executor asyncPool = useCommonPool ?
    ForkJoinPool.commonPool() : new ThreadPerTaskExecutor();
```
기본적으로 ForkJoinPool.commonPool() 을 Executor로 사용하게 되는데, `Runtime.getRuntime().availableProcessors()`가 반환하는 개수 만큼의 스레드를 사용한다.

### 사용자 정의 Executor를 사용하는 supplyAsync()
```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor) {
    return asyncSupplyStage(screenExecutor(executor), supplier);
}
```
supplyAsync() 메서드의 두번째 인자로 Executor를 넘겨주면 해당 Executor에 작업을 위임한다.

사용 예
```java
CompletableFuture.supplyAsync(supplier(), Executors.newFixedThreadPool(100));
```

