# Week 1 – Class 1: Introduction to Programming & Complexity Analysis

---

# Session 1: Introduction to Programming & Complexity Analysis

## 1. What is DSA?

### Data Structure

A Data Structure is a way of organizing and storing data so that it can be accessed and modified efficiently.

### Examples

* Array
* Linked List
* Stack
* Queue
* Tree
* Graph

### Algorithm

An Algorithm is a step-by-step procedure used to solve a problem.

### Example

Problem: Find the largest number in an array.

Algorithm:

1. Assume first element is largest.
2. Compare with remaining elements.
3. Update largest when bigger element is found.
4. Print largest element.

---

## 2. Why Learn DSA?

### Academic Benefits

* Helps in university exams
* Improves programming fundamentals

### Placement Benefits

Frequently asked in:

* [TCS](https://www.tcs.com?utm_source=chatgpt.com)
* [Infosys](https://www.infosys.com?utm_source=chatgpt.com)
* [Wipro](https://www.wipro.com?utm_source=chatgpt.com)
* [Cognizant](https://www.cognizant.com?utm_source=chatgpt.com)

### Industry Benefits

* Better coding skills
* Better problem-solving
* Efficient software development

---

# 3. Time Complexity

Time Complexity measures how execution time grows as input size increases.

## Common Complexities

| Complexity | Name         |
| ---------- | ------------ |
| O(1)       | Constant     |
| O(log n)   | Logarithmic  |
| O(n)       | Linear       |
| O(n log n) | Linearithmic |
| O(n²)      | Quadratic    |
| O(2ⁿ)      | Exponential  |

---

### Example 1

```java
for(int i=0;i<n;i++){
    System.out.println(i);
}
```

Time Complexity:

```
O(n)
```

---

### Example 2

```java
for(int i=0;i<n;i++){
    for(int j=0;j<n;j++){
        System.out.println(i+j);
    }
}
```

Time Complexity:

```
O(n²)
```

---

## Complexity Cheat Sheet

| Operation            | Complexity |
| -------------------- | ---------- |
| Access Array Element | O(1)       |
| Linear Search        | O(n)       |
| Binary Search        | O(log n)   |
| Nested Loops         | O(n²)      |

---

# 4. Space Complexity

Amount of extra memory used by an algorithm.

Example:

```java
int sum = 0;
```

Space Complexity:

```
O(1)
```

---

Example:

```java
int arr[] = new int[n];
```

Space Complexity:

```
O(n)
```

---

# 5. Functions

Function = Reusable block of code.

Example:

```java
public static int add(int a,int b){
    return a+b;
}
```

Benefits:

* Code Reusability
* Better Readability
* Easier Maintenance

---

# 6. Problem Solving Approach

Before coding:

### Step 1

Understand Problem

### Step 2

Identify Inputs & Outputs

### Step 3

Think of Logic

### Step 4

Write Algorithm

### Step 5

Convert into Code

### Step 6

Test with Examples

---

# Practice Problems (Session 1)

---

## 1. Prime Number

[Prime Number (GFG)](https://www.geeksforgeeks.org/problems/prime-number2314/1?utm_source=chatgpt.com)

### Problem Statement

Given an integer `N`, determine whether it is a prime number or not. A prime number is a number **greater than 1** that has **exactly two factors**: 1 and itself.

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| 7     | true   | Factors are only 1 and 7 |
| 12    | false  | Factors: 1, 2, 3, 4, 6, 12 |
| 1     | false  | Only one factor — not prime |
| 2     | true   | Smallest prime number |

---

### Approach 1: Brute Force (Count All Factors)

The simplest approach is to **count the total number of factors** of `N` by iterating from `1` to `N`. If the count is exactly 2 (i.e., 1 and N itself), then it is prime.

```java
public class PrimeNumber {
    public static boolean isPrimeBruteForce(int n) {
        // A prime number is strictly greater than 1
        if (n <= 1) {
            return false;
        }

        int factorsCount = 0;

        // Check divisibility for all numbers from 1 to n
        for (int i = 1; i <= n; i++) {
            if (n % i == 0) {
                factorsCount++; // i is a factor of n
            }
        }

        // If it has exactly 2 factors (1 and n), it is prime
        return factorsCount == 2;
    }

    public static void main(String[] args) {
        System.out.println("7 is prime? " + isPrimeBruteForce(7));   // true
        System.out.println("12 is prime? " + isPrimeBruteForce(12)); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the loop iterates all the way from 1 to N.
* **Space Complexity:** `O(1)` — no extra memory is used.

---

### Approach 2: Better Approach (Check 2 to N-1)

We already know that 1 and N are **always factors**. So instead of counting all factors, we just check if there is **any divisor between 2 and N-1**. The moment we find one, we immediately return `false` — no need to keep checking.

```java
public class PrimeNumber {
    public static boolean isPrimeBetter(int n) {
        if (n <= 1) {
            return false;
        }

        // Check for any divisor between 2 and n-1
        for (int i = 2; i < n; i++) {
            if (n % i == 0) {
                return false; // Found a factor — not prime, exit immediately
            }
        }

        // No factor found — it is prime
        return true;
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` in the worst case. For a prime number, the loop runs up to `N-1`. But for non-primes it exits early.
* **Space Complexity:** `O(1)`.

---

### Approach 3: Optimized (Up to √N)

#### The Math Behind It

Factors of a number **always appear in pairs**. For example, the factors of 36 are:

| Factor A | Factor B | A × B |
|----------|----------|-------|
| 1        | 36       | 36    |
| 2        | 18       | 36    |
| 3        | 12       | 36    |
| 4        | 9        | 36    |
| 6        | 6        | 36    |

Notice that after `√36 = 6`, the pairs just **flip and repeat**. For any factor pair `a × b = N`, at least one factor must be ≤ √N. So if no factor is found up to √N, none will be found beyond it either.

```java
public class PrimeNumber {
    public static boolean isPrimeOptimal(int n) {
        if (n <= 1) {
            return false;
        }

        // Only check up to sqrt(n)
        // Using i * i <= n avoids calling Math.sqrt() which is slower
        for (int i = 2; i * i <= n; i++) {
            if (n % i == 0) {
                return false; // Found a factor — not prime
            }
        }

        // No factor found — it is prime
        return true;
    }

    public static void main(String[] args) {
        System.out.println("29 is prime? " + isPrimeOptimal(29)); // true
        System.out.println("36 is prime? " + isPrimeOptimal(36)); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(√N)` — the loop runs only up to the square root of N. For N = 1,000,000, we check ~1000 numbers instead of 1,000,000!
* **Space Complexity:** `O(1)`.

### Teaching Tips
1. **Start with intuition:** Ask students "How do you check if 7 is prime? What about 9?" — let them describe the process before showing code.
2. **Introduce Early Exit:** Show how finding just one factor is enough to conclude "not prime". This trains early-return thinking.
3. **Visualize the Factor Pairs:** Write the table of factor pairs for 36 on the board. Make students see the "flip point" at √36 = 6. The √N optimization becomes visually obvious.

---

## 2. Palindrome Number

[Palindrome Number (LeetCode)](https://leetcode.com/problems/palindrome-number/?utm_source=chatgpt.com)

### Problem Statement

Given an integer `x`, return `true` if `x` reads the same **forward and backward** (palindrome), and `false` otherwise.

**Examples:**

| Input  | Output | Reason |
|--------|--------|--------|
| 121    | true   | 121 reversed is 121 |
| -121   | false  | -121 reversed is 121- (invalid) |
| 10     | false  | 10 reversed is 01 = 1, not equal |
| 0      | true   | 0 reversed is 0 |

---

### Approach 1: Brute Force (Convert to String)

The simplest idea is to **convert the integer to a String**, reverse that string using Java's built-in `StringBuilder.reverse()`, and compare it with the original.

```java
public class PalindromeNumber {
    public static boolean isPalindromeBruteForce(int x) {
        // Convert the integer to a string
        String str = Integer.toString(x);

        // Reverse the string using StringBuilder
        String reversed = new StringBuilder(str).reverse().toString();

        // Compare original and reversed strings
        return str.equals(reversed);
    }

    public static void main(String[] args) {
        System.out.println("121 is palindrome? " + isPalindromeBruteForce(121));  // true
        System.out.println("-121 is palindrome? " + isPalindromeBruteForce(-121)); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(D)` where D is the number of digits. We process each digit once during reversal.
* **Space Complexity:** `O(D)` — we store the original string and the reversed string.

---

### Approach 2: Optimal (Mathematical Digit Reversal)

#### The Math Behind It

We can reverse the number **mathematically without converting to a String**. The key operations are:
- `x % 10` → extracts the **last digit** of x
- `x / 10` → **removes** the last digit from x
- `reversed = reversed * 10 + digit` → **builds** the reversed number digit by digit

**Special Edge Cases to Handle First:**
- Negative numbers are **never** palindromes (e.g., `-121`)
- Numbers ending in `0` (except 0 itself) are **never** palindromes (e.g., `10` reversed is `01 = 1`)

**Dry Run for x = 121:**

| Iteration | x   | digit | reversed |
|-----------|-----|-------|----------|
| Start     | 121 | —     | 0        |
| 1         | 12  | 1     | 1        |
| 2         | 1   | 2     | 12       |
| 3         | 0   | 1     | 121      |

`original (121) == reversed (121)` → **true**

```java
public class PalindromeNumber {
    public static boolean isPalindromeOptimal(int x) {
        // Negative numbers are never palindromes
        // Numbers ending in 0 (except 0 itself) are never palindromes
        if (x < 0 || (x != 0 && x % 10 == 0)) {
            return false;
        }

        int original = x;
        int reversed = 0;

        while (x > 0) {
            int lastDigit = x % 10;                  // Extract last digit
            reversed = (reversed * 10) + lastDigit;  // Append to reversed number
            x = x / 10;                              // Remove last digit from x
        }

        // If original equals the reversed number, it is a palindrome
        return original == reversed;
    }

    public static void main(String[] args) {
        System.out.println("121 is palindrome? " + isPalindromeOptimal(121));  // true
        System.out.println("10 is palindrome? " + isPalindromeOptimal(10));    // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(D)` where D is the number of digits. Each iteration removes one digit.
* **Space Complexity:** `O(1)` — only a few integer variables, no extra memory.

### Teaching Tips
1. **The digit extraction pattern is fundamental:** `% 10` to get the last digit, `/ 10` to remove it. This pattern appears in almost every number-manipulation problem — master it here!
2. **Dry run on the board:** Take `x = 121`. Build the reversed number step by step as shown in the table above.
3. **Cover edge cases first:** Always ask students "What if the number is negative? What if it ends with 0?" — this trains them to think about edge cases before writing a single line of code.

---

## 3. Armstrong Number

[Armstrong Numbers (GFG)](https://www.geeksforgeeks.org/problems/armstrong-numbers2727/1?utm_source=chatgpt.com)

### Problem Statement

Given a 3-digit number, determine if it is an **Armstrong number**. A 3-digit Armstrong number satisfies:

> **N = (digit₁)³ + (digit₂)³ + (digit₃)³**

**Examples:**

| Input | Calculation                   | Output |
|-------|-------------------------------|--------|
| 153   | 1³ + 5³ + 3³ = 1+125+27 = 153 | true   |
| 371   | 3³ + 7³ + 1³ = 27+343+1 = 371 | true   |
| 123   | 1³ + 2³ + 3³ = 1+8+27 = 36   | false  |
| 100   | 1³ + 0³ + 0³ = 1              | false  |

---

### Approach 1: Brute Force (String Character Iteration)

Convert the number to a String, iterate over each character (digit), convert it back to an integer, cube it, and add to a running sum.

```java
public class ArmstrongNumber {
    public static boolean isArmstrongBruteForce(int n) {
        String str = Integer.toString(n); // Convert number to string
        int sum = 0;

        for (int i = 0; i < str.length(); i++) {
            int digit = str.charAt(i) - '0'; // Convert char digit to int ('5' - '0' = 5)
            sum += (digit * digit * digit);  // Add cube of digit to sum
        }

        // Check if sum of cubes equals original number
        return sum == n;
    }

    public static void main(String[] args) {
        System.out.println("153 is Armstrong? " + isArmstrongBruteForce(153)); // true
        System.out.println("123 is Armstrong? " + isArmstrongBruteForce(123)); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(D)` where D is the number of digits.
* **Space Complexity:** `O(D)` — for storing the string representation.

---

### Approach 2: Optimal (Mathematical Digit Extraction)

Use the same `% 10` and `/ 10` technique to extract each digit without converting to a string. No extra memory needed.

**Dry Run for n = 153:**

| Iteration | n   | digit | sum       |
|-----------|-----|-------|-----------|
| Start     | 153 | —     | 0         |
| 1         | 15  | 3     | 27        |
| 2         | 1   | 5     | 27+125=152|
| 3         | 0   | 1     | 152+1=153 |

`original (153) == sum (153)` → **true**

```java
public class ArmstrongNumber {
    public static boolean isArmstrongOptimal(int n) {
        int original = n; // Save original value to compare at the end
        int sum = 0;

        while (n > 0) {
            int digit = n % 10;               // Extract last digit
            sum += (digit * digit * digit);   // Add cube of digit to sum
            n = n / 10;                       // Remove last digit
        }

        // If sum of cubes equals original, it is an Armstrong number
        return original == sum;
    }

    public static void main(String[] args) {
        System.out.println("371 is Armstrong? " + isArmstrongOptimal(371)); // true
        System.out.println("100 is Armstrong? " + isArmstrongOptimal(100)); // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(D)` where D is the number of digits (≈ log₁₀ N iterations).
* **Space Complexity:** `O(1)` — only a few integer variables used.

### Teaching Tips
1. **Verify with a dry run:** Walk through `n = 153` using the table above. Students can see exactly how each digit is extracted and cubed.
2. **Connect to Palindrome:** Students who just learned Palindrome will immediately recognize the `% 10` / `/ 10` pattern. Reinforce it as a reusable core tool.

---

## 4. Factorial

[Factorial (GFG)](https://www.geeksforgeeks.org/problems/factorial5739/1?utm_source=chatgpt.com)

### Problem Statement

Given a positive integer `N`, find its **factorial** defined as:

> **N! = 1 × 2 × 3 × … × N**

> By convention, **0! = 1**.

**Examples:**

| Input | Output    |
|-------|-----------|
| 0     | 1         |
| 5     | 120       |
| 10    | 3,628,800 |

---

### Approach 1: Brute Force (Recursion — Top-Down)

Use a **top-down recursive** approach directly from the mathematical definition:
`N! = N × (N-1)!` with base case `0! = 1`.

```java
public class Factorial {
    public static long calculateFactorialRecursive(int n) {
        // Base case: 0! = 1 and 1! = 1
        if (n == 0 || n == 1) {
            return 1;
        }

        // Recursive case: N! = N × (N-1)!
        return n * calculateFactorialRecursive(n - 1);
    }

    public static void main(String[] args) {
        System.out.println("5! = " + calculateFactorialRecursive(5));  // 120
        System.out.println("10! = " + calculateFactorialRecursive(10)); // 3628800
    }
}
```

**Recursive Call Stack for n = 4:**
```
factorial(4)
  → 4 * factorial(3)
       → 3 * factorial(2)
            → 2 * factorial(1)
                 → returns 1
            → returns 2
       → returns 6
  → returns 24
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the function calls itself N times.
* **Space Complexity:** `O(N)` — each recursive call takes up space on the call stack. For very large N, this can cause a StackOverflow.

---

### Approach 2: Optimal (Iterative — Bottom-Up)

Instead of recursion, use a simple loop. Start from `1` and multiply up to `N`. No call stack overhead.

```java
public class Factorial {
    public static long calculateFactorialIterative(int n) {
        long fact = 1; // Use long to handle large factorial values (int overflows at 13!)

        // Multiply from 1 up to n (bottom-up)
        for (int i = 1; i <= n; i++) {
            fact = fact * i;
        }

        return fact;
    }

    public static void main(String[] args) {
        System.out.println("5! = " + calculateFactorialIterative(5));  // 120
        System.out.println("10! = " + calculateFactorialIterative(10)); // 3628800
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the loop runs exactly N times.
* **Space Complexity:** `O(1)` — only one variable `fact` is used. No call stack overhead.

### Teaching Tips
1. **Why `long` and not `int`?** `int` can store up to ~2.1 billion. `12! = 479,001,600` fits, but `13! = 6,227,020,800` does NOT. Always use `long` for factorial.
2. **Draw the call stack:** Show the stack growing for `n = 4` as in the diagram above. Students will clearly see what `O(N)` space means in practice.
3. **Compare both:** Recursion is elegant and mirrors the math definition. Iteration is safer and more efficient. Both are valid — context matters.

---

## 5. Fibonacci Number

[Fibonacci Number (LeetCode)](https://leetcode.com/problems/fibonacci-number/?utm_source=chatgpt.com)

### Problem Statement

The **Fibonacci sequence** is: `0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...`

Each number is the **sum of the two preceding ones**, starting from 0 and 1:
- F(0) = 0, F(1) = 1
- F(N) = F(N-1) + F(N-2) for N > 1

Given N, return the N-th Fibonacci number.

**Examples:**

| Input | Output |
|-------|--------|
| 0     | 0      |
| 1     | 1      |
| 5     | 5      |
| 10    | 55     |

---

### Approach 1: Brute Force (Recursion)

Directly translate the mathematical definition into code recursively. Simple to write but **extremely slow** — the same sub-problems are recalculated many times.

**Why it's slow:** To compute `F(5)`, we compute `F(4)` and `F(3)`. Inside `F(4)`, we compute `F(3)` again. `F(3)` is computed twice, `F(2)` is computed 3 times, and so on — redundant work grows exponentially.

```java
public class Fibonacci {
    public static int fibRecursive(int n) {
        // Base cases
        if (n == 0) return 0;
        if (n == 1) return 1;

        // Recursive case: F(n) = F(n-1) + F(n-2)
        return fibRecursive(n - 1) + fibRecursive(n - 2);
    }

    public static void main(String[] args) {
        System.out.println("F(10) = " + fibRecursive(10)); // 55
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(2^N)` — the recursion tree has approximately 2^N nodes. For N=40, this is over 1 billion operations!
* **Space Complexity:** `O(N)` — the maximum recursion depth at any time is N.

---

### Approach 2: Better (Iterative with Array)

Store each Fibonacci number in an array as we compute it. When we need `F(i)`, the values `F(i-1)` and `F(i-2)` are already saved — no redundant work.

```java
public class Fibonacci {
    public static int fibArray(int n) {
        if (n <= 1) return n;

        int[] dp = new int[n + 1]; // Array to store all results
        dp[0] = 0;
        dp[1] = 1;

        // Build up the array from index 2 to n
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2]; // Use previously stored values
        }

        return dp[n]; // Return the N-th Fibonacci number
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each Fibonacci number is computed exactly once.
* **Space Complexity:** `O(N)` — for the array of size N+1.

---

### Approach 3: Optimal (Iterative — Space Optimized)

We only ever need the **previous two numbers** to compute the next. So we don't need the whole array — just two variables that slide forward like a window.

**Dry Run for n = 7:**

| i | prev2 | prev1 | current |
|---|-------|-------|---------|
| 2 | 0     | 1     | 1       |
| 3 | 1     | 1     | 2       |
| 4 | 1     | 2     | 3       |
| 5 | 2     | 3     | 5       |
| 6 | 3     | 5     | 8       |
| 7 | 5     | 8     | 13      |

`F(7) = 13` ✅

```java
public class Fibonacci {
    public static int fibOptimal(int n) {
        if (n <= 1) return n;

        int prev2 = 0; // Represents F(i-2), starts as F(0)
        int prev1 = 1; // Represents F(i-1), starts as F(1)

        for (int i = 2; i <= n; i++) {
            int current = prev1 + prev2; // F(i) = F(i-1) + F(i-2)
            prev2 = prev1;               // Slide window forward
            prev1 = current;             // Slide window forward
        }

        return prev1; // prev1 now holds F(n)
    }

    public static void main(String[] args) {
        System.out.println("F(10) = " + fibOptimal(10)); // 55
        System.out.println("F(0) = " + fibOptimal(0));   // 0
        System.out.println("F(1) = " + fibOptimal(1));   // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the loop runs N-1 times.
* **Space Complexity:** `O(1)` — only two integer variables, regardless of how large N is.

### Teaching Tips
1. **Show the exponential blow-up:** Draw even a partial recursion tree for `F(6)` on the board. Students can visually count the repeated calls and understand *why* `O(2^N)` is terrible.
2. **Dry run the sliding window:** Use the table above on the board. Students find the "sliding" metaphor very intuitive.
3. **Teach the progression:** Always go Brute Force → Better → Optimal. This teaches the *thinking process* of optimization, not just the final answer.

---

## 6. Reverse Integer

[Reverse Integer (LeetCode)](https://leetcode.com/problems/reverse-integer/?utm_source=chatgpt.com)

### Problem Statement

Given a signed 32-bit integer `x`, return `x` with its **digits reversed**. If the reversed integer overflows the 32-bit signed integer range `[-2³¹, 2³¹ - 1]`, return `0`.

**Examples:**

| Input      | Output | Reason |
|------------|--------|--------|
| 123        | 321    | Normal reversal |
| -123       | -321   | Sign is preserved |
| 120        | 21     | Leading zeros are dropped |
| 1534236469 | 0      | Reversed value overflows int |

---

### Approach 1: Brute Force (String Manipulation)

Convert the number to a string, handle the negative sign separately, reverse the string, and parse it back to an integer. Use a try-catch to handle overflow.

```java
public class ReverseInteger {
    public static int reverseStringApproach(int x) {
        boolean isNegative = x < 0;

        // Work with absolute value to simplify string reversal
        String str = Integer.toString(Math.abs(x));

        // Reverse the string using StringBuilder
        String reversedStr = new StringBuilder(str).reverse().toString();

        try {
            int reversed = Integer.parseInt(reversedStr);
            // Restore the sign
            return isNegative ? -reversed : reversed;
        } catch (NumberFormatException e) {
            // parseInt throws this when the value overflows int range
            return 0;
        }
    }

    public static void main(String[] args) {
        System.out.println("reverse(123) = " + reverseStringApproach(123));   // 321
        System.out.println("reverse(-123) = " + reverseStringApproach(-123)); // -321
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(D)` where D is the number of digits.
* **Space Complexity:** `O(D)` — for storing the string and reversed string.

---

### Approach 2: Optimal (Mathematical Digit Extraction)

#### The Math Behind It

Use the same `% 10` and `/ 10` pattern to reverse the number **without any String conversion**:
- `x % 10` → gets the last digit (works for negatives too: `-123 % 10 = -3`)
- `x / 10` → removes the last digit
- `reversed = reversed * 10 + digit` → builds the reversed number

**Key trick for overflow:** Use a `long` variable to build `reversed`. At the end, check if the result is within the valid `int` range before casting. If not, return `0`.

**Dry Run for x = -123:**

| Iteration | x    | digit | reversed |
|-----------|------|-------|----------|
| Start     | -123 | —     | 0        |
| 1         | -12  | -3    | -3       |
| 2         | -1   | -2    | -32      |
| 3         | 0    | -1    | -321     |

`reversed = -321`, within int range → return **-321** ✅

```java
public class ReverseInteger {
    public static int reverseOptimal(int x) {
        long reversed = 0; // Use long to safely store value before overflow check

        while (x != 0) { // Works for both positive and negative x
            int digit = x % 10;                 // Extract last digit (negative for negative x)
            reversed = (reversed * 10) + digit; // Build reversed number
            x = x / 10;                         // Remove last digit
        }

        // Check if the reversed number fits in 32-bit signed integer range
        if (reversed > Integer.MAX_VALUE || reversed < Integer.MIN_VALUE) {
            return 0; // Overflow — return 0 as per problem requirement
        }

        return (int) reversed;
    }

    public static void main(String[] args) {
        System.out.println("reverse(123) = " + reverseOptimal(123));    // 321
        System.out.println("reverse(-123) = " + reverseOptimal(-123));  // -321
        System.out.println("reverse(120) = " + reverseOptimal(120));    // 21
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(D)` where D is the number of digits. The loop runs once per digit.
* **Space Complexity:** `O(1)` — only a few variables used, no extra data structures.

### Teaching Tips
1. **Overflow is a real-world concern:** `Integer.MAX_VALUE = 2,147,483,647`. Ask students what happens if we reverse `1,000,000,009`. The answer `9,000,000,001` overflows — always think about bounds!
2. **Why `long`?** Using `long` gives extra capacity to safely build the reversed number *before* deciding if it fits in an `int`. This is a common defensive coding pattern.
3. **Negative numbers are free:** Ask students what `-123 % 10` is in Java. The answer is `-3` — the sign propagates naturally through the math, so no special handling is needed.

---

# Class 1 Homework

### Easy (Mandatory)

1. [Palindrome Number (LeetCode)](https://leetcode.com/problems/palindrome-number/?utm_source=chatgpt.com)
2. [Prime Number (GFG)](https://www.geeksforgeeks.org/problems/prime-number2314/1?utm_source=chatgpt.com)
3. [Fibonacci Number (LeetCode)](https://leetcode.com/problems/fibonacci-number/?utm_source=chatgpt.com)
