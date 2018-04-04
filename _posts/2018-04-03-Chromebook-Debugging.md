---
layout:     post
title:      "Debugging and Testing on Chromebook"
subtitle:   " \"The road to black magic?\""
date:       2018-04-03 16:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unreal Engine 4
    - Chromebook
    - Debugging
---

## Before everything... ##
Developing and debugging Unreal Engine 4 games on Chromebook is not a very pleasing process. Since there is basically no one doing this anywhere else, so I had to use Android guides as a reference in this whole process.

## What I have tried so far ##
- **Nsight Tegra Debugger**: Default debugger provided by Codework for Android by Nvdia, sadly this default option hasn't been working since day 1. The process always stuck at attaching Debugger onto the process. Tried quite some black magics on this one but still not working. 
	- This method may be failing because the chromebook we have is very different from a regular Android device (?)

- **Crashlytics**: another crash analytic plugin provided
Unreal Engine 4 support, sadly this tool is reported as a "worm hole" and ended up not working.

- **Google Breakpad**: currently trying, integrated this into the game.
	- Using this article as a guide: 
	- http://zompi.pl/symbolicating-crash-dumps-for-android-in-unreal-engine-4/
	- Side note: it is good to know that under Engine/Build there are Java codes under Android directory. 
	- Make sure you **ENABLE** the plugin in Unreal Engine Editor before you start to package.
	- @TODO right now this only works on Ubuntu, see if it could be running on Windows later.