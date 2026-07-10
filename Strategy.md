# Strategy Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the robot example discussed in the video, along with the corresponding C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction to Design Patterns
2. Why Do We Need Design Patterns?
3. Core Idea Behind Design Patterns
4. Robot Simulation Problem
5. Initial Robot Design
6. Adding New Features
7. Problems with Inheritance
8. Increasing Inheritance Hierarchy
9. Problems Caused by Inheritance
10. Motivation for Strategy Pattern

---

# 1. Introduction to Design Patterns

Whenever we develop applications, many common design problems arise.

These problems are not unique.

Many developers have already faced them before and have already discovered good solutions for them.

Instead of solving these problems again from scratch, we reuse those proven solutions.

These reusable solutions are called **Design Patterns**.

---

# 2. Why Do We Need Design Patterns?

Applications never remain the same.

As time passes,

- new features are added,
- existing functionality changes,
- requirements evolve continuously.

A good application should be flexible enough so that integrating new features requires:

- minimum code changes
- minimum development time

This flexibility is achieved using

- Object Oriented Principles
- SOLID Principles
- Design Patterns

The goal is always to build applications that can evolve easily.

---

# 3. Core Idea Behind Design Patterns

Almost every design pattern follows one simple idea.

In every application there are two kinds of code.

## Static Part

The part that rarely changes.

## Dynamic Part

The part that changes frequently as new features are introduced.

The objective is:

> Separate the changing part from the non-changing part.

When the changing portion is isolated,

future modifications only affect that isolated code.

The remaining application stays unchanged.

---

# 4. Robot Simulation Problem

Suppose we are developing an application that simulates robots.

Different types of robots exist inside the application.

The first step is to create a base `Robot` class.

Initially, every robot has the following functionalities.

```
Robot

+ walk()

+ talk()

+ projection()
```

### Methods

## walk()

Represents walking behavior.

---

## talk()

Represents talking behavior.

---

## projection()

Represents how the robot looks.

Every robot looks different.

Therefore,

`projection()` is considered an abstract method that every child class implements according to its own appearance.

---

# 5. Initial Robot Design

Initially, only two robot types exist.

```
                 Robot
                    ▲
        -----------------------
        |                     |

 CompanionRobot        WorkerRobot
```

Both robots

- inherit `walk()`
- inherit `talk()`
- implement their own `projection()`

At this stage,

the design is simple and works correctly.

No issues are observed.

---

# 6. New Requirement

A new robot is introduced.

```
SparrowRobot
```

Unlike previous robots,

this robot can

- Walk
- Talk
- Fly

Since the existing Robot class does not contain flying functionality,

a new method is added.

```
fly()
```

Now SparrowRobot implements its own flying behavior.

---

# Multiple Flying Robots

Suppose more flying robots are introduced.

Example

```
SparrowRobot

CrowRobot

...
```

Every flying robot now implements

```
fly()
```

individually.

The implementation of `fly()` becomes identical across multiple classes.

This results in repeated code.

---

# 7. DRY Principle Violation

The repeated implementation of `fly()` violates the

## DRY Principle

**DRY = Don't Repeat Yourself**

Instead of writing identical code in multiple classes,

the same logic should exist only once and be reused.

---

# First Attempt to Solve the Problem

One possible solution is to move

```
fly()
```

into the parent `Robot` class.

The hierarchy becomes

```
Robot

walk()

talk()

fly()

projection()
```

---

## Problem

Now every robot inherits `fly()`.

This means

- CompanionRobot can fly.
- WorkerRobot can fly.

which is incorrect.

Only specific robots should have flying capability.

Therefore this solution does not work.

---

# 8. Increasing the Inheritance Hierarchy

To avoid giving flying capability to every robot,

the hierarchy is modified.

```
                    Robot
                      ▲

        ----------------------------

        |                          |

 NonFlyingRobot          FlyingRobot

                               ▲

                    ------------------

                    |                |

             SparrowRobot      CrowRobot
```

