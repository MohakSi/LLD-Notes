# Adapter Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided C++ implementation (XML to JSON Adapter Example). No external concepts have been added. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction
2. Real Life Analogy
3. Why Adapter Pattern?
4. Need for Adapter Pattern
5. Existing Code vs Third Party Library
6. XML to JSON Example
7. Components
8. UML
9. Beginning of C++ Implementation
10. Part Summary

---

# 1. Introduction

Adapter Pattern is a **Structural Design Pattern**.

The lecture starts by explaining it using real-life adapters.

The basic idea is

> **Connect two incompatible interfaces so they can work together.**

If two components cannot communicate directly,

an Adapter is introduced between them.

---

# 2. Real Life Analogy

The lecture first explains Adapter Pattern using everyday examples.

---

## Example 1

Suppose you are staying in a hotel.

The hotel has

```
US Socket
```

but your charger has

```
Indian Plug
```

The charger cannot be plugged directly.

An adapter is used.

```
Indian Charger

↓

Adapter

↓

US Socket
```

The adapter makes both compatible.

---

## Example 2

Another example shown is

```
Type-C Cable

↓

Adapter

↓

Type-A Port
```

Again,

the adapter converts one interface into another.

---

# Observation

The adapter

- does not change the charger
- does not change the socket

It only connects two incompatible interfaces.

Programming Adapter Pattern follows exactly the same idea.

---

# 3. Adapter Pattern in Programming

Suppose we have

```
Interface A
```

and

```
Interface B
```

Both are completely different.

```
Interface A

×

Interface B
```

They cannot communicate directly.

The lecture introduces

```
Adapter
```

between them.

```
Interface A

↓

Adapter

↓

Interface B
```

Now both interfaces can work together.

---

# Important Observation

The lecture mentions that

an interface may represent

- an abstract class
- an interface
- a contract

The important point is that

both sides expose different methods,

therefore direct communication is not possible.

---

# 4. Why Adapter Pattern?

The lecture explains how this situation appears in real applications.

Suppose we already have

```
Existing Application
```

Now,

we want to integrate

```
Third Party Library
```

Examples mentioned in the lecture include

- Payment Gateway
- Notification Service
- WhatsApp Notification Provider

These libraries already exist.

We simply integrate them.

---

# Existing Situation

```
Application

↓

Third Party Library
```

The application directly calls the third-party library.

---

# Problem

The application now becomes tightly coupled with that library.

Suppose today we use

```
Library A
```

Tomorrow,

we replace it with

```
Library B
```

because it is

- cheaper
- better
- more efficient

Now,

changes are required inside the application.

This is undesirable.

---

# Solution

Instead of directly calling the third-party library,

introduce an Adapter.

```
Application

↓

Adapter

↓

Third Party Library
```

Now,

the application only communicates with the Adapter.

The Adapter communicates with the library.

If tomorrow the library changes,

only the Adapter changes.

The existing application remains unchanged.

---

# Puzzle Piece Analogy

The lecture also explains this using puzzle pieces.

```
Existing Code

×

Third Party Library
```

The two puzzle pieces cannot connect.

An Adapter is inserted.

```
Existing Code

↓

Adapter

↓

Third Party Library
```

The Adapter fits both sides,

allowing communication between them.

---

# 5. Example Used in the Lecture

The lecture explains the pattern using

```
Report Generation
```

Suppose the Client expects

```
JSON
```

reports.

The Client communicates using

```
IReports
```

---

## Client Expectation

```
Client

↓

IReports

↓

getJsonData()

↓

JSON
```

The Client only understands JSON.

---

## Existing Third Party Library

A third-party library already exists.

Its job is

```
Raw Data

↓

XML

↓

Return XML
```

The library provides XML,

not JSON.

---

# Problem

```
Client

↓

Needs JSON

---------------

Third Party Library

↓

Returns XML
```

The interfaces are incompatible.

---

# Solution

Introduce an Adapter.

```
Client

↓

IReports

↓

Adapter

↓

XmlDataProvider

↓

XML

↓

Convert

↓

JSON

↓

Client
```

The Adapter performs the conversion.

---

# Components

The lecture introduces four components.

---

## Client

The Client always expects

```
JSON
```

The Client never interacts directly with the XML provider.

---

## Target

```
IReports
```

This is the interface expected by the Client.

It exposes

```cpp
getJsonData()
```

---

## Adaptee

```
XmlDataProvider
```

This is the third-party library.

It provides

```cpp
getXmlData()
```

The Client cannot use it directly.

---

## Adapter

```
XmlDataProviderAdapter
```

Responsibilities

- Behaves like `IReports`
- Uses `XmlDataProvider`
- Converts XML into JSON
- Returns JSON to the Client

---

# Important Relationship

