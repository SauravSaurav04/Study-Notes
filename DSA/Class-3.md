# Week 2 – Class 3: Arrays – Advanced Problem Solving

---

# Session 3: Arrays – Advanced Problem Solving

## 1. Two Pointer Technique

### What is the Two Pointer Technique?

The **Two Pointer technique** is a strategy where we use **two index variables** (pointers) that move through the array — often from opposite ends or at different speeds — to solve problems **without nested loops**.

> **Real-World Analogy:** Imagine two people walking toward each other along a hallway. One starts at the left door and one starts at the right door. They move toward each other until they meet. This is exactly how the two-pointer pattern works for problems like reversing an array or finding a pair with a target sum.

### When to Use Two Pointers

| Scenario | Description |
|----------|-------------|
| Sorted Array + Pair Sum | Move pointers inward based on comparison |
| In-Place Rearrangement | One fast pointer, one slow pointer |
| Array Reversal | Left and right converging |
| Remove/Filter Elements | Slow pointer marks "valid" position, fast scans |

### Core Pattern

```java
public class TwoPointerDemo {
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9};  // Sorted array
        int target = 10;

        int left = 0;                  // Pointer starting from the left end
        int right = arr.length - 1;   // Pointer starting from the right end

        while (left < right) {
            int sum = arr[left] + arr[right];

            if (sum == target) {
                System.out.println("Pair found: " + arr[left] + " + " + arr[right]); // 3 + 7
                break;
            } else if (sum < target) {
                left++;   // Sum too small — move left pointer rightward to increase sum
            } else {
                right--;  // Sum too large — move right pointer leftward to decrease sum
            }
        }
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each pointer moves at most N steps in total.
* **Space Complexity:** `O(1)` — only two integer pointer variables used.

---

## 2. Prefix Sum Technique

### What is Prefix Sum?

A **Prefix Sum array** is a precomputed array where each element at index `i` stores the **sum of all elements from index 0 to i** in the original array.

> **Real-World Analogy:** Think of a cashier's running total. After each item is scanned, the total grows. If you want the cost of items 3 through 7, you subtract the total before item 3 from the total after item 7. That's exactly what prefix sums allow.

### Building a Prefix Sum Array

For `arr = {2, 4, 6, 8, 10}`:

| Index | arr[i] | prefix[i] = prefix[i-1] + arr[i] |
|-------|--------|-----------------------------------|
| 0     | 2      | 2                                 |
| 1     | 4      | 6  (2+4)                          |
| 2     | 6      | 12 (6+6)                          |
| 3     | 8      | 20 (12+8)                         |
| 4     | 10     | 30 (20+10)                        |

### Key Formula

> **Sum of subarray from index L to R = prefix[R] − prefix[L−1]**
>
> (If L == 0, the answer is simply prefix[R])

```java
public class PrefixSumDemo {
    public static void main(String[] args) {
        int[] arr = {2, 4, 6, 8, 10};
        int n = arr.length;

        // Step 1: Build the prefix sum array
        int[] prefix = new int[n];
        prefix[0] = arr[0]; // First element is just itself

        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i - 1] + arr[i]; // Current prefix = previous prefix + current element
        }

        // Step 2: Answer range sum queries in O(1)
        // Query: Sum from index 1 to 3 (values 4, 6, 8 = 18)
        int L = 1, R = 3;
        int rangeSum = (L == 0) ? prefix[R] : prefix[R] - prefix[L - 1];
        System.out.println("Sum from index " + L + " to " + R + " = " + rangeSum); // 18

        // Query: Sum from index 0 to 4 (all elements = 30)
        System.out.println("Total sum = " + prefix[n - 1]); // 30
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` to build the prefix array, then `O(1)` per query — a huge win over `O(N)` per query without it.
* **Space Complexity:** `O(N)` — for the prefix array of size N.

---

## 3. Sliding Window Technique

### What is Sliding Window?

The **Sliding Window technique** maintains a **window (a subarray of fixed or variable size)** that slides across the array from left to right. Instead of recalculating results from scratch for each window, we **add the new element entering the window** and **subtract the element leaving the window**.

> **Real-World Analogy:** Imagine you're looking through a train window. As the train moves, new scenery enters on the right and old scenery disappears on the left. You don't rebuild your entire view from scratch — you just update it incrementally.

### Fixed-Size Window (size = k)

```java
public class SlidingWindowDemo {
    // Find the maximum sum of any subarray of size k
    public static int maxSumSubarray(int[] arr, int k) {
        int n = arr.length;

        // Step 1: Compute the sum of the first window (index 0 to k-1)
        int windowSum = 0;
        for (int i = 0; i < k; i++) {
            windowSum += arr[i];
        }

        int maxSum = windowSum; // Initialize max with the first window's sum

        // Step 2: Slide the window one step at a time from left to right
        for (int i = k; i < n; i++) {
            windowSum += arr[i];         // Add the new element entering from the right
            windowSum -= arr[i - k];     // Remove the element leaving from the left
            maxSum = Math.max(maxSum, windowSum); // Update max if this window is better
        }

        return maxSum;
    }

