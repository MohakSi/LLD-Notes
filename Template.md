# Template Method Design Pattern - Part 1

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Model Training C++ implementation. :contentReference[oaicite:0]{index=0}

---

# Table of Contents

1. Introduction
2. Motivation
3. ML Pipeline Example
4. Problem Without Template Method
5. Template Method Solution
6. UML
7. Beginning of C++ Implementation
8. Part Summary

---

# 1. Introduction

Template Method is a **Behavioral Design Pattern**.

The lecture explains that this pattern solves a **very specific type of problem**.

Unlike many general-purpose design patterns, Template Method is useful whenever there is a **fixed sequence (pipeline)** of operations that must always be followed. :contentReference[oaicite:1]{index=1}

---

# 2. Motivation

The lecture considers the example of a **Data Scientist** working with different Machine Learning algorithms.

Examples include

- Neural Network
- Decision Tree
- Random Forest
- SVM

Although every algorithm is different,

all of them follow the **same training pipeline**. :contentReference[oaicite:2]{index=2}

---

# Common Pipeline

Every ML model performs the following steps.

```
Load Data

↓

Preprocess Data

↓

Train Model

↓

Evaluate Model

↓

Save Model
```

The lecture emphasizes that this order **must never change**. :contentReference[oaicite:3]{index=3}

---

# 3. Problem Without Template Method

Suppose every developer writes their own training class.

For example,

```
Neural Network

↓

Developer writes all steps manually
```

Another developer writes

```
Decision Tree

↓

Again writes all steps manually
```

Since every developer writes the pipeline independently,

mistakes can happen.

Example

```
Load Data

↓

Train Model

↓

Preprocess Data
```

This is incorrect because preprocessing should happen before training.

The lecture explains that we should not allow developers to accidentally change the pipeline. :contentReference[oaicite:4]{index=4}

---

# Goal

Provide developers with a **fixed template**.

Developers should only implement the individual steps.

They should not be able to change the execution order.

---

# 4. Template Method Solution

The lecture introduces an abstract class

```
ModelTrainer
```

All models inherit from this class.

```
ModelTrainer

        ▲

        │

----------------------------

│                          │

NeuralNetwork      DecisionTree
```

The parent class defines the complete pipeline.

Child classes only provide implementations of individual steps. :contentReference[oaicite:5]{index=5}

---

# Template Method

Inside the parent class,

a method is created called

```
Template Method
```

(or `trainPipeline()` in the code).

This method calls every step in the required order.

```
Load

↓

Preprocess

↓

Train

↓

Evaluate

↓

Save
```

This order is permanently fixed.

---

# Important Observation

The lecture explains that child classes **cannot change this order**.

They are only responsible for implementing the individual operations.

The parent class controls the pipeline.

---

# Why Developers Cannot Change the Order

Suppose a developer overrides methods in this order.

```
Train

↓

Save

↓

Load

↓

Evaluate
```

It does **not** matter.

Whenever the client calls the Template Method,

execution still happens as

```
Load

↓

Preprocess

↓

Train

↓

Evaluate

↓

Save
```

because the parent class decides the execution order. :contentReference[oaicite:6]{index=6}

---

# Preventing Override

The lecture explains that the Template Method itself should **not** be overridden.

In C++ it can be made

```
const
```

and in Java it can be declared

```
final
```

so subclasses cannot change the algorithm structure. :contentReference[oaicite:7]{index=7}

---

# 5. Client

The lecture introduces a Client class.

The Client has a reference of

```
ModelTrainer
```

The Client never knows whether it is working with

- Neural Network
- Decision Tree
- SVM

It simply calls

```
templateMethod()
```

(or `trainPipeline()` in the implementation).

```
Client

↓

ModelTrainer

↓

trainPipeline()
```

The correct sequence is always followed. :contentReference[oaicite:8]{index=8}

---

# Standard UML

```
               ModelTrainer

        -------------------------

        templateMethod()

        step1()

        step2()

        step3()

                ▲

                │

        ----------------------

        │                    │

Concrete Class A     Concrete Class B
```

The parent defines

