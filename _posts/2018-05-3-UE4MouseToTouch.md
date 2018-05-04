---
layout:     post
title:      "Convert Mouse input to Touch input in UE4"
subtitle:   " \"Shot out to Epic for the lacking of documentation\""
date:       2018-05-03 16:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unreal Engine 4
    - Android 
    - Touch Screen
---

## A couple Conditions ##
1. If your application was originally designed for Windows/Mac users, and your input method includes Right Click, then you probably will need to figure something out design wise. Because Android does not know what a 'Right Click' is.
2. For mobile devices, there is only Touch1/2/3/4/5/6... these **Touch** enums represents how many fingers are operating the screen at the moment. Touch1 represents single finger operations, and so on. 
	- You can access the enum using ```ETouchIndex::Touch1```

## Example ##
- Assume I originally have a drag and drop functionality that controls the camera movement using mouse, say I hold mouse_left key and it rotates the camera. Now I want to convert this to a touch-screen operation.
- The idea here is to manually capture the deltaLocation for touch-screen drag and drop, and use that value to perform the rotation operation.

### Procedure ###
0. Add Touch 1 to the Mouse operations in Project Settings->Input in Unreal Engine.
![](https://i.imgur.com/XFZ00o8.png)
1. Create variables necessary for the function, such as
```
	FVector OldTouchLocation; 
	FVector NewTouchLocation; 
	bool bIsTouchPressed;
```
etc.

2. Under the ```APlayerController::MousePress()``` function, implement the following:
```
	Controller->GetInputTouchState(ETouchIndex::Touch1, OldTouchOneLocation.X, OldTouchOneLocation.Y, bIsTouchOneCurrentlyPressed);
```

	- This code will tell the application that, when the mouse is pressed, check the status of ```Touch1```, and the function call ``GetInputTouchState()``` will return the Location.X, Location.Y, and whether the user is holding the touch. The variables are returned by passing by reference.

3. Now under a tick function, write something like the following code to calculate the DeltaLocation for the touch operation. 
		```
		if (Cast<ATytoPlayerController>(Controller)->GetIsTouchPressed(ETouchIndex::Touch1)) {
			Cast<ATytoPlayerController>(Controller)->GetInputTouchState(ETouchIndex::Touch1, Cast<ATytoPlayerController>(Controller)->NewTouchOneLocation.X, Cast<ATytoPlayerController>(Controller)->NewTouchOneLocation.Y, Cast<ATytoPlayerController>(Controller)->bIsTouchOneCurrentlyPressed);
			FVector deltaTouchLocation = Cast<ATytoPlayerController>(Controller)->NewTouchOneLocation - Cast<ATytoPlayerController>(Controller)->OldTouchOneLocation;
			//deltaTouchLocation.X *= -1.0f;

			DeltaTouchOneOffset = deltaTouchLocation;
			Cast<ATytoPlayerController>(Controller)->OldTouchOneLocation = Cast<ATytoPlayerController>(Controller)->NewTouchOneLocation;
		}
		```
4. And finally, you can use the result of the calculation (deltaLocation) in your rotation code.

## Conclusion ##
- Epic did not do a good job at documenting the functionality of ```GetInputTouchState()```, basically this function will get you the state of the touch operation you request (one figer, two fingers, etc). And by using this method, you can get a good information of what user did and perform methods base on that.

- Also shot out to this guy for getting me to the right direction
- https://chrysalisinteractive.wordpress.com/2016/12/02/touch-and-drag-camera-tutorial-both-blueprint-and-c/