# Week 4 – Class 7: Linked List – Fundamentals

---

# Session 7: Linked List – Fundamentals

## 1. What is a Linked List?

A **Linked List** is a linear data structure where elements (called **nodes**) are stored at non-contiguous memory locations. Each node holds:

1. **Data** — the actual value stored
2. **Next** — a reference (pointer) to the next node in the sequence

The last node's `next` reference is `null`, marking the **end of the list**.

### Real-World Analogy

Think of a **treasure hunt** — each clue tells you where the next clue is hidden. You cannot jump directly to clue #5; you must follow the chain from clue #1 → #2 → #3 → ... → #5. A Linked List works the same way: to reach element 5, you must traverse from the head, following `next` pointers one by one.

Another analogy: a **train** — each coach (node) is connected to the next coach (via a coupling = `next` pointer). You cannot teleport to coach 7; you walk through coaches 1, 2, 3 … 6 first.

---

## 2. Array vs. Linked List — Key Differences

| Feature                  | Array                       | Linked List                |
|--------------------------|-----------------------------|----------------------------|
| Memory Allocation        | Contiguous (block)          | Non-contiguous (scattered) |
| Size                     | Fixed at creation           | Dynamic (grows/shrinks)    |
| Access by Index          | O(1) — direct access        | O(N) — must traverse       |
| Insertion at Beginning   | O(N) — shift all elements   | O(1) — update head pointer |
| Insertion at End         | O(1) amortized              | O(N) — traverse to end     |
| Deletion at Beginning    | O(N) — shift all elements   | O(1) — update head pointer |
| Memory Overhead          | Low                         | Extra memory for `next`    |
| Cache Performance        | Excellent (contiguous)      | Poor (scattered in memory) |

**When to use Linked List over Array:**
- When you need **frequent insertions/deletions at the beginning**
- When the **size of the list is unpredictable** at runtime
- When you want to **avoid costly shifting** of elements

---

## 3. Node Structure in Java

Every Linked List is made of `Node` objects. Each node is a simple class with two fields:

```java
// A Node is the building block of every Linked List
class Node {
    int data;   // The value stored in this node
    Node next;  // Reference to the next node (null if last node)

    // Constructor: creates a new node with given data, next = null by default
    Node(int data) {
        this.data = data;
        this.next = null;
    }
}
```

### Visualizing the Node

```
  ┌──────────────┐
  │  data = 10   │
  │  next ──────►│──► (next node or null)
  └──────────────┘
```

---

## 4. Singly Linked List — Structure

A **Singly Linked List** has nodes where each node points only to the **next** node (one direction).

```
head
 │
 ▼
┌───────┐    ┌───────┐    ┌───────┐    ┌───────┐
│ 10|next├───►│ 20|next├───►│ 30|next├───►│ 40|null│
└───────┘    └───────┘    └───────┘    └───────┘
```

- **head** — a reference to the **first node** of the list
- If `head == null`, the list is **empty**
- The last node always has `next = null`

---

## 5. Creating a Linked List in Java

```java
class Node {
    int data;
    Node next;

    Node(int data) {
        this.data = data;
        this.next = null;
    }
}

public class LinkedListDemo {
    public static void main(String[] args) {
        // Step 1: Create individual nodes
        Node n1 = new Node(10);
        Node n2 = new Node(20);
        Node n3 = new Node(30);
        Node n4 = new Node(40);

        // Step 2: Connect nodes by setting next pointers
        n1.next = n2;  // 10 → 20
        n2.next = n3;  // 20 → 30
        n3.next = n4;  // 30 → 40
        // n4.next is null by default → end of list

        // Step 3: head points to the first node
        Node head = n1;

        System.out.println("Linked List created: 10 -> 20 -> 30 -> 40 -> null");
    }
}
```

**Memory Model:**
```
head ──► [10|•]──►[20|•]──►[30|•]──►[40|null]
```

---

## 6. Traversal (Printing the Linked List)

To visit every node, start at `head` and follow `next` pointers until you reach `null`.

