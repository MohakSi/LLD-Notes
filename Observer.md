# Observer Design Pattern

> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided C++ implementation (YouTube Channel Example). No external concepts have been added.

---

# Table of Contents

1. Introduction
2. Why Observer Pattern?
3. Problem Statement
4. Polling vs Push Model
5. Observer Pattern Definition
6. Components of Observer Pattern
7. UML
8. C++ Implementation
9. Complete Execution Flow
10. Key Takeaways
11. Revision Sheet

---

# 1. Introduction

Observer Pattern is a **Behavioral Design Pattern**.

It is used when **one object depends on another object**.

Whenever the state of one object changes, all the dependent objects should automatically receive the updated information.

Instead of every object repeatedly checking for updates, the object itself informs everyone who is interested.

This creates a **One-to-Many relationship**.

```
           One Subject
                │
     ┌──────────┼──────────┐
     │          │          │
     ▼          ▼          ▼
 Observer1  Observer2  Observer3
```

---

# 2. Why Observer Pattern?

The lecture explains this pattern using a YouTube channel.

Suppose there is a channel named

```
CoderArmy
```

and two subscribers

```
Varun

Tarun
```

Whenever a new video is uploaded,

both subscribers should immediately receive a notification.

Instead of subscribers checking every minute whether a new video exists,

the channel itself notifies them.

This is exactly the problem Observer Pattern solves.

---

# Problem Without Observer Pattern

Suppose every subscriber continuously checks

```
Has a new video been uploaded?
```

The flow becomes

```
Subscriber

↓

Check Channel

↓

No Video

↓

Wait

↓

Check Again

↓

No Video

↓

Check Again
```

Every subscriber repeats this process.

If thousands of subscribers exist,

thousands of unnecessary requests are made even when no new video has been uploaded.

This approach is inefficient.

---

# Polling Model

The lecture refers to this continuous checking as **Polling**.

```
Subscriber

↓

Ask for update

↓

No

↓

Ask again

↓

No

↓

Ask again

↓

No

↓

Ask again...
```

Problems

- Continuous requests
- Wasted resources
- Unnecessary network calls
- Most requests return nothing

Polling becomes inefficient as the number of users increases.

---

# Push Model

Instead of subscribers asking repeatedly,

the channel itself informs everyone whenever something changes.

```
Upload Video

↓

Channel

↓

Notify Subscribers

↓

Subscriber 1

Subscriber 2

Subscriber 3
```

Subscribers do nothing until they receive a notification.

This approach is efficient because updates are sent only when required.

---

# 3. Observer Pattern Definition

The lecture defines Observer Pattern as:

> **Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified automatically.**

Important points:

- One Subject
- Multiple Observers
- Automatic Notification
- Loose Coupling

---

# 4. Components of Observer Pattern

The lecture introduces four important components.

---

## Subject (Observable)

The Subject is the object whose state changes.

Responsibilities

- Maintain list of observers
- Register observers
- Remove observers
- Notify observers

In this lecture,

the Subject is

```
Channel
```

---

## Observer

Observers are interested in receiving updates.

Whenever the Subject changes,

every Observer receives a notification.

In this lecture,

Observers are

```
Subscriber
```

---

## Concrete Subject

The actual implementation of the Subject.

Here,

```
Channel
```

implements

```
IChannel
```

---

## Concrete Observer

The actual implementation of the Observer.

Here,

```
Subscriber
```

implements

```
ISubscriber
```

---

# Relationship

```
              IChannel
                  ▲
                  │
              Channel
                  │
                  │
      Maintains List Of
                  │
                  ▼

      -------------------------

      │           │          │

 Subscriber   Subscriber  Subscriber
```

One channel can have many subscribers.

---

# 5. Standard UML

```
                +----------------------+
                |      IChannel        |
                +----------------------+
                | + subscribe()        |
                | + unsubscribe()      |
                | + notifySubscribers()|
                +----------^-----------+
                           |
                           |
                     implements
                           |
                    +--------------+
                    |   Channel    |
                    +--------------+
                    | subscribers  |
                    | latestVideo  |
                    +--------------+
                    | uploadVideo()|
                    +------+-------+
                           |
                           | notify()
                           |
          ---------------------------------
          |               |               |
          ▼               ▼               ▼

     +-----------+   +-----------+   +-----------+
     |Subscriber |   |Subscriber |   |Subscriber |
     +-----------+   +-----------+   +-----------+
            ▲
            |
      implements
            |
     +---------------+
     | ISubscriber   |
     +---------------+
     | + update()    |
     +---------------+
```

---

# 6. C++ Implementation

The implementation begins by creating the Observer interface.

```cpp
class ISubscriber {
public:
    virtual void update() = 0;
};
```

Purpose

Every subscriber must implement

```
update()
```

Whenever the Channel notifies its subscribers,

this method is executed.

---

## Observable Interface

Next,

an interface for the Channel is created.

