# Week 4 – Class 8: Linked List – Interview Problems

---

# Session 8: Linked List – Interview Problems

## 1. Why Linked List Problems Dominate Interviews

Linked List questions are among the **most frequently asked topics** in coding interviews at top companies (Google, Amazon, Microsoft, Meta). The reason: they test your ability to **think in pointers** — a skill that reveals whether you truly understand memory and references, not just syntax.

The three most important Linked List interview problems are:

| Problem | Core Technique | Why It Matters |
|---------|---------------|----------------|
| Reverse a Linked List | Pointer re-wiring | Fundamental for many other LL problems |
| Middle of a Linked List | Two Pointers (Slow & Fast) | Key pattern used in dozens of problems |
| Detect a Cycle | Floyd's Cycle Detection | Classic CS algorithm with elegant logic |

---

## 2. Quick Recap — The Node Structure

Every problem in this session uses the standard `Node` class. Keep this in mind as you read:

```java
// Node class — the building block of every Linked List
class Node {
    int data;   // value stored in this node
    Node next;  // reference to the next node (null if last)

    Node(int data) {
        this.data = data;
        this.next = null; // by default, a new node points to nothing
    }
}
```

Memory model of a list `1 → 2 → 3 → 4 → 5 → null`:
```
head
 │
 ▼
[1|•]──►[2|•]──►[3|•]──►[4|•]──►[5|null]
```

---

## 3. The Two-Pointer (Slow & Fast) Technique

Before solving the problems, understand this critical pattern used in **both the Middle Node and Cycle Detection** problems.

### What is the Slow & Fast Pointer?

We use **two pointers** that move through the list at **different speeds**:
- **Slow pointer** (`slow`) — moves **1 step** at a time
- **Fast pointer** (`fast`) — moves **2 steps** at a time

### Why This Works

Think of two runners on a circular track:
- If there is **no cycle**: the fast runner will reach the end (`null`) and lap the slow runner.
- If there **is a cycle**: the fast runner will eventually catch up to and **meet** the slow runner (like lapping them inside the loop).

When used for the **middle node**:
- When `fast` reaches the end of the list, `slow` is exactly at the **middle** — because slow covers half the distance that fast covers.

```
List: 1 → 2 → 3 → 4 → 5 → null

Step 0: slow = 1, fast = 1
Step 1: slow = 2, fast = 3
Step 2: slow = 3, fast = 5
        fast.next = null → STOP → slow is at the middle (node 3)
```

---

## 4. Reversing a Linked List — Core Concept

Reversing a linked list means **flipping all the `next` pointers** so that the last node becomes the first, and the first node becomes the last.

### Visual Before & After

```
Before:  head → [1] → [2] → [3] → [4] → [5] → null
After:   head → [5] → [4] → [3] → [2] → [1] → null
```

### The Three-Pointer Technique

We need **three pointers** to reverse the list without losing track of nodes:

| Pointer | Role |
|---------|------|
| `prev` | The node that will come AFTER current in the reversed list (starts as null) |
| `current` | The node we are currently reversing |
| `next` | Temporarily stores the next node before we break the link |