- the Template Method
- the order of execution

The child classes override the individual steps. :contentReference[oaicite:9]{index=9}

---

# 6. Beginning of the C++ Implementation

The implementation starts with

```cpp
class ModelTrainer
```

This is the parent class.

It contains

```cpp
trainPipeline()
```

which is the Template Method.

---

# trainPipeline()

The method performs

```cpp
loadData()

↓

preprocessData()

↓

trainModel()

↓

evaluateModel()

↓

saveModel()
```

The lecture emphasizes that this method defines the complete pipeline.

Every model will follow this exact order. :contentReference[oaicite:10]{index=10}

---

# Common Methods

Some operations are common for every model.

Examples shown in the lecture are

```cpp
loadData()

preprocessData()

saveModel()
```

These methods are implemented in the parent class.

Examples include

- Loading dataset
- Splitting into train/test
- Normalization
- Saving the model

Since these operations may be common,

they can be reused by every subclass. :contentReference[oaicite:11]{index=11}

---

# Abstract Methods

Some operations depend on the algorithm.

These remain abstract.

```cpp
trainModel()

evaluateModel()
```

Every concrete model must implement them.

For example,

- Neural Network training differs from Decision Tree training.
- Evaluation logic can also differ.

Hence these methods are overridden by subclasses.

---

# Part 1 Summary

- Template Method is a **Behavioral Design Pattern**.
- It is used when the **execution order (pipeline)** must remain fixed.
- The lecture explains this using a Machine Learning training pipeline.
- Without Template Method, developers may accidentally change the execution order.
- `ModelTrainer` defines the pipeline through `trainPipeline()`.
- Child classes only implement the individual steps.
- The Template Method itself should not be overridden.
- Common steps can be implemented in the parent class, while algorithm-specific steps remain abstract.

---

**Next:** Part 2 covers the complete code walkthrough (`NeuralNetworkTrainer`, `DecisionTreeTrainer`, `main()`), execution flow, default vs overridden methods, standard definition, real-world use cases, and the final revision sheet.

# Template Method Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided Model Training C++ implementation. :contentReference[oaicite:0]{index=0}

---

# 7. NeuralNetworkTrainer

The lecture first implements the

```
NeuralNetworkTrainer
```

class.

It inherits from

```
ModelTrainer
```

Therefore,

it automatically gets the

```
trainPipeline()
```

method.

---

## Overridden Methods

The Neural Network trainer overrides

```cpp
trainModel()

evaluateModel()

saveModel()
```

---

### trainModel()

The lecture's implementation prints

```
Training Neural Network

for 100 epochs
```

This represents the algorithm-specific training logic.

---

### evaluateModel()

The lecture evaluates

```
Accuracy

Loss
```

on the validation dataset.

Again,

this implementation is specific to the Neural Network model.

---

### saveModel()

Although

```
saveModel()
```

already has a default implementation,

the lecture overrides it to demonstrate that subclasses can provide their own saving mechanism.

For the Neural Network,

the model is saved in

```
.h5
```

format.

---

# Important Observation

The lecture explains that

if a subclass overrides a method,

its implementation is used.

Otherwise,

the parent implementation is executed.

This is simply runtime polymorphism.

---

# 8. DecisionTreeTrainer

Next,

the lecture implements

```
DecisionTreeTrainer
```

It also inherits from

```
ModelTrainer
```

---

## Overridden Methods

Decision Tree overrides

```cpp
trainModel()

evaluateModel()
```

---

### Common Methods

The lecture intentionally does **not** override

```cpp
saveModel()
```

Therefore,

the default implementation from

```
ModelTrainer
```

is used.

Similarly,

common preprocessing logic is reused from the parent class.

This demonstrates that subclasses only override the methods they need to customize. :contentReference[oaicite:1]{index=1}

---

# 9. Main Function

The lecture demonstrates the complete working.

---

## Neural Network

A

```cpp
NeuralNetworkTrainer
```

object is created.

Then,

```cpp
trainPipeline()
```

is called.

Execution becomes

```
Load Data

↓

Preprocess Data

↓

Train Neural Network

↓

Evaluate Neural Network

↓

Save Neural Network
```

