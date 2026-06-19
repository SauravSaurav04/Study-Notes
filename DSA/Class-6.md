# [Week 3] – Class 6: Recursion

---

# Theory: Recursion in Java

## 1. What is Recursion?

**Recursion** is a programming technique where a function **calls itself** to solve a smaller version of the same problem. Every recursive solution breaks a problem into two parts:

1. **Base Case** — The simplest version of the problem that can be solved directly (no further recursive call needed). This is the **exit condition** that stops the recursion.
2. **Recursive Case** — The general case where the function calls itself with a smaller or simpler input, moving toward the base case.

> **Real-World Analogy:** Imagine you're standing in a line of people and you want to know what position you are at. You ask the person in front of you, "What's your position?" They ask the person in front of them, and so on — until the person at the very front says "I'm number 1." Then each person adds 1 and passes the answer back. The person at the front is the **base case**; everyone else is a **recursive case**.

### The Structure of Every Recursive Function

```java
public class RecursionTemplate {
    public static returnType solve(parameters) {
        // BASE CASE: The simplest version of the problem — stop here
        if (base condition) {
            return base value;
        }

        // RECURSIVE CASE: Break the problem into a smaller subproblem
        // Do some work, then call the function with a smaller input
        return solve(smaller input);
    }
}
```

---

## 2. How Recursion Works — The Call Stack

Every time a function is called in Java, a **stack frame** is pushed onto the **call stack**. This frame stores:
- The function's local variables
- The return address (where to go after this call returns)

When a recursive function calls itself, a new frame is pushed on top. When the base case is reached, frames start **popping** off the stack one by one, returning values back up the chain.

> **Real-World Analogy:** Think of a stack of plates. Each recursive call adds a plate on top. The base case is when you stop stacking. Then you remove plates one by one (the return phase), reading what's written on each one.

### Visualizing the Call Stack for `factorial(4)`

```
factorial(4)
  └── 4 * factorial(3)
            └── 3 * factorial(2)
                      └── 2 * factorial(1)
                                └── BASE CASE: return 1

← Returns back up:
  factorial(1) = 1
  factorial(2) = 2 * 1 = 2
  factorial(3) = 3 * 2 = 6
  factorial(4) = 4 * 6 = 24
```

```java
public class CallStackDemo {
    public static int factorial(int n) {
        // BASE CASE: factorial of 0 or 1 is 1
        if (n <= 1) return 1;

        // RECURSIVE CASE: n! = n × (n-1)!
        System.out.println("Calling factorial(" + (n - 1) + ") from factorial(" + n + ")");
        int result = n * factorial(n - 1);
        System.out.println("factorial(" + n + ") = " + result);
        return result;
    }

    public static void main(String[] args) {
        System.out.println("Final Answer: " + factorial(4)); // 24
    }
}
```

---

## 3. Recursion Tree — Visualizing Recursive Calls

A **recursion tree** is a diagram that shows every function call as a node and every recursive sub-call as a child node. It is the most important tool for understanding and analyzing recursive algorithms.

> **Real-World Analogy:** Think of a family tree. You are the root. Each call you make spawns "children." Those children may spawn their own children. The leaves of the tree are the base cases.

### Recursion Tree for `fibonacci(4)`

```
                    fib(4)
                  /        \
              fib(3)        fib(2)
             /      \      /      \
          fib(2)  fib(1) fib(1)  fib(0)
          /    \
       fib(1) fib(0)
```

- **Leaves** (base cases): `fib(0)` = 0, `fib(1)` = 1
- **Internal nodes**: Combine results of their children
- **Repeated work**: `fib(2)` is computed twice — this is a sign that **memoization** can help

---

## 4. Key Recursion Patterns

Every recursive problem follows one of these structural patterns:

