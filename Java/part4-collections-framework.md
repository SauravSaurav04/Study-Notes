# Part 4: Collections Framework - Complete Study Guide

**Focus Areas:** Collections Framework, List, Set, Map, Queue, HashMap Internals, Comparable vs Comparator, Iteration Techniques

---

## 📋 Table of Contents
1. [Collections Framework Overview](#-collections-framework-overview)
2. [List Interface](#-list-interface)
3. [Set Interface](#-set-interface)
4. [Queue Interface](#-queue-interface)
5. [Map Interface](#-map-interface)
6. [HashMap Deep Dive](#-hashmap-deep-dive)
7. [Collections Utility Class](#-collections-utility-class)
8. [Comparable vs Comparator](#-comparable-vs-comparator)
9. [Iteration Techniques](#-iteration-techniques)
10. [Thread-Safe Collections](#-thread-safe-collections)
11. [Interview Questions](#-interview-questions)
12. [Practice Problems](#-practice-problems)
13. [📄 One-Page Cheatsheet](#-one-page-cheatsheet)

---

## 🎯 Collections Framework Overview

### What is Collection Framework?

**Definition:** The **Java Collection Framework** is a unified architecture for representing and manipulating collections of objects.

**Collection** = A single unit of objects (group of elements)

### Why Collections Framework?

**Before Collections Framework (JDK 1.0):**
- Only Array, Vector, Stack, Hashtable, Properties
- No common interface
- No standard algorithms
- Difficult to learn and use

**After Collections Framework (Java 1.2+):**
✅ **Unified architecture** with common interfaces  
✅ **Reusable data structures** (List, Set, Map, Queue)  
✅ **High-performance implementations**  
✅ **Standard algorithms** (sorting, searching, shuffling)  
✅ **Interoperability** between different implementations  
✅ **Reduced programming effort**  

### Collection Hierarchy

```
                    Iterable<E>
                        |
                    Collection<E>
                        |
        ┌───────────────┼───────────────┬────────────┐
        |               |               |            |
     List<E>         Set<E>         Queue<E>      Deque<E>
        |               |               |            |
   ┌────┴────┐     ┌────┴────┐     ┌────┴────┐     |
ArrayList  LinkedList  HashSet  TreeSet  PriorityQueue  ArrayDeque
  Vector              LinkedHashSet


Map<K, V> (Separate hierarchy - NOT part of Collection)
   |
   ┌─────────┴──────────┬──────────────┬───────────┐
HashMap  LinkedHashMap  TreeMap  Hashtable (Legacy)
```

### Core Interfaces

| Interface | Ordered? | Sorted? | Duplicates? | Null Elements? | Indexed? |
|-----------|----------|---------|-------------|----------------|----------|
| **List** | ✅ Yes (insertion order) | ❌ No | ✅ Allowed | ✅ Multiple | ✅ Yes |
| **Set** | ❌ No (HashSet) | ❌ No | ❌ Not Allowed | ✅ One null | ❌ No |
| **SortedSet** | ❌ No | ✅ Yes | ❌ Not Allowed | ❌ No null | ❌ No |
| **Queue** | ✅ Yes (FIFO) | ❌ No | ✅ Allowed | Depends | ❌ No |
| **Deque** | ✅ Yes | ❌ No | ✅ Allowed | ❌ No | ❌ No |
| **Map** | ❌ No (HashMap) | ❌ No | Keys: ❌ Values: ✅ | ✅ One null key | ❌ No |

### Key Terms

**1. Ordered vs Sorted**

| Ordered | Sorted |
|---------|--------|
| Maintains insertion order | Elements in specific order (ascending/descending) |
| Example: ArrayList, LinkedHashSet | Example: TreeSet, TreeMap |
| Order = how you added them | Order = natural or custom sorting |

**2. Collection vs Collections**

| Collection | Collections |
|------------|-------------|
| **Interface** (java.util.Collection) | **Utility Class** (java.util.Collections) |
| Root interface for List, Set, Queue | Contains static utility methods |
| Defines basic operations | sort(), reverse(), shuffle(), etc. |

**3. Fail-Fast vs Fail-Safe**

| Fail-Fast | Fail-Safe |
|-----------|-----------|
| Throws ConcurrentModificationException | Does NOT throw exception |
| Works on original collection | Works on copy of collection |
| Examples: ArrayList, HashMap iterators | Examples: CopyOnWriteArrayList |
| Fast, memory efficient | Slower, more memory |

---

## 🎯 List Interface

### Overview

**Definition:** Ordered collection (sequence) that allows duplicate elements. Users can access elements by integer index (zero-based).

**Key Characteristics:**
1. ✅ **Maintains insertion order**
2. ✅ **Allows duplicate elements**
3. ✅ **Allows multiple null values**
4. ✅ **Index-based access** (positional access)
5. ✅ **Dynamic size** (can grow/shrink)

**Common Methods:**
```java
// Adding elements
boolean add(E element)
void add(int index, E element)
boolean addAll(Collection<? extends E> c)

// Accessing elements
E get(int index)
E set(int index, E element)

// Removing elements
E remove(int index)
boolean remove(Object o)
void clear()

// Searching
int indexOf(Object o)
int lastIndexOf(Object o)
boolean contains(Object o)

// Other operations
int size()
boolean isEmpty()
List<E> subList(int fromIndex, int toIndex)
```

**List Implementations Comparison:**

| Feature | ArrayList | LinkedList | Vector |
|---------|-----------|------------|--------|
| **Structure** | Dynamic Array | Doubly Linked List | Dynamic Array |
| **Access Time** | O(1) - Fast | O(n) - Slow | O(1) - Fast |
| **Insert/Delete (start)** | O(n) - Slow | O(1) - Fast | O(n) - Slow |
| **Insert/Delete (end)** | O(1) - Fast | O(1) - Fast | O(1) - Fast |
| **Memory** | Less | More (node pointers) | Less |
| **Thread-Safe** | ❌ No | ❌ No | ✅ Yes |
| **Performance** | ⚡ Fast | ⚡ Fast (for insert/delete) | 🐌 Slow (synchronized) |
| **Growth** | 50% (1.5x) | N/A | 100% (2x) |
| **Legacy** | No (Java 1.2) | No (Java 1.2) | Yes (JDK 1.0) |
| **Use Case** | Random access | Insert/delete at ends | Legacy/thread-safe |

---

### 1️⃣ ArrayList

#### Overview

**Definition:** Resizable array implementation of List interface.

**Internal Working:**
- Backed by **dynamic array**
- Default initial capacity: **10**
- When array becomes full, creates **new larger array**
- Copies elements from old array to new array
- Growth formula: `newCapacity = (oldCapacity * 3/2) + 1`

**Growth Timeline:**
```
Capacity Growth:
10 → 15 → 22 → 33 → 49 → 73 → 109 → 163...
```

#### Internal Structure

```java
public class ArrayList<E> {
    private Object[] elementData;  // Array backing the list
    private int size;              // Number of elements
    
    private static final int DEFAULT_CAPACITY = 10;
    private static final Object[] EMPTY_ELEMENTDATA = {};
    
    // Constructor
    public ArrayList() {
        this.elementData = EMPTY_ELEMENTDATA;
    }
    
    // Add method (simplified)
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);
        elementData[size++] = e;
        return true;
    }
    
    // Ensure capacity
    private void ensureCapacityInternal(int minCapacity) {
        if (minCapacity > elementData.length) {
            grow(minCapacity);
        }
    }
    
    // Grow array
    private void grow(int minCapacity) {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5x growth
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
}
```

#### Visual Representation

**Adding Elements:**
```
Initial (Capacity = 10):
[null][null][null][null][null][null][null][null][null][null]

After adding 3 elements:
[10][20][30][null][null][null][null][null][null][null]
Size = 3

After adding 10 elements (full):
[10][20][30][40][50][60][70][80][90][100]
Size = 10

After adding 11th element (resize to 15):
[10][20][30][40][50][60][70][80][90][100][110][null][null][null][null]
Size = 11, Capacity = 15
```

#### Time Complexity

| Operation | Time Complexity | Explanation |
|-----------|----------------|-------------|
| `get(index)` | **O(1)** | Direct array access |
| `add(element)` | **O(1)** amortized | Add at end (occasional resize) |
| `add(index, element)` | **O(n)** | Shift elements right |
| `remove(index)` | **O(n)** | Shift elements left |
| `remove(object)` | **O(n)** | Search + remove |
| `contains(element)` | **O(n)** | Linear search |
| `indexOf(element)` | **O(n)** | Linear search |
| `size()` | **O(1)** | Return size variable |
| `isEmpty()` | **O(1)** | Check size == 0 |

#### Example Code

```java
import java.util.*;

public class ArrayListExample {
    public static void main(String[] args) {
        // Create ArrayList
        ArrayList<String> fruits = new ArrayList<>();
        
        // Add elements
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Cherry");
        fruits.add("Apple");  // Duplicates allowed
        fruits.add(null);     // Null allowed
        
        // Add at specific index
        fruits.add(1, "Avocado");  // Insert at index 1
        
        // Access elements
        String first = fruits.get(0);  // "Apple"
        System.out.println("First: " + first);
        
        // Update element
        fruits.set(0, "Apricot");  // Replace element at index 0
        
        // Remove elements
        fruits.remove(0);           // Remove by index
        fruits.remove("Banana");    // Remove by object
        
        // Size
        int size = fruits.size();
        System.out.println("Size: " + size);
        
        // Contains
        boolean hasCherry = fruits.contains("Cherry");
        System.out.println("Has Cherry: " + hasCherry);
        
        // Index of
        int index = fruits.indexOf("Apple");
        System.out.println("Index of Apple: " + index);
        
        // Iterate using for-each
        System.out.println("\nUsing for-each:");
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
        
        // Iterate using Iterator
        System.out.println("\nUsing Iterator:");
        Iterator<String> iterator = fruits.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        
        // Iterate using index
        System.out.println("\nUsing index:");
        for (int i = 0; i < fruits.size(); i++) {
            System.out.println(fruits.get(i));
        }
        
        // SubList
        List<String> subList = fruits.subList(0, 2);
        System.out.println("\nSubList: " + subList);
        
        // Clear all elements
        fruits.clear();
        System.out.println("After clear, size: " + fruits.size());
        
        // Check if empty
        boolean empty = fruits.isEmpty();
        System.out.println("Is empty: " + empty);
    }
}
```

#### When to Use ArrayList

**✅ Use ArrayList when:**
- Frequent **read operations** (random access by index)
- **Search operations** are primary use case
- **Few insertions/deletions** (especially at end)
- **Memory efficiency** is important
- **Single-threaded** environment

**❌ Avoid ArrayList when:**
- Frequent **insertions/deletions** at beginning or middle
- **Thread safety** required (use Vector or Collections.synchronizedList())
- Need **queue or stack** behavior (use LinkedList or ArrayDeque)

**Real-World Use Cases:**
- Displaying product lists in e-commerce
- Search results
- Dashboard data display
- Reading configuration files
- Maintaining history (back/forward navigation)

---

### 2️⃣ LinkedList

#### Overview

**Definition:** Doubly-linked list implementation of List and Deque interfaces.

**Internal Working:**
- Each element is a **Node** containing:
  - Data (element value)
  - Reference to **next** node
  - Reference to **previous** node
- Nodes can be **anywhere in memory** (not contiguous)
- Maintains references to **first** (head) and **last** (tail) nodes

#### Internal Structure

```java
public class LinkedList<E> {
    transient int size = 0;
    transient Node<E> first;  // Pointer to first node
    transient Node<E> last;   // Pointer to last node
    
    // Node class
    private static class Node<E> {
        E item;           // Data
        Node<E> next;     // Reference to next node
        Node<E> prev;     // Reference to previous node
        
        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
    
    // Add at end
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
    
    // Link element at end
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
    }
    
    // Get element (must traverse)
    public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
    
    // Find node at index
    Node<E> node(int index) {
        // Optimization: start from head or tail
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
}
```

#### Visual Representation

**Structure:**
```
null ← [Node1] ↔ [Node2] ↔ [Node3] ↔ [Node4] → null
        ↑                              ↑
       first                         last
        
Node1: prev=null, data="A", next=Node2
Node2: prev=Node1, data="B", next=Node3
Node3: prev=Node2, data="C", next=Node4
Node4: prev=Node3, data="D", next=null
```

**Adding Element at Beginning:**
```
Before:
null ← [B] ↔ [C] ↔ [D] → null

After adding "A" at beginning:
null ← [A] ↔ [B] ↔ [C] ↔ [D] → null
        ↑
      first
```

**Adding Element at End:**
```
Before:
null ← [A] ↔ [B] ↔ [C] → null

After adding "D" at end:
null ← [A] ↔ [B] ↔ [C] ↔ [D] → null
                            ↑
                          last
```

**Removing Element:**
```
Before:
null ← [A] ↔ [B] ↔ [C] ↔ [D] → null

After removing "B":
null ← [A] ↔ [C] ↔ [D] → null
         ↑      ↑
      Update links
```

#### Time Complexity

| Operation | Time Complexity | Explanation |
|-----------|----------------|-------------|
| `get(index)` | **O(n)** | Must traverse from head/tail |
| `add(element)` | **O(1)** | Add at end |
| `addFirst(element)` | **O(1)** | Add at beginning |
| `addLast(element)` | **O(1)** | Add at end |
| `add(index, element)` | **O(n)** | Must find position first |
| `remove(index)` | **O(n)** | Must find position first |
| `removeFirst()` | **O(1)** | Remove first node |
| `removeLast()` | **O(1)** | Remove last node |
| `contains(element)` | **O(n)** | Linear search |
| `size()` | **O(1)** | Return size variable |

#### Example Code

```java
import java.util.*;

public class LinkedListExample {
    public static void main(String[] args) {
        // Create LinkedList
        LinkedList<Integer> list = new LinkedList<>();
        
        // Add elements
        list.add(10);
        list.add(20);
        list.add(30);
        
        // Add at beginning
        list.addFirst(5);   // [5, 10, 20, 30]
        
        // Add at end
        list.addLast(40);   // [5, 10, 20, 30, 40]
        
        // Add at specific index
        list.add(2, 15);    // [5, 10, 15, 20, 30, 40]
        
        // Access elements
        int first = list.getFirst();   // 5
        int last = list.getLast();     // 40
        int second = list.get(1);      // 10
        
        System.out.println("First: " + first);
        System.out.println("Last: " + last);
        
        // Remove elements
        list.removeFirst();        // Remove 5
        list.removeLast();         // Remove 40
        list.remove(1);            // Remove element at index 1
        list.remove(Integer.valueOf(20)); // Remove element 20
        
        System.out.println("After removal: " + list);
        
        // Peek elements (without removing)
        int peekFirst = list.peekFirst();
        int peekLast = list.peekLast();
        
        // Poll elements (remove and return)
        int pollFirst = list.pollFirst();
        int pollLast = list.pollLast();
        
        // Use as Stack (LIFO)
        list.push(100);  // Add to beginning
        int pop = list.pop();  // Remove from beginning
        
        // Use as Queue (FIFO)
        list.offer(200);  // Add to end
        int poll = list.poll();  // Remove from beginning
        
        // Iterate
        for (Integer num : list) {
            System.out.println(num);
        }
    }
}
```

#### When to Use LinkedList

**✅ Use LinkedList when:**
- Frequent **insertions/deletions** at beginning
- Frequent **insertions/deletions** at end
- Implementing **queue** or **stack**
- Need **Deque** operations (both-end access)
- **Sequential access** is acceptable

**❌ Avoid LinkedList when:**
- Need **fast random access** by index
- **Memory** is limited (node overhead)
- Frequent **search operations**
- **Cache performance** is critical

**Real-World Use Cases:**
- Browser history (back/forward buttons)
- Music playlist (next/previous)
- Undo/Redo functionality
- Task scheduling queues
- Print job spooling

---

### 3️⃣ ArrayList vs LinkedList - Complete Comparison

#### Memory Structure Comparison

**ArrayList:**
```
Memory Layout (Contiguous):
[100][101][102][103][104]...
  ↓    ↓    ↓    ↓    ↓
 10   20   30   40   50

+ Better cache performance (locality of reference)
+ Less memory overhead
- Fixed capacity (needs resizing)
```

**LinkedList:**
```
Memory Layout (Scattered):
Memory Address 500: Node{prev=null, data=10, next=700}
Memory Address 700: Node{prev=500, data=20, next=200}
Memory Address 200: Node{prev=700, data=30, next=900}
Memory Address 900: Node{prev=200, data=40, next=null}

+ Dynamic size (no resizing needed)
- Poor cache performance
- More memory overhead (2 references per node)
```

#### Performance Comparison Table

| Operation | ArrayList | LinkedList | Winner |
|-----------|-----------|------------|--------|
| **Access by index** | O(1) | O(n) | ✅ ArrayList |
| **Search** | O(n) | O(n) | 🟰 Same |
| **Add at end** | O(1) amortized | O(1) | 🟰 Same |
| **Add at beginning** | O(n) | O(1) | ✅ LinkedList |
| **Add at middle** | O(n) | O(n) | 🟰 Same (but LinkedList better if position known) |
| **Remove from end** | O(1) | O(1) | 🟰 Same |
| **Remove from beginning** | O(n) | O(1) | ✅ LinkedList |
| **Remove from middle** | O(n) | O(n) | 🟰 Same |
| **Memory usage** | Low | High | ✅ ArrayList |
| **Cache performance** | Good | Poor | ✅ ArrayList |

#### Decision Tree

```
Need fast random access?
    ├─ Yes → ArrayList
    └─ No
        └─ Frequent insert/delete at beginning?
            ├─ Yes → LinkedList
            └─ No → ArrayList (default choice)
```

#### Benchmarking Example

```java
import java.util.*;

public class ListPerformanceComparison {
    public static void main(String[] args) {
        int size = 100000;
        
        // ArrayList Performance
        ArrayList<Integer> arrayList = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            arrayList.add(i);
        }
        long end = System.currentTimeMillis();
        System.out.println("ArrayList add at end: " + (end - start) + " ms");
        
        start = System.currentTimeMillis();
        for (int i = 0; i < 1000; i++) {
            arrayList.add(0, i);  // Add at beginning
        }
        end = System.currentTimeMillis();
        System.out.println("ArrayList add at beginning: " + (end - start) + " ms");
        
        // LinkedList Performance
        LinkedList<Integer> linkedList = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            linkedList.add(i);
        }
        end = System.currentTimeMillis();
        System.out.println("LinkedList add at end: " + (end - start) + " ms");
        
        start = System.currentTimeMillis();
        for (int i = 0; i < 1000; i++) {
            linkedList.addFirst(i);  // Add at beginning
        }
        end = System.currentTimeMillis();
        System.out.println("LinkedList add at beginning: " + (end - start) + " ms");
        
        // Random access
        start = System.currentTimeMillis();
        for (int i = 0; i < 10000; i++) {
            arrayList.get(i);
        }
        end = System.currentTimeMillis();
        System.out.println("ArrayList random access: " + (end - start) + " ms");
        
        start = System.currentTimeMillis();
        for (int i = 0; i < 10000; i++) {
            linkedList.get(i);
        }
        end = System.currentTimeMillis();
        System.out.println("LinkedList random access: " + (end - start) + " ms");
    }
}

/*
Expected Output (approximate):
ArrayList add at end: 5 ms
ArrayList add at beginning: 150 ms
LinkedList add at end: 8 ms
LinkedList add at beginning: 0 ms
ArrayList random access: 0 ms
LinkedList random access: 50 ms
*/
```

---

### 4️⃣ Vector

#### Overview

**Definition:** Synchronized (thread-safe) implementation of List interface. Legacy class from JDK 1.0.

**Key Characteristics:**
1. ✅ **Thread-safe** (all methods synchronized)
2. 🐌 **Slower** than ArrayList (synchronization overhead)
3. 📏 **Grows by 100%** (doubles capacity)
4. 📜 **Legacy class** (part of original Java)
5. ❌ **Not recommended** (use ArrayList with synchronization if needed)

**Why Vector is Legacy:**
- Existed before Collections Framework (JDK 1.0)
- All methods synchronized (overhead even in single-threaded apps)
- Better alternatives available now

**Vector vs ArrayList:**

| Feature | Vector | ArrayList |
|---------|--------|-----------|
| **Thread-Safe** | ✅ Yes (synchronized) | ❌ No |
| **Performance** | 🐌 Slower | ⚡ Faster |
| **Growth Rate** | 100% (doubles) | 50% (1.5x) |
| **Legacy** | Yes (JDK 1.0) | No (Java 1.2) |
| **Recommended** | ❌ No | ✅ Yes |
| **When to Use** | Legacy code | General purpose |

**Modern Alternatives:**
`java
// Instead of Vector, use:
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Or for concurrent access:
List<String> concurrentList = new CopyOnWriteArrayList<>();
`

#### Example Code

```java
import java.util.*;

public class VectorExample {
    public static void main(String[] args) {
        // Create Vector
        Vector<Integer> vector = new Vector<>();
        
        // Add elements (thread-safe)
        vector.add(10);
        vector.add(20);
        vector.add(30);
        
        // Vector-specific methods
        vector.addElement(40);  // Legacy method
        vector.insertElementAt(15, 1);
        
        // Remove elements
        vector.remove(0);
        vector.removeElement(30);  // Legacy method
        vector.removeElementAt(0);
        
        // Access elements
        int first = vector.firstElement();  // Legacy method
        int last = vector.lastElement();    // Legacy method
        int element = vector.elementAt(0);  // Legacy method
        
        // Capacity
        int capacity = vector.capacity();
        System.out.println("Capacity: " + capacity);
        
        // Size
        int size = vector.size();
        System.out.println("Size: " + size);
    }
}
```

#### When to Use Vector

**✅ Use Vector when:**
- Working with **legacy code**
- Existing codebase uses Vector
- Compatibility required with old APIs

**❌ Avoid Vector for new code:**
- Use **ArrayList** + external synchronization
- Use **Collections.synchronizedList()**
- Use **CopyOnWriteArrayList** for concurrent reads

---

## 🎯 Set Interface

### Overview

**Definition:** Collection that contains **no duplicate elements**. Models mathematical set abstraction.

**Key Characteristics:**
1. ❌ **No duplicate elements**
2. ❌ **No indexed access** (cannot access by position)
3. ✅ **Allows one null** (HashSet, LinkedHashSet)
4. ❌ **No ordering guarantee** (except LinkedHashSet and TreeSet)
5. ✅ **Based on equals() and hashCode()**

**Common Methods:**
```java
// Adding elements
boolean add(E element)  // Returns false if duplicate
boolean addAll(Collection<? extends E> c)

// Removing elements
boolean remove(Object o)
boolean removeAll(Collection<?> c)
void clear()

// Querying
boolean contains(Object o)
boolean containsAll(Collection<?> c)
int size()
boolean isEmpty()

// Iteration
Iterator<E> iterator()

// Set operations
boolean retainAll(Collection<?> c)  // Intersection
```

**Set Implementations Comparison:**

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---------------|---------|
| **Internal Structure** | HashMap | HashMap + LinkedList | Red-Black Tree |
| **Order** | ❌ No order | ✅ Insertion order | ✅ Sorted order |
| **Duplicates** | ❌ Not allowed | ❌ Not allowed | ❌ Not allowed |
| **Null** | ✅ One null | ✅ One null | ❌ No null |
| **Performance (add)** | O(1) | O(1) | O(log n) |
| **Performance (contains)** | O(1) | O(1) | O(log n) |
| **Performance (remove)** | O(1) | O(1) | O(log n) |
| **Memory** | Low | Medium | High |
| **Thread-Safe** | ❌ No | ❌ No | ❌ No |
| **Use Case** | Fast operations | Order matters | Sorted data |

---

### 1️⃣ HashSet

#### Overview

**Definition:** Hash table-based implementation of Set interface. Uses HashMap internally.

**Internal Working:**
- Uses **HashMap** internally
- Elements stored as **keys** in HashMap
- All keys map to same dummy **value** (PRESENT object)
- Uses hashCode() and equals() to check uniqueness

**Internal Structure:**
```java
public class HashSet<E> {
    private transient HashMap<E,Object> map;
    
    // Dummy value to associate with Object in backing Map
    private static final Object PRESENT = new Object();
    
    public HashSet() {
        map = new HashMap<>();
    }
    
    public boolean add(E e) {
        return map.put(e, PRESENT) == null;
    }
    
    public boolean remove(Object o) {
        return map.remove(o) == PRESENT;
    }
    
    public boolean contains(Object o) {
        return map.containsKey(o);
    }
    
    public int size() {
        return map.size();
    }
}
```

**How Uniqueness is Determined:**
1. Calculate hashCode() of element
2. Find bucket in HashMap
3. Compare with existing elements using equals()
4. If match found → duplicate, don't add
5. If no match → unique, add to set

#### Example Code

```java
import java.util.*;

public class HashSetExample {
    public static void main(String[] args) {
        // Create HashSet
        HashSet<String> set = new HashSet<>();
        
        // Add elements (no duplicates)
        set.add("Apple");
        set.add("Banana");
        set.add("Cherry");
        set.add("Apple");  // Duplicate - won't be added
        
        System.out.println("Set: " + set);  // [Apple, Banana, Cherry]
        System.out.println("Size: " + set.size());  // 3
        
        // Add null
        set.add(null);  // One null allowed
        set.add(null);  // Duplicate null ignored
        
        // Contains
        boolean hasApple = set.contains("Apple");
        System.out.println("Has Apple: " + hasApple);
        
        // Remove
        boolean removed = set.remove("Banana");
        System.out.println("Removed Banana: " + removed);
        
        // Iterate (no guaranteed order)
        System.out.println("\nIterating:");
        for (String fruit : set) {
            System.out.println(fruit);
        }
        
        // Set operations
        HashSet<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5));
        HashSet<Integer> set2 = new HashSet<>(Arrays.asList(4, 5, 6, 7, 8));
        
        // Union
        HashSet<Integer> union = new HashSet<>(set1);
        union.addAll(set2);
        System.out.println("\nUnion: " + union);  // [1, 2, 3, 4, 5, 6, 7, 8]
        
        // Intersection
        HashSet<Integer> intersection = new HashSet<>(set1);
        intersection.retainAll(set2);
        System.out.println("Intersection: " + intersection);  // [4, 5]
        
        // Difference
        HashSet<Integer> difference = new HashSet<>(set1);
        difference.removeAll(set2);
        System.out.println("Difference: " + difference);  // [1, 2, 3]
        
        // Clear
        set.clear();
        System.out.println("\nAfter clear: " + set.isEmpty());
    }
}
```

#### Custom Objects in HashSet

**Important:** Must override hashCode() and equals() for custom objects!

```java
import java.util.*;

class Employee {
    int id;
    String name;
    
    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    // MUST override hashCode() and equals()
    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Employee employee = (Employee) obj;
        return id == employee.id && Objects.equals(name, employee.name);
    }
    
    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "'}";
    }
}

public class CustomObjectHashSet {
    public static void main(String[] args) {
        HashSet<Employee> employees = new HashSet<>();
        
        employees.add(new Employee(1, "John"));
        employees.add(new Employee(2, "Alice"));
        employees.add(new Employee(1, "John"));  // Duplicate
        
        System.out.println("Employees: " + employees);
        System.out.println("Size: " + employees.size());  // 2
    }
}
```

#### When to Use HashSet

**✅ Use HashSet when:**
- Need **unique elements**
- **Fast operations** required (O(1))
- **Order doesn't matter**
- Frequent **add/remove/contains** operations

**❌ Avoid HashSet when:**
- Need **ordering** (use LinkedHashSet or TreeSet)
- Need **sorted elements** (use TreeSet)
- Need **indexed access** (use ArrayList)

**Real-World Use Cases:**
- Removing duplicates from list
- Checking unique usernames/emails
- Membership testing (is element present?)
- Implementing mathematical sets

---

### 2️⃣ LinkedHashSet

#### Overview

**Definition:** Hash table and linked list implementation of Set. Maintains insertion order.

**Internal Working:**
- Extends **HashSet**
- Uses **LinkedHashMap** internally
- Maintains **doubly-linked list** across all elements
- Preserves insertion order

**Key Difference from HashSet:**
- **HashSet**: No order guarantee
- **LinkedHashSet**: Maintains insertion order

#### Example Code

```java
import java.util.*;

public class LinkedHashSetExample {
    public static void main(String[] args) {
        // Create LinkedHashSet
        LinkedHashSet<String> set = new LinkedHashSet<>();
        
        // Add elements
        set.add("Zebra");
        set.add("Apple");
        set.add("Mango");
        set.add("Banana");
        
        // Maintains insertion order
        System.out.println("LinkedHashSet: " + set);
        // Output: [Zebra, Apple, Mango, Banana]
        
        // Compare with HashSet (no order)
        HashSet<String> hashSet = new HashSet<>();
        hashSet.add("Zebra");
        hashSet.add("Apple");
        hashSet.add("Mango");
        hashSet.add("Banana");
        
        System.out.println("HashSet: " + hashSet);
        // Output: [Apple, Mango, Banana, Zebra] (unpredictable order)
        
        // Iteration in insertion order
        System.out.println("\nIterating LinkedHashSet:");
        for (String fruit : set) {
            System.out.println(fruit);
        }
    }
}
```

#### When to Use LinkedHashSet

**✅ Use LinkedHashSet when:**
- Need **unique elements** + **insertion order**
- Predictable iteration order required
- Cache implementation (LRU-like behavior)

**Performance:**
- Slightly slower than HashSet (maintains links)
- Still O(1) for add/remove/contains

**Real-World Use Cases:**
- Maintaining order of visited pages
- Preserving order of processed items
- Removing duplicates while keeping order

---

### 3️⃣ TreeSet

#### Overview

**Definition:** NavigableSet implementation based on TreeMap (Red-Black tree). Elements stored in sorted order.

**Internal Working:**
- Uses **TreeMap** internally (Red-Black tree)
- Elements stored as **keys** in TreeMap
- **Self-balancing** binary search tree
- Elements always in **sorted order**

**Sorting Options:**
1. **Natural Ordering**: Elements must implement Comparable
2. **Custom Ordering**: Provide Comparator in constructor

**Key Characteristics:**
1. ✅ **Sorted order** (ascending by default)
2. ❌ **No null elements** (throws NullPointerException)
3. 🐌 **Slower operations** O(log n)
4. ✅ **NavigableSet methods** (floor, ceiling, higher, lower)

#### Example Code

```java
import java.util.*;

public class TreeSetExample {
    public static void main(String[] args) {
        // Create TreeSet (natural ordering)
        TreeSet<Integer> set = new TreeSet<>();
        
        // Add elements
        set.add(50);
        set.add(20);
        set.add(80);
        set.add(10);
        set.add(30);
        
        // Always sorted
        System.out.println("TreeSet: " + set);
        // Output: [10, 20, 30, 50, 80]
        
        // Navigation methods
        System.out.println("\nNavigation Methods:");
        System.out.println("First: " + set.first());  // 10
        System.out.println("Last: " + set.last());    // 80
        System.out.println("Lower than 30: " + set.lower(30));  // 20
        System.out.println("Higher than 30: " + set.higher(30)); // 50
        System.out.println("Floor of 25: " + set.floor(25));    // 20
        System.out.println("Ceiling of 25: " + set.ceiling(25)); // 30
        
        // Subset operations
        System.out.println("\nSubset Operations:");
        System.out.println("HeadSet (< 30): " + set.headSet(30));  // [10, 20]
        System.out.println("TailSet (>= 30): " + set.tailSet(30)); // [30, 50, 80]
        System.out.println("SubSet (20-50): " + set.subSet(20, 50)); // [20, 30]
        
        // Polling (remove and return)
        int first = set.pollFirst();  // Remove and return 10
        int last = set.pollLast();    // Remove and return 80
        System.out.println("\nAfter polling: " + set);
        
        // Reverse order
        System.out.println("\nDescending order:");
        TreeSet<Integer> descSet = (TreeSet<Integer>) set.descendingSet();
        System.out.println(descSet);
    }
}
```

#### Custom Comparator Example

```java
import java.util.*;

public class TreeSetComparatorExample {
    public static void main(String[] args) {
        // Descending order using Comparator
        TreeSet<Integer> descendingSet = new TreeSet<>(Collections.reverseOrder());
        descendingSet.add(50);
        descendingSet.add(20);
        descendingSet.add(80);
        descendingSet.add(10);
        
        System.out.println("Descending: " + descendingSet);
        // Output: [80, 50, 20, 10]
        
        // Custom objects with Comparator
        TreeSet<String> nameSet = new TreeSet<>((s1, s2) -> s2.compareTo(s1));
        nameSet.add("John");
        nameSet.add("Alice");
        nameSet.add("Bob");
        
        System.out.println("Names (reverse): " + nameSet);
        // Output: [John, Bob, Alice]
    }
}
```

#### When to Use TreeSet

**✅ Use TreeSet when:**
- Need **sorted elements**
- Need **range operations** (subSet, headSet, tailSet)
- Need **navigation methods** (floor, ceiling, higher, lower)
- Can accept O(log n) performance

**❌ Avoid TreeSet when:**
- Need **faster operations** (use HashSet)
- Elements **cannot be compared**
- Need **null elements**
- Need **frequent access** (TreeSet is slower)

**Real-World Use Cases:**
- Leaderboard/ranking system
- Sorted scores/grades
- Priority-based processing
- Range queries (elements between X and Y)

---

### 🔥 Set Implementations Comparison Summary

```java
import java.util.*;

public class SetComparison {
    public static void main(String[] args) {
        // HashSet - No order, fastest
        Set<String> hashSet = new HashSet<>();
        hashSet.add("Zebra");
        hashSet.add("Apple");
        hashSet.add("Mango");
        System.out.println("HashSet: " + hashSet);
        // Output: Random order (e.g., [Apple, Mango, Zebra])
        
        // LinkedHashSet - Insertion order
        Set<String> linkedHashSet = new LinkedHashSet<>();
        linkedHashSet.add("Zebra");
        linkedHashSet.add("Apple");
        linkedHashSet.add("Mango");
        System.out.println("LinkedHashSet: " + linkedHashSet);
        // Output: [Zebra, Apple, Mango]
        
        // TreeSet - Sorted order
        Set<String> treeSet = new TreeSet<>();
        treeSet.add("Zebra");
        treeSet.add("Apple");
        treeSet.add("Mango");
        System.out.println("TreeSet: " + treeSet);
        // Output: [Apple, Mango, Zebra]
    }
}
```

**Quick Decision Guide:**

```
Need unique elements?
├─ Yes
│   ├─ Need sorted order?
│   │   └─ Yes → TreeSet
│   └─ Need insertion order?
│       ├─ Yes → LinkedHashSet
│       └─ No → HashSet (default choice)
└─ No → Use List (ArrayList/LinkedList)
```

---

## 🎯 Queue Interface

### Overview

**Definition:** Collection designed for holding elements **prior to processing**. Typically orders elements in **FIFO** (First-In-First-Out) manner.

**Key Characteristics:**
1. ✅ **FIFO order** (typical)
2. ✅ **Two forms of methods** (exception vs special value)
3. ✅ **Can be bounded** (capacity limit) or unbounded
4. ❌ **No index-based access**

**Queue Methods:**

| Operation | Throws Exception | Returns Special Value |
|-----------|------------------|------------------------|
| **Insert** | dd(e) | offer(e) - false if full |
| **Remove** | 
emove() | poll() - null if empty |
| **Examine** | element() | peek() - null if empty |

**Queue Implementations:**

| Implementation | Order | Null | Thread-Safe | Performance |
|----------------|-------|------|-------------|-------------|
| **LinkedList** | FIFO | ✅ Yes | ❌ No | Fast |
| **PriorityQueue** | Priority (heap) | ❌ No | ❌ No | O(log n) |
| **ArrayDeque** | FIFO/LIFO | ❌ No | ❌ No | Fastest |
| **BlockingQueue** | FIFO | ❌ No | ✅ Yes | Thread-safe |

---

### PriorityQueue

#### Overview

**Definition:** Queue that orders elements based on their **priority** (natural ordering or Comparator).

**Internal Working:**
- Uses **binary heap** data structure
- **Min-heap** by default (smallest element at head)
- Can be **max-heap** using Collections.reverseOrder()
- **Not thread-safe**

**Key Characteristics:**
1. 🔢 **Priority-based ordering**
2. ❌ **No null elements**
3. ❌ **Not sorted** (only head is guaranteed smallest)
4. 🐌 **O(log n)** for add/remove
5. ⚡ **O(1)** for peek

#### Example Code

```java
import java.util.*;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Min-heap (default)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        minHeap.offer(30);
        minHeap.offer(10);
        minHeap.offer(50);
        minHeap.offer(20);
        
        System.out.println("Min-Heap:");
        while (!minHeap.isEmpty()) {
            System.out.println(minHeap.poll());  // 10, 20, 30, 50
        }
        
        // Max-heap
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        
        maxHeap.offer(30);
        maxHeap.offer(10);
        maxHeap.offer(50);
        maxHeap.offer(20);
        
        System.out.println("\nMax-Heap:");
        while (!maxHeap.isEmpty()) {
            System.out.println(maxHeap.poll());  // 50, 30, 20, 10
        }
        
        // Custom objects with Comparator
        PriorityQueue<String> nameQueue = new PriorityQueue<>(
            (s1, s2) -> s1.length() - s2.length()
        );
        
        nameQueue.offer("John");
        nameQueue.offer("Alice");
        nameQueue.offer("Bob");
        
        System.out.println("\nBy length:");
        while (!nameQueue.isEmpty()) {
            System.out.println(nameQueue.poll());  // Bob, John, Alice
        }
    }
}
```

#### Real-World Use Cases
- Task scheduling (priority-based)
- Dijkstra's shortest path algorithm
- Huffman coding
- A* search algorithm
- CPU scheduling
- Event simulation

---

### Deque Interface

#### Overview

**Definition:** **Double Ended Queue** - allows insertion and removal at **both ends**.

**Can be used as:**
1. **Queue** (FIFO)
2. **Stack** (LIFO)
3. **Deque** (both ends)

**Deque Methods:**

| Operation | First Element | Last Element |
|-----------|---------------|--------------|
| **Insert** | ddFirst() / offerFirst() | ddLast() / offerLast() |
| **Remove** | 
emoveFirst() / pollFirst() | 
emoveLast() / pollLast() |
| **Examine** | getFirst() / peekFirst() | getLast() / peekLast() |

**Deque as Stack:**
- push(e) = ddFirst(e)
- pop() = 
emoveFirst()
- peek() = peekFirst()

**Deque Implementations:**
- **ArrayDeque** (recommended for stack/queue)
- **LinkedList** (implements both List and Deque)

---

### ArrayDeque

#### Overview

**Definition:** Resizable array implementation of Deque interface.

**Why ArrayDeque over Stack/LinkedList:**
✅ **Faster** than LinkedList (cache-friendly)  
✅ **Faster** than Stack (not synchronized)  
✅ **No capacity restrictions** (grows dynamically)  
❌ **No null elements**

#### Example Code

```java
import java.util.*;

public class ArrayDequeExample {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();
        
        // Add at both ends
        deque.addFirst("A");   // [A]
        deque.addLast("B");    // [A, B]
        deque.addFirst("C");   // [C, A, B]
        deque.addLast("D");    // [C, A, B, D]
        
        System.out.println("Deque: " + deque);
        
        // Peek at both ends
        System.out.println("First: " + deque.peekFirst());  // C
        System.out.println("Last: " + deque.peekLast());    // D
        
        // Remove from both ends
        System.out.println("Removed first: " + deque.removeFirst());  // C
        System.out.println("Removed last: " + deque.removeLast());    // D
        
        System.out.println("After removal: " + deque);  // [A, B]
        
        // Use as Stack (LIFO)
        System.out.println("\nUsing as Stack:");
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(1);
        stack.push(2);
        stack.push(3);
        
        while (!stack.isEmpty()) {
            System.out.println(stack.pop());  // 3, 2, 1
        }
        
        // Use as Queue (FIFO)
        System.out.println("\nUsing as Queue:");
        Deque<Integer> queue = new ArrayDeque<>();
        queue.offer(1);
        queue.offer(2);
        queue.offer(3);
        
        while (!queue.isEmpty()) {
            System.out.println(queue.poll());  // 1, 2, 3
        }
    }
}
```

#### When to Use ArrayDeque

**✅ Use ArrayDeque when:**
- Need **stack** (instead of Stack class)
- Need **queue** (instead of LinkedList)
- Need **deque** (both-end operations)
- **Performance** is important

**Real-World Use Cases:**
- Browser history (back/forward)
- Undo/Redo functionality
- Sliding window problems
- BFS/DFS in graphs
- Palindrome checking

---

This is the first part. The file is getting very long. Would you like me to continue with the Map Interface, HashMap Deep Dive, Comparable vs Comparator, and remaining sections?

## 🎯 Map Interface

### Overview

**Definition:** Object that maps **keys to values**. Cannot contain duplicate keys; each key maps to at most one value.

**Key Characteristics:**
1. **Key-Value pairs** (entries)
2. ❌ **No duplicate keys**
3. ✅ **Duplicate values allowed**
4. ❌ **NOT part of Collection hierarchy**
5. ✅ **Entry interface** for key-value pairs

**Why Map is NOT a Collection:**
- Doesn't extend Collection interface
- Represents mapping, not collection of elements
- Has its own hierarchy

**Common Methods:**
```java
// Basic operations
V put(K key, V value)
V get(Object key)
V remove(Object key)
boolean containsKey(Object key)
boolean containsValue(Object value)

// Size operations
int size()
boolean isEmpty()
void clear()

// Views
Set<K> keySet()                    // Set of all keys
Collection<V> values()             // Collection of all values
Set<Map.Entry<K,V>> entrySet()    // Set of key-value pairs

// Java 8+ methods
V getOrDefault(Object key, V defaultValue)
V putIfAbsent(K key, V value)
V compute(K key, BiFunction<K,V,V> remappingFunction)
V merge(K key, V value, BiFunction<V,V,V> remappingFunction)
```

**Map Implementations Comparison:**

| Feature | HashMap | LinkedHashMap | TreeMap | Hashtable |
|---------|---------|---------------|---------|-----------|
| **Order** | ❌ No order | ✅ Insertion order | ✅ Sorted (keys) | ❌ No order |
| **Null Key** | ✅ One null | ✅ One null | ❌ No null | ❌ No null |
| **Null Value** | ✅ Multiple | ✅ Multiple | ✅ Multiple | ❌ No null |
| **Performance** | O(1) | O(1) | O(log n) | O(1) |
| **Thread-Safe** | ❌ No | ❌ No | ❌ No | ✅ Yes |
| **Structure** | Hash Table | Hash Table + LinkedList | Red-Black Tree | Hash Table |
| **Since** | Java 1.2 | Java 1.4 | Java 1.2 | JDK 1.0 |
| **Use Case** | General purpose | Order matters | Sorted keys | Legacy |

---

## 🔥 HashMap Deep Dive

### Overview

**HashMap** is the **most important Map implementation** for interviews!

**Definition:** Hash table-based implementation of Map interface. Provides O(1) average time for get/put operations.

**Key Features:**
- 🚀 **O(1) average time** for get/put/remove
- ✅ **One null key** allowed
- ✅ **Multiple null values** allowed
- ❌ **Not thread-safe**
- ❌ **No ordering guarantee**
- 📏 **Default capacity:** 16
- ⚖️ **Default load factor:** 0.75

### Internal Structure

**Components:**
1. **Array of buckets** (Node[] table)
2. **LinkedList** (for collision handling)
3. **Red-Black Tree** (Java 8+, when bucket size ≥ 8)

**Node Class:**
```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;        // Cached hash code
    final K key;
    V value;
    Node<K,V> next;        // Next node (for LinkedList)
    
    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }
}
```

**HashMap Class:**
```java
public class HashMap<K,V> {
    Node<K,V>[] table;     // Array of buckets
    int size;              // Number of key-value pairs
    int threshold;         // Resize threshold
    float loadFactor;      // Load factor
    
    static final int DEFAULT_INITIAL_CAPACITY = 16;
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
}
```

### Visual Representation

```
HashMap Structure (Capacity = 16):

Index   Bucket (LinkedList or Tree)
  0  →  null
  1  →  [Node: ("key1","val1")] → null
  2  →  [Node: ("key2","val2")] → [Node: ("key3","val3")] → null  (Collision)
  3  →  null
  ...
  15 →  [Node: ("key15","val15")] → null
```

### How HashMap Works - Step by Step

#### 1️⃣ **Put Operation: put(key, value)**

**Process:**

**Step 1: Calculate Hash Code**
```java
int hash = key.hashCode();
// Apply hash function to reduce collisions
hash = hash ^ (hash >>> 16);  // Spread higher bits
```

**Step 2: Calculate Bucket Index**
```java
int index = (capacity - 1) & hash;  // Bitwise AND (faster than modulo)
// Example: (16 - 1) & hash = 15 & hash
```

**Step 3: Check Bucket**
- **If bucket is empty:** Place new node
- **If bucket has nodes:** Handle collision

**Step 4: Handle Collision**
```java
// Traverse linked list/tree in bucket
Node<K,V> current = table[index];
while (current != null) {
    if (current.hash == hash && 
        (current.key == key || key.equals(current.key))) {
        // Key exists - replace value
        V oldValue = current.value;
        current.value = value;
        return oldValue;
    }
    current = current.next;
}
// Key not found - add new node at end
addNode(index, hash, key, value);
```

**Step 5: Check Load Factor & Resize**
```java
if (++size > threshold) {
    resize();  // Double capacity
}
```

#### 2️⃣ **Get Operation: get(key)**

**Process:**
```java
// 1. Calculate hash
int hash = key.hashCode();
hash = hash ^ (hash >>> 16);

// 2. Find bucket
int index = (capacity - 1) & hash;

// 3. Search in bucket
Node<K,V> node = table[index];
while (node != null) {
    if (node.hash == hash && 
        (node.key == key || key.equals(node.key))) {
        return node.value;  // Found!
    }
    node = node.next;
}
return null;  // Not found
```

### Collision Handling

**What is Collision?**
When two different keys map to the same bucket index.

**Example:**
```java
"Aa".hashCode() = 2112
"BB".hashCode() = 2112  // Same hash code!
// Both map to same bucket → COLLISION
```

**Resolution Strategy:**

**Before Java 8: Chaining (LinkedList)**
```
Bucket 5:
[Node1: ("Aa", 1)] → [Node2: ("BB", 2)] → [Node3: ("CC", 3)] → null

Time Complexity: O(n) in worst case
```

**After Java 8: Treeify (Red-Black Tree)**

When bucket size ≥ 8, converts to tree:
```
Bucket 5:
         [Node4]
        /      \
    [Node2]    [Node6]
    /    \      /    \
 [N1]   [N3]  [N5]  [N7]

Time Complexity: O(log n) in worst case
```

**Treeify Conditions:**
```java
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;

if (bucketSize >= 8) {
    treeifyBucket();  // Convert to tree
}
if (treeSize <= 6) {
    untreeify();  // Convert back to list
}
```

### Load Factor and Resizing

**Key Terms:**

**1. Capacity:**
- Size of internal array
- Always power of 2 (16, 32, 64, 128...)
- Enables efficient bitwise operations

**2. Load Factor:**
- Threshold for resizing
- Default: 0.75 (75%)
- Formula: 	hreshold = capacity × loadFactor

**3. Threshold:**
- Point at which HashMap resizes
- Example: 16 × 0.75 = 12

**Growth Timeline:**
```
Size → Capacity → Threshold → Action
 0      16         12          Initial
12      16         12          Full
13      32         24          RESIZE!
24      32         24          Full
25      64         48          RESIZE!
```

**Resizing Process (Rehashing):**

**Step 1:** Create new array (double capacity)
```java
Node<K,V>[] newTable = new Node[oldCapacity * 2];
```

**Step 2:** Recalculate index for each entry
```java
for (Node<K,V> node : oldTable) {
    while (node != null) {
        int newIndex = (newCapacity - 1) & node.hash;
        // Place in new bucket
        newTable[newIndex] = node;
        node = node.next;
    }
}
```

**Step 3:** Update threshold
```java
threshold = newCapacity * loadFactor;
```

**Why Resize is Expensive:**
- O(n) time complexity
- Must iterate all entries
- Recalculate indices
- Copy to new buckets

### hashCode() and equals() Contract

**The Contract:**

**Rule 1:** If .equals(b) is true, then .hashCode() == b.hashCode() **MUST** be true

**Rule 2:** If .hashCode() == b.hashCode(), .equals(b) **may or may not** be true

**Why Important?**
- HashMap uses hashCode() to find bucket
- Uses equals() to find exact key within bucket
- Without proper implementation → HashMap won't work!

**Problem Example (BAD):**
```java
class Employee {
    int id;
    String name;
    
    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Employee)) return false;
        Employee emp = (Employee) obj;
        return id == emp.id && Objects.equals(name, emp.name);
    }
    
    // ❌ NOT overriding hashCode()!
}

// Usage
Map<Employee, String> map = new HashMap<>();
Employee e1 = new Employee(1, "John");
Employee e2 = new Employee(1, "John");

map.put(e1, "Engineer");
System.out.println(map.get(e2));  // null ❌ WRONG!
// e1 and e2 are equal but have different hash codes
```

**Solution (GOOD):**
```java
class Employee {
    int id;
    String name;
    
    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id, name);  // ✅ CORRECT
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Employee)) return false;
        Employee emp = (Employee) obj;
        return id == emp.id && Objects.equals(name, emp.name);
    }
}

// Usage
Map<Employee, String> map = new HashMap<>();
Employee e1 = new Employee(1, "John");
Employee e2 = new Employee(1, "John");

map.put(e1, "Engineer");
System.out.println(map.get(e2));  // "Engineer" ✅ CORRECT!
```

### HashMap Example Code

```java
import java.util.*;

public class HashMapExample {
    public static void main(String[] args) {
        // Create HashMap
        HashMap<String, Integer> map = new HashMap<>();
        
        // Put entries
        map.put("John", 25);
        map.put("Alice", 30);
        map.put("Bob", 28);
        map.put(null, 0);  // Null key allowed
        map.put("Charlie", null);  // Null value allowed
        
        // Get value
        int age = map.get("John");  // 25
        int unknown = map.getOrDefault("Unknown", 0);  // 0
        
        // Check existence
        boolean hasJohn = map.containsKey("John");  // true
        boolean has25 = map.containsValue(25);  // true
        
        // Remove entry
        Integer removed = map.remove("Bob");  // Returns 28
        
        // Size
        int size = map.size();
        
        // Update existing key
        map.put("John", 26);  // Replaces old value
        
        // Put if absent
        map.putIfAbsent("David", 35);
        map.putIfAbsent("John", 30);  // Won't update (key exists)
        
        // Iterate - Method 1: entrySet (BEST)
        System.out.println("Method 1: entrySet");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }
        
        // Iterate - Method 2: keySet
        System.out.println("\nMethod 2: keySet");
        for (String key : map.keySet()) {
            System.out.println(key + " = " + map.get(key));
        }
        
        // Iterate - Method 3: values
        System.out.println("\nMethod 3: values");
        for (Integer value : map.values()) {
            System.out.println(value);
        }
        
        // Iterate - Method 4: forEach (Java 8+)
        System.out.println("\nMethod 4: forEach");
        map.forEach((key, value) -> 
            System.out.println(key + " = " + value)
        );
        
        // Compute operations (Java 8+)
        map.compute("John", (k, v) -> v + 1);  // Increment age
        
        // Merge operation
        map.merge("Alice", 1, (oldVal, newVal) -> oldVal + newVal);
        
        // Clear all entries
        map.clear();
        System.out.println("\nAfter clear: " + map.isEmpty());
    }
}
```

### Time Complexity

| Operation | Average Case | Worst Case (before Java 8) | Worst Case (Java 8+) |
|-----------|--------------|----------------------------|----------------------|
| put(key, value) | **O(1)** | O(n) | O(log n) |
| get(key) | **O(1)** | O(n) | O(log n) |
| 
emove(key) | **O(1)** | O(n) | O(log n) |
| containsKey(key) | **O(1)** | O(n) | O(log n) |
| containsValue(value) | O(n) | O(n) | O(n) |

### HashMap vs Hashtable

| Feature | HashMap | Hashtable |
|---------|---------|-----------|
| **Thread-Safe** | ❌ No (faster) | ✅ Yes (slower) |
| **Null Key** | ✅ One null | ❌ Not allowed |
| **Null Value** | ✅ Multiple | ❌ Not allowed |
| **Performance** | ⚡ Fast | 🐌 Slow (synchronized) |
| **Legacy** | No (Java 1.2) | Yes (JDK 1.0) |
| **Iterator** | Fail-fast | Fail-fast |
| **Extends** | AbstractMap | Dictionary (legacy) |
| **Recommended** | ✅ Yes | ❌ No (use ConcurrentHashMap) |

**Modern Alternatives to Hashtable:**
```java
// Option 1: Synchronized HashMap
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// Option 2: ConcurrentHashMap (BEST for concurrent access)
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
```

---

### LinkedHashMap

**Definition:** Hash table and linked list implementation. Maintains **insertion order**.

**Key Features:**
- ✅ **Maintains insertion order**
- ✅ **Optional access order** (LRU cache)
- ⚡ **O(1) operations** (slightly slower than HashMap)
- ✅ **One null key, multiple null values**

**Access Order Mode:**
```java
// Insertion order (default)
Map<String, Integer> map1 = new LinkedHashMap<>();

// Access order (useful for LRU cache)
Map<String, Integer> map2 = new LinkedHashMap<>(16, 0.75f, true);
```

**Example:**
```java
import java.util.*;

public class LinkedHashMapExample {
    public static void main(String[] args) {
        LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
        
        map.put("One", 1);
        map.put("Two", 2);
        map.put("Three", 3);
        
        // Iterates in insertion order
        for (String key : map.keySet()) {
            System.out.println(key + " = " + map.get(key));
        }
        // Output: One=1, Two=2, Three=3
    }
}
```

**LRU Cache Implementation:**
```java
import java.util.*;

class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);  // access order
        this.capacity = capacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;  // Remove oldest if size exceeds capacity
    }
}

public class LRUCacheExample {
    public static void main(String[] args) {
        LRUCache<Integer, String> cache = new LRUCache<>(3);
        
        cache.put(1, "A");
        cache.put(2, "B");
        cache.put(3, "C");
        System.out.println(cache);  // {1=A, 2=B, 3=C}
        
        cache.get(1);  // Access key 1 (moves to end)
        System.out.println(cache);  // {2=B, 3=C, 1=A}
        
        cache.put(4, "D");  // Adds new entry, removes oldest (2)
        System.out.println(cache);  // {3=C, 1=A, 4=D}
    }
}
```

---

### TreeMap

**Definition:** Red-Black tree-based NavigableMap implementation. Keys always in **sorted order**.

**Key Features:**
- ✅ **Sorted keys** (natural or custom order)
- ❌ **No null key** (throws NullPointerException)
- ✅ **Multiple null values**
- 🐌 **O(log n)** operations
- ✅ **NavigableMap methods**

**Example:**
```java
import java.util.*;

public class TreeMapExample {
    public static void main(String[] args) {
        // Natural ordering (ascending)
        TreeMap<String, Integer> map = new TreeMap<>();
        
        map.put("Charlie", 30);
        map.put("Alice", 25);
        map.put("Bob", 28);
        
        // Iterates in sorted order of keys
        for (String key : map.keySet()) {
            System.out.println(key + " = " + map.get(key));
        }
        // Output: Alice=25, Bob=28, Charlie=30
        
        // Navigation methods
        String firstKey = map.firstKey();  // "Alice"
        String lastKey = map.lastKey();    // "Charlie"
        String lower = map.lowerKey("Bob");  // "Alice"
        String higher = map.higherKey("Bob"); // "Charlie"
        
        // SubMap operations
        SortedMap<String, Integer> subMap = map.subMap("Alice", "Charlie");
        System.out.println("SubMap: " + subMap);  // {Alice=25, Bob=28}
        
        // Descending order
        TreeMap<Integer, String> descMap = new TreeMap<>(Collections.reverseOrder());
        descMap.put(1, "One");
        descMap.put(2, "Two");
        descMap.put(3, "Three");
        System.out.println(descMap);  // {3=Three, 2=Two, 1=One}
    }
}
```

---

## 🎯 Collections Utility Class

**Definition:** Utility class with **static methods** for operating on collections.

**Common Methods:**

### 1. Sorting and Searching
```java
List<Integer> list = Arrays.asList(5, 2, 8, 1, 9);

// Sort ascending
Collections.sort(list);  // [1, 2, 5, 8, 9]

// Sort descending
Collections.sort(list, Collections.reverseOrder());  // [9, 8, 5, 2, 1]

// Binary search (list must be sorted)
int index = Collections.binarySearch(list, 5);  // 2

// Reverse
Collections.reverse(list);  // [1, 8, 5, 2, 9]

// Shuffle
Collections.shuffle(list);  // Random order
```

### 2. Finding Extremes
```java
List<Integer> list = Arrays.asList(5, 2, 8, 1, 9);

// Max and Min
int max = Collections.max(list);  // 9
int min = Collections.min(list);  // 1

// Frequency (count occurrences)
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3);
int freq = Collections.frequency(numbers, 3);  // 3
```

### 3. Modification
```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

// Fill
Collections.fill(list, 0);  // [0, 0, 0, 0, 0]

// Copy
List<Integer> dest = new ArrayList<>(Collections.nCopies(5, 0));
Collections.copy(dest, list);

// Replace all
Collections.replaceAll(list, 0, 10);  // Replace all 0s with 10

// Swap
Collections.swap(list, 0, 4);  // Swap elements at index 0 and 4

// Rotate
Collections.rotate(list, 2);  // Rotate right by 2 positions
```

### 4. Synchronization Wrappers
```java
// Synchronized List
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Synchronized Set
Set<String> syncSet = Collections.synchronizedSet(new HashSet<>());

// Synchronized Map
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// IMPORTANT: Must synchronize when iterating
synchronized(syncList) {
    for (String item : syncList) {
        System.out.println(item);
    }
}
```

### 5. Unmodifiable Wrappers
```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

// Unmodifiable (read-only)
List<String> unmodList = Collections.unmodifiableList(list);
Set<String> unmodSet = Collections.unmodifiableSet(new HashSet<>(list));
Map<String, Integer> unmodMap = Collections.unmodifiableMap(new HashMap<>());

// Attempts to modify throw UnsupportedOperationException
// unmodList.add("D");  // ❌ Throws exception
```

### 6. Empty and Singleton Collections
```java
// Empty collections (immutable)
List<String> emptyList = Collections.emptyList();
Set<String> emptySet = Collections.emptySet();
Map<String, Integer> emptyMap = Collections.emptyMap();

// Singleton collections (immutable, one element)
Set<String> singletonSet = Collections.singleton("Only");
List<String> singletonList = Collections.singletonList("Only");
Map<String, Integer> singletonMap = Collections.singletonMap("key", 1);
```

---

## 🎯 Comparable vs Comparator

### Overview

**Both used for sorting objects**, but serve different purposes.

**Comparison Table:**

| Feature | Comparable | Comparator |
|---------|------------|------------|
| **Package** | java.lang | java.util |
| **Method** | int compareTo(T o) | int compare(T o1, T o2) |
| **Implementation** | By class itself | Separate class/lambda |
| **Modify Class** | Yes (must modify) | No (external) |
| **Sorting Orders** | One (natural) | Multiple (custom) |
| **Usage** | Collections.sort(list) | Collections.sort(list, comparator) |
| **When to Use** | Natural ordering | Custom ordering |

### Comparable Interface

**Definition:** Defines **natural ordering** of objects.

**When to Use:**
- Class has obvious natural ordering
- You control the class source code
- Single sorting order sufficient

**Example:**
```java
import java.util.*;

class Student implements Comparable<Student> {
    String name;
    int age;
    
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public int compareTo(Student other) {
        return this.age - other.age;  // Sort by age (ascending)
    }
    
    @Override
    public String toString() {
        return name + "(" + age + ")";
    }
}

public class ComparableExample {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("John", 25));
        students.add(new Student("Alice", 22));
        students.add(new Student("Bob", 28));
        
        Collections.sort(students);  // Uses compareTo()
        System.out.println(students);
        // Output: [Alice(22), John(25), Bob(28)]
    }
}
```

### Comparator Interface

**Definition:** Defines **custom ordering** separate from class.

**When to Use:**
- Need multiple sorting criteria
- Cannot modify class (third-party)
- Different sorting in different contexts

**Example:**
```java
import java.util.*;

class Student {
    String name;
    int age;
    
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    
    @Override
    public String toString() {
        return name + "(" + age + ")";
    }
}

// Comparator 1: Sort by name
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.getName().compareTo(s2.getName());
    }
}

// Comparator 2: Sort by age
class AgeComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.getAge() - s2.getAge();
    }
}

public class ComparatorExample {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("John", 25));
        students.add(new Student("Alice", 22));
        students.add(new Student("Bob", 28));
        
        // Sort by name
        Collections.sort(students, new NameComparator());
        System.out.println("By name: " + students);
        // Output: [Alice(22), Bob(28), John(25)]
        
        // Sort by age
        Collections.sort(students, new AgeComparator());
        System.out.println("By age: " + students);
        // Output: [Alice(22), John(25), Bob(28)]
        
        // Lambda expression (Java 8+)
        Collections.sort(students, (s1, s2) -> s1.getName().compareTo(s2.getName()));
        
        // Method reference (Java 8+)
        students.sort(Comparator.comparing(Student::getName));
        students.sort(Comparator.comparingInt(Student::getAge));
        
        // Reverse order
        students.sort(Comparator.comparing(Student::getName).reversed());
        
        // Multiple criteria (sort by age, then name)
        students.sort(Comparator.comparingInt(Student::getAge)
                               .thenComparing(Student::getName));
        
        // Null handling
        students.sort(Comparator.nullsFirst(Comparator.comparing(Student::getName)));
        students.sort(Comparator.nullsLast(Comparator.comparing(Student::getName)));
    }
}
```

### Java 8+ Comparator Methods

```java
import java.util.*;

public class Java8ComparatorExample {
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("John", 25),
            new Student("Alice", 22),
            new Student("Bob", 28),
            new Student("Charlie", 22)
        );
        
        // comparing - create comparator from function
        students.sort(Comparator.comparing(Student::getName));
        
        // comparingInt, comparingLong, comparingDouble
        students.sort(Comparator.comparingInt(Student::getAge));
        
        // thenComparing - chain comparisons
        students.sort(Comparator.comparingInt(Student::getAge)
                               .thenComparing(Student::getName));
        
        // reversed - reverse order
        students.sort(Comparator.comparing(Student::getAge).reversed());
        
        // naturalOrder, reverseOrder
        List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);
        numbers.sort(Comparator.naturalOrder());    // Ascending
        numbers.sort(Comparator.reverseOrder());    // Descending
        
        // nullsFirst, nullsLast
        List<String> names = Arrays.asList("John", null, "Alice", "Bob");
        names.sort(Comparator.nullsFirst(Comparator.naturalOrder()));
        // Output: [null, Alice, Bob, John]
        
        names.sort(Comparator.nullsLast(Comparator.naturalOrder()));
        // Output: [Alice, Bob, John, null]
    }
}
```

---

## 🎯 Iteration Techniques

### 1. For-Each Loop (Enhanced For Loop)
```java
List<String> list = Arrays.asList("A", "B", "C");

for (String item : list) {
    System.out.println(item);
}

// Cannot modify list during iteration
// Cannot access index
```

### 2. Iterator
```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    System.out.println(item);
    
    // Can remove during iteration
    if (item.equals("B")) {
        iterator.remove();  // Safe removal
    }
}
```

### 3. ListIterator (Bidirectional)
```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

ListIterator<String> listIterator = list.listIterator();

// Forward iteration
while (listIterator.hasNext()) {
    System.out.println(listIterator.next());
}

// Backward iteration
while (listIterator.hasPrevious()) {
    System.out.println(listIterator.previous());
}

// Can add/set during iteration
listIterator.add("D");
listIterator.set("E");
```

### 4. forEach() Method (Java 8+)
```java
List<String> list = Arrays.asList("A", "B", "C");

// List
list.forEach(item -> System.out.println(item));
list.forEach(System.out::println);  // Method reference

// Map
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);

map.forEach((key, value) -> 
    System.out.println(key + " = " + value)
);
```

### 5. Stream API (Java 8+)
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Filter and print
numbers.stream()
       .filter(n -> n % 2 == 0)
       .forEach(System.out::println);

// Map and collect
List<Integer> squared = numbers.stream()
                               .map(n -> n * n)
                               .collect(Collectors.toList());

// Reduce
int sum = numbers.stream()
                 .reduce(0, (a, b) -> a + b);
```

### 6. Index-Based (for loop)
```java
List<String> list = Arrays.asList("A", "B", "C");

for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}

// Can modify list
// Can access index
```

### Iteration Comparison

| Method | Modify Collection | Access Index | Direction | Performance |
|--------|-------------------|--------------|-----------|-------------|
| **For-Each** | ❌ No | ❌ No | Forward | Fast |
| **Iterator** | ✅ Yes (remove only) | ❌ No | Forward | Fast |
| **ListIterator** | ✅ Yes (add/set/remove) | ✅ Yes | Both | Fast |
| **forEach()** | ❌ No | ❌ No | Forward | Fast |
| **Stream** | ❌ No (creates new) | ❌ No | Forward | Variable |
| **Index-based** | ✅ Yes | ✅ Yes | Both | ArrayList: Fast, LinkedList: Slow |

---

## 🎯 Thread-Safe Collections

### Problem: Collections are NOT Thread-Safe

```java
Map<String, Integer> map = new HashMap<>();

// Thread 1
map.put("A", 1);

// Thread 2
map.put("B", 2);

// Result: DATA CORRUPTION! 💥
```

### Solutions

### 1. Collections.synchronizedXxx()
```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
Set<String> syncSet = Collections.synchronizedSet(new HashSet<>());
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// Must synchronize when iterating
synchronized(syncList) {
    for (String item : syncList) {
        System.out.println(item);
    }
}
```

**Drawback:** Locks entire collection (poor performance)

### 2. ConcurrentHashMap (BEST for Maps)
```java
Map<String, Integer> map = new ConcurrentHashMap<>();

// Thread-safe without locking entire map
map.put("A", 1);
map.get("A");

// Atomic operations
map.putIfAbsent("B", 2);
map.computeIfAbsent("C", k -> 3);
```

**Benefits:**
- Better performance (segment locking)
- No need for external synchronization
- Atomic operations

### 3. CopyOnWriteArrayList (Best for Read-Heavy)
```java
List<String> list = new CopyOnWriteArrayList<>();

// Thread-safe
list.add("A");
list.add("B");

// Iteration doesn't need synchronization
for (String item : list) {
    System.out.println(item);
}
```

**Benefits:**
- No ConcurrentModificationException
- Fast reads (no locking)
- Slow writes (creates copy)

**Use When:** Many reads, few writes

### Thread-Safe Collections Comparison

| Collection | Thread-Safe Alternative | Performance | Use Case |
|-----------|-------------------------|-------------|----------|
| **ArrayList** | CopyOnWriteArrayList | Read: Fast, Write: Slow | Read-heavy |
| **HashSet** | ConcurrentHashMap.newKeySet() | Good | General |
| **HashMap** | ConcurrentHashMap | Excellent | General (BEST) |
| **TreeMap** | ConcurrentSkipListMap | Good | Sorted keys |
| **LinkedList** | ConcurrentLinkedQueue | Good | Queue |

---

## 🔥 Interview Questions

### 1. What is the difference between List, Set, and Map?

**Answer:**

| Feature | List | Set | Map |
|---------|------|-----|-----|
| **Duplicates** | ✅ Allowed | ❌ Not allowed | Keys: ❌ Values: ✅ |
| **Order** | ✅ Ordered | Depends on implementation | Depends on implementation |
| **Null** | ✅ Multiple | ✅ One (HashSet) | ✅ One null key |
| **Index** | ✅ Yes | ❌ No | ❌ No |
| **Interface** | Collection | Collection | NOT Collection |

### 2. How does HashMap work internally?

**Answer:**
- Uses **array of buckets** (Node[])
- Calculates **hashCode()** of key
- Determines **bucket index** using (capacity - 1) & hash
- Handles **collisions** using LinkedList (< 8 nodes) or Tree (≥ 8 nodes)
- **Resizes** when size > capacity × loadFactor (0.75)
- **O(1) average** time complexity

### 3. Why override hashCode() and equals()?

**Answer:**
- HashMap uses **hashCode()** to find bucket
- Uses **equals()** to find exact key
- **Contract:** If .equals(b), then .hashCode() == b.hashCode()
- Without proper implementation, HashMap won't work correctly

### 4. ArrayList vs LinkedList?

**Answer:**

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| **Access** | O(1) Fast | O(n) Slow |
| **Insert (end)** | O(1) | O(1) |
| **Insert (start)** | O(n) | O(1) Fast |
| **Memory** | Less | More |
| **Use Case** | Random access | Insert/delete at ends |

### 5. HashSet vs TreeSet vs LinkedHashSet?

**Answer:**

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---------------|---------|
| **Order** | No | Insertion | Sorted |
| **Performance** | O(1) | O(1) | O(log n) |
| **Null** | Yes | Yes | No |
| **Use** | Fast ops | Order matters | Sorted data |

### 6. Comparable vs Comparator?

**Answer:**

| Comparable | Comparator |
|------------|------------|
| Natural ordering | Custom ordering |
| Modify class | External class |
| One sorting order | Multiple orders |
| compareTo() | compare() |

### 7. What is ConcurrentModificationException?

**Answer:**
- Thrown when collection modified during iteration
- **Fail-fast** behavior
- **Solution:** Use Iterator.remove() or CopyOnWriteArrayList

### 8. Difference between HashMap and Hashtable?

**Answer:**

| HashMap | Hashtable |
|---------|-----------|
| Not synchronized | Synchronized |
| Allows null | No null |
| Faster | Slower |
| Java 1.2 | JDK 1.0 (legacy) |

### 9. What is load factor in HashMap?

**Answer:**
- **Default: 0.75**
- Threshold for resizing
- Formula: 	hreshold = capacity × loadFactor
- When size > threshold, HashMap doubles capacity
- Trade-off between space and time

### 10. How to make ArrayList thread-safe?

**Answer:**
```java
// Option 1: Collections.synchronizedList()
List<String> list = Collections.synchronizedList(new ArrayList<>());

// Option 2: CopyOnWriteArrayList
List<String> list = new CopyOnWriteArrayList<>();

// Option 3: External synchronization
synchronized(list) {
    // operations
}
```

---

## 🎯 Practice Problems

### Problem 1: Remove Duplicates from ArrayList
```java
import java.util.*;

public class RemoveDuplicates {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 2, 3, 3, 3, 4, 5, 5);
        
        // Solution 1: Using HashSet
        Set<Integer> set = new HashSet<>(list);
        List<Integer> uniqueList = new ArrayList<>(set);
        System.out.println(uniqueList);
        
        // Solution 2: Using LinkedHashSet (maintains order)
        Set<Integer> linkedSet = new LinkedHashSet<>(list);
        List<Integer> orderedList = new ArrayList<>(linkedSet);
        System.out.println(orderedList);
        
        // Solution 3: Using Stream (Java 8+)
        List<Integer> streamList = list.stream()
                                       .distinct()
                                       .collect(Collectors.toList());
        System.out.println(streamList);
    }
}
```

### Problem 2: Find First Non-Repeated Character
```java
import java.util.*;

public class FirstNonRepeated {
    public static char findFirst(String str) {
        LinkedHashMap<Character, Integer> map = new LinkedHashMap<>();
        
        // Count occurrences
        for (char c : str.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        
        // Find first with count 1
        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            if (entry.getValue() == 1) {
                return entry.getKey();
            }
        }
        
        return '\0';  // Not found
    }
    
    public static void main(String[] args) {
        System.out.println(findFirst("swiss"));  // w
        System.out.println(findFirst("aabbcc"));  // '\0'
    }
}
```

### Problem 3: Sort HashMap by Value
```java
import java.util.*;

public class SortMapByValue {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("John", 25);
        map.put("Alice", 30);
        map.put("Bob", 20);
        
        // Solution: Convert to list and sort
        List<Map.Entry<String, Integer>> list = 
            new ArrayList<>(map.entrySet());
        
        list.sort(Map.Entry.comparingByValue());
        
        // Create LinkedHashMap to maintain order
        Map<String, Integer> sortedMap = new LinkedHashMap<>();
        for (Map.Entry<String, Integer> entry : list) {
            sortedMap.put(entry.getKey(), entry.getValue());
        }
        
        System.out.println(sortedMap);
        // Output: {Bob=20, John=25, Alice=30}
    }
}
```

### Problem 4: Group Anagrams
```java
import java.util.*;

public class GroupAnagrams {
    public static List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        
        for (String str : strs) {
            // Sort characters to create key
            char[] chars = str.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);
            
            // Add to group
            map.putIfAbsent(key, new ArrayList<>());
            map.get(key).add(str);
        }
        
        return new ArrayList<>(map.values());
    }
    
    public static void main(String[] args) {
        String[] strs = {"eat", "tea", "tan", "ate", "nat", "bat"};
        System.out.println(groupAnagrams(strs));
        // Output: [[eat, tea, ate], [tan, nat], [bat]]
    }
}
```

### Problem 5: LRU Cache
```java
import java.util.*;

class LRUCache extends LinkedHashMap<Integer, Integer> {
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);  // access order
        this.capacity = capacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity;
    }
    
    public int get(int key) {
        return super.getOrDefault(key, -1);
    }
    
    public void put(int key, int value) {
        super.put(key, value);
    }
}

public class LRUCacheTest {
    public static void main(String[] args) {
        LRUCache cache = new LRUCache(2);
        
        cache.put(1, 1);
        cache.put(2, 2);
        System.out.println(cache.get(1));  // 1
        cache.put(3, 3);  // Evicts key 2
        System.out.println(cache.get(2));  // -1 (not found)
        cache.put(4, 4);  // Evicts key 1
        System.out.println(cache.get(1));  // -1 (not found)
        System.out.println(cache.get(3));  // 3
        System.out.println(cache.get(4));  // 4
    }
}
```

---

## 📚 Summary

**Key Takeaways:**

1. **List:** Ordered, allows duplicates, indexed access
   - **ArrayList:** Fast random access
   - **LinkedList:** Fast insert/delete at ends

2. **Set:** No duplicates, no indexed access
   - **HashSet:** Fast, no order
   - **LinkedHashSet:** Fast, maintains insertion order
   - **TreeSet:** Sorted, O(log n)

3. **Map:** Key-value pairs, unique keys
   - **HashMap:** Fast, no order (MOST IMPORTANT)
   - **LinkedHashMap:** Fast, maintains insertion order
   - **TreeMap:** Sorted keys, O(log n)

4. **Queue/Deque:**
   - **PriorityQueue:** Priority-based
   - **ArrayDeque:** Fast stack/queue

5. **Important Concepts:**
   - HashMap internals (hashing, collision, resizing)
   - hashCode() and equals() contract
   - Comparable vs Comparator
   - Thread-safe collections
   - Iteration techniques

6. **Performance:**
   - HashMap/HashSet: O(1) average
   - TreeMap/TreeSet: O(log n)
   - ArrayList access: O(1)
   - LinkedList access: O(n)

---

**🎓 Study Tips:**
1. **Practice HashMap internals** - Most asked in interviews
2. **Understand time complexities** - Critical for problem-solving
3. **Know when to use what** - Practical decision-making
4. **Practice coding problems** - LeetCode, HackerRank
5. **Master Comparable vs Comparator** - Common interview topic

**Good Luck! 🚀**

---

## 📄 One-Page Cheatsheet

### 🎯 Collections Framework - Quick Reference

#### **Collection Hierarchy**
```
Collection
├─ List (Ordered, Duplicates ✅, Index ✅)
│  ├─ ArrayList (Array, Fast Access)
│  ├─ LinkedList (Doubly-Linked, Fast Insert/Delete)
│  └─ Vector (Synchronized, Legacy)
├─ Set (No Duplicates, No Index)
│  ├─ HashSet (No Order, O(1))
│  ├─ LinkedHashSet (Insertion Order, O(1))
│  └─ TreeSet (Sorted, O(log n))
├─ Queue (FIFO)
│  ├─ PriorityQueue (Priority Heap)
│  └─ ArrayDeque (Fast Stack/Queue)
└─ Deque (Both Ends)

Map (NOT Collection)
├─ HashMap (No Order, O(1))
├─ LinkedHashMap (Insertion Order)
├─ TreeMap (Sorted Keys, O(log n))
└─ Hashtable (Synchronized, Legacy)
```

---

### 📊 Time Complexity Cheatsheet

| Operation | ArrayList | LinkedList | HashSet | TreeSet | HashMap | TreeMap |
|-----------|-----------|------------|---------|---------|---------|---------|
| **Add** | O(1)* | O(1) | O(1) | O(log n) | O(1) | O(log n) |
| **Get/Access** | O(1) | O(n) | - | - | O(1) | O(log n) |
| **Remove** | O(n) | O(1)** | O(1) | O(log n) | O(1) | O(log n) |
| **Contains** | O(n) | O(n) | O(1) | O(log n) | O(1) | O(log n) |
| **Size** | O(1) | O(1) | O(1) | O(1) | O(1) | O(1) |

*O(1) amortized (occasionally O(n) for resize)  
**O(1) at ends, O(n) at middle

---

### 🔑 Key Characteristics Quick Table

| Collection | Order | Duplicates | Null | Thread-Safe | Use When |
|------------|-------|------------|------|-------------|----------|
| **ArrayList** | ✅ Insertion | ✅ Yes | ✅ Multiple | ❌ No | Random access |
| **LinkedList** | ✅ Insertion | ✅ Yes | ✅ Multiple | ❌ No | Insert/delete at ends |
| **Vector** | ✅ Insertion | ✅ Yes | ✅ Multiple | ✅ Yes | Legacy code |
| **HashSet** | ❌ No | ❌ No | ✅ One | ❌ No | Fast, unique elements |
| **LinkedHashSet** | ✅ Insertion | ❌ No | ✅ One | ❌ No | Unique + order |
| **TreeSet** | ✅ Sorted | ❌ No | ❌ No | ❌ No | Sorted unique |
| **HashMap** | ❌ No | Keys: ❌ | ✅ One key | ❌ No | Fast key-value |
| **LinkedHashMap** | ✅ Insertion | Keys: ❌ | ✅ One key | ❌ No | Key-value + order |
| **TreeMap** | ✅ Sorted keys | Keys: ❌ | ❌ No | ❌ No | Sorted key-value |

---

### 🚀 HashMap Internals (MOST IMPORTANT!)

**Structure:** Array of Buckets (Node[])  
**Default Capacity:** 16  
**Load Factor:** 0.75 (resize at 75% full)  
**Growth:** Doubles (16→32→64...)

**Put Operation:**
1. Calculate `hash = key.hashCode()`
2. Find bucket: `index = (capacity-1) & hash`
3. Check collision → LinkedList or Tree
4. Resize if `size > threshold`

**Collision Handling:**
- **< 8 nodes:** LinkedList (O(n))
- **≥ 8 nodes:** Red-Black Tree (O(log n))

**Critical Contract:**
```java
If a.equals(b) → a.hashCode() == b.hashCode() (MUST)
If a.hashCode() == b.hashCode() → a.equals(b) (MAY)
```

**Always Override Both:**
```java
@Override
public int hashCode() {
    return Objects.hash(field1, field2);
}

@Override
public boolean equals(Object obj) {
    // Check class, cast, compare fields
}
```

---

### 🔄 ArrayList vs LinkedList vs HashSet

| Feature | ArrayList | LinkedList | HashSet |
|---------|-----------|------------|---------|
| **Best For** | Random access | Insert at ends | Unique elements |
| **Worst For** | Insert at start | Random access | Ordered data |
| **Memory** | Low | High (2 refs/node) | Medium |
| **Cache** | ✅ Good | ❌ Poor | Medium |

**Decision Tree:**
```
Need indexed access? 
├─ Yes → ArrayList
└─ No → Need unique?
    ├─ Yes → HashSet
    └─ No → Insert at ends? 
        ├─ Yes → LinkedList
        └─ No → ArrayList
```

---

### ⚖️ Comparable vs Comparator

| Comparable | Comparator |
|------------|------------|
| `compareTo(T o)` | `compare(T o1, T o2)` |
| In class itself | External class |
| **One** sort order | **Multiple** sort orders |
| Modify class | Don't modify class |
| Natural ordering | Custom ordering |
| `Collections.sort(list)` | `Collections.sort(list, comp)` |

**Example:**
```java
// Comparable
class Student implements Comparable<Student> {
    public int compareTo(Student s) { 
        return this.age - s.age; 
    }
}

// Comparator
Comparator<Student> byName = (s1, s2) -> s1.name.compareTo(s2.name);
Comparator<Student> byAge = Comparator.comparingInt(Student::getAge);
```

---

### 🔒 Thread-Safe Collections

| Original | Thread-Safe Alternative | Performance | Use Case |
|----------|------------------------|-------------|----------|
| ArrayList | `CopyOnWriteArrayList` | Read: Fast, Write: Slow | Read-heavy |
| HashMap | `ConcurrentHashMap` ⭐ | Excellent | General (BEST) |
| HashSet | `ConcurrentHashMap.newKeySet()` | Good | Unique elements |
| LinkedList | `ConcurrentLinkedQueue` | Good | Queue |

**Synchronized Wrappers:**
```java
List<String> list = Collections.synchronizedList(new ArrayList<>());
Set<String> set = Collections.synchronizedSet(new HashSet<>());
Map<String, Integer> map = Collections.synchronizedMap(new HashMap<>());

// MUST synchronize when iterating!
synchronized(list) {
    for(String s : list) { }
}
```

---

### 🎯 Collections Utility - Top Methods

```java
// Sorting
Collections.sort(list);
Collections.sort(list, Comparator.reverseOrder());
Collections.reverse(list);
Collections.shuffle(list);

// Searching
Collections.binarySearch(list, key);  // List must be sorted!

// Extremes
Collections.max(list);
Collections.min(list);
Collections.frequency(list, element);

// Modification
Collections.fill(list, value);
Collections.replaceAll(list, oldVal, newVal);

// Immutable
Collections.unmodifiableList(list);
Collections.emptyList();
Collections.singletonList("only");
```

---

### 🔥 Interview Must-Know Points

1. **HashMap Default Capacity:** 16
2. **HashMap Default Load Factor:** 0.75
3. **HashMap Treeify Threshold:** 8 nodes
4. **ArrayList Default Capacity:** 10
5. **ArrayList Growth:** 1.5x (50%)
6. **Vector Growth:** 2x (100%)
7. **TreeSet/TreeMap:** Red-Black Tree (self-balancing BST)
8. **Fail-Fast:** ArrayList, HashMap iterators
9. **Fail-Safe:** CopyOnWriteArrayList

---

### 📝 Quick Iteration Guide

| Method | Remove? | Index? | Best For |
|--------|---------|--------|----------|
| **for-each** | ❌ | ❌ | Simple iteration |
| **Iterator** | ✅ | ❌ | Safe removal |
| **for-index** | ✅ | ✅ | ArrayList |
| **forEach()** | ❌ | ❌ | Java 8+ |
| **Stream** | ❌ | ❌ | Functional ops |

---

### 🎲 Common Pitfalls

❌ **DON'T:**
- Modify list during for-each (use Iterator.remove())
- Use `==` for object comparison (use `.equals()`)
- Override `equals()` without `hashCode()`
- Use `null` in TreeSet/TreeMap
- Use raw types: `List list = new ArrayList();`

✅ **DO:**
- Use generics: `List<String> list = new ArrayList<>();`
- Override both `hashCode()` and `equals()` for HashMap keys
- Use `ConcurrentHashMap` for concurrent access
- Prefer ArrayList over LinkedList (default choice)
- Use TreeSet/TreeMap for sorted data

---

### 🏆 Top 5 Most Asked Interview Questions

1. **How does HashMap work internally?**
   - Array of buckets, hashing, collision (LinkedList→Tree), load factor, resizing

2. **ArrayList vs LinkedList?**
   - ArrayList: Fast access O(1), slow insert O(n)
   - LinkedList: Slow access O(n), fast insert O(1) at ends

3. **Why override hashCode() and equals()?**
   - HashMap uses hashCode() to find bucket, equals() to find key
   - Contract: equals() true → same hashCode()

4. **HashSet vs TreeSet?**
   - HashSet: O(1), no order, allows one null
   - TreeSet: O(log n), sorted, no null

5. **Comparable vs Comparator?**
   - Comparable: Natural order, in class, one order
   - Comparator: Custom order, external, multiple orders

---

### 🎯 Memory Tips

**LIST:** **L**ocated by **I**ndex, **S**orted by insertion, **T**akes duplicates  
**SET:** **S**ingle occurrence, **E**liminates duplicates, **T**errific for uniqueness  
**MAP:** **M**aps keys, **A**ssociates values, **P**airs them together  

**HashMap Formula:** `index = (capacity - 1) & hash`  
**Resize Formula:** `threshold = capacity × 0.75`  
**Treeify Rule:** "**Eight** nodes makes it **great** (converts to tree)"

---

### ⚡ Performance Rules of Thumb

- **Need speed?** → HashMap/HashSet (O(1))
- **Need order?** → LinkedHashMap/LinkedHashSet
- **Need sorting?** → TreeMap/TreeSet (O(log n))
- **Random access?** → ArrayList
- **Insert at ends?** → LinkedList or ArrayDeque
- **Thread-safe?** → ConcurrentHashMap

---

### 📚 Final Checklist Before Interview

- [ ] Can explain HashMap internal working
- [ ] Know time complexities of all operations
- [ ] Understand hashCode() and equals() contract
- [ ] Can differentiate ArrayList vs LinkedList
- [ ] Know when to use Set vs List vs Map
- [ ] Understand Comparable vs Comparator
- [ ] Know thread-safe alternatives
- [ ] Can write custom Comparator with lambdas
- [ ] Practiced LRU Cache implementation
- [ ] Solved group anagrams problem

---

**🚀 Print this page and keep it handy during interview prep!**