Now

`fly()`

exists inside `FlyingRobot`.

All flying robots inherit it.

This removes code duplication for robots that fly using the same mechanism.

---

# New Problem

Another robot is introduced.

```
JetRobot
```

This robot also flies,

but it flies using **jets**, not wings.

The existing implementation of `fly()` is different from what JetRobot requires.

A new implementation becomes necessary.

---

# Hierarchy Expands Again

To support another flying mechanism,

the hierarchy grows.

```
Robot

    ▲

FlyingRobot

      ▲

------------------------

|                      |

FlyWithWings      FlyWithJet

|                      |

Sparrow           JetRobot

Crow              JetRobot2
```

The hierarchy keeps expanding whenever a new flying behavior appears.

---

# 9. More Features Increase Complexity

The same issue appears for other behaviors.

Suppose some robots

- cannot walk,
- cannot talk,
- cannot fly.

Separate inheritance trees become necessary.

Eventually,

different combinations start appearing.

Examples

- Walkable
- NonWalkable
- Talkable
- NonTalkable
- Flyable
- NonFlyable

Each combination creates another inheritance branch.

The inheritance tree becomes increasingly complicated.

---

# Problems with Inheritance

Using inheritance in this manner leads to several issues.

## Code Reuse is Poor

Behavior gets duplicated.

---

## New Features Require Many Changes

Adding one feature forces modifications to the hierarchy.

---

## DRY Principle Breaks

Same implementation appears repeatedly.

---

## Inheritance Tree Becomes Complex

More behaviors

↓

More subclasses

↓

More combinations

↓

More complexity

---

## Design Stops Being Flexible

Every new feature requires changing existing classes.

---

# Important Observation

Increasing inheritance is **not** the solution to inheritance problems.

Adding more and more subclasses only makes the hierarchy more difficult to maintain.

---

# 10. Motivation for Strategy Pattern

The Robot example exposes one important observation.

Some parts of the Robot class keep changing repeatedly.

These are

- Walking
- Talking
- Flying

However,

one part remains specific to every robot.

```
projection()
```

This observation leads to the main idea behind the Strategy Design Pattern.

> The behaviors that change repeatedly should be separated from the class instead of being managed using an ever-growing inheritance hierarchy.

The next part introduces the Strategy Design Pattern and shows how these changing behaviors are extracted into separate strategy classes.

---

# Part 1 Summary

- Applications continuously evolve.
- Design Patterns solve recurring design problems.
- The Robot example initially works using inheritance.
- Adding flying robots introduces duplicated code.
- Moving `fly()` into the parent class gives every robot flying capability.
- Creating more inheritance levels makes the hierarchy increasingly complex.
- Walking, Talking and Flying are the parts that keep changing.
- This motivates the need for the **Strategy Design Pattern**.

---

**Next:** Part 2 — Strategy Pattern Solution (Extracting Behaviors, Strategy Interfaces, Composition, and Robot Redesign).

# Strategy Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Strategy Pattern Definition
2. Revisiting the Robot Problem
3. Identifying the Changing Behaviors
4. Extracting Behaviors
5. Creating Strategy Interfaces
6. Concrete Strategies
7. Robot Class Redesign
8. Client and Strategy
9. Runtime Behavior
10. Favor Composition over Inheritance

---

# 1. Strategy Pattern Definition

The lecture defines the Strategy Pattern as:

> **"Define a family of algorithms, put them into separate classes so that they can be changed at runtime."**

At first, this definition may not be very intuitive.

The Robot example is used to understand what this definition actually means.

---

# 2. Revisiting the Robot Problem

Initially, the Robot class looked like this.

```
Robot

+ walk()

+ talk()

+ fly()

+ projection()
```

From Part 1 we already know that

- walking behavior changes
- talking behavior changes
- flying behavior changes

Only

```
projection()
```

remains unique to every robot.

---

# Important Observation

Some parts of the Robot class change repeatedly.