| Pattern | Description | Example |
|---------|-------------|---------|
| **Linear Recursion** | Each call makes exactly one recursive call | Factorial, Sum of N |
| **Binary Recursion** | Each call makes two recursive calls | Fibonacci, Merge Sort |
| **Tail Recursion** | Recursive call is the last operation | Reverse array with two pointers |
| **Head Recursion** | Processing happens AFTER the recursive call returns | Printing digits in reverse |

```java
public class RecursionPatterns {

    // LINEAR RECURSION — one call per level
    public static long sumOfN(int n) {
        if (n == 0) return 0;           // Base case
        return n + sumOfN(n - 1);       // Recursive case: add n, solve for n-1
    }

    // BINARY RECURSION — two calls per level
    public static long fibonacci(int n) {
        if (n <= 1) return n;           // Base case: fib(0)=0, fib(1)=1
        return fibonacci(n - 1) + fibonacci(n - 2); // Two recursive calls
    }

    // HEAD RECURSION — recursive call FIRST, work happens on the way back UP
    public static void printReverse(int n) {
        if (n == 0) return;             // Base case: stop
        printReverse(n - 1);            // Go all the way down first
        System.out.print(n + " ");      // Then print on the way back up
        // Result: 1 2 3 4 5 (numbers printed in ascending order from n=5)
    }

    public static void main(String[] args) {
        System.out.println("Sum of 5: " + sumOfN(5));      // 15
        System.out.println("Fibonacci(6): " + fibonacci(6)); // 8
        printReverse(5);                                      // 1 2 3 4 5
    }
}
```

---

## 5. Common Recursion Pitfalls

| Mistake | What Goes Wrong | Fix |
|---------|-----------------|-----|
| **Missing base case** | Infinite recursion → StackOverflowError | Always define a base case first |
| **Wrong base case value** | Returns wrong answer | Trace manually for small inputs |
| **Not reducing the problem** | Recursive call with same input → infinite loop | Ensure input gets strictly smaller |
| **Using `int` for large results** | Overflow for large N (e.g., factorial(20)) | Use `long` for factorials, sums |
| **Redundant recomputation** | Exponential time (e.g., naive Fibonacci) | Use memoization (store computed results) |

```java
// WRONG — no base case → StackOverflowError
public static int badRecursion(int n) {
    return n + badRecursion(n - 1); // Never stops!
}

// CORRECT — clear base case, problem reduces each time
public static int goodRecursion(int n) {
    if (n == 0) return 0;           // Base case
    return n + goodRecursion(n - 1); // n decreases each call
}
```

---

## 6. Complexity Analysis of Recursive Functions

### Time Complexity

Count the **total number of recursive calls** made.

| Function | Calls Made | Time Complexity |
|----------|-----------|-----------------|
| `factorial(n)` | n calls in a straight line | `O(N)` |
| `sumOfN(n)` | n calls in a straight line | `O(N)` |
| `fibonacci(n)` | ~2^N calls (binary tree) | `O(2^N)` |
| `reverseArray(n)` | n/2 calls | `O(N)` |

### Space Complexity

The space is determined by the **maximum depth of the call stack** at any one time.

| Function | Max Stack Depth | Space Complexity |
|----------|----------------|------------------|
| `factorial(n)` | n frames deep | `O(N)` |
| `fibonacci(n)` | n frames deep (longest path) | `O(N)` |
| `reverseArray(n)` | n/2 frames | `O(N)` |

> **Key Insight:** Even if a recursive function uses no extra arrays, it still uses `O(depth)` **stack space**. This is often overlooked by beginners.

---

## Recursion Cheat Sheet

| Concept | Rule | Java Tool |
|---------|------|-----------|
| Base Case | Always define it FIRST | `if (n <= 0) return ...` |
| Stack Space | Every recursive call uses stack memory | Max depth = space complexity |
| Binary Recursion | Leads to exponential time without memoization | Use `long[]` memo array |
| Overflow prevention | Factorials grow very fast | Use `long` not `int` |
| Recursion → Iteration | Tail-recursive functions can be made iterative | Use a loop + accumulator |

---

# Practice Problems (Session 6)

---

## 1. Factorial of a Number

