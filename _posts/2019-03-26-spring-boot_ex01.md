---
layout: post
title: "스프링부트 앱 실행전 코드 실행"
subtitle: 스프링부트 샘플데이터 삽입하기 
date: 2019-03-26 22:55:06 +0900
categories: SpringBoot
tags: SpringBoot
---

# 스프링부트 앱 실행전 코드 실행
---

스프링부트 어플리케이션 실행과 동시에 특정 코드를 미리 실행시킬 수 있다.  

`ApplicationRunner`나 `CommandLineRunner` 인터페이스를 구현한 Bean을 사용하면 된다.
동작 방식은 동일하게 스프링부트 어플리케이션이 뜨기 전에 인터페이스 구현체의 **run** 메서드를 수행한다.  
다만 차이는 ApplicationRunner는 파라미터로 `ApplicationArguments`를 받고, CommandLineRunner는 파라미터로 `String[]`를 받는다.
  - [ApplicationArguments Example](https://goni9071.tistory.com/entry/spring-boot-main-args-ApplicationArguments){:target="_blank"}
  
또한, `@Order`를 사용하여 Runner의 실행 순서를 지정할 수 있다.

> 샘플데이터 작성 예시       
Member가 Team에 소속되기 위해서 Team 데이터가 먼저 추가되어야 한다.

```java
@Component
@RequiredArgsConstructor
public class SampleDataApplicationRunner implements ApplicationRunner {

    private final TeamRepository teamRepository;

    private final MemberRepository memberRepository;

    @Override
    public void run(ApplicationArguments args) {
        System.out.println("SampleData save finish..");
    }

    @Order(1)
    @Component
    class TeamDataRunner implements CommandLineRunner {

        @Override
        public void run(String... args) {
            teamRepository.save(new Team("Arsenal"));
            teamRepository.save(new Team("Dortmund"));
        }
    }

    @Order(2)
    @Component
    class MemberDataRunner implements CommandLineRunner {

        @Override
        public void run(String... args) {
            memberRepository.save(new Member("Ozil", "Mesut", new Team("Arsenal")));
            memberRepository.save(new Member("Reus", "Marco", new Team("Dortmund")));
        }
    }

}
```