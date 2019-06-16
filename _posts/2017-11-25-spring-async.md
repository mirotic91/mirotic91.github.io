---
layout: post
title: "Spring Async"
subtitle: Spring 메서드 비동기로 처리하기
date: 2017-11-25 12:00:00 +0900
categories: Spring
tags: Spring Async
---

## Spring Async
- 메서드를 비동기로 처리하면 호출한 메서드가 완료될 때 까지 기다리지 않고 다음 작을 수행한다.
- 비동기로 수행되는 메서드는 별도의 스레드에서 실행된다.

---

### Async Configuration

```java
// Java Config
@Configuration
@EnableAsync
public class SpringAsyncConfig {
    
}
```

```xml
<!--XML Config-->
<task:executor id="myexecutor" pool-size="5"/>
<task:annotation-driven executor="myexecutor"/>
```

---

### @Async
- `@Async` 로 명시된 **메서드는 반드시 public 으로 선언되어야 한다**. 메서드가 public 이어야 프록시가 될 수 있기 때문이다.
- **같은 클래스 내에서 비동기 메서드를 호출할 경우 비동기로 작동하지 않는다**. 셀프 호출은 프록시를 우회하고 해당 메서드를 직접 호출하기 때문이다.

#### void 반환형
반환값 없이 간단하게 비동기 메서드를 실행할 수 있.
```java
@Async
public void asyncMethodWithVoidReturnType() {
    System.out.println("Execute method asynchronously. "
      + Thread.currentThread().getName());
}
```

#### Future 반환형
스프링은 `Future` 를 구현하는 `AsyncResult` 클래스를 제공한다. 이는 비동기 메서드 실행 결과를 추적하는 데 사용할 수 있다.
```java
@Async
public Future<String> asyncMethodWithReturnType() {
    System.out.println("Execute method asynchronously - "
      + Thread.currentThread().getName());
    try {
        Thread.sleep(5000);
        return new AsyncResult<String>("hello world !!!!");
    } catch (InterruptedException e) {
        //
    }
 
    return null;
}

public void testAsyncAnnotationForMethodsWithReturnType() throws InterruptedException, ExecutionException {
    System.out.println("Invoking an asynchronous method. "
      + Thread.currentThread().getName());
    Future<String> future = asyncAnnotationExample.asyncMethodWithReturnType();
 
    while (true) {
        if (future.isDone()) {
            System.out.println("Result from asynchronous process - " + future.get());
            break;
        }
        System.out.println("Continue doing something else. ");
        Thread.sleep(1000);
    }
}
```

---

### Executor
- 스프링은 기본값으로 `SimpleAsyncTaskExecutor` 를 사용하여 메서드를 비동기로 처리한다. 
- 기본 설정은 메서드/애플리케이션 레벨로 재정의하여 사용할 수 있다.
- 아래와 같이 설정하면 비동기 메서드를 실행하는 기본 실행자가 변경된다. 실행자의 상세 옵션들도 변경할 수 있다.

#### Method Level Override
실행자 빈을 설정하고 @Async 속성에 실행자명을 명시해야 한다.
```java
@Configuration
@EnableAsync
public class SpringAsyncConfig {

    @Bean(name = "threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
        return new ThreadPoolTaskExecutor();
    }
}

@Async("threadPoolTaskExecutor")
public void asyncMethodWithConfiguredExecutor() {
    System.out.println("Execute method with configured executor - "
      + Thread.currentThread().getName());
}

```

#### Application Level Override
`AsyncConfigurer` 인터페이스를 구현하여 getAsyncExecutor() 메서드를 재정의해야 한다.
```java
@Configuration
@EnableAsync
public class SpringAsyncConfig implements AsyncConfigurer {

    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(100);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("TEST_");
        executor.initialize();
        return executor;
    }

}

```

---

### Exception Handling
- 반환형이 void일 때, 예외는 호출 스레드에 전달되지 않는다. 예외 처리를 위한 설정이 추가적으로 필요하다.
- `AsyncUncaughtExceptionHandler` 인터페이스를 구현하여 비동기 예외처리자를 커스터마이징할 수도 있다. 
handleUncaughtException() 메서드는 잡히지 않은 비동기 예외가 발생할때 호출된다.

```java
@Configuration
@EnableAsync
public class SpringAsyncConfig implements AsyncConfigurer {

    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(100);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("TEST_");
        executor.initialize();
        return executor;
    }

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new SimpleAsyncUncaughtExceptionHandler();
    //  return new CustomAsyncExceptionHandler();
    }

}

public class CustomAsyncExceptionHandler implements AsyncUncaughtExceptionHandler {

    @Override
    public void handleUncaughtException(Throwable throwable, Method method, Object... obj) {
        System.out.println("Exception message : " + throwable.getMessage());
        System.out.println("Method name : " + method.getName());
        for (Object param : obj) {
            System.out.println("Parameter value : " + param);
        }
    }

}

```

---

- Async 어노테이션을 사용하는 순간 서로 다른 스레드에서 동작하기 때문에 앞에서 생성한 `Transaction` 이 연장되지 않으므로 주의해야 한다.
- Spring Boot 기반의 웹 애플리케이션은 HTTP 요청이 들어왔을 때, 내장된 서블릿 컨테이너가 관리하는 독립적인 1개의 Worker 스레드에 의해 동기 방식으로 실행된다. 
하지만 요청 처리 중 `@Async` 가 명시된 메서드를 호출하면 앞서 등록한 `ThreadPoolTaskExecutor` 빈에 의해 관리되는 또 다른 독립적인 Worker 스레드에서 실행된다. 
별도의 스레드에서 동작하기 때문에 원래의 요청 스레드는 그대로 다음 문장을 실행하여 HTTP 응답을 마칠 수 있다.

<br>
Reference
- *[Baeldung](http://www.baeldung.com/spring-async)*