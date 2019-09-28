---
layout: post
title: "디폴트 메서드"
subtitle: Java8에서 변화된 인터페이스
date: 2019-09-24 22:25:00 +0900
categories: Java
tags: Java interface
---

Java8 이전에는 인터페이스를 구현하는 클래스는 **인터페이스에서 정의하는 모든 메서드 구현을 제공하거나 슈퍼클래스의 구현을 상속**받아야만 했다.  

> 인터페이스에 메서드가 추가되면 여러 문제가 발생했다.
인터페이스에 새로 추가된 메서드를 구현하도록 인터페이스를 구현하는 기존 클래스를 고쳐야 했기 때문이다. 
직접 인터페이스와 이를 구현하는 클래스를 관리할 수 있는 상황이라면 이 문제를 어렵지 않게 해결할 수 있지만 **인터페이스를 대중에 공개했을 때**는 상황이 다르다. 
그래서 디폴트 메서드가 탄생한 것이다.

---

Java8 에서는 이 문제를 해결하는 새로운 기능을 제공한다.

1. 인터페이스 내부에 `정적 메서드` 를 사용하는 것
    - 인터페이스에서 메서드 구현이 가능하다.
    - 인터페이스명으로 메서드를 호출해야 한다.
    - 상속받아 재정의 할 수 없다.
2. 인터페이스의 기본 구현을 제공할 수 있도록 `디폴트 메서드` 라는 기능을 사용하는 것    
   기존 인터페이스를 구현하는 클래스는 자동으로 인터페이스의 추가된 새로운 메서드의 디폴트 메서드를 상속받게 된다. ex) List 인터페이스의 sort 메서드
    - 인터페이스에서 메서드 구현이 가능하다.
    - 참조 변수로 메서드를 호출해야 한다.
    - 상속받아 재정의 할 수 있다.

---

### 추상 클래스와 Java8의 인터페이스
- 추상 클래스와 인터페이스는 **메서드와 바디를 포함하는 메서드**를 정의할 수 있다.
- 클래스는 하나의 추상 클래스만 상속받을 수 있지만 **인터페이스를 여러 개 구현할 수 있다.**
- 추상 클래스는 인스턴스 변수로 공통 상태를 가질 수 있지만 **인터페이스는 인스턴스 변수를 가질 수 없다.**

---

### 변화하는 API
인터페이스에 메서드를 추가했을 때는 바이너리 호환성을 유지하지만 인터페이스를 구현하는 클래스를 재컴파일하면 에러가 발생한다.
- `바이너리 호환성` : 뭔가를 바꾼 이후에도 에러 없이 기존 바이너리가 실행될 수 있음을 의미한다.
  - ex) 인터페이스에 메서드 추가 후 호출하지 않는 한 에러가 발생하지 않는다.
- `소스 호환성` : 코드를 고쳐도 기존 프로그램을 성공적으로 재컴파일할 수 있음을 의미한다.
- `동작 호환성` : 코드를 바꾼 다음에도 같은 입력값이 주어지면 프로그램이 같은 동작을 실행함을 의미한다.

---

### 디폴트 메서드 활용 패턴
- `선택형 메서드` : 해당 메서드가 필요한 구현체에서만 구현할 수 있다. 빈 메서드를 구현할 필요가 없어졌고, 불필요한 코드를 줄일 수 있다.
- `동작 다중 상속` : 여러 인터페이스에서 동작을 상속받을 수 있다. 중복되지 않는 최소한의 인터페이스를 유지한다면 코드에서 동작을 쉽게 재사용하고 조합할 수 있다. 
  - ex) ArrayList - RandomAccess, Cloneable, Serializable..

---

### 해석규칙
만약 **같은 디폴트 메서드 시그너처**를 포함하는 두 인터페이스를 구현한다면?!

> **C++ 다이아몬드 문제**  
C++는 클래스의 **다중 상속**을 지원한다. 클래스 D가 클래스 B와 C를 상속받고 B와 C는 클래스 A를 상속받는다고 가정하자.
그러면 클래스 D는 B 객체와 C 객체의 복사본에 접근할 수 있다. 
결과적으로 A의 메서드를 사용할 때 B의 메서드인지 C의 메서드인지 명시적으로 해결해야 한다. 
또한 클래스는 상태를 가질 수 있으므로 B의 멤버 변수를 고쳐도 C 객체의 복사본에 반영되지 않는다.

같은 디폴트 메서드 시그너처를 갖는 여러 메서드를 상속받는 **충돌 문제를 해결할 수 있는 세 가지 규칙**을 제시한다.
- 첫째, **클래스가 항상 이긴다.** 클래스나 슈퍼클래스에서 정의한 메서드가 디폴트 메서드보다 우선권을 갖는다.

```java
public interface A {

    default void print() {
        System.out.println("print A");
    }
}

public interface B extends A {

    default void print() {
        System.out.println("print B");
    }
}
public class C implements A {
    
    @Override
    public void print() {
        System.out.println("print C");
    }
}

public class D extends C implements B { }

// print C
```

- 둘째, 위 규칙 이외의 경우 **서브인터페이스가 이긴다.** 상속관계를 갖는 인터페이스에서 같은 시그너처를 갖는 메서드를 정의할 때는 서브인터페이스가 이긴다.

```java
public interface A {

    default void print() {
        System.out.println("print A");
    }
}

public interface B extends A {

    default void print() {
        System.out.println("print B");
    }
}

public interface C extends A { }

public class D implements B, C { }

// print B
```

- 셋째, 여전히 디폴트 메서드의 **우선순위가 결정되지 않았다면** 여러 인터페이스를 상속받는 클래스가 **명시적으로 디폴트 메서드를 오버라이드하고 호출**해야 한다.

```java
public interface A {

    default void print() {
        System.out.println("print A");
    }
}

public interface B extends A {

    default void print() {
        System.out.println("print B");
    }
}

public interface C extends A {

    default void print() {
        System.out.println("print C");
    }
}

public class D implements B, C { 
    
    @Override
    void print() {
        B.super.print();
    }
}

// print B
``` 

<br>
##### Reference
- *Java8 in Action*