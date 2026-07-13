# Chain of Responsibility Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided ATM Cash Dispenser C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction
2. Motivation
3. ATM Example
4. Flow of Request
5. UML
6. Code Walkthrough (MoneyHandler)
7. ThousandHandler Logic
8. Remaining Handlers
9. Client Setup
10. Part Summary

---

# 1. Introduction

Chain of Responsibility is a **Behavioral Design Pattern**.

The lecture explains that it is a **use-case specific design pattern**, commonly asked in Low Level Design interviews.

The main idea is that a request is **not sent to one object directly**, but to a **chain of objects**, where each object decides whether it can handle the request or should forward it to the next object. :contentReference[oaicite:1]{index=1}

---

# 2. Basic Idea

Consider a client sending a request.

Instead of sending it directly to one object,

the request is sent to the first object in a chain.

```
Client

↓

Handler 1

↓

Handler 2

↓

Handler 3
```

Each handler performs one check.

```
Can I handle this request?
```

If the answer is

```
YES
```

it processes the request.

Otherwise,

it forwards the request to the next handler.

---

# Request Flow

```
Client

↓

Handler 1

↓

Can Handle?

YES → Process Request

NO

↓

Handler 2

↓

Can Handle?

YES → Process Request

NO

↓

Handler 3
```

The lecture compares this structure with a **Linked List**, because every handler stores a reference to the next handler in the chain. :contentReference[oaicite:2]{index=2}

---

# 3. ATM Cash Dispensing Example

The lecture uses an **ATM Machine** as the standard example.

Inside the ATM,

different note denominations are managed independently.

For example,

```
₹1000 Handler

↓

₹500 Handler

↓

₹200 Handler

↓

₹100 Handler
```

Each denomination has its own handler.

Each handler knows only

- how many notes it has
- how much amount it can dispense

It does not know anything about other denominations except the next handler in the chain. :contentReference[oaicite:3]{index=3}

---

# Example 1

Suppose the customer wants

```
₹5000
```

If the ₹1000 handler has

```
5 notes
```

it can satisfy the entire request.

```
₹1000 Handler

↓

5 × ₹1000

↓

Request Completed
```

No other handler is called.

---

# Example 2

Suppose the customer wants

```
₹6000
```

The ₹1000 handler has only

```
5 notes
```

It dispenses

```
₹5000
```

Remaining amount

```
₹1000
```

is forwarded to the next handler.

```
₹1000 Handler

↓

Dispense ₹5000

↓

Forward ₹1000

↓

₹500 Handler

↓

2 × ₹500
```

The request is now fully satisfied.

---

# Example 3

Suppose the customer requests

```
₹150
```

The available handlers are

```
₹1000

₹500

₹100
```

There is no ₹50 handler.

Eventually,

the ₹100 handler cannot complete the remaining amount.

Depending on the use case,

the transaction may

- fail completely
- or dispense only the available amount

The lecture explains that this depends on business requirements. :contentReference[oaicite:4]{index=4}

---

# 4. UML

The lecture introduces the following structure.

```
          MoneyHandler

        dispense(amount)

               ▲

      ---------------------

      │         │         │

₹1000Handler ₹500Handler ₹100Handler
```

Each concrete handler inherits from

```
MoneyHandler
```

---

## Chain

Instead of storing

```
₹1000Handler

↓

₹500Handler
```

directly,

the lecture explains that the parent class itself stores the reference.

```
MoneyHandler

↓

nextHandler
```

Therefore,

every concrete handler automatically gets access to the next handler.

---

# Why Store nextHandler in Parent?

The lecture explains that if

```
₹1000Handler
```

directly stores

```
₹500Handler
```

then the code becomes tightly coupled.

Suppose

```
₹500
```

notes are removed in the future.

The code must change.

Instead,

the parent class stores

```
MoneyHandler*

nextHandler
```

Now the chain can be changed dynamically without modifying any concrete handler.

This follows better design principles. :contentReference[oaicite:5]{index=5}

---

# 5. MoneyHandler

The lecture first creates the abstract class

```cpp
class MoneyHandler
```

It stores

```cpp
MoneyHandler* nextHandler;
```

This reference points to the next handler in the chain.

Initially,

```
nextHandler

=

nullptr
```

---

## setNextHandler()

The parent class provides

```cpp
setNextHandler()
```

Its job is simply to connect two handlers.

Example

```cpp
thousandHandler->setNextHandler(fiveHundredHandler);
```

This creates the chain.

---

## dispense()

The lecture declares

```cpp
virtual void dispense(int amount)=0;
```

as a pure virtual function.

Each denomination has different business logic,

therefore every concrete handler implements it separately. :contentReference[oaicite:6]{index=6}

---

# 6. ThousandHandler

The lecture first implements

```cpp
ThousandHandler
```

It stores

```cpp
numNotes
```

