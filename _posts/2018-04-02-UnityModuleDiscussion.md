---
layout:     post
title:      "Thoughts on Nesting 3D Objects"
subtitle:   " \"What's the best way to composite GameObject in Unity?\""
date:       2018-04-02 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - Unity
    - Thought
---

# Thoughts on Nesting 3D Objects #
### Why nesting 3D objects? ###
I started thinking about this issue in Unity since I started my FF_Tactic Project about a week ago. So here are a few things I noticed when I try to play with colliders and other components on a Object in code:
1. When I want to have access to a collider of an object, I must do ```GetComponentInChild<Component>()``` in order to have access to the collider of a nested GameObject, because by default, the collider of an object is bound to the 3D model (mesh), and the way I learned on class is to include the 3D model under an Empty object, so it looks like this:

- Desk
	- DeskModel
		- Collider
		- Transform
		- etc
	- DeskScript

The issue with this structure is that, whenever you click on an object in game, you are actually accessing its collider, and when you want to have access to its Object (script concept), you need to do ```GetComponentInParent<>()```, and when you want have access to its collider, you need to do ```GetComponentInChild<>()```.

This is a little too much of work do deal with, so I started thinking why this is the way taught in class, so I had a conversation with my Unity Professor.

### Professor's Response ###
Mr. French had a few reasoning behind the way he uses GameObjects, and I realized I have made some misunderstanding about it.


> NESTING 3D OBJECTS INSIDE AN EMPTY GAME OBJECT
> 
> 1.1 When creating compound objects in Unity, I will usually have at least three elements that make up my game object. One will be the model (comprised of one or more separate 3D objects), one will be the audio sources (one will suffice if only one sound will be played at any given time), and the third might be empty game objects used as position references for object instancing.
> 
> 1.2 In order to keep all of this organized, I usually create an empty game object and name it accordingly (f.i. "Player"). Afterwards, I create separate children - empty game objects (Model - used for nesting the 3D graphics, Audio - used for nesting audio sources, and a position reference game object which can be used as a point from which missiles can be spawned, for example).
> 
> 1.3 I usually have no need to access transform parent or transform children game objects, because I remove the colliders from the 3D game objects (children), and place a new collider (usually one is sufficient) on the parent game object. Thus, even in the case of your code, you wouldn't have to work with transform.parent, because the hit would return the game object which has a collider on it, in this case the parent itself.
> 
> 1.4 This method has it's drawbacks. 
> 1.4.1 One of them is that you need to be careful while tagging the game objects, sometimes the parent and children need to have the same tag, other times it is important that they do not have the same tag.
> 1.4.2 Another drawback is that this method isn't needed if your model is a single basic 3D shape, and you don't intend to add audio sources, transform references, compound colliders etc. If it's just a simple rolling ball, you can create a 3D sphere, name it "Player", and attach all the components you wish to it.
> 1.4.3 Yet another drawback might be that you simply don't like the method, and wish to find an alternate way of working, which is totally acceptable.

#### As a Summary####
- I misunderstood some of the technique. Collider and Mesh should be treated as two separate Components on an GameObject, so the Collider should be placed under the GameObject itself, instead of the Mesh. The default implementation provided by Unity is to treat the Mesh as the Base component of the GameObject, and that is different from what we want to have. 

- Also, here is an comparison between attaching the script component to the Parent vs the Children.

> 2.1 This one mostly depends on the type of script file, and the way you build your game objects.
> 2.2 Sometimes it is totally irrelevant whether the script has been placed on the parent or child game object.
> 2.2.1 Here's a case where it can be relevant: let's say we create an empty game object, name it "Player", and nest a sphere as it's child. If we wish to move the player by accessing the rigidbody, we need to add a rigidbody component to a game object. I would usually attach the collider, the rigidbory, and the move script to the parent game object, because when we move the parent, we also move the sphere child game object 
> as well. 
> 2.2.2 Another way around this would be to attach the rigidbody to the parent game object "Player", attach the move script to the child game object (sphere), but then we would need to access the rigidbody of the game object's transform parent, instead of the gameobject itself. It would work, but is needless in my opinion.
> 2.1.4 Yet another way to do it would be to place the collider, rigidbody and move script on the sphere, and not the parent. This would have one side-effect though: as we are moving the child, it's position in local space (relative to the position of the parent) would change, but the position of the player game object would remain the same, it would not be affected with the movement of the sphere. If that is a behavior one requires, then this method would work.

However, even as I understand the issue now, it is too hard to reimplement what I already had in the code, because changing the parent/child relationship could be a mess, and so far I don't have a good idea on how to write test cases in the Unity Project yet.