Some parts never change.

According to the lecture,

the changing behaviors are

- Walk
- Talk
- Fly

The non-changing part is

- Projection

Therefore,

the changing behaviors should be removed from the Robot class.

---

# 3. Identifying the Changing Behaviors

The lecture identifies

```
walk()

talk()

fly()
```

as different **algorithms**.

Here,

an algorithm simply means a behavior or functionality.

Different robots may

- walk differently,
- talk differently,
- fly differently.

Therefore,

these behaviors should not remain inside Robot.

---

# 4. Extracting Behaviors

The first step is to remove these behaviors from Robot.

Instead of

```
Robot

walk()

talk()

fly()

projection()
```

the Robot class becomes

```
Robot

projection()
```

Walking,

Talking,

and

Flying

will now exist outside Robot.

---

# 5. Creating Strategy Interfaces

Each changing behavior gets its own interface (abstract class in C++).

## Walk Strategy

```
WalkableRobot

+ walk()
```

---

## Talk Strategy

```
TalkableRobot

+ talk()
```

---

## Fly Strategy

```
FlyableRobot

+ fly()
```

These interfaces contain only the behavior declaration.

No implementation is provided.

---

# Why Interfaces?

Different robots can perform the same action differently.

For example,

walking can have multiple implementations.

Instead of hardcoding them inside Robot,

Robot will use whichever implementation is supplied.

---

# 6. Concrete Strategies

Each interface has concrete implementations.

---

## Walk Strategies

```
WalkableRobot

        ▲

------------------------

|                      |

NormalWalk         NoWalk
```

### NormalWalk

Implements normal walking.

### NoWalk

Represents robots that cannot walk.

The lecture specifically points out that

> **Not being able to walk is also a valid walking behavior.**

---

## Talk Strategies

```
TalkableRobot

        ▲

------------------------

|                      |

NormalTalk       NoTalk
```

### NormalTalk

Robot talks normally.

### NoTalk

Robot cannot talk.

The lecture again treats

```
NoTalk
```

as another valid talking behavior.

---

## Fly Strategies

```
FlyableRobot

        ▲

------------------------

|                      |

NormalFly       NoFly
```

### NormalFly

Robot flies normally.

### NoFly

Robot cannot fly.

Again,

not flying is considered a separate behavior.

---

# Complete Strategy Hierarchy

```
WalkableRobot

|

+-- NormalWalk

|

+-- NoWalk



TalkableRobot

|

+-- NormalTalk

|

+-- NoTalk



FlyableRobot

|

+-- NormalFly

|

+-- NoFly
```

Instead of creating more robot subclasses,

only new strategy classes are created whenever a new behavior is required.

---

# 7. Robot Class Redesign

The Robot class no longer contains

- walk()
- talk()
- fly()

implementations.

Instead,

it stores references to the strategy interfaces.

```
Robot

----------------------------

WalkableRobot*

TalkableRobot*

FlyableRobot*

----------------------------

projection()
```

This relationship is called

## Composition

Robot **has**

- a Walk Strategy
- a Talk Strategy
- a Fly Strategy

instead of inheriting all of them.

---

# Constructor

The constructor receives

- Walk Strategy
- Talk Strategy
- Fly Strategy

and stores their references.

```
Robot

↓

Constructor

↓

Walk Strategy

Talk Strategy

Fly Strategy
```

Now the Robot does not decide

how to walk,

how to talk,

or

how to fly.

Those decisions are made by the strategies.

---

# 8. Client and Strategy

The lecture introduces two important terms.

---

## Client

The Robot class becomes the

**Client**.

The Client simply uses the strategies.

---

## Strategy

Each behavior hierarchy becomes a

**Strategy**.

Examples

Walking Strategy

Talking Strategy

Flying Strategy

Each strategy contains multiple implementations.

---

# 9. Runtime Behavior

When creating a robot,

any strategy implementation can be supplied.

Example

```
CompanionRobot

↓

NormalWalk

NormalTalk

NoFly
```

