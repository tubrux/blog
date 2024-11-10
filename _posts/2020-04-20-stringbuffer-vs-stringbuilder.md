---
layout: post
---
### **StringBuffer vs StringBuilder in Java: Which One Should We Use?**

##### Table of Contents
1. [StringBuffer vs StringBuilder in Java: Which One Should We Use?](#stringbuffer-vs-stringbuilder-in-java-which-one-should-we-use)
2. [StringBuffer: Thread-Safe But Slower](#stringbuffer-thread-safe-but-slower)
3. [StringBuilder: Faster, But Not Thread-Safe](#stringbuilder-faster-but-not-thread-safe)
4. [When Should We Use StringBuffer or StringBuilder?](#when-should-we-use-stringbuffer-or-stringbuilder)
5. [Which One Should We Choose?](#which-one-should-we-choose)
6. [Summary](#summary)

When we work with strings in Java, we often need to modify them, such as appending, inserting, or deleting characters. However, Java strings are immutable, meaning every time we modify a string, a new object is created. To avoid this, we use mutable sequences of characters, namely `StringBuffer` and `StringBuilder`. But, how do we decide which one to use? Let’s break it down!

#### **StringBuffer: Thread-Safe But Slower**

`StringBuffer` is a class that provides a mutable sequence of characters, and it is **thread-safe**. This means that it is designed to handle multiple threads accessing and modifying the same instance concurrently without causing data inconsistency. It achieves thread safety by **synchronizing** its methods, ensuring that only one thread can modify the object at a time.

However, this thread safety comes at a cost: **performance**. Because of synchronization, the operations on a `StringBuffer` are slower compared to `StringBuilder`, especially in single-threaded environments where the synchronization overhead isn’t needed.

Here’s an example of using `StringBuffer`:

```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" World!");
System.out.println(sb);  // Output: Hello World!
```

In this example, we can see that the `append()` method is used to modify the string. This method is thread-safe, meaning it can safely be used by multiple threads in a concurrent environment.

#### **StringBuilder: Faster, But Not Thread-Safe**

`StringBuilder` is very similar to `StringBuffer`, but it is **not synchronized**. This means that it is **not thread-safe**, which makes it faster in environments where thread safety is not a concern. Since `StringBuilder` doesn’t synchronize its methods, it performs better in single-threaded scenarios, where we don't need to worry about multiple threads modifying the object at the same time.

If we don’t need thread safety (like in most single-threaded applications), `StringBuilder` should be our go-to choice because of its performance advantage.

Here’s an example of using `StringBuilder`:

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World!");
System.out.println(sb);  // Output: Hello World!
```

This is almost identical to the `StringBuffer` example, but behind the scenes, `StringBuilder` is not synchronizing its methods, making it more efficient.

Maybe we need another scenario to show that it is not thread safe. We assume this is using unit tests:

```java
void append(Appendable appendable, String string) {
    try {
        appendable.append(string);
    } catch (Exception e) {
        System.out.println("Exception caught: " + e.getMessage());
        System.out.println(appendable.getClass().getTypeName());
    }
}

@RepeatedTest(100)
public void shouldIndicateStringBuilderIsNotThreadSafe() throws InterruptedException {
    StringBuilder sharedBuilder = new StringBuilder();

    Runnable appendTask = (String text) -> {
        for (int i = 0; i < 1000; i++) {
            append(sharedBuilder, text);
        }
    };

    Thread thread1 = new Thread(() -> appendTask.run("1"));
    Thread thread2 = new Thread(() -> appendTask.run("2"));
    Thread thread3 = new Thread(() -> appendTask.run("A"));

    thread1.start();
    thread2.start();
    thread3.start();

    thread1.join();
    thread2.join();
    thread3.join();

    System.out.println("sharedBuilder.length(): " + sharedBuilder.length() + 
                       (sharedBuilder.length() != 3000 ? " ❌" : ""));
}
```

This code shows `StringBuilder` is not thread-safe. Multiple threads (`thread1`, `thread2`, `thread3`) try to append to a single `StringBuilder` (`sharedBuilder`), creating race conditions where operations overlap and interfere with each other. Ideally, the final length should be `3000`, but due to unsynchronized access, it's often less, indicating lost or incomplete operations.

```html
sharedBuilder.length():2933❌
sharedBuilder.length():3000
sharedBuilder.length():3000
sharedBuilder.length():2851❌
sharedBuilder.length():2391❌
sharedBuilder.length():3000
sharedBuilder.length():2954❌
sharedBuilder.length():3000
Exception caught: arraycopy: last destination index 36 out of bounds for byte[34]
java.lang.StringBuilder
sharedBuilder.length():2999❌
sharedBuilder.length():2655❌
sharedBuilder.length():3000
sharedBuilder.length():3000
sharedBuilder.length():3000
sharedBuilder.length():3000
```
The output confirms that `StringBuilder` is not thread-safe. Each test run should ideally result in `sharedBuilder.length()` being exactly `3000`, but as we see, the length is sometimes less (e.g., `2933`, `2851`, `2391`). These discrepancies happen because multiple threads are modifying `sharedBuilder` simultaneously, causing some `append` operations to be lost or interrupted due to race conditions.

The occasional exception, like `arraycopy: last destination index 36 out of bounds for byte[34]`, indicates that `StringBuilder` is experiencing issues with internal indexing. This usually happens when threads interfere with each other’s operations, causing unexpected state changes and breaking the internal structure of `StringBuilder`.

The test cases that result in `3000` show what we'd expect in an ideal scenario, but the inconsistent results and exceptions confirm that `StringBuilder` cannot handle concurrent modifications safely. Switching to a thread-safe alternative like `StringBuffer` or adding synchronization would prevent these issues.

To fix this, we can either switch to `StringBuffer`, which is thread-safe, or wrap `append` calls in a `synchronized` block:

```java
synchronized (sharedBuilder) {
    append(sharedBuilder, "1");
}
```

This way, we prevent interference and ensure reliable results.

#### **When Should We Use StringBuffer or StringBuilder?**

- **Use `StringBuffer`** when:
  - We are working in a **multi-threaded** environment, and multiple threads will be modifying the same string.
  - We need to ensure **thread safety** but don’t mind the slight performance overhead due to synchronization.

- **Use `StringBuilder`** when:
  - We are working in a **single-threaded** environment or can guarantee that no other thread will modify the string concurrently.
  - We need **better performance** since `StringBuilder` is faster than `StringBuffer`.

#### **Which One Should We Choose?**

In most cases, we’ll prefer `StringBuilder` because performance is often more important than thread safety, especially in single-threaded scenarios. We can always handle thread safety manually if needed, for example, by synchronizing our code ourselves.

However, if we are working in a multi-threaded environment and need to ensure that no data corruption occurs due to concurrent modifications, `StringBuffer` is the safer choice, even if it comes with a slight performance cost.

### **Summary**

- **StringBuffer** is thread-safe but slower due to synchronization. It’s the best option when thread safety is critical.
- **StringBuilder** is faster and more efficient but not thread-safe. It’s ideal for single-threaded applications or situations where thread safety is not a concern.

By understanding these differences, we can choose the right class for the job based on our needs. 

By the way, by using the <a href="https://tubrux.github.io/">Tubrux</a> library, we can easily find non-thread-safe data structures in our project. So we can fix them as early as possible.
