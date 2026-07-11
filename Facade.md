# Facade Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Computer Boot-up C++ implementation. No external concepts have been added. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction
2. What is a Facade?
3. Why Facade Pattern?
4. Computer Boot-up Example
5. Components
6. Standard UML
7. Beginning of C++ Implementation
8. Part Summary

---

# 1. Introduction

Facade Pattern is a **Structural Design Pattern**.

The lecture introduces it as one of the simplest design patterns.

Its main purpose is to provide a **single entry point** to a complex subsystem.

Instead of allowing the client to communicate with many different classes,

the client communicates with only one class called the **Facade**.

---

# 2. What is a Facade?

Suppose we have a subsystem containing many classes.

```
Class A

Class B

Class C

Class D

Class E
```

These classes interact with one another.

Example

```
A → B

A → C

B → C

B → D

D → E
```

Together,

they perform one large task.

---

## Problem

A client wants to use this subsystem.

Without any abstraction,

the client has to know

- which class to call first
- which class to call next
- the complete sequence of operations

The client becomes tightly coupled with the subsystem.

---

# Solution

Introduce a new class.

```
Facade
```

The client communicates only with this class.

```
Client

↓

Facade

↓

Complex Subsystem
```

The Facade internally communicates with all subsystem classes.

The client only calls a single method.

---

# Gateway Idea

The lecture describes the Facade as a **Gateway**.

Instead of entering the subsystem directly,

the client enters through the Facade.

```
Client

↓

Facade

↓

Subsystem
```

The client does not know

how the work is performed internally.

---

# Single Method

The lecture explains this with a simple method.

```
getWorkDone()
```

The client simply calls

```
getWorkDone()
```

The Facade performs all required interactions internally.

The client only receives the final result.

---

# 3. Why Facade Pattern?

The lecture highlights two major reasons.

---

## Hide Complexity

The client does not need to understand

the internal working of the subsystem.

Only the Facade knows

which classes should be called.

---

## Decoupling

The client becomes independent of the subsystem.

```
Client

↓

Facade

↓

Subsystem
```

If the subsystem changes later,

the client remains unchanged.

Only the Facade may require modification.

---

# Complex Subsystem

The subsystem may contain

- many classes
- many dependencies
- many interactions

The client should not be responsible for handling them.

---

# Principle of Least Knowledge

The lecture mentions another benefit.

Facade helps establish the

```
Principle of Least Knowledge
```

The detailed discussion of this principle comes later in the lecture.

For now,

the important idea is

the client should know as little as possible about the subsystem.

---

# 4. Computer Boot-up Example

The lecture explains Facade Pattern using

```
Computer Boot-up
```

Suppose we press

```
Power Button
```

From the user's perspective,

only one operation happens.

```
Start Computer
```

However,

internally,

many components work together.

---

# Internal Components

The lecture introduces several subsystem classes.

```
CPU

Memory

BIOS

Hard Drive

Power Supply

Cooling System

Operating System
```

Each performs a small task.

Together,

they complete the boot-up process.

---

# Internal Flow

When the computer starts,

many operations occur.

Example

```
Power Supply

↓

Cooling System

↓

CPU Initialization

↓

Memory Check

↓

BIOS Boot

↓

Hard Drive Spin

↓

Operating System Load
```

All these operations are hidden from the client.

---

# Client View

The client simply performs

```
startComputer()
```

Nothing else.

The complete subsystem remains hidden.

---

# Clean Diagram

```
                 Client

                    │

                    ▼

           ComputerFacade

           startComputer()

                    │

      -----------------------------

      │      │      │      │

 Power  CPU  BIOS  Memory ...

      │

      ▼

 Operating System

      ▼

 Computer Booted
```

The client interacts only with

```
ComputerFacade
```

---

# 5. Components

The lecture identifies two major parts.

---

## Client

The client wants to start the computer.

The client never communicates directly with

- CPU
- BIOS
- Memory
- Hard Drive

---

## Facade

```
ComputerFacade
```

Responsibilities

- Communicate with every subsystem
- Execute the operations in the correct order
- Hide all implementation details

---

## Subsystem

The subsystem consists of multiple classes.

Examples

```
PowerSupply

CoolingSystem

CPU

Memory

HardDrive

BIOS

OperatingSystem
```

Each subsystem performs only its own responsibility.

---

# Relationship

```
Client

↓

ComputerFacade

↓

PowerSupply

↓

CoolingSystem

↓

CPU

↓

Memory

↓

BIOS

↓

HardDrive

↓

OperatingSystem
```

The client knows only the Facade.

---

# 6. Standard UML

```
                Client

                   │

                   ▼

                Facade

             execute()

                   │

     -----------------------------

     │      │      │      │

Subsystem1 Subsystem2 Subsystem3 ...
```

The Client communicates only with the Facade.

