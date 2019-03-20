---
layout: post
title: "Ch07. 오류 처리"
subtitle: 예외를 사용하여 오류를 깔끔하게 처리하자
date: 2019-03-19 12:15:13 +0900
categories: CleanCode
tags: CleanCode
---

# 오류 처리
---

- 오류 처리는 프로그램에 반드시 필요한 요소 중 하나일 뿐이다.
- 뭔가 잘못될 가능성은 늘 존재한다. 잘못을 바로 잡을 책임은 프로그래머에게 있다.
- 오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드라 부르기 어렵다.

#### 오류 코드보다 예외를 사용하라
- 오류를 막기 위한 조건문을 무분별하게 사용하면 호출자 코드가 복잡해진다.  
오류가 발생하면 `예외`를 던지는 편이 낫다.
- *비즈니스 로직과 오류를 처리하는 로직이 분리*되기 때문에 코드가 깨끗해지고 각 개념을 **독립적**으로 살펴 보고 이해할 수 있다.

```java
// Before
public class DeviceController {
    ...
    public void sendShutDown() {
        DeviceHandle handle = getHandle(DEV1);
        if (handle != DeviceHandle.INVALID) {
            retrieveDeviceRecord(handle);
            if (record.getStatus() != DEVICE_SUSPENDED) {
                pauseDevice(handle);
                clearDeviceWorkQueue(handle);
                closeDevice(handle);
            } else {
                logger.log("Device suspended. Unable to shut down");
            }
        } else {
            logger.log("Invalid handle for: " + DEV1.toString());
        }
    }
    ...
}

// After
public class DeviceController {
    ...
    public void sendShutDown() {
        try {
            tryToShutDown();
        } catch (DeviceShutDownError e) {
            logger.log(e);
        }
    }
    
    private void tryToShutDown() throws DeviceShutDownError {
        DeviceHandle handle = getHandle(DEV1);
        DeviceRecord record = retrieveDeviceRecord(handle);
        
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
    }

    private DeviceHandle getHandle(DeviceID id) {
        ...
        throw new DeviceShutDownError("Invalid handle for: " + id.toString());
        ...
    }

    ...
}

```

#### Try-Catch-Finally 문부터 작성하라
- try 블록은 `트랜잭션`과 비슷하다. try 블록에서 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다. 그러므로 `try-catch-finally` 문으로 시작하는 편이 낫다.
- 먼저 강제로 예외를 일으키는 `테스트 케이스`를 작성한 후 테스트를 통과하게 코드를 작성하는 방법을 권장한다. 그러면 자연스럽게 try 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워진다.

#### 미확인 예외를 사용하라
- 확인된 예외는 OCP를 위반한다. 하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야 한다는 말이다.  
*최하위 단계에서 최상위 단계까지 연쇄적인 수정*이 일어난다! 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 `캡슐화`가 깨진다.
- 때로는 확인된 예외도 유용하다. 아주 중요한 라이브러리를 작성한다면 모든 예외를 잡아야 한다. 하지만 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

#### 예외에 의미를 제공하라
- 오류 메세지에 정보를 담아 **예외와 함께** 던진다. 오류가 발생한 원인, 위치, 연산 이름, 실패 유형 등을 남긴다.

#### 호출자를 고려해 예외 클래스를 정의하라
- 오류를 분류하는 방법은 수없이 많다. 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되어야 한다.
- 외부 API를 사용할 때는 `감싸기` 기법이 최선이다. **의존성**이 크게 줄어들기 때문이다.
1. 다른 라이브러리로 변경하더라도 비용이 적게 든다.
2. 테스트 케이스 작성이 용이하다.
3. 외부 API 설계에 종속되지 않는다.

```java
// Bad
    ACMEPort port = new ACMEPort(12);
    try {
        port.open();
    } catch (DeviceResponseException e) {
        reportPortError(e);
        logger.log("Device response exception", e);
    } catch (ATM1212UnlockedException e) {
        reportPortError(e);
        logger.log("Unlock exception", e);
    } catch (GMXError e) {
        reportPortError(e);
        logger.log("Device response exception");
    } finally {
        ...
    }
    
// Good
// ACMEPort 클래스를 감싸는 LocalPort 클래스 정의 
    LocalPort port = new LocalPort(12);
    try {
        port.open();
    } catch (PortDeviceFailure e) {
        reportError(e);
        logger.log(e.getMessage(), e);
    } finally {
        ...
    }
    
    public class LocalPort {
    
        private ACMEPort innerPort;
    
        public LocalPort(int portNumber) {
            innerPort = new ACMEPort(portNumber);
        }
    
        public void open() {
            try {
                innerPort.open();
            } catch (DeviceResponseException e) {
                throw new PortDeviceFailure(e);
            } catch (ATM1212UnlockedException e) {
                throw new PortDeviceFailure(e);
            } catch (GMXError e) {
                throw new PortDeviceFailure(e);
            }
        }
    }
```

#### 정상 흐름을 정의하라
- 외부 API를 감싸 독자적인 예외를 던지고, 코드 위에 처리기를 정의해 중단된 계산을 처리한다. 때로는 중단이 적합하지 않은 때도 있다.

```java
// Bad
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
    m_total += getMealPerDiem();
}

// Good
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();

public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() { }
}
```

`특수 사례 패턴` *Special Case Pattern*  
> 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식이다.  
그러면 **클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다.**  
클래스나 객체가 예외적인 상황을 캡슐화해서 처리하기 때문이다.  
ex) Collections.emptyList()

#### null을 반환하지 마라
- 우리가 흔히 저지르는 바람에 오류를 유발하는 행위는 **null을 반환**하는 것이다.  
null을 반환하는 코드는 일거리를 늘릴 뿐만 아니라 **호출자**에게 문제를 떠넘긴다.  
호출자가 null에 대한 처리를 해야되는 상황이 생긴다. 방어코드가 누락되는 경우에는 *NullPointerException*이 발생한다.
- null 체크를 놓친 것이 문제가 아니라 null 체크할 코드가 **너무 많다**는 것이 문제다.  
애초에 null이 반환되는 코드가 문제다. 그 대신 예외를 던지거나 특수 사례 객체를 반환한다. 

#### null을 전달하지 마라
- 메서드에서 null을 반환하는 방식도 나쁘지만 메서드로 **null을 전달**하는 방식은 더 나쁘다. 
null을 전달하려는 경우 Exception을 던지는 처리를 할 수 있으나 그에 대한 처리가 추가로 필요하게 된다.   
즉, 인수로 null이 넘어오면 코드에 문제가 있다는 말이다.

#### 결론
- 깨끗한 코드는 읽기도 좋아야 하지만 **안정성**도 높아야 한다.
- 오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지며 **유지보수성**도 크게 향상된다.

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
