# Command Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Smart Home Automation C++ implementation. No external concepts have been added. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction
2. Basic Idea
3. Why Convert a Request into an Object?
4. Smart Home Automation Example
5. Problem Without Command Pattern
6. Command Pattern Solution
7. UML
8. Beginning of C++ Implementation
9. Part Summary

---

# 1. Introduction

Command Pattern is a **Behavioral Design Pattern**.

The lecture begins with a simple question.

Suppose one object wants another object to perform some work.

Normally,

it directly calls one of its methods.

For example,

```
Receiver.run()

Receiver.walk()

Receiver.talk()
```

The source object directly communicates with the receiver.

---

## Basic Interaction

```
Source

↓

Receiver

↓

Method Call

↓

Output
```

The request goes directly from the source to the receiver.

---

# 2. Treat the Request as an Object

Instead of directly calling the receiver,

the lecture introduces a new idea.

Treat the **request itself as an object**.

Instead of only having

```
Source

↓

Receiver
```

we now introduce another object.

```
Source

↓

Command

↓

Receiver
```

The Source no longer communicates directly with the Receiver.

Instead,

it sends the request to the Command.

The Command knows how to communicate with the Receiver.

---

# Why Introduce Command?

The lecture explains that introducing the Command object makes

```
Source

and

Receiver
```

**loosely coupled**.

The Source no longer needs to know

- which receiver is being used
- how the receiver performs the work

It simply asks the Command to execute the request.

---

# Request Flow

```
Source

↓

Command

↓

Receiver

↓

Result
```

The Command acts as an intermediary between the Source and the Receiver.

---

# 3. Smart Home Automation Example

The lecture explains the pattern using a **Smart Home Automation System**.

Suppose there is a remote controller.

It contains multiple buttons.

Each button controls a different appliance.

Examples

- Light
- Fan
- AC

```
Remote

┌──────┐

│ Btn1 │ → Light

│ Btn2 │ → Fan

│ Btn3 │ → AC

└──────┘
```

Pressing a button performs an action on the corresponding appliance.

---

# Goal

Whenever a button is pressed,

the correct appliance should perform the required operation.

Examples

```
Button 1

↓

Light ON
```

```
Button 2

↓

Fan ON
```

```
Button 3

↓

AC ON
```

---

# 4. Solution Without Command Pattern

The lecture first discusses how we would normally implement this.

Suppose

```
Remote
```

stores

```
Light

Fan

AC
```

objects directly.

Whenever

```
pressLightButton()
```

is called,

the Remote executes

```cpp
light.on();
```

Similarly,

```
pressFanButton()
```

calls

```cpp
fan.on();
```

---

# Problem

The Remote now knows

- Light
- Fan
- AC

directly.

The Remote becomes tightly coupled with every appliance.

---

# Tight Coupling

```
          Remote

     /      |      \

 Light     Fan      AC
```

Every new appliance requires changes inside the Remote.

---

# Dynamic Requirement

Suppose today

Button 1 controls

```
Light
```

Tomorrow,

the same button should control

```
Fan
```

Later,

it should control

```
Heater
```

Every time,

the Remote class has to be modified.

---

# Problem

Changing the behavior of a button requires modifying

```
Remote
```

This violates the **Open-Closed Principle** discussed earlier in the course.

---

# 5. Command Pattern Solution

Instead of connecting

```
Remote

↓

Light
```

the lecture introduces

```
Remote

↓

Command

↓

Light
```

Now,

the Remote only knows

```
Command
```

The Command knows

which appliance should perform the operation.

---

# New Flow

```
Button Press

↓

Remote

↓

Command.execute()

↓

Receiver

↓

Actual Work
```

The Remote never directly interacts with the appliance.

---

# Advantage

If tomorrow

Button 1 should control a Fan,

the Remote remains unchanged.

Only the assigned Command changes.

---

# Dynamic Assignment

Today

```
Button 1

↓

LightCommand
```

Tomorrow

```
Button 1

↓

FanCommand
```

The Remote still performs exactly the same operation.

```
command.execute()
```

---

# 6. UML

The lecture now introduces the UML.

---

## Command Interface

```
Command

----------------

execute()
```

Every command must implement

```
execute()
```

---

## Receiver

```
Light

-------------

on()

off()
```

The Receiver knows

how to perform the actual work.

---

## Concrete Command

```
LightCommand

--------------

Light*

execute()
```

LightCommand stores

```
Light*
```

When

```
execute()
```

is called,

it simply invokes

```
light.on()
```

---

## Invoker

The Invoker is

```
RemoteController
```

It stores

```
Command*
```

instead of

```
Light*

Fan*

AC*
```

This removes direct dependency on appliances.

---

# UML Diagram