Another robot may use

```
WorkerRobot

↓

NoWalk

NoTalk

NormalFly
```

No code inside Robot changes.

Only the supplied strategy objects change.

This is what the lecture means by

> **"They can be changed at runtime."**

---

# Dynamic Selection

Suppose

```
NormalWalk
```

is replaced with

```
NoWalk
```

The Robot immediately changes its walking behavior.

Nothing inside Robot is modified.

Only the supplied strategy object changes.

---

# Advantages of This Design

Compared to inheritance,

this approach

- removes repeated code,
- avoids complex inheritance hierarchies,
- allows different combinations of behaviors,
- supports adding new behaviors without modifying Robot.

---

# 10. Favor Composition over Inheritance

The lecture concludes this section with the central idea of the Strategy Pattern.

Instead of using inheritance to keep extending behavior,

use composition.

Robot does not inherit walking,

talking,

or flying.

Robot simply **has** these behaviors.

```
Robot

HAS A

Walk Strategy

Talk Strategy

Fly Strategy
```

This removes the need for the large inheritance hierarchy created in Part 1.

---

# Part 2 Summary

- Strategy Pattern extracts changing behaviors into separate classes.
- Walk, Talk and Fly become independent strategies.
- Robot only keeps the behavior that does not change.
- Robot stores references to strategy interfaces.
- Different strategy implementations can be supplied dynamically.
- Robot becomes a client that uses strategies.
- Composition replaces inheritance.

---

**Next:** Part 3 — Complete C++ Code Walkthrough (Strategy Classes, Robot Class, CompanionRobot, WorkerRobot and Main Function).

# Strategy Design Pattern - Part 3

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Code Structure
2. Strategy Interfaces
3. Concrete Strategies
4. Robot Class
5. Concrete Robots
6. Main Function
7. Complete Execution Flow
8. Runtime Behavior
9. Removing Inheritance Completely
10. Part Summary

---

# 1. Code Structure

The code follows exactly the design discussed in the previous section.

Instead of putting every behavior inside the `Robot` class,

the behaviors are separated into different strategy classes.

The complete structure is

```
Walk Strategy
        │
        ▼

Talk Strategy
        │
        ▼

Fly Strategy
        │
        ▼

Robot
        │
        ▼

CompanionRobot

WorkerRobot
```

---

# 2. Strategy Interfaces

The first step in the implementation is creating the strategy interfaces.

There are three interfaces.

```
WalkableRobot

TalkableRobot

FlyableRobot
```

Each interface contains only one abstract function.

---

## WalkableRobot

```
WalkableRobot

+ walk()
```

Purpose

Defines the walking behavior.

---

## TalkableRobot

```
TalkableRobot

+ talk()
```

Purpose

Defines the talking behavior.

---

## FlyableRobot

```
FlyableRobot

+ fly()
```

Purpose

Defines the flying behavior.

---

These are abstract classes.

They only declare the functions.

No implementation is provided.

---

# 3. Concrete Strategies

Every strategy interface has concrete implementations.

---

## Walk Strategy

```
WalkableRobot

        ▲

----------------------

|                    |

NormalWalk      NoWalk
```

### NormalWalk

```
Walking normally...
```

---

### NoWalk

```
Cannot walk.
```

The lecture specifically considers

```
Cannot walk
```

as another valid walking behavior.

---

## Talk Strategy

```
TalkableRobot

        ▲

----------------------

|                    |

NormalTalk      NoTalk
```

### NormalTalk

```
Talking normally...
```

---

### NoTalk

```
Cannot talk.
```

---

## Fly Strategy

```
FlyableRobot

        ▲

----------------------

|                    |

NormalFly      NoFly
```

### NormalFly

```
Flying normally...
```

---

### NoFly

```
Cannot fly.
```

---

# Strategy Hierarchy

