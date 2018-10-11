---
layout:     post
title:      "How to test values stored in MDC"
subtitle:   " \"Ways to test thread-local context\""
date:       2018-10-09 15:43:00
author:     "Karth"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - Java
    - Test
---

## Issue 
------------------
- So say if you need to use **MDC** (Mapped Diagnostic Context) as a thread-safe context to store and transfer data.
- The issues kicks in when you want to make unit tests on the MDC values.
- Since MDC is thread-local, that means you can't really retrieve its value by '@Autowire' the controller or call it directly. 

### A Non-Elegant Solution
- At very first, I made the controller to output the values in console, and make tests that test against the console output. Something like below:

    ```js
    ByteArrayOutputStream out = new ByteArrayOutputStream();
    PrintStream stream = new PrintStream(out);
    System.setOut(stream);

    //Sends the request to controller through some route, and check the log
    when()
        .get(ROUTE)
        .then();

    String[] logs = new String(out.toByteArray()).split("\n");
    assertThat(new String(out.toByteArray()), logs.length, is(4));

    String expectedValue = "expected_value"

    assertThat(logs[0].split("Some-Splitter")[1], is(expectedTenant_id));
    ```

- Now while this solution works, there are a few issues.
    1. This solution relies alot on the format of the log output, that means if the something changes with the log, this test probably needs to be changed as well
    2. This solution may pollute the output console with logs that's used specifically for test purposes.
    3. You can't really have every tests rely on the output of the console because the console output format may change (say from regular to Json)

- So let's try something better.

### A Sorta-Elegant Solution
- So back to the business, instead of checking against the log, we find a way to pull the data out of the MDC that's hidden in the local thread.
- We are doing this RESTfully, so first let's start with adding a new `GetMapping()` in our controller:
    ```js
    @GetMapping("/getMDCMap")
        public Map<String, String> getMDCMap() {
        return MDC.getCopyOfContextMap();
    }
    ```
- This will return the MDC that was currently stored in that thread whenever it's requested
- And now under our test, we can easily do something like this to retrieve the values stored in MDC:
    ```js
    Map mdcMap = when()
        .get(ROUTE + "/getMDCMap")
        .then()
        .extract()
        .body()
        .as(Map.class);
    ```
- This should get the `Map<>` that was contained within the MDC, and you should be able to do something like:
    ```js
    assertThat(mdcMap.get("Some-Key-Id"), is(expectedTenant_id));
    ```

- Now you have a clean code and a happy programmer!