# Week 5 – Class 10: Queue

---

# Session 10: Queue

## 1. What is a Queue?

A **Queue** is a linear data structure that follows the **FIFO** principle — **First In, First Out**. The element that is inserted **first** is the one that gets removed **first**.

Think of a queue as a **line of people waiting at a bank counter**:
- The person who arrived **first** gets served **first**.
- New people join at the **back (rear)**.
- People leave from the **front**.

### Key Terms

| Term  | Meaning |
|-------|---------|
| **Enqueue** | Add an element to the rear (back) of the queue |
| **Dequeue** | Remove an element from the front of the queue |
| **Front**   | The first element in the queue (next to be removed) |
| **Rear**    | The last element in the queue (most recently added) |
| **Peek**    | View the front element without removing it |
| **isEmpty** | Check if the queue has no elements |

---

## 2. Queue vs Stack

| Feature        | Stack (LIFO)             | Queue (FIFO)              |
|----------------|--------------------------|---------------------------|
| Order          | Last In, First Out       | First In, First Out       |
| Insert         | Push → at top            | Enqueue → at rear         |
| Remove         | Pop → from top           | Dequeue → from front      |
| Real-world     | Undo history, call stack | Bank line, printer, BFS   |

---

## 3. Queue Operations & Complexity

| Operation   | Description                          | Time Complexity |
|-------------|--------------------------------------|-----------------|
| `enqueue()` | Add element at rear                  | O(1)            |
| `dequeue()` | Remove element from front            | O(1)            |
| `peek()`    | Return front element without removal | O(1)            |
| `isEmpty()` | Check if queue is empty              | O(1)            |
| `size()`    | Return number of elements            | O(1)            |

---

## 4. Queue Implementation Using Array

The simplest implementation uses a fixed-size array with `front` and `rear` pointer variables.

```java
public class QueueUsingArray {
    private int[] arr;       // Array to store queue elements
    private int front;       // Index of the front element
    private int rear;        // Index of the rear element
    private int size;        // Current number of elements
    private int capacity;    // Maximum capacity of the queue

    // Constructor: initialize the queue with a given capacity
    public QueueUsingArray(int capacity) {
        this.capacity = capacity;
        arr = new int[capacity];
        front = 0;
        rear = -1;    // -1 means queue is empty
        size = 0;
    }

    // Enqueue: add element to the rear
    public void enqueue(int data) {
        if (size == capacity) {
            System.out.println("Queue is FULL — cannot enqueue " + data);
            return;
        }
        rear++;               // Move rear pointer forward
        arr[rear] = data;     // Place the new element at rear
        size++;               // Increment count
        System.out.println("Enqueued: " + data);
    }

    // Dequeue: remove element from the front
    public int dequeue() {
        if (isEmpty()) {
            System.out.println("Queue is EMPTY — cannot dequeue");
            return -1;
        }
        int removed = arr[front]; // Capture front element
        front++;                  // Move front pointer forward
        size--;                   // Decrement count
        return removed;
    }

    // Peek: view front element without removing it
    public int peek() {
        if (isEmpty()) {
            System.out.println("Queue is EMPTY");
            return -1;
        }
        return arr[front]; // Return front without changing pointers
    }

    // Check if queue is empty
    public boolean isEmpty() {
        return size == 0;
    }

    public static void main(String[] args) {
        QueueUsingArray q = new QueueUsingArray(5);

        q.enqueue(10);          // Queue: [10]
        q.enqueue(20);          // Queue: [10, 20]
        q.enqueue(30);          // Queue: [10, 20, 30]

        System.out.println("Front: " + q.peek());       // 10
        System.out.println("Dequeued: " + q.dequeue()); // 10
        System.out.println("Front now: " + q.peek());   // 20
    }
}
```

**Limitation of Simple Array Queue:** The `front` pointer keeps moving forward, wasting space. A **Circular Queue** solves this.

---

## 5. Circular Queue

In a **Circular Queue**, when the `rear` reaches the end of the array, it wraps back to index `0` (if space is available at the front). This prevents wasted space.

**Key formula:** `rear = (rear + 1) % capacity`