which represents the number of ₹1000 notes available.

The constructor initializes this value.

---

## dispense()

The business logic begins by calculating

```cpp
notesNeeded = amount / 1000;
```

This tells the handler how many ₹1000 notes are required.

---

### Case 1

Enough notes exist.

```
notesNeeded <= numNotes
```

The handler

- dispenses the notes
- decreases its note count

---

### Case 2

Not enough notes exist.

The handler dispenses all available notes.

```
notesNeeded = numNotes

numNotes = 0
```

The remaining amount is forwarded to the next handler.

---

## Remaining Amount

After dispensing,

the handler computes

```cpp
remainingAmount
```

If

```
remainingAmount > 0
```

then

```cpp
nextHandler->dispense(remainingAmount);
```

is called.

Otherwise,

the request ends here.

---

## Last Handler

If

```
nextHandler == nullptr
```

and money still remains,

the lecture prints

```
Insufficient fund in ATM
```

since no handler exists to satisfy the remaining amount. :contentReference[oaicite:7]{index=7}

---

# 7. Remaining Handlers

The lecture then creates

- `FiveHundredHandler`
- `TwoHundredHandler`
- `HundredHandler`

The logic remains exactly the same.

Only the denomination changes.

For example,

```
₹500 Handler

↓

amount / 500
```

```
₹200 Handler

↓

amount / 200
```

```
₹100 Handler

↓

amount / 100
```

Each handler

- dispenses what it can
- forwards the remaining amount to the next handler if required. :contentReference[oaicite:8]{index=8}

---

# 8. Client Setup

The client creates

```cpp
ThousandHandler

FiveHundredHandler

TwoHundredHandler

HundredHandler
```

Each handler receives the number of notes available.

Then,

the chain is created.

```cpp
1000

↓

500

↓

200

↓

100
```

Finally,

the client calls

```cpp
thousandHandler->dispense(amount);
```

The request automatically travels through the chain until it is completely handled.

---

# Execution Flow

```
Client

↓

₹1000 Handler

↓

₹500 Handler

↓

₹200 Handler

↓

₹100 Handler
```

Each handler processes as much amount as possible before forwarding the remaining request.

---

# Part 1 Summary

- Chain of Responsibility is a **Behavioral Design Pattern**.
- A request travels through a chain of handlers until it is completely handled.
- The lecture explains the pattern using the **ATM Cash Dispenser** example.
- Every denomination (`₹1000`, `₹500`, `₹200`, `₹100`) has its own handler.
- The abstract `MoneyHandler` stores a `nextHandler` pointer and defines the `dispense()` interface.
- Each concrete handler dispenses as many notes as possible and forwards the remaining amount to the next handler.
- The client builds the chain using `setNextHandler()` and starts processing by calling `dispense()` on the first handler.

---

**Next:** Part 2 covers the remaining code walkthrough, client execution examples (`₹300`, `₹3000`, `₹4000`), SOLID principles discussed in the lecture, Standard UML, Standard Definition, Linked List vs Chain of Responsibility, real-world use cases, and the final revision sheet.

# Chain of Responsibility Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided ATM Cash Dispenser C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Complete Client Execution
2. Why Multiple Handlers?
3. SOLID Principles
4. Standard UML
5. Standard Definition
6. Linked List vs Chain of Responsibility
7. Real World Use Cases
8. Revision Sheet
9. Final Summary

---

# 9. Complete Client Execution

The lecture demonstrates the execution using different withdrawal amounts.

---

## Example 1

Request

```
₹300
```

Chain

```
₹1000

↓

₹500

↓

₹200

↓

₹100
```

Execution

- ₹1000 Handler cannot dispense.
- ₹500 Handler cannot dispense.
- ₹200 Handler dispenses one ₹200 note.
- Remaining ₹100 is forwarded.
- ₹100 Handler dispenses one ₹100 note.

Output

```
Dispensing 1 × ₹200

Dispensing 1 × ₹100
```

---

## Example 2

Request

```
₹3000
```

The ATM contains

```
3 × ₹1000
```

The ₹1000 handler itself satisfies the complete request.

Output

```
Dispensing 3 × ₹1000
```

The request never reaches the next handlers.

---

## Example 3

Request

```
₹4000
```

Suppose

```
₹1000 Handler

↓

3 notes available
```

Execution

```
Dispense

3 × ₹1000

↓

Remaining ₹1000

↓

₹500 Handler

↓

Dispense

2 × ₹500
```

Final Output

```
Dispensing 3 × ₹1000

Dispensing 2 × ₹500
```

The lecture uses this example to demonstrate how the request travels through the chain until the complete amount is dispensed. :contentReference[oaicite:1]{index=1}

---

# 10. Why Create Multiple Handlers?

The lecture raises an important question.

Instead of creating separate classes,

why not create

```
One Handler
```

containing

