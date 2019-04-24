---
layout: post
title: "Spring AOP"
subtitle: Spring Aspect Oriented Programming 
date: 2019-04-25 00:03:00 +0900
categories: Spring
tags: Spring AOP
---

# Spring Aspect Oriented Programming 
---

## AOP
: 흩어진 **관심사**를 모듈화할 수 있는 프로그래밍 기법
* `OOP` 를 보완하는 수단으로 `프록시 패턴` 을 사용한다.
* 반복되는 일들은 다른 객체에 위임하고 클래스는 자기 역할에만 집중한다.

---

## AOP 주요 개념

Aspect
- 핵심 기능 외에 **부가되는 모듈**을 의미한다.
- 부가 기능인 `Advice` 와 어드바이스를 어디에 적용할지를 결정하는 `PointCut` 을 가지고 있다.

Advice 
- 부가 기능으로 `Aspect` 가 **해야할 일**들을 의미한다.

PointCut 
- 부가 기능을 **어디**에 적용해야 하는가를 의미한다.  
즉, `Advice`를 적용할 `JoinPoint` 를 선별하는 기능을 정의한 모듈을 의미한다.

Target 
- 부가 기능이 적용될 **대상**을 의미한다.

JoinPoint 
- `Advice` 가 적용될 수 있는 **위치**를 의미한다.
- 다른 AOP 프레임워크와 달리 Spring에서는 메소드 조인포인트만 제공한다.  
Spring 프레임워크 내에서 `JoinPoint` 는 메서드를 가리킨다 하여도 무방하다.

---

## 스프링 AOP 특징 
* 프록시 기반의 AOP 구현체 
* **스프링 빈에만** `AOP` 를 적용할 수 있다. 
* 모든 AOP 기능을 제공하는 것이 목적이 아닌 스프링 `IoC` 와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적이다. 

---

## 프록시 패턴 
: **기존 코드의 변경 없이** 접근 제어 혹은 부가 기능을 추가할 수 있다.

![Proxy Pattern](/img/spring/proxy-pattern.png)

### 문제점
* 매번 프록시 클래스를 작성해야 한다. 
* 여러 클래스의 여러 메소드에 적용해야 하는 경우도 있다. 
* 객체들 관계도 복잡하다.

### 해결책 : 스프링 AOP 
* 스프링 IoC 컨테이너가 제공하는 기반 시설과 동적 프록시를 사용하여 여러 복잡한 문제를 해결한다.
* 동적 프록시 : 동적으로 프록시 객체를 생성하는 방법 
    * 자바가 제공하는 방법은 인터페이스 기반 프록시 생성
* 스프링 IoC : 기존 빈을 대체하는 동적 프록시 빈을 만들어 등록시켜준다. 
    * 클라이언트 코드의 변경 없음

---

## 애노테이션 기반의 스프링 AOP
 
의존성 추가
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

Aspect 정의
* @Aspect
* 빈으로 등록되어야 하므로 컴포넌트 스캔 사용시 @Component 도 추가한다.

PointCut 정의
* @Pointcut(표현식)
    * execution
    * @annotation
    * bean
* 포인트컷 조합
    * `&&`
    * `||`
    * `!`

Advice 정의
* @Before : JoinPoint **이전**에 실행되는 Advice
* @AfterReturning : JoinPoint 메서드의 **정상적인 호출 이후**에 실행되는 Advice
* @AfterThrowing : JoinPoint 메서드에서 **예외가 발생한 이후**에 실행되는 Advice
* @Around : JoinPoint **이전과 이후**에 실행되는 Advice

> 예제) 스프링 AOP를 사용하여 메서드 실행시간 로깅하기

```java
// Aspect 에서 어디에 적용할지 표시할 용도로 사용된다.
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime { 
    
}
```

```java
// Service 클래스의 get 메서드나 @LogExecutionTime 애노테이션이 정의된 메서드의 
// 실행시간을 로깅하는 Aspect 구현
@Slf4j
@Aspect
@Component
public class LogAspect {

    @Pointcut("execution(* com..*.*Service.*(..))")
    private void serviceLayer() {}

    @Pointcut("execution(* get*(..))")
    private void getMethod() {}

    @Pointcut("serviceLayer() && getMethod()")
    private void serviceLayerGetMethod() {}

    @Around("serviceLayerGetMethod() || @annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        Object proceed = joinPoint.proceed();
        stopWatch.stop();
        log.info(stopWatch.prettyPrint());
        return proceed; 
    }
}

```
