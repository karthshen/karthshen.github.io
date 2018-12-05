---
layout:     post
title:      "Intercept AMQP Inbound Message"
subtitle:   " \"Some thoughts on AMQP\""
date:       2018-11-01 15:43:00
author:     "Karth"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - Axon
    - Java
    - Backend
---

## Introduction ##
Following from the previous blog. After the metadata of the event has been intercepted on the  outbound AMQP event message, we want to intercept the inbound event message to store this metadata back to MDC to keep the the data flowing in the system. 

## Example User-Story
Assuming in this user story, the requirement is to add the information from `event.metadata()` to MDC context for later use. 

## Multiple Designs
- So for this user story, there are two ways to implement this feature.

1. The first way is to extend the cluster class and Override the `doPublish()` method or something to add your own logic for it before the event is processed. The con for this implementation is that you will need to extend a new child class for each type of cluster used in your code. 
2. This is also the type of scenario where the nature of AOP shines. You can have an `Aspect` class which intercept on the `doPublish()` method of the clusters and have your code added there. 


## Implementations for the OOP Design
Let's take `SimpleCluster` as an example.

1. Extend the simple cluster as follows:
   ```java
    public class SampleSimpleCluster extends SimpleCluster {
        public SampleSimpleCluster(String name, OrderResolver orderResolver) {
            super(name, orderResolver);
        }

        @Override
        public void doPublish(List<EventMessage> events, Set<EventListener> eventListeners, MultiplexingEventProcessingMonitor monitor) {
            String tenantId = (String) events.get(0).getMetaData().get("tenantId");
            String correlationId = (String) events.get(0).getMetaData().get("correlationId");

            MDC.put(TracingContextConstants.CORRELATION_KEY, correlationId);

            super.doPublish(events, eventListeners, monitor);

            MDC.clear();
        }
    }
    ```
2. In the `Configuration` class, change the cluster name from `SimpleCluster` to the cluster you just created. 

3. The feature is then completed, you can have unit tests to test the feature with the previously mentioned AMQP outbound message intercepting feature.

## Implementations for the AOP Designs
For the AOP Design, you can simply have an `Aspect` class that does the following:

```java
class EventTracingAspect { 
  @Around("//here you would add a pointcut to target the simpleCluster.doPublish()")
  Object injectTracingMetadataforSimpleClusterHandlerThread(ProceedingJoinPoint pjp){
    //here you would inject the MDC
    events = pjp.getArgs()[0];
    String tenantId = (String) events.get(0).getMetaData().get("tenantId");
            String correlationId = (String) events.get(0).getMetaData().get("correlationId");

    MDC.put(TracingContextConstants.CORRELATION_KEY, correlationId);
    Object result = pjp.proceed();

    //here you would clear the MDC
    return result;
  }
}
```

- The con for this implementation is that, since you are directly intercepting a third-party library with AOP, if there happens to be a change on the name/path/method definition on this third-party library, you won't be noticed for the change and your code may break without you knowing. 

