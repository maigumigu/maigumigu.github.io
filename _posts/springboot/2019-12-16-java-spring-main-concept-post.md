---
title: "Spring 핵심 개념"
date: 2019-12-15 09:56:28 -0400
categories: Springboot
---

- 백기선님의 **예제로 배우는 스프링 입문 (개정판)** 강좌 수강하면서 기록함

  - 강의 요약이 문제가 되는 경우 포스트 삭제함

- 해당 강의는 Pet Clinic 예제와 함께 진행하였음

  https://github.com/spring-projects/spring-petclinic

- 강의 링크
  https://www.inflearn.com/course/spring_revised_edition#

# IoC (Inversion of Control)

> 일반적으로 

클래스 내에서 사용할 의존성은 스스로 생성한다. 그 클래스가 사용할 기능이니까 스스로 생성하는 것이 직관적으로 맞다.  

이해를 돕기위해 수도 코드 작성.

```java
public class Owner {
    private VisitRepository visitRepository;
    
    OWner()
    {
        visitRepository = new VisitRepository();
    }
}
```



> IoC는

어떤 클래스 내에서 사용할 의존성을 외부에서 생성하고 관리하는 것이다.  

아래의 코드를 보면 OwnerController 클래스가 사용할 VisitRepository, OwnerRepository를 스스로 생성하고 있지 않고, 생성자에서 외부 객체를 받아와서 대입하는 방식이다.  

외부에서 의존성 관리하고 해당 클래스로 의존성을 주입하고 있는 상황이다.이를 *[의존성 주입]: dependency injection이라고 부른다.

```java
@Controller
class OwnerController {

    private static final String 
        VIEWS_OWNER_CREATE_OR_UPDATE_FORM = "owners/createOrUpdateOwnerForm";
    private final OwnerRepository owners;
    private VisitRepository visits;
    @Autowired
    private PetRepository petRepository;

    public OwnerController(OwnerRepository clinicService, VisitRepository visits) {
        this.owners = clinicService;
        this.visits = visits;
    }
    (후략)
```



# AOP (Aspect oriented Programming)

# PSA