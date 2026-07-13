# Proxy Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the transcript provided. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction
2. What is Proxy Pattern?
3. Why do we need a Proxy?
4. Core Idea
5. Types of Proxy
6. Virtual Proxy
7. Image Display Example
8. Standard UML for Virtual Proxy
9. Part Summary

---

# 1. Introduction

Proxy Pattern is a **Structural Design Pattern**.

The lecture mentions that it is a **very specific design pattern**, used only in certain types of problems.

The idea is simple and easy to understand once the motivation is clear. :contentReference[oaicite:1]{index=1}

---

# 2. What is Proxy Pattern?

Suppose we have

```
User

↓

Resource
```

Normally,

the user sends a request directly to the resource.

```
User

↓

Request

↓

Resource

↓

Response

↓

User
```

---

## Problem

The lecture explains that sometimes

we do **not** want the user to communicate directly with the resource.

Instead,

we introduce another object.

```
Proxy
```

Now the flow becomes

```
User

↓

Proxy

↓

Resource

↓

Proxy

↓

User
```

The Proxy sits between the User and the Resource.

---

# 3. Why do we need a Proxy?

The lecture discusses several reasons.

---

## Reason 1 — Authentication

The resource may contain

```
Critical Data
```

We do not want every user to access it.

The Proxy first authenticates the user.

```
User

↓

Proxy

↓

Authentication

↓

Resource
```

If authentication fails,

the request never reaches the Resource.

---

## Reason 2 — Validation

Before forwarding the request,

the Proxy can perform additional validations.

Examples mentioned in the lecture include

- checking invalid data
- performing validations before calling the Resource

Only after successful validation is the request forwarded.

---

## Reason 3 — Remote Resource

The Resource may exist on another server.

```
User

↓

Proxy (Local)

↓

Internet

↓

Remote Resource
```

The User only communicates with a local object.

The User never knows that an internet call is taking place.

---

# Important Observation

The lecture emphasizes that

the User **cannot differentiate**

whether it is talking to

- the Proxy
- or the actual Resource.

Both behave exactly the same from the User's perspective. :contentReference[oaicite:2]{index=2}

---

# 4. Core Idea

The Proxy acts as the

```
Representative
```

of the Resource.

Any request made by the User first reaches the Proxy.

```
User

↓

Proxy

↓

Resource
```

The Resource is never accessed directly.

---

# 5. Types of Proxy

The lecture introduces three types.

```
Virtual Proxy

Protection Proxy

Remote Proxy
```

Although their purposes differ,

their core concept remains exactly the same.

The Proxy always acts as the representative of the Resource. :contentReference[oaicite:3]{index=3}

---

# Common Relationship

The lecture explains that

every Proxy has the following relationship.

```
Client

↓

Proxy

↓

Resource
```

The Client always communicates with the Proxy.

---

# 6. Virtual Proxy

The lecture first explains

```
Virtual Proxy
```

using an image loading example.

---

## Problem Statement

Suppose we have a class

```
ImageDisplay
```

It is responsible for displaying an image.

Methods

```
display()
```

Constructor

```
ImageDisplay(path)
```

The constructor receives the image path.

---

# Expensive Constructor

The lecture explains that

creating an ImageDisplay object is expensive.

Inside the constructor,

many operations are performed.

Examples mentioned include

```
Load Image

↓

Compression

↓

Filters

↓

Ready for Display
```

These operations take time.

---

# Why perform them inside the constructor?

The lecture explains the reason.

If these operations were done inside

```
display()
```

then every display request would become slow.

Instead,

the constructor performs all heavy work beforehand,

so

```
display()
```

becomes very fast.

---

# Problem

Suppose the client writes

```cpp
ImageDisplay* img =
new ImageDisplay(path);
```

The expensive constructor immediately executes.

Now imagine

```
display()
```

is never called.

The lecture points out that

all the expensive work was wasted.

The application consumed

- memory
- CPU
- resources

without actually displaying the image. :contentReference[oaicite:4]{index=4}

---

# Solution

Introduce

```
ImageProxy
```

Instead of creating

```
ImageDisplay
```

immediately,

the client creates

```
ImageProxy
```

```
Client

↓

ImageProxy

↓

ImageDisplay
```

---

# Lazy Loading

The lecture explains that

the Proxy does **not** create the real object immediately.

Instead,

it waits until

```
display()
```

is actually called.

Only then is

```
ImageDisplay
```

created.

This is called

```
Lazy Loading
```

---

# Why not modify ImageDisplay?

The lecture answers this question.

One possible reason is

```
Third Party Library
```

If

```
ImageDisplay
```

belongs to a third-party library,

its code cannot be modified.

Therefore,

a Proxy is introduced instead. :contentReference[oaicite:5]{index=5}

---

# Interface

The lecture introduces

```
IDisplay
```

Both

```
ImageDisplay

ImageProxy
```

implement

```
IDisplay
```

```
          IDisplay

          display()

          ▲

     ----------------

     │              │

ImageDisplay   ImageProxy
```

