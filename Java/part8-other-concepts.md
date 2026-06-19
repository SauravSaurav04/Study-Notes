# Part 8: Other Concepts

**Focus Areas:** Multithreading, Concurrency, Packages, Wrapper Classes, Annotations, Reflection, Design Principles

---

## 📋 Table of Contents
1. [Multithreading & Concurrency](#-multithreading--concurrency)
2. [Java Packages](#-java-packages)
3. [Wrapper Classes](#-wrapper-classes)
4. [Annotations](#-annotations)
5. [Reflection API](#-reflection-api)
6. [Design Principles & Patterns](#-design-principles--patterns)
7. [Cheatsheet - Quick Reference](#-cheatsheet---quick-reference)

---

## 🎯 Multithreading & Concurrency

**Theory:**
Multithreading is the ability of a program to execute multiple threads (lightweight processes) simultaneously. It enables better CPU utilization and improves application performance.

### What is a Thread?

**Thread** is the smallest unit of execution within a process. It has its own:
- Program Counter
- Stack
- Local Variables

**Shared Resources** (among threads in same process):
- Heap Memory
- Code Section
- Data Section
- Open Files

**Benefits of Multithreading:**
1. ✅ **Better CPU Utilization**: Multiple threads can run on multiple cores
2. ✅ **Improved Performance**: Parallel execution of tasks
3. ✅ **Responsiveness**: GUI remains responsive while background tasks run
4. ✅ **Resource Sharing**: Threads share memory space of process
5. ✅ **Lightweight**: Less overhead compared to processes

**Challenges:**
1. ❌ **Synchronization Issues**: Race conditions, deadlocks
2. ❌ **Complexity**: Harder to design and debug
3. ❌ **Thread Safety**: Shared data must be protected
4. ❌ **Context Switching**: Overhead of switching between threads

---

### Thread Lifecycle (Thread States)

**Theory:**
A thread goes through various states during its lifetime.

**6 Thread States:**

```
        NEW
         ↓
     RUNNABLE ←→ RUNNING
         ↓           ↓
      BLOCKED    WAITING
         ↓           ↓
      TIMED_WAITING
         ↓
     TERMINATED
```

| State | Description | How to Reach |
|-------|-------------|--------------|
| **NEW** | Thread created but not started | `Thread t = new Thread()` |
| **RUNNABLE** | Ready to run, waiting for CPU | `t.start()` |
| **RUNNING** | Currently executing | Thread scheduler assigns CPU |
| **BLOCKED** | Waiting for monitor lock | Trying to enter synchronized block |
| **WAITING** | Waiting indefinitely for another thread | `wait()`, `join()`, `park()` |
| **TIMED_WAITING** | Waiting for specified time | `sleep(time)`, `wait(time)`, `join(time)` |
| **TERMINATED** | Thread finished execution | `run()` method completes or exception |

**Example:**
```java
public class ThreadLifecycleDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            System.out.println("Thread is running");
            try {
                Thread.sleep(2000);  // TIMED_WAITING
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Thread finished");
        });

        System.out.println("State: " + thread.getState());  // NEW
        
        thread.start();
        System.out.println("State: " + thread.getState());  // RUNNABLE
        
        Thread.sleep(100);
        System.out.println("State: " + thread.getState());  // TIMED_WAITING
        
        thread.join();  // Wait for thread to finish
        System.out.println("State: " + thread.getState());  // TERMINATED
    }
}
```

---

### Creating Threads

**Theory:**
There are **3 main ways** to create threads in Java.

#### Method 1: Extending Thread Class

**Steps:**
1. Create class that extends `Thread`
2. Override `run()` method
3. Create instance and call `start()`

```java
// Step 1: Extend Thread class
class MyThread extends Thread {
    // Step 2: Override run() method
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(Thread.currentThread().getName() + " - Count: " + i);
            try {
                Thread.sleep(500);  // Sleep for 500ms
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class ThreadExample1 {
    public static void main(String[] args) {
        // Step 3: Create instances and start
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        
        t1.setName("Thread-1");
        t2.setName("Thread-2");
        
        t1.start();  // ✅ Correct - Creates new thread
        t2.start();
        
        // t1.run();  // ❌ Wrong - Runs in main thread, not separate thread
    }
}
```

**Output (Interleaved):**
```
Thread-1 - Count: 1
Thread-2 - Count: 1
Thread-1 - Count: 2
Thread-2 - Count: 2
...
```

#### Method 2: Implementing Runnable Interface (Preferred)

**Advantages over extending Thread:**
- ✅ Can extend another class (Java doesn't support multiple inheritance)
- ✅ Better OOP design (composition over inheritance)
- ✅ Can reuse same Runnable for multiple threads
- ✅ Cleaner separation of task from thread mechanism

```java
// Step 1: Implement Runnable interface
class MyTask implements Runnable {
    private String taskName;
    
    public MyTask(String taskName) {
        this.taskName = taskName;
    }
    
    // Step 2: Implement run() method
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(taskName + " - Count: " + i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        // Step 3: Create Runnable instances
        MyTask task1 = new MyTask("Task-1");
        MyTask task2 = new MyTask("Task-2");
        
        // Step 4: Pass to Thread constructor
        Thread t1 = new Thread(task1);
        Thread t2 = new Thread(task2);
        
        t1.start();
        t2.start();
    }
}
```

#### Method 3: Using Lambda Expression (Java 8+)

**Most Concise Way:**
```java
public class LambdaThreadExample {
    public static void main(String[] args) {
        // Anonymous Runnable using lambda
        Thread t1 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("Lambda Thread - Count: " + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        t1.start();
        
        // Even more concise
        new Thread(() -> System.out.println("Quick task")).start();
    }
}
```

**Comparison:**

| Method | Pros | Cons | Use When |
|--------|------|------|----------|
| **Extend Thread** | Simple for basic tasks | Can't extend other class | Simple standalone threads |
| **Implement Runnable** | Flexible, better design | Slightly more code | Preferred approach |
| **Lambda Expression** | Most concise | Limited to simple tasks | Quick, simple tasks |

---

### Important Thread Methods

#### 1. start() vs run()

**Critical Difference:**

```java
Thread t = new Thread(() -> System.out.println("Thread: " + Thread.currentThread().getName()));

// ✅ start() - Creates new thread and calls run()
t.start();  // Output: Thread: Thread-0

// ❌ run() - Calls run() in current thread (no new thread created)
t.run();    // Output: Thread: main
```

| Method | Behavior |
|--------|----------|
| `start()` | Creates new thread, calls `run()` in that thread |
| `run()` | Executes in current thread (no new thread created) |

#### 2. sleep()

**Pauses execution** for specified milliseconds.

```java
public class SleepExample {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("Count: " + i);
                try {
                    Thread.sleep(1000);  // Sleep for 1 second
                } catch (InterruptedException e) {
                    System.out.println("Thread interrupted");
                }
            }
        });
        
        t.start();
    }
}
```

**Key Points:**
- ✅ Static method: `Thread.sleep(milliseconds)`
- ✅ Throws `InterruptedException` (checked exception)
- ✅ Thread enters **TIMED_WAITING** state
- ✅ Does NOT release locks (if holding any)

#### 3. join()

**Waits for a thread to finish** before continuing.

```java
public class JoinExample {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 1; i <= 3; i++) {
                System.out.println("Thread 1: " + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 1; i <= 3; i++) {
                System.out.println("Thread 2: " + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        t1.start();
        t1.join();  // Main thread waits for t1 to finish
        
        t2.start();
        t2.join();  // Main thread waits for t2 to finish
        
        System.out.println("All threads finished");
    }
}
```

**Output (Sequential):**
```
Thread 1: 1
Thread 1: 2
Thread 1: 3
Thread 2: 1
Thread 2: 2
Thread 2: 3
All threads finished
```

**Variations:**
```java
t.join();           // Wait indefinitely
t.join(1000);       // Wait max 1 second
t.join(1000, 500);  // Wait max 1.5 seconds
```

#### 4. yield()

**Hints to scheduler** that current thread is willing to yield its current use of CPU.

```java
public class YieldExample {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("Thread 1: " + i);
                Thread.yield();  // Give chance to other threads
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("Thread 2: " + i);
            }
        });
        
        t1.start();
        t2.start();
    }
}
```

**Key Points:**
- ✅ Static method: `Thread.yield()`
- ✅ Only a **hint** to scheduler (not guaranteed)
- ✅ Thread remains in **RUNNABLE** state
- ✅ Used for better thread cooperation

#### 5. interrupt()

**Interrupts a thread** that is sleeping or waiting.

```java
public class InterruptExample {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try {
                System.out.println("Thread going to sleep");
                Thread.sleep(10000);  // Sleep for 10 seconds
                System.out.println("Thread woke up naturally");
            } catch (InterruptedException e) {
                System.out.println("Thread was interrupted!");
            }
        });
        
        t.start();
        Thread.sleep(2000);  // Wait 2 seconds
        
        t.interrupt();  // Interrupt the sleeping thread
        System.out.println("Interrupt sent");
    }
}
```

**Output:**
```
Thread going to sleep
Interrupt sent
Thread was interrupted!
```

#### 6. Priority Methods

**Thread priority** influences scheduler decisions (1-10, default is 5).

```java
public class PriorityExample {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("Low Priority: " + i);
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("High Priority: " + i);
            }
        });
        
        // Set priorities
        t1.setPriority(Thread.MIN_PRIORITY);   // 1
        t2.setPriority(Thread.MAX_PRIORITY);   // 10
        
        System.out.println("T1 Priority: " + t1.getPriority());
        System.out.println("T2 Priority: " + t2.getPriority());
        
        t1.start();
        t2.start();
    }
}
```

**Priority Constants:**
- `Thread.MIN_PRIORITY` = 1
- `Thread.NORM_PRIORITY` = 5 (default)
- `Thread.MAX_PRIORITY` = 10

**Note:** Priority is only a **hint** to scheduler, not a guarantee.

#### 7. Daemon Threads

**Daemon threads** run in background and do not prevent JVM from exiting.

```java
public class DaemonExample {
    public static void main(String[] args) {
        Thread daemon = new Thread(() -> {
            while (true) {
                System.out.println("Daemon running...");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        Thread user = new Thread(() -> {
            for (int i = 1; i <= 3; i++) {
                System.out.println("User thread: " + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        daemon.setDaemon(true);  // Must be called before start()
        
        daemon.start();
        user.start();
        
        System.out.println("Main ending");
        // JVM exits when all user threads finish (daemon may still be running)
    }
}
```

**Key Points:**
- ✅ `setDaemon(true)` must be called **before** `start()`
- ✅ JVM exits when all **user threads** finish (ignores daemon threads)
- ✅ Examples: Garbage Collector, Finalizer thread
- ✅ Check with `isDaemon()`

**Summary Table:**

| Method | Description | Throws Exception |
|--------|-------------|------------------|
| `start()` | Start thread execution | No |
| `run()` | Code to execute in thread | No |
| `sleep(ms)` | Pause for milliseconds | InterruptedException |
| `join()` | Wait for thread to finish | InterruptedException |
| `join(ms)` | Wait max milliseconds | InterruptedException |
| `yield()` | Hint to yield CPU | No |
| `interrupt()` | Interrupt thread | No |
| `setPriority(int)` | Set priority (1-10) | No |
| `getPriority()` | Get priority | No |
| `setDaemon(boolean)` | Set as daemon thread | No |
| `isDaemon()` | Check if daemon | No |
| `isAlive()` | Check if thread running | No |
| `currentThread()` | Get current thread | No |
| `getName()` | Get thread name | No |
| `setName(String)` | Set thread name | No |

---

### Synchronization

**Theory:**
Synchronization is the mechanism to control access to shared resources by multiple threads. It prevents **race conditions** and ensures **thread safety**.

**Problem: Race Condition**

```java
// WITHOUT SYNCHRONIZATION - Race Condition
class Counter {
    private int count = 0;
    
    public void increment() {
        count++;  // NOT atomic! (read, increment, write)
    }
    
    public int getCount() {
        return count;
    }
}

public class RaceConditionDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.getCount());
        // Expected: 2000, Actual: varies (e.g., 1847, 1923)
    }
}
```

**Why?** `count++` is not atomic:
1. Read value of count
2. Increment value
3. Write back to count

Multiple threads can interleave these steps, causing lost updates.

#### Solution 1: Synchronized Method

```java
class Counter {
    private int count = 0;
    
    // Synchronized method - only one thread can execute at a time
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}

public class SynchronizedMethodDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.getCount());
        // Output: 2000 (correct)
    }
}
```

**How it works:**
- When a thread enters synchronized method, it acquires **lock** on object
- Other threads must wait until lock is released
- Only one thread can execute synchronized method at a time

#### Solution 2: Synchronized Block

**More flexible** - synchronize only critical section.

```java
class Counter {
    private int count = 0;
    
    public void increment() {
        // Only critical section is synchronized
        synchronized(this) {
            count++;
        }
    }
    
    public int getCount() {
        synchronized(this) {
            return count;
        }
    }
}

public class SynchronizedBlockDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.getCount());
        // Output: 2000 (correct)
    }
}
```

**Advantages:**
- ✅ More granular control
- ✅ Better performance (only critical section locked)
- ✅ Can use different locks

#### Solution 3: Static Synchronization

**For static methods** - lock on Class object, not instance.

```java
class Counter {
    private static int count = 0;
    
    // Static synchronized method - locks on Counter.class
    public static synchronized void increment() {
        count++;
    }
    
    public static synchronized int getCount() {
        return count;
    }
}

public class StaticSyncDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                Counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                Counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Count: " + Counter.getCount());
        // Output: 2000
    }
}
```

**Comparison:**

| Type | Lock On | Syntax | Use Case |
|------|---------|--------|----------|
| **Instance Sync Method** | Object instance | `synchronized method()` | Instance variables |
| **Static Sync Method** | Class object | `static synchronized method()` | Static variables |
| **Synchronized Block** | Specified object | `synchronized(obj) {}` | Fine-grained control |

---

### Deadlock

**Theory:**
Deadlock occurs when two or more threads are blocked forever, waiting for each other to release locks.

**Conditions for Deadlock:**
1. **Mutual Exclusion**: Resources cannot be shared
2. **Hold and Wait**: Thread holds lock and waits for another
3. **No Preemption**: Locks cannot be forcibly taken
4. **Circular Wait**: Circular chain of threads waiting for locks

**Deadlock Example:**

```java
public class DeadlockDemo {
    private static final Object lock1 = new Object();
    private static final Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock1...");
                
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                
                System.out.println("Thread 1: Waiting for lock2...");
                synchronized (lock2) {
                    System.out.println("Thread 1: Acquired lock2!");
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Holding lock2...");
                
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                
                System.out.println("Thread 2: Waiting for lock1...");
                synchronized (lock1) {
                    System.out.println("Thread 2: Acquired lock1!");
                }
            }
        });
        
        t1.start();
        t2.start();
        
        // DEADLOCK! Both threads wait forever
    }
}
```

**Output:**
```
Thread 1: Holding lock1...
Thread 2: Holding lock2...
Thread 1: Waiting for lock2...
Thread 2: Waiting for lock1...
(Program hangs forever)
```

**Prevention:**

```java
public class DeadlockPreventionDemo {
    private static final Object lock1 = new Object();
    private static final Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            // Acquire locks in same order
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock1...");
                synchronized (lock2) {
                    System.out.println("Thread 1: Acquired lock2!");
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            // Acquire locks in SAME order (lock1 then lock2)
            synchronized (lock1) {
                System.out.println("Thread 2: Holding lock1...");
                synchronized (lock2) {
                    System.out.println("Thread 2: Acquired lock2!");
                }
            }
        });
        
        t1.start();
        t2.start();
        
        // No deadlock! Threads execute sequentially
    }
}
```

**Deadlock Prevention Strategies:**
1. ✅ **Lock Ordering**: Always acquire locks in same order
2. ✅ **Lock Timeout**: Use `tryLock()` with timeout
3. ✅ **Avoid Nested Locks**: Minimize lock nesting
4. ✅ **Use Higher-Level Concurrency Utilities**: Use `java.util.concurrent`

---

### Inter-thread Communication (wait, notify, notifyAll)

**Theory:**
Threads can communicate with each other using `wait()`, `notify()`, and `notifyAll()` methods.

**Methods (from Object class):**

| Method | Description | Must be in |
|--------|-------------|------------|
| `wait()` | Releases lock and waits | synchronized block |
| `wait(ms)` | Waits for max milliseconds | synchronized block |
| `notify()` | Wakes up one waiting thread | synchronized block |
| `notifyAll()` | Wakes up all waiting threads | synchronized block |

**Producer-Consumer Example:**

```java
class SharedResource {
    private int data;
    private boolean hasData = false;
    
    // Producer produces data
    public synchronized void produce(int value) {
        while (hasData) {
            try {
                wait();  // Wait if data already exists
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        this.data = value;
        hasData = true;
        System.out.println("Produced: " + value);
        notify();  // Notify consumer
    }
    
    // Consumer consumes data
    public synchronized int consume() {
        while (!hasData) {
            try {
                wait();  // Wait if no data available
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        hasData = false;
        System.out.println("Consumed: " + data);
        notify();  // Notify producer
        return data;
    }
}

public class ProducerConsumerDemo {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        
        // Producer thread
        Thread producer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                resource.produce(i);
            }
        });
        
        // Consumer thread
        Thread consumer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                resource.consume();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

**Output:**
```
Produced: 1
Consumed: 1
Produced: 2
Consumed: 2
Produced: 3
Consumed: 3
Produced: 4
Consumed: 4
Produced: 5
Consumed: 5
```

**Key Points:**
- ✅ `wait()`, `notify()`, `notifyAll()` must be called from **synchronized context**
- ✅ `wait()` releases the lock
- ✅ `sleep()` does NOT release the lock
- ✅ Always use `while` loop (not `if`) for condition checking (prevents spurious wakeups)

**wait() vs sleep():**

| Feature | wait() | sleep() |
|---------|--------|---------|
| **Class** | Object class | Thread class |
| **Lock** | Releases lock | Does NOT release lock |
| **Wakeup** | notify()/notifyAll() | After time elapses |
| **Context** | Must be in synchronized | Anywhere |
| **Purpose** | Inter-thread communication | Pause execution |

---

### volatile Keyword

**Theory:**
The `volatile` keyword ensures that changes to a variable are **immediately visible** to all threads.

**Problem Without volatile:**

```java
class Task {
    private boolean running = true;  // Cached in thread's local memory
    
    public void stop() {
        running = false;
    }
    
    public void run() {
        while (running) {
            // Do work
        }
        System.out.println("Stopped");
    }
}
```

Thread may cache `running` in CPU register/cache, not seeing updates from other threads.

**Solution: Use volatile**

```java
class Task {
    private volatile boolean running = true;  // ✅ volatile
    
    public void stop() {
        running = false;  // Immediately visible to all threads
    }
    
    public void run() {
        while (running) {
            // Do work
        }
        System.out.println("Stopped");
    }
}

public class VolatileExample {
    public static void main(String[] args) throws InterruptedException {
        Task task = new Task();
        
        Thread worker = new Thread(() -> task.run());
        worker.start();
        
        Thread.sleep(1000);
        
        task.stop();  // Stop the worker thread
        System.out.println("Stop signal sent");
    }
}
```

**What volatile Does:**
1. ✅ Reads/writes go directly to **main memory** (not CPU cache)
2. ✅ Changes are **immediately visible** to all threads
3. ✅ Prevents **instruction reordering**
4. ❌ Does NOT provide **atomicity** (use `synchronized` or `AtomicInteger` for that)

**When to Use:**
- ✅ Flag variables (boolean flags)
- ✅ Status variables
- ✅ Single variable updates visible to multiple threads
- ❌ NOT for compound operations (like `count++`)

**volatile vs synchronized:**

| Feature | volatile | synchronized |
|---------|----------|--------------|
| **Visibility** | ✅ Yes | ✅ Yes |
| **Atomicity** | ❌ No | ✅ Yes |
| **Performance** | Faster | Slower |
| **Use Case** | Simple flags | Complex operations |
| **Lock** | No lock | Acquires lock |

---

### Executor Framework (java.util.concurrent)

**Theory:**
The Executor Framework provides a **higher-level API** for managing threads, replacing manual thread creation.

**Problems with Manual Thread Creation:**
1. ❌ **Overhead**: Creating/destroying threads is expensive
2. ❌ **No Limit**: Can create unlimited threads (resource exhaustion)
3. ❌ **No Reusability**: Threads die after task completes
4. ❌ **Hard to Manage**: Manual lifecycle management

**Solution: Thread Pool**

#### ThreadPoolExecutor

**Creating Thread Pools:**

```java
import java.util.concurrent.*;

public class ExecutorExample {
    public static void main(String[] args) {
        // 1. Fixed Thread Pool (Fixed number of threads)
        ExecutorService executor1 = Executors.newFixedThreadPool(3);
        
        // 2. Cached Thread Pool (Creates threads as needed)
        ExecutorService executor2 = Executors.newCachedThreadPool();
        
        // 3. Single Thread Executor (Single worker thread)
        ExecutorService executor3 = Executors.newSingleThreadExecutor();
        
        // 4. Scheduled Thread Pool (For scheduled tasks)
        ScheduledExecutorService executor4 = Executors.newScheduledThreadPool(2);
        
        // Close all
        executor1.shutdown();
        executor2.shutdown();
        executor3.shutdown();
        executor4.shutdown();
    }
}
```

**Using Fixed Thread Pool:**

```java
import java.util.concurrent.*;

public class FixedThreadPoolDemo {
    public static void main(String[] args) {
        // Create thread pool with 3 threads
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        // Submit 10 tasks (3 threads will handle them)
        for (int i = 1; i <= 10; i++) {
            int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " executed by " + 
                                   Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        // Shutdown executor
        executor.shutdown();  // No new tasks accepted, existing tasks complete
        
        try {
            // Wait for all tasks to complete (max 60 seconds)
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();  // Force shutdown
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
        
        System.out.println("All tasks completed");
    }
}
```

**Output:**
```
Task 1 executed by pool-1-thread-1
Task 2 executed by pool-1-thread-2
Task 3 executed by pool-1-thread-3
Task 4 executed by pool-1-thread-1
Task 5 executed by pool-1-thread-2
...
All tasks completed
```

**Thread Pool Types:**

| Type | Description | Use Case |
|------|-------------|----------|
| **FixedThreadPool** | Fixed number of threads | Known workload |
| **CachedThreadPool** | Creates threads as needed, reuses idle threads | Short-lived async tasks |
| **SingleThreadExecutor** | Single worker thread | Sequential execution |
| **ScheduledThreadPool** | Schedule tasks for future execution | Periodic tasks |

**ExecutorService Methods:**

| Method | Description |
|--------|-------------|
| `submit(Runnable)` | Submit task, returns Future |
| `submit(Callable)` | Submit task that returns result |
| `execute(Runnable)` | Execute task, no return value |
| `shutdown()` | Graceful shutdown (finish existing tasks) |
| `shutdownNow()` | Immediate shutdown (attempts to stop all tasks) |
| `awaitTermination(time, unit)` | Wait for termination |
| `isShutdown()` | Check if shutdown initiated |
| `isTerminated()` | Check if all tasks completed |

---

### Callable & Future

**Theory:**
`Callable` is similar to `Runnable`, but can **return a result** and **throw exceptions**.

**Runnable vs Callable:**

| Feature | Runnable | Callable |
|---------|----------|----------|
| **Return Value** | ❌ void | ✅ Generic type |
| **Method** | `run()` | `call()` |
| **Exception** | Cannot throw checked exception | Can throw Exception |
| **Result** | No | Yes (via Future) |
| **Since** | Java 1.0 | Java 5.0 |

#### Callable Example

```java
import java.util.concurrent.*;

public class CallableExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // Callable that returns sum of numbers
        Callable<Integer> task1 = () -> {
            System.out.println("Task 1 started");
            Thread.sleep(2000);
            return 100;
        };
        
        Callable<Integer> task2 = () -> {
            System.out.println("Task 2 started");
            Thread.sleep(1000);
            return 200;
        };
        
        // Submit tasks and get Future objects
        Future<Integer> future1 = executor.submit(task1);
        Future<Integer> future2 = executor.submit(task2);
        
        System.out.println("Tasks submitted");
        
        // Get results (blocks until task completes)
        Integer result1 = future1.get();  // Blocks for 2 seconds
        Integer result2 = future2.get();  // Blocks for 1 second
        
        System.out.println("Result 1: " + result1);
        System.out.println("Result 2: " + result2);
        System.out.println("Sum: " + (result1 + result2));
        
        executor.shutdown();
    }
}
```

**Output:**
```
Tasks submitted
Task 1 started
Task 2 started
Result 1: 100
Result 2: 200
Sum: 300
```

#### Future Methods

```java
import java.util.concurrent.*;

public class FutureMethodsDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException, TimeoutException {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        Callable<String> task = () -> {
            Thread.sleep(3000);
            return "Task completed";
        };
        
        Future<String> future = executor.submit(task);
        
        // 1. isDone() - Check if task completed
        System.out.println("Is done? " + future.isDone());  // false
        
        // 2. isCancelled() - Check if task was cancelled
        System.out.println("Is cancelled? " + future.isCancelled());  // false
        
        // 3. get() - Get result (blocks until ready)
        // String result = future.get();  // Blocks for 3 seconds
        
        // 4. get(timeout) - Get result with timeout
        try {
            String result = future.get(2, TimeUnit.SECONDS);  // Timeout!
            System.out.println("Result: " + result);
        } catch (TimeoutException e) {
            System.out.println("Task timed out!");
        }
        
        // 5. cancel(mayInterruptIfRunning) - Cancel task
        boolean cancelled = future.cancel(true);
        System.out.println("Cancelled? " + cancelled);
        
        System.out.println("Is done? " + future.isDone());  // true (cancelled)
        System.out.println("Is cancelled? " + future.isCancelled());  // true
        
        executor.shutdown();
    }
}
```

**Future Methods:**

| Method | Description |
|--------|-------------|
| `get()` | Get result (blocks until ready) |
| `get(timeout, unit)` | Get result with timeout |
| `isDone()` | Check if task completed |
| `isCancelled()` | Check if task was cancelled |
| `cancel(mayInterrupt)` | Attempt to cancel task |

#### Multiple Callables Example

```java
import java.util.concurrent.*;
import java.util.*;

public class MultipleCallablesDemo {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        // Create multiple Callables
        List<Callable<Integer>> tasks = new ArrayList<>();
        
        for (int i = 1; i <= 5; i++) {
            int taskId = i;
            tasks.add(() -> {
                System.out.println("Task " + taskId + " executing");
                Thread.sleep(1000);
                return taskId * 10;
            });
        }
        
        // invokeAll() - Execute all tasks and wait for all to complete
        List<Future<Integer>> futures = executor.invokeAll(tasks);
        
        // Get all results
        System.out.println("\nResults:");
        for (Future<Integer> future : futures) {
            System.out.println(future.get());
        }
        
        executor.shutdown();
    }
}
```

**Output:**
```
Task 1 executing
Task 2 executing
Task 3 executing
Task 4 executing
Task 5 executing

Results:
10
20
30
40
50
```

---

### Common Concurrency Utilities

#### 1. CountDownLatch

**Waits for a set of threads** to complete before proceeding.

```java
import java.util.concurrent.*;

public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch latch = new CountDownLatch(3);  // Wait for 3 threads
        
        for (int i = 1; i <= 3; i++) {
            int taskId = i;
            new Thread(() -> {
                System.out.println("Task " + taskId + " started");
                try {
                    Thread.sleep(1000 * taskId);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("Task " + taskId + " completed");
                latch.countDown();  // Decrement count
            }).start();
        }
        
        System.out.println("Waiting for tasks to complete...");
        latch.await();  // Block until count reaches 0
        System.out.println("All tasks completed!");
    }
}
```

#### 2. CyclicBarrier

**Waits for threads** to reach a common barrier point.

```java
import java.util.concurrent.*;

public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier barrier = new CyclicBarrier(3, () -> {
            System.out.println("All threads reached barrier!");
        });
        
        for (int i = 1; i <= 3; i++) {
            int taskId = i;
            new Thread(() -> {
                System.out.println("Thread " + taskId + " working");
                try {
                    Thread.sleep(1000 * taskId);
                    System.out.println("Thread " + taskId + " reached barrier");
                    barrier.await();  // Wait at barrier
                } catch (InterruptedException | BrokenBarrierException e) {
                    e.printStackTrace();
                }
                System.out.println("Thread " + taskId + " continuing");
            }).start();
        }
    }
}
```

#### 3. Semaphore

**Controls access** to a resource with limited capacity.

```java
import java.util.concurrent.*;

public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(2);  // Only 2 threads can access
        
        for (int i = 1; i <= 5; i++) {
            int taskId = i;
            new Thread(() -> {
                try {
                    System.out.println("Thread " + taskId + " waiting");
                    semaphore.acquire();  // Acquire permit
                    System.out.println("Thread " + taskId + " acquired permit");
                    
                    Thread.sleep(2000);  // Use resource
                    
                    System.out.println("Thread " + taskId + " releasing permit");
                    semaphore.release();  // Release permit
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

---

## 🎯 Java Packages

**Theory:**
A package is a **namespace** that organizes related classes and interfaces. It helps avoid naming conflicts and makes code more maintainable.

### Why Use Packages?

1. ✅ **Namespace Management**: Avoid naming conflicts
2. ✅ **Organization**: Group related classes
3. ✅ **Access Control**: Control visibility with access modifiers
4. ✅ **Reusability**: Easily reuse classes in other projects
5. ✅ **Maintainability**: Better code organization

**Example:**
- `java.util.Date` - Date class from java.util package
- `java.sql.Date` - Different Date class from java.sql package

Without packages, we couldn't have two classes with the same name.

---

### Built-in Packages

Java provides many built-in packages:

| Package | Description | Common Classes |
|---------|-------------|----------------|
| `java.lang` | Fundamental classes (auto-imported) | String, Math, System, Integer, Thread |
| `java.util` | Utility classes | ArrayList, HashMap, Scanner, Date |
| `java.io` | Input/Output operations | File, FileReader, BufferedReader |
| `java.nio` | New I/O (non-blocking) | Path, Files |
| `java.net` | Networking | URL, Socket, ServerSocket |
| `java.sql` | Database connectivity (JDBC) | Connection, Statement, ResultSet |
| `java.awt` | GUI components (old) | Frame, Button, Panel |
| `javax.swing` | GUI components (new) | JFrame, JButton, JPanel |
| `java.time` | Date and time API (Java 8+) | LocalDate, LocalTime, LocalDateTime |
| `java.util.concurrent` | Concurrency utilities | ExecutorService, CountDownLatch |
| `java.util.stream` | Stream API (Java 8+) | Stream, Collectors |

**Usage:**

```java
// 1. Import specific class
import java.util.ArrayList;
import java.util.HashMap;

// 2. Import all classes from package (not recommended)
import java.util.*;

// 3. Use fully qualified name (no import needed)
java.util.ArrayList<String> list = new java.util.ArrayList<>();

// 4. Static import (for static members)
import static java.lang.Math.PI;
import static java.lang.Math.sqrt;

public class PackageDemo {
    public static void main(String[] args) {
        // Using imported classes
        ArrayList<String> list = new ArrayList<>();
        HashMap<String, Integer> map = new HashMap<>();
        
        // Using static imports
        System.out.println("PI: " + PI);
        System.out.println("sqrt(16): " + sqrt(16));
    }
}
```

---

### Creating Custom Packages

#### Step 1: Declare Package

```java
// File: com/mycompany/utils/Calculator.java
package com.mycompany.utils;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int subtract(int a, int b) {
        return a - b;
    }
    
    public int multiply(int a, int b) {
        return a * b;
    }
    
    public double divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero");
        }
        return (double) a / b;
    }
}
```

#### Step 2: Use Package

```java
// File: Main.java
import com.mycompany.utils.Calculator;

public class Main {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        
        System.out.println("10 + 5 = " + calc.add(10, 5));
        System.out.println("10 - 5 = " + calc.subtract(10, 5));
        System.out.println("10 * 5 = " + calc.multiply(10, 5));
        System.out.println("10 / 5 = " + calc.divide(10, 5));
    }
}
```

#### Directory Structure

```
project/
├── com/
│   └── mycompany/
│       └── utils/
│           └── Calculator.java
└── Main.java
```

#### Compilation & Execution

```bash
# Compile
javac com/mycompany/utils/Calculator.java
javac Main.java

# Run
java Main
```

---

### Access Modifiers in Packages

**4 Access Levels:**

| Modifier | Same Class | Same Package | Subclass (Different Package) | World |
|----------|------------|--------------|------------------------------|-------|
| **public** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **protected** | ✅ Yes | ✅ Yes | ✅ Yes | ❌ No |
| **default** (no modifier) | ✅ Yes | ✅ Yes | ❌ No | ❌ No |
| **private** | ✅ Yes | ❌ No | ❌ No | ❌ No |

**Example:**

```java
// File: com/mycompany/A.java
package com.mycompany;

public class A {
    public int publicVar = 1;
    protected int protectedVar = 2;
    int defaultVar = 3;  // Package-private
    private int privateVar = 4;
    
    public void display() {
        System.out.println("Public: " + publicVar);
        System.out.println("Protected: " + protectedVar);
        System.out.println("Default: " + defaultVar);
        System.out.println("Private: " + privateVar);
    }
}
```

```java
// File: com/mycompany/B.java (Same package)
package com.mycompany;

public class B {
    public void access() {
        A obj = new A();
        System.out.println(obj.publicVar);     // ✅ OK
        System.out.println(obj.protectedVar);  // ✅ OK
        System.out.println(obj.defaultVar);    // ✅ OK
        // System.out.println(obj.privateVar); // ❌ Error
    }
}
```

```java
// File: com/other/C.java (Different package)
package com.other;

import com.mycompany.A;

public class C extends A {
    public void access() {
        System.out.println(publicVar);     // ✅ OK
        System.out.println(protectedVar);  // ✅ OK (inherited)
        // System.out.println(defaultVar); // ❌ Error
        // System.out.println(privateVar); // ❌ Error
    }
}
```

```java
// File: com/other/D.java (Different package, no inheritance)
package com.other;

import com.mycompany.A;

public class D {
    public void access() {
        A obj = new A();
        System.out.println(obj.publicVar);     // ✅ OK
        // System.out.println(obj.protectedVar); // ❌ Error
        // System.out.println(obj.defaultVar);   // ❌ Error
        // System.out.println(obj.privateVar);   // ❌ Error
    }
}
```

**Best Practices:**
1. ✅ Use **public** for API classes/methods
2. ✅ Use **protected** for methods that subclasses should override
3. ✅ Use **default** (package-private) for helper classes
4. ✅ Use **private** for implementation details
5. ✅ Follow naming convention: `com.company.project.module`

---

## 🎯 Wrapper Classes

**Theory:**
Wrapper classes convert **primitive types into objects**. Each primitive has a corresponding wrapper class.

### Primitive to Wrapper Mapping

| Primitive | Wrapper Class | Example |
|-----------|---------------|---------|
| byte | Byte | `Byte b = 10;` |
| short | Short | `Short s = 100;` |
| int | Integer | `Integer i = 100;` |
| long | Long | `Long l = 1000L;` |
| float | Float | `Float f = 10.5f;` |
| double | Double | `Double d = 10.5;` |
| char | Character | `Character c = 'A';` |
| boolean | Boolean | `Boolean b = true;` |

### Why Wrapper Classes?

1. ✅ **Collections**: Collections can only store objects, not primitives
2. ✅ **Generics**: Generics work with objects only
3. ✅ **Null Support**: Primitives cannot be null, wrappers can
4. ✅ **Utility Methods**: Wrappers provide utility methods
5. ✅ **Object-Oriented**: Can use object-oriented features

---

### Autoboxing and Unboxing (Java 5+)

**Autoboxing**: Automatic conversion from primitive to wrapper

**Unboxing**: Automatic conversion from wrapper to primitive

```java
public class AutoboxingDemo {
    public static void main(String[] args) {
        // AUTOBOXING - Primitive to Wrapper (automatic)
        Integer obj1 = 100;              // int → Integer
        Double obj2 = 10.5;              // double → Double
        Character obj3 = 'A';            // char → Character
        Boolean obj4 = true;             // boolean → Boolean
        
        // UNBOXING - Wrapper to Primitive (automatic)
        int num1 = obj1;                 // Integer → int
        double num2 = obj2;              // Double → double
        char ch = obj3;                  // Character → char
        boolean flag = obj4;             // Boolean → boolean
        
        // Before Java 5 (Manual boxing/unboxing)
        Integer obj5 = Integer.valueOf(100);  // Manual boxing
        int num3 = obj5.intValue();           // Manual unboxing
        
        System.out.println("Autoboxing: " + obj1);
        System.out.println("Unboxing: " + num1);
    }
}
```

**Autoboxing in Collections:**

```java
import java.util.*;

public class AutoboxingInCollections {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        
        // Autoboxing - int primitives automatically boxed to Integer objects
        list.add(10);    // int → Integer (autoboxing)
        list.add(20);
        list.add(30);
        
        // Unboxing - Integer objects automatically unboxed to int primitives
        int sum = 0;
        for (Integer num : list) {
            sum += num;  // Integer → int (unboxing)
        }
        
        System.out.println("Sum: " + sum);
    }
}
```

---

### Important Wrapper Methods

```java
public class WrapperMethodsDemo {
    public static void main(String[] args) {
        // ========== INTEGER METHODS ==========
        
        // 1. valueOf() - String to Integer (returns object)
        Integer obj1 = Integer.valueOf("100");
        Integer obj2 = Integer.valueOf(200);
        System.out.println("valueOf: " + obj1);
        
        // 2. parseInt() - String to int (returns primitive)
        int num1 = Integer.parseInt("100");
        int num2 = Integer.parseInt("1010", 2);  // Binary to decimal
        System.out.println("parseInt: " + num1);
        System.out.println("Binary 1010 = " + num2);
        
        // 3. toString() - Integer to String
        String str1 = Integer.toString(100);
        String str2 = Integer.toString(10, 2);  // Decimal to binary
        System.out.println("toString: " + str1);
        System.out.println("10 in binary: " + str2);
        
        // 4. intValue() - Integer to int
        Integer obj3 = 100;
        int num3 = obj3.intValue();
        System.out.println("intValue: " + num3);
        
        // 5. Comparison
        Integer a = 100;
        Integer b = 200;
        System.out.println("compareTo: " + a.compareTo(b));  // -1 (a < b)
        System.out.println("equals: " + a.equals(b));        // false
        System.out.println("compare: " + Integer.compare(a, b));  // -1
        
        // 6. Min/Max values
        System.out.println("MAX_VALUE: " + Integer.MAX_VALUE);
        System.out.println("MIN_VALUE: " + Integer.MIN_VALUE);
        
        // 7. Number of bits
        System.out.println("SIZE (bits): " + Integer.SIZE);
        System.out.println("BYTES: " + Integer.BYTES);
        
        // ========== DOUBLE METHODS ==========
        
        Double d1 = Double.valueOf("10.5");
        double d2 = Double.parseDouble("20.5");
        String s1 = Double.toString(30.5);
        double d3 = d1.doubleValue();
        
        System.out.println("\nDouble valueOf: " + d1);
        System.out.println("parseDouble: " + d2);
        
        // Special values
        System.out.println("isNaN: " + Double.isNaN(0.0 / 0.0));  // true
        System.out.println("isInfinite: " + Double.isInfinite(1.0 / 0.0));  // true
        
        // ========== CHARACTER METHODS ==========
        
        char ch = 'A';
        System.out.println("\nisDigit: " + Character.isDigit(ch));        // false
        System.out.println("isLetter: " + Character.isLetter(ch));        // true
        System.out.println("isUpperCase: " + Character.isUpperCase(ch));  // true
        System.out.println("isLowerCase: " + Character.isLowerCase(ch));  // false
        System.out.println("toUpperCase: " + Character.toUpperCase('a')); // A
        System.out.println("toLowerCase: " + Character.toLowerCase('A')); // a
        System.out.println("isWhitespace: " + Character.isWhitespace(' '));  // true
        
        // ========== BOOLEAN METHODS ==========
        
        Boolean bool1 = Boolean.valueOf("true");
        boolean bool2 = Boolean.parseBoolean("false");
        String str2 = Boolean.toString(true);
        
        System.out.println("\nBoolean valueOf: " + bool1);
        System.out.println("parseBoolean: " + bool2);
        System.out.println("logicalAnd: " + Boolean.logicalAnd(true, false));  // false
        System.out.println("logicalOr: " + Boolean.logicalOr(true, false));    // true
    }
}
```

---

### Caching (Integer Cache)

**Important Interview Topic!**

Java caches Integer objects in range **-128 to 127** for performance.

```java
public class IntegerCacheDemo {
    public static void main(String[] args) {
        // Within cache range (-128 to 127)
        Integer a = 127;
        Integer b = 127;
        System.out.println(a == b);       // true (same object from cache)
        System.out.println(a.equals(b));  // true (same value)
        
        // Outside cache range
        Integer x = 128;
        Integer y = 128;
        System.out.println(x == y);       // false (different objects)
        System.out.println(x.equals(y));  // true (same value)
        
        // Using new (always creates new object)
        Integer p = new Integer(127);
        Integer q = new Integer(127);
        System.out.println(p == q);       // false (different objects)
        System.out.println(p.equals(q));  // true (same value)
        
        // Best practice: Always use equals() for value comparison
    }
}
```

**Output:**
```
true
true
false
true
false
true
```

**Cache Ranges:**

| Wrapper | Cache Range |
|---------|-------------|
| Byte | -128 to 127 (all values) |
| Short | -128 to 127 |
| Integer | -128 to 127 (configurable) |
| Long | -128 to 127 |
| Character | 0 to 127 |
| Boolean | true and false |

---

### Primitive vs Object Types

```java
public class PrimitiveVsWrapperDemo {
    public static void main(String[] args) {
        // Performance comparison
        long startTime, endTime;
        
        // Primitive (Fast)
        startTime = System.currentTimeMillis();
        long sum1 = 0L;
        for (long i = 0; i < 1000000000L; i++) {
            sum1 += i;
        }
        endTime = System.currentTimeMillis();
        System.out.println("Primitive time: " + (endTime - startTime) + "ms");
        
        // Wrapper (Slow - autoboxing overhead)
        startTime = System.currentTimeMillis();
        Long sum2 = 0L;
        for (long i = 0; i < 1000000000L; i++) {
            sum2 += i;  // Autoboxing/unboxing overhead
        }
        endTime = System.currentTimeMillis();
        System.out.println("Wrapper time: " + (endTime - startTime) + "ms");
        
        // Null support
        int primitiveInt = 0;      // Cannot be null
        Integer wrapperInt = null;  // Can be null
        
        // if (primitiveInt == null) {}  // ❌ Compilation error
        if (wrapperInt == null) {      // ✅ OK
            System.out.println("Wrapper can be null");
        }
        
        // Collections (must use wrappers)
        // List<int> list1 = new ArrayList<>();  // ❌ Error
        List<Integer> list2 = new ArrayList<>();  // ✅ OK
    }
}
```

**When to Use:**

| Use Primitive When | Use Wrapper When |
|-------------------|------------------|
| Performance critical | Need null support |
| Local variables | Collections/Generics |
| Arrays | Object methods needed |
| Mathematical operations | Serialization |
| Simple data types | Database nullable columns |

---

## 🎯 Annotations

**Theory:**
Annotations provide **metadata** about code. They don't affect program execution directly but can be used by compiler, runtime, or tools.

**Uses:**
1. ✅ **Compiler Instructions**: `@Override`, `@SuppressWarnings`
2. ✅ **Runtime Processing**: Reflection, Dependency Injection
3. ✅ **Code Generation**: Lombok, JPA
4. ✅ **Documentation**: `@Deprecated`, `@Author`
5. ✅ **Testing**: `@Test`, `@Before`, `@After`

**Syntax:**
```java
@AnnotationName
@AnnotationName(element = value)
@AnnotationName(element1 = value1, element2 = value2)
```

---

### Built-in Annotations

#### 1. @Override

**Indicates method overrides** superclass method.

```java
class Animal {
    public void makeSound() {
        System.out.println("Animal makes sound");
    }
}

class Dog extends Animal {
    @Override  // ✅ Compiler checks if method exists in superclass
    public void makeSound() {
        System.out.println("Dog barks");
    }
    
    // @Override  // ❌ Compilation error - no such method in superclass
    // public void makeSond() {  // Typo caught!
    //     System.out.println("Dog barks");
    // }
}

public class OverrideDemo {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.makeSound();  // Output: Dog barks
    }
}
```

**Benefits:**
- ✅ Catches typos in method names
- ✅ Ensures method actually overrides
- ✅ Better code readability

#### 2. @Deprecated

**Marks code as deprecated** (discouraged from use).

```java
class OldLibrary {
    // Old method (deprecated)
    @Deprecated
    public void oldMethod() {
        System.out.println("Old method - Don't use!");
    }
    
    // New method
    public void newMethod() {
        System.out.println("New method - Use this!");
    }
}

public class DeprecatedDemo {
    public static void main(String[] args) {
        OldLibrary lib = new OldLibrary();
        
        lib.oldMethod();  // ⚠️ Compiler warning: deprecated
        lib.newMethod();  // ✅ No warning
    }
}
```

**Javadoc with Deprecated:**
```java
/**
 * Old method for calculations
 * @deprecated Use {@link #newMethod()} instead
 */
@Deprecated
public void oldMethod() {
    // Old implementation
}
```

#### 3. @SuppressWarnings

**Suppresses compiler warnings**.

```java
import java.util.*;

public class SuppressWarningsDemo {
    // Suppress unchecked warning
    @SuppressWarnings("unchecked")
    public void method1() {
        List list = new ArrayList();  // Raw type
        list.add("Hello");  // Unchecked operation
    }
    
    // Suppress unused warning
    @SuppressWarnings("unused")
    public void method2() {
        int unusedVariable = 10;  // Not used anywhere
    }
    
    // Suppress multiple warnings
    @SuppressWarnings({"unchecked", "unused"})
    public void method3() {
        List list = new ArrayList();
        int unusedVar = 5;
    }
    
    // Suppress all warnings (not recommended)
    @SuppressWarnings("all")
    public void method4() {
        // All warnings suppressed
    }
    
    public static void main(String[] args) {
        SuppressWarningsDemo demo = new SuppressWarningsDemo();
        demo.method1();
    }
}
```

**Common Warning Types:**
- `"unchecked"` - Unchecked operations
- `"unused"` - Unused variables/methods
- `"deprecation"` - Deprecated API usage
- `"rawtypes"` - Raw type usage
- `"all"` - All warnings

#### 4. @FunctionalInterface (Java 8+)

**Marks interface as functional** (single abstract method).

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);  // Single abstract method (SAM)
    
    // default and static methods allowed
    default void display() {
        System.out.println("Calculator interface");
    }
    
    static void info() {
        System.out.println("Info");
    }
}

public class FunctionalInterfaceDemo {
    public static void main(String[] args) {
        // Lambda expression (only works with functional interfaces)
        Calculator add = (a, b) -> a + b;
        Calculator multiply = (a, b) -> a * b;
        
        System.out.println("10 + 5 = " + add.calculate(10, 5));
        System.out.println("10 * 5 = " + multiply.calculate(10, 5));
    }
}
```

**Benefits:**
- ✅ Compiler ensures single abstract method
- ✅ Can use lambda expressions
- ✅ Better documentation

#### 5. @SafeVarargs (Java 7+)

**Suppresses warnings** for varargs with generic types.

```java
import java.util.*;

public class SafeVarargsDemo {
    // Generic varargs method
    @SafeVarargs
    public static <T> void printAll(T... items) {
        for (T item : items) {
            System.out.println(item);
        }
    }
    
    @SafeVarargs
    public static <T> List<T> asList(T... items) {
        return Arrays.asList(items);
    }
    
    public static void main(String[] args) {
        printAll("Apple", "Banana", "Cherry");
        printAll(1, 2, 3, 4, 5);
        
        List<String> fruits = asList("Apple", "Banana", "Cherry");
        System.out.println(fruits);
    }
}
```

---

### Custom Annotations

**Creating custom annotations:**

```java
import java.lang.annotation.*;

// Define custom annotation
@Retention(RetentionPolicy.RUNTIME)  // Available at runtime
@Target(ElementType.METHOD)          // Can be applied to methods
public @interface MyAnnotation {
    String value();           // Single element
    int priority() default 1; // Element with default value
}

// Using custom annotation
class Service {
    @MyAnnotation(value = "Process data", priority = 5)
    public void processData() {
        System.out.println("Processing data...");
    }
    
    @MyAnnotation("Quick task")  // Short form (if only one element named "value")
    public void quickTask() {
        System.out.println("Quick task");
    }
}

// Reading annotation at runtime using Reflection
public class CustomAnnotationDemo {
    public static void main(String[] args) throws Exception {
        Service service = new Service();
        
        // Get method
        java.lang.reflect.Method method = service.getClass().getMethod("processData");
        
        // Check if annotation present
        if (method.isAnnotationPresent(MyAnnotation.class)) {
            MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);
            System.out.println("Value: " + annotation.value());
            System.out.println("Priority: " + annotation.priority());
        }
    }
}
```

---

### Meta-Annotations

**Annotations that annotate other annotations:**

#### 1. @Retention

**Specifies how long annotation is retained**.

```java
@Retention(RetentionPolicy.SOURCE)   // Discarded by compiler
@Retention(RetentionPolicy.CLASS)    // Stored in .class file, not available at runtime (default)
@Retention(RetentionPolicy.RUNTIME)  // Available at runtime (for reflection)
```

#### 2. @Target

**Specifies where annotation can be applied**.

```java
@Target(ElementType.TYPE)            // Class, interface, enum
@Target(ElementType.METHOD)          // Methods
@Target(ElementType.FIELD)           // Fields
@Target(ElementType.PARAMETER)       // Method parameters
@Target(ElementType.CONSTRUCTOR)     // Constructors
@Target(ElementType.LOCAL_VARIABLE)  // Local variables
@Target(ElementType.ANNOTATION_TYPE) // Annotations
@Target(ElementType.PACKAGE)         // Packages

// Multiple targets
@Target({ElementType.METHOD, ElementType.FIELD})
```

#### 3. @Documented

**Includes annotation in Javadoc**.

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Important {
    String value();
}
```

#### 4. @Inherited

**Annotation is inherited by subclasses**.

```java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface ParentAnnotation {
}

@ParentAnnotation
class Parent {
}

// Child class inherits @ParentAnnotation
class Child extends Parent {
}
```

---

### Complete Custom Annotation Example

```java
import java.lang.annotation.*;
import java.lang.reflect.Method;

// 1. Define annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Test {
    int priority() default 1;
    String author() default "Unknown";
}

// 2. Use annotation
class TestSuite {
    @Test(priority = 1, author = "John")
    public void test1() {
        System.out.println("Test 1 executed");
    }
    
    @Test(priority = 2, author = "Jane")
    public void test2() {
        System.out.println("Test 2 executed");
    }
    
    @Test(priority = 3)
    public void test3() {
        System.out.println("Test 3 executed");
    }
    
    public void notATest() {
        System.out.println("Not a test");
    }
}

// 3. Process annotations using Reflection
public class AnnotationProcessor {
    public static void main(String[] args) throws Exception {
        TestSuite suite = new TestSuite();
        
        // Get all methods
        Method[] methods = suite.getClass().getDeclaredMethods();
        
        // Execute methods with @Test annotation
        System.out.println("Executing tests:\n");
        for (Method method : methods) {
            if (method.isAnnotationPresent(Test.class)) {
                Test test = method.getAnnotation(Test.class);
                System.out.println("Priority: " + test.priority());
                System.out.println("Author: " + test.author());
                method.invoke(suite);  // Execute method
                System.out.println();
            }
        }
    }
}
```

**Output:**
```
Executing tests:

Priority: 1
Author: John
Test 1 executed

Priority: 2
Author: Jane
Test 2 executed

Priority: 3
Author: Unknown
Test 3 executed
```

---

## 🎯 Reflection API

**Theory:**
Reflection is the ability of a program to **examine and modify** its own structure and behavior at runtime. It allows:
- Inspecting classes, interfaces, fields, methods
- Creating objects dynamically
- Invoking methods dynamically
- Accessing/modifying private fields

**java.lang.reflect Package:**
- `Class<T>` - Represents a class
- `Method` - Represents a method
- `Field` - Represents a field
- `Constructor` - Represents a constructor

**Uses:**
1. ✅ **Frameworks**: Spring, Hibernate (Dependency Injection, ORM)
2. ✅ **Testing**: JUnit, Mockito
3. ✅ **Serialization**: JSON, XML parsing
4. ✅ **IDEs**: Code completion, refactoring
5. ✅ **Annotations Processing**: Reading annotations at runtime

**Drawbacks:**
1. ❌ **Performance Overhead**: Slower than direct calls
2. ❌ **Security**: Can access private members
3. ❌ **Complexity**: Harder to understand and maintain

---

### Getting Class Object

**3 Ways to get Class object:**

```java
public class GetClassDemo {
    public static void main(String[] args) throws ClassNotFoundException {
        // Method 1: Using .class syntax
        Class<?> class1 = String.class;
        System.out.println("Method 1: " + class1.getName());
        
        // Method 2: Using getClass() method
        String str = "Hello";
        Class<?> class2 = str.getClass();
        System.out.println("Method 2: " + class2.getName());
        
        // Method 3: Using Class.forName() (most common for dynamic loading)
        Class<?> class3 = Class.forName("java.lang.String");
        System.out.println("Method 3: " + class3.getName());
        
        // All three refer to same Class object
        System.out.println("Same class? " + (class1 == class2 && class2 == class3));
    }
}
```

---

### Inspecting Class Metadata

```java
import java.lang.reflect.*;

class Person {
    private String name;
    private int age;
    
    public Person() {
    }
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    private void privateMethod() {
        System.out.println("Private method called");
    }
}

public class ClassMetadataDemo {
    public static void main(String[] args) {
        Class<?> clazz = Person.class;
        
        // 1. Class Name
        System.out.println("Simple Name: " + clazz.getSimpleName());
        System.out.println("Full Name: " + clazz.getName());
        
        // 2. Modifiers
        int modifiers = clazz.getModifiers();
        System.out.println("Is Public? " + Modifier.isPublic(modifiers));
        System.out.println("Is Abstract? " + Modifier.isAbstract(modifiers));
        
        // 3. Superclass
        System.out.println("Superclass: " + clazz.getSuperclass().getSimpleName());
        
        // 4. Interfaces
        System.out.println("\nInterfaces:");
        Class<?>[] interfaces = clazz.getInterfaces();
        for (Class<?> iface : interfaces) {
            System.out.println(" - " + iface.getSimpleName());
        }
        
        // 5. Constructors
        System.out.println("\nConstructors:");
        Constructor<?>[] constructors = clazz.getConstructors();
        for (Constructor<?> constructor : constructors) {
            System.out.println(" - " + constructor);
        }
        
        // 6. Fields (public only)
        System.out.println("\nPublic Fields:");
        Field[] fields = clazz.getFields();
        for (Field field : fields) {
            System.out.println(" - " + field.getName());
        }
        
        // 7. All Fields (including private)
        System.out.println("\nAll Fields:");
        Field[] allFields = clazz.getDeclaredFields();
        for (Field field : allFields) {
            System.out.println(" - " + field.getName() + " (" + field.getType().getSimpleName() + ")");
        }
        
        // 8. Methods (public only)
        System.out.println("\nPublic Methods:");
        Method[] methods = clazz.getMethods();
        for (Method method : methods) {
            if (method.getDeclaringClass() == clazz) {  // Exclude inherited methods
                System.out.println(" - " + method.getName());
            }
        }
        
        // 9. All Methods (including private)
        System.out.println("\nAll Methods:");
        Method[] allMethods = clazz.getDeclaredMethods();
        for (Method method : allMethods) {
            System.out.println(" - " + method.getName());
        }
    }
}
```

---

### Creating Objects Dynamically

```java
import java.lang.reflect.*;

class Product {
    private String name;
    private double price;
    
    // No-arg constructor
    public Product() {
        this.name = "Unknown";
        this.price = 0.0;
    }
    
    // Parameterized constructor
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    @Override
    public String toString() {
        return "Product{name='" + name + "', price=" + price + "}";
    }
}

public class CreateObjectDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("Product");
        
        // Method 1: Using newInstance() (deprecated in Java 9+)
        // Object obj1 = clazz.newInstance();  // Only works with no-arg constructor
        
        // Method 2: Using Constructor
        // Create object using no-arg constructor
        Constructor<?> constructor1 = clazz.getConstructor();
        Object obj1 = constructor1.newInstance();
        System.out.println("No-arg constructor: " + obj1);
        
        // Create object using parameterized constructor
        Constructor<?> constructor2 = clazz.getConstructor(String.class, double.class);
        Object obj2 = constructor2.newInstance("Laptop", 1200.00);
        System.out.println("Parameterized constructor: " + obj2);
    }
}
```

---

### Invoking Methods Dynamically

```java
import java.lang.reflect.*;

class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    private int multiply(int a, int b) {
        return a * b;
    }
    
    public static int subtract(int a, int b) {
        return a - b;
    }
}

public class InvokeMethodDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Calculator.class;
        Object obj = clazz.getConstructor().newInstance();
        
        // 1. Invoke public method
        Method addMethod = clazz.getMethod("add", int.class, int.class);
        Object result1 = addMethod.invoke(obj, 10, 5);
        System.out.println("10 + 5 = " + result1);
        
        // 2. Invoke private method (need to set accessible)
        Method multiplyMethod = clazz.getDeclaredMethod("multiply", int.class, int.class);
        multiplyMethod.setAccessible(true);  // Bypass access control
        Object result2 = multiplyMethod.invoke(obj, 10, 5);
        System.out.println("10 * 5 = " + result2);
        
        // 3. Invoke static method
        Method subtractMethod = clazz.getMethod("subtract", int.class, int.class);
        Object result3 = subtractMethod.invoke(null, 10, 5);  // null for static method
        System.out.println("10 - 5 = " + result3);
    }
}
```

---

### Accessing and Modifying Fields

```java
import java.lang.reflect.*;

class Employee {
    private String name;
    private int salary;
    
    public Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }
    
    @Override
    public String toString() {
        return "Employee{name='" + name + "', salary=" + salary + "}";
    }
}

public class AccessFieldDemo {
    public static void main(String[] args) throws Exception {
        Employee emp = new Employee("John", 50000);
        System.out.println("Before: " + emp);
        
        Class<?> clazz = emp.getClass();
        
        // Access private field "name"
        Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true);  // Bypass access control
        
        // Get value
        String name = (String) nameField.get(emp);
        System.out.println("Name: " + name);
        
        // Set value
        nameField.set(emp, "Jane");
        
        // Access private field "salary"
        Field salaryField = clazz.getDeclaredField("salary");
        salaryField.setAccessible(true);
        
        // Get value
        int salary = (int) salaryField.get(emp);
        System.out.println("Salary: " + salary);
        
        // Set value
        salaryField.set(emp, 60000);
        
        System.out.println("After: " + emp);
    }
}
```

**Output:**
```
Before: Employee{name='John', salary=50000}
Name: John
Salary: 50000
After: Employee{name='Jane', salary=60000}
```

---

### Complete Reflection Example

```java
import java.lang.reflect.*;

class Student {
    private String name;
    private int rollNo;
    
    public Student() {
        this.name = "Unknown";
        this.rollNo = 0;
    }
    
    public Student(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public int getRollNo() {
        return rollNo;
    }
    
    public void setRollNo(int rollNo) {
        this.rollNo = rollNo;
    }
    
    private void display() {
        System.out.println("Student: " + name + " (Roll No: " + rollNo + ")");
    }
    
    @Override
    public String toString() {
        return "Student{name='" + name + "', rollNo=" + rollNo + "}";
    }
}

public class CompleteReflectionDemo {
    public static void main(String[] args) throws Exception {
        // 1. Get Class object
        Class<?> clazz = Student.class;
        System.out.println("=== Class Info ===");
        System.out.println("Class Name: " + clazz.getSimpleName());
        
        // 2. Create object using reflection
        System.out.println("\n=== Creating Objects ===");
        Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
        Student student = (Student) constructor.newInstance("Alice", 101);
        System.out.println("Created: " + student);
        
        // 3. Get all fields
        System.out.println("\n=== Fields ===");
        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            System.out.println(field.getName() + " = " + field.get(student));
        }
        
        // 4. Modify private field
        System.out.println("\n=== Modifying Field ===");
        Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true);
        nameField.set(student, "Bob");
        System.out.println("Modified: " + student);
        
        // 5. Invoke public method
        System.out.println("\n=== Invoking Public Method ===");
        Method getNameMethod = clazz.getMethod("getName");
        String name = (String) getNameMethod.invoke(student);
        System.out.println("getName() returned: " + name);
        
        // 6. Invoke private method
        System.out.println("\n=== Invoking Private Method ===");
        Method displayMethod = clazz.getDeclaredMethod("display");
        displayMethod.setAccessible(true);
        displayMethod.invoke(student);
        
        // 7. List all public methods
        System.out.println("\n=== All Public Methods ===");
        Method[] methods = clazz.getMethods();
        for (Method method : methods) {
            if (method.getDeclaringClass() == clazz) {
                System.out.println(" - " + method.getName());
            }
        }
    }
}
```

**Output:**
```
=== Class Info ===
Class Name: Student

=== Creating Objects ===
Created: Student{name='Alice', rollNo=101}

=== Fields ===
name = Alice
rollNo = 101

=== Modifying Field ===
Modified: Student{name='Bob', rollNo=101}

=== Invoking Public Method ===
getName() returned: Bob

=== Invoking Private Method ===
Student: Bob (Roll No: 101)

=== All Public Methods ===
 - getName
 - setName
 - getRollNo
 - setRollNo
 - toString
```

---

## 🎯 Design Principles & Patterns

### SOLID Principles

**SOLID** is an acronym for five design principles that make software more understandable, flexible, and maintainable.

---

#### 1. Single Responsibility Principle (SRP)

**"A class should have only one reason to change."**

Each class should have **only one responsibility**.

**❌ Bad Example (Multiple Responsibilities):**
```java
class Employee {
    private String name;
    private double salary;
    
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
    
    // Responsibility 1: Calculate salary
    public double calculateSalary() {
        return salary * 12;
    }
    
    // Responsibility 2: Save to database
    public void saveToDatabase() {
        System.out.println("Saving " + name + " to database");
        // Database code
    }
    
    // Responsibility 3: Generate report
    public void generateReport() {
        System.out.println("Generating report for " + name);
        // Report generation code
    }
}
```

**✅ Good Example (Single Responsibility):**
```java
// Class 1: Handles employee data
class Employee {
    private String name;
    private double salary;
    
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
    
    public String getName() {
        return name;
    }
    
    public double getSalary() {
        return salary;
    }
}

// Class 2: Handles salary calculations
class SalaryCalculator {
    public double calculateAnnualSalary(Employee emp) {
        return emp.getSalary() * 12;
    }
}

// Class 3: Handles database operations
class EmployeeRepository {
    public void save(Employee emp) {
        System.out.println("Saving " + emp.getName() + " to database");
    }
}

// Class 4: Handles report generation
class ReportGenerator {
    public void generateReport(Employee emp) {
        System.out.println("Generating report for " + emp.getName());
    }
}
```

---

#### 2. Open/Closed Principle (OCP)

**"Software entities should be open for extension but closed for modification."**

You should be able to **extend** behavior without **modifying** existing code.

**❌ Bad Example:**
```java
class AreaCalculator {
    public double calculateArea(Object shape) {
        if (shape instanceof Circle) {
            Circle circle = (Circle) shape;
            return Math.PI * circle.radius * circle.radius;
        } else if (shape instanceof Rectangle) {
            Rectangle rectangle = (Rectangle) shape;
            return rectangle.length * rectangle.width;
        }
        // Adding new shape requires modifying this method
        return 0;
    }
}
```

**✅ Good Example:**
```java
// Abstract class (closed for modification)
abstract class Shape {
    abstract double calculateArea();
}

// Concrete classes (open for extension)
class Circle extends Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    double calculateArea() {
        return Math.PI * radius * radius;
    }
}

class Rectangle extends Shape {
    private double length;
    private double width;
    
    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }
    
    @Override
    double calculateArea() {
        return length * width;
    }
}

// Adding new shape doesn't require modifying existing code
class Triangle extends Shape {
    private double base;
    private double height;
    
    public Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }
    
    @Override
    double calculateArea() {
        return 0.5 * base * height;
    }
}

// Calculator doesn't need to change
class AreaCalculator {
    public double calculateArea(Shape shape) {
        return shape.calculateArea();
    }
}
```

---

#### 3. Liskov Substitution Principle (LSP)

**"Objects of a superclass should be replaceable with objects of subclass without affecting correctness."**

Subclasses should be **substitutable** for their base classes.

**❌ Bad Example:**
```java
class Bird {
    public void fly() {
        System.out.println("Flying");
    }
}

class Ostrich extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Ostrich can't fly!");
    }
}

public class LSPBadExample {
    public static void makeBirdFly(Bird bird) {
        bird.fly();  // Will fail for Ostrich!
    }
    
    public static void main(String[] args) {
        Bird sparrow = new Bird();
        makeBirdFly(sparrow);  // ✅ OK
        
        Bird ostrich = new Ostrich();
        makeBirdFly(ostrich);  // ❌ Exception!
    }
}
```

**✅ Good Example:**
```java
abstract class Bird {
    abstract void move();
}

class FlyingBird extends Bird {
    @Override
    void move() {
        fly();
    }
    
    private void fly() {
        System.out.println("Flying");
    }
}

class Sparrow extends FlyingBird {
}

class Ostrich extends Bird {
    @Override
    void move() {
        run();
    }
    
    private void run() {
        System.out.println("Running");
    }
}

public class LSPGoodExample {
    public static void makeBirdMove(Bird bird) {
        bird.move();  // Works for all birds
    }
    
    public static void main(String[] args) {
        Bird sparrow = new Sparrow();
        makeBirdMove(sparrow);  // ✅ Flying
        
        Bird ostrich = new Ostrich();
        makeBirdMove(ostrich);  // ✅ Running
    }
}
```

---

#### 4. Interface Segregation Principle (ISP)

**"Clients should not be forced to depend on interfaces they don't use."**

Create **specific interfaces** instead of one general-purpose interface.

**❌ Bad Example:**
```java
interface Worker {
    void work();
    void eat();
    void sleep();
}

class Human implements Worker {
    @Override
    public void work() {
        System.out.println("Human working");
    }
    
    @Override
    public void eat() {
        System.out.println("Human eating");
    }
    
    @Override
    public void sleep() {
        System.out.println("Human sleeping");
    }
}

class Robot implements Worker {
    @Override
    public void work() {
        System.out.println("Robot working");
    }
    
    @Override
    public void eat() {
        // Robot doesn't eat! Forced to implement
        throw new UnsupportedOperationException("Robot doesn't eat");
    }
    
    @Override
    public void sleep() {
        // Robot doesn't sleep! Forced to implement
        throw new UnsupportedOperationException("Robot doesn't sleep");
    }
}
```

**✅ Good Example:**
```java
// Segregated interfaces
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    @Override
    public void work() {
        System.out.println("Human working");
    }
    
    @Override
    public void eat() {
        System.out.println("Human eating");
    }
    
    @Override
    public void sleep() {
        System.out.println("Human sleeping");
    }
}

class Robot implements Workable {
    @Override
    public void work() {
        System.out.println("Robot working");
    }
    // No need to implement eat() and sleep()
}
```

---

#### 5. Dependency Inversion Principle (DIP)

**"Depend on abstractions, not concretions."**

High-level modules should not depend on low-level modules. Both should depend on **abstractions**.

**❌ Bad Example:**
```java
// Low-level module
class MySQLDatabase {
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}

// High-level module depends on low-level module
class UserService {
    private MySQLDatabase database;  // Tight coupling!
    
    public UserService() {
        this.database = new MySQLDatabase();
    }
    
    public void saveUser(String user) {
        database.save(user);
    }
}
// If we want to switch to MongoDB, we need to modify UserService!
```

**✅ Good Example:**
```java
// Abstraction
interface Database {
    void save(String data);
}

// Low-level modules (implementations)
class MySQLDatabase implements Database {
    @Override
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}

class MongoDatabase implements Database {
    @Override
    public void save(String data) {
        System.out.println("Saving to MongoDB: " + data);
    }
}

// High-level module depends on abstraction
class UserService {
    private Database database;  // Depends on abstraction
    
    public UserService(Database database) {  // Dependency Injection
        this.database = database;
    }
    
    public void saveUser(String user) {
        database.save(user);
    }
}

public class DIPGoodExample {
    public static void main(String[] args) {
        // Easy to switch implementations
        UserService service1 = new UserService(new MySQLDatabase());
        service1.saveUser("John");
        
        UserService service2 = new UserService(new MongoDatabase());
        service2.saveUser("Jane");
    }
}
```

**Output:**
```
Saving to MySQL: John
Saving to MongoDB: Jane
```

---

### Common Design Patterns

Design patterns are **reusable solutions** to common software design problems.

#### 1. Singleton Pattern

**Ensures only one instance** of a class exists.

```java
class Singleton {
    // 1. Private static instance
    private static Singleton instance;
    
    // 2. Private constructor (prevents instantiation)
    private Singleton() {
    }
    
    // 3. Public static method to get instance
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    
    public void showMessage() {
        System.out.println("Hello from Singleton!");
    }
}

public class SingletonDemo {
    public static void main(String[] args) {
        // Singleton obj = new Singleton();  // ❌ Error: constructor is private
        
        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstance();
        
        System.out.println("Same instance? " + (obj1 == obj2));  // true
        
        obj1.showMessage();
    }
}
```

**Thread-Safe Singleton:**
```java
class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() {
    }
    
    // Double-checked locking
    public static ThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (ThreadSafeSingleton.class) {
                if (instance == null) {
                    instance = new ThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}
```

---

#### 2. Factory Pattern

**Creates objects without specifying exact class**.

```java
// Product interface
interface Vehicle {
    void drive();
}

// Concrete products
class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a car");
    }
}

class Bike implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Riding a bike");
    }
}

class Truck implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a truck");
    }
}

// Factory
class VehicleFactory {
    public static Vehicle createVehicle(String type) {
        switch (type.toLowerCase()) {
            case "car":
                return new Car();
            case "bike":
                return new Bike();
            case "truck":
                return new Truck();
            default:
                throw new IllegalArgumentException("Unknown vehicle type");
        }
    }
}

public class FactoryPatternDemo {
    public static void main(String[] args) {
        Vehicle car = VehicleFactory.createVehicle("car");
        car.drive();
        
        Vehicle bike = VehicleFactory.createVehicle("bike");
        bike.drive();
        
        Vehicle truck = VehicleFactory.createVehicle("truck");
        truck.drive();
    }
}
```

---

#### 3. Builder Pattern

**Constructs complex objects step by step**.

```java
class Computer {
    // Required parameters
    private String CPU;
    private String RAM;
    
    // Optional parameters
    private String storage;
    private String GPU;
    private String monitor;
    
    private Computer(Builder builder) {
        this.CPU = builder.CPU;
        this.RAM = builder.RAM;
        this.storage = builder.storage;
        this.GPU = builder.GPU;
        this.monitor = builder.monitor;
    }
    
    @Override
    public String toString() {
        return "Computer{CPU='" + CPU + "', RAM='" + RAM + "', storage='" + 
               storage + "', GPU='" + GPU + "', monitor='" + monitor + "'}";
    }
    
    // Builder class
    public static class Builder {
        // Required parameters
        private String CPU;
        private String RAM;
        
        // Optional parameters (with default values)
        private String storage = "256GB SSD";
        private String GPU = "Integrated";
        private String monitor = "None";
        
        public Builder(String CPU, String RAM) {
            this.CPU = CPU;
            this.RAM = RAM;
        }
        
        public Builder storage(String storage) {
            this.storage = storage;
            return this;
        }
        
        public Builder GPU(String GPU) {
            this.GPU = GPU;
            return this;
        }
        
        public Builder monitor(String monitor) {
            this.monitor = monitor;
            return this;
        }
        
        public Computer build() {
            return new Computer(this);
        }
    }
}

public class BuilderPatternDemo {
    public static void main(String[] args) {
        // Basic computer
        Computer basic = new Computer.Builder("Intel i3", "8GB")
                .build();
        System.out.println(basic);
        
        // Gaming computer
        Computer gaming = new Computer.Builder("Intel i9", "32GB")
                .storage("1TB SSD")
                .GPU("RTX 4090")
                .monitor("27\" 4K")
                .build();
        System.out.println(gaming);
    }
}
```

---

#### 4. Observer Pattern

**Notifies multiple objects** when state changes.

```java
import java.util.*;

// Observer interface
interface Observer {
    void update(String message);
}

// Subject (Observable)
class Subject {
    private List<Observer> observers = new ArrayList<>();
    private String state;
    
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    public void detach(Observer observer) {
        observers.remove(observer);
    }
    
    public void setState(String state) {
        this.state = state;
        notifyObservers();
    }
    
    private void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(state);
        }
    }
}

// Concrete Observers
class EmailObserver implements Observer {
    private String email;
    
    public EmailObserver(String email) {
        this.email = email;
    }
    
    @Override
    public void update(String message) {
        System.out.println("Email to " + email + ": " + message);
    }
}

class SMSObserver implements Observer {
    private String phone;
    
    public SMSObserver(String phone) {
        this.phone = phone;
    }
    
    @Override
    public void update(String message) {
        System.out.println("SMS to " + phone + ": " + message);
    }
}

public class ObserverPatternDemo {
    public static void main(String[] args) {
        Subject subject = new Subject();
        
        // Register observers
        Observer email1 = new EmailObserver("user1@example.com");
        Observer email2 = new EmailObserver("user2@example.com");
        Observer sms1 = new SMSObserver("+1234567890");
        
        subject.attach(email1);
        subject.attach(email2);
        subject.attach(sms1);
        
        // Change state (all observers notified)
        subject.setState("New product launched!");
        
        System.out.println("\n--- Detaching email2 ---\n");
        subject.detach(email2);
        
        subject.setState("50% discount today!");
    }
}
```

---

## 🎯 Key Interview Questions

### Multithreading Questions

**Q1: What is the difference between process and thread?**

| Feature | Process | Thread |
|---------|---------|--------|
| **Definition** | Independent program | Lightweight unit within process |
| **Memory** | Separate memory space | Shares memory with other threads |
| **Creation** | Expensive | Less expensive |
| **Communication** | Complex (IPC) | Simple (shared memory) |
| **Switching** | Slower | Faster |

**Q2: What is the difference between start() and run() method?**

- `start()` creates new thread and calls `run()` in that thread
- `run()` executes in current thread (no new thread created)
- Calling `run()` directly defeats the purpose of multithreading

**Q3: What is the difference between wait() and sleep()?**

| Feature | wait() | sleep() |
|---------|--------|---------|
| **Class** | Object | Thread |
| **Lock** | Releases lock | Does NOT release lock |
| **Wakeup** | notify()/notifyAll() | After timeout |
| **Context** | Synchronized block | Anywhere |

**Q4: What is deadlock? How to prevent it?**

Deadlock occurs when threads wait for each other indefinitely.

**Prevention:**
1. Lock ordering - Always acquire locks in same order
2. Lock timeout - Use tryLock() with timeout
3. Avoid nested locks
4. Use higher-level concurrency utilities

**Q5: What is the difference between synchronized method and synchronized block?**

- **Synchronized method**: Locks entire object
- **Synchronized block**: Locks specific object (more granular control)

**Q6: What is volatile keyword?**

- Ensures changes to variable are immediately visible to all threads
- Does NOT provide atomicity
- Use for simple flags/status variables

**Q7: What is thread pool? Why use it?**

Thread pool reuses threads instead of creating new ones.

**Benefits:**
- Reduces overhead of thread creation/destruction
- Limits number of concurrent threads
- Better resource management

---

### Design Principles Questions

**Q1: Explain SOLID principles?**

- **S**: Single Responsibility - One class, one responsibility
- **O**: Open/Closed - Open for extension, closed for modification
- **L**: Liskov Substitution - Subclasses should be substitutable
- **I**: Interface Segregation - Specific interfaces, not general
- **D**: Dependency Inversion - Depend on abstractions, not concretions

**Q2: What is Singleton pattern? Is it thread-safe?**

Singleton ensures only one instance exists.

**Not thread-safe** by default. Use:
- Double-checked locking with volatile
- Synchronized method
- Enum (best approach)

**Q3: When to use Factory pattern?**

- When object creation is complex
- When you need to decouple client from concrete classes
- When you want centralized object creation

---

## ✅ Cheatsheet - Quick Reference

### 🔹 Thread Lifecycle
```
NEW → RUNNABLE → RUNNING → BLOCKED/WAITING/TIMED_WAITING → TERMINATED
```

### 🔹 Creating Threads
```java
// Method 1: Extend Thread
class MyThread extends Thread {
    public void run() { }
}
new MyThread().start();

// Method 2: Implement Runnable (preferred)
class MyTask implements Runnable {
    public void run() { }
}
new Thread(new MyTask()).start();

// Method 3: Lambda
new Thread(() -> { /* code */ }).start();
```

### 🔹 Thread Methods
| Method | Description |
|--------|-------------|
| `start()` | Start new thread |
| `run()` | Code to execute |
| `sleep(ms)` | Pause execution |
| `join()` | Wait for thread to finish |
| `yield()` | Hint to scheduler |
| `interrupt()` | Interrupt thread |
| `setDaemon(true)` | Set as daemon thread |

### 🔹 Synchronization
```java
// Synchronized method
public synchronized void method() { }

// Synchronized block
synchronized(this) { }

// Static synchronization
public static synchronized void method() { }
```

### 🔹 Inter-thread Communication
```java
wait()       // Release lock and wait
notify()     // Wake one waiting thread
notifyAll()  // Wake all waiting threads
```

### 🔹 Executor Framework
```java
ExecutorService executor = Executors.newFixedThreadPool(3);
executor.submit(() -> { /* task */ });
executor.shutdown();
```

### 🔹 Access Modifiers
| Modifier | Same Class | Same Package | Subclass | World |
|----------|------------|--------------|----------|-------|
| public | ✅ | ✅ | ✅ | ✅ |
| protected | ✅ | ✅ | ✅ | ❌ |
| default | ✅ | ✅ | ❌ | ❌ |
| private | ✅ | ❌ | ❌ | ❌ |

### 🔹 Wrapper Classes
```java
Primitive → Wrapper: int→Integer, double→Double, char→Character

Autoboxing:   Integer obj = 10;  // int → Integer
Unboxing:     int num = obj;     // Integer → int

valueOf():    Integer.valueOf("100")
parseInt():   Integer.parseInt("100")
toString():   Integer.toString(100)
```

### 🔹 Annotations
```java
@Override          // Method overrides superclass
@Deprecated        // Marked as deprecated
@SuppressWarnings  // Suppress warnings
@FunctionalInterface  // Functional interface (SAM)
```

### 🔹 Reflection
```java
// Get Class
Class<?> clazz = String.class;
Class<?> clazz = obj.getClass();
Class<?> clazz = Class.forName("ClassName");

// Create object
Constructor<?> cons = clazz.getConstructor();
Object obj = cons.newInstance();

// Invoke method
Method method = clazz.getMethod("methodName");
method.invoke(obj);

// Access field
Field field = clazz.getDeclaredField("fieldName");
field.setAccessible(true);
field.get(obj);
field.set(obj, value);
```

### 🔹 SOLID Principles
```
S - Single Responsibility: One class, one job
O - Open/Closed: Open for extension, closed for modification
L - Liskov Substitution: Subclasses substitutable for base class
I - Interface Segregation: Many specific interfaces
D - Dependency Inversion: Depend on abstractions
```

### 🔹 Common Design Patterns
```
Singleton    - One instance only
Factory      - Create objects without specifying class
Builder      - Construct complex objects step by step
Observer     - Notify multiple objects of state changes
```

---

## 📝 Practice Questions

1. Write a program demonstrating thread creation using all three methods
2. Implement a producer-consumer problem using wait() and notify()
3. Create a thread pool and submit 10 tasks to it
4. Demonstrate deadlock and then fix it
5. Implement a thread-safe Singleton class
6. Create a custom annotation and process it using Reflection
7. Use Reflection to invoke private methods and access private fields
8. Implement Factory pattern for creating different shapes
9. Implement Builder pattern for creating a complex object (like Pizza with toppings)
10. Demonstrate all SOLID principles with real examples
11. Write a program showing the difference between synchronized method and block
12. Create a custom thread pool using ThreadPoolExecutor
13. Implement Observer pattern for a news subscription system
14. Use Callable and Future to perform parallel computations
15. Demonstrate volatile keyword with a flag-based thread termination

---

## ✅ Key Takeaways

### Part 8 Summary:

**1. Multithreading:**
- ✅ Threads share memory, processes don't
- ✅ Use `start()` (not `run()`) to create new thread
- ✅ Synchronization prevents race conditions
- ✅ `wait()` releases lock, `sleep()` doesn't
- ✅ Executor Framework > Manual thread creation
- ✅ Callable returns result, Runnable doesn't

**2. Packages:**
- ✅ Organize related classes
- ✅ Avoid naming conflicts
- ✅ Control access with modifiers
- ✅ Follow naming convention: com.company.project

**3. Wrapper Classes:**
- ✅ Convert primitives to objects
- ✅ Autoboxing/Unboxing (Java 5+)
- ✅ Integer cache: -128 to 127
- ✅ Always use `equals()` for value comparison
- ✅ Primitives faster than wrappers

**4. Annotations:**
- ✅ Provide metadata about code
- ✅ `@Override` catches overriding mistakes
- ✅ `@Deprecated` marks old code
- ✅ Custom annotations with Reflection
- ✅ Meta-annotations: @Retention, @Target

**5. Reflection:**
- ✅ Examine/modify code at runtime
- ✅ Create objects dynamically
- ✅ Invoke methods dynamically
- ✅ Access private members (with setAccessible)
- ✅ Used by frameworks (Spring, Hibernate)

**6. Design Principles:**
- ✅ SOLID makes code maintainable
- ✅ Single Responsibility: One job per class
- ✅ Open/Closed: Extend, don't modify
- ✅ Depend on abstractions, not implementations
- ✅ Design patterns solve common problems

---

**End of Part 8 Study Material**

This comprehensive guide covers all essential concepts of Part 8 with detailed explanations, code examples, and interview questions. Practice the code examples and questions to master these concepts! 🚀
