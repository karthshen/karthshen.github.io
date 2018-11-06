---
layout:     post
title:      "Java Dependency included but not exist"
subtitle:   " \"Another example of resolution by purging\""
date:       2018-10-05 15:43:00
author:     "Karth"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - Intellij
    - Java
---

# Issue
- Java reported me that `Java <Library> does not exist`, when the dependency injection is well implemented in `pom.xml` and visible in Maven.

# Process
- Solution 1 - `Clean the Cache` : Didn't work
- Solution 2 - Re-imported the dependencies and nothing changes

# Final Solution
- Deleted whatever dependency that's in question under 
    ```
     ~/.m2/repository/com/<Dependency Name>
    ```
    - This deletes the imported repository on your local. 
- Run `mvn clean compile` in console, this will re-import the Maven dependency for the desired project.
- Re-run the application, everything regarding the previous dependency error should be resolved.