This allows the Client to work only with

```
IDisplay
```

without knowing the actual implementation.

---

# Relationships

The lecture highlights two relationships.

## ImageProxy is-a IDisplay

```
ImageProxy

↓

IDisplay
```

---

## ImageProxy has-a ImageDisplay

```
ImageProxy

↓

ImageDisplay
```

The Proxy stores a reference to the real object.

---

# Client

The Client stores

```
IDisplay*
```

The Client never knows whether it receives

- ImageDisplay
- ImageProxy

The application always passes

```
ImageProxy
```

to the Client.

---

# display()

The lecture explains the Proxy implementation.

When

```
display()
```

is called,

the Proxy first checks

```
Is ImageDisplay already created?
```

---

## If No

```
Create ImageDisplay

↓

Call display()
```

---

## If Yes

Simply call

```
display()
```

directly.

The expensive constructor runs only once.

---

# Execution Flow

```
Client

↓

ImageProxy

↓

Is Object Created?

↓

No

↓

Create ImageDisplay

↓

display()
```

Next time

```
Client

↓

ImageProxy

↓

Already Exists

↓

display()
```

No object creation occurs.

---

# Purpose of Virtual Proxy

The lecture summarizes Virtual Proxy in one line.

> **Protect an expensive resource by delaying its creation until it is actually needed.**

The expensive object is created only when one of its methods is called.

---

# Trade-off

The lecture discusses the trade-off.

## Benefit

- Saves memory
- Saves resources
- Avoids unnecessary object creation

---

## Cost

The first call to

```
display()
```

becomes slightly slower,

because object creation happens at that moment.

---

# Key Observations

- Client never accesses the real object directly.
- Proxy behaves exactly like the real object.
- Real object is created lazily.
- Virtual Proxy is useful when object creation is expensive.
- The pattern implements Lazy Loading.

---

# Part 1 Summary

- Proxy Pattern introduces an intermediate object between the Client and the Resource.
- The Proxy behaves exactly like the Resource from the Client's perspective.
- The lecture discusses three types of Proxy:
  - Virtual Proxy
  - Protection Proxy
  - Remote Proxy
- Virtual Proxy is used for **Lazy Loading** of expensive objects.
- The Image Display example demonstrates how object creation is delayed until `display()` is actually called.
- `ImageProxy` both **implements the same interface (`IDisplay`)** and **holds a reference to the real `ImageDisplay` object**, allowing it to control when the expensive object is created.

---

**Next:** Part 2 covers **Protection Proxy**, **Remote Proxy**, the complete proxy code walkthrough, Standard UML, Standard Definition, Real-world Use Cases, and the final revision sheet.

# Proxy Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the transcript provided. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Protection Proxy
2. Remote Proxy
3. Code Walkthrough
4. Standard UML
5. Standard Definition
6. Real World Use Cases
7. Revision Sheet
8. Final Summary

---

# 7. Protection Proxy

After explaining Virtual Proxy,

the lecture introduces

```
Protection Proxy
```

The basic structure remains exactly the same.

Only the **intent** changes.

Instead of protecting an expensive object,

Protection Proxy protects a **critical resource**.

---

## Example

The lecture considers a

```
Document Reader
```

application.

An interface is created.

```
IDocumentReader
```

It contains

```
unlockPDF(file, password)
```

The concrete implementation is

```
RealDocumentReader
```

which actually unlocks the PDF.

---

## Problem

The lecture explains that

not every user should be allowed to unlock PDFs.

Suppose

unlocking PDFs

is a premium feature.

Only premium users should access it.

---

## User Class

The lecture introduces a

```
User
```

class.

It stores

```
name

isPremium
```

where

```
isPremium = true
```

means

Premium User.

---

## DocumentProxy

A Proxy is introduced.

```
DocumentProxy
```

Relationships

```
DocumentProxy

IS-A

IDocumentReader
```

and

```
DocumentProxy

HAS-A

RealDocumentReader
```

The Proxy also stores

```
User
```

---

## unlockPDF()

When the client calls

```
unlockPDF()
```

the Proxy first checks

```
user.isPremium
```

---

### Premium User

```
Client

↓

DocumentProxy

↓

Premium?

↓

Yes

↓

RealDocumentReader

↓

unlockPDF()
```

---

### Normal User

```
Client

↓

DocumentProxy

↓

Premium?

↓

No

↓

Access Denied
```

The request never reaches the real object.

---

# Purpose of Protection Proxy

The lecture summarizes it as

> Protect a critical resource by validating or authenticating the user before allowing access.

---

# 8. Remote Proxy

The lecture next explains

```
Remote Proxy
```

Again,

the overall structure remains unchanged.

Only the purpose changes.

---

## Example

Suppose a class exists on another server.

```
DataService
```

It contains

```
fetchData()
```

The class communicates with a database.

---

## Problem

Without a Proxy,

the client has to

- establish an internet connection
- communicate with the remote server
- call the remote method

The lecture explains that

the client should not be responsible for this.

---

