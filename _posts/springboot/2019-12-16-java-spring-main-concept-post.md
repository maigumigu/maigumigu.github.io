---
title: "Spring 핵심 개념"
date: 2019-12-15 09:56:28 -0400
categories: Springboot
---

- 백기선님의 **예제로 배우는 스프링 입문 (개정판)** 강좌 수강하면서 이해한대로 사견 덧붙여 기록함

  - 강의 요약이 문제가 되는 경우 포스트 삭제함

- 해당 강의는 Pet Clinic 예제와 함께 진행하였음

  [https://github.com/spring-projects/spring-petclinic](https://github.com/spring-projects/spring-petclinic)

- 강의 링크  
  [https://www.inflearn.com/course/spring_revised_edition#](https://www.inflearn.com/course/spring_revised_edition#)
  
*****

# IoC (Inversion of Control)

직역하면 제어역전인데 백기선님은 **제어권의 역전**이라는 표현을 사용하셨다. 제어권이 뒤바뀐다는 것인데, 어떤 의미일까?

>일반적으로 

클래스 내에서 사용할 의존성은 스스로 생성한다. 그 클래스가 사용할 기능이니까 스스로 생성하는 것이 직관적으로 맞다.  

이해를 돕기위해 수도 코드 작성.

```java
public class Owner {
    private VisitRepository visitRepository;
    
    Owner()
    {
        visitRepository = new VisitRepository();
    }
}
```

> IoC는

어떤 클래스 내에서 사용할 의존성을 외부에서 생성하고 관리하는 것이다.  

아래의 코드를 보면 OwnerController 클래스가 사용할 VisitRepository, OwnerRepository를 스스로 생성하고 있지 않고, 생성자에서 외부 객체를 받아와서 대입하는 방식이다.  

외부에서 의존성 관리해주고 해당 클래스로 의존성을 주입하고 있는 상황이다.이를 *[의존성 주입]: dependency injection이라고 부른다.

```java
@Controller
class OwnerController {
    private final OwnerRepository owners;
    private final VisitRepository visits;



    public OwnerController(OwnerRepository clinicService, VisitRepository visits) {
        this.owners = clinicService;
        this.visits = visits;
    }
}
```

## IoC Container

spring에서는 IoC Container 라는 애를 두어서 의존성 주입과 관련된 객체들(그 이름도 유명한 Bean)을 관리하도록 한다. 

IoC Container의 역할은 1. Bean 등록 2. Bean 제공 2. Bean 사이의 의존성 주입이다.

### Bean 등록

Bean을 등록하는 방법에는 

1. @Bean annotation을 통한 명시적 빈 등록

   ```JAVA
   @Configuration
   public class BeanClass {
       @Bean
       public BeanClass 리턴되는_객체가_빈(){
           return 바로_얘;
       }
   }
   ```

   TMI지만 함수이름 한글로 해도 코드 잘 돌아간다. ㄴ^O^ㄱ

2. @Controller 등의 특정 annotation을 통한 빈 등록

   - Lifecycle callback: Spring의 Component 스캔기능을 통해 @Component 어노테이션이 달린 애들을 빈으로 등록해줌
   - @Controller는 @Component를 이미 가지고 있다.
     - intelliJ에서 ctrl 키 누르고 클릭하면 구현 코드를 Read Only로 확인가능
   - @Component를 가지고 있는 interface들
     - @Repository
     - @Service
     - @Controller
     - @Configuration

이 두 가지 방법이 있고, 

IoC Container에서 빈 등록과 관련된 annotation을 인식하여 자동으로 빈을 등록해준다.  

### Bean 제공

BeanFactory를 상속 받는 ApplicationContext를 통해 빈 객체를 가져올 수 있다. ```applicationContext.getBean("name")```이런식으로 직접적으로 가져올 수도 있다. 그러나 ApplicationContext를 통해 직접적으로 Bean 객체를 받는 방법은 거의 사용하지 않는다.

### Bean 사이의 의존성 주입

Bean 객체들끼리만 의존성 주입이 가능하기 때문에 ''**Bean 사이의**'' 를 강조했다. 의존성을 주입하는 세가지 방법은 Bean 에 대한 설명을 하면서 자세히 하겠다. 

## Bean

Bean 객체는 앞서 언급했듯이 의존성 주입과 관련된 객체들이다. 

즉, 의존성 주입이 가능한 객체의 분류를 Bean이라고 한다. 일반적인 자바 객체인 POJO(Plain Old Java Object)와는 다른 종류의 객체인 것이다. 

정리해서 말하면, Bean이란

> 1. 의존성 주입이 가능한 객체
>
> 2. Spring IoC Container가 관리하는 객체

이다.

Bean 의존성을 주입하는 방법 세가지는 다음과 같다.

1. 생성자로 주입
2. 필드로 주입
3. setter로 주입

### 생성자로 주입

앞선 코드처럼 사용하고자 하는 의존성을 생성자의 파라미터로 받아서 assign하는 방법이다.

```java
@Controller
class OwnerController {
    private final OwnerRepository owners;
    private VisitRepository visits;

    public OwnerController(OwnerRepository clinicService, VisitRepository visits) {
        this.owners = clinicService;
        this.visits = visits;
    }
}
```

생성자로 받아놓으면 OwnerController에서는 OwnerRepository,VisitRepository의 기능을 사용할 수 있게 된다.

### 필드로 주입

어찌보면 가장 편한 방법이다. 의존성을 사용하고자하는 필드의 선언부에 @Autowired 어노테이션을 달면 되기 때문이다. 직관적이다. 

```java
@Controller
class OwnerController {
    @Autowired
    private OwnerRepository owners;
    @Autowired
    private VisitRepository visits;
}
```

### setter로 주입

```java
@Controller
class OwnerController {
    private OwnerRepository owners;
    private VisitRepository visits;
    
    @Autowired
    public void setOwnerRepository(OwnerRepository ownerRepository) {
        this.owners = ownerRepository;
    }
}
```




### 생성자 주입 방식을 사용하자

(공식적으로 && 비공식적으로) 생성자 주입 방식을 추천하고, 필드 주입방식을 지양하기를 권한다. 이유는 다음과 같다.

1. final 키워드가 들어가지 않기 때문에 주입된 의존성이 변하지 않을 것이라는 보장이 없다고 한다.

2. IoC Container와 결합성이 높아진다. (객체지향적 설계에서 클래스간 결합도는 낮추고 클래스 자체의 응집력을 높이는 것이 좋다. low coupling, high cohesion)

3. 명시적인 생성자 주입 및 setter 주입과는 달리 @Autowired 어노테이션만으로 암시적으로 의존성 주입이 이루어진다. 의존성은 명시적일수록 좋으니까.

   ~~왜? 유지보수 때문에? autowired도 명시적인 것 아닌가? 이거는 궁금증이 남는다.~~

   ~~백기선님은 생성자 주입을 하면 반드시 있어야 하는 객체의 의존성 주입을 강제할 수 있기 때문에 좋다고 하셨는데 명시성과 관련이 있는 얘기인지는 모르겠다~~

출처: [English: 왜 필드 주입을 추천하지 않는가](https://blog.marcnuri.com/field-injection-is-not-recommended/)  

*****

# AOP (Aspect Oriented Programming)

https://www.tutorialspoint.com/spring/aop_with_spring.htm

와 여기 도입부 설명 너무 잘했다 

logging, auditing, declarative transactions, security, caching

*****

# PSA

Modularity