The Facade communicates with the subsystem.

---

# 7. Beginning of the C++ Implementation

The lecture first creates the subsystem classes.

---

## PowerSupply

```cpp
class PowerSupply
```

Method

```cpp
providePower()
```

Responsibility

Provide power to the computer.

---

## CoolingSystem

Method

```cpp
startFans()
```

Responsibility

Start the cooling fans.

---

## CPU

Method

```cpp
initialize()
```

Responsibility

Initialize the CPU.

---

## Memory

Method

```cpp
selfTest()
```

Responsibility

Perform memory self-test.

---

## HardDrive

Method

```cpp
spinUp()
```

Responsibility

Spin the hard drive.

---

## BIOS

Method

```cpp
boot()
```

The lecture points out that

BIOS internally requires

```
CPU

Memory
```

The BIOS starts

- CPU initialization
- Memory self-test

---

## OperatingSystem

Method

```cpp
load()
```

Responsibility

Load the operating system.

---

# Observation

Each subsystem class performs only one specific responsibility.

None of them knows about the complete boot process.

The complete boot sequence is managed by the Facade.

---

# Beginning of ComputerFacade

The lecture now introduces

```cpp
ComputerFacade
```

It stores objects of every subsystem.

```cpp
PowerSupply

CoolingSystem

CPU

Memory

HardDrive

BIOS

OperatingSystem
```

The client never creates these objects directly.

Instead,

they are managed inside the Facade.

---

# Part 1 Summary

- Facade Pattern is a **Structural Design Pattern**.
- It provides a **single entry point** to a complex subsystem.
- The client communicates only with the Facade.
- The subsystem may contain many interacting classes.
- The Computer Boot-up example demonstrates this concept.
- The subsystem includes:
  - PowerSupply
  - CoolingSystem
  - CPU
  - Memory
  - BIOS
  - HardDrive
  - OperatingSystem
- `ComputerFacade` owns these subsystem objects and hides all complexity from the client.

---

**Next:** Part 2 covers the complete `ComputerFacade::startComputer()` implementation, execution flow, `main()` walkthrough, Principle of Least Knowledge, standard definition, Facade vs Adapter comparison, real-world use cases, and the final revision sheet.

# Facade Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Computer Boot-up C++ implementation. :contentReference[oaicite:0]{index=0}

---

# 8. ComputerFacade Class

After creating all the subsystem classes,

the lecture introduces the

```cpp
ComputerFacade
```

This class acts as the single entry point for the client.

Instead of the client interacting with every subsystem,

the client only communicates with

```
ComputerFacade
```

---

## Data Members

The Facade stores objects of all subsystem classes.

```cpp
PowerSupply

CoolingSystem

CPU

Memory

HardDrive

BIOS

OperatingSystem
```

The client never creates or manages these objects.

The Facade owns them and coordinates their execution.

---

# startComputer()

The most important method of the Facade is

```cpp
startComputer()
```

This method hides the complete boot-up sequence from the client.

---

## Execution Sequence

The lecture performs the operations in the following order.

```
Provide Power

↓

Start Cooling Fans

↓

Boot BIOS

↓

Spin Hard Drive

↓

Load Operating System

↓

Computer Booted Successfully
```

The client performs only one operation,

while the Facade executes all internal steps.

---

## BIOS Boot

During

```cpp
bios.boot(cpu, memory);
```

the BIOS internally performs

```
CPU Initialization

↓

Memory Self Test
```

The client does not know about these internal interactions.

---

# Complete Flow

```
Client

↓

startComputer()

↓

ComputerFacade

↓

PowerSupply

↓

CoolingSystem

↓

BIOS

↓

CPU

↓

Memory

↓

HardDrive

↓

OperatingSystem

↓

Computer Booted Successfully
```

---

# 9. Client Code

The client code is extremely simple.

```cpp
ComputerFacade computer;

computer.startComputer();
```

The lecture emphasizes that

the client does not interact with

- CPU
- Memory
- BIOS
- Hard Drive
- Operating System

directly.

Only one method is called.

```
startComputer()
```

---

# Output Flow

When

```cpp
startComputer()
```

is executed,

the output becomes

```
Starting Computer

↓

Power Supply

↓

Cooling System

↓

CPU Initialization

↓

Memory Self Test

↓

Hard Drive Spin Up

↓

Operating System Loading

↓

Computer Booted Successfully
```

All subsystem classes execute behind the scenes.

---

# Complete Execution Flow

```
Client

↓

ComputerFacade

↓

providePower()

↓

startFans()

↓

boot()

↓

initialize()

↓

selfTest()

↓

spinUp()

↓

load()

↓

Computer Booted Successfully
```

---

# 10. Principle of Least Knowledge

After explaining the Facade Pattern,

the lecture discusses the

## Principle of Least Knowledge

The rule is very simple.