```java
public class LinkedListTraversal {

    // Traverses from head to the end, printing each node's data
    public static void printList(Node head) {
        Node current = head;  // Start from the head node

        while (current != null) {           // Continue until we reach the end (null)
            System.out.print(current.data); // Print current node's data
            if (current.next != null) {
                System.out.print(" -> ");   // Print arrow only if there's a next node
            }
            current = current.next;         // Move to the next node
        }
        System.out.println(" -> null");     // Mark the end of the list
    }

    public static void main(String[] args) {
        // Build the list: 10 -> 20 -> 30 -> 40
        Node head = new Node(10);
        head.next = new Node(20);
        head.next.next = new Node(30);
        head.next.next.next = new Node(40);

        printList(head); // Output: 10 -> 20 -> 30 -> 40 -> null
    }
}
```

**Traversal Trace for [10 → 20 → 30 → null]:**

| Step | `current` node | Action               |
|------|----------------|----------------------|
| 1    | Node(10)       | Print 10, move next  |
| 2    | Node(20)       | Print 20, move next  |
| 3    | Node(30)       | Print 30, move next  |
| 4    | `null`         | Loop ends            |

**Complexity:** `O(N)` time, `O(1)` space — we visit each node exactly once.

---

## 7. Insertion Operations

### 7.1 Insert at the Beginning

**Idea:** Create a new node, point its `next` to the current `head`, then update `head` to the new node.

```
Before: head ──► [10]──►[20]──►[30]──►null
Insert 5 at beginning:
After:  head ──► [5]──►[10]──►[20]──►[30]──►null
```

```java
public static Node insertAtBeginning(Node head, int data) {
    Node newNode = new Node(data);  // Create the new node
    newNode.next = head;            // New node points to current head
    head = newNode;                 // Update head to point to new node
    return head;                    // Return new head
}
```

**Complexity:** `O(1)` — only pointer updates, no traversal needed.

---

### 7.2 Insert at the End

**Idea:** Traverse to the last node (where `next == null`), then set its `next` to the new node.

```
Before: head ──► [10]──►[20]──►[30]──►null
Insert 40 at end:
After:  head ──► [10]──►[20]──►[30]──►[40]──►null
```

```java
public static Node insertAtEnd(Node head, int data) {
    Node newNode = new Node(data);  // Create the new node

    // Edge case: if the list is empty, new node becomes the head
    if (head == null) {
        return newNode;
    }

    Node current = head;
    while (current.next != null) {  // Traverse until the last node
        current = current.next;
    }
    current.next = newNode;         // Last node now points to new node
    return head;
}
```

**Complexity:** `O(N)` — must traverse the entire list to reach the last node.

---

### 7.3 Insert at a Given Position

**Idea:** Traverse to the node just before the desired position, then rewire the `next` pointers.

```
Before: head ──► [10]──►[20]──►[30]──►null
Insert 25 at position 3 (1-indexed):
After:  head ──► [10]──►[20]──►[25]──►[30]──►null
```

```java
public static Node insertAtPosition(Node head, int data, int position) {
    Node newNode = new Node(data);

    // Edge case: insert at position 1 (beginning)
    if (position == 1) {
        newNode.next = head;
        return newNode;
    }

    Node current = head;
    // Traverse to the node just before the desired position
    for (int i = 1; i < position - 1 && current != null; i++) {
        current = current.next;
    }

    // If position is out of range, do nothing
    if (current == null) {
        System.out.println("Position out of range!");
        return head;
    }

    newNode.next = current.next;  // New node points to what was at position
    current.next = newNode;       // Previous node now points to new node
    return head;
}
```

**Complexity:** `O(N)` — traversal to the target position.

---

## 8. Deletion Operations

### 8.1 Delete from the Beginning

**Idea:** Move `head` forward to the second node. The first node becomes unreferenced and is garbage collected.

```
Before: head ──► [10]──►[20]──►[30]──►null
Delete from beginning:
After:  head ──► [20]──►[30]──►null  (node 10 is gone)
```

```java
public static Node deleteFromBeginning(Node head) {
    if (head == null) {
        System.out.println("List is already empty!");
        return null;
    }
    head = head.next;  // Move head to the second node (first node is now unreachable)
    return head;
}
```

**Complexity:** `O(1)` — just one pointer update.

---

### 8.2 Delete from the End

**Idea:** Traverse to the second-to-last node and set its `next` to `null`.

```
Before: head ──► [10]──►[20]──►[30]──►null
Delete from end:
After:  head ──► [10]──►[20]──►null  (node 30 is gone)
```