```java
public class CircularQueue {
    private int[] arr;
    private int front;
    private int rear;
    private int size;
    private int capacity;

    public CircularQueue(int capacity) {
        this.capacity = capacity;
        arr = new int[capacity];
        front = 0;
        rear = -1;
        size = 0;
    }

    public void enqueue(int data) {
        if (size == capacity) {
            System.out.println("Queue is FULL");
            return;
        }
        // Wrap around using modulo — the key to circular behavior
        rear = (rear + 1) % capacity;
        arr[rear] = data;
        size++;
    }

    public int dequeue() {
        if (isEmpty()) {
            System.out.println("Queue is EMPTY");
            return -1;
        }
        int removed = arr[front];
        // Front also wraps around
        front = (front + 1) % capacity;
        size--;
        return removed;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public int peek() {
        if (isEmpty()) return -1;
        return arr[front];
    }

    public static void main(String[] args) {
        CircularQueue cq = new CircularQueue(3);
        cq.enqueue(1);
        cq.enqueue(2);
        cq.enqueue(3);
        System.out.println("Dequeued: " + cq.dequeue()); // 1
        cq.enqueue(4); // Wraps around to index 0 — allowed!
        System.out.println("Front: " + cq.peek());        // 2
    }
}
```

---

## 6. Queue Using Java's Built-in LinkedList

Java provides the `Queue` interface, most commonly implemented by `LinkedList`. This is the **recommended** way to use a queue in Java programs.

```java
import java.util.LinkedList;
import java.util.Queue;

public class BuiltInQueue {
    public static void main(String[] args) {
        Queue<Integer> queue = new LinkedList<>();

        queue.offer(10);  // offer() = enqueue (returns false if full, safer than add())
        queue.offer(20);
        queue.offer(30);

        System.out.println("Front: " + queue.peek());   // 10 — view without removing
        System.out.println("Removed: " + queue.poll()); // 10 — removes front
        System.out.println("Front now: " + queue.peek()); // 20
        System.out.println("Is empty? " + queue.isEmpty()); // false
    }
}
```

### Queue Method Reference

| Method      | Throws Exception if fails | Returns null/false if fails |
|-------------|---------------------------|------------------------------|
| `add(e)`    | Yes (throws exception)    | —                            |
| `offer(e)`  | —                         | Returns `false`              |
| `remove()`  | Yes (throws exception)    | —                            |
| `poll()`    | —                         | Returns `null`               |
| `element()` | Yes (throws exception)    | —                            |
| `peek()`    | —                         | Returns `null`               |

> **Rule of thumb:** Always prefer `offer()`, `poll()`, and `peek()` — they handle edge cases gracefully without crashing your program.

---

## 7. Deque (Double-Ended Queue)