```
               Command

           + execute()

                 ▲

                 │

         -----------------

         │               │

 LightCommand      FanCommand

         │               │

         ▼               ▼

      Light            Fan



             ▲

             │

      RemoteController

        Command*
```

The Remote communicates only with the Command.

The Command communicates with the Receiver.

---

# 7. Beginning of the C++ Implementation

The implementation starts with the

```cpp
Command
```

interface.

```cpp
class Command {
public:
    virtual void execute() = 0;
    virtual void undo() = 0;
};
```

The lecture later adds

```
undo()
```

to support button toggling.

At the beginning,

the main focus is

```
execute()
```

---

# Receiver Classes

The Receivers are

```cpp
Light

Fan
```

Each Receiver provides

```cpp
on()

off()
```

Example

```cpp
class Light {
public:
    void on();
    void off();
};
```

The Receiver knows how to perform the actual operation.

The Command simply delegates the request.

---

# Concrete Command

The lecture implements

```
LightCommand
```

It stores

```cpp
Light*
```

Whenever

```cpp
execute()
```

is called,

it invokes

```cpp
light->on();
```

Similarly,

```
FanCommand
```

stores

```
Fan*
```

and executes

```cpp
fan->on();
```

Each Concrete Command is responsible for exactly one Receiver.

---

# Remote Controller

Instead of storing

```
Light

Fan

AC
```

the Remote stores

```
Command*
```

Initially,

the lecture considers a single button.

Later,

this is extended to multiple buttons by storing an array (or vector) of Commands.

Each button can then be assigned a different Command dynamically.

---

# Part 1 Summary

- Command Pattern is a **Behavioral Design Pattern**.
- Instead of directly invoking the Receiver, the Source sends the request to a Command object.
- Treating the request as an object decouples the Source from the Receiver.
- The Smart Home Automation example demonstrates this using a Remote Controller.
- Without Command Pattern, the Remote becomes tightly coupled to appliances.
- Using Commands allows buttons to be reassigned dynamically without modifying the Remote.
- The main participants introduced are:
  - **Command** (Interface)
  - **Concrete Commands** (`LightCommand`, `FanCommand`)
  - **Receiver** (`Light`, `Fan`)
  - **Invoker** (`RemoteController`)

---

