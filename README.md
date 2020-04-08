# Introduction
A showcase for a microservice architecture and tools while implementing the board game Codenames: Duet 

# Initial high level architecture details


![alt text](images/arch.png "")


## Main Components

Brief explanation of the components. A more detail explanation is available at the README in the project module.

All modules will serve their API documentation using RestDocs and make it available at the ```http://COMPONENT_URL/docs/index.html``` url.

### Player Service

A very minimalist approach to register and get player's details

### Game Session Service

TBC



# Technology Stack

Area | Library | Link
--- | --- | ---
Programming Language | Java 11 | tbc
Cache Layer | tbc | tbc
DB layer | Spring Data <br> mybatis | https://mybatis.org/mybatis-3/
Documentation | REST docs | 
Dev Tools | lombok |
Ops | Spring Actuator | https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html
Distributed Tracing | Sleuth <br> Zipkin |
Cloud Messaging | Solace PubSub+ <br> Spring Clound Messaging |
Databases | H2 <br> mySQL |
Database Schema Management | liquibase |
Testing | JUnit <br> AssertJ <br> Mockito <br> REST Assured |
Deployment | Docker <br> Kubernetes |
Build | Maven |


# TODO:
- [ ] Setup a CI/CD solution with TravisCI
- [ ] Host on AWS
- [ ] Build the Terraform scripts
- [ ] Distribute Tracing with Zipkin or Jaeger
- [ ] Build an API Gateway utilizing Spring Cloud
- [ ] Make use of fault tolerance library, such as Hystrix for the Game Stats Service
- [ ] Use Event Storm for the Game Replay Service
- [ ] Use Client-Side load balance using Ribbon



# Building and running the application
## Pre-requisites

TBC

## Building

TBC


## Setting environment variables

TBC


## Running the application

### Running zipkin

 1 - Make sure zipkin server is up and running
 
```docker run -d -p 9411:9411 openzipkin/zipkin```

Navigate your browser to http://localhost:9411/zipkin/, to access its home page



## Stopping the application

TBC

## Deploying the application on Kubernetes

TBC

# Tips, Tricks and things to remember



# Technology Stack - Brief explanation


## Spring Boot:

@SpringBootApplication is a convenience annotation that adds all of the following:
- @Configuration: Tags the class as a source of bean definitions for the application context.
- @EnableAutoConfiguration: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
- @EnableWebMvc: Flags the application as a web application and activates key behaviors, such as setting up a DispatcherServlet. Spring Boot adds it automatically when it sees spring-webmvc on the classpath.
- @ComponentScan: Tells Spring to look for other components, configurations, and services in the the com.example.testingrestdocs package, letting it find the HelloController class.


## Spring Cloud Sleuth

Spring Cloud Sleuth adds two types of IDs to your logging:

- Trace Id: A unique ID that remains the same throughout the request containing multiple microservices.
- Span Id: A unique ID per microservice.

Basically, a Trace ID will contain multiple Span ID which can easily be used by log aggregation tools.

```xml
        <dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-sleuth</artifactId>
		</dependency>
```

###  Sleuth Integration with Zipkins
Zipkins is a distributed tracing system usually used to troubleshoot latency problems in service architectures.

We have to tell Sleuth to send data to the Zipkin server by adding a dependency to the pom.xml:

```xml
<dependency>  
 <groupId>org.springframework.cloud</groupId>  
 <artifactId>spring-cloud-starter-zipkin</artifactId>  
</dependency>
```
and adding following properties in the application.yaml
```yaml
spring
   sleuth
      sampler
         probability: 100  
spring
   zipkin
      baseUrl: http://localhost:9411/
```
The spring.zipkin.baseUrl property tells Spring and Sleuth where to push data to. Also, by default, Spring Cloud Sleuth sets all spans to non-exportable. 
This means these traces (Trace Id and Span Id) appear in logs but are not exported to another remote store like Zipkin.

In order to export spans to the Zipkin server, we need to set a sampler rate using spring.sleuth.sampler.probability. A value of 100 means all the spans will be sent to the Zipkin server too.