```java
public static Node deleteFromEnd(Node head) {
    // Edge case: empty list
    if (head == null) return null;

    // Edge case: only one node in the list
    if (head.next == null) return null;

    Node current = head;
    // Traverse until current is the second-to-last node
    while (current.next.next != null) {
        current = current.next;
    }

    current.next = null;  // Remove the last node by unlinking it
    return head;
}
```

**Complexity:** `O(N)` — traversal to reach the second-to-last node.

---

### 8.3 Delete a Node by Value

**Idea:** Find the node whose `data` matches the target, then skip over it by connecting the previous node's `next` to the node after the target.

```
Before: head ──► [10]──►[20]──►[30]──►[40]──►null
Delete node with value 30:
After:  head ──► [10]──►[20]──►[40]──►null
```

```java
public static Node deleteByValue(Node head, int value) {
    // Edge case: empty list
    if (head == null) return null;

    // Edge case: value is at the head
    if (head.data == value) {
        return head.next;  // New head is the second node
    }

    Node current = head;
    // Traverse until we find the node BEFORE the one to delete
    while (current.next != null && current.next.data != value) {
        current = current.next;
    }

    // If the value was found, unlink that node
    if (current.next != null) {
        current.next = current.next.next;  // Skip over the target node
    } else {
        System.out.println("Value " + value + " not found in list!");
    }

    return head;
}
```

**Complexity:** `O(N)` — in the worst case, we traverse the entire list.

---

## 9. Linked List Complexity Cheat Sheet

| Operation             | Time Complexity | Space Complexity |
|-----------------------|-----------------|------------------|
| Traversal / Search    | O(N)            | O(1)             |
| Insert at Beginning   | O(1)            | O(1)             |
| Insert at End         | O(N)            | O(1)             |
| Insert at Position k  | O(k)            | O(1)             |
| Delete from Beginning | O(1)            | O(1)             |
| Delete from End       | O(N)            | O(1)             |
| Delete by Value       | O(N)            | O(1)             |
| Find Length           | O(N)            | O(1)             |

---

## 10. Finding the Length of a Linked List

```java
public static int findLength(Node head) {
    int count = 0;      // Counter for the number of nodes
    Node current = head;

    while (current != null) {  // Traverse until end of list
        count++;               // Increment count for each node visited
        current = current.next;
    }

    return count; // Total number of nodes
}
```

**Complexity:** `O(N)` time — visits every node once. `O(1)` space — only one counter variable.

---

## 11. Full Linked List Class (Putting It All Together)

```java
class Node {
    int data;
    Node next;

    Node(int data) {
        this.data = data;
        this.next = null;
    }
}

public class LinkedList {

    // Print all nodes from head to tail
    public static void printList(Node head) {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }

    // Insert a new node at the beginning (O(1))
    public static Node insertAtBeginning(Node head, int data) {
        Node newNode = new Node(data);
        newNode.next = head;
        return newNode;
    }

    // Insert a new node at the end (O(N))
    public static Node insertAtEnd(Node head, int data) {
        Node newNode = new Node(data);
        if (head == null) return newNode;
        Node current = head;
        while (current.next != null) current = current.next;
        current.next = newNode;
        return head;
    }

    // Delete the first node (O(1))
    public static Node deleteFromBeginning(Node head) {
        if (head == null) return null;
        return head.next;
    }

    // Delete the last node (O(N))
    public static Node deleteFromEnd(Node head) {
        if (head == null || head.next == null) return null;
        Node current = head;
        while (current.next.next != null) current = current.next;
        current.next = null;
        return head;
    }

    // Find the length of the list (O(N))
    public static int findLength(Node head) {
        int count = 0;
        Node current = head;
        while (current != null) { count++; current = current.next; }
        return count;
    }

    public static void main(String[] args) {
        Node head = null;

        // Build list: 10 -> 20 -> 30 -> 40
        head = insertAtEnd(head, 10);
        head = insertAtEnd(head, 20);
        head = insertAtEnd(head, 30);
        head = insertAtEnd(head, 40);
        System.out.print("After building:    "); printList(head); // 10 -> 20 -> 30 -> 40 -> null

        // Insert 5 at beginning
        head = insertAtBeginning(head, 5);
        System.out.print("After insert at beginning: "); printList(head); // 5 -> 10 -> 20 -> 30 -> 40 -> null

        // Delete from beginning
        head = deleteFromBeginning(head);
        System.out.print("After delete beginning: "); printList(head); // 10 -> 20 -> 30 -> 40 -> null

        // Delete from end
        head = deleteFromEnd(head);
        System.out.print("After delete end:  "); printList(head); // 10 -> 20 -> 30 -> null

        // Find length
        System.out.println("Length: " + findLength(head)); // 3
    }
}
```

