---
layout: post
title: "Spring PSA"
subtitle: Spring Portable Service Abstraction 
date: 2019-05-01 23:53:00 +0900
categories: Spring
tags: Spring PSA
---

# Spring Portable Service Abstraction 
---

## PSA
: 세부 기술에 특화된 코드가 변경되더라도 일관된 방식으로 접근할 수 있게 해주는 추상화 기법

* Spring 프레임워크에서 제공하는 대부분의 API는 추상화되어 있어 기존 코드의 변경없이 구현체 변경이 용이하다.
* 유연하고 확장성 좋은 코드로 테스트하기도 쉬워진다.

---

## 스프링의 대표적인 예

### 스프링 Web MVC
- Web MVC를 다루는 `@Controller`, `@RequestMapping` 등의 애노테이션을 사용하면 **Servlet** 기반의 프로그래밍을 지원한다. 
- Web MVC 코드의 변경없이 Spring5부터 지원하는 _WebFlux_ 의존성으로 변경하면 **Reactive** 기반의 프로그래밍으로 대부분 호환 가능하다. 

### 스프링 트랜잭션
트랜잭션을 처리하는 구현체가 변경되더라도 `@Transactional` 애노테이션을 변경할 필요없이 `PlatformTransactionManager` 인터페이스의 세부 기술에 해당하는 _JpaTransactionManager, HibernateTransactionManager, DataSourceTransactionManager_ 등의 구현체로 자유롭게 변경할 수 있다.

### 스프링 캐시
캐시를 다루는 `@Cacheable`, `@CacheEvict` 등의 애노테이션을 변경할 필요없이 `CacheManager` 인터페이스의 세부 기술에 해당하는 _JCacheManager, ConcurrentCacheManager, EhCacheCacheManager_ 등의 구현체로 자유롭게 변경할 수 있다.
