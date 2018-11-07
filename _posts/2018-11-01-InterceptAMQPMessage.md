---
layout:     post
title:      "Intercept AMQP Outbound Message"
subtitle:   " \"Some thoughts\""
date:       2018-10-05 15:43:00
author:     "Karth"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - Axon
    - Java
    - Backend
---

## Introduction
Say if I need to intercept the `metadata` of the outbound AMQP event before it's sent to RabbitMQ. 

I will need to have some sort of Interceptor to intercept the AMQP message. And this interceptor has to be working asynchronously so when an AsynchronousBus is in use, it won't have threading problems.

## Example User-Story
- Assuming in this user story, the requirement is to add certain information from MDC context `Map<String, Object>` to the `event.metadata` of the AMQP outbound event. 

## Design
- The idea is to do the MDC transfer inside of a `CommandDispatchInterceptor`, which will first transfer the data from MDC to `command.metadata`. Then use a `AuditingInterceptor` with a customized `AuditDataProvider` to transfer the data from `command.metadata` to `event.metadata`. 
- By transferring data to `command.metadata` first, will prevent any multi-threading issues with `MDC` (which is thread-local) or `AsynchronousCommandBus` (which is also thread-local).

## Implementation
1. Create a custom `SampleCommandDispatchInterceptor` that implements the `CommandDispatchInterceptor` interface.
    ```java
    public class TracingCommandDispatchInterceptor implements CommandDispatchInterceptor {
    @Override
    public CommandMessage<?> handle(CommandMessage<?> commandMessage) {
            /*
             Implement Data Transfer from MDC to command.metadata
             andMetadata() method will add the parameter to the original metadata in commandMessage
            */
            return commandMessage.andMetadata(MDC.getCopyOfContextMap());
        }
    }
    ```

2. Register this custom `CommandDispatchInterceptor` as a Bean in Configuration class.
   ```java
    /**
    * @return The TracingCommandDispatchInterceptor that is going to be attached to CommandBus
    */
    @Bean
    public TracingCommandDispatchInterceptor tracingCommandDispatchInterceptor() {
        return new TracingCommandDispatchInterceptor();
    }
   ```

3. Attach the registered Bean to `CommandBus`
    ```java
    @ConditionalOnMissingBean
    @Bean
    @ConditionalOnProperty(name = "axon.test-mode", havingValue = "false", matchIfMissing = true)
    public CommandBus commandBus(List<CommandDispatchInterceptor> commandDispatchInterceptors) {
        SimpleCommandBus cmdBus = new SimpleCommandBus();

        cmdBus.setDispatchInterceptors(commandDispatchInterceptors);
        return cmdBus;
    }
    ```
4. At this point, the data in MDC is transferred over to `command.metadata`. Now the next few steps is to transfer the data from `command.metadata` to `event.metadata`.

5. Create a custom `AuditDataProvider` which will be used by an `AuditingInterceptor` later. This customized `AuditDataProvider` will return the `command.metadata`. And whatever it returns, will be added to the `metadata` of outbound event message.
    ```java
    public class SampleAuditDataProvider implements AuditDataProvider {
        @Override
        public Map<String, Object> provideAuditDataFor(CommandMessage<?> command) {
            return command.getMetaData();
        }
    }
    ```

6. Register an `AuditingInterceptor` as a Bean in Configuration class, and attach it to `CommandBus`.
    ```java
    @Bean
    public AuditingInterceptor auditingInterceptor() {
        AuditingInterceptor interceptor = new AuditingInterceptor();
        interceptor.setAuditDataProvider(new SampleAuditDataProvider());

        return interceptor;
    }
    ```
    Note our custom `AuditDataProvider` is passed in to this `AuditingInterceptor`.
    
    Now we will attach this Interceptor to our CommandBus. Since `AuditingInterceptor` does implement `CommandHandlerInterceptor`, we will just pass in a list of commandHandlerInterceptor to the `CommandBus`.
    ```java
    @ConditionalOnMissingBean
    @Bean
    @ConditionalOnProperty(name = "axon.test-mode", havingValue = "false", matchIfMissing = true)
    public CommandBus commandBus(List<CommandDispatchInterceptor> commandDispatchInterceptors) {
        SimpleCommandBus cmdBus = new SimpleCommandBus();

        cmdBus.setDispatchInterceptors(commandDispatchInterceptors);
        return cmdBus;
    }
    ```

7. Up until this point, the implementation of the MDC -> `Command.metadata` -> `Event.metadata` is finished. 

## Testing
- If we place something in MDC and trigger an AMQP outbound event, it should return this new event with the information we needed. 
- Make a new queue on RabbitMQ that monitors every single message coming from `eventBus`, and we get the follow message:
- **Before**
  ```json
    {
    "schemaVersion": "1.0.0",
    "id": "79c97f40-6a3f-4475-891b-7e5c251749ba",
    "timestamp": "2018-10-30T14:54:11.583Z",
    "tenantId": null,
    "type": "pay.deductions.DeductionCreated",
    "body": {
    ...
    }
    "metadata": {

    }
    ```
- **After**
    ```json
    {
    "schemaVersion": "1.0.0",
    "id": "79c97f40-6a3f-4475-891b-7e5c251749ba",
    "timestamp": "2018-10-30T14:54:11.583Z",
    "tenantId": null,
    "type": "pay.deductions.DeductionCreated",
    "body": {
    ...
    }
    "metadata": {
        "tenantId": "d0de813b-c3d8-4be5-876a-6c96c31ea7d3",
        "correlationId": "cd75ea39-04ae-473c-ad16-b6467c1d06e1",
        "trace": {
            "X-B3-TraceId": "2c47cedc82b363d9",
            "X-B3-SpanId": "2c47cedc82b363d9",
            "X-Span-Export": "false"
        }
    },
        "aggregateId": "55def832-8312-4dbb-9366-6f33ffb6d548",
        "sequenceNumber": "0"
    }
    ```

- The JSON message we get after has the information in `metadata` as we needed. 