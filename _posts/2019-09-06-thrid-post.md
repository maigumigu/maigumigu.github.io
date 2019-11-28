---
title: "spring boot 호다닥 공부하기 (2)"
date: 2019-09-06 08:26:28 -0400
categories: springboot
---

# **Spring Boot 호다닥 공부하기 (2)

공부 3일차 오전까지 한 것:

- [스프링 부트 시작하기] 책에서 데모 프로젝트 구현에 필요한 부분 읽음
- spring boot와 postgreSQL 연동시 발생하는 encoding format issue 해결
- controller와 model에 대한 코드 상에서 이해
- sql mapper tool들에 대한 개념적 이해 
- spring boot dependency 세팅에 대한 이해



어노테이션이라는 말을 ~~계속 치기 귀찮아서~~ @로 대신한다.

## demo3 프로젝트 세팅 && 코드 구조 이해하기!

Maven 프로젝트 기준 Spring boot 디렉토리 구조:

![1567654550919](C:\Users\classact\AppData\Roaming\Typora\typora-user-images\1567654550919.png)

이 중 주의를 기울여 살펴보아야 할 파일들은 다음과 같다.



- 프로그램 로직이 담긴 packge com.example.demo3 하위의 파일들
- resource 폴더 아래에 담긴 세팅과 view와 관련된 파일들
- 의존성 및 빌드과 관련된 pom.xml



## 프로그램 로직이 담긴 소스코드



#### <u>Demo2Application.java</u>

```java
package com.example.demo3;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Demo3Application {    

    public static void main(String[] args) {       	   
        SpringApplication.run(Demo3Application.class, args);
    }
}
```

spring boot project를 생성하면 자동으로 생성되는 파일이다.  SpringApplication을 실행하기 시작한다는 의미가 있다. 

args를 추가로 넘겨줄 수 있는 것 같은데, 어떤 상황에 argument를 넘겨주는 지는 공부가 더 필요할 것 같다.



```@SpringBootApplication```

해당 @을 통해 3개의 기능이 활성화 된다.

@EnableAutoConfiguration :  spring boot auto configuration. 

@ComponentScan: 소스 코드 내부에 @Component가 달린 애들을 검사한다. 

@Configuration:추가적인 bean 이나 configuration class들을 적용할 수 있다.



 ```SpringApplication.run(Demo3Application.class, args);```

어플리케이션 실행에 필요한 전처리 작업을 해준다.

관련 인스턴스를 생성하고, 빈을 로드하고, 내장 톰캣 서버를 실행해준다. 

default 포트 번호는 8080이다.



#### <u>AppRunner.java</u>

```java
package com.example.demo3;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements ApplicationRunner {    

    @Autowired    
    AccountRepository accountRepository;


    @Override
    public void run(ApplicationArguments args) throws Exception {          
    
        Account account = new Account("Jung", "admin");      
        accountRepository.save(account);    
    }
}
```

@Repository @을 달고 디비에 접근하는 정석적인 코드는 아니고, 이 프로젝트에서 Application runner를 이용해 db에서 접근하는 역할을 한 class이다. 사수분은 log 찍어볼 때 Application runner를 사용했다고 하셨다.



```@Component```

AppRunner 클래스가 Spring Framework의 component라는 것을 denote해주기 위한 @이다.

@Service, @Repository, @Controller를 포함할 수 있는 generic한 @이라고 할 수 있다.

각각의 구체적인 @에 대해서는 stackoverflow에서 찾은 설명을 첨부해둔다.



출처: https://stackoverflow.com/questions/6827752/whats-the-difference-between-component-repository-service-annotations-in

```
│ Annotation │ Meaning                                             │
├────────────┼─────────────────────────────────────────────────────┤
│ @Component │ generic stereotype for any Spring-managed component │
│ @Repository│ stereotype for persistence layer(access DB)         │
│ @Service   │ stereotype for service layer(logic,calculation)     │
│ @Controller│ stereotype for presentation layer (spring-mvc)	   |
```



```@Autowired```

https://www.baeldung.com/spring-autowire

여기 설명 쩔음. 일단 지금은 name으로 auto wiring하는게 가장 편할듯.



ApplicationRunner

spring application이 시작되고 난 후에 일련의 명령을 실행할 필요가 있을 때 쓴다고 한다.

