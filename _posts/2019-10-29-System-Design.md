---
layout:     post
title:      "System Design Principles"
subtitle:   " \"How to design something not shitty\""
date:       2019-08-07 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Design
    - System Design
    - Interview
    - Thought
---
## User System ##
- Very read-heavy, limited write operations. 
- Use cache to optimize read-heavy systems. 

#### Cache ####
- Kinda like a hashmap, if some key is in cache, return it, otherwise find it in database
- Three stages
	- Cache Miss
	- Cache Hit
	- Cache Update

- Naive approach may cause race condition. Lock is not a good solution because it does not scale.    
