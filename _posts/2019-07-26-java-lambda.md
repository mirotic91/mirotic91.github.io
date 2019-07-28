---
layout: post
title: "람다 표현식"
subtitle: 람다를 활용해 코드를 간결하게 표현하자
date: 2019-07-26 22:00:00 +0900
categories: Java
tags: Java lambda
---

## 람다란?
**람다 표현식**은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이라 할 수 있다.

- 이름이 없는 **익명 함수**로 파라미터 리스트, 바디, 반환 형식, 예외 리스트를 가질 수 있다.
- 메서드 인수로 전달하거나 변수로 저장할 수 있다.
- 익명 클래스와 달리 코드를 **간결**하게 구현할 수 있다.

> 람다 표현식

```java
(parameters) -> expression
(int a, int b) -> a * b;

(parameters) -> { statements; }
(Apple a) -> { 
  System.out.println(a.getColor());
}
```

------

## 함수형 인터페이스란?

**함수형 인터페이스**는 추상 메서드가 오직 하나인 인터페이스를 의미한다.  
**함수 디스크립터**는 람다 표현식의 시그니처를 서술하는 메서드다.

```java
// 함수형 인터페이스
@FunctionalInterface
public interface Predicate<T> {
  // 함수 디스크립터 : T -> boolean
  boolean test(T t);
}
```

- `@FunctionalInterface` 는 함수형 인터페이스임을 가리키는 어노테이션이다.
- 함수형 인터페이스라는 문맥에서 람다 표현식을 사용할 수 있다.
- 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 **전체 표현식을 함수형 인터페이스의 인스턴스**(함수형 인터페이스를 **concrete 구현**한 클래스의 인스턴스)로 취급할 수 있다.
- 제네릭 파라미터에는 참조형만 사용할 수 있다. 기본형에 대해서는 오토박싱을 피할 수 있도록 특화된 함수형 인터페이스를 제공한다.
    - IntPredicate, LongConsumer, DoubleFunction<R>, ToLongFunction<T>

> 자바8의 대표적인 함수형 인터페이스

| 함수형 인터페이스 | 함수 디스크립터 | 추상 메서드 |
|:-----:|:---:|:---:|
| `Predicate<T>` | T -> boolean | test |
| `Consumer<T>` | T -> void | accept |
| `Function<T, R>` | T -> R | apply |
| `Supplier<T>` | () -> T | get |
| `UnaryOperator<T>` | T -> T | apply |
| `BinaryOperator<T>` | (T, T) -> T | apply |
| `BiPredicate<L, R>` | (L, R) -> boolean | test |
| `BiConsumer<T, U>` | (T, U) -> void | accept |
| `BiFunction<T, U, R>` | (T, U) -> R void | apply |

---

### 메서드 레퍼런스란?
- 하나의 메서드를 참조하는 람다를 **간결**하게 표현 할 수 있다. 
- 명시적으로 메서드명을 참조하므로 **가독성**이 향상된다.
- 기존의 메서드 구현을 **재사용**하고 직접 전달할 수 있다.

```
Lambda Expression - (Apple a) -> a.getWeight()
Method Reference - Apple::getWeight
```

생성자 레퍼런스는 `ClassName::new` 형식으로 만든다.

```java
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();

Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(100);

BiFunction<String, Integer, Apple> c3 = Apple::new;
Apple a3 = c3.apply("green", 100);
```

함수형 인터페이스는 람다 표현식을 조합할 수 있는 다양한 **디폴트 메서드**를 제공한다.
- `Comparator` : thenComparing
- `Predicate` : negate, and, or
- `Function` : andThen, compose

<br>
##### Reference
- *Java8 in Action*