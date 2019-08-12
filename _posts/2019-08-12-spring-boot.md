---
layout: post
title: "Spring Boot 소개"
subtitle: Spring Boot에 대해 알아보자
date: 2019-08-12 20:15:00 +0900
categories: Spring
tags: Spring SpringBoot
---

## Spring Boot
- **독립적인 애플리케이션을 쉽고 빠르게 만들 수 있다.**
- 수동 설정이 아닌 컨벤션으로 자동 설정된다.
- 비즈니스 외에도 내장 서버, 시큐리티, 헬스 체크, 외부 설정파일 등을 지원한다.

---

### Project Structure
- ComponentScan 하는 Root Application의 위치는 디폴트 패키지에 위치시킨다.
- 자바 파일 외에는 resources 패키지에 위치시킨다.

---

### Dependency
- **최상위 의존성(*spring-boot-dependencies*)에 버전이 명시**되어 있으므로 버전을 명시할 필요 없다.
- 의존성 간의 **버전 호환성 관리**를 전담해준다.
- 특정 버전을 사용하고 싶다면 버전을 명시해서 사용하면 된다.
- 부트에서 지원하지 않는 의존성은 버전을 반드시 명시해서 사용해야 한다.
- 부트에서 관리하는 버전을 변경하고 싶다면 properties 태그에 프로퍼티와 버전을 명시한다.

---

### AutoConfiguration
- `@SpringBootApplication` = @SpringBootConfiguration + @ComponentScan + @EnableAutoConfiguration
- 빈은 두 단계로 나눠서 읽혀진다.
  - Step1) @ComponentScan
    - @Component
    - @Configuration 과 @Repository, @Service, @Controller
  - Step2) @EnableAutoConfiguration *(override)*
    - 자동으로 가져올 빈들이 기본적으로 정의되어 있다.
      - *spring-boot-autoconfigure-2.1.6.RELEASE.jar!/META-INF/spring.factories*
    - @Configuration
    - @ConditionalOn* : 조건 만족시에만 빈 등록
      - @ConditionalOnMissingBean : 등록되지 않은 빈만 등록한다.
      - 빈을 재정의하지 않고 값을 수정하고 싶다면 @CofigutaionProperites를 활용할 수 있다.

---

### Embedded Server
- **Spring Boot는 서버가 아니다.**
- 내부에서 서버를 만드는 일련의 과정을 상세하고 유연하게 설정하여 실행해주는게 Spring Boot의 자동 설정이다.
  - ServletWebServerFactoryAutoConfiguration
    - TomcatServletWebServerFactoryCustomizer
  - DispatcherServletAutoConfiguration
- Tomcat, Jetty, Undertow 등을 내장하고 있다.

---

### Actuator
- Spring Boot Actuactor는 Spring Boot 애플리케이션을 운영하고 관리하는데 필요한 기능들을 사용하기 쉬운 형태로 제공한다.
- 주요 기능
    - `dump` : 스레드 덤프를 실행하고 결과를 표시한다.
    - `mappings` : HTTP 요청을 받아주는 모든 링크의 목록을 표시한다.
    - `info` : 애플리케이션과 관련된 정보를 표시한다.
        - ```properties
          #application.properties
          info.app.name=mirotic
          info.app.version=1.0.1
          info.app.description=sample project
          ```
    - `health` : 애플리케이션의 정상 동작 상태 정보를 표시한다.
        - HealthIndicator 인터페이스를 구현하여 health 메소드를 재정의
    - `autoconfig` : 자동 환경설정에 대한 정보를 표시한다.
    - `metrics` : 애플리케이션에서 수집한 각종 지표를 표시한다.
  
<br>