> **Talk only to your immediate friends.**

The goal is to reduce interactions between classes.

Less interaction leads to

```
Loose Coupling
```

---

# Example

Suppose

```
A

↓

B

↓

C
```

Class A has a relationship with B.

Class B has a relationship with C.

The lecture explains that

A should communicate only with B.

It should not directly communicate with C.

---

## Incorrect Flow

```
A

↓

B

↓

Get C

↓

Call C
```

This creates unnecessary coupling.

---

## Correct Flow

```
A

↓

B

↓

B performs the work

↓

Return Result
```

Class A never interacts with C directly.

---

# Why?

The lecture explains that

the fewer classes know about each other,

the better the design becomes.

This keeps the application loosely coupled.

---

# Guidelines Discussed

The lecture explains four situations where method calls are acceptable.

---

## 1. Call Your Own Methods

A class can always call

its own methods.

---

## 2. Call Methods of Objects Passed as Parameters

If an object is received as a parameter,

its methods can be called.

---

## 3. Call Methods of Objects Created Inside the Method

If an object is created inside the current method,

its methods may be called.

---

## 4. Call Methods of Objects Having a Direct Relationship

If a class has a direct relationship with another object,

its methods can be called.

---

These rules help in following the

```
Principle of Least Knowledge
```

---

# 11. Standard Definition

The lecture gives the following definition.

> **Facade Pattern provides a simplified, unified interface to a set of complex subsystems. It hides the complexity of the system and exposes only what is necessary to the client.**

This matches the Computer Boot-up example.

The client sees only

```
startComputer()
```

while all subsystem interactions remain hidden.

---

# 12. Facade vs Adapter

The lecture discusses a common confusion between

- Facade Pattern
- Adapter Pattern

Both introduce an intermediate class,

but their intentions are different.

---

## Facade

Purpose

```
Hide Complexity
```

The subsystem already works.

The Facade only simplifies access to it.

---

## Adapter

Purpose

```
Connect Incompatible Interfaces
```

The Adapter enables communication between two components that cannot directly work together.

---

## Difference in Intent

Facade

```
Hide Complex Subsystem
```

Adapter

```
Convert One Interface Into Another
```

The lecture emphasizes that

understanding the **intent**

helps choose the correct pattern.

---

# 13. Real World Use Cases

The lecture discusses several examples.

---

## Computer Boot-up

The example used throughout the lecture.

One method

```
startComputer()
```

internally performs many operations.

---

## Game Engines

The lecture explains that

game engines may expose methods like

```
startGame()

loadGame()
```

Internally,

many operations occur.

Examples mentioned include

- loading game assets
- memory management
- physics engine initialization

The client calls only one method,

while the Facade coordinates the subsystem.

---

## Payment Systems

Another example discussed is

```
makePayment()
```

Internally,

multiple operations may occur.

Examples mentioned in the lecture include

- balance checking
- PIN verification
- fraud detection
- notification

The client simply performs

```
makePayment()
```

without interacting with individual subsystems.

---

# Key Observations

- The client communicates only with the Facade.
- The Facade coordinates all subsystem classes.
- The subsystem remains hidden from the client.
- The client does not know the execution sequence.
- The Facade simplifies a complex API into a single entry point.
- It also promotes loose coupling.

---

# Revision Sheet

## Pattern Type

Structural Design Pattern

---

## Participants

### Client

Uses only

```
ComputerFacade
```

---

### Facade

```
ComputerFacade
```

Method

```
startComputer()
```

---

### Subsystems

- PowerSupply
- CoolingSystem
- CPU
- Memory
- BIOS
- HardDrive
- OperatingSystem

---

## Main Flow

```
Client

↓

ComputerFacade

↓

Subsystem

↓

Computer Booted
```

---

## Principle of Least Knowledge

```
Talk Only To Your Immediate Friends
```

---

## Facade Intent

```
Hide Complexity
```

---

## Adapter Intent

```
Connect Different Interfaces
```

---

## Complete Boot Sequence

```
Power Supply

↓

Cooling System

↓

BIOS

↓

CPU Initialization

↓

Memory Self Test

↓

Hard Drive

↓

Operating System

↓

Computer Booted Successfully
```

---

# Final Summary

The Facade Pattern provides a **single, simplified interface** to a complex subsystem.

In the lecture's Computer Boot-up example:

- **ComputerFacade** acts as the Facade.
- **PowerSupply, CoolingSystem, CPU, Memory, BIOS, HardDrive, and OperatingSystem** form the subsystem.
- The client calls only `startComputer()`, while the Facade coordinates the complete boot process internally.

The lecture also explains the **Principle of Least Knowledge**, encouraging classes to communicate only with their immediate collaborators, and concludes by distinguishing **Facade** (hide complexity) from **Adapter** (connect incompatible interfaces) based on their intent.
