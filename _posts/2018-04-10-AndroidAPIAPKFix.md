---
layout:     post
title:      "Android Build API Missing Fix"
subtitle:   " \"Can't make an apk without api?\""
date:       2018-04-10 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unreal Engine APK and API
---

# Issue #
After installing Android Studio, it somehow messes with the API that's previously installed on my computer.

# Fix #
 ~~After some trail and errors, it seems like installing/uninstalling stuff in SDK Manager happen to fix the issue.~~

### Update ###
1. Uninstall Android SDK under C:/Program Files
2. Uninstall Codeworks with Uninstall batch under NVPACK.
3. Reinstall NVPACK with the codework for android
4. If this doesn't work, try to reinstall Visual Studio and redo step 1 to 3.