A **Deque** (pronounced "deck") allows insertion and deletion from **both ends** — front and rear. In Java, use `ArrayDeque`.

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class DequeDemo {
    public static void main(String[] args) {
        Deque<Integer> deque = new ArrayDeque<>();

        deque.addFirst(10); // Add to front  → [10]
        deque.addLast(20);  // Add to rear   → [10, 20]
        deque.addFirst(5);  // Add to front  → [5, 10, 20]

        System.out.println("Front: " + deque.peekFirst()); // 5
        System.out.println("Rear: " + deque.peekLast());   // 20

        deque.removeFirst(); // Remove from front → [10, 20]
        deque.removeLast();  // Remove from rear  → [10]

        System.out.println("Remaining: " + deque.peekFirst()); // 10
    }
}
```

---

## 8. Real-World Applications of Queue

| Application                     | Why Queue is Used |
|---------------------------------|-------------------|
| **Printer Spooler**             | Documents print in the order they were sent |
| **CPU Scheduling**              | Processes are scheduled in order (Round Robin) |
| **BFS (Graph Traversal)**       | Explores nodes level by level |
| **Customer Service Systems**    | Calls/tickets handled in arrival order |
| **Web Server Request Handling** | HTTP requests queued and processed in order |

---

# Practice Problems (Session 10)

---

## 1. Implement Queue Using Two Stacks

[Implement Queue using Stacks (LeetCode 232)](https://leetcode.com/problems/implement-queue-using-stacks/)

### Problem Statement

Implement a **FIFO Queue** using only **two stacks**. Your Queue class must support:
- `push(x)` — Push element `x` to the back of the queue.
- `pop()` — Remove and return the element from the front.
- `peek()` — Return the element at the front without removing it.
- `empty()` — Return `true` if the queue is empty.

**You may ONLY use standard stack operations:** push to top, pop from top, peek at top, check if empty.

**Examples:**

| Operation         | Queue State  | Return Value |
|-------------------|--------------|--------------|
| `push(1)`         | [1]          | —            |
| `push(2)`         | [1, 2]       | —            |
| `peek()`          | [1, 2]       | `1`          |
| `pop()`           | [2]          | `1`          |
| `empty()`         | [2]          | `false`      |
| `pop()`           | []           | `2`          |
| `empty()`         | []           | `true`       |

---

### Approach 1: Brute Force (Move All Elements on Every Push)

Every time we `push`, we transfer all elements from `stack1` to `stack2`, push the new element to `stack1`, then transfer all back. This guarantees `stack1` always has elements in FIFO order at the top.

#### The Math/Logic Behind It

A stack is LIFO. If we have `[1, 2, 3]` (3 on top), the FIFO order should give us `1` first. To access `1`, we need to reverse the stack. Two stacks reverse each other perfectly — moving elements from stack1 to stack2 reverses the order.

**Dry Run: push(1), push(2), push(3), pop():**

| Step    | stack1 (top→)   | stack2 (top→) | Action |
|---------|-----------------|---------------|--------|
| push(1) | [1]             | []            | Move s1→s2, push 1, move s2→s1 |
| push(2) | [1, 2]          | []            | Move s1→s2, push 2, move s2→s1 |
| push(3) | [1, 2, 3]       | []            | Move s1→s2, push 3, move s2→s1 |
| pop()   | [2, 3]          | []            | Pop from s1 → returns 1 ✅ |

```java
import java.util.Stack;

public class QueueUsingTwoStacksBrute {
    Stack<Integer> stack1 = new Stack<>(); // Main stack — elements in FIFO order (front at top)
    Stack<Integer> stack2 = new Stack<>(); // Temporary helper stack

    // Push: ensure front element is always at top of stack1
    public void push(int x) {
        // Step 1: Move everything from stack1 to stack2
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }

        // Step 2: Push the new element onto empty stack1
        stack1.push(x); // x is now at the bottom (will be last out)

