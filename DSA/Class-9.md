# Week 5 – Class 9: Stack

---

# Session 9: Stack

## 1. What is a Stack?

A **Stack** is a linear data structure that follows the **LIFO** principle — **Last In, First Out**. The element inserted most recently is the first one to come out, just like a stack of plates in a cafeteria.

> **Real-World Analogy:** Think of a stack of plates. You always place a new plate on the **top**, and when you need one, you take it from the **top** as well. You can never take a plate from the middle without removing all the plates above it first.

| Operation | Description | Time Complexity |
|-----------|-------------|-----------------|
| `push(x)` | Add element `x` to the top of the stack | `O(1)` |
| `pop()` | Remove and return the top element | `O(1)` |
| `peek()` / `top()` | View the top element without removing it | `O(1)` |
| `isEmpty()` | Check whether the stack is empty | `O(1)` |
| `size()` | Return the number of elements in the stack | `O(1)` |

---

## 2. Stack in Memory — How It Looks

```
      push(10) → push(20) → push(30)

      ┌──────┐
      │  30  │  ← top (last in, first out)
      ├──────┤
      │  20  │
      ├──────┤
      │  10  │
      └──────┘
        Stack

      pop() → removes 30 → top is now 20
      peek() → returns 20 (does NOT remove)
```

---

## 3. Stack Implementation in Java

Java provides a built-in `Stack` class, but interviews often ask you to implement one yourself. Below are **two implementations**: using Java's built-in class, and a custom implementation using an array.

### 3a. Using Java's Built-in Stack

```java
import java.util.Stack;

public class BuiltInStackDemo {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>(); // create an empty integer stack

        // push — add elements to the top
        stack.push(10); // stack: [10]
        stack.push(20); // stack: [10, 20]
        stack.push(30); // stack: [10, 20, 30]

        // peek — view top without removing
        System.out.println("Top: " + stack.peek()); // 30

        // pop — remove and return top element
        System.out.println("Popped: " + stack.pop()); // 30
        System.out.println("Popped: " + stack.pop()); // 20

        // isEmpty — check if stack is empty
        System.out.println("Is empty? " + stack.isEmpty()); // false

        // size — number of elements remaining
        System.out.println("Size: " + stack.size()); // 1
    }
}
```

### 3b. Custom Stack Using an Array

```java
public class CustomStack {
    private int[] arr;     // underlying array to hold stack elements
    private int top;       // index pointing to the current top element
    private int capacity;  // maximum capacity of the stack

    // Constructor: create a stack with given capacity
    public CustomStack(int capacity) {
        this.capacity = capacity;
        this.arr = new int[capacity]; // allocate array of fixed size
        this.top = -1;               // -1 means stack is empty (no element at index 0 yet)
    }

    // push: add element to top of stack
    public void push(int value) {
        if (top == capacity - 1) {            // top is at last index → stack is full
            System.out.println("Stack Overflow! Cannot push " + value);
            return;
        }
        arr[++top] = value; // increment top first, then place value at that index
        System.out.println("Pushed: " + value);
    }

    // pop: remove and return the top element
    public int pop() {
        if (isEmpty()) {              // check for underflow
            System.out.println("Stack Underflow! Stack is empty.");
            return -1;
        }
        return arr[top--]; // return value at top, then decrement top
    }

    // peek: view top element without removing it
    public int peek() {
        if (isEmpty()) {
            System.out.println("Stack is empty!");
            return -1;
        }
        return arr[top]; // just read the value, do NOT change top
    }

    // isEmpty: returns true if no elements in the stack
    public boolean isEmpty() {
        return top == -1; // top = -1 is our sentinel for "empty"
    }

    // size: returns the number of elements currently in the stack
    public int size() {
        return top + 1; // if top = 2, there are 3 elements (indices 0, 1, 2)
    }

    public static void main(String[] args) {
        CustomStack stack = new CustomStack(5);

        stack.push(10); // Pushed: 10
        stack.push(20); // Pushed: 20
        stack.push(30); // Pushed: 30

        System.out.println("Peek: " + stack.peek()); // 30
        System.out.println("Pop: " + stack.pop());   // 30
        System.out.println("Pop: " + stack.pop());   // 20
        System.out.println("Size: " + stack.size()); // 1
        System.out.println("Is Empty? " + stack.isEmpty()); // false
    }
}
```

---

## 4. Where Are Stacks Used?

Stacks appear everywhere in computer science. Knowing their applications helps you recognize **when** to use a stack in interview problems.

