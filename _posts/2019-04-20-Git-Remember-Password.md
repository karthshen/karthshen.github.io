---
layout:     post
title:      "Git remember user password"
subtitle:   " \"I can't believe I am writing a blog on this\""
date:       2019-04-20 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Git
    - Utility
    - Version Control
---

## To Make Git Remember Password ##

Run

`git config credential.helper store`

Then

`git pull`

after providing the user-name and password, those information will be remembered by git for later uses. The credentials are stored in a file on a disk. 

If the password is changed later, simply run

`git pull`

to fail the login, then the old credential will be removed from the `~/.git-credentials` file, then run 

`git pull`

to provide a new credential for login.