        // Step 3: Move everything back from stack2 to stack1
        while (!stack2.isEmpty()) {
            stack1.push(stack2.pop());
        }
        // Now stack1 has elements with the oldest at the top
    }

    // Pop: simply pop from stack1 (front is always at top)
    public int pop() {
        return stack1.pop();
    }

    // Peek: simply peek stack1 (front is at top)
    public int peek() {
        return stack1.peek();
    }

    // Empty: check if stack1 is empty
    public boolean empty() {
        return stack1.isEmpty();
    }

    public static void main(String[] args) {
        QueueUsingTwoStacksBrute q = new QueueUsingTwoStacksBrute();
        q.push(1);
        q.push(2);
        q.push(3);
        System.out.println("Peek: " + q.peek()); // 1 — first pushed is at front
        System.out.println("Pop: " + q.pop());   // 1
        System.out.println("Pop: " + q.pop());   // 2
        System.out.println("Empty: " + q.empty()); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` per `push()` — every push moves all N elements twice (to helper and back). `pop()` and `peek()` are O(1).
* **Space Complexity:** `O(N)` — we need space for all elements across the two stacks.

---

### Approach 2: Optimal (Lazy Transfer — Amortized O(1))

Instead of transferring on every `push`, we **transfer lazily**: only move from `stack1` to `stack2` when `stack2` is empty AND we need to `pop()` or `peek()`. This gives **amortized O(1)** per operation.

#### The Math/Logic Behind It

- `stack1` (input stack): all new pushes go here.
- `stack2` (output stack): all pops/peeks come from here.
- Only transfer when `stack2` is empty. Each element is moved **at most once** — from stack1 to stack2. Total cost across all operations is O(N) for N operations → amortized O(1) each.

**Dry Run: push(1), push(2), push(3), pop(), pop(), push(4), pop():**

| Step    | stack1 (top→) | stack2 (top→) | Return |
|---------|---------------|---------------|--------|
| push(1) | [1]           | []            | —      |
| push(2) | [2,1]         | []            | —      |
| push(3) | [3,2,1]       | []            | —      |
| pop()   | []            | [1,2,3]       | 1 ✅   |
| pop()   | []            | [2,3]         | 2 ✅   |
| push(4) | [4]           | [2,3]         | —      |
| pop()   | [4]           | [3]           | 3 ✅   |

```java
import java.util.Stack;

public class QueueUsingTwoStacksOptimal {
    Stack<Integer> stack1 = new Stack<>(); // Input stack — receives all pushes
    Stack<Integer> stack2 = new Stack<>(); // Output stack — provides all pops and peeks

    // Push: simply push to input stack — O(1) always
    public void push(int x) {
        stack1.push(x); // New elements always go to stack1
    }

    // Transfer elements from stack1 to stack2 (only when stack2 is empty)
    private void transfer() {
        if (stack2.isEmpty()) {
            // Move all from stack1 to stack2 — this reverses the order!
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop()); // Each element moved exactly once
            }
        }
    }

    // Pop: get the front element (bottom of stack1 = top of stack2)
    public int pop() {
        transfer();         // Ensure stack2 has elements
        return stack2.pop(); // Front is at top of stack2
    }

    // Peek: view the front element without removing
    public int peek() {
        transfer();          // Ensure stack2 has elements
        return stack2.peek(); // Front is at top of stack2
    }

    // Empty: queue is empty only if BOTH stacks are empty
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }

    public static void main(String[] args) {
        QueueUsingTwoStacksOptimal q = new QueueUsingTwoStacksOptimal();

        q.push(1);
        q.push(2);
        q.push(3);
        System.out.println("Peek: " + q.peek()); // 1
        System.out.println("Pop: " + q.pop());   // 1
        System.out.println("Pop: " + q.pop());   // 2
        q.push(4);
        System.out.println("Pop: " + q.pop());   // 3 (not 4 — FIFO guaranteed)
        System.out.println("Empty: " + q.empty()); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(1) amortized` — each element is pushed to stack1 once and moved to stack2 once. Across N operations, total work is O(N) → O(1) per operation on average.
* **Space Complexity:** `O(N)` — storing all elements across the two stacks.

### Teaching Tips
1. **Use a real-life analogy:** Imagine a stack of plates (LIFO). To serve customers in arrival order (FIFO), you dump the first plate-stack into a second stack. Now the bottom plate (oldest) is on top. This is exactly what the two-stack trick does.
2. **Draw both stacks side by side:** On the board, simulate push(1), push(2), push(3), pop() step by step. Let students trace which stack holds what.
3. **Introduce Amortized Analysis:** Tell students "lazy transfer costs O(N) once, but that cost is spread across N pop() calls — so each pop costs O(1) on average." This is their first taste of amortized analysis.

---

## 2. Queue Implementation Using Linked List

[Implement Queue using Linked List (GFG)](https://www.geeksforgeeks.org/problems/implement-queue-using-linked-list/1)

### Problem Statement

Implement a **Queue data structure** using a **Linked List** (not an array). The queue should support:
- `enqueue(x)` — Insert element `x` at the rear.
- `dequeue()` — Remove and return the element from the front. Return `-1` if empty.

**Examples:**

| Operations                                | Queue State      | Dequeue Returns |
|-------------------------------------------|------------------|-----------------|
| enqueue(2), enqueue(3)                    | [2, 3]           | —               |
| dequeue()                                 | [3]              | 2               |
| enqueue(4)                                | [3, 4]           | —               |
| dequeue()                                 | [4]              | 3               |
| dequeue()                                 | []               | 4               |
| dequeue() on empty                        | []               | -1              |

---

### Approach 1: Brute Force (Singly Linked List — O(N) Enqueue)

Use a singly linked list. Keep only a `front` pointer. For enqueue, traverse to the last node every time and append. Dequeue simply removes from front.

#### The Math/Logic Behind It

- Traversal to find the last node takes O(N) — this is the bottleneck.
- Dequeue from front is always O(1) — just move `front` to `front.next`.

**Dry Run: enqueue(1), enqueue(2), enqueue(3), dequeue():**

| Step        | Linked List State        | front |
|-------------|--------------------------|-------|
| enqueue(1)  | 1 → null                 | 1     |
| enqueue(2)  | 1 → 2 → null             | 1     |
| enqueue(3)  | 1 → 2 → 3 → null         | 1     |
| dequeue()   | 2 → 3 → null             | 2     |

```java
public class QueueLinkedListBrute {
    // Node class — each element in the linked list
    static class Node {
        int data;
        Node next;
        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    Node front; // Points to the first (oldest) element

    public QueueLinkedListBrute() {
        front = null; // Empty queue initially
    }

    // Enqueue: traverse to end and add new node — O(N)
    public void enqueue(int x) {
        Node newNode = new Node(x); // Create new node

        if (front == null) {
            front = newNode; // Queue was empty — new node becomes front
            return;
        }

        // Traverse to the last node
        Node current = front;
        while (current.next != null) {
            current = current.next; // Move to next node
        }

        current.next = newNode; // Attach new node at the end
    }

    // Dequeue: remove from front — O(1)
    public int dequeue() {
        if (front == null) {
            return -1; // Queue is empty
        }
        int removed = front.data; // Save front value
        front = front.next;       // Move front to next node
        return removed;
    }

    public static void main(String[] args) {
        QueueLinkedListBrute q = new QueueLinkedListBrute();
        q.enqueue(1);
        q.enqueue(2);
        q.enqueue(3);
        System.out.println("Dequeued: " + q.dequeue()); // 1
        System.out.println("Dequeued: " + q.dequeue()); // 2
        System.out.println("Dequeued: " + q.dequeue()); // 3
        System.out.println("Dequeued: " + q.dequeue()); // -1 (empty)
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` for enqueue — must traverse the entire list to find the last node. `O(1)` for dequeue.
* **Space Complexity:** `O(N)` — one node per element stored.

---

### Approach 2: Optimal (Maintain Both Front & Rear Pointers)

By keeping a `rear` pointer that always points to the last node, we can enqueue in **O(1)** — no traversal needed. Both enqueue and dequeue become O(1).

**Dry Run: enqueue(1), enqueue(2), enqueue(3), dequeue():**

| Step        | front → ... → rear  | Action |
|-------------|---------------------|--------|
| enqueue(1)  | 1 (front=rear=1)    | rear.next = new node; rear = new node |
| enqueue(2)  | 1 → 2               | rear.next = 2; rear = 2 |
| enqueue(3)  | 1 → 2 → 3           | rear.next = 3; rear = 3 |
| dequeue()   | 2 → 3               | front = front.next → returns 1 ✅ |

```java
public class QueueLinkedListOptimal {
    static class Node {
        int data;
        Node next;
        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    Node front; // Points to the first element (to dequeue from)
    Node rear;  // Points to the last element (to enqueue after)

    public QueueLinkedListOptimal() {
        front = null;
        rear = null; // Both null means queue is empty
    }

    // Enqueue: add at rear in O(1) using the rear pointer
    public void enqueue(int x) {
        Node newNode = new Node(x); // Create the new node

        if (rear == null) {
            // Queue is empty — new node is both front and rear
            front = newNode;
            rear = newNode;
            return;
        }

        rear.next = newNode; // Connect current rear to the new node
        rear = newNode;      // Update rear to point to the new node
    }

    // Dequeue: remove from front in O(1)
    public int dequeue() {
        if (front == null) {
            return -1; // Queue is empty
        }
        int removed = front.data; // Save the front value
        front = front.next;       // Move front pointer to next element

        if (front == null) {
            rear = null; // Queue became empty — reset rear too
        }

        return removed;
    }

    // Peek: view front without removing
    public int peek() {
        if (front == null) return -1;
        return front.data;
    }

    public boolean isEmpty() {
        return front == null;
    }

    public static void main(String[] args) {
        QueueLinkedListOptimal q = new QueueLinkedListOptimal();

        q.enqueue(10);
        q.enqueue(20);
        q.enqueue(30);
        System.out.println("Peek: " + q.peek());          // 10
        System.out.println("Dequeued: " + q.dequeue());   // 10
        System.out.println("Dequeued: " + q.dequeue());   // 20
        q.enqueue(40);
        System.out.println("Dequeued: " + q.dequeue());   // 30
        System.out.println("Dequeued: " + q.dequeue());   // 40
        System.out.println("Dequeued: " + q.dequeue());   // -1 (empty)
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(1)` for both `enqueue()` and `dequeue()` — the rear pointer eliminates the need to traverse.
* **Space Complexity:** `O(N)` — one node allocated per stored element.

### Teaching Tips
1. **Start with the problem:** Ask students "In the brute force, why is enqueue O(N)?" Guide them to realize traversal is the bottleneck. Then ask "How can we skip traversal?" — let them discover the rear pointer idea themselves.
2. **Draw the two-pointer diagram:** On the board, draw `front → node1 → node2 → node3 ← rear`. Show that enqueue touches only `rear.next` and `rear` — no traversal!
3. **Edge case walkthrough:** The edge case where the queue becomes empty after a dequeue (must set `rear = null`) is commonly missed. Walk through it explicitly on the board.

---

## 3. Number of Recent Calls (Sliding Window Queue)

[Number of Recent Calls (LeetCode 933)](https://leetcode.com/problems/number-of-recent-calls/)

### Problem Statement

You have a `RecentCounter` class that counts the number of **recent requests** within a time window. Implement:
- `RecentCounter()` — Initializes the counter (no recent requests).
- `int ping(int t)` — Adds a request at time `t` (in milliseconds) and returns the number of requests in the **inclusive range `[t - 3000, t]`**. It is guaranteed that `t` is strictly increasing in each call.

**Examples:**

| ping(t) | Window [t-3000, t] | Requests in Window | Returns |
|---------|--------------------|--------------------|---------|
| ping(1) | [-2999, 1]         | {1}                | 1       |
| ping(100)| [-2900, 100]      | {1, 100}           | 2       |
| ping(3001)| [1, 3001]        | {1, 100, 3001}     | 3       |
| ping(3002)| [2, 3002]        | {100, 3001, 3002}  | 3       |

> Note: `ping(3002)` — the window starts at `2`, so `t=1` falls out of the window → only 3 remain.

---

### Approach 1: Brute Force (ArrayList — Check All Elements)

Store all ping times in a list. For every new ping, scan the entire list and count how many values fall within `[t - 3000, t]`.

**Dry Run: ping(1), ping(100), ping(3001), ping(3002):**

| ping(t) | list state after ping   | Count in [t-3000, t] |
|---------|-------------------------|----------------------|
| 1       | [1]                     | 1                    |
| 100     | [1, 100]                | 2                    |
| 3001    | [1, 100, 3001]          | 3                    |
| 3002    | [1, 100, 3001, 3002]    | 3 (1 is out: 3002-3000=2, 1 < 2) |

```java
import java.util.ArrayList;

public class RecentCounterBrute {
    ArrayList<Integer> requests; // Store all ping timestamps

    public RecentCounterBrute() {
        requests = new ArrayList<>();
    }

    public int ping(int t) {
        requests.add(t); // Record this new request

        int count = 0;
        // Scan ALL stored requests and count those in range [t-3000, t]
        for (int time : requests) {
            if (time >= t - 3000) { // Check if this request is within the window
                count++;
            }
        }
        return count;
    }

    public static void main(String[] args) {
        RecentCounterBrute rc = new RecentCounterBrute();
        System.out.println(rc.ping(1));    // 1
        System.out.println(rc.ping(100));  // 2
        System.out.println(rc.ping(3001)); // 3
        System.out.println(rc.ping(3002)); // 3
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` per `ping()` — scans all previous requests each time. For N pings, total cost is O(N²).
* **Space Complexity:** `O(N)` — all ping timestamps are stored forever (even old ones out of window).

---

### Approach 2: Optimal (Queue — Sliding Window)

Use a **Queue** to represent the active window. When a new ping arrives, add `t` to the rear. Then remove from the front any timestamps that are older than `t - 3000`. The queue size is the answer.

#### The Math/Logic Behind It

The window `[t - 3000, t]` slides forward with each call. Elements that fall behind the left boundary are **permanently invalid** — they'll never be in a future window either (since `t` is always increasing). So we can safely remove them with `poll()`.

**Dry Run: ping(1), ping(100), ping(3001), ping(3002):**

| ping(t) | Queue before remove | Remove condition   | Queue after | Return |
|---------|---------------------|--------------------|-------------|--------|
| 1       | [1]                 | 1 >= 1-3000=-2999 ✅ keep | [1]       | 1      |
| 100     | [1, 100]            | all ≥ -2900 ✅ keep | [1, 100]  | 2      |
| 3001    | [1, 100, 3001]      | all ≥ 1 ✅ keep    | [1,100,3001] | 3   |
| 3002    | [1,100,3001,3002]   | 1 < 2 ❌ remove    | [100,3001,3002] | 3 |

```java
import java.util.LinkedList;
import java.util.Queue;

public class RecentCounterOptimal {
    Queue<Integer> queue; // Stores timestamps of requests within the window

    public RecentCounterOptimal() {
        queue = new LinkedList<>();
    }

    public int ping(int t) {
        queue.offer(t); // Add this new request timestamp to the rear

        // Remove all timestamps that are OLDER than the window start (t - 3000)
        // Only the front can be out of range — queue is always sorted (t increases)
        while (queue.peek() < t - 3000) {
            queue.poll(); // Discard outdated request from front
        }

        // Remaining elements in queue are all within [t-3000, t]
        return queue.size();
    }

    public static void main(String[] args) {
        RecentCounterOptimal rc = new RecentCounterOptimal();
        System.out.println(rc.ping(1));    // 1
        System.out.println(rc.ping(100));  // 2
        System.out.println(rc.ping(3001)); // 3
        System.out.println(rc.ping(3002)); // 3 — '1' was removed from front
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(1) amortized` — each timestamp is added once and removed at most once. Total O(N) work across N pings.
* **Space Complexity:** `O(W)` where W is the max number of pings within any 3-second window — at most O(3000) in the worst case.

### Teaching Tips
1. **Use a sliding window visual:** Draw a timeline on the board: `1 ... 100 ... 3001, 3002`. Show the [t-3000, t] bracket sliding right with each ping. It becomes visually obvious why old entries must be removed.
2. **Why is a Queue perfect here?** Because timestamps are **always sorted** (t is increasing) and we only ever remove from the **front** (oldest entries). This perfectly maps to Queue's dequeue-from-front behavior.
3. **Contrast with the brute force:** Show that the brute force never deletes anything — it grows forever. The queue approach actively cleans itself, keeping only useful data.

---

# Class 10 Homework

### Easy (Mandatory)
1. [Implement Stack using Queues – LeetCode 225](https://leetcode.com/problems/implement-stack-using-queues/) *(Reverse of what we did in class — simulate LIFO using two FIFO queues)*
2. [First Unique Character in a String – LeetCode 387](https://leetcode.com/problems/first-unique-character-in-a-string/) *(Use a queue to track characters in order)*
3. [Design Circular Queue – LeetCode 622](https://leetcode.com/problems/design-circular-queue/) *(Implement circular queue with fixed-size array using modulo)*

### Medium (Recommended)
1. [Sliding Window Maximum – LeetCode 239](https://leetcode.com/problems/sliding-window-maximum/) *(Deque-based sliding window — a classic interview problem)*
2. [Rotting Oranges – LeetCode 994](https://leetcode.com/problems/rotting-oranges/) *(BFS using Queue — every rotten orange spreads to neighbors level by level)*
3. [Task Scheduler – LeetCode 621](https://leetcode.com/problems/task-scheduler/) *(Queue + greedy — schedule tasks with cooldown periods)*

### Challenge Problem (Optional)
[First Negative Integer in Every Window of Size K – GFG](https://www.geeksforgeeks.org/problems/first-negative-integer-in-every-window-of-size-k3345/1) *(Use a Deque to efficiently find the first negative in each sliding window)*