| Application | How Stack Is Used |
|-------------|-------------------|
| **Function Call Stack** | Every method call is pushed; when it returns, it's popped |
| **Undo / Redo** | Each action is pushed; undo pops the last action |
| **Browser Back Button** | Each visited URL is pushed; back button pops it |
| **Balanced Parentheses** | Push open brackets; pop and match on close brackets |
| **Expression Evaluation** | Infix → Postfix conversion and evaluation use stacks |
| **Next Greater Element** | Maintain a stack of candidates while scanning |

---

## 5. Key Stack Problem Pattern — Monotonic Stack

A **Monotonic Stack** is a stack that maintains elements in a **sorted order** (either strictly increasing or strictly decreasing from bottom to top). It is the core technique behind many stack interview problems, especially **Next Greater Element**.

### How It Works (Monotonic Decreasing Stack)

- Process elements one by one.
- Before pushing an element, **pop all elements from the stack that are smaller** than the current element.
- Each popped element's "Next Greater Element" is the current element being processed.
- If the stack is empty when we're done, the remaining elements have no greater element to the right.

```
Array:  [4, 5, 2, 10, 8]

Step 1: Push 4   → Stack: [4]
Step 2: 5 > 4    → Pop 4 (NGE of 4 is 5), Push 5   → Stack: [5]
Step 3: 2 < 5    → Just push 2                       → Stack: [5, 2]
Step 4: 10 > 2   → Pop 2 (NGE of 2 is 10)
        10 > 5   → Pop 5 (NGE of 5 is 10), Push 10  → Stack: [10]
Step 5: 8 < 10   → Just push 8                       → Stack: [10, 8]
End: stack has [10, 8] → no NGE → NGE = -1

Result: NGE of 4=5, 5=10, 2=10, 10=-1, 8=-1
```

---

## 6. Key Stack Complexity Cheat Sheet

| Operation | Array-based Stack | LinkedList-based Stack |
|-----------|-------------------|------------------------|
| Push | `O(1)` | `O(1)` |
| Pop | `O(1)` | `O(1)` |
| Peek | `O(1)` | `O(1)` |
| Search | `O(N)` | `O(N)` |
| Space | `O(N)` | `O(N)` |

> **Key Insight:** All fundamental stack operations run in **O(1)** time. The power of a stack lies not in the operations themselves, but in how you use it strategically to reduce complexity of otherwise O(N²) problems to O(N).

---

# Practice Problems (Session 9)

---

## 1. Valid Parentheses

