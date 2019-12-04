---
title: "spring boot 호다닥 공부하기 (1)"
date: 2097-09-06 08:26:28 -0400
categories: Springboot
---
# **Spring Boot 호다닥 공부하기 (1)**

내가 현재까지 spring에 관해 알고 있었던 지식은 다음과 같다.

- 웹 백 엔드 개발 프레임워크
- Java 기반
- 다양한 기능과 프레임워크의 복잡성

회사 프로젝트에서는 Spring Boot를 백엔드 프레임워크로 사용하고 있으므로, 

약 3~4일간 spring boot에 대한 기초적인 내용을 공부하고 간단한 게시판을 구현해보고자 한다.

*회사 프로젝트에서 사용하는 tool : java openjdk 1.8 ver, Spring boot, Postgresql, Gradle*



## Spring vs Spring boot?

https://dzone.com/articles/understanding-the-basics-of-spring-vs-spring-boot

의 링크를 참고하면, spring framework은 많은 기능들이 더해지면서 복잡해졌다고 한다. 

새로운 프로젝트를 시작하기 위해서는 긴 일련의 과정들을 필요로 했고, 

이 시간을 절약하고 바닥부터 세팅하는 것을 방지하기 위해 Spring boot가 등장했다.

그렇다면 어떤 부분들을 spring boot에서 처리해주는 것인지 궁금해진다. 

위 링크에서는 annotiation configuration(auto configuration)으로 개발 시간을 훅 단축시켜준다고 한다. 

즉, 적은 노력으로 프로젝트를 완성시킬 수 있도록 도와준다고 한다. 개발자들 입장에서는 굉장히 구미 돋는 포인트이다.

> The added advantages that come with Spring Boot 
>
> are of great value to the developers as they offer completion of projects with minimal efforts.



## Generate First Spring boot project

https://www.javadevjournal.com/spring-boot/spring-boot-application-intellij/

spring-boot-starter-web도 dependency추가해줘야함

```java

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

```java

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/")
    public String hello(){

        return "Hello World!";
    }

}

```

