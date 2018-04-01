---
layout:     post
title:      "Github Clean for Untracked Files"
subtitle:   " \"Fix for untracked files after update gitignore\""
date:       2018-04-01 0:15:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Bug fix
---
﻿

##GitHub Clean for Untracked Files

So sometimes after updating .gitignore it doesn't immediately reflect on the current changes in your git repository. The following commands should be able to clean the current git cache and allow you to commit a clean changelist.

```
git rm -r --cached .
git add .
git commit -m "fixed untracked files"
```


