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

2. IoC Container와 결합성이 높아진다. (객체지향적 설계에서 클래스간 결합도는 낮추고 클래스 자체의 응집력을 높이는 것이 좋다. *low coupling, high cohesion*)

3. 명시적인 생성자 주입 및 setter 주입과는 달리 @Autowired 어노테이션만으로 암시적으로 의존성 주입이 이루어진다. 의존성은 명시적일수록 좋으니까.

   ~~******왜? 유지보수 때문에? autowired도 명시적인 것 아닌가? 이거는 궁금증이 남는다.~~

   ~~백기선님은 생성자 주입을 하면 반드시 있어야 하는 객체의 의존성 주입을 강제할 수 있기 때문에 좋다고 하셨는데 명시성과 관련이 있는 얘기인지는 모르겠다~~

출처: [왜 필드 주입을 추천하지 않는가](https://blog.marcnuri.com/field-injection-is-not-recommended/)
******

# AOP (Aspect Oriented Programming)

### AOP의 니즈

코딩을 하다보면 여러곳에 흝어져 있지만 동일한 내용의 코드가 반복되는 것을 보게 된다.  

보통 그런 코드들은 핵심 로직과 별개로 부수적인 설정, 로직에 가까울 때가 많다.

대표적인 것이 JDBC에서의 transaction 처리이다.

```java
connection.setAutoCommit(false);  
//transaction scope
// 1. 이거 삭제해
// 2. 이거 추가해
// 3. 어쩌구 저쩌구
connection.commit();              
```

transaction을 해야할 때 마다 autocommit을 false로 해주고 commit를 해주는 코드가 들어간다.  

이런 반복되는 코드들을 하나의 모듈로 만들어서 처리하고,  

원래 코드에서는 핵심로직에 집중할 수 있도록 하는 것이 AOP이다.  

### AOP의 적용

transaction을 명시하는 경우 외에도 logging, caching 등의 작업에 이용될 수 있을 것 같다.  

### 관점이라는 용어랑 AOP가 무슨 관계지?

그런데 AOP의 의미 자체에 대해 생각하면 혼란스럽다. Aspect를 번역하면 '관점'이라는 말이 된다.  

그렇다면 AOP는 관점 지향 프로그래밍으로 해석 된다. 어떤 관점을 의미하는 거지?라는 의문에 빠질 수 있다.  

그러므로 관점이라는 말에 너무 얽매이지 말고, Aspect라는 term을 새롭게 바라보는 게 이해에 도움이 된다. 

### Aspect를 새롭게 정의하자면,

여러 클래스에 흝어져 있는 동일한 코드가 존재하는 경우, 이 코드들을 모듈화한 결과물, 즉 코드를 의미한다.  

### Proxy 패턴을 통한 AOP지원

AOP 지원 방식에는 AspectJ 등 다양한 방법이 있지만, Spring은 Proxy Pattern을 통해 지원하고 있다.

Proxy 패턴에서는 Proxy(대리하는 애)가 Client의 요청이 들어오면,  

수행해야할 태스크들을 위임하여 Aspect들이 기능하도록 한다. 

### @Transactional

spring에서는 @Transactional 어노테이션을 지원하고,

transaction 작업이 필요한 메소드 시그니처 부분에 적어두면 spring 내부적으로 transaction 처리를 해준다. 

다음의 예시 코드는 JPA 지식이 조금 필요하지만 일단 @Query 아규먼트 부분의 기능을 하는 함수라고 이해하면 된다.

```java
@Query("SELECT DISTINCT owner FROM Owner owner left join fetch owner.pets WHERE owner.firstName LIKE CONCAT('%',:Name,'%')")
@Transactional(readOnly = true)
Collection<Owner> findAllByFirstName(@Param("Name") String firstName);
```

### @Aspect

Aspect를 커스터마이징해서 사용할 수 있는 @Aspect 어노테이션도 제공한다. 

예를들어 @LogExecutionTime라는 어노테이션을 만들어 다음과 같이 사용하고자 한다. @LogExecutionTime이라는 이름에서 알 수 있듯이 소요 시간을 측정해 프린트해주는 기능을 가지고 있다. 

```java
    @GetMapping("/owners/find")
    @LogExecutionTime
    public String initFindForm(Map<String, Object> model) {
        model.put("owner", new Owner());
        return "owners/findOwners";
    }
```

1. 먼저 LogExecutionTime라는 이름의 어노테이션 인터페이스를 만들어준다. 

@Target 어노테이션을 통해 해당 어노테이션을 메소드에 대해 적용해줄 것과, runtime까지 유지하고 있을 것을 명시한다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {
}
```

2. LogAspect라는 이름의 클래스를 만들어 준다.  

   @Component와 @Aspect 어노테이션을 추가해서 해당 aspect를 IoC 컨테이너가 관리하도록 명시해준다.

    @Around annotation을 통해 @LogExecutionTime이 달린 메소드 *전후*에 logExecutionTime를 실행한다는 뜻이다.

```java
@Component
@Aspect
public class LogAspect {
    Logger logger = LoggerFactory.getLogger(LogAspect.class);

    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable
    {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();

        Object proceed = joinPoint.proceed();

        stopWatch.stop();
        logger.info(stopWatch.prettyPrint());

        return proceed;
    }
}
```



******

# PSA

분량을 늘려서 설명할 순있지만 Portable Service Abstraction이라는 용어에 담긴 의미말고는 뭐를 더 설명해야 할지 모르겠다.  

PSA를 통해 코드 변경 거의 없이 또 다른 기술 스택으로 변경 가능하다. 좀더 공부하고 중요한 포인트를 발견하면 설명을 추가해야 겠다.  



지금까지 spring triangle이라고 불리는 IoC, AOP,PSA에 대해 알아보았다.