---
layout: post
title: "Spring Cache"
subtitle: 스프링 캐시 적용하기 
date: 2017-10-06 12:00:00 +0900
categories: Spring
tags: Spring Cache
---

## 캐시 추상화
캐시 추상화는 Java 메서드에 캐싱을 적용함으로써 **캐시에 보관된 정보로 메서드의 실행 횟수를 줄여준다.**
즉, 대상 메서드가 실행될 때마다 추상화가 해당 메서드가 같은 인자로 이미 실행되었는지 확인하는 캐싱 동작을 적용한다.  
해당 데이터가 존재한다면 실제 메서드를 실행하지 않고 결과를 반환하고,
존재하지 않는다면 메서드를 실행하여 그 결과를 캐싱한 뒤에 사용자에게 반환해서 다음 호출 시에 사용할 수 있도록 한다.

---

### @Cacheable
- 캐시를 적용할 수 있는 메서드를 **지정**할 때 사용한다. 즉, 캐시를 통해 메서드 실행을 건너뛸 수 있다.
- 캐시는 본질적으로 `key-value` 저장소이므로 캐시된 메서드를 호출할 때마다 해당 키로 변환되어야 한다.
- 캐시 추상화는 다음 알고리즘에 기반을 둔 KeyGenerator를 사용한다.
 1. 파라미터가 없으면 0을 반환한다.
 2. 파라미터가 하나만 있으면 해당 인스턴스를 반환한다.
 3. 파라미터가 둘 이상이면 모든 파라미터의 해시를 계산한 키를 반환한다.

### @CachePut
- 메서드 실행에 영향을 주지 않고 캐시를 **갱신**할 때 사용한다. 즉, 메서드를 항상 실행하고 어노테이션 옵션에 따라 그 결과를 캐시에 보관한다.

### @CacheEvict
- 캐시를 **제거**하는 메서드를 구분할 때 사용한다. 즉, 캐시에서 데이터를 제거하는 트리거로 동작하는 메서드다.
- 한 지역의 전체 캐시를 모두 지워야 할 때 옵션(*allEntries=true*)을 편리하게 사용할 수 있다.
비효율적으로 각 엔트리를 하나씩 지우는 것이 아니라 한 번에 모든 엔트리를 제거한다.
- `@CacheEvict` 를 void 메서드에 사용할 수 있다는 것은 중요한 부분이다. 메서드가 트리거로 동작하므로 반환값을 무시한다.

### @Caching
- 같은 계열의 어노테이션을 여러 개 지정해야 하는 경우가 있다. 
예를 들어, 조건이나 키 표현식이 캐시에 따라 다른 경우이다. 자바는 이러한 선언을 지원하지 않지만 우회할 수 있다.  
- `@Caching` 에서 중첩된 `@Cacheable`, `@CachePut`, `@CacheEvict` 를 같은 메서드에 다수 사용할 수 있다.

### 캐시 활성화
캐시 어노테이션을 선언하는 것만으로 자동으로 동작이 실행되지 않는다는 것이 중요하다. 스프링의 다른 기능처럼 **선언적으로 기능을 활성화**해야 한다. 
이는 캐시에 문제가 있다고 의심된다면 코드의 모든 어노테이션을 지우는 대신 활성화 설정만 지워서 캐시를 비활성화 시킬 수 있다는 의미이기도 하다.    

실제로는 이 선언으로 스프링이 캐시 어노테이션을 처리하도록 한다.
- XML기반 : `<cache:annotation-driven/>`
- Java Config 기반 : `@EnableCaching`

<br>
##### Reference
- *[Spring Cache](http://websystique.com/spring/spring-4-cacheable-cacheput-cacheevict-caching-cacheconfig-enablecaching-tutorial)*