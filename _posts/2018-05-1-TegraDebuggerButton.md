---
layout:     post
title:      "NSight Tegra Debugger disappeared"
subtitle:   " \"NSight Tegra Debugger disappears after running -vsdebugandroid\""
date:       2018-05-1 10:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unity
    - Thought
---

# Issue #
NSight Tegra Debugger disappeared after running -vsdebugAndroid command, it got replaced with a Local Debugger button.

# Fix #
Tried all kinds of methods, what ends up working is by re-download the project file from source control and recompile. Apparently certain commands build will kill the NSight Tegra Debugger option. 