```cpp
class IChannel {
public:
    virtual void subscribe(ISubscriber*) = 0;
    virtual void unsubscribe(ISubscriber*) = 0;
    virtual void notifySubscribers() = 0;
};
```

Responsibilities

- Add subscriber
- Remove subscriber
- Notify subscribers

Every concrete channel must implement these operations.

---

## Channel Class

The lecture then implements

```
Channel
```

It stores

```cpp
vector<ISubscriber*> subscribers;
```

This list contains all the subscribers currently subscribed to the channel.

It also stores

```cpp
latestVideo
```

which represents the newest uploaded video.

---

## subscribe()

```cpp
subscribe(ISubscriber*)
```

Purpose

Add a subscriber into the subscribers list.

The implementation first checks whether the subscriber already exists.

If it does not,

the subscriber is added.

Duplicate subscriptions are avoided.

---

## unsubscribe()

Purpose

Remove a subscriber from the list.

If the subscriber exists,

it is erased from the vector.

If it does not exist,

nothing happens.

---

## notifySubscribers()

```cpp
for(ISubscriber* sub : subscribers)
{
    sub->update();
}
```

This is the heart of Observer Pattern.

The Channel simply loops through every subscriber

and calls

```
update()
```

The Channel does not know

- what each subscriber does,
- how the notification is handled.

It only knows that every subscriber supports

```
update()
```

This keeps the Channel independent of Subscriber implementation.

---

# Part 1 Summary

- Observer Pattern creates a **One-to-Many relationship**.
- Polling continuously checks for updates and wastes resources.
- Push Model sends updates only when necessary.
- The Subject maintains the list of observers.
- Observers implement a common `update()` method.
- `IChannel` defines `subscribe()`, `unsubscribe()`, and `notifySubscribers()`.
- `Channel` stores subscribers and notifies all of them whenever its state changes.

---

**Next:** Part 2 covers the remaining code (`uploadVideo()`, `Subscriber`, `main()`), complete execution flow, YouTube notification flow, advantages discussed in the lecture, and the revision sheet.

# Observer Design Pattern - Part 2

> **Course:** Low Level Design (LLD)
>
> **Design Pattern Type:** Behavioral Design Pattern
>
> **Source:** These notes are based solely on the lecture explanation and the provided C++ implementation (YouTube Channel Example).

---

# 7. Completing the Channel Class

After implementing

- `subscribe()`
- `unsubscribe()`
- `notifySubscribers()`

the Channel class still needs a way to publish new content.

This is done using

```cpp
uploadVideo()
```

---

## uploadVideo()

```cpp
void uploadVideo(const string& title) {
    latestVideo = title;

    cout << "\n[" << name << " uploaded \"" << title << "\"]\n";

    notifySubscribers();
}
```

### Working

Step 1

A new video's title is stored.

```
latestVideo = title
```

↓

Step 2

A message is printed.

```
CoderArmy uploaded

Observer Pattern Tutorial
```

↓

Step 3

Every subscriber is notified.

```
notifySubscribers()
```

This completes the **Push Model** discussed earlier.

The channel does not wait for subscribers to ask for updates.

Instead,

it immediately informs everyone.

---

# getVideoData()

The Channel also provides

```cpp
string getVideoData()
```

Its purpose is to return information about the latest uploaded video.

```cpp
return

"Checkout our new Video : "

+ latestVideo;
```

Notice that

Subscribers never store the latest video themselves.

Whenever they need information,

they request it from the Channel.

---

# 8. Subscriber Class

The next class is

```cpp
Subscriber
```

This class implements

```cpp
ISubscriber
```

Every Subscriber stores two pieces of information.

```cpp
string name;

Channel* channel;
```

---

## name

Represents the subscriber's name.

Example

```
Varun

Tarun
```

---

## channel

Stores the channel to which this subscriber belongs.

Using this reference,

the subscriber can obtain the latest uploaded video's information.

---

# Constructor

```cpp
Subscriber(

name,

channel
)
```

The constructor initializes

- subscriber name
- channel reference

---

# update()

The Observer interface declared

```cpp
update()
```

Subscriber provides its implementation.

```cpp
void update() {

    cout

    << "Hey "

    << name

    << channel->getVideoData();

}
```

Whenever

```
notifySubscribers()
```

is executed,

this function runs automatically.

---

# Notification Flow

```
Channel

↓

notifySubscribers()

↓

Subscriber::update()

↓

channel->getVideoData()

↓

Latest Video Printed
```

Notice that

Subscriber never knows

when a new video is uploaded.

It only receives a notification.

---

# 9. Main Function

The lecture demonstrates the complete flow using

```
CoderArmy
```

---

## Step 1

Create a Channel.

```cpp
Channel* channel =
new Channel("CoderArmy");
```

Current state

```
Channel

Subscribers

[ ]
```

---

## Step 2

Create Subscribers.

```cpp
Subscriber* subs1

Subscriber* subs2
```

