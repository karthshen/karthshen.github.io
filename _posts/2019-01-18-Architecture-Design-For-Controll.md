---
layout:     post
title:      "Fighting Game Character Control Design"
subtitle:   " \"State Pattern + Command Pattern\""
date:       2019-01-18 15:43:00
author:     "Karth"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - Unity
    - Architecture
    - Game Development
---

# Character Control Architecture based on the Command Pattern and State Pattern #
- The purpose of this blog is to design a Controller Architecture with the combination of State Pattern and Command Pattern

## Introduction on State Pattern
Look closely to State Pattern for Game Programming at http://gameprogrammingpatterns.com/state.html

The purpose of state pattern is to `alter an object's behavior base on its state, the object may change its class`. 

As suggested by the Game Programming Design Pattern, the Character class can hold it's `state` object, and have its behavior stored in `state` object to execute.
```C++
void Heroine::handleInput(Input input)
{
  HeroineState* state = state_->handleInput(*this, input);
  if (state != NULL)
  {
    delete state_;
    state_ = state;
  }
}
```

And the State object will be written like:
```C++
HeroineState* StandingState::handleInput(Heroine& heroine,
                                         Input input)
{
  if (input == PRESS_DOWN)
  {
    // Other code...
    return new DuckingState();
  }

  // Stay in this state.
  return NULL;
}
```

The Character will have its available behaviors based on a Finite State Machine like this:

![Image](http://gameprogrammingpatterns.com/images/state-flowchart.png)

For an example, Character can only `Duck` while `Standing`, and can only go `Diving` while `Jumping`.

The use of State Pattern here for our controller design is very beneficial. Because we can easily isolate the *behaviors* of character to each state class, instead of having a giant, twisted conditional logic loop like this:

```C++
void Heroine::handleInput(Input input)
{
  if (input == PRESS_B)
  {
    if (!isJumping_ && !isDucking_)
    {
      // Jump...
    }
  }
  else if (input == PRESS_DOWN)
  {
    if (!isJumping_)
    {
      isDucking_ = true;
      setGraphics(IMAGE_DUCK);
    }
  }
  else if (input == RELEASE_DOWN)
  {
    if (isDucking_)
    {
      isDucking_ = false;
      setGraphics(IMAGE_STAND);
    }
  }
}
```

## Introduction on the Command Pattern
Look at http://gameprogrammingpatterns.com/command.html for detailed explanation on Command Pattern.

The purpose of the Command Pattern is to `Encapsulate a request as an object, thereby letting users parameterize clients with different requests, queue or log requests, and support undoable operations.`

Only the first part of that sentence is necessary to explain this pattern, which is to `encapsulate` a request. Basically, as addressed by the Game Programming Book, *A command is a reified method call.*

The Command Pattern can turn something like:
```C++
void InputHandler::handleInput()
{
  if (isPressed(BUTTON_X)) jump();
  else if (isPressed(BUTTON_Y)) fireGun();
  else if (isPressed(BUTTON_A)) swapWeapon();
  else if (isPressed(BUTTON_B)) lurchIneffectively();
}
```

to 

```C++
void InputHandler::handleInput()
{
  if (isPressed(BUTTON_X)) buttonX_->execute();
  else if (isPressed(BUTTON_Y)) buttonY_->execute();
  else if (isPressed(BUTTON_A)) buttonA_->execute();
  else if (isPressed(BUTTON_B)) buttonB_->execute();
}
```

Which then the buttons can be swapped out very easily because they are now commands. 

And the implementation for such Command is like
```C++
class JumpCommand : public Command
{
public:
  virtual void execute() { jump(); }
};

class FireCommand : public Command
{
public:
  virtual void execute() { fireGun(); }
};

```

## The Combination of State Pattern and Command Pattern

So here is the need, I want the button presses encapsulated into `Commands`, and at same time I want the Characters to have the `States` so I can encapsulate their behaviors to the state objects.

One difficulty is that you can't just copy the code from Game Programming book, because their implementation is to have `Character::HandleInput()` function to handle the State and Command at same time. But our goal is to encapsulate the conditional logic part in State.

```c++
    if (isPressed(BUTTON_X)) buttonX_->execute();
    else if (isPressed(BUTTON_Y)) buttonY_->execute();
    else if (isPressed(BUTTON_A)) buttonA_->execute();
    else if (isPressed(BUTTON_B)) buttonB_->execute();
```

So in this case, instead of having the `Characters` to hold the `Command` object, I am making the `States` to hold the Commands, and inside of the `State::HandleInput()`, there will be the conditional logic loop checking for input and calling for appropriate Commands. 

The flow for `Character::Move()` will be something similar to this:
```
Controller->HandleInput(): Calls HandleInput functions on Actors
Actor->HandleInput(): Calls the handleInput() function for the current state
state->handleInput(): detect movement axis, call moveCommand()
moveCommand->execute(): actor.Move();
actor->move(): move to direction base on the joystick axis
```

Here is a brief overview for the Controller Design, hopefully it helps.