## Solution

Introduce

```
DataServiceProxy
```

Flow

```
Client

↓

DataServiceProxy

↓

Internet

↓

DataService

↓

Database
```

The client simply calls

```
fetchData()
```

The Proxy establishes the connection,

calls the remote service,

and returns the result.

---

## Lazy Loading

The lecture mentions that

Remote Proxy can also perform

```
Lazy Loading
```

The network connection may be established only when

```
fetchData()
```

is called.

This is similar to Virtual Proxy.

---

# Purpose of Remote Proxy

The lecture summarizes it as

> Represent an object that exists on another server.

The client remains unaware of any network communication.

---

# Summary of All Three Types

## Virtual Proxy

Purpose

```
Protect Expensive Resource
```

Technique

```
Lazy Loading
```

---

## Protection Proxy

Purpose

```
Protect Critical Resource
```

Technique

```
Authentication

Authorization

Validation
```

---

## Remote Proxy

Purpose

```
Represent Remote Object
```

Technique

```
Network Communication
```

The lecture emphasizes that

the **diagram remains the same**

for all three.

Only the **intent** changes. :contentReference[oaicite:1]{index=1}

---

# 9. Code Walkthrough

The lecture demonstrates the Virtual Proxy implementation.

---

## Interface

```
IImage
```

contains

```
display()
```

---

## RealImage

```
RealImage
```

implements

```
IImage
```

The constructor performs

```
Loading Image From Disk
```

This is treated as the expensive operation.

Its

```
display()
```

method simply displays the image.

---

## ImageProxy

The Proxy stores

```
RealImage*

filename
```

Initially

```
RealImage*
```

is

```
nullptr
```

---

## display()

The lecture explains the logic.

```
If RealImage == nullptr

↓

Create RealImage

↓

display()

Else

↓

display()
```

Thus,

the expensive object is created only on the first request.

---

## Main Function

The client creates

```
ImageProxy
```

instead of

```
RealImage
```

```
IImage*

↓

ImageProxy
```

When

```
display()
```

is called,

the output becomes

```
Loading Image From Disk

↓

Displaying Image
```

The lecture demonstrates that

the expensive constructor executes only when

```
display()
```

is invoked. :contentReference[oaicite:2]{index=2}

---

# 10. Standard UML

```
                 ISubject

              operation()

                  ▲

      -------------------------

      │                       │

ProxySubject          RealSubject

      │

      │ HAS-A

      ▼

RealSubject



Client

↓

ISubject
```

The Client always communicates with

```
ISubject
```

The Proxy

- behaves like the Real Subject
- stores a reference to the Real Subject
- decides how and when the Real Subject should be accessed. :contentReference[oaicite:3]{index=3}

---

# 11. Standard Definition

The lecture provides the following definition.

> **Proxy Pattern provides a surrogate or placeholder for another object to control access to it.**

The Proxy acts as the representative of the real object.

Every request reaches the Proxy first.

The Proxy then decides

- whether to forward it
- reject it
- validate it
- authenticate it
- send it over the network. :contentReference[oaicite:4]{index=4}

---

# 12. Real World Use Cases

The lecture discusses several examples.

---

## Premium Features

If only premium users should access a feature,

use

```
Protection Proxy
```

Example

```
Unlock PDF
```

---

## Login Features

Whenever authentication is required,

Protection Proxy is suitable.

---

## Microservices

The lecture explains a microservice architecture.

```
Server 1

↓

Cart Service

↓

UserServiceProxy

↓

Internet

↓

Server 2

↓

User Service
```

The Cart Service communicates only with the Proxy.

The Proxy communicates with the remote User Service.

This is a typical

```
Remote Proxy
```

use case. :contentReference[oaicite:5]{index=5}

---

# Revision Sheet

## Pattern Type

Structural Design Pattern

---

## Participants

### Subject

```
Interface
```

---

### Real Subject

```
Actual Resource
```

---

### Proxy

- IS-A Subject
- HAS-A Real Subject

---

### Client

Communicates only with

```
Subject
```

---

## Types

### Virtual Proxy

Lazy Loading

Protects expensive resources

---

### Protection Proxy

Authentication

Authorization

Protects critical resources

---

### Remote Proxy

Represents remote objects

Handles network communication

---

## Relationships

```
Proxy

IS-A

Subject

---------------

Proxy

HAS-A

Real Subject
```

---

## Complete Flow

```
Client

↓

Proxy

↓

Real Subject

↓

Response

↓

Client
```

---

# Final Summary

The Proxy Pattern introduces a **representative object** between the Client and the Real Subject to control access to the real object.

In the lecture:

- **Virtual Proxy** delays creation of expensive objects through lazy loading.
- **Protection Proxy** authenticates or validates users before allowing access to critical resources.
- **Remote Proxy** represents an object located on another server and hides network communication from the client.

Although the purpose of each type differs, the lecture emphasizes that **their structure remains the same**—the Proxy behaves like the Real Subject, holds a reference to it, and decides how requests should be forwarded.
