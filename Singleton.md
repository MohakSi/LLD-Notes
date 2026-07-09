# Low Level Design (LLD) Notes

# Lecture 10 -- Singleton Design Pattern

> **Design Pattern Type:** Creational

---

# Definition

The **Singleton Design Pattern** ensures that:

1.  Only **one instance** of a class exists during the application's
    lifetime.
2.  A **global access point** is provided to access that instance.

In simple words:

> Whenever anyone asks for an object of this class, the same object is
> returned.

---

# Problem Statement

Normally, every `new` operation creates a new object.

```cpp
A* obj1 = new A();
A* obj2 = new A();
```

Result:

    obj1 ----> Object A1
    obj2 ----> Object A2

However, for certain classes such as a Logger or Configuration Manager,
multiple objects are unnecessary and can even be harmful.

---

# How Object Creation Works

When we write:

```cpp
A* obj = new A();
```

The following happens internally:

1.  Memory is allocated on the **Heap**.
2.  The class constructor is invoked.
3.  The pointer stored on the **Stack** points to the heap object.

```{=html}
<!-- -->
```

    Stack

    obj
     |
     v

    Heap
    +-----------+
    | Object A  |
    +-----------+

Every call to `new A()` repeats this process and creates a brand-new
object.

---

# First Attempt

Make the constructor private.

```cpp
class Singleton {
private:
    Singleton() {}
};
```

Now external code cannot do:

```cpp
Singleton* s = new Singleton();
```

This prevents object creation completely.

Problem:

We need **one** object, not **zero** objects.

---

# Correct Approach

Allow the class to create and manage its own object.

Expose a static method:

```cpp
static Singleton* getInstance();
```

Instead of:

```cpp
new Singleton();
```

clients use:

```cpp
Singleton::getInstance();
```

---

# Why `getInstance()` Must Be Static

There is no object yet.

A non-static method requires an object.

A static method belongs to the class and can therefore create the first
object.

---

# Static Instance Variable

The class stores the only object using a static pointer.

```cpp
private:
    static Singleton* instance;
```

Initially,

```cpp
instance = nullptr;
```

---

# Lazy Initialization

```cpp
Singleton* Singleton::getInstance() {

    if(instance == nullptr) {
        instance = new Singleton();
    }

    return instance;
}
```

Flow:

    instance == nullptr ?

    YES
     |
    Create Object
     |
    Store in instance
     |
    Return instance

    Second Call

    instance == nullptr ?

    NO
     |
    Return existing instance

---

# Why `instance` is Static

There must be only one shared pointer.

If it were not static, every object would have its own copy.

---

# Complete Singleton Structure

```cpp
class Singleton {

private:

    static Singleton* instance;

    Singleton(){}

public:

    static Singleton* getInstance();
};
```

---

# Thread Safety Problem

Two threads may execute:

```cpp
if(instance == nullptr)
```

at the same time.

Both create an object.

Singleton guarantee breaks.

---

# Thread-safe Singleton (Mutex)

```cpp
lock();

if(instance == nullptr)
    instance = new Singleton();

unlock();
```

Only one thread enters the critical section at a time.

---

# Drawback

Every call acquires the lock even after the Singleton already exists.

Locking is relatively expensive.

---

# Double Checked Locking

```cpp
if(instance == nullptr)
{
    lock();

    if(instance == nullptr)
        instance = new Singleton();

    unlock();
}

return instance;
```

Benefits:

- Avoids unnecessary locking.
- Remains thread-safe.

---

# Eager Initialization

Instead of lazy creation:

```cpp
Singleton* Singleton::instance = new Singleton();
```

Now:

```cpp
Singleton* getInstance() {
    return instance;
}
```

Advantages:

- Very simple
- Thread-safe
- No locking required

Disadvantage:

The object is created even if never used.

Suitable only when object creation is inexpensive.

---

# Real-world Use Cases

## Logger

All services share a single logger.

    Payment
          \
    Order -----> Logger
          /
    User

Benefits:

- Consistent logging
- Less memory
- Single logging pipeline

---

## Database Connection Manager

Creating database connections is expensive.

Instead of multiple managers, the application shares one manager
instance.

---

## Configuration Manager

Stores:

- API Keys
- Environment Variables
- Database URLs
- Feature Flags

Acts as the application's single source of truth.

---

# When NOT to Use Singleton

Avoid Singleton when:

- Multiple independent objects are required.
- Each user/session requires separate state.
- Global state makes testing difficult.
- The class naturally represents many entities.

---

# Advantages

- Saves memory
- Single source of truth
- Global access
- Prevents duplicate objects
- Easy resource sharing

---

# Disadvantages

- Harder to unit test
- Thread-safety concerns
- Hidden global dependency
- Can become a bottleneck if overused

---

# Interview Revision

- Pattern Type: **Creational**
- Private Constructor
- Static Instance Variable
- Static `getInstance()`
- Lazy Initialization
- Eager Initialization
- Thread Safety using Mutex
- Double Checked Locking
- Typical Uses:
  - Logger
  - Database Connection Manager
  - Configuration Manager

---

> **Source:** These notes are based on Lecture 10 (Singleton Design
> Pattern) from the provided transcript, with clearer organization and
> interview-focused explanations while preserving the concepts taught in
> the lecture.
