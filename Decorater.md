# Decorator Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the Mario C++ implementation provided. No external concepts have been added.

---

# Table of Contents

1. Introduction
2. Why Decorator Pattern?
3. Mario Example
4. Problems with Inheritance
5. Class Explosion
6. Decorator Pattern Idea
7. Component and Decorator
8. UML
9. Beginning of C++ Implementation
10. Part Summary

---

# 1. Introduction

Decorator Pattern is a **Structural Design Pattern**.

It is used when we want to **add new functionality to an object without modifying its original class**.

Instead of changing the original object,

we wrap it inside another object that provides additional functionality.

The original object remains unchanged.

---

# 2. Why Decorator Pattern?

The lecture explains this pattern using the **Mario game**.

Initially,

Mario has no special powers.

```
Mario
```

Abilities

```
Run

Jump
```

This is the basic character.

---

# New Requirement

Mario can now collect power-ups.

Examples shown in the lecture are

- Height Up
- Gun
- Star Power

Whenever Mario collects a power-up,

his abilities increase.

For example,

```
Mario

↓

Height Up

↓

Mario with Height Up
```

---

Another power-up can be collected later.

```
Mario

↓

Height Up

↓

Gun

↓

Mario with Height Up + Gun
```

Later,

another one can be added.

```
Mario

↓

Height Up

↓

Gun

↓

Star Power
```

Each new power-up should extend Mario's abilities.

---

# 3. Naive Solution Using Inheritance

One possible approach is creating subclasses.

```
Mario

▲

|

SuperMario
```

Suppose

```
SuperMario
```

represents

Height Up.

Now another requirement appears.

Mario should have a Gun.

A new subclass is required.

```
Mario

▲

|

GunMario
```

Now another requirement appears.

Mario should have both

- Height Up
- Gun

Another subclass becomes necessary.

```
HeightGunMario
```

---

# More Power-ups

Now suppose

Star Power

is introduced.

Again,

new subclasses are required.

Examples

```
StarMario

GunStarMario

HeightStarMario

HeightGunStarMario
```

Every combination becomes another class.

---

# 4. Class Explosion

Suppose there are only

3 power-ups.

Possible combinations already start increasing rapidly.

```
Mario

Mario + Height

Mario + Gun

Mario + Star

Mario + Height + Gun

Mario + Height + Star

Mario + Gun + Star

Mario + Height + Gun + Star
```

Every combination requires another subclass.

---

Now imagine

10 different power-ups.

The number of subclasses becomes extremely large.

This is known as

## Class Explosion

Inheritance no longer becomes practical.

---

# Problems with Inheritance

The lecture highlights several issues.

## Too Many Classes

Every new combination becomes another subclass.

---

## Difficult Maintenance

Whenever another power-up is introduced,

multiple new classes have to be created.

---

## Poor Scalability

As requirements grow,

the inheritance hierarchy keeps increasing.

---

## Rigid Design

Changing combinations becomes difficult.

---

# Observation

The problem is

not Mario.

The problem is

the **additional abilities**.

Mario always remains Mario.

Only the additional features change.

This observation motivates the Decorator Pattern.

---

# 5. Decorator Pattern Idea

Instead of creating subclasses for every combination,

keep

```
Mario
```

unchanged.

Whenever a new ability is required,

wrap Mario inside another object.

```
Mario

↓

Height Up Decorator
```

Now Mario behaves as

```
Mario with Height Up
```

Without changing the Mario class.

---

Another ability can again wrap the previous object.

```
Mario

↓

Height Up

↓

Gun
```

Now the resulting object behaves as

```
Mario

+

Height Up

+

Gun
```

Again,

no subclass is created.

---

Another decorator can again wrap it.

```
Mario

↓

Height Up

↓

Gun

↓

Star
```

Now all three abilities exist together.

Each decorator simply adds one more feature.

---

# Decorator Chain

```
Star Decorator

        │

        ▼

Gun Decorator

        │

        ▼

Height Decorator

        │

        ▼

Mario
```

Every decorator wraps the previous object.

---

# Important Observation

Each decorator

adds functionality,

but never changes the existing object.

Mario remains unchanged throughout the process.

---

# 6. Component and Decorator

The lecture now introduces two important terms.

---

## Component

The original object.

In this example,

```
Mario
```

is the Component.

Every decorator ultimately wraps a Component.

---

## Decorator

A Decorator is another object

that wraps an existing Component

and adds one more responsibility.

Examples

```
Height Up

Gun

Star Power
```

Each one is a Decorator.

---

# 7. "Is-A" and "Has-A" Relationship

This is the most important idea discussed in the lecture.

A Decorator has two relationships.

---

## Is-A Relationship

Decorator

**is a**

Character.

