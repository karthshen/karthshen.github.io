---
layout:     post
title:      "The Inroduction to Java Spring"
subtitle:   " \"Maintainability and Simplicity\""
date:       2019-05-16 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Java
    - Spring
    - Backend
---

# Java Spring Framework #

## Spring Annotations ##

`public static void main( ... )`

Starts the Java and then the application

`@SpringBootApplication`

An annotation that wraps commonly used annotations with Spring boot

`@Configuration`

Spring configuration on startup

`@EnableAutoConfiguration`

Auto configures the framework, looks for a file with SpringConfiguration annotation and configure the framework according to it

`@ComponentScan`

Recursively looks for any spring component. Such as controllers, services, and repositories.

Note: The components has to be in the sub-folder of the app class. So if the app class is in an nested folder, the spring won't be able to find anything outside of its hierarchy.

`SpringApplication.run( ... )`

Starts Spring, creates spring context, applies annotations and set up container.

## Wiring up the UI Client with Sprin Boot ##

### RESTful API ###

```java
@RequestMapping(value = "shipwrecks", method = RequestMethod.POST)
public Shipwreck create(@RequestBody Shipwreck shipwreck){
    return ShipwreckStub.create(shipwreck);
}
```

`RequestMapping` indicates the function is a RESTful funciton. `RequestMethod` indicates the type of the request is sent/received. 

```java
@RequestMapping(value = "shipwrecks/{id}", method = RequestMethod.GET)
public Shipwreck get(@PathVariable Long id){
    return ShipwreckStub.get(id);
}
```

The function can take in parameter from the request url. The convention is written as above. 

## Application Configuration ##

The configuration of the application can be easily modified by adding `application.properties` file under the resources folder.

The common configurations is located at 

https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html

### Environment Specific ###

Client can create `application-test.properties` or `application-prod.properties` in order to have different profiles set up for spring. The configuration needs to be modified accordingly. 