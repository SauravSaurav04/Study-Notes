# Week 1 – Class 2: Arrays – Fundamentals

---

# Session 2: Arrays – Fundamentals

## 1. What is an Array?

An **Array** is the simplest and most widely used data structure. It stores a **fixed-size, ordered collection of elements of the same data type** in **contiguous (adjacent) memory locations**.

> **Real-World Analogy:** Imagine a row of numbered lockers in a school corridor. Each locker has a unique number (index), and you can directly reach any locker without opening the ones before it. That's exactly how an array works in memory.

### Key Properties

| Property          | Description                                        |
|-------------------|----------------------------------------------------|
| Fixed Size        | Size is declared at creation and cannot be changed |
| Same Data Type    | All elements must be of the same type              |
| Zero-Indexed      | First element is at index 0                        |
| Random Access     | Any element can be accessed in O(1) time           |
| Contiguous Memory | Elements are stored side by side in RAM            |

---

## 2. Declaring and Initializing Arrays in Java

```java
public class ArrayBasics {
    public static void main(String[] args) {

        // --- Method 1: Declare first, then initialize ---
        int[] arr1 = new int[5];   // Creates array of size 5, all values default to 0
        arr1[0] = 10;
        arr1[1] = 20;
        arr1[2] = 30;
        arr1[3] = 40;
        arr1[4] = 50;

        // --- Method 2: Declare and initialize together (array literal) ---
        int[] arr2 = {10, 20, 30, 40, 50};

        // --- Accessing an element ---
        System.out.println("First element: " + arr2[0]);   // 10
        System.out.println("Third element: " + arr2[2]);   // 30
        System.out.println("Last element:  " + arr2[arr2.length - 1]); // 50

        // --- Array length ---
        System.out.println("Size of array: " + arr2.length); // 5
    }
}
```

> **Memory Picture for `int[] arr = {10, 20, 30, 40, 50}`:**

| Index  | 0  | 1  | 2  | 3  | 4  |
|--------|----|----|----|----|----|
| Value  | 10 | 20 | 30 | 40 | 50 |
| Address| 100| 104| 108| 112| 116|

*(Each `int` occupies 4 bytes, so addresses increase by 4)*

---

## 3. Array Traversal

**Traversal** means visiting every element of the array exactly once — usually to print, sum, or process them.

```java
public class ArrayTraversal {
    public static void main(String[] args) {
        int[] arr = {10, 20, 30, 40, 50};

        // --- Method 1: Traditional for loop (gives index control) ---
        System.out.print("Using for loop: ");
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i] + " "); // Access element at index i
        }
        System.out.println();

        // --- Method 2: Enhanced for-each loop (simpler, no index needed) ---
        System.out.print("Using for-each: ");
        for (int element : arr) {
            System.out.print(element + " "); // Iterates through each element
        }
        System.out.println();
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we visit each of the N elements exactly once.
* **Space Complexity:** `O(1)` — no extra memory is used beyond the loop variable.

---

## 4. Insertion in an Array

Since arrays have a **fixed size**, "insertion" means shifting elements to make room, then placing the new value.

> **Analogy:** If seats in a row of chairs are all filled and you want to insert a new person at position 2, everyone from position 2 onward must shift one seat to the right.

```java
public class ArrayInsertion {
    public static void insertElement(int[] arr, int n, int position, int value) {
        // Shift elements from the end towards the position (right to left)
        for (int i = n - 1; i >= position; i--) {
            arr[i + 1] = arr[i]; // Move each element one step to the right
        }
        arr[position] = value; // Place the new value at the desired position
    }

