---
layout: post
title: "Spring Validator Customizing"
subtitle: ConstraintValidator 를 구현하여 유효성 검증하기
date: 2019-01-07 12:00:00 +0900
categories: Spring
tags: Spring, Validator, JSR-303
---

## ConstraintValidator 를 구현하여 유효성 검증하기
- 데이터를 저장하기 전에는 유효한 데이터인지 검증이 요구된다.
- 서비스에 정의한 값들이 반복적이여서 검증하는 코드를 숨기고 명시적으로 보여줄 수 있는 `JSR-303` 어노테이션을 커스텀하여 검증해보자.

## JSR-303 Annotation Customizing
- 우선 필드에서 검증할 어노테이션을 생성해준다. 이 때, `message`, `groups`, `payload` 메서드는 반드시 존재해야 한다.
- 그리고 values 라는 속성을 추가하여 주로 사용되는 값을 디폴트로 설정했다. (Size)
다른 값을 검증하려면 필드의 어노테이션에 values 속성을 설정한다.  
- `@Constraint` 어노테이션에는 직접 생성할 `ConstraintValidator` 를 구현하는 Validator를 설정한다.

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = AllowedValueValidator.class)
public @interface AllowedValue {
    
  String[] values() default { "S", "M", "L" };
  
  String message() default "{com.validator.constraints.AllowedValue.message}";
  
  Class<?>[] groups() default {};
  
  Class<? extends Payload>[] payload() default {};
}

```

---

- Validator를 생성하여 ConstraintValidator 인터페이스를 상속 받고, 인터페이스에 선언된 메서드를 구현한다.  
- `initialize` 메서드에서는 초기화할 데이터가 필요한 경우 설정한다. 앞서 만든 어노테이션의 기본값으로 초기화했다.
- `isValid` 메서드에서는 실제 검증할 내용을 작성한다. 검증할 필드에 요청된 값이 values에 정의된 값 중에 존재하는지 확인했다.

```java
public class AllowedValueValidator implements ConstraintValidator<AllowedValue, String> {
    
  private String[] values;
  
  @Override
  public void initialize(AllowedValue constraintAnnotation) {
    this.values = constraintAnnotation.values();
  }
  
  @Override
  public boolean isValid(String value, ConstraintValidatorContext context) {
    return ArrayUtils.contains(values, value);
  }
}

```

---

- 검증할 필드에 커스텀한 어노테이션을 표기한다.  
- 특정 필드에서는 기본 검증 값이 아닌 다른 값을 검증하도록 표기했다.(Color)

```java
public class Clothing {

  private String id;

  private String name;

  @AllowedValue
  private String topSize;

  @AllowedValue
  private String bottomSize;

  @AllowedValue(values = { "red", "blue" })
  private String color;
}

```

---

- 컨트롤러에서는 기존 처럼 모델에 `@Valid` 와 `BindingResult` 를 표기한다.  
- 해당 요청이 들어오면 모델 필드에 표기한 커스텀 어노테이션의 Validator에 작성한 isValid 메서드로 검증하게 된다.  
- 유효하지 않으면 bindingResult의 해당 필드에 대한 에러가 담긴다.

```java
@PostMapping("/clothing/update")
public void update(@Valid Clothing clothing, BindingResult bindingResult) {
  if (bindingResult.hasErrors()) {
    FieldError fieldError = bindingResult.getFieldError();
    throw new RuntimeException(fieldError.getDefaultMessage());
  }
  
  clothingManager.update(clothing);
}

```

---

- 반복적인 값들에 대한 검증으로 괜찮지만 모델 필드의 values에 표기할 값들이 많아지면 오히려 코드가 복잡해 보일 수도 있다. 
- 검증하는 방법은 다양하니 상황에 적절하게 잘 사용하는 것이 좋다.