```
WalkableRobot

|

+-- NormalWalk

|

+-- NoWalk



TalkableRobot

|

+-- NormalTalk

|

+-- NoTalk



FlyableRobot

|

+-- NormalFly

|

+-- NoFly
```

---

# 4. Robot Class

The Robot class no longer implements

- walking
- talking
- flying

Instead,

it stores references to all three strategy interfaces.

```
Robot

----------------------------

WalkableRobot*

TalkableRobot*

FlyableRobot*

----------------------------

projection()
```

---

## Constructor

The constructor receives

```
Walk Strategy

Talk Strategy

Fly Strategy
```

and stores them.

```
Robot

↓

Constructor

↓

Walk Strategy

Talk Strategy

Fly Strategy
```

The Robot now knows

which behavior it should use,

but it does not implement those behaviors itself.

---

## walk()

The Robot's

```
walk()
```

method does not perform walking.

Instead,

it delegates the request.

```
walk()

↓

walkBehavior->walk()
```

Depending on the strategy passed,

either

```
NormalWalk
```

or

```
NoWalk
```

gets executed.

---

## talk()

Similarly,

```
talk()

↓

talkBehavior->talk()
```

The Robot simply forwards the request.

---

## fly()

Likewise,

```
fly()

↓

flyBehavior->fly()
```

Again,

Robot itself performs no flying logic.

---

## projection()

Unlike the other three behaviors,

projection remains an abstract method.

Every concrete robot defines its own projection.

---

# Robot becomes a "Dumb Object"

The lecture describes Robot as becoming a

> **Dumb Object**

Reason:

Robot itself performs no work.

It simply forwards every request to the corresponding strategy.

```
walk()

↓

Walk Strategy



talk()

↓

Talk Strategy



fly()

↓

Fly Strategy
```

Robot acts only as a delegator.

---

# 5. Concrete Robots

The implementation contains two robot types.

```
Robot

        ▲

------------------------

|                      |

CompanionRobot    WorkerRobot
```

---

## CompanionRobot

Implements only

```
projection()
```

All remaining behaviors come from the supplied strategies.

---

## WorkerRobot

Similarly,

it also implements only

```
projection()
```

Walking,

talking,

and flying

still come from the strategies.

---

# 6. Main Function

Two robots are created.

---

## Robot 1

```
CompanionRobot

↓

NormalWalk

↓

NormalTalk

↓

NoFly
```

Construction

```
Robot

↓

new CompanionRobot(

NormalWalk,

NormalTalk,

NoFly
)
```

Behavior

```
walk()

↓

Walking normally


talk()

↓

Talking normally


fly()

↓

Cannot fly
```

---

## Robot 2

```
WorkerRobot

↓

NoWalk

↓

NoTalk

↓

NormalFly
```

Construction

```
Robot

↓

new WorkerRobot(

NoWalk,

NoTalk,

NormalFly
)
```

Behavior

```
walk()

↓

Cannot walk


talk()

↓

Cannot talk


fly()

↓

Flying normally
```

---

# 7. Complete Execution Flow

## Companion Robot

```
Create Robot

↓

Store

NormalWalk

NormalTalk

NoFly

↓

walk()

↓

NormalWalk::walk()

↓

Talking

↓

NormalTalk::talk()

↓

fly()

↓

NoFly::fly()

↓

projection()
```

---

## Worker Robot

```
Create Robot

↓

Store

NoWalk

NoTalk

NormalFly

↓

walk()

↓

NoWalk::walk()

↓

talk()

↓

NoTalk::talk()

↓

fly()

↓

NormalFly::fly()

↓

projection()
```

---

# 8. Runtime Behavior

One important observation from the lecture is that

the Robot class never changes.

Only the supplied strategies change.

For example,

```
NormalWalk
```

can immediately be replaced by

```
NoWalk
```

without modifying the Robot class.

Similarly,

```
NoFly
```

can be replaced by

```
NormalFly
```

Again,

the Robot implementation remains unchanged.

Only the strategy object changes.

---

# 9. Removing Inheritance Completely