    public static void main(String[] args) {
        int[] arr = {2, 1, 5, 1, 3, 2};
        int k = 3;
        System.out.println("Max sum (k=3): " + maxSumSubarray(arr, k)); // 9 → {5,1,3}

        int[] arr2 = {1, 4, 2, 9, 7, 3, 8};
        System.out.println("Max sum (k=3): " + maxSumSubarray(arr2, 3)); // 19 → {9,7,3}? No: {9,7,3}=19
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the window slides exactly N-k times, and each slide is O(1).
* **Space Complexity:** `O(1)` — only a few integer variables maintained.

---

## Technique Summary Cheat Sheet

| Technique | Best For | Time Complexity |
|-----------|----------|-----------------|
| Two Pointer | Pair sum, remove elements in-place, sorted arrays | O(N) |
| Prefix Sum | Range queries, subarray sums | O(N) build + O(1) query |
| Sliding Window | Fixed/variable-size subarray problems | O(N) |

---

# Practice Problems (Session 3)

---

## 1. Two Sum

[Two Sum (LeetCode 1)](https://leetcode.com/problems/two-sum/)

### Problem Statement

Given an array of integers `nums` and an integer `target`, return the **indices** of the two numbers such that they **add up to `target`**. You may assume that each input has **exactly one solution**, and you may not use the same element twice.

**Examples:**

| Input | Target | Output | Reason |
|-------|--------|--------|--------|
| [2, 7, 11, 15] | 9  | [0, 1] | nums[0] + nums[1] = 2 + 7 = 9 |
| [3, 2, 4]      | 6  | [1, 2] | nums[1] + nums[2] = 2 + 4 = 6 |
| [3, 3]         | 6  | [0, 1] | nums[0] + nums[1] = 3 + 3 = 6 |
| [1, 5, 3, 7]   | 8  | [1, 3] | nums[1] + nums[3] = 5 + 3 = 8 |

---

### Approach 1: Brute Force (Check Every Pair)

The simplest approach is to **check every possible pair** of elements. For each element at index `i`, scan all elements after it (`j > i`) and check if `nums[i] + nums[j] == target`. This is the most straightforward idea: try all combinations.

#### The Math/Logic Behind It

If the array has N elements, the number of possible pairs is `N × (N-1) / 2`. For N=1000, that's ~500,000 pairs. This grows quadratically — not efficient, but simple and always correct.

**Dry Run for nums = {2, 7, 11, 15}, target = 9:**

| i | j | nums[i] | nums[j] | Sum | Match? |
|---|---|---------|---------|-----|--------|
| 0 | 1 | 2       | 7       | 9   | ✅ Yes → return [0,1] |

```java
public class TwoSum {
    public static int[] twoSumBrute(int[] nums, int target) {
        int n = nums.length;

        // Outer loop: pick each element as the first number
        for (int i = 0; i < n; i++) {
            // Inner loop: check all elements after i as the second number
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j}; // Found the pair — return their indices
                }
            }
        }

        return new int[]{-1, -1}; // No pair found (problem guarantees one exists, so this won't be reached)
    }

    public static void main(String[] args) {
        int[] nums1 = {2, 7, 11, 15};
        int[] result1 = twoSumBrute(nums1, 9);
        System.out.println("Indices: [" + result1[0] + ", " + result1[1] + "]"); // [0, 1]

        int[] nums2 = {3, 2, 4};
        int[] result2 = twoSumBrute(nums2, 6);
        System.out.println("Indices: [" + result2[0] + ", " + result2[1] + "]"); // [1, 2]

        int[] nums3 = {3, 3};
        int[] result3 = twoSumBrute(nums3, 6);
        System.out.println("Indices: [" + result3[0] + ", " + result3[1] + "]"); // [0, 1]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N²)` — two nested loops, each potentially running N times, resulting in N×(N-1)/2 pair checks.
* **Space Complexity:** `O(1)` — no extra data structures used, just two loop variables.

---

### Approach 2: Optimal (HashMap — One Pass)

Instead of checking every pair, we can use a **HashMap** to remember numbers we've already seen. For each element `nums[i]`, we calculate its **complement** = `target - nums[i]`. If the complement is already in the HashMap, we've found our pair immediately!

#### The Math/Logic Behind It

For the pair `(a, b)` where `a + b = target`:
- When we process `a`, we store `a → index` in the map.
- When we later process `b`, we look up `target - b = a` in the map.
- If found, return `[map.get(a), current index]`.

**Dry Run for nums = {2, 7, 11, 15}, target = 9:**

| i | nums[i] | complement (9-nums[i]) | Map before lookup | Found? | Map after |
|---|---------|------------------------|-------------------|--------|-----------|
| 0 | 2       | 7                      | {}                | No     | {2→0}     |
| 1 | 7       | 2                      | {2→0}             | ✅ Yes → return [0,1] | — |

```java
import java.util.HashMap;

public class TwoSum {
    public static int[] twoSumOptimal(int[] nums, int target) {
        // Map stores: { number → its index } for all elements seen so far
        HashMap<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i]; // The number we need to pair with nums[i]

            if (map.containsKey(complement)) {
                // The complement was already seen — we found the pair!
                return new int[]{map.get(complement), i};
            }

            // Complement not yet seen — add current number to the map for future lookups
            map.put(nums[i], i);
        }

        return new int[]{-1, -1}; // Should never reach here given problem constraints
    }

    public static void main(String[] args) {
        int[] nums1 = {2, 7, 11, 15};
        int[] result1 = twoSumOptimal(nums1, 9);
        System.out.println("Indices: [" + result1[0] + ", " + result1[1] + "]"); // [0, 1]

        int[] nums2 = {3, 2, 4};
        int[] result2 = twoSumOptimal(nums2, 6);
        System.out.println("Indices: [" + result2[0] + ", " + result2[1] + "]"); // [1, 2]

        int[] nums3 = {3, 3};
        int[] result3 = twoSumOptimal(nums3, 6);
        System.out.println("Indices: [" + result3[0] + ", " + result3[1] + "]"); // [0, 1]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we traverse the array exactly once; each HashMap lookup and insertion is O(1) on average.
* **Space Complexity:** `O(N)` — in the worst case, we store all N elements in the HashMap before finding the answer.

### Teaching Tips
1. **Start with the brute force story:** "Imagine you walk into a party and want to find someone whose height, added to yours, equals exactly 180 cm. You could measure yourself against every person — that's brute force. Or you could shout 'I need someone 70 cm tall!' — that's the HashMap approach."
2. **Clarify the complement insight:** Write `a + b = target` on the board. Rearrange to `b = target - a`. "For every number `a` I see, I need `b`. I check if `b` is already in the map." This is the core insight.
3. **Common mistake — using the same element twice:** If `nums = {6}` and `target = 12`, students might think 6+6=12. Stress that `j > i` in the brute force, and in the HashMap approach we only add the current element AFTER checking — preventing index reuse.

---

## 2. Move Zeroes

[Move Zeroes (LeetCode 283)](https://leetcode.com/problems/move-zeroes/)

### Problem Statement

Given an integer array `nums`, move all **zeroes** to the **end** of it while maintaining the **relative order** of the non-zero elements. This must be done **in-place** without making a copy of the array.

**Examples:**

| Input              | Output             | Reason |
|--------------------|--------------------|--------|
| [0, 1, 0, 3, 12]  | [1, 3, 12, 0, 0]  | Non-zeros maintain order; zeros pushed to end |
| [0]               | [0]               | Single zero stays |
| [1, 2, 3]         | [1, 2, 3]         | No zeros — unchanged |
| [0, 0, 1]         | [1, 0, 0]         | 1 moves to front; two zeros at end |

---

### Approach 1: Brute Force (Extra Array)

Collect all non-zero elements in a temporary list, copy them back to the start of the original array, then fill the remaining positions with zeros.

**Dry Run for nums = {0, 1, 0, 3, 12}:**

| Step | Action | Array State |
|------|--------|-------------|
| Collect non-zeros | Pick 1, 3, 12 | temp = {1, 3, 12} |
| Copy back | Fill indexes 0,1,2 | {1, 3, 12, 3, 12} |
| Fill zeros | Fill indexes 3,4 | {1, 3, 12, 0, 0} ✅ |

```java
public class MoveZeroes {
    public static void moveZeroesBrute(int[] nums) {
        int n = nums.length;

        // Step 1: Collect all non-zero elements in order
        int[] temp = new int[n];
        int pos = 0; // Position to place the next non-zero element in temp

        for (int i = 0; i < n; i++) {
            if (nums[i] != 0) {
                temp[pos] = nums[i]; // Copy non-zero element to temp
                pos++;
            }
        }
        // temp now holds: {1, 3, 12, 0, 0} (Java fills rest with 0 by default)

        // Step 2: Copy temp back to nums
        for (int i = 0; i < n; i++) {
            nums[i] = temp[i];
        }
    }

    public static void main(String[] args) {
        int[] nums1 = {0, 1, 0, 3, 12};
        moveZeroesBrute(nums1);
        for (int x : nums1) System.out.print(x + " "); // 1 3 12 0 0
        System.out.println();

        int[] nums2 = {0, 0, 1};
        moveZeroesBrute(nums2);
        for (int x : nums2) System.out.print(x + " "); // 1 0 0
        System.out.println();

        int[] nums3 = {1, 2, 3};
        moveZeroesBrute(nums3);
        for (int x : nums3) System.out.print(x + " "); // 1 2 3
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — two separate O(N) passes: one to collect non-zeros, one to copy back.
* **Space Complexity:** `O(N)` — an extra `temp` array of size N is created.

---

### Approach 2: Optimal (Two Pointer — In-Place)

Use a **slow pointer** (`insertPos`) that tracks where the next non-zero element should be placed. The **fast pointer** (`i`) scans through the array. Whenever `nums[i]` is non-zero, we place it at `insertPos` and advance both pointers. After all non-zeros are placed, fill the remaining positions with zeros.

#### The Math/Logic Behind It

Think of `insertPos` as a "write head" — it only advances when we write a non-zero value. The fast pointer scans everything. The gap between the two pointers represents the zeros that have already been "processed" and pushed behind.

**Dry Run for nums = {0, 1, 0, 3, 12}:**

| i (fast) | nums[i] | insertPos (slow) | Action | Array State |
|----------|---------|------------------|--------|-------------|
| 0        | 0       | 0                | Skip   | {0,1,0,3,12} |
| 1        | 1       | 0 → 1            | Write 1 at [0] | {1,1,0,3,12} |
| 2        | 0       | 1                | Skip   | {1,1,0,3,12} |
| 3        | 3       | 1 → 2            | Write 3 at [1] | {1,3,0,3,12} |
| 4        | 12      | 2 → 3            | Write 12 at [2] | {1,3,12,3,12} |
| Fill 0s  | —       | from 3 to end    | Write 0 | {1,3,12,0,0} ✅ |

```java
public class MoveZeroes {
    public static void moveZeroesOptimal(int[] nums) {
        int n = nums.length;

        // insertPos is the slow pointer — marks where the next non-zero should go
        int insertPos = 0;

        // Fast pointer: scan through the entire array
        for (int i = 0; i < n; i++) {
            if (nums[i] != 0) {
                nums[insertPos] = nums[i]; // Write this non-zero element at insertPos
                insertPos++;               // Advance slow pointer
            }
        }

        // All non-zero elements are now at the front (indices 0 to insertPos-1)
        // Fill the remaining positions with zeros
        while (insertPos < n) {
            nums[insertPos] = 0;
            insertPos++;
        }
    }

    public static void main(String[] args) {
        int[] nums1 = {0, 1, 0, 3, 12};
        moveZeroesOptimal(nums1);
        for (int x : nums1) System.out.print(x + " "); // 1 3 12 0 0
        System.out.println();

        int[] nums2 = {0};
        moveZeroesOptimal(nums2);
        for (int x : nums2) System.out.print(x + " "); // 0
        System.out.println();

        int[] nums3 = {1, 2, 3};
        moveZeroesOptimal(nums3);
        for (int x : nums3) System.out.print(x + " "); // 1 2 3
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — one pass to place all non-zeros + one pass (at most) to fill zeros; total work is O(N).
* **Space Complexity:** `O(1)` — everything is done in-place using only the `insertPos` variable.

### Teaching Tips
1. **Introduce slow/fast pointers:** Explain that `insertPos` is the "clean" zone boundary — everything before it is guaranteed non-zero. This is students' first encounter with the slow/fast pointer variant. Emphasize it as a very common interview pattern.
2. **Visualize on the board:** Draw the array as boxes. Use two different colored markers for the slow and fast pointers. Move them step by step. Students should see the non-zero elements "compact" to the front.
3. **Why not just swap?** Some students will suggest swapping 0s with non-zeros. That works too (even preserves order), but the two-step approach (write then fill) is cleaner to explain and easier to get right.

---

## 3. Remove Duplicates from Sorted Array

[Remove Duplicates from Sorted Array (LeetCode 26)](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

### Problem Statement

Given a **sorted** integer array `nums`, remove the **duplicates in-place** so that each unique element appears only once. The **relative order** must be kept. Return the count `k` of unique elements. The first `k` elements of `nums` must hold the unique values (the rest don't matter).

**Examples:**

| Input           | Output (k) | Modified Array (first k elements) | Reason |
|-----------------|-----------|-----------------------------------|--------|
| [1, 1, 2]       | 2         | [1, 2, ...]                       | Two unique elements: 1, 2 |
| [0,0,1,1,1,2,2,3,3,4] | 5 | [0, 1, 2, 3, 4, ...]           | Five unique elements |
| [1, 2, 3]       | 3         | [1, 2, 3]                         | Already unique |
| [1, 1, 1, 1]   | 1         | [1, ...]                           | Only one unique element |

---

### Approach 1: Brute Force (Using a Set)

Use a **LinkedHashSet** (which preserves insertion order and rejects duplicates) to collect unique elements, then copy them back into the array.

```java
import java.util.LinkedHashSet;

public class RemoveDuplicates {
    public static int removeDuplicatesBrute(int[] nums) {
        // LinkedHashSet: maintains insertion order AND ignores duplicate insertions
        LinkedHashSet<Integer> set = new LinkedHashSet<>();

        for (int num : nums) {
            set.add(num); // Duplicate numbers are silently ignored by Set
        }

        // Copy unique elements back into the original array
        int index = 0;
        for (int num : set) {
            nums[index] = num;
            index++;
        }

        return set.size(); // Return the count of unique elements
    }

    public static void main(String[] args) {
        int[] nums1 = {1, 1, 2};
        int k1 = removeDuplicatesBrute(nums1);
        System.out.print("k = " + k1 + ", Array: ");
        for (int i = 0; i < k1; i++) System.out.print(nums1[i] + " "); // k=2, 1 2
        System.out.println();

        int[] nums2 = {0, 0, 1, 1, 1, 2, 2, 3, 3, 4};
        int k2 = removeDuplicatesBrute(nums2);
        System.out.print("k = " + k2 + ", Array: ");
        for (int i = 0; i < k2; i++) System.out.print(nums2[i] + " "); // k=5, 0 1 2 3 4
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — one pass to add to the set, one pass to copy back; both are O(N).
* **Space Complexity:** `O(N)` — the LinkedHashSet stores up to N unique elements.

---

### Approach 2: Optimal (Two Pointer — In-Place on Sorted Array)

Since the array is **sorted**, all duplicates are **adjacent**. We use a slow pointer `i` to mark the position of the last confirmed unique element. The fast pointer `j` scans forward. Whenever `nums[j]` differs from `nums[i]`, it's a new unique element — advance `i` and copy it there.

#### The Math/Logic Behind It

In a sorted array, equal elements cluster together. So we only need to compare each element with its immediate "unique predecessor." The slow pointer `i` always points to the last unique element written.

**Dry Run for nums = {0, 0, 1, 1, 1, 2, 2, 3, 3, 4}:**

| j  | nums[j] | nums[i] | Different? | Action | i |
|----|---------|---------|------------|--------|---|
| 0  | 0       | —       | Init i=0   | —      | 0 |
| 1  | 0       | 0       | No         | Skip   | 0 |
| 2  | 1       | 0       | ✅ Yes     | i++, nums[1]=1 | 1 |
| 3  | 1       | 1       | No         | Skip   | 1 |
| 4  | 1       | 1       | No         | Skip   | 1 |
| 5  | 2       | 1       | ✅ Yes     | i++, nums[2]=2 | 2 |
| 6  | 2       | 2       | No         | Skip   | 2 |
| 7  | 3       | 2       | ✅ Yes     | i++, nums[3]=3 | 3 |
| 8  | 3       | 3       | No         | Skip   | 3 |
| 9  | 4       | 3       | ✅ Yes     | i++, nums[4]=4 | 4 |

Return `i + 1 = 5`. Array: `{0, 1, 2, 3, 4, ...}` ✅

```java
public class RemoveDuplicates {
    public static int removeDuplicatesOptimal(int[] nums) {
        if (nums.length == 0) return 0;

        // i is the slow pointer — points to the last confirmed unique element
        int i = 0;

        // j is the fast pointer — scans every element starting from the second
        for (int j = 1; j < nums.length; j++) {
            if (nums[j] != nums[i]) {
                // nums[j] is a new unique element (different from the last unique we recorded)
                i++;              // Move slow pointer forward to the next available position
                nums[i] = nums[j]; // Write the new unique element at that position
            }
            // If nums[j] == nums[i], it's a duplicate — skip it (do nothing)
        }

        return i + 1; // The count of unique elements is the slow pointer's index + 1
    }

    public static void main(String[] args) {
        int[] nums1 = {1, 1, 2};
        int k1 = removeDuplicatesOptimal(nums1);
        System.out.print("k = " + k1 + ", Array: ");
        for (int i = 0; i < k1; i++) System.out.print(nums1[i] + " "); // k=2, 1 2
        System.out.println();

        int[] nums2 = {0, 0, 1, 1, 1, 2, 2, 3, 3, 4};
        int k2 = removeDuplicatesOptimal(nums2);
        System.out.print("k = " + k2 + ", Array: ");
        for (int i = 0; i < k2; i++) System.out.print(nums2[i] + " "); // k=5, 0 1 2 3 4
        System.out.println();

        int[] nums3 = {1, 1, 1, 1};
        int k3 = removeDuplicatesOptimal(nums3);
        System.out.print("k = " + k3 + ", Array: ");
        for (int i = 0; i < k3; i++) System.out.print(nums3[i] + " "); // k=1, 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the fast pointer `j` traverses every element exactly once; the slow pointer only advances when a unique element is found.
* **Space Complexity:** `O(1)` — the removal is done in-place with only two pointer variables; no extra array needed.

### Teaching Tips
1. **Why does sorted order matter?** Ask students: "If the array were unsorted and had duplicates, could we still use this approach?" Answer: No! Because duplicates might not be adjacent. The sorted property is the key enabler here. This trains critical thinking about input constraints.
2. **Visualize the two zones:** Draw a line in the array between index `i` and `i+1`. Everything to the left is the "clean zone" (unique, confirmed). Everything to the right is "unexplored". The fast pointer explores; the slow pointer marks the clean boundary.
3. **Return `i + 1`, not `i`:** A very common off-by-one mistake. After the loop, `i` is the *index* of the last unique element, so the *count* is `i + 1`. Have students trace through a small array and verify.

---

## 4. Best Time to Buy and Sell Stock

[Best Time to Buy and Sell Stock (LeetCode 121)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### Problem Statement

You are given an array `prices` where `prices[i]` is the **price of a stock on day `i`**. You want to **maximize your profit** by choosing a single day to **buy** and a different **later** day to **sell**. Return the **maximum profit** possible. If no profit can be made, return `0`.

**Key Constraint:** You must buy **before** you sell (you cannot buy at day 5 and sell at day 3).

**Examples:**

| Input               | Output | Reason |
|---------------------|--------|--------|
| [7, 1, 5, 3, 6, 4] | 5      | Buy at day 1 (price=1), sell at day 4 (price=6), profit=5 |
| [7, 6, 4, 3, 1]    | 0      | Prices only decrease — no profit possible |
| [2, 4, 1, 7]       | 6      | Buy at day 2 (price=1), sell at day 3 (price=7), profit=6 |
| [1, 2]             | 1      | Buy at day 0 (price=1), sell at day 1 (price=2) |

---

### Approach 1: Brute Force (Try All Pairs of Buy/Sell Days)

Check every pair of days `(i, j)` where `i < j`. Compute `prices[j] - prices[i]` and track the maximum profit found across all pairs.

**Dry Run for prices = {7, 1, 5, 3, 6, 4}:**

| Buy Day (i) | Sell Day (j) | Profit | Max Profit |
|-------------|--------------|--------|------------|
| 0 (price=7) | 1 (price=1)  | -6     | 0          |
| 0 (price=7) | 2 (price=5)  | -2     | 0          |
| 1 (price=1) | 2 (price=5)  | 4      | 4          |
| 1 (price=1) | 4 (price=6)  | 5      | **5** ✅   |
| ...         | ...          | ...    | 5          |

```java
public class StockBuySell {
    public static int maxProfitBrute(int[] prices) {
        int n = prices.length;
        int maxProfit = 0; // Start with 0 — if no profitable trade exists, we return 0

        // Outer loop: pick every possible buy day
        for (int i = 0; i < n - 1; i++) {
            // Inner loop: pick every possible sell day AFTER the buy day
            for (int j = i + 1; j < n; j++) {
                int profit = prices[j] - prices[i]; // Profit if we buy at i and sell at j
                if (profit > maxProfit) {
                    maxProfit = profit; // Update max if this is the best profit so far
                }
            }
        }

        return maxProfit;
    }

    public static void main(String[] args) {
        int[] prices1 = {7, 1, 5, 3, 6, 4};
        System.out.println("Max Profit: " + maxProfitBrute(prices1)); // 5

        int[] prices2 = {7, 6, 4, 3, 1};
        System.out.println("Max Profit: " + maxProfitBrute(prices2)); // 0

        int[] prices3 = {2, 4, 1, 7};
        System.out.println("Max Profit: " + maxProfitBrute(prices3)); // 6
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N²)` — for each of the N buy days, we check up to N-1 sell days; total comparisons ≈ N×(N-1)/2.
* **Space Complexity:** `O(1)` — only a `maxProfit` variable is used, no extra arrays.

---

### Approach 2: Optimal (One Pass — Track Minimum Price)

#### The Math/Logic Behind It

Key insight: **To maximize profit, we want to buy at the lowest price seen so far and sell at the current price.**

As we scan left to right, we maintain:
- `minPrice` → the **cheapest buy price** seen so far (updated greedily)
- `maxProfit` → the **best profit** achieved so far

At each day `i`, we ask:
1. Is `prices[i]` the new minimum? If yes, update `minPrice`.
2. Is `prices[i] - minPrice` the new maximum profit? If yes, update `maxProfit`.

**Dry Run for prices = {7, 1, 5, 3, 6, 4}:**

| Day | Price | minPrice | Profit (Price - minPrice) | maxProfit |
|-----|-------|----------|---------------------------|-----------|
| 0   | 7     | 7        | 0                         | 0         |
| 1   | 1     | 1        | 0                         | 0         |
| 2   | 5     | 1        | 4                         | 4         |
| 3   | 3     | 1        | 2                         | 4         |
| 4   | 6     | 1        | **5** ✅                  | 5         |
| 5   | 4     | 1        | 3                         | 5         |

Final answer: **5** ✅

```java
public class StockBuySell {
    public static int maxProfitOptimal(int[] prices) {
        int minPrice = Integer.MAX_VALUE; // Initialize to the largest possible value
        int maxProfit = 0;               // Initialize profit to 0 (no trade = 0 profit)

        for (int i = 0; i < prices.length; i++) {
            if (prices[i] < minPrice) {
                // Found a new cheapest day to buy — update minPrice
                minPrice = prices[i];
            } else if (prices[i] - minPrice > maxProfit) {
                // Selling today would beat our current best profit — update maxProfit
                maxProfit = prices[i] - minPrice;
            }
        }

        return maxProfit; // Return the best profit achievable (0 if prices always drop)
    }

    public static void main(String[] args) {
        int[] prices1 = {7, 1, 5, 3, 6, 4};
        System.out.println("Max Profit: " + maxProfitOptimal(prices1)); // 5

        int[] prices2 = {7, 6, 4, 3, 1};
        System.out.println("Max Profit: " + maxProfitOptimal(prices2)); // 0

        int[] prices3 = {2, 4, 1, 7};
        System.out.println("Max Profit: " + maxProfitOptimal(prices3)); // 6

        int[] prices4 = {1, 2};
        System.out.println("Max Profit: " + maxProfitOptimal(prices4)); // 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — the array is traversed exactly once; each element is processed in O(1).
* **Space Complexity:** `O(1)` — only two variables (`minPrice` and `maxProfit`) are maintained regardless of array size.

### Teaching Tips
1. **Use the stock market story:** "Imagine you can see an entire week's stock prices in advance. You want to pick the best single day to buy and the best single day to sell AFTER that. How would YOU do it?" Let students describe the brute force. Then guide them to the insight: "Can we figure out the answer in just one pass if we always remember the cheapest price seen so far?"
2. **Draw the price graph:** Plot the prices as a line graph on the board. Point out visually where the minimum is (day 1, price=1) and where the maximum subsequent price is (day 4, price=6). The optimal buy and sell points are visually obvious on the graph.
3. **Common mistake — selling before buying:** Some students forget the constraint `i < j` and try to sell at the global minimum. Emphasize: "We can only sell AFTER we buy. So we always compute profit as `current price - minimum price seen SO FAR (before today)`."

---

# Class 3 Homework

### Easy (Mandatory)
1. [Running Sum of 1D Array (LeetCode 1480)](https://leetcode.com/problems/running-sum-of-1d-array/) — Build a prefix sum array where each element is the sum of all elements up to that index
2. [Find Pivot Index (LeetCode 724)](https://leetcode.com/problems/find-pivot-index/) — Find the leftmost index where the sum of elements to the left equals the sum to the right
3. [Check If Array Is Sorted and Rotated (LeetCode 1752)](https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/) — Determine if a sorted array was rotated some number of positions

### Medium (Recommended)
1. [Maximum Average Subarray I (LeetCode 643)](https://leetcode.com/problems/maximum-average-subarray-i/) — Use Sliding Window to find the maximum average of any contiguous subarray of length k
2. [Subarray Sum Equals K (LeetCode 560)](https://leetcode.com/problems/subarray-sum-equals-k/) — Use Prefix Sum + HashMap to count subarrays that sum to exactly k
3. [Container With Most Water (LeetCode 11)](https://leetcode.com/problems/container-with-most-water/) — Use Two Pointers on an array of heights to find the container that holds the most water

### Challenge Problem (Optional)
[Trapping Rain Water (LeetCode 42)](https://leetcode.com/problems/trapping-rain-water/) — Given an elevation map (array of bar heights), compute how much rainwater can be trapped between bars — combines Prefix Sum thinking with Two Pointer optimization