[Factorial of Large Number (GFG)](https://www.geeksforgeeks.org/factorial-large-number/)

### Problem Statement

Given a non-negative integer `n`, compute its **factorial** using recursion. The **factorial** of `n` (written as `n!`) is the product of all positive integers from `1` to `n`. By definition, `0! = 1`.

**Examples:**

| Input (n) | Output | Reason |
|-----------|--------|--------|
| `0` | `1` | By definition: 0! = 1 |
| `1` | `1` | 1! = 1 |
| `5` | `120` | 5! = 5 × 4 × 3 × 2 × 1 = 120 |
| `10` | `3628800` | 10! = 10 × 9 × ... × 1 = 3,628,800 |

---

### Approach 1: Brute Force (Iterative — using a loop)

Use a simple `for` loop to multiply numbers from `1` to `n`. This is straightforward, uses O(1) space, and avoids any stack overhead.

#### The Math/Logic Behind It

`n! = 1 × 2 × 3 × ... × n`

We start with an accumulator `result = 1` and multiply each number from `2` to `n` into it.

**Dry Run for n = 5:**

| Iteration (i) | result = result × i | result Value |
|---------------|---------------------|--------------|
| Start         | —                   | 1            |
| i = 2         | 1 × 2               | 2            |
| i = 3         | 2 × 3               | 6            |
| i = 4         | 6 × 4               | 24           |
| i = 5         | 24 × 5              | 120 ✅        |

```java
public class FactorialIterative {
    public static long factorialIterative(int n) {
        // Use 'long' to handle large results — int overflows at n=13!
        long result = 1;

        // Multiply result by each number from 2 to n
        for (int i = 2; i <= n; i++) {
            result *= i; // Accumulate the product
        }

        return result; // Return the final product
    }

    public static void main(String[] args) {
        System.out.println(factorialIterative(0));  // 1 (0! = 1 by definition)
        System.out.println(factorialIterative(5));  // 120
        System.out.println(factorialIterative(10)); // 3628800
        System.out.println(factorialIterative(15)); // 1307674368000
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the loop runs exactly N-1 times, performing one multiplication per iteration.
* **Space Complexity:** `O(1)` — only the `result` variable is used; no extra arrays or stack frames are created.

---

### Approach 2: Optimal (Recursive — Direct Mathematical Definition)

Express the factorial using its own mathematical definition: `n! = n × (n-1)!`. The base case is `factorial(0) = 1` or `factorial(1) = 1`. This approach maps directly to the mathematical recurrence and is the canonical recursive solution.

#### The Math/Logic Behind It

The mathematical recurrence relation:
- `factorial(0) = 1` ← base case
- `factorial(n) = n × factorial(n-1)` ← recursive case

**Dry Run for n = 5 (call stack trace):**

| Call | Expression | Waits For | Returns |
|------|------------|-----------|---------|
| `factorial(5)` | `5 × factorial(4)` | `factorial(4)` | `5 × 24 = 120` |
| `factorial(4)` | `4 × factorial(3)` | `factorial(3)` | `4 × 6 = 24` |
| `factorial(3)` | `3 × factorial(2)` | `factorial(2)` | `3 × 2 = 6` |
| `factorial(2)` | `2 × factorial(1)` | `factorial(1)` | `2 × 1 = 2` |
| `factorial(1)` | Base case → return 1 | — | `1` |

```java
public class FactorialRecursive {
    public static long factorial(int n) {
        // BASE CASE: 0! = 1 and 1! = 1 — stop recursion here
        if (n <= 1) return 1;

        // RECURSIVE CASE: n! = n × (n-1)!
        // The function calls itself with a SMALLER input (n-1)
        // Java computes factorial(n-1) first, then multiplies by n
        return (long) n * factorial(n - 1);
    }

    public static void main(String[] args) {
        System.out.println(factorial(0));  // 1
        System.out.println(factorial(5));  // 120
        System.out.println(factorial(10)); // 3628800
        System.out.println(factorial(15)); // 1307674368000
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the function calls itself exactly N times (from n down to 1), with O(1) work per call.
* **Space Complexity:** `O(N)` — the call stack grows N frames deep (one frame per recursive call); this is the hidden cost of recursion.

### Teaching Tips
1. **Build the intuition with a story first:** "If I ask you: what is 5 factorial? You say: 5 times 4 factorial. But to know 4 factorial, you need 3 factorial, and so on. You're delegating the smaller problem to 'future you.' That's recursion." Students immediately understand the delegation model.
2. **Show the StackOverflowError on purpose:** Call `factorial(-1)` or `factorial(10000)` without a guard. Let students see the error. Then explain that a missing/incorrect base case causes infinite recursion. This makes the base case feel critical, not optional.
3. **Emphasize the `long` data type:** Write `factorial(13)` with `int` on the board — the result overflows to a negative number. Then switch to `long` and show the correct result. This trains students to always consider overflow when results can grow large.

---

## 2. Fibonacci Number

[Fibonacci Number (LeetCode 509)](https://leetcode.com/problems/fibonacci-number/)

### Problem Statement

The **Fibonacci sequence** is defined as: `F(0) = 0`, `F(1) = 1`, and `F(n) = F(n-1) + F(n-2)` for `n > 1`. Given an integer `n`, return `F(n)`.

**Examples:**

| Input (n) | Output | Reason |
|-----------|--------|--------|
| `0` | `0` | F(0) = 0 (base case) |
| `1` | `1` | F(1) = 1 (base case) |
| `6` | `8` | 0, 1, 1, 2, 3, 5, 8 — 6th index is 8 |
| `10` | `55` | 0,1,1,2,3,5,8,13,21,34,55 — 10th index |

---

### Approach 1: Brute Force (Naive Recursion)

Directly translate the mathematical definition into code: `fib(n) = fib(n-1) + fib(n-2)`. This is simple and elegant but extremely inefficient because the **same subproblems are solved repeatedly**.

#### The Math/Logic Behind It

For `fib(5)`, the recursion tree shows massive repeated work:

```
                     fib(5)
                   /         \
              fib(4)          fib(3)
             /       \       /      \
         fib(3)    fib(2) fib(2)  fib(1)
         /    \    /    \  /    \
      fib(2) fib(1) fib(1) fib(0) fib(1) fib(0)
      /    \
   fib(1) fib(0)
```

`fib(3)` is computed **twice**, `fib(2)` is computed **three times** — exponential waste!

**Dry Run for n = 6 (tracking unique calls only):**

| Call | Sub-calls Made | Returns |
|------|---------------|---------|
| `fib(6)` | `fib(5) + fib(4)` | 8 |
| `fib(5)` | `fib(4) + fib(3)` | 5 |
| `fib(4)` | `fib(3) + fib(2)` | 3 |
| `fib(3)` | `fib(2) + fib(1)` | 2 |
| `fib(2)` | `fib(1) + fib(0)` | 1 |
| `fib(1)` | Base case | 1 |
| `fib(0)` | Base case | 0 |

```java
public class FibonacciBrute {
    public static long fibBrute(int n) {
        // BASE CASE 1: fib(0) = 0
        if (n == 0) return 0;

        // BASE CASE 2: fib(1) = 1
        if (n == 1) return 1;

        // RECURSIVE CASE: fib(n) = fib(n-1) + fib(n-2)
        // Each call spawns TWO more calls — this leads to exponential time
        return fibBrute(n - 1) + fibBrute(n - 2);
    }

    public static void main(String[] args) {
        System.out.println(fibBrute(0));  // 0
        System.out.println(fibBrute(6));  // 8
        System.out.println(fibBrute(10)); // 55
        // WARNING: fibBrute(50) will take very long — exponential time!
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(2^N)` — each call branches into two calls; the total number of calls grows exponentially, roughly as `2^N`.
* **Space Complexity:** `O(N)` — the call stack is at most N frames deep at any point (the deepest path down the left side of the recursion tree is N levels deep).

---

### Approach 2: Optimal (Recursive + Memoization / Top-Down DP)

Use a **memo array** to store the result of each `fib(n)` the first time it is computed. Before computing `fib(n)`, check if it's already in the memo array. If yes, return it immediately (O(1)). This eliminates all redundant recomputation.

#### The Math/Logic Behind It

The key insight: **if `fib(n)` is already computed, never compute it again**. We store computed values in a `long[]` array indexed by `n`. The memo array converts exponential time to linear time.

**Dry Run for n = 6 with memoization (first pass only):**

| Call | In Memo? | Action | Stored in memo[n] |
|------|----------|--------|-------------------|
| `fib(6)` | No | Compute `fib(5) + fib(4)` | memo[6] = 8 |
| `fib(5)` | No | Compute `fib(4) + fib(3)` | memo[5] = 5 |
| `fib(4)` | No | Compute `fib(3) + fib(2)` | memo[4] = 3 |
| `fib(3)` | No | Compute `fib(2) + fib(1)` | memo[3] = 2 |
| `fib(2)` | No | Compute `fib(1) + fib(0)` | memo[2] = 1 |
| `fib(1)` | No | Base case → 1 | — |
| `fib(0)` | No | Base case → 0 | — |
| `fib(4)` (second time) | **Yes** ✅ | Return memo[4] = 3 instantly | — |

Every subproblem is solved **at most once**.

```java
public class FibonacciMemo {
    public static long fibMemo(int n, long[] memo) {
        // BASE CASE 1: fib(0) = 0
        if (n == 0) return 0;

        // BASE CASE 2: fib(1) = 1
        if (n == 1) return 1;

        // CHECK MEMO: If we've already computed fib(n), return it immediately
        if (memo[n] != -1) return memo[n]; // -1 means "not yet computed"

        // RECURSIVE CASE: Compute fib(n), store it in memo, then return it
        // Store result before returning so future calls can reuse it
        memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
        return memo[n];
    }

    public static long fibonacci(int n) {
        // Initialize memo array with -1 (sentinel value: "not computed yet")
        long[] memo = new long[n + 1];
        java.util.Arrays.fill(memo, -1); // All entries start as "uncomputed"
        return fibMemo(n, memo);
    }

    public static void main(String[] args) {
        System.out.println(fibonacci(0));  // 0
        System.out.println(fibonacci(6));  // 8
        System.out.println(fibonacci(10)); // 55
        System.out.println(fibonacci(50)); // 12586269025 — fast even for large n!
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each unique value of `fib(n)` is computed exactly once and then looked up in O(1) for all subsequent requests; there are only N unique subproblems.
* **Space Complexity:** `O(N)` — the memo array of size N+1 stores all computed results, plus up to N frames on the call stack.

### Teaching Tips
1. **Draw the recursion tree WITHOUT memoization first:** Let students see the exponential explosion of calls for `fib(6)`. Count the number of nodes together. Then overlay red "X" marks on all the repeated calls. This viscerally shows the inefficiency and makes memoization feel like a necessary invention, not just an optimization.
2. **Introduce memoization as "remembering your work":** Say: "You've already solved `fib(3)` — why solve it again? Write the answer on a sticky note and look it up next time." That's literally what a memo array is. This builds intuition before any code is written.
3. **Common mistake — forgetting to initialize memo to -1:** If students use `0` as the default memo value, `fib(0) = 0` will be incorrectly treated as "already computed." Show this bug, then explain why `-1` (a value that can never be a valid Fibonacci number) is the correct sentinel.

---

## 3. Sum of First N Natural Numbers

[Sum of first N natural numbers (GFG)](https://www.geeksforgeeks.org/sum-of-first-n-natural-numbers/)

### Problem Statement

Given a positive integer `n`, compute the **sum of all natural numbers from 1 to n** using recursion. Return the result. Note: for large values of `n`, the sum can exceed `int` range.

**Examples:**

| Input (n) | Output | Reason |
|-----------|--------|--------|
| `1` | `1` | Sum = 1 |
| `5` | `15` | 1 + 2 + 3 + 4 + 5 = 15 |
| `10` | `55` | 1 + 2 + ... + 10 = 55 |
| `100` | `5050` | Well-known Gauss formula result |

---

### Approach 1: Brute Force (Iterative Loop)

Use a `for` loop to accumulate the sum from 1 to n. This is the most straightforward approach.

#### The Math/Logic Behind It

`Sum(n) = 1 + 2 + 3 + ... + n`

Start with `sum = 0` and add each number from `1` to `n`.

**Dry Run for n = 5:**

| i | sum = sum + i | sum Value |
|---|---------------|-----------|
| 1 | 0 + 1 | 1 |
| 2 | 1 + 2 | 3 |
| 3 | 3 + 3 | 6 |
| 4 | 6 + 4 | 10 |
| 5 | 10 + 5 | 15 ✅ |

```java
public class SumOfN {
    public static long sumIterative(int n) {
        long sum = 0; // Use 'long' since sum of large N can exceed int range

        // Add every number from 1 to n into the accumulator
        for (int i = 1; i <= n; i++) {
            sum += i; // Accumulate the sum
        }

        return sum;
    }

    public static void main(String[] args) {
        System.out.println(sumIterative(1));   // 1
        System.out.println(sumIterative(5));   // 15
        System.out.println(sumIterative(10));  // 55
        System.out.println(sumIterative(100)); // 5050
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the loop runs exactly N times, performing one addition per iteration.
* **Space Complexity:** `O(1)` — only the `sum` accumulator variable is used; no extra memory or stack frames.

---

### Approach 2: Optimal (Recursive Definition)

Express the sum using its own definition: `sum(n) = n + sum(n-1)`. The base case is `sum(0) = 0` or `sum(1) = 1`. This maps directly to the recursive structure and is the classic recursion teaching example.

#### The Math/Logic Behind It

The recurrence relation:
- `sum(0) = 0` ← base case
- `sum(n) = n + sum(n-1)` ← recursive case

**Dry Run for n = 5 (call stack trace):**

| Call | Expression | Returns |
|------|------------|---------|
| `sum(5)` | `5 + sum(4)` | `5 + 10 = 15` ✅ |
| `sum(4)` | `4 + sum(3)` | `4 + 6 = 10` |
| `sum(3)` | `3 + sum(2)` | `3 + 3 = 6` |
| `sum(2)` | `2 + sum(1)` | `2 + 1 = 3` |
| `sum(1)` | Base case → 1 | `1` |

```java
public class SumOfN {
    public static long sumRecursive(int n) {
        // BASE CASE: sum of 0 numbers is 0, sum of 1 number is 1
        if (n <= 0) return 0;

        // RECURSIVE CASE: sum(n) = n + sum(n-1)
        // We trust that sumRecursive(n-1) correctly returns sum of 1 to (n-1)
        // Then we just add n on top of it
        return n + sumRecursive(n - 1);
    }

    public static void main(String[] args) {
        System.out.println(sumRecursive(1));   // 1
        System.out.println(sumRecursive(5));   // 15
        System.out.println(sumRecursive(10));  // 55
        System.out.println(sumRecursive(100)); // 5050
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the function calls itself N times, once for each integer from n down to 1, with O(1) work (one addition) per call.
* **Space Complexity:** `O(N)` — the call stack grows N frames deep (one frame per recursive call), unlike the iterative version which uses O(1) space.

### Teaching Tips
1. **Use Sum of N as the "Hello World" of recursion:** Before Fibonacci and Factorial, start here. The function does only ONE thing per call (adds n), and the structure is crystal clear. Ask: "What is sum(5)? It's 5 plus sum(4). What is sum(4)? It's 4 plus sum(3)..." Let the students finish the chain aloud. They'll discover recursion themselves.
2. **Compare iterative vs recursive space usage side-by-side:** Run both versions with n=10000. The iterative version works fine. The recursive version may throw a `StackOverflowError`. This is the most memorable way to teach that recursion uses stack space — and that this matters in practice.
3. **Introduce the formula `n*(n+1)/2` as a bonus:** After showing O(N) recursive and iterative versions, reveal that there's an O(1) formula: `n*(n+1)/2`. This is a great teaching moment about mathematical insight vs computational brute force, and sets up future discussions about closed-form solutions.

---

## 4. Reverse an Array Using Recursion

[Reverse an Array (GFG)](https://www.geeksforgeeks.org/write-a-program-to-reverse-an-array-or-string/)

### Problem Statement

Given an integer array `arr` of size `n`, **reverse it in-place** using recursion. Do not use any extra array. The array must be modified directly.

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| `[1, 2, 3, 4, 5]` | `[5, 4, 3, 2, 1]` | All elements reversed end-to-end |
| `[10, 20, 30]` | `[30, 20, 10]` | First ↔ Last, middle stays |
| `[7]` | `[7]` | Single element — unchanged |
| `[1, 2]` | `[2, 1]` | Two elements — simply swap |

---

### Approach 1: Brute Force (Extra Array)

Copy the original array into a new array in **reverse order**, then copy everything back. This is simple to understand but uses O(N) extra space.

#### The Math/Logic Behind It

Element at index `i` in the original maps to index `n-1-i` in the reversed array.

**Dry Run for arr = [1, 2, 3, 4, 5], n = 5:**

| i (new index) | n-1-i (source) | temp[i] ← arr[n-1-i] |
|---------------|----------------|-----------------------|
| 0 | 4 | temp[0] = 5 |
| 1 | 3 | temp[1] = 4 |
| 2 | 2 | temp[2] = 3 |
| 3 | 1 | temp[3] = 2 |
| 4 | 0 | temp[4] = 1 |

Copy back: `arr = [5, 4, 3, 2, 1]` ✅

```java
public class ReverseArray {
    public static void reverseBrute(int[] arr) {
        int n = arr.length;

        // Step 1: Create a temp array to hold the reversed order
        int[] temp = new int[n];

        // Step 2: Fill temp in reverse order from arr
        for (int i = 0; i < n; i++) {
            temp[i] = arr[n - 1 - i]; // Mirror mapping
        }

        // Step 3: Copy temp back into the original array
        for (int i = 0; i < n; i++) {
            arr[i] = temp[i];
        }
    }

    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        reverseBrute(arr1);
        System.out.println(java.util.Arrays.toString(arr1)); // [5, 4, 3, 2, 1]

        int[] arr2 = {10, 20, 30};
        reverseBrute(arr2);
        System.out.println(java.util.Arrays.toString(arr2)); // [30, 20, 10]

        int[] arr3 = {7};
        reverseBrute(arr3);
        System.out.println(java.util.Arrays.toString(arr3)); // [7]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — two passes over the array of size N: one to fill `temp`, one to copy back.
* **Space Complexity:** `O(N)` — the `temp` array of size N is allocated as extra memory.

---

### Approach 2: Optimal (Recursive Two-Pointer — In-Place)

Use two indices `left` and `right`. **Swap** the elements at these positions, then **recursively** call the function for the inner sub-array (`left+1`, `right-1`). The base case is when `left >= right` (pointers have met or crossed).

#### The Math/Logic Behind It

The key insight: **reversing an array is the same as swapping the outermost pair and then recursively reversing the inner sub-array**. Each recursive call solves a strictly smaller problem (the inner sub-array shrinks by 2 each time).

**Dry Run for arr = [1, 2, 3, 4, 5], left=0, right=4:**

| Call | left | right | Swap | Array After Swap |
|------|------|-------|------|-----------------|
| 1 | 0 | 4 | arr[0] ↔ arr[4]: 1 ↔ 5 | `[5, 2, 3, 4, 1]` |
| 2 | 1 | 3 | arr[1] ↔ arr[3]: 2 ↔ 4 | `[5, 4, 3, 2, 1]` |
| 3 | 2 | 2 | left == right → Base case, STOP | `[5, 4, 3, 2, 1]` ✅ |

```java
public class ReverseArray {
    public static void reverseRecursive(int[] arr, int left, int right) {
        // BASE CASE: When left and right meet or cross, we're done
        // (left == right means odd-length middle element, no swap needed)
        if (left >= right) return;

        // SWAP the elements at the left and right positions
        int temp   = arr[left];   // Temporarily store the left element
        arr[left]  = arr[right];  // Put right element into left position
        arr[right] = temp;        // Put stored left element into right position

        // RECURSIVE CASE: Solve the inner sub-array (one step smaller from each end)
        reverseRecursive(arr, left + 1, right - 1);
    }

    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        reverseRecursive(arr1, 0, arr1.length - 1);
        System.out.println(java.util.Arrays.toString(arr1)); // [5, 4, 3, 2, 1]

        int[] arr2 = {10, 20, 30};
        reverseRecursive(arr2, 0, arr2.length - 1);
        System.out.println(java.util.Arrays.toString(arr2)); // [30, 20, 10]

        int[] arr3 = {7};
        reverseRecursive(arr3, 0, arr3.length - 1);
        System.out.println(java.util.Arrays.toString(arr3)); // [7]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the function swaps N/2 pairs total; each call does O(1) work, and there are N/2 total calls.
* **Space Complexity:** `O(N)` — the call stack grows N/2 frames deep (one frame per recursive call); though no extra arrays are used, recursion itself consumes stack memory.

### Teaching Tips
1. **Draw the "shrinking sub-array" on the board:** Write `[1, 2, 3, 4, 5]`. Circle the outermost pair (1 and 5). Swap them. Now circle the next pair (2 and 4). Swap. The middle (3) stays. Students instantly see the recursive structure — it's just a series of pair swaps from outside in.
2. **Contrast with Class 4's two-pointer reverse:** Students already know the iterative two-pointer swap (from String reversal). Show that this is the exact same logic, but expressed recursively. Connecting new material to something they already know is a powerful teaching technique.
3. **Ask: "What happens if `left > right`?"** For even-length arrays, left and right will eventually cross (left = 3, right = 2). The condition `left >= right` handles BOTH even and odd cases correctly. Have students trace a 4-element array to verify this themselves.

---

# Class 6 Homework

### Easy (Mandatory)
1. [Power of Two (LeetCode 231)](https://leetcode.com/problems/power-of-two/) — Check if a number is a power of two using recursion: divide by 2 until you reach 1 or an odd number
2. [Sum of Digits (GFG)](https://www.geeksforgeeks.org/sum-of-digits-of-a-number-using-recursion/) — Recursively compute the sum of all digits of a given positive integer
3. [Print 1 to N without a loop (GFG)](https://www.geeksforgeeks.org/print-1-to-n-without-using-loops/) — Print all numbers from 1 to N using only recursion, no loops allowed

### Medium (Recommended)
1. [Climbing Stairs (LeetCode 70)](https://leetcode.com/problems/climbing-stairs/) — You can climb 1 or 2 steps at a time; use recursion + memoization to count distinct ways to reach the top of an N-step staircase
2. [Subsets (LeetCode 78)](https://leetcode.com/problems/subsets/) — Given an integer array with distinct elements, use recursion to return all possible subsets (the power set)
3. [Generate Parentheses (LeetCode 22)](https://leetcode.com/problems/generate-parentheses/) — Given n pairs of parentheses, recursively generate all combinations of well-formed parentheses strings

### Challenge Problem (Optional)
[Permutations (LeetCode 46)](https://leetcode.com/problems/permutations/) — Given an array of distinct integers, use recursion with backtracking to return all possible permutations — a classic recursive problem that builds the foundation for backtracking algorithms
