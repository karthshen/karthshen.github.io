---
layout:     post
title:      "Did I write a false test, or should I blame the code?"
subtitle:   " \"Conceptual Question here\""
date:       2019-08-07 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Test
    - Java
    - Software Engineer
    - Thought
---

# Question #
- Think about this, you get a story, it's about writing a high level automation test for the collaboration of multiple functions in multiple applications. 
    - You have your tests setup successfully. 
    - You run the test.
    - Your test fails.

- Alright, of course the test is failing probably because you wrote something wrong, but what if it's the functionality itself isn't running? How do you know which component should be responsible in this case?

# Couple Insights #
- So something you can start trying is that, test this functionality manually. Say if the UI support has already been build, you can manually go over your test case to make sure that the result of the functionality is expected. If that's the case, then it has to be the test you wrote that is responsible for the failing of it.

- What if the UI is not built yet?

# Logical Thoughts #
- Here is the last thing you can do, check over what you wrote for the test, make sure each step is written correctly. If you are certain that what you wrote makes sense, and the actual outcome of your test isn't expected, you can say with some certainty that, whoever wrote the functionality is responsible for this test failure. 