The lecture highlights two relationships.

---

## Adapter is-a Target

```
Adapter

↓

IReports
```

Therefore,

the Adapter can be passed wherever

```
IReports
```

is expected.

---

## Adapter has-a Adaptee

```
Adapter

↓

XmlDataProvider*
```

The Adapter stores a reference to the XML provider.

Whenever JSON is requested,

it first asks the XML provider for XML.

---

# UML

```
                  IReports

            getJsonData()

                    ▲

                    │

        XmlDataProviderAdapter

        -----------------------

        XmlDataProvider*

        -----------------------

                    │

                    ▼

            XmlDataProvider

            getXmlData()



Client

↓

IReports
```

The Client never communicates directly with

```
XmlDataProvider
```

It always communicates through

```
IReports
```

---

# 6. Beginning of the C++ Implementation

The implementation starts by creating

```cpp
IReports
```

```cpp
class IReports {
public:
    virtual string getJsonData(const string&) = 0;
};
```

Purpose

Every implementation must provide

```
getJsonData()
```

The Client only depends on this interface.

---

# Adaptee

Next,

the third-party class is created.

```cpp
XmlDataProvider
```

It exposes

```cpp
getXmlData()
```

Instead of JSON,

it returns XML.

The lecture uses a simple

```
name:id
```

string

to generate XML.

This implementation is only for demonstration purposes.

The important idea is that

the library already exists

and returns XML.

---

# Adapter Class

The Adapter is

```cpp
XmlDataProviderAdapter
```

The lecture points out two important relationships.

### It inherits

```cpp
IReports
```

Therefore,

the Adapter

**is-a**

```
IReports
```

---

### It stores

```cpp
XmlDataProvider*
```

Therefore,

the Adapter

**has-a**

```
XmlDataProvider
```

Whenever the Client requests JSON,

the Adapter

- calls `getXmlData()`
- receives XML
- converts XML into JSON
- returns JSON

The Client never knows that XML was involved.

---

# Part 1 Summary

- Adapter Pattern is a **Structural Design Pattern**.
- It connects two incompatible interfaces.
- The lecture explains it using socket adapters and cable converters.
- Real-world motivation comes from integrating third-party libraries.
- Instead of tightly coupling the application with a third-party library, an Adapter is introduced.
- In the example:
  - **Client** expects JSON.
  - **IReports** is the Target interface.
  - **XmlDataProvider** is the Adaptee.
  - **XmlDataProviderAdapter** converts XML into JSON.
- The Adapter has:
  - **is-a** relationship with `IReports`
  - **has-a** relationship with `XmlDataProvider`

---

**Next:** Part 2 covers the complete code walkthrough (`getJsonData()`, `Client`, `main()`), execution flow, object adapter vs class adapter, standard definition, standard UML, and the real-world use cases discussed in the lecture.

# Adapter Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Structural Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided XML to JSON Adapter implementation. :contentReference[oaicite:0]{index=0}

---

# 7. Complete Working of the Adapter

Once the Client requests

```cpp
getJsonData()
```

the complete flow becomes

```
Client

↓

Adapter

↓

XmlDataProvider

↓

XML Data

↓

Convert XML to JSON

↓

Return JSON

↓

Client
```

The Client never communicates directly with the XML provider.

---

# 8. getJsonData()

The most important method of the Adapter is

```cpp
getJsonData()
```

The lecture explains its working step by step.

---

## Step 1

Call the Adaptee.

```cpp
string xml =
xmlProvider->getXmlData(data);
```

The Adapter first asks the XML provider for XML.

At this point,

the Adapter has received XML data.

---

## Step 2

Extract required information.

The lecture's implementation extracts

```
<name>

<id>
```

from the XML.

Example

```xml
<user>

<name>Alice</name>

<id>42</id>

</user>
```

The Adapter finds

- name
- id

inside the XML.

---

## Step 3

Convert XML into JSON.

After extracting the values,

the Adapter creates

```json
{
  "name": "Alice",
  "id": 42
}
```

Finally,

this JSON is returned to the Client.

---

# Important Observation

The lecture mentions that

the XML parsing logic is only for demonstration.

The implementation can vary depending on

- business logic
- input format
- project requirements

The important idea is

```
XML

↓

Adapter

↓

JSON
```

---

# 9. Client Class

The Client works only with

```cpp
IReports
```

```cpp
void getReport(
IReports* report,
string rawData
)
```

The Client simply calls

```cpp
report->getJsonData(rawData);
```

The Client does **not** know

- whether JSON was generated manually
- whether XML was used internally
- whether a third-party library was called

The Client only expects JSON.

---

# Main Function

The lecture demonstrates the complete flow.

---

## Step 1

Create the Adaptee.

```cpp
XmlDataProvider*
```