```
Decorator

↓

Character
```

Therefore,

Decorator can be used anywhere a Character is expected.

---

## Has-A Relationship

Decorator also

**has a**

Character.

```
Decorator

↓

Character*
```

This allows one decorator to wrap another.

---

Together,

the Decorator

```
IS-A Character

AND

HAS-A Character
```

This dual relationship makes the pattern work.

---

# UML

```
                 Character
                      ▲
                      │
                  Mario

                      ▲
                      │

            CharacterDecorator

           ---------------------

           Character* character

           ---------------------

                 ▲

     -----------------------------

     │            │             │

 HeightUp     GunPowerUp   StarPowerUp
```

Notice that

CharacterDecorator

- inherits Character
- stores a Character pointer

This gives both

"is-a"

and

"has-a"

relationships simultaneously.

---

# 8. Beginning of the C++ Implementation

The implementation starts by creating the Component interface.

```cpp
class Character {
public:
    virtual string getAbilities() const = 0;
};
```

Every Character must provide

```
getAbilities()
```

---

## Concrete Component

The basic implementation is

```cpp
Mario
```

```cpp
string getAbilities()
{
    return "Mario";
}
```

Initially,

Mario has no extra powers.

The output is simply

```
Mario
```

---

## CharacterDecorator

The next class introduced is

```cpp
CharacterDecorator
```

Unlike Mario,

this class

inherits

```
Character
```

and also stores

```cpp
Character*
```

This implements the

- is-a
- has-a

relationships discussed earlier.

The actual power-ups will inherit from this class.

---

# Part 1 Summary

- Decorator Pattern is a **Structural Design Pattern**.
- Mario gains new abilities using power-ups.
- Creating subclasses for every power-up combination leads to **Class Explosion**.
- Instead of inheritance, each power-up wraps the previous object.
- Mario itself never changes.
- The Decorator has both an **is-a** and **has-a** relationship with the Component.
- `Character` acts as the Component interface.
- `Mario` is the Concrete Component.
- `CharacterDecorator` is the base Decorator that wraps another `Character`.

---

**Next:** Part 2 covers the complete code walkthrough (`HeightUp`, `GunPowerUp`, `StarPowerUp`, `main()`), execution flow, decorator chaining, and the final revision sheet.

# Decorator Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Mario C++ implementation.

---

# 9. Concrete Decorators

After creating the `CharacterDecorator` base class, the lecture implements different power-ups as **Concrete Decorators**.

Each power-up

- inherits from `CharacterDecorator`
- wraps another `Character`
- adds one additional ability

The wrapped object is never modified.

---

## HeightUp Decorator

```cpp
class HeightUp : public CharacterDecorator {
public:
    HeightUp(Character* c) : CharacterDecorator(c) {}

    string getAbilities() const override {
        return character->getAbilities() + " with HeightUp";
    }
};
```

### Purpose

Adds the **Height Up** ability to the existing character.

Instead of returning

```
Mario
```

it returns

```
Mario with HeightUp
```

---

### Flow

```
Mario

↓

HeightUp

↓

Mario with HeightUp
```

The decorator first asks the wrapped object for its abilities.

Then it appends

```
with HeightUp
```

to the result.

---

## GunPowerUp Decorator

```cpp
class GunPowerUp : public CharacterDecorator {
public:
    GunPowerUp(Character* c) : CharacterDecorator(c) {}

    string getAbilities() const override {
        return character->getAbilities() + " with Gun";
    }
};
```

### Purpose

Adds the **Gun** ability.

If Mario already has HeightUp,

then GunPowerUp wraps that object.

---

### Flow

```
Mario

↓

HeightUp

↓

GunPowerUp

↓

Mario with HeightUp with Gun
```

Again,

the previous object is not modified.

A new decorator simply wraps it.

---

## StarPowerUp Decorator

```cpp
class StarPowerUp : public CharacterDecorator {
public:
    StarPowerUp(Character* c)
        : CharacterDecorator(c) {}

    string getAbilities() const override {
        return character->getAbilities()
             + " with Star Power (Limited Time)";
    }
};
```

### Purpose

Adds **Star Power**.

The lecture points out that this power is **temporary**.

Therefore,

the returned string becomes

```
Mario

with HeightUp

with Gun

with Star Power
```

---

## Destructor

The implementation also contains

```cpp
~StarPowerUp()
```

which prints

```
Destroying StarPowerUp Decorator
```

This simply shows when the outermost decorator is destroyed.

---

# Complete Decorator Chain

After applying all three decorators,

the object structure becomes

```
StarPowerUp

        │

        ▼

GunPowerUp

        │

        ▼

HeightUp

        │

        ▼

Mario
```

Every decorator wraps the previous object.

