# Factory Design Pattern

> **Lecture Notes**\
> **Source:** Based only on the lecture explanation and the provided C++
> implementations.

---

# Table of Contents

1.  Introduction
2.  Why Factory Pattern?
3.  Types of Factory
4.  Simple Factory
5.  Factory Method
6.  Abstract Factory
7.  Real World Example
8.  Factory vs Strategy
9.  Revision Sheet

---

# 1. Introduction

Factory Design Pattern is used to **separate object creation logic from
business logic**.

Instead of creating objects directly using `new`, the client asks a
factory to create the required object.

    Client
       |
       v
    Factory
       |
       v
    Concrete Object

The client never needs to know **how** the object is created.

---

# 2. Why Factory Pattern?

Without Factory

```cpp
Burger* burger = new PremiumBurger();
```

The client knows:

- Which class to instantiate.
- When to instantiate it.
- How to instantiate it.

With Factory

```cpp
Burger* burger = burgerFactory->createBurger(type);
```

Now the client only asks for a burger.

The factory decides which object to create.

---

# 3. Types of Factory

According to the lecture there are three types.

Type Description

---

Simple Factory One concrete factory creates products
Factory Method Factory becomes abstract
Abstract Factory Factory creates multiple related product families

---

# 4. Simple Factory

## Idea

There is only **one factory**.

There is only **one product hierarchy**.

    Burger

    ├── BasicBurger
    ├── StandardBurger
    └── PremiumBurger

Every burger overrides

```cpp
prepare()
```

---

## BurgerFactory

The factory contains one method.

```cpp
Burger* createBurger(string& type)
```

Depending upon

- basic
- standard
- premium

it creates the respective burger.

---

## Execution Flow

    Client

    ↓

    BurgerFactory

    ↓

    createBurger("standard")

    ↓

    StandardBurger

    ↓

    prepare()

---

## Code Logic

```text
if(type=="basic")
      return BasicBurger

else if(type=="standard")
      return StandardBurger

else if(type=="premium")
      return PremiumBurger

else
      Invalid
```

---

## UML

                     Burger
                        ▲
          ----------------------------
          |            |             |
     BasicBurger StandardBurger PremiumBurger

                    ▲
                    |
              BurgerFactory

            createBurger(type)

---

## Definition

> A factory class that decides which concrete class to instantiate
> depending upon the given type.

---

## Key Takeaways

- One Factory
- One Product Family
- Client never creates objects directly.

---

# 5. Factory Method

Simple Factory had only one factory.

Now the factory also becomes abstract.

    BurgerFactory

            ▲

    -------------------------
    |                       |

    SinghBurger      KingBurger

---

## Product Hierarchy

Normal Burgers

- BasicBurger
- StandardBurger
- PremiumBurger

Wheat Burgers

- BasicWheatBurger
- StandardWheatBurger
- PremiumWheatBurger

---

## SinghBurger

Creates only

- BasicBurger
- StandardBurger
- PremiumBurger

---

## KingBurger

Creates only

- BasicWheatBurger
- StandardWheatBurger
- PremiumWheatBurger

---

## Client Flow

```cpp
BurgerFactory* factory =
new KingBurger();

Burger* burger =
factory->createBurger(type);

burger->prepare();
```

If

```cpp
new SinghBurger()
```

is used instead,

normal burgers are produced.

---

## UML

                     Burger
                        ▲
            -------------------------
            |                       |

     Normal Burgers          Wheat Burgers

    BurgerFactory (Abstract)

              ▲
        ------------------
        |                |

    SinghBurger    KingBurger

---

## Definition

> Define an interface for creating objects but allow subclasses to
> decide which class to instantiate.

---

## Key Takeaways

- Factory becomes abstract.
- Multiple concrete factories.
- Client decides factory, not concrete product.

---

# 6. Abstract Factory

Factory Method creates one product family.

Abstract Factory creates **multiple related product families**.

Products used in lecture:

- Burger
- Garlic Bread

---

## Product Families

Burger

    BasicBurger
    StandardBurger
    PremiumBurger

    BasicWheatBurger
    StandardWheatBurger
    PremiumWheatBurger

Garlic Bread

    BasicGarlicBread
    CheeseGarlicBread

    BasicWheatGarlicBread
    CheeseWheatGarlicBread

---

## MealFactory

Factory now has two methods.

```cpp
createBurger()

createGarlicBread()
```

---

## SinghBurger

Creates

- Normal Burgers
- Normal Garlic Bread

---

## KingBurger

Creates

- Wheat Burgers
- Wheat Garlic Bread

---

## Client Flow

    MealFactory

    ↓

    KingBurger

    ↓

    createBurger("basic")

    ↓

    BasicWheatBurger

    ↓

    createGarlicBread("cheese")

    ↓

    CheeseWheatGarlicBread

---

## UML

                  MealFactory

          createBurger()

          createGarlicBread()

                  ▲

    --------------------------------

    |                              |

    SinghBurger              KingBurger

    |                              |

    Normal Products        Wheat Products

---

## Definition

> Provides an interface for creating families of related objects without
> specifying their concrete classes.

---

## Key Takeaways

- Multiple product families.
- Same factory creates related products.

---

# 7. Real World Example

Notification System

    Notification

    ├── SMS
    ├── Email
    └── Push

NotificationFactory receives

    SMS
    EMAIL
    PUSH

and returns the appropriate notification object.

---

# 8. Factory vs Strategy

Factory Pattern Strategy Pattern

---

Object Creation Algorithm Selection
Separates object creation Separates algorithms
Creates new object Uses already created object

Thumb Rule from lecture:

- Need different algorithms → Strategy
- Need to separate object creation → Factory

---

# 9. Evolution

    Simple Factory

    ↓

    Factory Method

    ↓

    Abstract Factory

---

# Revision Sheet

## Simple Factory

- One Factory
- One Product Family

## Factory Method

- Abstract Factory
- Multiple Concrete Factories

## Abstract Factory

- Multiple Product Families
- Same Factory creates related objects

---

# Final Summary

Factory Pattern hides object creation from the client.

The lecture gradually evolves the design as follows:

\`\`\` Client creates object

↓

Simple Factory

↓

Factory Method

↓

Abstract Factory