**Step-by-step logic for each iteration:**
1. Save `current.next` into `next` (so we don't lose the rest of the list)
2. Reverse the pointer: `current.next = prev`
3. Advance `prev = current`
4. Advance `current = next`

---

## 5. Cycle Detection — Core Concept

A **cycle** (or loop) in a Linked List means some node's `next` pointer points **back to a previous node**, creating an infinite loop. If you traverse such a list naively, you'll loop forever.

```
Normal List:    1 → 2 → 3 → 4 → 5 → null   (no cycle)
List with cycle:
                1 → 2 → 3 → 4 → 5
                        ↑         |
                        └─────────┘  (node 5 points back to node 3)
```

**Floyd's Cycle Detection Algorithm** (Tortoise & Hare):
- If there's no cycle: fast pointer will reach `null`
- If there's a cycle: fast pointer will eventually "lap" slow pointer and they'll **meet at the same node**

This works because inside a cycle, the relative speed between fast and slow is 1 step per iteration, so fast will catch slow in at most `cycle_length` iterations.

---

# Practice Problems (Session 8)

---

## 1. Reverse a Linked List

[Reverse Linked List (LeetCode 206)](https://leetcode.com/problems/reverse-linked-list/)

### Problem Statement

Given the **head** of a singly linked list, **reverse** the list and return the new head. Every `next` pointer must be flipped so the list is traversed in the opposite direction.

**Examples:**

| Input List              | Output List             | Reason                                      |
|-------------------------|-------------------------|---------------------------------------------|
| 1 → 2 → 3 → 4 → 5     | 5 → 4 → 3 → 2 → 1     | All next pointers flipped, 5 becomes head   |
| 1 → 2                  | 2 → 1                  | Two-node list simply swaps direction        |
| (single node: 1)       | 1                       | Single node is its own reverse              |

---

### Approach 1: Brute Force (Store in Array, Rebuild)

The simplest idea: traverse the list and store all values in an array. Then rebuild the list from right to left using those stored values. This avoids thinking about pointers at all.

#### The Math/Logic Behind It

- Step 1: Walk the list and collect all `N` values into an array → `[1, 2, 3, 4, 5]`
- Step 2: Create a new list by iterating the array backwards → `5, 4, 3, 2, 1`

| Step | Array Index | Value | Action                      |
|------|-------------|-------|-----------------------------|
| 1    | 0           | 1     | Store in array[0]           |
| 2    | 1           | 2     | Store in array[1]           |
| 3    | 2           | 3     | Store in array[2]           |
| 4    | 3           | 4     | Store in array[3]           |
| 5    | 4           | 5     | Store in array[4]           |
| 6    | Read 4→0    | —     | Build new list: 5→4→3→2→1  |

```java
import java.util.ArrayList;

class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class ReverseLinkedListBrute {

    public static Node reverse(Node head) {
        // Step 1: Collect all values from the list into an ArrayList
        ArrayList<Integer> values = new ArrayList<>();
        Node current = head;
        while (current != null) {
            values.add(current.data); // store each node's data
            current = current.next;
        }

        // Step 2: Overwrite list nodes with values in reverse order
        current = head;
        for (int i = values.size() - 1; i >= 0; i--) {
            current.data = values.get(i); // fill current node with value from end of array
            current = current.next;
        }

        return head; // head node is the same; only data inside nodes changed
    }

    public static void printList(Node head) {
        Node curr = head;
        while (curr != null) {
            System.out.print(curr.data + (curr.next != null ? " -> " : ""));
            curr = curr.next;
        }
        System.out.println(" -> null");
    }

    public static void main(String[] args) {
        // Test 1: 1 -> 2 -> 3 -> 4 -> 5
        Node head1 = new Node(1);
        head1.next = new Node(2);
        head1.next.next = new Node(3);
        head1.next.next.next = new Node(4);
        head1.next.next.next.next = new Node(5);
        System.out.print("Original: "); printList(head1);
        Node reversed1 = reverse(head1);
        System.out.print("Reversed: "); printList(reversed1); // expected: 5 -> 4 -> 3 -> 2 -> 1

        // Test 2: 1 -> 2
        Node head2 = new Node(1);
        head2.next = new Node(2);
        System.out.print("Original: "); printList(head2);
        Node reversed2 = reverse(head2);
        System.out.print("Reversed: "); printList(reversed2); // expected: 2 -> 1

        // Test 3: single node
        Node head3 = new Node(42);
        System.out.print("Original: "); printList(head3);
        Node reversed3 = reverse(head3);
        System.out.print("Reversed: "); printList(reversed3); // expected: 42
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we traverse the list twice: once to collect values, once to overwrite them.
* **Space Complexity:** `O(N)` — we store all N values in an ArrayList, which grows linearly with the list size.

---

### Approach 2: Optimal (Three-Pointer In-Place Reversal)

Instead of storing values, we **re-wire the `next` pointers directly**. We use three pointers (`prev`, `current`, `next`) to reverse the direction of each link as we walk through the list. This is the standard interview answer.

#### The Math/Logic Behind It

At each step, we flip one arrow: `current → next` becomes `current → prev`.

| Iteration | `prev` | `current` | `next` | Action                            |
|-----------|--------|-----------|--------|-----------------------------------|
| Start     | null   | 1         | —      | Initialize                        |
| 1         | null   | 1         | 2      | 1.next = null, prev=1, curr=2     |
| 2         | 1      | 2         | 3      | 2.next = 1,    prev=2, curr=3     |
| 3         | 2      | 3         | 4      | 3.next = 2,    prev=3, curr=4     |
| 4         | 3      | 4         | 5      | 4.next = 3,    prev=4, curr=5     |
| 5         | 4      | 5         | null   | 5.next = 4,    prev=5, curr=null  |
| End       | 5      | null      | —      | `prev` is the new head            |

```
After Iteration 1:  null ←[1]  [2]→[3]→[4]→[5]→null
After Iteration 2:  null ←[1]←[2]  [3]→[4]→[5]→null
After Iteration 3:  null ←[1]←[2]←[3]  [4]→[5]→null
After Iteration 4:  null ←[1]←[2]←[3]←[4]  [5]→null
After Iteration 5:  null ←[1]←[2]←[3]←[4]←[5]   new head = 5
```

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class ReverseLinkedListOptimal {

    public static Node reverse(Node head) {
        Node prev = null;       // will become null after the new last node (old head)
        Node current = head;    // start from the head of the original list
        Node next = null;       // temporary holder for the next node

        while (current != null) {
            next = current.next;    // SAVE the next node before we break the link
            current.next = prev;    // REVERSE the pointer: point current back to prev
            prev = current;         // ADVANCE prev forward to current node
            current = next;         // ADVANCE current to the saved next node
        }

        // When current is null, prev is pointing to the new head (last node of original)
        return prev;
    }

    public static void printList(Node head) {
        Node curr = head;
        while (curr != null) {
            System.out.print(curr.data + (curr.next != null ? " -> " : ""));
            curr = curr.next;
        }
        System.out.println(" -> null");
    }

    public static void main(String[] args) {
        // Test 1: Standard 5-node list
        Node head1 = new Node(1);
        head1.next = new Node(2);
        head1.next.next = new Node(3);
        head1.next.next.next = new Node(4);
        head1.next.next.next.next = new Node(5);
        System.out.print("Original: "); printList(head1);
        Node reversed1 = reverse(head1);
        System.out.print("Reversed: "); printList(reversed1); // expected: 5 -> 4 -> 3 -> 2 -> 1

        // Test 2: Two-node list
        Node head2 = new Node(10);
        head2.next = new Node(20);
        System.out.print("Original: "); printList(head2);
        Node reversed2 = reverse(head2);
        System.out.print("Reversed: "); printList(reversed2); // expected: 20 -> 10

        // Test 3: Single node (should remain unchanged)
        Node head3 = new Node(99);
        Node reversed3 = reverse(head3);
        System.out.print("Single node: "); printList(reversed3); // expected: 99
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each node is visited exactly once; at each node, we do a constant amount of pointer work.
* **Space Complexity:** `O(1)` — we only use three pointer variables (`prev`, `current`, `next`) regardless of how large the list is.

### Teaching Tips
1. **Use a physical demonstration:** Place 5 chairs in a line. Each student "points" (stretches their arm) toward the person ahead of them. To reverse, go from left to right: each person now points back to the person behind. Ask "What are the three things each person needs to remember?" — this maps directly to `prev`, `current`, and `next`.
2. **Common mistake:** Students often forget to save `current.next` before overwriting it. Emphasize: "Step 1 is ALWAYS save `next`. If you forget this, you lose the rest of the list forever." Drawing a "broken chain" on the board helps.
3. **Board technique:** Draw boxes for each node. At each iteration, physically erase the rightward arrow and draw a leftward one. Students can see the list reverse arrow by arrow, making the pointer logic concrete.

---

## 2. Middle of a Linked List

[Middle of the Linked List (LeetCode 876)](https://leetcode.com/problems/middle-of-the-linked-list/)

### Problem Statement

Given the **head** of a singly linked list, return the **middle node**. If the list has two middle nodes (even length), return the **second** middle node.

**Examples:**

| Input List                 | Output (Middle Node) | Reason                                                |
|----------------------------|----------------------|-------------------------------------------------------|
| 1 → 2 → 3 → 4 → 5        | Node 3               | 5 nodes → 3rd node is the exact middle               |
| 1 → 2 → 3 → 4 → 5 → 6   | Node 4               | 6 nodes → two middles (3 and 4), return 2nd (node 4) |
| 1 → 2 → 3                 | Node 2               | 3 nodes → 2nd node is the middle                     |

---

### Approach 1: Brute Force (Count Length, Then Traverse)

First, count the total number of nodes (N). Then, traverse again up to the `(N/2 + 1)`-th node (integer division). This requires **two passes** over the list.

#### The Math/Logic Behind It

- For N = 5: middle index = 5/2 + 1 = 3rd node → Node(3) ✓
- For N = 6: middle index = 6/2 + 1 = 4th node → Node(4) ✓
- For N = 3: middle index = 3/2 + 1 = 2nd node → Node(2) ✓

| Pass | Action | Result |
|------|--------|--------|
| Pass 1 | Count all nodes | N = 5 |
| Compute | Find middle position | pos = N/2 + 1 = 3 |
| Pass 2 | Traverse to position 3 | Return Node(3) |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class MiddleOfListBrute {

    public static Node findMiddle(Node head) {
        // Pass 1: Count the total number of nodes
        int count = 0;
        Node current = head;
        while (current != null) {
            count++;              // increment count for every node visited
            current = current.next;
        }

        // Calculate the middle position (1-indexed)
        int middlePos = count / 2 + 1; // for even N, this gives the 2nd middle

        // Pass 2: Traverse to the middle position
        current = head;
        for (int i = 1; i < middlePos; i++) {
            current = current.next; // move forward one node each step
        }

        return current; // this is the middle node
    }

    public static void main(String[] args) {
        // Test 1: Odd length list — 1 -> 2 -> 3 -> 4 -> 5
        Node head1 = new Node(1);
        head1.next = new Node(2);
        head1.next.next = new Node(3);
        head1.next.next.next = new Node(4);
        head1.next.next.next.next = new Node(5);
        System.out.println("Middle of [1,2,3,4,5]: " + findMiddle(head1).data); // expected: 3

        // Test 2: Even length list — 1 -> 2 -> 3 -> 4 -> 5 -> 6
        Node head2 = new Node(1);
        head2.next = new Node(2);
        head2.next.next = new Node(3);
        head2.next.next.next = new Node(4);
        head2.next.next.next.next = new Node(5);
        head2.next.next.next.next.next = new Node(6);
        System.out.println("Middle of [1,2,3,4,5,6]: " + findMiddle(head2).data); // expected: 4

        // Test 3: Three-node list — 1 -> 2 -> 3
        Node head3 = new Node(1);
        head3.next = new Node(2);
        head3.next.next = new Node(3);
        System.out.println("Middle of [1,2,3]: " + findMiddle(head3).data); // expected: 2
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we traverse the entire list once to count (N steps) and again up to the middle (N/2 steps), totalling about 1.5N which is still O(N).
* **Space Complexity:** `O(1)` — only a counter variable and a pointer; no extra data structures used.

---

### Approach 2: Optimal (Slow & Fast Two-Pointer)

We use two pointers starting at `head`. `slow` moves 1 step and `fast` moves 2 steps per iteration. When `fast` reaches the end, `slow` is exactly at the middle — **in a single pass**.

#### The Math/Logic Behind It

The key insight: fast covers **2× the distance** of slow. So when fast finishes the list (distance N), slow has covered N/2 — the middle!

**Dry Run for `1 → 2 → 3 → 4 → 5`:**

| Step | `slow` at | `fast` at | Condition Check |
|------|-----------|-----------|-----------------|
| 0    | 1         | 1         | fast ≠ null, fast.next ≠ null → continue |
| 1    | 2         | 3         | fast ≠ null, fast.next ≠ null → continue |
| 2    | 3         | 5         | fast.next = null → **STOP** |
| —    | **3**     | 5         | `slow` = Node(3) = **middle** ✓ |

**Dry Run for `1 → 2 → 3 → 4 → 5 → 6`:**

| Step | `slow` at | `fast` at | Condition Check |
|------|-----------|-----------|-----------------|
| 0    | 1         | 1         | continue |
| 1    | 2         | 3         | continue |
| 2    | 3         | 5         | continue |
| 3    | 4         | null (6.next) | fast = null → **STOP** |
| —    | **4**     | null       | `slow` = Node(4) = **2nd middle** ✓ |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class MiddleOfListOptimal {

    public static Node findMiddle(Node head) {
        Node slow = head;  // slow pointer — moves 1 step at a time
        Node fast = head;  // fast pointer — moves 2 steps at a time

        // Continue until fast reaches the end or goes past it
        while (fast != null && fast.next != null) {
            slow = slow.next;       // slow moves 1 step
            fast = fast.next.next;  // fast moves 2 steps
        }

        // When fast is at/past the end, slow is at the middle
        return slow;
    }

    public static void main(String[] args) {
        // Test 1: Odd length — 1 -> 2 -> 3 -> 4 -> 5
        Node head1 = new Node(1);
        head1.next = new Node(2);
        head1.next.next = new Node(3);
        head1.next.next.next = new Node(4);
        head1.next.next.next.next = new Node(5);
        System.out.println("Middle of [1,2,3,4,5]: " + findMiddle(head1).data); // expected: 3

        // Test 2: Even length — 1 -> 2 -> 3 -> 4 -> 5 -> 6
        Node head2 = new Node(1);
        head2.next = new Node(2);
        head2.next.next = new Node(3);
        head2.next.next.next = new Node(4);
        head2.next.next.next.next = new Node(5);
        head2.next.next.next.next.next = new Node(6);
        System.out.println("Middle of [1,2,3,4,5,6]: " + findMiddle(head2).data); // expected: 4

        // Test 3: Single node — 1
        Node head3 = new Node(1);
        System.out.println("Middle of [1]: " + findMiddle(head3).data); // expected: 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — `fast` traverses the full list at 2× speed, so we stop after N/2 iterations, but this is still linear; only one pass needed.
* **Space Complexity:** `O(1)` — only two pointer variables are used; no arrays or extra structures.

### Teaching Tips
1. **Physical analogy:** Use a hallway with chairs. Have one student walk normally (slow) and another jog at double speed (fast). Start them both at one end. When the jogger hits the wall, ask where the walker is — they'll be at the midpoint. This makes the two-pointer concept immediately intuitive.
2. **Common mistake:** Students often forget the condition `fast.next != null` and only check `fast != null`, which causes a `NullPointerException` when `fast` is at the last node and tries to do `fast.next.next`. Always check **both conditions**.
3. **Extend the lesson:** Show students that the Slow & Fast pointer pattern is the basis for many other problems: palindrome linked list, cycle detection, nth node from end. Once they master the pattern here, those problems become straightforward.

---

## 3. Detect a Cycle in a Linked List

[Linked List Cycle (LeetCode 141)](https://leetcode.com/problems/linked-list-cycle/)

### Problem Statement

Given the **head** of a linked list, determine if the linked list has a **cycle** in it. A cycle exists if some node's `next` pointer points back to a previous node, causing traversal to loop infinitely. Return `true` if there is a cycle, `false` otherwise.

**Examples:**

| Input List (with connections)             | Output  | Reason                                            |
|-------------------------------------------|---------|---------------------------------------------------|
| 3 → 2 → 0 → -4 → (back to node 2)       | `true`  | Node -4 points back to node 2, forming a loop    |
| 1 → 2 → (back to node 1)                 | `true`  | Node 2 points back to node 1 (head), loop exists |
| 1 → null                                  | `false` | Single node with no cycle                        |

---

### Approach 1: Brute Force (HashSet to Track Visited Nodes)

Traverse the list and store each **node reference** (not data) in a `HashSet`. If we ever encounter a node we've already seen, there's a cycle. If we reach `null`, there's no cycle.

#### The Math/Logic Behind It

- Nodes are uniquely identified by their **memory address** (object reference in Java), not their data value.
- We store node references in a HashSet. Checking if a reference is in the set takes `O(1)` average time.
- The moment we see a repeated reference, we've gone around a cycle.

**Dry Run for `1 → 2 → 3 → (back to 2)`:**

| Step | Node Visited | HashSet State             | Seen Before? |
|------|--------------|---------------------------|--------------|
| 1    | Node@1(val=1)| {Node@1}                  | No           |
| 2    | Node@2(val=2)| {Node@1, Node@2}          | No           |
| 3    | Node@3(val=3)| {Node@1, Node@2, Node@3}  | No           |
| 4    | Node@2(val=2)| —                         | **YES → cycle detected!** |

```java
import java.util.HashSet;

class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class DetectCycleBrute {

    public static boolean hasCycle(Node head) {
        // HashSet stores references to visited Node objects (not just values)
        HashSet<Node> visited = new HashSet<>();

        Node current = head;
        while (current != null) {
            // If we've seen this exact node object before, there's a cycle
            if (visited.contains(current)) {
                return true; // cycle detected!
            }
            visited.add(current); // mark this node as visited
            current = current.next; // move to the next node
        }

        // Reached null → no cycle
        return false;
    }

    public static void main(String[] args) {
        // Test 1: List WITH a cycle — 1 -> 2 -> 3 -> 4 -> (back to 2)
        Node head1 = new Node(1);
        Node node2 = new Node(2);
        Node node3 = new Node(3);
        Node node4 = new Node(4);
        head1.next = node2;
        node2.next = node3;
        node3.next = node4;
        node4.next = node2;  // creates a cycle back to node2
        System.out.println("Has cycle (expect true): " + hasCycle(head1)); // true

        // Test 2: List WITHOUT a cycle — 1 -> 2 -> 3 -> null
        Node head2 = new Node(1);
        head2.next = new Node(2);
        head2.next.next = new Node(3);
        System.out.println("Has cycle (expect false): " + hasCycle(head2)); // false

        // Test 3: Single node, no cycle
        Node head3 = new Node(5);
        System.out.println("Has cycle (expect false): " + hasCycle(head3)); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — in the worst case (no cycle, or cycle at the very end), we visit every node exactly once before reaching null or a repeated node.
* **Space Complexity:** `O(N)` — the HashSet can store at most N node references, one for each node in the list before a cycle is found.

---

### Approach 2: Optimal (Floyd's Cycle Detection — Slow & Fast Pointers)

This is **Floyd's Tortoise and Hare Algorithm** — the classic interview answer. We use two pointers at different speeds. If there's a cycle, the fast pointer will lap the slow pointer and they will **meet**. If there's no cycle, fast will hit `null`.

#### The Math/Logic Behind It

Imagine two runners on a circular track:
- The fast runner gains **1 lap distance** on the slow runner per cycle length `L`
- The fast pointer gains **1 step** on the slow pointer per iteration
- Therefore, after at most `L` iterations inside the loop, fast will catch slow

**Key rule:**
- If `fast == null` or `fast.next == null` → **no cycle** (reached the end)
- If `fast == slow` (they meet) → **cycle exists**

**Dry Run for `3 → 2 → 0 → -4 → (back to 2)`:**

```
List structure:
3 → 2 → 0 → -4
    ↑         |
    └─────────┘
```

| Step | slow at | fast at | Meet? |
|------|---------|---------|-------|
| 0    | 3       | 3       | No (start) |
| 1    | 2       | 0       | No |
| 2    | 0       | 2       | No |
| 3    | -4      | -4      | **YES! slow == fast → cycle detected** |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class DetectCycleOptimal {

    public static boolean hasCycle(Node head) {
        // Edge case: empty list or single node with no self-loop → no cycle
        if (head == null || head.next == null) {
            return false;
        }

        Node slow = head;       // slow pointer: moves 1 step per iteration
        Node fast = head;       // fast pointer: moves 2 steps per iteration

        while (fast != null && fast.next != null) {
            slow = slow.next;       // slow advances by 1
            fast = fast.next.next;  // fast advances by 2

            // If slow and fast meet, a cycle exists
            if (slow == fast) {
                return true; // they're pointing to the SAME node object
            }
        }

        // fast reached null — no cycle
        return false;
    }

    public static void main(String[] args) {
        // Test 1: Cycle — 1 -> 2 -> 3 -> 4 -> (back to node 2)
        Node head1 = new Node(1);
        Node cycleNode = new Node(2);
        head1.next = cycleNode;
        head1.next.next = new Node(3);
        head1.next.next.next = new Node(4);
        head1.next.next.next.next = cycleNode; // node 4 points back to node 2
        System.out.println("Has cycle (expect true):  " + hasCycle(head1)); // true

        // Test 2: No cycle — 1 -> 2 -> 3 -> 4 -> null
        Node head2 = new Node(1);
        head2.next = new Node(2);
        head2.next.next = new Node(3);
        head2.next.next.next = new Node(4);
        System.out.println("Has cycle (expect false): " + hasCycle(head2)); // false

        // Test 3: Self-loop — single node pointing to itself
        Node head3 = new Node(1);
        head3.next = head3; // node points to itself
        System.out.println("Has cycle (expect true):  " + hasCycle(head3)); // true
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — if no cycle, fast reaches null in N/2 steps; if there's a cycle of length L, fast catches slow in at most N + L steps, which is still linear.
* **Space Complexity:** `O(1)` — only two pointer variables are used; no HashSet or additional data structure needed, making this far more memory-efficient than the brute-force approach.

### Teaching Tips
1. **Use the circular track analogy:** Draw a circular road and put two dots: "turtle" (slow) and "hare" (fast). Ask students: "If the hare runs twice as fast, will it ever lap the turtle?" Everyone says yes — so in a cycle, fast WILL meet slow. Then draw a straight road: "Can the hare lap the turtle on a straight road?" No — it just runs off the edge (null). This gives intuition for both cases.
2. **Common mistake:** Students compare `slow.data == fast.data` instead of `slow == fast`. Emphasize that the comparison must be on the **references (objects)**, not the values, because two different nodes can have the same data value.
3. **Extend the discussion:** Ask "Where do slow and fast meet inside the cycle?" Then introduce the extension: "Can we find the START of the cycle?" — reset one pointer to head, keep the other at the meeting point, advance both 1 step at a time — they'll meet at the cycle's entry. This leads naturally to LeetCode 142 (Linked List Cycle II).

---

## 4. Bonus: Nth Node from End of a Linked List

[Remove Nth Node From End of List (LeetCode 19)](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

### Problem Statement

Given the **head** of a linked list and an integer **n**, find the **value of the nth node from the end** of the list. (The last node is the 1st from the end, the second-to-last is the 2nd from the end, etc.)

**Examples:**

| Input List             | N | Output | Reason                              |
|------------------------|---|--------|-------------------------------------|
| 1 → 2 → 3 → 4 → 5   | 2 | 4      | 2nd from end: 5 is 1st, 4 is 2nd   |
| 1 → 2 → 3 → 4 → 5   | 1 | 5      | 1st from end is the last node (5)  |
| 1 → 2 → 3             | 3 | 1      | 3rd from end: count = 3, so node 1 |

---

### Approach 1: Brute Force (Count Length, Then Access)

Count the total nodes (length L). The nth node from the end is the `(L - n + 1)`th node from the beginning. Traverse again to that position.

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class NthFromEndBrute {

    public static int nthFromEnd(Node head, int n) {
        // Pass 1: find the total length of the list
        int length = 0;
        Node current = head;
        while (current != null) {
            length++;            // count every node
            current = current.next;
        }

        // Convert "nth from end" to "kth from start"
        int k = length - n + 1;  // e.g., 5th list, n=2 → k = 5 - 2 + 1 = 4th from start

        // Pass 2: traverse to the kth node from the start
        current = head;
        for (int i = 1; i < k; i++) {
            current = current.next; // advance to the kth position
        }

        return current.data; // this is the nth node from the end
    }

    public static void main(String[] args) {
        // Build list: 1 -> 2 -> 3 -> 4 -> 5
        Node head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(4);
        head.next.next.next.next = new Node(5);

        System.out.println("2nd from end: " + nthFromEnd(head, 2)); // expected: 4
        System.out.println("1st from end: " + nthFromEnd(head, 1)); // expected: 5
        System.out.println("5th from end: " + nthFromEnd(head, 5)); // expected: 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — two passes over the list: one to count (N steps) and one to reach position L-n+1 (at most N steps).
* **Space Complexity:** `O(1)` — only a counter and a pointer are used; no auxiliary data structures.

---

### Approach 2: Optimal (Two-Pointer Gap Technique)

Move the `fast` pointer `n` steps ahead first. Then move both `slow` and `fast` together, one step at a time. When `fast` reaches the end, `slow` is at the nth node from the end — all in **one pass**.

#### The Math/Logic Behind It

The gap between `fast` and `slow` stays exactly `n` at all times. When `fast` is at the last node (position L), `slow` is at position `L - n` — which is the nth node from the end.

**Dry Run for list `1→2→3→4→5`, n=2:**

| Step | slow at | fast at | Action |
|------|---------|---------|--------|
| Init | 1       | 1       | Start |
| Gap  | 1       | 3       | Move fast 2 steps ahead |
| 1    | 2       | 4       | Move both 1 step |
| 2    | 3       | 5       | Move both 1 step |
| 3    | 4       | null (5.next) | fast.next = null → STOP |
| —    | **4**   | —       | slow = Node(4) = 2nd from end ✓ |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class NthFromEndOptimal {

    public static int nthFromEnd(Node head, int n) {
        Node slow = head;  // slow will end up at the nth node from end
        Node fast = head;  // fast starts n steps ahead of slow

        // Step 1: Move fast pointer n steps ahead
        for (int i = 0; i < n; i++) {
            if (fast == null) {
                System.out.println("n is larger than the list length!");
                return -1; // invalid input guard
            }
            fast = fast.next; // advance fast by 1 step, n times
        }

        // Step 2: Move both pointers together until fast reaches null
        while (fast != null) {
            slow = slow.next; // slow advances 1 step
            fast = fast.next; // fast advances 1 step (maintaining the gap of n)
        }

        // When fast is null, slow is at the nth node from the end
        return slow.data;
    }

    public static void main(String[] args) {
        // Build list: 1 -> 2 -> 3 -> 4 -> 5
        Node head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(4);
        head.next.next.next.next = new Node(5);

        System.out.println("2nd from end: " + nthFromEnd(head, 2)); // expected: 4
        System.out.println("1st from end: " + nthFromEnd(head, 1)); // expected: 5
        System.out.println("5th from end: " + nthFromEnd(head, 5)); // expected: 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — fast traverses the full list (N steps total); slow covers the remaining N-n steps; combined, it's a single effective pass.
* **Space Complexity:** `O(1)` — only two pointer variables used; no extra memory regardless of list size.

### Teaching Tips
1. **Visualize the gap:** Draw a ruler. Mark `slow` and `fast` on it. Show that as they both move right at the same speed, the gap stays fixed at `n`. When `fast` falls off the right edge, `slow` is exactly `n` positions from the right edge. This "fixed gap" concept is the entire trick.
2. **Reinforce the pattern:** Point out that this "two-pointer with a head start" pattern appears in many interview problems. Seeing it here prepares students for variations like "Remove Nth Node from End" (LeetCode 19) where they must also delete the found node.
3. **Write the invariant on the board:** `distance(slow, fast) = n` at all times during phase 2. Keeping this invariant visible helps students reason about correctness without tracing every step.

---

# Class 8 Homework

### Easy (Mandatory)
1. [Palindrome Linked List — LeetCode 234](https://leetcode.com/problems/palindrome-linked-list/) — Check if a linked list reads the same forwards and backwards. *Hint: find the middle, reverse the second half, compare.*
2. [Delete Middle Node of a Linked List — LeetCode 2095](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/) — Find and delete the middle node using the slow & fast pointer technique.
3. [Merge Two Sorted Lists — LeetCode 21](https://leetcode.com/problems/merge-two-sorted-lists/) — Merge two sorted linked lists and return the merged sorted list.

### Medium (Recommended)
1. [Linked List Cycle II — LeetCode 142](https://leetcode.com/problems/linked-list-cycle-ii/) — Return the **node where the cycle begins**, not just whether a cycle exists. Extend Floyd's algorithm.
2. [Reorder List — LeetCode 143](https://leetcode.com/problems/reorder-list/) — Reorder a list from `L0 → L1 → … → Ln` to `L0 → Ln → L1 → Ln-1 → L2 → Ln-2`. Uses middle, reverse, and merge together.
3. [Reverse Linked List II — LeetCode 92](https://leetcode.com/problems/reverse-linked-list-ii/) — Reverse only a sub-portion (from position `left` to `right`) of the linked list.

### Challenge Problem (Optional)
[Sort List — LeetCode 148](https://leetcode.com/problems/sort-list/) — Sort a linked list in `O(N log N)` time and `O(1)` space using Merge Sort on a linked list. Combines finding the middle (slow & fast) with recursive merging.