Current state

```
Varun

Tarun
```

Neither subscriber is registered yet.

---

## Step 3

Subscribe both users.

```cpp
channel->subscribe(subs1);

channel->subscribe(subs2);
```

Current state

```
Channel

↓

Subscribers

----------------

Varun

Tarun
```

---

## Step 4

Upload first video.

```cpp
uploadVideo(

"Observer Pattern Tutorial"

)
```

Flow

```
uploadVideo()

↓

latestVideo updated

↓

notifySubscribers()

↓

Varun

↓

update()

↓

Print Notification



notifySubscribers()

↓

Tarun

↓

update()

↓

Print Notification
```

Output

```
CoderArmy uploaded

Observer Pattern Tutorial

↓

Hey Varun

Checkout our new Video

Observer Pattern Tutorial

↓

Hey Tarun

Checkout our new Video

Observer Pattern Tutorial
```

Both subscribers receive notifications because both are registered.

---

## Step 5

Unsubscribe Varun.

```cpp
channel->unsubscribe(subs1);
```

Current state

```
Subscribers

↓

Tarun
```

Varun is removed from the subscriber list.

---

## Step 6

Upload another video.

```cpp
uploadVideo(

"Decorator Pattern Tutorial"

)
```

Flow

```
uploadVideo()

↓

notifySubscribers()

↓

Tarun

↓

update()
```

Output

```
CoderArmy uploaded

Decorator Pattern Tutorial

↓

Hey Tarun

Checkout our new Video

Decorator Pattern Tutorial
```

Only Tarun receives the notification because

Varun has already unsubscribed.

---

# Complete Execution Flow

```
Create Channel

↓

Create Subscribers

↓

Subscribe

↓

Subscribers Stored

↓

Upload Video

↓

latestVideo Updated

↓

notifySubscribers()

↓

Loop Over Subscribers

↓

update()

↓

Subscriber Requests

Video Data

↓

Notification Printed
```

---

# Subscribe Flow

```
Subscriber

↓

subscribe()

↓

Added to Vector

↓

Will Receive Notifications
```

---

# Unsubscribe Flow

```
Subscriber

↓

unsubscribe()

↓

Removed From Vector

↓

Will Not Receive Notifications
```

---

# Notification Flow

```
uploadVideo()

↓

latestVideo Updated

↓

notifySubscribers()

↓

update()

↓

getVideoData()

↓

Notification Displayed
```

---

# Relationship Between Classes

```
              Channel

                   │

        Stores List Of

                   │

        --------------------

        │                  │

   Subscriber         Subscriber

        │                  │

        └────── update() ──┘
```

The Channel only knows that every subscriber implements

```
update()
```

It does not know

how notifications are displayed.

This responsibility belongs entirely to the Subscriber.

---

# Key Points From the Lecture

- Channel is the **Observable (Subject)**.
- Subscriber is the **Observer**.
- One Channel can have many Subscribers.
- Subscribers register themselves using `subscribe()`.
- Subscribers leave using `unsubscribe()`.
- Channel stores all subscribers in a vector.
- Whenever a new video is uploaded, the Channel calls `notifySubscribers()`.
- `notifySubscribers()` invokes `update()` on every registered Subscriber.
- Subscriber retrieves the latest video information from the Channel using `getVideoData()`.

---

# Complete Flow of Observer Pattern

```
Channel Created

↓

Subscribers Created

↓

Subscribers Register

↓

Channel Stores Subscribers

↓

New Video Uploaded

↓

Channel Updates State

↓

Channel Notifies Subscribers

↓

Subscriber Receives Notification

↓

Subscriber Reads Latest Video

↓

Notification Displayed
```

---

# Revision Sheet

## Pattern Type

Behavioral Design Pattern

---

## Main Components

### Subject

```
IChannel
```

---

### Concrete Subject

```
Channel
```

---

### Observer

```
ISubscriber
```

---

### Concrete Observer

```
Subscriber
```

---

## Important Methods

### Subject

- subscribe()
- unsubscribe()
- notifySubscribers()

---

### Observer

- update()

---

## Channel Data

- subscribers
- latestVideo
- name

---

## Subscriber Data

- name
- channel

---

## Complete Sequence

```
Subscriber

↓

subscribe()

↓

uploadVideo()

↓

notifySubscribers()

↓

update()

↓

getVideoData()

↓

Notification
```

---

# Final Summary

The Observer Pattern establishes a **one-to-many dependency** between objects.

In this implementation:

- **Channel** acts as the Subject (Observable).
- **Subscriber** acts as the Observer.
- Subscribers register themselves with the Channel.
- Whenever a new video is uploaded, the Channel automatically notifies every registered Subscriber by calling `update()`.
- Subscribers that unsubscribe no longer receive notifications.

The lecture demonstrates the pattern using a YouTube channel, where new video uploads are pushed directly to subscribers instead of subscribers continuously polling the channel for updates.
