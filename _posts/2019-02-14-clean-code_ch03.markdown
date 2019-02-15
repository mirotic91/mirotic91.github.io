---
layout: post
title: "Ch03. 함수"
date: 2019-02-14 22:35:03 +0900
categories: book clean-code
---

# 함수
---
#### 작게 만들어라!
- 함수는 작을수록 더 좋다.

##### 블록과 들여쓰기
- 조건문의 조건에 함수를, 블록 안에도 함수를 만들어 이름을 적절히 짓는다면 코드를 이해하기 쉬워진다.

#### 한 가지만 해라!
- 함수는 한 가지를 해야 한다. 그 한 가지만 잘 해야 한다.
> `한 가지` 를 하는지 판단하는 방법
1. 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행해야 한다.  
2. 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.

##### 함수 내 섹션
- 함수 내에서 섹션으로 나눠질 수 있다는 것은 한 함수에서 여러 작업을 한다는 증거다.

#### 함수 당 추상화 수준은 하나로!
- 한 함수 내에 **추상화 수준**을 섞으면 코드를 읽는 사람이 헷갈린다.  
특정 표현이 근본 개념인지 아니면 세부사항인지 구분하기 어려운 탓이다.

##### 내려가기 규칙
- 코드는 **위에서 아래로** 이야기처럼 읽혀야 좋다.
- 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.

#### Switch 문
- 본질적으로 `switch` 문은 **n가지**를 처리한다. (다중 if/else 문 포함) 

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
    switch (e.type) {
        case COMMISSIONED: return calculateCommissionedPay(e);
        case HOURLY: return calculateHourlyPay(e);
        case SALARIED: return calculateSalariedPay(e);
        default: throw new InvalidEmployeeType(e.type);
    }
}
```

- `switch` 문은 작게 만들기 어렵지만, 다형성을 이용하여 `switch` 문을 `추상 팩토리`에 숨겨 다형적 객체를 생성하는 코드 안에서만 사용하도록 한다.

```java
public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
}

public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}

