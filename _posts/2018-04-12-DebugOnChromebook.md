---
layout:     post
title:      "Debug Unreal Engine 4 projects on Chromebook "
subtitle:   " \No Guarantee this guide will work.\""
date:       2018-04-12 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Chromebook
    - Unreal Engine 4
    - Android
---


# Android Studio Debug Steps #
1. Inlcude LibUE4.so symbols when Android Studio says "Missing LibUE4.so symbol".

2. Check the Settings to make sure that it looks identical to this.
	![](https://i.imgur.com/WLqjml9.jpg)

3. In "Edit Configurations", under Debugger - Symbol Directories, add the symbol directory from Client folder.

	![](https://i.imgur.com/PQESqyA.jpg) 

# Debugging the Client in Visual Studio #
- Try to run 
	- ```E:/ImmersedUnreal4/UnrealEngine/Engine/Binaries/DotNET/UnrealBuildTool.exe  -projectfiles -project="E:/.../TytoOnline.uproject" -game -engine -progress -vsdebugandroid```

- This command should generate two Debuggable projects in Visual Studio, however, it seems like this option only works with Android Virtual Devices.