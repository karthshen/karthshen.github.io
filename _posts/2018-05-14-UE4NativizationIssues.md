---
layout:     post
title:      "UE4 Nativization with NDK (Android)"
subtitle:   " \"Nativization issue?\""
date:       2018-05-14 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unreal Engine 4
    - Blueprint Nativization
---


# Issues I Encountered #
### 1. C++ Exception not enabled, thus String.h failed to compile because it had a try-catch block ###

### 2. A Plugin (RakNet) didn't compile because of File Not Found error ###

### 3. After compiled, certain generated BP_XX.cpp didn't compile ###

# My fixes for each one #

### Enable C++ Exception ###
- Inside of ```Engine/Source/Programs/UnrealBuildTool/Platform/Android/Andr                                                                oidToolChain.cs``` there is a boolean variable for exception enabling, set it to true and the C++ exceptions should be enabled.

### Fix include errors for plugins ###
- Under the build.cs file for the game, there should be places for PublicDependencies and PrivateDependencies for your plugins. In my case, my plugin had public dependencies but it was marked as private in the game.build.cs, I changed it to public and things worked.

### BP_GameMode 'marked override' fix ###
- Change the errored blueprint functions to ```BlueprintImplementable``` and delete the *override* functions reported.