Notice that

the order comes from

```
trainPipeline()
```

not from the subclass.

---

## Decision Tree

Similarly,

a

```cpp
DecisionTreeTrainer
```

object is created.

Again,

the client simply calls

```cpp
trainPipeline()
```

Execution becomes

```
Load Data

↓

Preprocess Data

↓

Train Decision Tree

↓

Evaluate Decision Tree

↓

Save Model
```

The pipeline remains identical.

Only the implementation of individual steps changes. :contentReference[oaicite:2]{index=2}

---

# Complete Execution Flow

```
Client

↓

trainPipeline()

↓

loadData()

↓

preprocessData()

↓

trainModel()

↓

evaluateModel()

↓

saveModel()
```

If the object is

```
NeuralNetworkTrainer
```

then

```
trainModel()

↓

Neural Network Training
```

If the object is

```
DecisionTreeTrainer
```

then

```
trainModel()

↓

Decision Tree Training
```

The execution order never changes.

---

# Standard UML Execution

```
Client

↓

Template Method

↓

Step 1

↓

Step 2

↓

Step 3
```

The lecture explains that

regardless of which concrete class is used,

the Template Method always decides the sequence.

---

# Standard Definition

The lecture gives the following definition.

> **Template Method Pattern defines the skeleton of an algorithm in a method, deferring some steps to subclasses. It lets subclasses redefine certain steps of an algorithm without changing the algorithm's overall structure.** :contentReference[oaicite:3]{index=3}

---

# Real World Use Cases

The lecture explains that

Template Method is useful whenever the **order of execution is fixed**.

---

## Machine Learning Pipeline

The example discussed throughout the lecture.

```
Load

↓

Preprocess

↓

Train

↓

Evaluate

↓

Save
```

Different algorithms implement these steps differently,

but the sequence remains the same.

---

## Payment Pipeline

The lecture also explains a payment example.

Suppose Account A transfers money to Account B.

The pipeline becomes

```
Validate Account

↓

Debit Money

↓

Credit Money

↓

Validate Transaction

↓

Complete Transaction
```

This order must always remain fixed.

Different payment methods

(UPI, Credit Card, Debit Card)

may implement individual steps differently,

but the execution sequence cannot change. :contentReference[oaicite:4]{index=4}

---

# Key Observations

- Parent class defines the complete algorithm.
- Child classes customize only selected steps.
- The execution order is fixed.
- The Template Method is not overridden.
- Common methods can remain implemented in the parent class.
- Only algorithm-specific methods need to be overridden.

---

# Revision Sheet

## Pattern Type

Behavioral Design Pattern

---

## Participants

### Abstract Class

```
ModelTrainer
```

---

### Template Method

```
trainPipeline()
```

---

### Common Methods

```
loadData()

preprocessData()

saveModel()
```

---

### Abstract Methods

```
trainModel()

evaluateModel()
```

---

### Concrete Classes

- NeuralNetworkTrainer
- DecisionTreeTrainer

---

## Complete Pipeline

```
Load Data

↓

Preprocess Data

↓

Train Model

↓

Evaluate Model

↓

Save Model
```

---

## Important Rule

The **algorithm structure never changes**.

Only the implementation of individual steps changes.

---

## Main Flow

```
Client

↓

ModelTrainer

↓

trainPipeline()

↓

Fixed Order

↓

Subclass Implementations
```

---

# Final Summary

The Template Method Pattern defines the **skeleton of an algorithm** in the parent class while allowing subclasses to customize selected steps.

In the lecture's example:

- **ModelTrainer** is the abstract parent class.
- **trainPipeline()** is the Template Method that fixes the execution order.
- **NeuralNetworkTrainer** and **DecisionTreeTrainer** provide algorithm-specific implementations for training and evaluation.
- Common operations such as loading, preprocessing, or saving can remain in the parent class, while subclasses override only the methods that differ.

The lecture concludes that whenever a problem requires a **fixed execution pipeline** but allows individual steps to vary, the Template Method Pattern is an appropriate solution.
