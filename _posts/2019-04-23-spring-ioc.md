---
layout: post
title: "Spring IoC"
subtitle: Spring Inversion of Control 
date: 2019-04-23 23:39:00 +0900
categories: Spring
tags: Spring IoC
---

# Spring Inversion of Control
---

## IoC/DI
: 어떤 객체가 사용하는 의존 객체를 **직접** 만들어 사용하는 것이 아닌 **주입** 받아 사용하는 방법을 뜻한다.
* 애플리케이션 컴포넌트의 중앙 저장소
* 빈 설정 소스로 부터 빈 정의를 읽어들이고, 빈을 구성하고 제공한다.

```java

// before
class OwnerController { 
    
    private OwnerRepository repository = new OwnerRepository();
}

// after
class OwnerController { 
    
    private OwnerRepository repository; 
    
    public OwnerController(OwnerRepository repository) { 
        this.repository = repository; 
    } 
}

```

---

## Spring IoC Container _(=ApplicationContext)_
: `빈` 인스턴스를 생성하고 필요로 하는 의존성을 찾아서 주입해주는 역할을 한다.   
인스턴스의 생명주기를 대신 관리해주어 직접 다룰 일은 거의 없다.

---

## Bean
: `Spring IoC Container` 가 관리하는 객체  
이름, 클래스, 스코프, 프로퍼티 등을 정의할 수 있다.

### 빈으로 등록하면 좋은 이유
* 의존성을 주입 받아 사용할 수 있다.
* Scope
    * **Singleton** : 객체를 한번만 생성해서 재사용한다. _default_
    * Prototype : 매번 다른 객체를 생성한다.
    * ...
* 라이프사이클 인터페이스를 사용할 수 있다.
    * ex) @PostConstruct, @PreDestroy

### 빈으로 등록하는 방법
* @Component
    * @Repository
    * @Service
    * @Controller
* @Bean 직접 등록 (단, @Configuration 명시된 클래스에서)

### 등록된 빈을 사용하는 방법
* @Autowired or @Inject
* ApplicationContext.getBean() 직접 사용

```java

@Component
public class SampleApplicationRunner implements ApplicationRunner {

    @Autowired
    private MemberRepository memberRepository;

    @Override
    public void run(ApplicationArguments args) {
        memberRepository.save(new Member("Torreira", "Lucas"));
    }
}

@Repository
public class MemberRepository {
    
    void save(Member member);
}

```

---

## Component Scan
* 특정 패키지 이하의 모든 클래스 중에 `@Component` 애노테이션을 사용한 클래스를 자동으로 빈으로 등록해준다.
* 설정 방법
    * XML 설정 - `<context:component-scan>`
    * 자바 설정 - `@ComponentScan`
        * _basePackageClasses_ 속성을 사용하여 TypeSafe 한 패키지 설정 가능
        * _excludeFilters_ 속성을 사용하여 스캔 제외 대상 설정 가능

---

## 의존성 주입 _(Dependency Injection)_
: 클래스간의 의존관계를 빈을 기반으로 컨테이너가 자동으로 연결해주는 것

### 필요한 의존성을 받아오는 방법
* Field - @Autowired or @Inject
* Setter
* Constructor
    * 어떠한 빈이 되는 클래스에 **생성자가 하나**만 있고, 그 생성자 파라미터에 **빈**을 넘겨주면 그 빈을 주입해준다.

> Lombok 의 생성자를 이용하여 간단하게 적용 가능

```java
@RequiredArgsConstructor
class OwnerController {

    private final OwnerRepository repository;
}

```