**Next:** Part 2 covers the complete code walkthrough (`undo()`, `RemoteController`, `setCommand()`, `pressButton()`, execution flow, toggle behavior, main function, real-world use cases, and revision sheet.

# Command Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Smart Home Automation C++ implementation. :contentReference[oaicite:0]{index=0}

---

# 8. Adding Undo Support

After explaining the basic Command Pattern,

the lecture improves the design by introducing

```cpp
undo()
```

Earlier,

the Command interface contained only

```cpp
execute()
```

Now it becomes

```cpp
class Command {
public:
    virtual void execute() = 0;
    virtual void undo() = 0;
};
```

---

## Why add undo()?

The lecture explains this using the remote controller.

Suppose pressing a button once

```
Light ON
```

Pressing the same button again should

```
Light OFF
```

Instead of creating two different buttons,

the same button performs

- Execute
- Undo

depending on its current state.

---

## Button Flow

```
Press

↓

execute()

↓

Light ON

↓

Press Again

↓

undo()

↓

Light OFF
```

This creates the toggle behavior.

---

# 9. Multiple Buttons

Initially,

the Remote contained only one command.

The lecture extends this to

multiple buttons.

Instead of storing

```cpp
Command*
```

it stores

```cpp
Command* buttons[]
```

Every index represents one button.

Example

```
Button 0

↓

LightCommand

---------------

Button 1

↓

FanCommand

---------------

Button 2

↓

nullptr
```

Each button can independently execute a different command.

---

# 10. RemoteController Class

The Invoker is

```cpp
RemoteController
```

It stores

```cpp
Command* buttons[4];
```

and

```cpp
bool buttonPressed[4];
```

---

## buttons[]

Stores the command assigned to every button.

Example

```
Index 0

↓

LightCommand

---------------

Index 1

↓

FanCommand
```

---

## buttonPressed[]

Stores the current state.

```
false

↓

Button OFF
```

```
true

↓

Button ON
```

This helps decide whether to call

```
execute()

or

undo()
```

---

# Constructor

The constructor initializes

```cpp
buttons[i] = nullptr;
```

and

```cpp
buttonPressed[i] = false;
```

Initially,

no command is assigned

and every button is in the OFF state.

---

# setCommand()

```cpp
setCommand(index, command)
```

Purpose

Assign a command to a particular button.

Flow

```
Index Valid?

↓

Yes

↓

Delete Previous Command

↓

Assign New Command

↓

buttonPressed = false
```

If the index is invalid,

nothing is assigned.

---

# pressButton()

This is the most important method in the implementation.

Flow

```
Button Pressed

↓

Index Valid?

↓

Command Exists?

↓

Yes

↓

buttonPressed ?

↓

false

↓

execute()

↓

true

---------------

buttonPressed ?

↓

true

↓

undo()

↓

false
```

The lecture explains that

the Remote never knows

what operation is being performed.

It simply invokes

```
execute()

or

undo()
```

on the assigned command.

---

# Invalid Button

If no command exists,

the Remote prints

```
No command assigned at button
```

This handles buttons that have not yet been configured.

---

# 11. Main Function

The lecture demonstrates the complete flow.

---

## Step 1

Create Receivers.

```cpp
Light

Fan
```

Current objects

```
Light

Fan
```

---

## Step 2

Create the Remote.

```cpp
RemoteController
```

Initially

```
Buttons

↓

nullptr

nullptr

nullptr

nullptr
```

---

## Step 3

Assign Commands.

```cpp
Button 0

↓

LightCommand

---------------

Button 1

↓

FanCommand
```

The Remote now knows

only Commands,

not Receivers.

---

## Step 4

Press Button 0

```
Button 0

↓

LightCommand

↓

execute()

↓

Light ON
```

Output

```
Light is ON
```

---

## Step 5

Press Button 0 Again

```
Button 0

↓

LightCommand

↓

undo()

↓

Light OFF
```

Output

```
Light is OFF
```

---

## Step 6

Press Button 1

```
Button 1

↓

FanCommand

↓

execute()

↓

Fan ON
```

Output

```
Fan is ON
```

---

## Step 7

Press Button 1 Again

```
Button 1

↓

FanCommand

↓

undo()

↓

Fan OFF
```

Output

```
Fan is OFF
```

---

## Step 8

Press Button 2

No command has been assigned.

Output

```
No command assigned at button 2
```

---

# Complete Execution Flow

```
User

↓

Press Button

↓

RemoteController

↓

buttons[index]

↓

execute()

or

undo()

↓

Concrete Command

↓

Receiver

↓

Actual Work
```

---

# Relationship Between Classes

```
RemoteController

        │

        ▼

Command Interface

        ▲

        │

----------------------------

│                          │

LightCommand          FanCommand

│                          │

▼                          ▼

Light                     Fan
```

The Remote only communicates with the Command interface.

Each Concrete Command knows its Receiver.

---

# Important Doubt Discussed in the Lecture

The lecture explains why

the abstract

```
Command
```

does **not**

store a Receiver reference.

Instead,

only the

Concrete Commands

store Receiver references.

Reason

The abstract Command only defines

```
execute()

undo()
```

It does not know

what object it is operating on.

Each Concrete Command knows

its own Receiver.

Example

```
LightCommand

↓

Light
```

```
FanCommand

↓

Fan
```

Therefore,

the Receiver reference belongs inside the Concrete Command.

---

# Real World Use Cases

The lecture discusses common applications.

---

## Undo Feature

Applications supporting

```
Ctrl + Z
```

internally use the Command Pattern.

Examples

- Text Editors
- Photoshop
- Other applications with Undo functionality

Each user action becomes a Command.

The same Command knows how to

- execute
- undo

---

## Keyboard Shortcuts

Keyboard shortcuts are another example.

Example

```
Shortcut Key

↓

Command

↓

Required Action
```

The keyboard is not directly connected to the functionality.

Instead,

a Command is assigned to the shortcut.

This allows shortcuts to be changed dynamically.

---

# Key Observations

- The Remote does not know the Receiver.
- Commands know the Receiver.
- Every button stores a Command.
- Buttons can be reassigned dynamically.
- Undo is implemented by calling the opposite operation.
- The Remote only delegates requests.

---

# Revision Sheet

## Pattern Type

Behavioral Design Pattern

---

## Participants

### Invoker

```
RemoteController
```

---

### Command Interface

```
Command
```

Methods

- execute()
- undo()

---

### Concrete Commands

- LightCommand
- FanCommand

---

### Receivers

- Light
- Fan

---

## Important Data Members

RemoteController

```
buttons[]

buttonPressed[]
```

---

## Main Methods

```
setCommand()

pressButton()

execute()

undo()
```

---

## Complete Flow

```
User

↓

Remote

↓

Command

↓

Receiver

↓

Action Performed
```

---

## Toggle Flow

```
First Press

↓

execute()

↓

ON

---------------

Second Press

↓

undo()

↓

OFF
```

---

# Final Summary

The Command Pattern encapsulates a request as an object.

In this implementation:

- **RemoteController** acts as the **Invoker**.
- **Command** provides a common interface.
- **LightCommand** and **FanCommand** are the **Concrete Commands**.
- **Light** and **Fan** act as the **Receivers**.

The Remote delegates every button press to a Command object. Each Command knows which Receiver should perform the work. By introducing `undo()`, the same button can toggle between executing and reversing an action, while the Remote remains independent of the actual appliances.