Towards the end of the lecture,

one further improvement is discussed.

Currently,

inheritance still exists because

```
projection()
```

is abstract.

The lecture suggests that

projection itself can also become a strategy.

Example

```
Projectable
```

```
Projectable

↓

projection()
```

Different projection implementations can then be created.

```
Companion Projection

Worker Projection

...
```

Robot would simply store another strategy reference.

```
Projectable*
```

and delegate

```
projection()

↓

projectable->projection()
```

At this point,

even the concrete robot subclasses become unnecessary.

The Robot class can directly use

- Walk Strategy
- Talk Strategy
- Fly Strategy
- Projection Strategy

through composition.

---

# Part 3 Summary

- Strategy interfaces are implemented as abstract classes.
- Every interface has multiple concrete implementations.
- Robot stores references to the strategies.
- Robot delegates all behavior.
- CompanionRobot and WorkerRobot only implement projection.
- Different behaviors are selected by passing different strategy objects.
- The lecture finally shows that even projection can become another strategy, removing the remaining inheritance.

---

**Next:** Part 4 — Standard UML, Real-world Examples (Payment & Sorting), Conclusions, and Revision Notes.

# Strategy Design Pattern - Part 4

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Standard UML
2. Understanding the UML
3. Real World Example – Payment System
4. Real World Example – Sorting
5. Why Strategy Pattern is Useful
6. Final Conclusion
7. Complete Revision Sheet

---

# 1. Standard UML

The lecture presents the following standard UML for the Strategy Design Pattern.

```
                    +----------------------+
                    |        Client        |
                    +----------------------+
                    | - strategy           |
                    +----------------------+
                    | + execute()          |
                    +----------+-----------+
                               |
                               | HAS-A
                               |
                               v
                    +----------------------+
                    |      Strategy        |
                    +----------------------+
                    | + run()              |
                    +----------^-----------+
                               |
          ---------------------------------------------
          |                    |                     |
          |                    |                     |
          v                    v                     v
+------------------+ +------------------+ +------------------+
| ConcreteStrategy1| | ConcreteStrategy2| | ConcreteStrategy3|
+------------------+ +------------------+ +------------------+
| + run()          | | + run()          | | + run()          |
+------------------+ +------------------+ +------------------+
```

---

# 2. Understanding the UML

## Client

The Client is the main class that uses the strategies.

In the Robot example,

```
Robot
```

is the Client.

The Client does not implement the algorithm.

Instead,

it simply stores a reference to the Strategy.

---

## Strategy

The Strategy is an interface (or abstract class).

It declares one common method.

Example

```
run()
```

In the Robot example,

the Strategy interfaces are

- WalkableRobot
- TalkableRobot
- FlyableRobot

---

## Concrete Strategies

The actual implementation is provided by concrete strategy classes.

Examples from the Robot implementation:

### Walking

```
NormalWalk

NoWalk
```

---

### Talking

```
NormalTalk

NoTalk
```

---

### Flying

```
NormalFly

NoFly
```

Each concrete strategy overrides the method defined in the interface.

---

## Client Execution

The lecture explains the Client's execution flow as

```
Client

↓

execute()

↓

strategy.run()

↓

Concrete Strategy executes
```

The Client never knows which concrete implementation is being used.

It simply calls the strategy.

---

# 3. Real World Example – Payment System

The lecture uses a Payment System as the first real-world example.

```
PaymentSystem
```

contains a method

```
payNow()
```

However,

payments can happen using different methods.

Examples

```
UPI

Credit Card

Debit Card

Net Banking
```

Each payment method represents a different strategy.

---

## Strategy Hierarchy

```
Payment Strategy

        ▲

-----------------------------------

|              |                 |

UPI        Credit/Debit      Net Banking
```

Whenever

```
payNow()
```

is called,

the strategy that was supplied is executed.

The Payment System itself never changes.

Only the payment strategy changes.

---

# Execution Flow

