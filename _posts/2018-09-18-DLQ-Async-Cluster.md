---
layout:     post
title:      "Research on Dead-Letter-Queue with Async Cluster on Axon"
subtitle:   " \"DLQ not working with Async Cluster\""
date:       2018-09-18 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Axon
    - Java
    - Dead-Letter-Queue
---

# Research on Dead-Letter-Queue issue
--

### Cause of the Issue
- During one of the version update, someone in the company updated **`AxonAutoCOnfiguration`** starts to use **`AsynchronousCLuster`** instead of `SimpleCluster` when the *Concurrent Consumer* equals to 1. 
- The logic of Dead-Letter-Queue was originally triggered by **`SimpleCluster`** throwing **`AmqpRejectAndRequeueException`**. The issue is that, when **`AsynchronousCLuster`** is used, the exception is handled within the**`ErrorHandler`**of**`AsynchrounousCluster`**, and the ErrorHandler, according to the Axon Documentation, should not throw exceptions. Since no exception is thrown while using AsynchronousCLuster, the rejected/dead messages will never be sent to the Dead-Letter-Queue.

### Observations on the Logic of DQL
#### In the case of SimpleCluster
1. The **AmqpRejectAndDontRequeueException** was thrown whenever a message is determined as *Rejected*.
2. The exception is then caught and thrown by different classes, and eventually invokes *doInvokeListener* in **AbstractMessageListenerContainer**. 
3. After some black magic.
4. *doPublish* method under **SimpleCluster** catches a *RuntimeException*, it then notifies its monitors and throw the exception it catches. (The *RuntimeException* in this case is the *AmqpRejectedAndDontRequeueException*)  

#### In the case of AsynchronousCluster
1. **AsynchronousCLuster** does not have a try-catch block in its *doPublish()* method, because since it's Asynchronous, all of the exceptions happens in AsynchronousCluster is handled by its **ErrorHandler**. 
2. The **ErrorHandler** handles the exception, and returns a **RetryPolicy** base on the Exception it handles. 
3. The issue is that, since the DLQ is only triggered by **AmqpRejectedAndDontRequeueException**, the DLQ is never triggered in AsynchronousCluster, which does not throw exceptions.   

### Attempts to Fix this Issue 