public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED: return new CommissionedEmployee(r);
            case HOURLY: return new HourlyEmployee(r);
            case SALARIED: return new SalariedEmploye(r); 
            default: throw new InvalidEmployeeType(r.type);
        }
    }
}
```

- 하지만 `switch` 문은 불가피하게 써야될 상황이 많으므로, 상황에 따라서는 사용 할 수도 있다.

#### 서술적인 이름을 사용하라!
- 좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않다.
- 함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
  - ex) SetupTeardownIncluder, includeSetupAndTeardownPages
- **길고 서술적인 이름이 짧고 어려운 이름이나 서술적인 주석보다 좋다.**
- 서술적인 이름을 사용하면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.
- 이름을 붙일 때는 **일관성**이 있어야 한다.

#### 함수 인수
> 최선은 입력 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우다. 그 이상은 피하는 편이 좋다. 왜냐하면 코드를 읽는 사람이 입력 인수를 이해해야 한다.  
또한, 테스트 케이스 작성시에도 인수가 많을수록 테스트 조합이 부담스러워진다.  
출력 인수는 입력 인수보다 이해하기 어렵다.

##### 많이 쓰는 단항 형식
- 함수에 질의하여 결과를 반환하는 경우
  - ex) boolean fileExists("MyFile")
- 이벤트 함수는 이벤트라는 사실이 명확히 드러나야 한다. 이름과 문맥을 주의해서 선택해야 한다.
  - ex) passwordAttemptFailedNtimes(int attempts)

##### 플래그 인수
- 함수로 부울 값을 넘기는 것은 플래그에 따라 다른 일을 처리한다는 뜻이다.

##### 이항 함수
- 단항 함수보다 이해하기 어렵다.
- 그럼에도 이항 함수가 적절한 경우
  - ex) assertEquals(expected, actual)

##### 삼항 함수
- 인수가 많을수록 주춤하거나 실수가 많아진다.

##### 인수 객체
- 인수들을 객체로 묶어 줄여줄 수 있다.
- **묶는 기준과 이름이 있기에 개념을 표현하게 된다.**

##### 인수 목록
- 가변 인수를 취하는 함수도 삼항 함수 이상은 과하다.

##### 동사와 키워드
- 함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수다.
  - ex) assertEquals(x, y) -> assertExpectedEqualsActual(x, y)  
  이항 함수의 인수 순서를 명확하게 표현해야 한다.

#### 부수 효과를 일으키지 마라!
- 한 가자리를 하겠다고 약속하고선 다른 일까지 하곤 한다.  
그로 인해 시간적인 결합이나 순서 종속성을 초래할 수 있다.

```java
public class UserValidator {
    private Cryptographer cryptographer;
    
    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize(); // 함수명을 벗어난 부수효과
                return true;
            }
        }

        return false;
    }
}
```

##### 출력 인수
- 일반적으로 출력 인수는 피해야 한다. 함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다.

#### 명령과 조회를 분리하라!
- 함수는 뭔가를 수행하거나 답하거나 둘 중 하나만 해야 한다.  
**객체 상태를 변경하거나 아니면 객체 정보를 반환하거나 둘 중 하나만 해야 혼란스럽지 않다.**

#### 오류 코드보다 예외를 사용하라!
- 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.

```java
// 중첩된 오류 처리 코드
if (deletePage(page) == E_OK) {
    if (registry.deleteReference(page.name) == E_OK) {
        if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
            logger.log("page deleted");
        } else {
            logger.log("configKey not deleted");
        }
    } else {
        logger.log("deleteReference from registry failed");
    }
} else {
    logger.log("delete failed"); return E_ERROR;
}
```

```java
// try-catch 문을 이용하여 오류 처리 코드를 분리
try {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
} catch {
    logger.log(e.getMessage());
}
```

##### Try/Catch 블록 뽑아내기
- 정상 동작과 오류 처리 동작을 뒤섞는 추한 구조이므로 `try-catch` 문을 별도 함수로 뽑아내는 편이 좋다.  
비즈니스 로직과 오류 처리가 분리되어 코드를 이해하고 수정하기 용이해진다.

```java
// 위 코드를 메소드로 추출
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    } catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```

##### 오류 처리도 한 가지 작업이다.
- 오류 처리도 `한 가지` 작업에 속한다. 그러므로 **오류만 처리해야 마땅하다.**

#### 반복하지 마라!
- 중복은 문제다. 코드 길이가 늘어날 뿐 아니라 알고리즘이 **변경되면 중복되는 곳을 전부 수정**해야 한다. 수정하는 중에 누락되는 부분이 생길 수 있어 오류가 발생할 확률도 높다.

#### 구조적 프로그래밍
- 모든 함수와 함수 내 모든 블록에 입구와 출구가 하나만 존재해야 한다.  
루프 안에서 `break`나 `continue`를 사용해선 안되며 `goto`는 절대로 안된다.
- 하지만 함수를 작게 만든다면 여러 차례 사용해도 괜찮다.  
오히려 단일 입/출구 규칙보다 의도를 표현하기 쉬워진다.

#### 함수를 어떻게 짜죠?
- 처음에는 길고 복잡하다. 중복된 루프도 많다. 인수 목록도 길다. 이름은 즉흥적이고 코드는 중복된다.
- 단위 테스트 케이스를 작성 한다.  
그리고 리팩토링하며 위 코드가 동일하게 테스트 케이스를 통과해야 한다.  
처음부터 한번에 가능할 수 없다.

#### 결론
모든 시스템은 프로그래머가 설계한 `도메인 특화 언어`로 만들어진다. 함수는 그 언어에서 동사며, 클래스는 명사다.  
위에서 설명한 규칙을 따른다면 길이가 짧고, 이름이 좋고, 체계가 잡힌 함수를 만들 수 있다.

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