```
if...

else if...

else if...
```

for every denomination?

---

## Answer

The lecture explains that although this would work,

it violates important design principles.

Creating separate handlers provides a cleaner and more extensible design.

---

# Open-Closed Principle

Suppose tomorrow

```
₹50
```

notes are introduced.

With one large

```
if-else
```

implementation,

existing code must be modified.

With Chain of Responsibility,

we simply create

```
FiftyHandler
```

and connect it into the chain.

Existing handlers remain unchanged.

Thus,

the design follows the **Open-Closed Principle**.

---

# Single Responsibility Principle

Each handler has only one responsibility.

Example

```
₹1000 Handler

↓

Manage ₹1000 Notes
```

```
₹500 Handler

↓

Manage ₹500 Notes
```

If tomorrow

the ₹500 note changes,

only

```
FiveHundredHandler
```

needs modification.

No other handler changes.

The lecture highlights this as another benefit of the pattern. :contentReference[oaicite:2]{index=2}

---

# 11. Standard UML

The lecture presents the standard UML.

```
                IHandler

         handleRequest()

                ▲

      ----------------------

      │                    │

ConcreteHandler1   ConcreteHandler2

        │

        │ HAS-A

        ▼

     IHandler (next)
```

Important points

- Every concrete handler inherits from `IHandler`.
- `IHandler` stores a reference to another `IHandler`.
- The Client communicates only with the first handler.
- Each handler decides whether to process the request or forward it. :contentReference[oaicite:3]{index=3}

---

# Execution Flow

```
Client

↓

Handler 1

↓

Can Handle?

↓

Yes → Finish

↓

No

↓

Handler 2

↓

Can Handle?

↓

Yes → Finish

↓

No

↓

Handler 3
```

The request keeps moving until

- a handler processes it
- or no handler remains.

---

# 12. Standard Definition

The lecture gives the following definition.

> **Chain of Responsibility Pattern allows a request to pass along a chain of potential handlers. Each handler decides whether it can process the request or pass it to the next handler in the chain.** :contentReference[oaicite:4]{index=4}

---

# 13. Linked List vs Chain of Responsibility

The lecture compares Chain of Responsibility with a Linked List.

---

## Similarity

Both maintain

```
Object

↓

Next Object

↓

Next Object
```

using a

```
next
```

reference.

This is why the pattern visually resembles a Linked List.

---

## Difference 1

### Linked List

Purpose

```
Store Data
```

---

### Chain of Responsibility

Purpose

```
Handle Requests
```

The lecture emphasizes that

the intent of the two structures is completely different.

---

## Difference 2

### Linked List

Each node points to another node of the **same class**.

```
Node

↓

Node
```

---

### Chain of Responsibility

Handlers belong to different concrete classes.

Example

```
₹1000 Handler

↓

₹500 Handler

↓

₹200 Handler

↓

₹100 Handler
```

All of them implement the same abstract interface,

but they are different classes.

This is another major difference highlighted in the lecture. :contentReference[oaicite:5]{index=5}

---

# 14. Real World Use Cases

The lecture introduces practical interview problems where this pattern is useful.

---

## Logger System

A logging framework may contain

```
Info Logger

↓

Debug Logger

↓

Error Logger
```

Each logger decides whether it should handle the log,

or pass it further in the chain.

The lecture mentions Logger Design as one of the standard interview problems solved using this pattern. :contentReference[oaicite:6]{index=6}

---

# Revision Sheet

## Pattern Type

Behavioral Design Pattern

---

## Participants

### Abstract Handler

```
MoneyHandler
```

---

### Concrete Handlers

- ThousandHandler
- FiveHundredHandler
- TwoHundredHandler
- HundredHandler

---

### Client

Starts the request from the first handler.

---

## Relationships

```
MoneyHandler

HAS-A

MoneyHandler*

(nextHandler)
```

---

## Core Method

```
dispense(amount)
```

---

## Request Flow

```
Client

↓

First Handler

↓

Next Handler

↓

Next Handler

↓

...

↓

Last Handler
```

---

## Advantages Discussed

- Open-Closed Principle
- Single Responsibility Principle
- Easy to extend
- Dynamic handler chain
- Loose coupling

---

# Final Summary

The Chain of Responsibility Pattern allows a request to travel through a **chain of handlers**, where each handler decides whether it can process the request or forward it to the next handler.

In the lecture:

- `MoneyHandler` is the abstract handler that stores a reference to the next handler.
- `ThousandHandler`, `FiveHundredHandler`, `TwoHundredHandler`, and `HundredHandler` implement the dispensing logic for their respective denominations.
- The client builds the chain using `setNextHandler()` and starts processing from the first handler.
- The lecture explains that this design follows the **Open-Closed Principle** and **Single Responsibility Principle**, and concludes with common interview use cases such as designing a **Logger System**.
