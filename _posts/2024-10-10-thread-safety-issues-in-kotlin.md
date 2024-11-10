---
layout: post
---
### Table of Contents

- [1. Overview](#1-overview)
- [2. Race Condition](#2-race-condition)
  - [Example Scenario](#example-scenario)
- [3. ConcurrentModificationException](#3-concurrentmodificationexception)
  - [Solution](#solution)
- [4. Deadlock](#3-deadlock)
  - [Example Deadlock Scenario](#example-deadlock-scenario)
- [5. Early Prevention of Thread-Safety Issues with Tubrux](#5-early-prevention-of-thread-safety-issues-with-tubrux)
- [6. Conclusion](#6-conclusion)

### 1. Overview
Thread-safety issues in Kotlin are related to problems that arise when multiple threads access or modify the same data without proper synchronization mechanisms. These issues generally occur in the context of multi-threading or concurrency.

So, by implementing thread-safe data structures, we hope can reduce the potential for thread safety issues.

There may be many thread-safety issues, but here are some common ones

### 2. Race Condition
Race conditions is a situation where two or more threads compete to access and modify the same data at the same time. It can occur when we handle them without proper synchronization, resulting in unpredictable or inconsistent results.

To understand race conditions in a simple case, this diagram can be easily understood:

**Example Scenario:**
```
Initial State: counter = 0

  Thread-1                Thread-2               Thread-3
     |                       |                       |
     |---- Read (0) -------> |                       |
     |                       |---- Read (0) -------> |
     |                       |                       |---- Read (0) -------->|
     |-- Increment (+1)      |                       |                       |
     |                       |                       |                       |
     |-- Write (counter = 1) |                       |                       |
     |                       |-- Increment (+1).     |                       |
     |                       |                       |                       |
     |                       |-- Write (counter = 1) |                       |
     |                       |                       |-- Increment (+1)      |
     |                       |                       |                       |
     |                       |                       |-- Write (counter = 1) |

```

The three threads are not synchronized when accessing and updating the counter value. If each thread is functioning properly and coordinated, the final result of the increment should be 3 instead of 1.

A race condition is usually difficult to reproduce, debug, and eliminate. We describe the bugs introduced by race conditions as heisenbugs.

### 3. ConcurrentModificationException
This exception arises when a collection is modified (e.g., adding or removing elements) during an iteration in a multi-threaded environment. Collections like `MutableList`, `MutableSet`, and `MutableMap` throw this exception when modified structurally during iteration.

<img src="https://github.com/tubrux/blog/blob/dark/_posts/cur-modif.png?raw=true"/>

Collections like `MutableList`, `MutableSet`, or `MutableMap` can throw `ConcurrentModificationException` when the collection is structurally modified while being iterated over (fail-fast).

This is also something that cannot be allowed, because unhandled exceptions can cause the application to crash or terminate.

If a `ConcurrentModificationException` can potentially occur in a single thread, it becomes even more likely in a multi-threaded environment because multiple threads may attempt to modify the collection simultaneously.

With JUnit we can easily validate this exception with `assertFailsWith<ConcurrentModificationException>`.

### 4. Deadlock
Deadlock can occur when multiple threads are waiting for a resource (e.g., a lock) held by another thread, so that no thread can continue executing.

This is the most dangerous problem, because it causes the application to stop functioning and often causes downtime.

Just imagine, in a financial application that processes transactions, if a deadlock occurs, the transaction will not be completed and can potentially cause lost transaction data:

**Example Deadlock Scenario:**

```
[Thread 1]                 [Thread 2]                  [Thread 3]
    |                          |                           |
    v                          v                           v
   Lock A ----> Waiting for   Lock B ----> Waiting for   Lock C ----> Waiting for
       ^                          |                           |
       |                          v                           v
Waiting for <---- Lock C    Waiting for <---- Lock A    Waiting for <---- Lock B
```

All three threads are waiting for resources held by other threads, resulting in a deadlock. None can continue execution.

### 5. Early Prevention of Thread-Safety Issues with Tubrux

Tubrux provides automatic analysis for potential thread-safety issues in Java and Kotlin code, helping developers detect and prevent problems like race conditions, deadlocks, and concurrent modification exceptions before they become issues in production environments. 

This is an example of the output on Intellij IDEA after we run Tubrux on our project:

<img src="https://github.com/tubrux/blog/blob/dark/_posts/deadlock-min.png?raw=true"/>

See how easy it is. By using Tubrux, developers can statically analyze their code to identify potentially unsafe data structures in multi-threaded contexts, and receive recommendations for proper synchronization. This enables early prevention and faster fixes, reducing the risk of hard-to-diagnose bugs during testing or deployment stages.

### 6. Conclusion
In summary, Kotlin provides multiple approaches to ensure thread-safety through data structures and synchronization mechanisms. By selecting the appropriate method, developers can mitigate risks like race conditions, `ConcurrentModificationException`, and deadlocks in multi-threaded applications.