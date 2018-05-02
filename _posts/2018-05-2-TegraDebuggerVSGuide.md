---
layout:     post
title:      "Debug UE4 Game on Android Platform with Visual Studio using Tegra Debugger"
subtitle:   " \"A not so detailed Guide\""
date:       2018-05-2 13:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unreal Engine 4
    - Chromebook
    - Visual Studio
    
---

## What you need ##
1. An Android Device with Tegra CPU
	- I have tested this on Chromebook and a couple other android emulators, but none of them worked with this method. You can still debug on these devices using Android Studio. 
	- I used a Nvidia Shield for this guide.

2. Build the game in Visual Studio using 'Debug Game' or 'Development' with 'Android' configuration.
![](https://i.imgur.com/HyOdvOl.png)

3. Set up your package options for android under Project Setting->Package Setting->Android
	- Make sure the 'Package Game Data Inside APK' option is toggled.
	- Only toggle on the Architecture you need to build on, this reduces the build time and package time by a lot.

4. Make the APK in Unreal Engine editor using Package->Android option.
![](https://i.imgur.com/ifjEHsD.png)

5. Now you can go back to Visual Studio, open your Project Properties and under Debugging, make sure you set Debug Mode to **'Java and Native Code'**

6. Make sure you have your device connected through ADB or USB, and you can press Nsight Tegra Debugger and debug your game!
	- If the game takes longer to deploy, you can extend your deploy time in Nsight Tegra Options. 