ApplicationArguments

ApplicationRunner에서 run 함수의 argument로 쓰인다. 코드 상에서는 logger를 통해 어플리케이션 시작시 함께 덧붙여진 argument를 가져오는 용도로 사용하는 것으로 보인다.



#### <u>AccountController.java</u>

```java
package com.example.demo3;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class AccountController {
@Autowired
AccountRepository accountRepository;

@GetMapping("/accounts")
List<Account> getAccount(){

    List<Account> all = accountRepository.findAll();
    return all;
}
}
```
```@RestController```

@Controller와 @RestController 차이점 젤 쉬운 설명:

> @RestController = @Controller + @ResponseBody

@Controller는 기본적으로 data를 view와 매핑해주는 역할을 한다(MVC).   

@ResponseBody를 이용하면 응답 스트림 내용을 직접적으로 지정해줄수 있다.



```java
@GetMapping("/accounts")
```

```@RequestMapping(method = RequestMethod.GET)```의 shortcut이라고 한다

argument로 경로를 넣어줄 수 있고, default는 자연히 "/'"가 될 것이다.



## resource 관련 파일들

위의 이미지에서 resource 폴더의 구조를 보면 resouce > **static** , **template**, **application.properties**가 있다.

먼저 **static** 폴더는 말그대로 html이나 css 같은 정적 리소스를 위한 폴더이다. ResourceHandlerRegistry와 addResourceHandler 등을 이용해 코드상에서 경로를 지정해주어  매핑을 한다. **template** 폴더는 일반적으로 타임리프가 벨로시티 같은 템플릿 엔진 파일을 위한 폴더라는데 ~~아직 사용하지 않았으므로~~ 넘어감. 회사에서는 리액트를 쓰고 있다고 한다.

매핑 예시 :

```java
@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter {    
    @Override    
    public void addResourceHandlers(ResourceHandlerRegistry registry) {        
     registry.addResourceHandler("/static/**").addResourceLocations("classpath:static/");    }
}
```



**application.properties** 파일에서는 디비 정보 등 다양한 세팅을 해줄 수 있다.

아래는 이 프로젝트의 properties들이다. 어떤 세팅을 해줄 수  있는지 감이 팍 온다.

```
#database setting
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
spring.datasource.name=postgres
spring.datasource.hikari.username=postgres
spring.datasource.password=migu

#common jpa setting
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.temp.use_jdbc_metadata_defaults= false

#custom port num
server.port=8081
```

 

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">    
    <modelVersion>4.0.0</modelVersion>    
    <parent>        
        <groupId>org.springframework.boot</groupId>        
        <artifactId>spring-boot-starter-parent</artifactId>        
        <version>2.1.7.RELEASE</version>        
        <relativePath/> <!-- lookup parent from repository -->    
    </parent>    
    <groupId>com.example</groupId>    
    <artifactId>demo3</artifactId>    
    <version>0.0.1-SNAPSHOT</version>    
    <name>demo3</name>    
    <description>Demo project for Spring Boot</description>    
    
    <properties>        
        <java.version>1.8</java.version>    
    </properties>    
    <dependencies>        
        <dependency>            
            <groupId>org.springframework.boot</groupId>            
            <artifactId>spring-boot-starter-data-jpa</artifactId>        
        </dependency>        
        <dependency>            
            <groupId>org.springframework.boot</groupId>            
            <artifactId>spring-boot-starter-web</artifactId>        
        </dependency>        
        <dependency>            
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>            
            <scope>runtime</scope>        
        </dependency>        
        <dependency>            
            <groupId>org.springframework.boot</groupId>            
            <artifactId>spring-boot-starter-test</artifactId>            
            <scope>test</scope>        
        </dependency>    
    </dependencies>    
    <build>        
        <plugins>            
            <plugin>                
                <groupId>org.springframework.boot</groupId>                
                <artifactId>spring-boot-maven-plugin</artifactId>            
            </plugin>        
        </plugins>    
    </build>
</project>
```

dependency 중 spring-boot-starter-webdhk spring-boot-start-test는 처음 프로젝트를 만들 때 추가해줘야 하는 의존성들이고, jpa와 postgresql은 db 관련 작업을 하기 위해 추가해준 것이다.