Current object

```
XmlDataProvider
```

This represents the third-party library.

---

## Step 2

Create the Adapter.

```cpp
IReports* adapter =
new XmlDataProviderAdapter(xmlProvider);
```

Notice that

the reference type is

```cpp
IReports
```

because the Adapter

**is-a**

```
IReports
```

---

## Step 3

Create Raw Data.

```cpp
Alice:42
```

This is passed into the Adapter.

---

## Step 4

Create Client.

```cpp
Client
```

The Client receives

```cpp
IReports*
```

It never receives

```
XmlDataProvider
```

---

## Step 5

Client Calls

```cpp
getReport()
```

Flow

```
Client

↓

getJsonData()

↓

Adapter

↓

getXmlData()

↓

XML

↓

Convert

↓

JSON

↓

Return JSON
```

Output

```json
{
  "name": "Alice",
  "id": 42
}
```

---

# Complete Execution Flow

```
Raw Data

↓

Client

↓

IReports

↓

XmlDataProviderAdapter

↓

XmlDataProvider

↓

XML Generated

↓

XML Parsed

↓

JSON Created

↓

Client Receives JSON
```

---

# Standard Definition

The lecture gives the following definition.

> **Adapter converts the interface of a class into another interface that the client expects.**

The lecture further explains

that the Adapter allows

two incompatible interfaces

to communicate with each other.

---

# Standard UML

```
              Target

             request()

                 ▲

                 │

             Adapter

      -------------------

      Adaptee*

      -------------------

                 │

                 ▼

             Adaptee

       specificRequest()



Client

↓

Target
```

The Client only communicates with the

```
Target
```

The Adapter communicates with the

```
Adaptee
```

---

# Object Adapter

The lecture explains that

everything discussed so far is an

## Object Adapter

Reason

The Adapter stores

```
Adaptee*
```

using composition.

```
Adapter

↓

Adaptee*
```

The Adapter

has-a

reference to the Adaptee.

---

# Class Adapter

Another variation discussed is

## Class Adapter

Instead of composition,

multiple inheritance is used.

```
Target

        ▲

        │

     Adapter

        ▲

        │

    Adaptee
```

Here,

the Adapter inherits from

both

- Target
- Adaptee

---

# Difference

## Object Adapter

Uses

```
Composition
```

---

## Class Adapter

Uses

```
Multiple Inheritance
```

---

# Which One is Preferred?

The lecture recommends

```
Composition

over

Inheritance
```

Therefore,

Object Adapter

is preferred.

The lecture also mentions that

Java does not support multiple inheritance of classes,

making the Object Adapter the common choice.

---

# Real World Use Cases

The lecture discusses two common scenarios.

---

## Third Party Libraries

Whenever an application needs to integrate

- payment gateways
- notification services
- vendor libraries

an Adapter is introduced.

```
Application

↓

Adapter

↓

Vendor Library
```

The existing application remains independent of the vendor implementation.

---

## Legacy Code

Another common use case is

```
Modern Application

↓

Adapter

↓

Legacy Code
```

Older systems often expose different interfaces.

Instead of modifying the legacy code,

an Adapter is introduced so both systems can communicate.

---

# Key Observations

- Client communicates only with the Target interface.
- Adapter behaves like the Target.
- Adapter internally communicates with the Adaptee.
- Adapter converts one interface into another.
- Existing code remains unchanged.
- Third-party libraries remain unchanged.

---

# Revision Sheet

## Pattern Type

Structural Design Pattern

---

## Participants

### Client

Uses only

```
IReports
```

---

### Target

```
IReports
```

Method

```
getJsonData()
```

---

### Adapter

```
XmlDataProviderAdapter
```

- is-a `IReports`
- has-a `XmlDataProvider`

---

### Adaptee

```
XmlDataProvider
```

Method

```
getXmlData()
```

---

## Complete Flow

```
Client

↓

Target

↓

Adapter

↓

Adaptee

↓

XML

↓

JSON

↓

Client
```

---

## Relationships

```
Adapter

IS-A

Target

---------------

Adapter

HAS-A

Adaptee
```

---

## Adapter Types

### Object Adapter

Uses Composition

---

### Class Adapter

Uses Multiple Inheritance

---

# Final Summary

The Adapter Pattern allows two incompatible interfaces to work together without modifying either one.

In the lecture's example:

- **Client** expects JSON through `IReports`.
- **XmlDataProvider** (third-party library) provides XML.
- **XmlDataProviderAdapter** bridges the gap by implementing `IReports`, calling `XmlDataProvider`, converting XML into JSON, and returning the expected result.

The lecture concludes by showing that Adapter Pattern is commonly used when integrating third-party libraries or communicating with legacy systems while keeping the existing application independent of implementation changes.