---

# 10. Main Function

The lecture demonstrates the pattern by decorating Mario step by step.

---

## Step 1

Create the basic character.

```cpp
Character* mario = new Mario();
```

Output

```
Mario
```

Current object

```
Mario
```

---

## Step 2

Apply HeightUp.

```cpp
mario = new HeightUp(mario);
```

Current object

```
HeightUp

↓

Mario
```

Output

```
Mario with HeightUp
```

---

## Step 3

Apply GunPowerUp.

```cpp
mario = new GunPowerUp(mario);
```

Current object

```
GunPowerUp

↓

HeightUp

↓

Mario
```

Output

```
Mario with HeightUp with Gun
```

Notice that

GunPowerUp wraps

HeightUp,

which itself wraps

Mario.

---

## Step 4

Apply StarPowerUp.

```cpp
mario = new StarPowerUp(mario);
```

Current object

```
StarPowerUp

↓

GunPowerUp

↓

HeightUp

↓

Mario
```

Output

```
Mario

with HeightUp

with Gun

with Star Power (Limited Time)
```

---

# 11. How getAbilities() Works

Suppose we call

```cpp
mario->getAbilities()
```

after all decorators have been applied.

Execution begins from the outermost decorator.

```
StarPowerUp

↓

GunPowerUp

↓

HeightUp

↓

Mario
```

---

## Step-by-Step Execution

### Mario

Returns

```
Mario
```

---

### HeightUp

Receives

```
Mario
```

Returns

```
Mario with HeightUp
```

---

### GunPowerUp

Receives

```
Mario with HeightUp
```

Returns

```
Mario with HeightUp with Gun
```

---

### StarPowerUp

Receives

```
Mario with HeightUp with Gun
```

Returns

```
Mario with HeightUp with Gun with Star Power
```

This recursive delegation is the core idea of the Decorator Pattern.

Each decorator

- calls the wrapped object's method
- adds its own functionality
- returns the updated result

---

# Complete Execution Flow

```
getAbilities()

↓

StarPowerUp

↓

GunPowerUp

↓

HeightUp

↓

Mario

↓

Return "Mario"

↓

Append HeightUp

↓

Append Gun

↓

Append Star Power

↓

Final Output
```

---

# Object Structure

```
Character

        ▲

        │

      Mario

        ▲

        │

HeightUp Decorator

        ▲

        │

GunPowerUp Decorator

        ▲

        │

StarPowerUp Decorator
```

Every object still behaves like a `Character`.

Therefore,

the client always works with

```cpp
Character*
```

without knowing how many decorators are attached.

---

# Key Observations

- The original `Mario` class is never modified.
- Every power-up adds one new responsibility.
- Decorators can be combined in any order.
- New decorators can be added without changing existing classes.
- The client always interacts with the `Character` interface.

---

# Advantages (As Explained in the Lecture)

- Add functionality dynamically.
- Avoid creating subclasses for every combination.
- Reuse existing decorators.
- Keep the original class unchanged.
- Multiple decorators can be stacked together.

---

# Complete Flow of Decorator Pattern

```
Create Mario

↓

Wrap using HeightUp

↓

Wrap using GunPowerUp

↓

Wrap using StarPowerUp

↓

Call getAbilities()

↓

Request reaches Mario

↓

Each decorator adds its own ability

↓

Final decorated result returned
```

---

# Revision Sheet

## Pattern Type

Structural Design Pattern

---

## Component

```text
Character
```

---

## Concrete Component

```text
Mario
```

---

## Base Decorator

```text
CharacterDecorator
```

---

## Concrete Decorators

- HeightUp
- GunPowerUp
- StarPowerUp

---

## Important Relationship

Decorator

- **is-a** Character
- **has-a** Character

---

## Main Idea

Instead of

```
Mario
↓

SuperMario
↓

GunMario
↓

HeightGunMario
```

use

```
Mario

↓

HeightUp

↓

GunPowerUp

↓

StarPowerUp
```

---

## Complete Sequence

```
Mario

↓

HeightUp

↓

GunPowerUp

↓

StarPowerUp

↓

getAbilities()

↓

Final Decorated Output
```

---

# Final Summary

The Decorator Pattern allows new functionality to be added to an object **without modifying its original class**.

In the lecture's Mario example:

- `Character` is the Component.
- `Mario` is the Concrete Component.
- `CharacterDecorator` acts as the base Decorator.
- `HeightUp`, `GunPowerUp`, and `StarPowerUp` are Concrete Decorators.

Each decorator wraps an existing `Character`, delegates the call to the wrapped object, adds its own behavior, and returns the enhanced result. This avoids the class explosion that would occur if inheritance were used to represent every possible combination of Mario's power-ups.