    public static void main(String[] args) {
        int[] arr = new int[6]; // Size 6 to accommodate insertion
        arr[0] = 10; arr[1] = 20; arr[2] = 30; arr[3] = 40; arr[4] = 50;
        int n = 5; // Current number of elements

        insertElement(arr, n, 2, 99); // Insert 99 at index 2

        // Print result
        for (int i = 0; i <= n; i++) {
            System.out.print(arr[i] + " "); // Expected: 10 20 99 30 40 50
        }
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — in the worst case (insert at position 0), all N elements must shift right.
* **Space Complexity:** `O(1)` — shifting is done in-place, no extra array needed.

---

## 5. Deletion in an Array

**Deletion** means removing an element and shifting the remaining elements left to fill the gap.

```java
public class ArrayDeletion {
    public static int deleteElement(int[] arr, int n, int position) {
        // Shift elements left starting from the position after the deleted element
        for (int i = position; i < n - 1; i++) {
            arr[i] = arr[i + 1]; // Overwrite the deleted element with the next one
        }
        return n - 1; // Return the new size (one less than before)
    }

    public static void main(String[] args) {
        int[] arr = {10, 20, 30, 40, 50};
        int n = 5;

        n = deleteElement(arr, n, 2); // Delete element at index 2 (value: 30)

        for (int i = 0; i < n; i++) {
            System.out.print(arr[i] + " "); // Expected: 10 20 40 50
        }
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — in the worst case (delete at position 0), all N-1 elements shift left.
* **Space Complexity:** `O(1)` — no extra array needed, done in-place.

---

## 6. Linear Search

**Linear Search** is the simplest searching algorithm. It checks every element one by one from left to right until the target is found or the array ends.

```java
public class LinearSearch {
    public static int linearSearch(int[] arr, int target) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == target) {
                return i; // Target found — return its index
            }
        }
        return -1; // Target not found in the array
    }

    public static void main(String[] args) {
        int[] arr = {10, 25, 37, 42, 58};
        System.out.println("Index of 37: " + linearSearch(arr, 37)); // 2
        System.out.println("Index of 99: " + linearSearch(arr, 99)); // -1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — in the worst case the target is the last element or not present, so all N elements are checked.
* **Space Complexity:** `O(1)` — only the loop variable is used.

---

## 7. Binary Search

**Binary Search** is a fast search algorithm that works only on **sorted arrays**. It repeatedly halves the search space by comparing the middle element with the target.

> **Analogy:** Searching for a word in a dictionary. You open to the middle page; if the word comes before, you discard the right half — if after, you discard the left half. You repeat until you find it.

### How It Works

1. Set `left = 0`, `right = n - 1`
2. Calculate `mid = (left + right) / 2`
3. If `arr[mid] == target` → found!
4. If `arr[mid] < target` → target is in the right half → `left = mid + 1`
5. If `arr[mid] > target` → target is in the left half → `right = mid - 1`
6. Repeat until `left > right`

**Dry Run for arr = {5, 12, 23, 37, 56, 78, 91}, target = 37:**

| Step | left | right | mid | arr[mid] | Action             |
|------|------|-------|-----|----------|--------------------|
| 1    | 0    | 6     | 3   | 37       | Found! Return 3 ✅ |

**Dry Run for target = 56:**

| Step | left | right | mid | arr[mid] | Action              |
|------|------|-------|-----|----------|---------------------|
| 1    | 0    | 6     | 3   | 37       | 37 < 56 → left = 4  |
| 2    | 4    | 6     | 5   | 78       | 78 > 56 → right = 4 |
| 3    | 4    | 4     | 4   | 56       | Found! Return 4 ✅  |

```java
public class BinarySearch {
    public static int binarySearch(int[] arr, int target) {
        int left = 0;
        int right = arr.length - 1;

        while (left <= right) {
            // Use (left + right) / 2 — safe for small inputs
            // For very large indices, use left + (right - left) / 2 to avoid overflow
            int mid = left + (right - left) / 2;

            if (arr[mid] == target) {
                return mid;           // Target found at index mid
            } else if (arr[mid] < target) {
                left = mid + 1;       // Target is in the right half
            } else {
                right = mid - 1;      // Target is in the left half
            }
        }

        return -1; // Target not found
    }

    public static void main(String[] args) {
        int[] arr = {5, 12, 23, 37, 56, 78, 91};
        System.out.println("Index of 37: " + binarySearch(arr, 37)); // 3
        System.out.println("Index of 56: " + binarySearch(arr, 56)); // 4
        System.out.println("Index of 99: " + binarySearch(arr, 99)); // -1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(log N)` — with each comparison, the search space is cut in half; for N=1,000,000 we need at most ~20 comparisons.
* **Space Complexity:** `O(1)` — only a few pointer variables are used.

---

## Array Operations – Complexity Cheat Sheet

| Operation         | Best Case | Worst Case | Explanation                                     |
|-------------------|-----------|------------|-------------------------------------------------|
| Access (by index) | O(1)      | O(1)       | Direct memory address calculation               |
| Linear Search     | O(1)      | O(N)       | Target may be at end or not present             |
| Binary Search     | O(1)      | O(log N)   | Only on sorted arrays; halves search space      |
| Insertion         | O(1)      | O(N)       | Inserting at end is fast; at start shifts all   |
| Deletion          | O(1)      | O(N)       | Deleting last is fast; at start shifts all      |
| Traversal         | O(N)      | O(N)       | Must visit every element                        |

---

# Practice Problems (Session 2)

---

## 1. Largest Element in Array

[Largest Element in Array (GFG)](https://www.geeksforgeeks.org/problems/largest-element-in-array4009/1)

### Problem Statement

Given an array `arr[]` of size `N`, find the **largest element** present in it.

**Examples:**

| Input               | Output | Reason                          |
|---------------------|--------|---------------------------------|
| [3, 2, 1, 56, 10000, 167] | 10000 | 10000 is the maximum element |
| [1, 2, 3, 4, 5]    | 5      | Last element is the largest     |
| [5, 4, 3, 2, 1]    | 5      | First element is the largest    |
| [7]                 | 7      | Single element is always largest|

---

### Approach 1: Brute Force (Sort and Return Last)

The simplest idea: **sort the array in ascending order** and the largest element will be at the last index. No manual comparison needed.

#### The Math/Logic Behind It

Sorting rearranges elements so that `arr[0] ≤ arr[1] ≤ ... ≤ arr[N-1]`. The last element `arr[N-1]` is always the maximum.

```java
import java.util.Arrays;

public class LargestElement {
    public static int findLargestBrute(int[] arr) {
        Arrays.sort(arr);             // Sort array in ascending order
        return arr[arr.length - 1];  // Last element is the largest
    }

    public static void main(String[] args) {
        int[] arr1 = {3, 2, 1, 56, 10000, 167};
        System.out.println("Largest: " + findLargestBrute(arr1)); // 10000

        int[] arr2 = {5, 4, 3, 2, 1};
        System.out.println("Largest: " + findLargestBrute(arr2)); // 5

        int[] arr3 = {7};
        System.out.println("Largest: " + findLargestBrute(arr3)); // 7
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N log N)` — `Arrays.sort()` uses a variant of merge sort which takes N log N time.
* **Space Complexity:** `O(1)` — sorting is done in-place (no extra array created).

---

### Approach 2: Optimal (Single Pass — Linear Scan)

We don't need to sort the array. Just keep track of the **maximum seen so far** as we scan left to right. Start by assuming the first element is the maximum, then update whenever we find something bigger.

**Dry Run for arr = {3, 2, 56, 10000, 167}:**

| Index | Element | max (before) | Update? | max (after) |
|-------|---------|-------------|---------|-------------|
| 0     | 3       | 3 (init)    | —       | 3           |
| 1     | 2       | 3           | No      | 3           |
| 2     | 56      | 3           | Yes     | 56          |
| 3     | 10000   | 56          | Yes     | 10000       |
| 4     | 167     | 10000       | No      | 10000       |

Final answer: **10000** ✅

```java
public class LargestElement {
    public static int findLargestOptimal(int[] arr) {
        int max = arr[0]; // Assume the first element is the largest

        for (int i = 1; i < arr.length; i++) {
            if (arr[i] > max) {
                max = arr[i]; // Update max whenever a bigger element is found
            }
        }

        return max; // After scanning all elements, max holds the largest value
    }

    public static void main(String[] args) {
        int[] arr1 = {3, 2, 1, 56, 10000, 167};
        System.out.println("Largest: " + findLargestOptimal(arr1)); // 10000

        int[] arr2 = {1, 2, 3, 4, 5};
        System.out.println("Largest: " + findLargestOptimal(arr2)); // 5

        int[] arr3 = {7};
        System.out.println("Largest: " + findLargestOptimal(arr3)); // 7
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we scan each of the N elements exactly once.
* **Space Complexity:** `O(1)` — only the `max` variable is used regardless of array size.

### Teaching Tips
1. **Start with the analogy:** "Imagine you're checking the heights of students in a row. You note the first student's height, then only update your 'current tallest' when you see someone taller." — this is exactly what the algorithm does.
2. **Common Mistake:** Students often initialize `max = 0`. Warn them this fails if all array elements are negative! Always initialize `max = arr[0]`.
3. **Board exercise:** Write `{3, 2, 56, 10000, 167}` and trace the dry-run table on the board, updating `max` step by step. Students should be able to do this themselves on the next problem.

---

## 2. Second Largest Element in Array

[Second Largest (GFG)](https://www.geeksforgeeks.org/problems/second-largest3735/1)

### Problem Statement

Given an array `arr[]` of size `N`, find the **second largest distinct element**. If no second largest exists (e.g., all elements are equal), return `-1`.

**Examples:**

| Input              | Output | Reason                                     |
|--------------------|--------|--------------------------------------------|
| [12, 35, 1, 10, 34, 1] | 34 | Largest is 35; second largest is 34    |
| [10, 10, 10]       | -1     | All elements are same — no second largest  |
| [1, 2]             | 1      | Largest is 2; second largest is 1          |
| [5]                | -1     | Only one element — no second largest       |

---

### Approach 1: Brute Force (Sort and Scan from End)

Sort the array in descending order. The largest is `arr[0]`. Then scan from index 1 to find the first element **different** from `arr[0]` — that is the second largest.

```java
import java.util.Arrays;
import java.util.Collections;

public class SecondLargest {
    public static int findSecondLargestBrute(int[] arr) {
        if (arr.length < 2) return -1; // Edge case: can't have second largest with 1 element

        // Sort in descending order using boxed Integer array
        Integer[] boxed = new Integer[arr.length];
        for (int i = 0; i < arr.length; i++) boxed[i] = arr[i];
        Arrays.sort(boxed, Collections.reverseOrder()); // Sorts largest to smallest

        int largest = boxed[0];

        // Scan from the second element to find a distinct second largest
        for (int i = 1; i < boxed.length; i++) {
            if (boxed[i] != largest) {
                return boxed[i]; // First element different from the largest
            }
        }

        return -1; // All elements are the same
    }

    public static void main(String[] args) {
        int[] arr1 = {12, 35, 1, 10, 34, 1};
        System.out.println("2nd Largest: " + findSecondLargestBrute(arr1)); // 34

        int[] arr2 = {10, 10, 10};
        System.out.println("2nd Largest: " + findSecondLargestBrute(arr2)); // -1

        int[] arr3 = {1, 2};
        System.out.println("2nd Largest: " + findSecondLargestBrute(arr3)); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N log N)` — sorting dominates.
* **Space Complexity:** `O(N)` — we create a boxed `Integer[]` array to use `Collections.reverseOrder()`.

---

### Approach 2: Optimal (Single Pass — Two Variables)

Track **two variables** simultaneously: `largest` and `secondLargest`. In one single scan:
- If current element > `largest` → old `largest` becomes new `secondLargest`, update `largest`
- Else if current element > `secondLargest` AND it's not equal to `largest` → update `secondLargest`

**Dry Run for arr = {12, 35, 1, 10, 34, 1}:**

| Index | Element | largest | secondLargest | Action                           |
|-------|---------|---------|---------------|----------------------------------|
| 0     | 12      | 12      | -1            | Initialize largest = 12          |
| 1     | 35      | 35      | 12            | 35 > 12 → secondLargest=12, largest=35 |
| 2     | 1       | 35      | 12            | 1 < 12 → no update               |
| 3     | 10      | 35      | 12            | 10 < 12 → no update               |
| 4     | 34      | 35      | 34            | 34 > 12 and 34 ≠ 35 → secondLargest=34 |
| 5     | 1       | 35      | 34            | no update                        |

Final answer: **34** ✅

```java
public class SecondLargest {
    public static int findSecondLargestOptimal(int[] arr) {
        if (arr.length < 2) return -1; // Edge case: need at least 2 elements

        int largest = Integer.MIN_VALUE;       // Tracks the overall largest
        int secondLargest = Integer.MIN_VALUE; // Tracks the second largest

        for (int i = 0; i < arr.length; i++) {
            if (arr[i] > largest) {
                secondLargest = largest; // Old largest becomes the new second largest
                largest = arr[i];        // Update the new largest
            } else if (arr[i] > secondLargest && arr[i] != largest) {
                // Element is smaller than largest but bigger than current second largest
                // Also ensure it's distinct from the largest
                secondLargest = arr[i];
            }
        }

        // If secondLargest was never updated, there is no distinct second largest
        return secondLargest == Integer.MIN_VALUE ? -1 : secondLargest;
    }

    public static void main(String[] args) {
        int[] arr1 = {12, 35, 1, 10, 34, 1};
        System.out.println("2nd Largest: " + findSecondLargestOptimal(arr1)); // 34

        int[] arr2 = {10, 10, 10};
        System.out.println("2nd Largest: " + findSecondLargestOptimal(arr2)); // -1

        int[] arr3 = {1, 2};
        System.out.println("2nd Largest: " + findSecondLargestOptimal(arr3)); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we scan through all N elements exactly once.
* **Space Complexity:** `O(1)` — only two integer variables are maintained throughout.

### Teaching Tips
1. **Build on the Largest problem:** Students just learned to track one maximum. Now they're tracking two. Ask "How would you modify the previous solution to also track the runner-up?"
2. **Common Mistake — Not handling duplicates:** If `largest = 35` and the next element is also `35`, students forget to add the `arr[i] != largest` condition. Show with `{35, 35, 10}` — the answer should be 10, not 35.
3. **Board technique:** Draw two labeled boxes `[largest]` and `[second]` and update them live as you walk through the array. Students follow along easily with this visual.

---

## 3. Reverse an Array

[Reverse an Array (GFG)](https://www.geeksforgeeks.org/problems/reverse-an-array/1)

### Problem Statement

Given an array `arr[]`, **reverse** it in-place. The first element becomes last, the second becomes second-to-last, and so on.

**Examples:**

| Input             | Output            | Reason                              |
|-------------------|-------------------|-------------------------------------|
| [1, 2, 3, 4, 5]   | [5, 4, 3, 2, 1]   | All elements reversed               |
| [1, 2, 3, 4]      | [4, 3, 2, 1]      | Even length array reversed          |
| [1]               | [1]               | Single element stays the same       |
| [5, 1]            | [1, 5]            | Two elements swap                   |

---

### Approach 1: Brute Force (Extra Array)

Create a new array of the same size. Fill it by reading the original array from right to left, then copy it back.

```java
public class ReverseArray {
    public static void reverseBrute(int[] arr) {
        int n = arr.length;
        int[] temp = new int[n]; // Extra array to store reversed elements

        // Fill temp from right to left of original
        for (int i = 0; i < n; i++) {
            temp[i] = arr[n - 1 - i]; // Element at index i in reversed = element at n-1-i in original
        }

        // Copy reversed elements back into the original array
        for (int i = 0; i < n; i++) {
            arr[i] = temp[i];
        }
    }

    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        reverseBrute(arr1);
        // Print result
        for (int x : arr1) System.out.print(x + " "); // 5 4 3 2 1
        System.out.println();

        int[] arr2 = {1, 2, 3, 4};
        reverseBrute(arr2);
        for (int x : arr2) System.out.print(x + " "); // 4 3 2 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — two passes of O(N) each: one to fill temp, one to copy back.
* **Space Complexity:** `O(N)` — requires an extra array of size N to store the reversed elements.

---

### Approach 2: Optimal (Two Pointer — In-Place Swap)

Use two pointers: `left` starting from the beginning and `right` starting from the end. Swap `arr[left]` and `arr[right]`, then move them toward each other until they meet in the middle.

#### The Math/Logic Behind It

For an array of size N:
- Position 0 swaps with position N-1
- Position 1 swaps with position N-2
- Position 2 swaps with position N-3
- ...

We only need N/2 swaps to reverse the entire array!

**Dry Run for arr = {1, 2, 3, 4, 5}:**

| Step | left | right | Swap            | Array State         |
|------|------|-------|-----------------|---------------------|
| 1    | 0    | 4     | arr[0] ↔ arr[4] | {5, 2, 3, 4, 1}     |
| 2    | 1    | 3     | arr[1] ↔ arr[3] | {5, 4, 3, 2, 1}     |
| 3    | 2    | 2     | left == right   | Stop (middle reached)|

Final array: **{5, 4, 3, 2, 1}** ✅

```java
public class ReverseArray {
    public static void reverseOptimal(int[] arr) {
        int left = 0;              // Pointer starting from the beginning
        int right = arr.length - 1; // Pointer starting from the end

        while (left < right) {
            // Swap the elements at left and right
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;

            left++;  // Move left pointer forward
            right--; // Move right pointer backward
        }
        // When left >= right, all elements have been swapped — array is reversed
    }

    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        reverseOptimal(arr1);
        for (int x : arr1) System.out.print(x + " "); // 5 4 3 2 1
        System.out.println();

        int[] arr2 = {1, 2, 3, 4};
        reverseOptimal(arr2);
        for (int x : arr2) System.out.print(x + " "); // 4 3 2 1
        System.out.println();

        int[] arr3 = {1};
        reverseOptimal(arr3);
        for (int x : arr3) System.out.print(x + " "); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we perform exactly N/2 swaps, which simplifies to O(N).
* **Space Complexity:** `O(1)` — only a single `temp` variable is used; the reversal is done in-place.

### Teaching Tips
1. **The two-pointer pattern is fundamental:** This is students' first encounter with the two-pointer technique. Emphasize it — it will appear again in problems like Two Sum, Move Zeroes, and others. Frame it as: "One pointer from the left, one from the right, move toward the center."
2. **Visualize with index cards:** Number 5 index cards 1–5, lay them on a desk, and physically swap card 1 with card 5, then card 2 with card 4. Students immediately see that the middle card never needs to swap.
3. **Why only N/2 swaps?** Ask the class: "If we have 100 elements, how many swaps do we need?" Answer: 50. If we did 100 swaps (going all the way through), we'd reverse and then reverse back to the original!

---

## 4. Missing Number in Array

[Missing Number (LeetCode 268)](https://leetcode.com/problems/missing-number/)

### Problem Statement

Given an array `nums` containing **N distinct numbers** taken from the range `[0, N]` (that's N+1 possible numbers), find the **one number** that is missing from the array.

**Examples:**

| Input         | Output | Reason                                  |
|---------------|--------|-----------------------------------------|
| [3, 0, 1]     | 2      | Numbers 0–3 expected; 2 is missing      |
| [0, 1]        | 2      | Numbers 0–2 expected; 2 is missing      |
| [9,6,4,2,3,5,7,0,1] | 8 | Numbers 0–9 expected; 8 is missing  |
| [0]           | 1      | Numbers 0–1 expected; 1 is missing      |

---

### Approach 1: Brute Force (Sort and Check)

Sort the array. Then scan through it — the missing number is the first index where `arr[i] ≠ i`. If no such index is found, the missing number is `N`.

```java
import java.util.Arrays;

public class MissingNumber {
    public static int missingNumberBrute(int[] nums) {
        int n = nums.length;
        Arrays.sort(nums); // Sort the array — O(N log N)

        for (int i = 0; i < n; i++) {
            if (nums[i] != i) {
                return i; // The index i should have the value i, but it doesn't — i is missing
            }
        }

        return n; // All values 0 to N-1 were present; N itself is the missing number
    }

    public static void main(String[] args) {
        int[] arr1 = {3, 0, 1};
        System.out.println("Missing: " + missingNumberBrute(arr1)); // 2

        int[] arr2 = {0, 1};
        System.out.println("Missing: " + missingNumberBrute(arr2)); // 2

        int[] arr3 = {0};
        System.out.println("Missing: " + missingNumberBrute(arr3)); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N log N)` — dominated by the sort operation.
* **Space Complexity:** `O(1)` — sorting in-place, no extra memory.

---

### Approach 2: Optimal (Gauss Sum Formula)

#### The Math/Logic Behind It

The numbers `0` to `N` should all be present. Their **expected sum** is given by the Gauss formula:

> **Expected Sum = N × (N + 1) / 2**

The **actual sum** is the sum of all elements currently in the array. The missing number is simply:

> **Missing = Expected Sum − Actual Sum**

**Example for arr = {3, 0, 1}, N = 3:**
- Expected Sum = 3 × 4 / 2 = **6**
- Actual Sum = 3 + 0 + 1 = **4**
- Missing = 6 − 4 = **2** ✅

| Step         | Calculation             | Value |
|--------------|-------------------------|-------|
| Expected Sum | N × (N+1) / 2 = 3×4/2  | 6     |
| Actual Sum   | 3 + 0 + 1               | 4     |
| Missing      | 6 − 4                   | **2** |

```java
public class MissingNumber {
    public static int missingNumberOptimal(int[] nums) {
        int n = nums.length;

        // Calculate the expected sum using the Gauss formula
        // Use long to avoid overflow for large N values
        long expectedSum = (long) n * (n + 1) / 2;

        // Calculate the actual sum of the array
        long actualSum = 0;
        for (int num : nums) {
            actualSum += num; // Add each element to the actual sum
        }

        // The difference is the missing number
        return (int) (expectedSum - actualSum);
    }

    public static void main(String[] args) {
        int[] arr1 = {3, 0, 1};
        System.out.println("Missing: " + missingNumberOptimal(arr1)); // 2

        int[] arr2 = {9, 6, 4, 2, 3, 5, 7, 0, 1};
        System.out.println("Missing: " + missingNumberOptimal(arr2)); // 8

        int[] arr3 = {0};
        System.out.println("Missing: " + missingNumberOptimal(arr3)); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — one pass to calculate the actual sum; the formula is O(1).
* **Space Complexity:** `O(1)` — only two variables (`expectedSum` and `actualSum`) are used.

### Teaching Tips
1. **Introduce the Gauss formula with a story:** "Carl Friedrich Gauss, at age 10, was asked to sum 1 to 100. He noticed that 1+100=101, 2+99=101, ... 50 pairs all equal 101. So the sum is 50×101 = 5050. We use his same formula here: N×(N+1)/2."
2. **The insight is about what's missing:** "The array is almost complete — just one number is absent. The cleanest way to find that absence is to compare what *should be there* versus what *is there*."
3. **Extend the thinking:** After this problem, ask students: "What if two numbers were missing instead of one?" This leads them toward XOR or frequency-count approaches — a great preview of future problems.

---

# Class 2 Homework

### Easy (Mandatory)
1. [Check if Array is Sorted (GFG)](https://www.geeksforgeeks.org/problems/check-if-an-array-is-sorted0701/1) — Check whether the given array is sorted in non-decreasing order
2. [Move Zeros to End (GFG)](https://www.geeksforgeeks.org/problems/move-all-zeroes-to-end-of-array0751/1) — Push all zeros to the end while maintaining order of non-zero elements
3. [Single Number (LeetCode)](https://leetcode.com/problems/single-number/) — Every element appears twice except one; find that element

### Medium (Recommended)
1. [Rotate Array (LeetCode)](https://leetcode.com/problems/rotate-array/) — Rotate an array to the right by `k` steps
2. [Maximum Subarray – Kadane's Algorithm (LeetCode)](https://leetcode.com/problems/maximum-subarray/) — Find the contiguous subarray with the largest sum
3. [Count of Smaller Numbers After Self (Hint: try brute force first) (LeetCode)](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)

### Challenge Problem (Optional)
[Find the Duplicate Number (LeetCode)](https://leetcode.com/problems/find-the-duplicate-number/) — Given an array of N+1 integers where each value is in range [1, N], find the duplicate number using O(1) extra space
