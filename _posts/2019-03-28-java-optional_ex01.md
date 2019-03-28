---
layout: post
title: "Optional 객체란"
subtitle: Optional을 사용하여 null-safe한 코드 작성하기
date: 2019-03-28 20:45:00 +0900
categories: Java
tags: Java Optional
---

# Optional 객체란
---

`Optional<T>` 객체란 값이 존재할지 존재하지 않을지 모르는 객체이다.  
즉, `null`을 담고 있을 가능성이 있는 객체를 래핑했다고 볼 수 있다.

Optional 객체를 사용하면 `NullPointerException`을 예방할 수 있다.  
또한, null 검사 등 불필요한 방어 코드를 제거할 수 있어 **가독성**이 좋아진다.  
네이밍 규칙은 Optional 객체임을 알 수 있는 이름이 좋다.    

---
## Optional 객체 생성
Optional 클래스는 간편하게 객체를 생성할 수 있도록 **정적 팩토리 메서드**를 제공한다.  

`Optional.empty()`
- null을 담고 있는 Optional 객체를 생성한다.
  - Optional<Member> maybeMember = Optional.empty();

`Optional.of(T value)`
- null이 아닌 객체를 담고 있는 Optional 객체를 생성한다.
  - Optional<Member> maybeMember = Optional.of(member);

`Optional.ofNullable(T value)`
- null인지 아닌지 알 수 없는 객체를 담고 있는 Optional 객체를 생성한다.
  - Optional<Member> maybeMember = Optional.ofNullable(member);
- Optional.of와 달리 null인 객체도 담을 수 있다.

  - 
```java
public static <T> Optional<T> ofNullable(T value) {
    return value == null ? empty() : of(value);
}
```

---
## Optional 객체 접근
Optional 클래스는 담고 있는 객체를 가져오는 다양한 메서드를 제공한다.  
get과 orElse로 시작하는 메서드들은 객체가 담겨 있으면 그 값을 반환한다.  
단, null인 경우에는 다르게 동작한다.

`get()`
- 객체가 null인 경우, NoSuchElementException이 발생한다.

`orElse(T other)`
- 객체가 null인 경우, 인자로 받은 값을 반환한다.

```java
// before
String str = getSomething();
int length = str == null ? 0 : str.length();

// after
int length = Optional.ofNullable(getSomething())
                     .map(String::length)
                     .orElse(0);
```
 
`orElseGet(Supplier<? extends T> other)`
- 객체가 null인 경우, 함수형 인자로 받은 값를 통해 생성된 객체를 반환한다.

`orElseThrow(Supplier<? extends X> exceptionSupplier)`
- 객체가 null인 경우, 함수형 인자로 생성된 예외가 발생한다.

```java
// before
Member member = getMember();
if (member == null) {
    throw new MemberNotFoundException();
}

// after
Member member = Optional.ofNullable(getMember())
                        .orElseThrow(MemberNotFoundException::new);
```

`isPresent()`
- 객체의 존재 여부를 true/false로 반환한다.

`ifPresent(Consumer<? super T> consumer)`
- 객체가 존재할 때만 함수형 인자로 받은 메서드를 수행한다.

```java
// before
Member member = getMember();
if (member != null) {
    member.init();
}

// after
Member member = Optional.ofNullable(getMember())
                        .ifPresent(Member::init);
```

<br>