[Valid Parentheses (LeetCode 20)](https://leetcode.com/problems/valid-parentheses/)

### Problem Statement

Given a string `s` containing only the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is **valid**. A string is valid if:
- Every **open bracket** has a corresponding **close bracket** of the **same type**.
- Open brackets must be closed **in the correct order** — the most recently opened bracket must be closed first (LIFO!).

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| `"()"` | `true` | One matching pair |
| `"()[]{}"` | `true` | Three matching pairs, all correct order |
| `"(]"` | `false` | Open `(` but closed with `]` — wrong type |
| `"([)]"` | `false` | Order is wrong: `[` is opened inside `(` but closed before `(` |
| `"{[]}"` | `true` | Nested correctly: `[]` inside `{}` |

---

### Approach 1: Brute Force (Repeated Replacement)

Repeatedly scan the string and remove all adjacent valid pairs like `()`, `[]`, `{}` until no more replacements are possible. If the resulting string is empty, it was valid.

#### The Math/Logic Behind It

If we keep removing innermost pairs, eventually a valid string will become empty. An invalid string will get "stuck" with leftover brackets that can't be matched.

| Iteration | String | Pairs Removed |
|-----------|--------|---------------|
| 0 | `{[()]}` | — |
| 1 | `{[]}` | `()` removed |
| 2 | `{}` | `[]` removed |
| 3 | `` (empty) | `{}` removed → **valid!** |

```java
public class ValidParenthesesBrute {

    public static boolean isValid(String s) {
        // Keep replacing valid inner pairs until no more replacements happen
        while (s.contains("()") || s.contains("[]") || s.contains("{}")) {
            // Each replace call removes ONE type of inner pair
            s = s.replace("()", ""); // remove all "()" pairs
            s = s.replace("[]", ""); // remove all "[]" pairs
            s = s.replace("{}", ""); // remove all "{}" pairs
        }
        // If everything matched, the string should be empty
        return s.isEmpty();
    }

    public static void main(String[] args) {
        System.out.println(isValid("()"));      // expected: true
        System.out.println(isValid("()[]{}")); // expected: true
        System.out.println(isValid("(]"));     // expected: false
        System.out.println(isValid("([)]"));   // expected: false
        System.out.println(isValid("{[]}"));   // expected: true
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N²)` — each `replace` call scans the entire string (O(N)), and in the worst case we do O(N) replacement rounds (e.g., `((((…))))` needs N/2 rounds), giving O(N²) total.
* **Space Complexity:** `O(N)` — Java's `String.replace()` creates new String objects each time; in the worst case, we create O(N) intermediate strings.

---

### Approach 2: Optimal (Stack-Based Matching)

Use a **stack** to track open brackets as we scan left to right. When we see a closing bracket, check if it matches the most recently opened one (top of stack). If at any point it doesn't match, return `false`. At the end, the stack should be empty.

#### The Math/Logic Behind It

The "most recently opened bracket must be closed first" is exactly the LIFO property of a stack. The stack **remembers opening brackets in the order they were seen**.

**Dry Run for `"([{}])"`:**

| Index | Char | Action | Stack State |
|-------|------|--------|-------------|
| 0 | `(` | Open bracket → push | `[(]` |
| 1 | `[` | Open bracket → push | `[(, []` |
| 2 | `{` | Open bracket → push | `[(, [, {]` |
| 3 | `}` | Close → top is `{` → match! pop | `[(, []` |
| 4 | `]` | Close → top is `[` → match! pop | `[(]` |
| 5 | `)` | Close → top is `(` → match! pop | `[]` (empty) |
| End | — | Stack is empty → **valid!** | ✓ |

```java
import java.util.Stack;

public class ValidParenthesesOptimal {

    public static boolean isValid(String s) {
        Stack<Character> stack = new Stack<>(); // stack stores unmatched open brackets

        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i); // get current character

            // If current character is an opening bracket, push it onto the stack
            if (ch == '(' || ch == '[' || ch == '{') {
                stack.push(ch);
            }
            // If current character is a closing bracket
            else {
                // If stack is empty, no matching open bracket exists → invalid
                if (stack.isEmpty()) {
                    return false;
                }

                char top = stack.pop(); // remove the most recent open bracket

                // Check if the popped open bracket matches the current close bracket
                if (ch == ')' && top != '(') return false; // mismatch: expected '(' but got something else
                if (ch == ']' && top != '[') return false; // mismatch: expected '['
                if (ch == '}' && top != '{') return false; // mismatch: expected '{'
            }
        }

        // At the end, stack must be empty (all open brackets were matched and closed)
        return stack.isEmpty();
    }

    public static void main(String[] args) {
        System.out.println(isValid("()"));      // expected: true
        System.out.println(isValid("()[]{}")); // expected: true
        System.out.println(isValid("(]"));     // expected: false
        System.out.println(isValid("([)]"));   // expected: false
        System.out.println(isValid("{[]}"));   // expected: true
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we scan the string exactly once, and each character causes at most one push or one pop, both of which are O(1) operations.
* **Space Complexity:** `O(N)` — in the worst case (e.g., all open brackets like `(((((`), every character is pushed onto the stack, using O(N) space.

### Teaching Tips
1. **Physical demo with colored cards:** Give students colored cards labeled `(`, `[`, `{`. Have one student be the "stack" and physically hold cards. As the class reads a string aloud, the student pushes opening cards and holds them up to match closing ones. Students immediately see the order matters.
2. **Common mistake:** Students often forget to check `stack.isEmpty()` before calling `stack.pop()` on a closing bracket. This causes a `EmptyStackException`. Emphasize: "Always guard a `pop()` with an `isEmpty()` check when the stack might be empty."
3. **Bridge to real life:** Tell students this is exactly how your IDE (e.g., IntelliJ, VS Code) highlights mismatched brackets in real-time. It runs essentially this same algorithm on every keystroke — making this a very relatable and practical example.

---

## 2. Next Greater Element I

[Next Greater Element I (LeetCode 496)](https://leetcode.com/problems/next-greater-element-i/)

### Problem Statement

You are given two arrays `nums1` and `nums2`, where `nums1` is a **subset** of `nums2`. For each element in `nums1`, find its **Next Greater Element** in `nums2`. The Next Greater Element of a number `x` in `nums2` is the **first greater number to its right** in `nums2`. If no such number exists, return `-1` for that element.

**Examples:**

| nums1 | nums2 | Output | Reason |
|-------|-------|--------|--------|
| `[4,1,2]` | `[1,3,4,2]` | `[-1,3,-1]` | 4→no greater; 1→3 (first right); 2→no greater |
| `[2,4]` | `[1,2,3,4]` | `[3,-1]` | 2→3 (next right); 4→no greater element |
| `[1]` | `[1]` | `[-1]` | Only element, nothing to its right |

---

### Approach 1: Brute Force (Nested Loops)

For each element in `nums1`, find its position in `nums2`, then scan rightward in `nums2` to find the first greater element. This uses two nested loops.

#### The Math/Logic Behind It

For each query element, we do a linear scan of `nums2` to locate it, then another linear scan to find its NGE. In the worst case, each query takes O(M) time where M = length of `nums2`.

**Dry Run for nums1 = `[4,1,2]`, nums2 = `[1,3,4,2]`:**

| Query (nums1[i]) | Find in nums2 at index | Scan right for NGE | Result |
|------------------|------------------------|--------------------|--------|
| 4 | index 2 | nothing to right > 4 | -1 |
| 1 | index 0 | 3 > 1 → found at index 1 | 3 |
| 2 | index 3 | nothing to right | -1 |

```java
public class NextGreaterElementBrute {

    public static int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int[] result = new int[nums1.length]; // output array, same size as nums1

        for (int i = 0; i < nums1.length; i++) {
            int target = nums1[i]; // the element we're looking for in nums2
            boolean found = false; // tracks whether we've found target in nums2
            result[i] = -1;        // default: no greater element found

            for (int j = 0; j < nums2.length; j++) {
                if (!found && nums2[j] == target) {
                    found = true; // we found target at index j in nums2
                }
                // Once found, look for the first element greater than target to its right
                else if (found && nums2[j] > target) {
                    result[i] = nums2[j]; // this is the NGE
                    break; // stop looking further — we want the FIRST greater element
                }
            }
        }

        return result;
    }

    public static void main(String[] args) {
        int[] nums1a = {4, 1, 2};
        int[] nums2a = {1, 3, 4, 2};
        int[] res1 = nextGreaterElement(nums1a, nums2a);
        System.out.print("Result 1: ");
        for (int x : res1) System.out.print(x + " "); // expected: -1 3 -1
        System.out.println();

        int[] nums1b = {2, 4};
        int[] nums2b = {1, 2, 3, 4};
        int[] res2 = nextGreaterElement(nums1b, nums2b);
        System.out.print("Result 2: ");
        for (int x : res2) System.out.print(x + " "); // expected: 3 -1
        System.out.println();

        int[] nums1c = {1};
        int[] nums2c = {1};
        int[] res3 = nextGreaterElement(nums1c, nums2c);
        System.out.print("Result 3: ");
        for (int x : res3) System.out.print(x + " "); // expected: -1
        System.out.println();
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N × M)` — for each of the N elements in `nums1`, we scan nums2 (length M) twice in the worst case, giving O(N·M).
* **Space Complexity:** `O(1)` — apart from the output array, no extra data structures are used.

---

### Approach 2: Optimal (Monotonic Stack + HashMap)

Precompute the **Next Greater Element for every element in `nums2`** using a monotonic decreasing stack. Store the results in a `HashMap`. Then for each element in `nums1`, simply look up the answer in O(1).

#### The Math/Logic Behind It

We process `nums2` from **left to right**. We maintain a stack of elements for which we haven't yet found a NGE. When we see a new element that is **greater than the stack's top**, that element is the NGE for the top — we pop and record it. Elements left in the stack at the end have no NGE (→ -1).

**Dry Run for nums2 = `[1, 3, 4, 2]`:**

| Step | Current | Stack Before | Action | NGE Map |
|------|---------|--------------|--------|---------|
| 1 | 1 | `[]` | Push 1 | `{}` |
| 2 | 3 | `[1]` | 3>1 → pop 1, record NGE[1]=3; push 3 | `{1→3}` |
| 3 | 4 | `[3]` | 4>3 → pop 3, record NGE[3]=4; push 4 | `{1→3, 3→4}` |
| 4 | 2 | `[4]` | 2<4 → just push 2 | `{1→3, 3→4}` |
| End | — | `[4, 2]` | No NGE for 4 and 2 → -1 | `{1→3, 3→4, 4→-1, 2→-1}` |

```java
import java.util.HashMap;
import java.util.Stack;

public class NextGreaterElementOptimal {

    public static int[] nextGreaterElement(int[] nums1, int[] nums2) {
        // HashMap stores: element → its Next Greater Element in nums2
        HashMap<Integer, Integer> ngeMap = new HashMap<>();
        Stack<Integer> stack = new Stack<>(); // monotonic decreasing stack (stores elements)

        // Step 1: Compute NGE for every element in nums2 using a monotonic stack
        for (int i = 0; i < nums2.length; i++) {
            int current = nums2[i];

            // Pop elements from stack that are smaller than current → current is their NGE
            while (!stack.isEmpty() && stack.peek() < current) {
                int popped = stack.pop();         // this element's NGE is current
                ngeMap.put(popped, current);      // record the NGE in the map
            }

            stack.push(current); // push current element (we haven't found its NGE yet)
        }

        // All elements remaining in the stack have no NGE → map them to -1
        while (!stack.isEmpty()) {
            ngeMap.put(stack.pop(), -1); // no greater element exists to their right
        }

        // Step 2: Build the result for nums1 using the precomputed map
        int[] result = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            result[i] = ngeMap.get(nums1[i]); // O(1) lookup
        }

        return result;
    }

    public static void main(String[] args) {
        int[] nums1a = {4, 1, 2};
        int[] nums2a = {1, 3, 4, 2};
        int[] res1 = nextGreaterElement(nums1a, nums2a);
        System.out.print("Result 1: ");
        for (int x : res1) System.out.print(x + " "); // expected: -1 3 -1
        System.out.println();

        int[] nums1b = {2, 4};
        int[] nums2b = {1, 2, 3, 4};
        int[] res2 = nextGreaterElement(nums1b, nums2b);
        System.out.print("Result 2: ");
        for (int x : res2) System.out.print(x + " "); // expected: 3 -1
        System.out.println();

        int[] nums1c = {1};
        int[] nums2c = {1};
        int[] res3 = nextGreaterElement(nums1c, nums2c);
        System.out.print("Result 3: ");
        for (int x : res3) System.out.print(x + " "); // expected: -1
        System.out.println();
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N + M)` — we process `nums2` once with the stack (O(M)), and then look up each element of `nums1` once in the HashMap (O(N)); total is O(N+M).
* **Space Complexity:** `O(M)` — the HashMap stores at most M entries (one per element in `nums2`), and the stack holds at most M elements at a time.

### Teaching Tips
1. **Monotonic Stack visualization:** Draw an array on the board and process it left to right. Place a sticky note for each element pushed onto the stack. When you pop a note (because you found a greater element), draw an arrow from the popped element to the current element — that arrow IS the NGE relationship. Students can see all NGE relationships emerge naturally.
2. **Common mistake:** Students often try to process the array from right to left and keep a different kind of stack. Emphasize: when processing left to right, popping happens when you find a larger element (monotonic decreasing stack). When processing right to left, you push and peek differently. Both approaches work, but left-to-right is more intuitive for this problem.
3. **Pattern generalization:** Once students understand this, point out that "Next Smaller Element", "Previous Greater Element", and similar problems all use the same monotonic stack pattern — just with different comparison directions. Mastering this one problem gives them the key to a whole family of stack problems.

---

## 3. Min Stack

[Min Stack (LeetCode 155)](https://leetcode.com/problems/min-stack/)

### Problem Statement

Design a stack that supports **push**, **pop**, **top**, and **retrieving the minimum element** — all in **O(1)** time. Implement the `MinStack` class with the following methods:
- `push(val)` — Push element `val` onto the stack.
- `pop()` — Remove the top element.
- `top()` — Get the top element.
- `getMin()` — Retrieve the **minimum element** in the stack.

**Examples:**

| Operations | Stack State | getMin() | Explanation |
|------------|-------------|----------|-------------|
| push(-2), push(0), push(-3) | [-2, 0, -3] | -3 | -3 is the current minimum |
| pop() | [-2, 0] | -2 | -3 removed; min is now -2 |
| top() | [-2, 0] | — | Returns 0 (current top) |
| getMin() | [-2, 0] | -2 | -2 is the new minimum |

---

### Approach 1: Brute Force (Scan on Every getMin Call)

Use a regular stack. When `getMin()` is called, traverse the entire stack to find the minimum. This is simple but slow for frequent `getMin()` calls.

```java
import java.util.Stack;

public class MinStackBrute {
    private Stack<Integer> stack;

    public MinStackBrute() {
        stack = new Stack<>(); // standard stack with no special tracking
    }

    // push: simply push the value
    public void push(int val) {
        stack.push(val);
    }

    // pop: simply pop the top
    public void pop() {
        stack.pop();
    }

    // top: return the top element
    public int top() {
        return stack.peek();
    }

    // getMin: scan the entire stack to find the minimum — O(N) each call
    public int getMin() {
        int min = Integer.MAX_VALUE; // start with the largest possible value
        for (int val : stack) {
            min = Math.min(min, val); // update min if we find something smaller
        }
        return min;
    }

    public static void main(String[] args) {
        MinStackBrute ms = new MinStackBrute();
        ms.push(-2);
        ms.push(0);
        ms.push(-3);
        System.out.println("Min: " + ms.getMin()); // expected: -3
        ms.pop();
        System.out.println("Top: " + ms.top());    // expected: 0
        System.out.println("Min: " + ms.getMin()); // expected: -2
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` per `getMin()` call — we must scan all N elements of the stack each time to find the minimum, since we have no tracking structure.
* **Space Complexity:** `O(N)` — the stack stores N elements.

---

### Approach 2: Optimal (Auxiliary Min Stack)

Maintain **two stacks**: the main stack for elements, and a **min-stack** that always holds the current minimum at each state. Every time we push, we also push the current minimum to the min-stack. Every time we pop, we pop from both. The top of the min-stack is always the current minimum in O(1).

#### The Math/Logic Behind It

At any point in time, the minimum of the stack is the minimum of the main stack's contents. The key insight: the minimum can only **change** when we `push` a smaller value or `pop` the current minimum. By mirroring every push/pop on the min-stack, we always have the minimum available at the top.

**Dry Run for push(-2), push(0), push(-3), pop():**

| Operation | Main Stack | Min Stack | getMin() |
|-----------|------------|-----------|----------|
| push(-2) | [-2] | [-2] | -2 |
| push(0) | [-2, 0] | [-2, -2] | -2 (0 is not smaller) |
| push(-3) | [-2, 0, -3] | [-2, -2, -3] | -3 |
| pop() | [-2, 0] | [-2, -2] | -2 (both stacks synced) |

```java
import java.util.Stack;

public class MinStackOptimal {
    private Stack<Integer> mainStack; // stores all elements normally
    private Stack<Integer> minStack;  // stores the running minimum at each state

    public MinStackOptimal() {
        mainStack = new Stack<>();
        minStack  = new Stack<>();
    }

    // push: push value to main stack; push running minimum to min stack
    public void push(int val) {
        mainStack.push(val); // always push to main stack

        // Push the new minimum to minStack
        // If minStack is empty, val IS the minimum
        // Otherwise, push whichever is smaller: val or current minimum
        if (minStack.isEmpty()) {
            minStack.push(val);
        } else {
            minStack.push(Math.min(val, minStack.peek())); // track the running minimum
        }
    }

    // pop: pop from BOTH stacks simultaneously to keep them in sync
    public void pop() {
        mainStack.pop(); // remove top of main stack
        minStack.pop();  // also remove the corresponding minimum entry
    }

    // top: return the top element of the main stack
    public int top() {
        return mainStack.peek(); // does not remove; just views
    }

    // getMin: the top of minStack is ALWAYS the current minimum — O(1)!
    public int getMin() {
        return minStack.peek(); // no need to scan; top of minStack is always the answer
    }

    public static void main(String[] args) {
        MinStackOptimal ms = new MinStackOptimal();
        ms.push(-2);
        ms.push(0);
        ms.push(-3);
        System.out.println("Min: " + ms.getMin()); // expected: -3
        ms.pop();
        System.out.println("Top: " + ms.top());    // expected: 0
        System.out.println("Min: " + ms.getMin()); // expected: -2

        // Additional test
        MinStackOptimal ms2 = new MinStackOptimal();
        ms2.push(5);
        ms2.push(3);
        ms2.push(7);
        ms2.push(1);
        System.out.println("Min: " + ms2.getMin()); // expected: 1
        ms2.pop(); // remove 1
        System.out.println("Min: " + ms2.getMin()); // expected: 3
        ms2.pop(); // remove 7
        System.out.println("Min: " + ms2.getMin()); // expected: 3
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(1)` for all operations — push, pop, top, and getMin each perform a constant amount of work, regardless of the stack size.
* **Space Complexity:** `O(N)` — we use two stacks, each holding at most N elements; total space is 2N which is still O(N).

### Teaching Tips
1. **Shadow stack analogy:** Tell students to imagine having a personal assistant who always keeps track of the "cheapest item" in their shopping cart. Every time they add something, the assistant notes "the cheapest so far." When something is removed, the assistant checks their notes for the previous cheapest. The minStack IS that assistant's notebook.
2. **Common mistake:** Students often think they should only push to the minStack when the new value is smaller than the current minimum. But this breaks the sync between the two stacks — when you pop, you pop from both simultaneously, so they must always be the same size. Always push to minStack on every push.
3. **Board visualization:** Draw two columns side by side (main stack | min stack). Do push and pop operations live on the board, updating both columns together. Students can visually verify that the top of the min column always equals the minimum of the main column.

---

## 4. Largest Rectangle in Histogram

[Largest Rectangle in Histogram (LeetCode 84)](https://leetcode.com/problems/largest-rectangle-in-histogram/)

### Problem Statement

Given an array `heights` representing the heights of bars in a histogram (each bar has **width 1**), find the **area of the largest rectangle** that can be formed within the histogram.

**Examples:**

| Input `heights` | Output (Max Area) | Reason |
|-----------------|-------------------|--------|
| `[2,1,5,6,2,3]` | `10` | Rectangle of height 5 spanning bars at index 2 and 3 (width=2, area=5×2=10) |
| `[2,4]` | `4` | Single bar of height 4 gives area 4 |
| `[1,1,1,1]` | `4` | Entire histogram is width 4, height 1 → area = 4 |

---

### Approach 1: Brute Force (Check All Pairs)

For every pair of indices `(i, j)`, the rectangle spanning from bar `i` to bar `j` has height = `min(heights[i..j])` and width = `j - i + 1`. Find the maximum such area.

#### The Math/Logic Behind It

A rectangle spanning bars i to j can only be as tall as the **shortest bar** in that range (since the rectangle must fit within all bars). We check all O(N²) pairs.

| Left | Right | Min Height | Width | Area |
|------|-------|------------|-------|------|
| 0 | 0 | 2 | 1 | 2 |
| 0 | 1 | 1 | 2 | 2 |
| 2 | 3 | 5 | 2 | **10** |
| 2 | 4 | 2 | 3 | 6 |

```java
public class LargestRectangleBrute {

    public static int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int maxArea = 0; // track the maximum area found so far

        // Try every possible pair (i, j) as left and right boundary
        for (int i = 0; i < n; i++) {
            int minHeight = heights[i]; // the rectangle can be at most as tall as heights[i]

            for (int j = i; j < n; j++) {
                // Update minimum height as we expand the rectangle to the right
                minHeight = Math.min(minHeight, heights[j]); // limiting bar in the range [i..j]

                int width = j - i + 1;           // width spans from bar i to bar j inclusive
                int area  = minHeight * width;   // area = height × width

                maxArea = Math.max(maxArea, area); // update max if this area is larger
            }
        }

        return maxArea;
    }

    public static void main(String[] args) {
        System.out.println(largestRectangleArea(new int[]{2, 1, 5, 6, 2, 3})); // expected: 10
        System.out.println(largestRectangleArea(new int[]{2, 4}));             // expected: 4
        System.out.println(largestRectangleArea(new int[]{1, 1, 1, 1}));       // expected: 4
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N²)` — we use two nested loops; for each starting bar `i`, we scan all bars to its right, giving N + (N-1) + … + 1 = N(N+1)/2 iterations.
* **Space Complexity:** `O(1)` — only a few integer variables used; no extra data structures.

---

### Approach 2: Optimal (Monotonic Stack)

For each bar, the largest rectangle it can be part of extends left and right until a **shorter bar** is encountered. We use a monotonic increasing stack to efficiently find, for each bar, the index of the **nearest shorter bar** on its left and right. Then area = height × (rightBound - leftBound - 1).

#### The Math/Logic Behind It

The key insight: when we pop a bar from the stack (because the current bar is shorter), the **current bar is the right boundary** and the **new stack top is the left boundary** for the popped bar's rectangle.

**Dry Run for `[2, 1, 5, 6, 2, 3]`:**

| Index | height | Stack (indices) | Action | Area Computed |
|-------|--------|-----------------|--------|---------------|
| 0 | 2 | [] | push 0 | — |
| 1 | 1 | [0] | 1<2: pop 0; area=2×1=2; push 1 | 2 |
| 2 | 5 | [1] | push 2 | — |
| 3 | 6 | [1,2] | push 3 | — |
| 4 | 2 | [1,2,3] | 2<6: pop 3; area=6×1=6; 2<5: pop 2; area=5×2=10; push 4 | 6, **10** |
| 5 | 3 | [1,4] | push 5 | — |
| End | — | [1,4,5] | pop 5: area=3×1=3; pop 4: area=2×4=8; pop 1: area=1×6=6 | 3, 8, 6 |
| **Max** | | | | **10** |

```java
import java.util.Stack;

public class LargestRectangleOptimal {

    public static int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int maxArea = 0;
        // Stack stores INDICES of bars in monotonically increasing order of height
        Stack<Integer> stack = new Stack<>();

        for (int i = 0; i <= n; i++) {
            // Use height 0 as a sentinel at the end to flush all remaining bars from stack
            int currentHeight = (i == n) ? 0 : heights[i];

            // Pop bars from the stack that are taller than the current bar
            // The current bar is the RIGHT boundary for those taller bars
            while (!stack.isEmpty() && heights[stack.peek()] > currentHeight) {
                int height = heights[stack.pop()]; // height of the rectangle using the popped bar

                // Width: from the new stack top (left boundary) to current index (right boundary)
                // If stack is empty, left boundary extends to the beginning (index -1)
                int width = stack.isEmpty() ? i : (i - stack.peek() - 1);

                int area = height * width;           // area of rectangle with this bar as the smallest
                maxArea = Math.max(maxArea, area);   // update the maximum
            }

            stack.push(i); // push current bar's index (we haven't found its right boundary yet)
        }

        return maxArea;
    }

    public static void main(String[] args) {
        System.out.println(largestRectangleArea(new int[]{2, 1, 5, 6, 2, 3})); // expected: 10
        System.out.println(largestRectangleArea(new int[]{2, 4}));             // expected: 4
        System.out.println(largestRectangleArea(new int[]{1, 1, 1, 1}));       // expected: 4
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each bar is pushed onto the stack exactly once and popped at most once; across all iterations, the total push + pop operations is 2N → O(N).
* **Space Complexity:** `O(N)` — the stack holds at most N indices in the worst case (e.g., a strictly increasing histogram where nothing is popped until the very end).

### Teaching Tips
1. **Histogram visualization:** Draw the histogram on the board with colored markers. As you walk through the algorithm, stack indices as numbers in a separate "stack column." When you pop, physically draw the rectangle on the histogram and write the area. Students can see the rectangles form progressively, making the logic tangible.
2. **Common mistake:** Students often store heights in the stack instead of indices. Emphasize that we MUST store **indices** because we need both the height AND the width calculation (`i - stack.peek() - 1`). If we only store heights, we can't compute the width.
3. **Sentinel trick:** Explain why we append a virtual bar of height 0 at the end (`i == n` case). Without it, bars that remain in the stack at the end (which form the rightmost valid rectangles) would never be processed. The sentinel forces one final pop of all remaining bars.

---

# Class 9 Homework

### Easy (Mandatory)
1. [Baseball Game — LeetCode 682](https://leetcode.com/problems/baseball-game/) — Simulate a baseball score record using a stack; handle `+`, `D`, `C` operations on the score stack.
2. [Reverse String Using Stack — GFG](https://www.geeksforgeeks.org/stack-set-3-reverse-string-using-stack/) — Use a stack to reverse a given string by pushing all characters and popping them out.
3. [Evaluate Reverse Polish Notation — LeetCode 150](https://leetcode.com/problems/evaluate-reverse-polish-notation/) — Evaluate a postfix expression using a stack; push numbers, pop two operands for each operator.

### Medium (Recommended)
1. [Daily Temperatures — LeetCode 739](https://leetcode.com/problems/daily-temperatures/) — For each day, find how many days you must wait for a warmer temperature. Classic monotonic stack problem.
2. [Decode String — LeetCode 394](https://leetcode.com/problems/decode-string/) — Decode a nested encoded string like `3[a2[b]]` using two stacks: one for counts and one for string builders.
3. [Car Fleet — LeetCode 853](https://leetcode.com/problems/car-fleet/) — Use a stack to simulate cars merging into fleets based on speed and position — a creative monotonic stack application.

### Challenge Problem (Optional)
[Trapping Rain Water — LeetCode 42](https://leetcode.com/problems/trapping-rain-water/) — Compute how much rainwater can be trapped between histogram bars. Can be solved with a monotonic stack in O(N) time and O(N) space — a step beyond the histogram problem that tests deep understanding of the same pattern.