```
Payment System

↓

payNow()

↓

Payment Strategy

↓

UPI

or

Credit Card

or

Net Banking
```

Depending upon the supplied strategy,

the corresponding payment method is executed.

---

# 4. Real World Example – Sorting

The lecture gives another example using sorting algorithms.

Suppose there is a

```
Sorting
```

class.

It contains

```
sort()
```

Different sorting algorithms can be used.

Examples

```
Quick Sort

Merge Sort

Insertion Sort
```

Each sorting algorithm becomes a Strategy.

---

## Strategy Hierarchy

```
Sorting Strategy

        ▲

----------------------------------------

|               |                      |

QuickSort    MergeSort        InsertionSort
```

The lecture further explains that even these strategies can have multiple implementations.

For example,

```
QuickSort

        ▲

-------------------------

|                       |

Normal QuickSort

Randomized QuickSort
```

Similarly,

Merge Sort can also have different implementations.

---

## Execution Flow

```
Sorting

↓

sort()

↓

Sorting Strategy

↓

Concrete Sorting Strategy
```

The Sorting class never changes.

Only the supplied strategy changes.

---

# 5. Why Strategy Pattern is Useful

According to the lecture,

Strategy Pattern is one of the most useful Design Patterns.

Whenever an application has

- multiple algorithms,
- multiple behaviors,
- changing functionality,

Strategy Pattern can be applied.

Instead of modifying the main class,

new behavior is introduced by creating another strategy.

The existing code remains unchanged.

---

# Main Idea of Strategy Pattern

The lecture concludes the pattern with one important statement.

```
Favor Composition

over

Inheritance
```

Instead of extending classes repeatedly,

compose objects using different strategies.

---

# 6. Final Conclusion

The Robot example demonstrated that

inheritance keeps growing as new behaviors are introduced.

This results in

- duplicated code,
- complex hierarchy,
- difficult maintenance.

Strategy Pattern solves this by

- separating behaviors,
- creating strategy interfaces,
- creating concrete strategies,
- composing them inside the Client.

The Client simply delegates the work.

---

# Complete Flow of the Robot Example

```
Robot

↓

Remove changing behaviors

↓

Create Strategy Interfaces

↓

Create Concrete Strategies

↓

Use Composition

↓

Delegate Requests

↓

Behavior selected at runtime
```

---

# Complete Revision Sheet

## Strategy Pattern Definition

> Define a family of algorithms, put them into separate classes so that they can be changed at runtime.

---

## Main Components

```
Client

↓

Strategy

↓

Concrete Strategies
```

---

## Robot Example

Changing Behaviors

- Walk
- Talk
- Fly

Non-changing Behavior

- Projection

---

## Strategy Interfaces

- WalkableRobot
- TalkableRobot
- FlyableRobot

---

## Concrete Strategies

### Walk

- NormalWalk
- NoWalk

### Talk

- NormalTalk
- NoTalk

### Fly

- NormalFly
- NoFly

---

## Client

```
Robot
```

Robot stores

- Walk Strategy
- Talk Strategy
- Fly Strategy

and delegates every request.

---

## Real World Examples

### Payment System

Strategies

- UPI
- Credit Card
- Debit Card
- Net Banking

---

### Sorting

Strategies

- Quick Sort
- Merge Sort
- Insertion Sort

---

## Final Takeaway

The lecture concludes Strategy Pattern with the following idea:

```
Changing Behavior

↓

Move into Strategy

↓

Use Composition

↓

Delegate Work

↓

Avoid Large Inheritance Hierarchies
```

---

# End of Strategy Design Pattern

This completes the Strategy Design Pattern lecture exactly in the order presented:

1. Introduction to Design Patterns
2. Robot Problem
3. Problems with Inheritance
4. Strategy Pattern Definition
5. Strategy Interfaces
6. Concrete Strategies
7. Composition
8. Complete C++ Implementation
9. Standard UML
10. Payment System Example
11. Sorting Example
12. Conclusion