---

# Practice Problems (Session 7)

---

## 1. Insert a Node in a Linked List

[Insert a Node in a Linked List (GFG)](https://www.geeksforgeeks.org/problems/linked-list-insertion-1587115620/1)

### Problem Statement

Given the **head** of a singly linked list and an integer **data**, insert a new node with the given data at the **end** of the linked list and return the updated head.

**Examples:**

| Input List       | Data to Insert | Output List          | Reason                                |
|------------------|----------------|----------------------|---------------------------------------|
| 1 -> 2 -> 3      | 4              | 1 -> 2 -> 3 -> 4     | Node 4 appended at the tail           |
| (empty)          | 10             | 10                   | New node becomes the only node        |
| 5 -> 10          | 15             | 5 -> 10 -> 15        | Node 15 appended after node 10        |

---

### Approach 1: Brute Force (Traverse to End)

The simplest approach: traverse the entire list node by node until we find the last node (the one whose `next` is `null`). Then attach the new node there. This is the direct, most intuitive approach with no tricks.

| Step | Current Node | Action                              |
|------|--------------|-------------------------------------|
| 1    | Node(1)      | Not last node, move forward         |
| 2    | Node(2)      | Not last node, move forward         |
| 3    | Node(3)      | `next == null` → this is the last node |
| 4    | —            | Set Node(3).next = new Node(4)      |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class InsertAtEnd {

    public static Node insertAtEnd(Node head, int data) {
        Node newNode = new Node(data); // Create the new node to insert

        // Edge case: if list is empty, the new node IS the list
        if (head == null) {
            return newNode;
        }

        Node current = head;

        // Traverse until we find the last node (current.next == null)
        while (current.next != null) {
            current = current.next;
        }

        // Attach the new node at the end
        current.next = newNode;

        return head; // Head has not changed
    }

    public static void printList(Node head) {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }

    public static void main(String[] args) {
        Node head = null;

        // Test 1: Insert into empty list
        head = insertAtEnd(head, 10);
        printList(head); // 10 -> null

        // Test 2: Insert multiple nodes at end
        head = insertAtEnd(head, 20);
        head = insertAtEnd(head, 30);
        printList(head); // 10 -> 20 -> 30 -> null

        // Test 3: Insert one more at end
        head = insertAtEnd(head, 40);
        printList(head); // 10 -> 20 -> 30 -> 40 -> null
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we must traverse all N nodes to reach the last node before inserting.
* **Space Complexity:** `O(1)` — only one extra `Node` object is created; no auxiliary data structures are used.

---

### Approach 2: Optimal (Maintain a Tail Pointer)

The key insight: if we always keep track of the **tail** (last node), we can insert at the end in `O(1)` without traversal. This is the pattern used by most production-level Linked List implementations.

#### The Math/Logic Behind It

Instead of chasing the tail every time, we remember it. Every time we insert at the end, we:
1. Set `tail.next = newNode`
2. Update `tail = newNode`

Both steps are pointer updates — `O(1)` regardless of list size.

| Operation           | Without Tail Pointer | With Tail Pointer |
|---------------------|----------------------|-------------------|
| Insert at End       | O(N)                 | O(1)              |
| Insert at Beginning | O(1)                 | O(1)              |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class InsertAtEndOptimal {

    // We maintain both head and tail as class-level references
    static Node head = null;
    static Node tail = null;

    // Insert at end in O(1) using the tail pointer
    public static void insertAtEnd(int data) {
        Node newNode = new Node(data);

        // Case 1: List is empty
        if (head == null) {
            head = newNode;  // New node is both head and tail
            tail = newNode;
            return;
        }

        // Case 2: List has at least one node
        tail.next = newNode; // Current tail points to new node
        tail = newNode;      // Update tail to the new last node
    }

    public static void printList() {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }

    public static void main(String[] args) {
        // Test 1: Build a list using O(1) insertions
        insertAtEnd(10);
        insertAtEnd(20);
        insertAtEnd(30);
        printList(); // 10 -> 20 -> 30 -> null

        // Test 2: Insert more elements
        insertAtEnd(40);
        insertAtEnd(50);
        printList(); // 10 -> 20 -> 30 -> 40 -> 50 -> null
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(1)` — inserting at the end is now a constant-time operation since we always have direct access to the tail.
* **Space Complexity:** `O(1)` — just one extra `tail` reference; no traversal or auxiliary structure needed.

### Teaching Tips
1. **Use a physical chain analogy:** Give students 4 sticky notes. Label them 10, 20, 30, 40. Have them hold each one with an arrow pointing to the next. Ask: "How do you add a new sticky note at the end?" — they naturally walk to the last person, which shows WHY traversal is needed.
2. **Emphasize the tail pointer insight:** Draw both approaches side-by-side: one where you run to the end every time vs. one where you always know who the last person is. The efficiency gain is intuitive.
3. **Reinforce edge cases:** Always ask: "What if the list is empty?" before writing the insert code. Train the habit of checking `head == null` first.

---

## 2. Delete a Node in a Linked List

[Delete Node in a Linked List (LeetCode 237)](https://leetcode.com/problems/delete-node-in-a-linked-list/)

### Problem Statement

You are given a reference to a **node to be deleted** in a singly linked list (it is **guaranteed** that the node is NOT the tail). You do **not** have access to the `head` of the list. Delete the given node.

**Note:** This problem has a twist — you cannot access the previous node, yet you must "delete" the given node. The trick is to **copy** the next node's data into the current node and then skip the next node.

**Examples:**

| Input List          | Node to Delete | Output List        | Reason                                |
|---------------------|----------------|--------------------|---------------------------------------|
| 4 -> 5 -> 1 -> 9   | node = 5       | 4 -> 1 -> 9        | Node with value 5 is removed          |
| 4 -> 5 -> 1 -> 9   | node = 1       | 4 -> 5 -> 9        | Node with value 1 is removed          |
| 1 -> 2 -> 3        | node = 1       | 2 -> 3             | Node with value 1 removed from front  |

---

### Approach 1: Brute Force (Copy Data + Skip Next)

Since we cannot go back to the previous node (singly linked), we use a clever trick: **copy the next node's data into the current node**, then make the current node skip the next node. This effectively "deletes" the current node's value from the list.

#### The Math/Logic Behind It

```
Before: ... ──► [node: 5] ──► [next: 1] ──► [9] ──► null
Step 1: Copy next's data into current → [node: 1] ──► [next: 1] ──► [9]
Step 2: Skip next node → [node: 1] ──► [9] ──► null
Result: ... ──► [1] ──► [9] ──► null   (node "5" is gone)
```

| Step | Action                              | List State              |
|------|-------------------------------------|-------------------------|
| 0    | Target node has data = 5, next → 1  | ... 5 → 1 → 9 → null   |
| 1    | Copy node.next.data (1) into node   | ... 1 → 1 → 9 → null   |
| 2    | Set node.next = node.next.next      | ... 1 → 9 → null       |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class DeleteNode {

    // Given only the node to delete (not head), delete it in O(1)
    public static void deleteNode(Node node) {
        // The trick: copy next node's data into current node
        node.data = node.next.data;  // Overwrite current node's data with next node's data

        // Skip the next node (it now has a duplicate of current node's new data)
        node.next = node.next.next;  // Bypass the next node
    }

    public static void printList(Node head) {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }

    public static void main(String[] args) {
        // Build list: 4 -> 5 -> 1 -> 9
        Node head = new Node(4);
        Node nodeToDelete = new Node(5); // We keep reference to this node
        head.next = nodeToDelete;
        head.next.next = new Node(1);
        head.next.next.next = new Node(9);

        System.out.print("Before deletion: "); printList(head); // 4 -> 5 -> 1 -> 9 -> null

        // Test 1: Delete node with value 5
        deleteNode(nodeToDelete);
        System.out.print("After deleting 5: "); printList(head); // 4 -> 1 -> 9 -> null

        // Test 2: Delete node with value 1
        Node head2 = new Node(4);
        Node node1 = new Node(5);
        Node nodeToDelete2 = new Node(1);
        head2.next = node1;
        node1.next = nodeToDelete2;
        nodeToDelete2.next = new Node(9);

        deleteNode(nodeToDelete2);
        System.out.print("After deleting 1: "); printList(head2); // 4 -> 5 -> 9 -> null
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(1)` — no traversal needed; we only copy one value and update one pointer.
* **Space Complexity:** `O(1)` — no extra memory used at all.

---

### Approach 2: Standard Delete by Value (with head access)

This is the **general approach** used when you have access to the `head`. Traverse to find the node just before the target and rewire.

```java
public class DeleteByValue {

    public static Node deleteByValue(Node head, int value) {
        // Edge case: empty list
        if (head == null) return null;

        // Edge case: the head itself needs to be deleted
        if (head.data == value) {
            return head.next; // New head is the second node
        }

        Node current = head;

        // Traverse to find the node JUST BEFORE the one to delete
        while (current.next != null && current.next.data != value) {
            current = current.next;
        }

        // If found, unlink the node with the target value
        if (current.next != null) {
            current.next = current.next.next; // Skip over the target node
        }

        return head;
    }

    public static void printList(Node head) {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }

    public static void main(String[] args) {
        // Build: 10 -> 20 -> 30 -> 40
        Node head = new Node(10);
        head.next = new Node(20);
        head.next.next = new Node(30);
        head.next.next.next = new Node(40);

        System.out.print("Before: "); printList(head); // 10 -> 20 -> 30 -> 40 -> null

        // Test 1: Delete 30 (middle node)
        head = deleteByValue(head, 30);
        System.out.print("Delete 30: "); printList(head); // 10 -> 20 -> 40 -> null

        // Test 2: Delete head (10)
        head = deleteByValue(head, 10);
        System.out.print("Delete 10: "); printList(head); // 20 -> 40 -> null

        // Test 3: Delete tail (40)
        head = deleteByValue(head, 40);
        System.out.print("Delete 40: "); printList(head); // 20 -> null
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — in the worst case, the target value is at the end, requiring full traversal of all N nodes.
* **Space Complexity:** `O(1)` — only a single `current` pointer variable is used.

### Teaching Tips
1. **The "copy trick" blows minds:** When presenting the LeetCode version, first ask students: "You can't go backwards — how do you delete the current node?" Let them struggle for 2 minutes. Then reveal the copy trick. It becomes memorable.
2. **Diagram the standard deletion on the board:** Draw three boxes (Prev → Target → Next). Show that "deleting" Target = making Prev point directly to Next. The Target box is then "floating" with no reference.
3. **Emphasize the edge cases:** Always handle: (a) empty list, (b) deleting the head, (c) value not found. Write these checks first before the main loop.

---

## 3. Traversal and Count Nodes

[Count Nodes of Linked List (GFG)](https://www.geeksforgeeks.org/problems/count-nodes-of-linked-list/1)

### Problem Statement

Given the **head** of a singly linked list, return the **total number of nodes** in the list. This is the most fundamental traversal problem — master this pattern and you can solve almost any traversal-based problem.

**Examples:**

| Input List             | Output | Reason                             |
|------------------------|--------|------------------------------------|
| 1 -> 2 -> 3 -> 4 -> 5 | 5      | There are 5 nodes in the list      |
| 10 -> 20              | 2      | There are 2 nodes                  |
| (empty / null)        | 0      | An empty list has 0 nodes          |

---

### Approach 1: Brute Force (Iterative Count)

Walk through the list from `head` to `null`, incrementing a counter for every node visited. This is the most direct approach.

**Dry Run for [10 → 20 → 30 → null]:**

| Step | `current` | `count` | Action                     |
|------|-----------|---------|----------------------------|
| 1    | Node(10)  | 1       | Count node, move forward   |
| 2    | Node(20)  | 2       | Count node, move forward   |
| 3    | Node(30)  | 3       | Count node, move forward   |
| 4    | null      | 3       | Loop ends, return 3        |

```java
class Node {
    int data;
    Node next;
    Node(int data) { this.data = data; this.next = null; }
}

public class CountNodes {

    public static int countNodes(Node head) {
        int count = 0;          // Start counter at 0
        Node current = head;    // Start traversal from head

        while (current != null) {    // Keep going until we reach the end
            count++;                 // Increment count for each node found
            current = current.next; // Move to the next node
        }

        return count; // Return total number of nodes
    }

    public static void main(String[] args) {
        // Test 1: Empty list
        System.out.println("Empty list count: " + countNodes(null)); // 0

        // Test 2: List with 5 nodes
        Node head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(4);
        head.next.next.next.next = new Node(5);
        System.out.println("Count of [1,2,3,4,5]: " + countNodes(head)); // 5

        // Test 3: Single node list
        Node single = new Node(42);
        System.out.println("Single node count: " + countNodes(single)); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we visit every node exactly once, so the time grows linearly with the number of nodes.
* **Space Complexity:** `O(1)` — only one `count` integer and one `current` pointer are used, regardless of list size.

---

### Approach 2: Optimal (Recursive Count)

Use recursion: the count of nodes starting from `head` = 1 + count of nodes starting from `head.next`. Base case: if `head` is `null`, return 0.

```java
public class CountNodesRecursive {

    // Recursive approach: count = 1 + count of remaining list
    public static int countRecursive(Node head) {
        // Base case: null means we've reached beyond the last node
        if (head == null) {
            return 0;
        }

        // Recursive case: 1 (current node) + count of rest of the list
        return 1 + countRecursive(head.next);
    }

    public static void main(String[] args) {
        // Test 1: List [10, 20, 30, 40, 50]
        Node head = new Node(10);
        head.next = new Node(20);
        head.next.next = new Node(30);
        head.next.next.next = new Node(40);
        head.next.next.next.next = new Node(50);

        System.out.println("Recursive count: " + countRecursive(head)); // 5

        // Test 2: Empty list
        System.out.println("Recursive count (empty): " + countRecursive(null)); // 0

        // Test 3: Two node list
        Node two = new Node(7);
        two.next = new Node(14);
        System.out.println("Recursive count [7,14]: " + countRecursive(two)); // 2
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the function makes exactly N+1 recursive calls (one for each node, plus one when it hits `null`).
* **Space Complexity:** `O(N)` — the call stack grows to depth N (one stack frame per node), so this uses more memory than the iterative version.

### Teaching Tips
1. **Start with the traversal template:** Write `Node current = head; while(current != null) { ... current = current.next; }` on the board and call it the "Linked List Traversal Template." Tell students: memorize this. 90% of Linked List problems use this exact skeleton.
2. **Contrast iterative vs. recursive:** Iterative uses O(1) space; recursive uses O(N) stack space. For production code on large lists, always prefer iterative traversal to avoid stack overflow.
3. **Make it hands-on:** Have students trace the dry run table themselves with a small list of 4 nodes. Students who write the table understand the while loop instantly.

---

# Class 7 Homework

### Easy (Mandatory)

1. [Print Linked List Elements (GFG)](https://www.geeksforgeeks.org/problems/print-linked-list-elements/1) — Practice printing all elements of a linked list.
2. [Search an element in a Linked List (GFG)](https://www.geeksforgeeks.org/problems/search-an-element-in-a-linked-list/1) — Find whether a given key exists in the list.
3. [Length of Linked List (GFG)](https://www.geeksforgeeks.org/problems/count-nodes-of-linked-list/1) — Find the total number of nodes in a linked list.
4. [Linked List Insertion at Position (GFG)](https://www.geeksforgeeks.org/problems/linked-list-insertion-1587115620/1) — Insert a node at a given position.

### Medium (Recommended)

1. [Delete the Middle Node of a Linked List (LeetCode 2095)](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/) — Find and delete the node exactly at the middle of the list.
2. [Remove Duplicates from Sorted List (LeetCode 83)](https://leetcode.com/problems/remove-duplicates-from-sorted-list/) — Remove all duplicate values from a sorted linked list.
3. [Merge Two Sorted Lists (LeetCode 21)](https://leetcode.com/problems/merge-two-sorted-lists/) — Merge two sorted linked lists into one sorted list.

### Challenge Problem (Optional)

[Add Two Numbers (LeetCode 2)](https://leetcode.com/problems/add-two-numbers/) — Given two non-empty linked lists representing two non-negative integers stored in reverse order, add them and return the result as a linked list.
