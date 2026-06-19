# [Week 3] – Class 5: Sorting Algorithms

> **Session:** Class 5 | **Week:** 3 | **Topic:** Sorting Algorithms
> **Algorithms Covered:** Bubble Sort · Selection Sort · Insertion Sort · Merge Sort (Intro)

---

# Theory

## What is Sorting?

**Sorting** is the process of arranging elements of a collection in a specific order — usually **ascending** or **descending**.

> 💡 **Why does sorting matter?**
> - **Binary Search** only works on sorted data.
> - Many interview problems (Two Sum, Merge Intervals, etc.) become trivially easier on sorted arrays.
> - Used everywhere: databases, file systems, ranking systems, and OS schedulers.

---

## 1. Bubble Sort

### Definition

**Bubble Sort** repeatedly compares **adjacent elements** and **swaps** them if they are in the wrong order. After each full pass, the **largest unsorted element "bubbles up"** to its correct position at the end of the array.

### Real-World Analogy

Imagine a row of students arranged by height. A teacher walks down the line and swaps any two adjacent students who are out of order. After one full walk, the tallest student is at the end. The teacher keeps walking until no more swaps are needed.

### Algorithm Steps

1. Loop from `i = 0` to `n - 2` (outer pass — we need at most n-1 passes)
2. In each pass, loop `j` from `0` to `n - 2 - i`
3. If `arr[j] > arr[j+1]`, **swap** them
4. After each pass, the last `i+1` elements are in their correct positions (no need to revisit them)
5. **Optimization:** If no swap happened in a full pass → array is already sorted → break early

### Dry Run

**Array:** `[5, 3, 8, 1, 2]`

**Pass 1** (the largest element bubbles to its correct position at the end):

| Step | Comparison | Swap? | Array After     |
|------|-----------|-------|-----------------|
| 1    | 5 > 3? YES | ✅ Swap | [3, 5, 8, 1, 2] |
| 2    | 5 > 8? NO  | —      | [3, 5, 8, 1, 2] |
| 3    | 8 > 1? YES | ✅ Swap | [3, 5, 1, 8, 2] |
| 4    | 8 > 2? YES | ✅ Swap | [3, 5, 1, 2, **8**] |

→ **8** is now at its correct position (last index).

**Pass 2:**

| Step | Comparison | Swap? | Array After       |
|------|-----------|-------|-------------------|
| 1    | 3 > 5? NO  | —      | [3, 5, 1, 2, 8]   |
| 2    | 5 > 1? YES | ✅ Swap | [3, 1, 5, 2, 8]   |
| 3    | 5 > 2? YES | ✅ Swap | [3, 1, 2, **5**, 8] |

→ **5** is now at its correct position. *(Continues until fully sorted: `[1, 2, 3, 5, 8]`)*

### Java Code

```java
import java.util.Arrays;

public class BubbleSort {

    public static void bubbleSort(int[] arr) {
        int n = arr.length;

        for (int i = 0; i < n - 1; i++) {          // we need at most n-1 passes
            boolean swapped = false;                 // flag to detect if any swap happened in this pass

            for (int j = 0; j < n - 1 - i; j++) {  // last i elements are already sorted, skip them
                if (arr[j] > arr[j + 1]) {           // out-of-order pair found
                    // swap arr[j] and arr[j+1]
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                    swapped = true;                  // mark that a swap occurred
                }
            }

            if (!swapped) break; // no swap in this entire pass → array is already sorted
        }
    }

    public static void main(String[] args) {
        int[] arr1 = {5, 3, 8, 1, 2};
        bubbleSort(arr1);
        System.out.println(Arrays.toString(arr1)); // Expected: [1, 2, 3, 5, 8]

        int[] arr2 = {1, 2, 3, 4, 5}; // already sorted — best case: only 1 pass
        bubbleSort(arr2);
        System.out.println(Arrays.toString(arr2)); // Expected: [1, 2, 3, 4, 5]

        int[] arr3 = {9, 7, 5, 3, 1}; // reverse sorted — worst case
        bubbleSort(arr3);
        System.out.println(Arrays.toString(arr3)); // Expected: [1, 3, 5, 7, 9]
    }
}
```

### Complexity Analysis

| Case                    | Time Complexity | Space Complexity |
|-------------------------|----------------|-----------------|
| Best (already sorted)   | O(N)           | O(1)            |
| Average                 | O(N²)          | O(1)            |
| Worst (reverse sorted)  | O(N²)          | O(1)            |

- **Time — Best O(N):** With the `swapped` flag, if no swaps occur in the very first pass, we break — only N comparisons are made.
- **Time — Worst O(N²):** For reverse-sorted input, every element must bubble all the way — approximately N × (N-1) / 2 comparisons total.
- **Space O(1):** Sorting is done in-place; only a few extra variables (`temp`, `swapped`) are used, regardless of N.

---

## 2. Selection Sort

### Definition

**Selection Sort** divides the array into a **sorted portion** (left) and an **unsorted portion** (right). In each pass, it **finds the minimum element** from the unsorted portion and **places it at the beginning** of that unsorted portion.

### Real-World Analogy

Imagine you have a pile of numbered cards face-up on a table. You scan all cards, pick the smallest one, and place it in position 1. Then scan the remaining cards, pick the next smallest, and place it in position 2. Repeat until all cards are placed.

### Algorithm Steps

1. Start with `i = 0` (the entire array is the "unsorted" portion)
2. Assume `arr[i]` is the minimum; record `minIdx = i`
3. Scan from `arr[i+1]` to `arr[n-1]` — if a smaller element is found, update `minIdx`
4. Swap `arr[i]` with `arr[minIdx]`
5. Now `arr[0..i]` is sorted; advance `i` by 1 and repeat

### Dry Run

**Array:** `[64, 25, 12, 22, 11]`

| Pass | Unsorted Portion         | Min Found    | Swap           | Array After              |
|------|--------------------------|--------------|----------------|--------------------------|
| 1    | [64, 25, 12, 22, 11]     | 11 (idx 4)   | arr[0]↔arr[4]  | [**11**, 25, 12, 22, 64] |
| 2    | [25, 12, 22, 64]         | 12 (idx 2)   | arr[1]↔arr[2]  | [11, **12**, 25, 22, 64] |
| 3    | [25, 22, 64]             | 22 (idx 3)   | arr[2]↔arr[3]  | [11, 12, **22**, 25, 64] |
| 4    | [25, 64]                 | 25 (idx 3)   | no swap needed | [11, 12, 22, **25**, 64] |

Final: `[11, 12, 22, 25, 64]` ✅

### Java Code

```java
import java.util.Arrays;

public class SelectionSort {

    public static void selectionSort(int[] arr) {
        int n = arr.length;

        for (int i = 0; i < n - 1; i++) {        // i marks the start of the unsorted portion
            int minIdx = i;                        // assume the first unsorted element is the minimum

            for (int j = i + 1; j < n; j++) {     // scan the rest of the unsorted portion
                if (arr[j] < arr[minIdx]) {        // found a smaller element
                    minIdx = j;                    // update the index of the minimum
                }
            }

            // swap arr[i] with the actual minimum element found
            if (minIdx != i) {                     // skip swap if minimum is already in position
                int temp = arr[minIdx];
                arr[minIdx] = arr[i];
                arr[i] = temp;
            }
        }
    }

    public static void main(String[] args) {
        int[] arr1 = {64, 25, 12, 22, 11};
        selectionSort(arr1);
        System.out.println(Arrays.toString(arr1)); // Expected: [11, 12, 22, 25, 64]

        int[] arr2 = {5, 1, 4, 2, 8};
        selectionSort(arr2);
        System.out.println(Arrays.toString(arr2)); // Expected: [1, 2, 4, 5, 8]

        int[] arr3 = {1}; // single element — no sorting needed
        selectionSort(arr3);
        System.out.println(Arrays.toString(arr3)); // Expected: [1]
    }
}
```

### Complexity Analysis

| Case    | Time Complexity | Space Complexity |
|---------|----------------|-----------------|
| Best    | O(N²)          | O(1)            |
| Average | O(N²)          | O(1)            |
| Worst   | O(N²)          | O(1)            |

- **Time O(N²) always:** Selection Sort always scans the full unsorted portion to find the minimum — it never exits early, even if the array is already sorted. The inner loop always runs in full.
- **Space O(1):** Only a constant amount of extra memory is used (`minIdx`, `temp`), regardless of the input size.

> **Key Insight:** Unlike Bubble Sort, Selection Sort makes at most **N-1 swaps** (one per pass). This makes it preferable when write/swap operations are costly (e.g., writing to flash memory).

---

## 3. Insertion Sort

### Definition

**Insertion Sort** builds the sorted array **one element at a time**. It picks each new element from the unsorted portion and **inserts it into its correct position** among the already-sorted elements on the left.

### Real-World Analogy

Think about how you sort playing cards in your hand. You pick one new card at a time. You slide it left past any cards that are larger until it's in the right spot — the cards already in your hand are always sorted.

### Algorithm Steps

1. Start from index `1` (treat `arr[0]` alone as a sorted portion of size 1)
2. Pick `arr[i]` as the `key` (the element to be inserted)
3. Compare `key` with elements going left from `arr[i-1]`
4. **Shift** every element that is greater than `key` one position to the right
5. Insert `key` into the gap that was created

### Dry Run

**Array:** `[12, 11, 13, 5, 6]`

| i | key | Action                          | Array After         |
|---|-----|---------------------------------|---------------------|
| 1 | 11  | 12 > 11 → shift 12 right        | [**11, 12**, 13, 5, 6] |
| 2 | 13  | 12 < 13 → no shift needed       | [11, 12, **13**, 5, 6] |
| 3 | 5   | 13>5, 12>5, 11>5 → shift all 3  | [**5**, 11, 12, 13, 6] |
| 4 | 6   | 13>6, 12>6, 11>6, 5<6 → insert  | [5, **6**, 11, 12, 13] |

Final: `[5, 6, 11, 12, 13]` ✅

### Java Code

```java
import java.util.Arrays;

public class InsertionSort {

    public static void insertionSort(int[] arr) {
        int n = arr.length;

        for (int i = 1; i < n; i++) {              // arr[0] alone is already "sorted"; start from index 1
            int key = arr[i];                       // element to be inserted into the sorted portion
            int j = i - 1;                          // start comparing from the element just before key

            // shift all elements in the sorted portion that are greater than key one step right
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];                // shift element one position to the right
                j--;                                // move comparison pointer to the left
            }

            arr[j + 1] = key;                       // insert key into the correct position
        }
    }

    public static void main(String[] args) {
        int[] arr1 = {12, 11, 13, 5, 6};
        insertionSort(arr1);
        System.out.println(Arrays.toString(arr1)); // Expected: [5, 6, 11, 12, 13]

        int[] arr2 = {1, 2, 3, 4, 5}; // already sorted — while loop never runs
        insertionSort(arr2);
        System.out.println(Arrays.toString(arr2)); // Expected: [1, 2, 3, 4, 5]

        int[] arr3 = {9, 5, 2, 1};
        insertionSort(arr3);
        System.out.println(Arrays.toString(arr3)); // Expected: [1, 2, 5, 9]
    }
}
```

### Complexity Analysis

| Case                   | Time Complexity | Space Complexity |
|------------------------|----------------|-----------------|
| Best (already sorted)  | O(N)           | O(1)            |
| Average                | O(N²)          | O(1)            |
| Worst (reverse sorted) | O(N²)          | O(1)            |

- **Time — Best O(N):** When already sorted, the `while` loop condition `arr[j] > key` fails immediately for every `i` — only one comparison per element is made.
- **Time — Worst O(N²):** For reverse-sorted input, every new `key` must be shifted all the way to index 0, giving approximately N²/2 total shifts.
- **Space O(1):** In-place sorting — only `key` and `j` are extra variables.

> 💡 **Insertion Sort is excellent for small arrays or nearly-sorted data.** Java's built-in `Arrays.sort()` uses Insertion Sort internally for small subarrays (size ≤ 7).

---

## 4. Merge Sort (Introduction)

### Definition

**Merge Sort** is a **Divide-and-Conquer** algorithm. It recursively **splits** the array into two halves, **sorts** each half independently, and then **merges** the two sorted halves back into one fully sorted array.

### Real-World Analogy

Imagine you want to sort 8 exam papers by score. You split them into two piles of 4. Each pile is split again into piles of 2. You sort each pair. Then you carefully merge pairs back into groups of 4 (always picking the lower score first), then into the final sorted stack of 8.

### The 3-Step Divide-and-Conquer Process

```
DIVIDE   → Find the midpoint and split the array into two halves
CONQUER  → Recursively sort each half (same 3 steps apply to each half)
MERGE    → Combine the two sorted halves into one sorted array
```

### Recursion Tree Visualization

```
              [38, 27, 43, 3, 9, 82, 10]
                   ↙                  ↘
          [38, 27, 43]           [3, 9, 82, 10]
            ↙       ↘               ↙         ↘
          [38]    [27, 43]       [3, 9]      [82, 10]
                  ↙     ↘       ↙    ↘       ↙     ↘
                [27]   [43]   [3]   [9]   [82]   [10]
                  ↘     ↙      ↘    ↙       ↘     ↙
                [27, 43]       [3, 9]        [10, 82]
                    ↘              ↙              ↙
              [27, 38, 43]     [3, 9, 10, 82]
                       ↘           ↙
                [3, 9, 10, 27, 38, 43, 82]  ✅
```

Each level does O(N) work during merging, and there are log₂(N) levels → total O(N log N).

### Dry Run — The Merge Step

Merging `[27, 38, 43]` and `[3, 9, 10, 82]` (both already sorted):

| i → leftArr | j → rightArr | Comparison   | Placed | Result So Far              |
|-------------|-------------|--------------|--------|----------------------------|
| 27          | 3           | 27 > 3       | 3      | [3]                        |
| 27          | 9           | 27 > 9       | 9      | [3, 9]                     |
| 27          | 10          | 27 > 10      | 10     | [3, 9, 10]                 |
| 27          | 82          | 27 < 82      | 27     | [3, 9, 10, 27]             |
| 38          | 82          | 38 < 82      | 38     | [3, 9, 10, 27, 38]         |
| 43          | 82          | 43 < 82      | 43     | [3, 9, 10, 27, 38, 43]     |
| done        | 82          | copy leftover| 82     | [3, 9, 10, 27, 38, 43, 82] |

### Java Code

```java
import java.util.Arrays;

public class MergeSort {

    // Main sort function — recursively divides the array
    public static void mergeSort(int[] arr, int left, int right) {
        if (left >= right) return;                    // base case: 1 or 0 elements — already sorted

        int mid = left + (right - left) / 2;          // find midpoint (this form avoids integer overflow)

        mergeSort(arr, left, mid);                    // recursively sort the left half
        mergeSort(arr, mid + 1, right);               // recursively sort the right half

        merge(arr, left, mid, right);                 // merge the two sorted halves
    }

    // Merge function — combines two sorted subarrays into one sorted subarray
    public static void merge(int[] arr, int left, int mid, int right) {
        int n1 = mid - left + 1;                      // number of elements in left subarray
        int n2 = right - mid;                         // number of elements in right subarray

        // create temporary arrays to hold copies of the two halves
        int[] leftArr = new int[n1];
        int[] rightArr = new int[n2];

        // copy data from arr into temporary arrays
        for (int i = 0; i < n1; i++) leftArr[i] = arr[left + i];
        for (int j = 0; j < n2; j++) rightArr[j] = arr[mid + 1 + j];

        int i = 0;     // pointer for leftArr
        int j = 0;     // pointer for rightArr
        int k = left;  // pointer for writing back into the original arr

        // merge: always pick the smaller of the two current elements
        while (i < n1 && j < n2) {
            if (leftArr[i] <= rightArr[j]) {          // <= ensures stability (equal elements keep original order)
                arr[k] = leftArr[i];
                i++;
            } else {
                arr[k] = rightArr[j];
                j++;
            }
            k++;
        }

        // copy any remaining elements from leftArr (if left half was longer)
        while (i < n1) arr[k++] = leftArr[i++];

        // copy any remaining elements from rightArr (if right half was longer)
        while (j < n2) arr[k++] = rightArr[j++];
    }

    public static void main(String[] args) {
        int[] arr1 = {38, 27, 43, 3, 9, 82, 10};
        mergeSort(arr1, 0, arr1.length - 1);
        System.out.println(Arrays.toString(arr1)); // Expected: [3, 9, 10, 27, 38, 43, 82]

        int[] arr2 = {5, 2, 4, 6, 1, 3};
        mergeSort(arr2, 0, arr2.length - 1);
        System.out.println(Arrays.toString(arr2)); // Expected: [1, 2, 3, 4, 5, 6]

        int[] arr3 = {1}; // single element — base case triggered immediately
        mergeSort(arr3, 0, arr3.length - 1);
        System.out.println(Arrays.toString(arr3)); // Expected: [1]
    }
}
```

### Complexity Analysis

| Case    | Time Complexity | Space Complexity |
|---------|----------------|-----------------|
| Best    | O(N log N)     | O(N)            |
| Average | O(N log N)     | O(N)            |
| Worst   | O(N log N)     | O(N)            |

- **Time O(N log N):** The array is split into **log₂ N levels** of recursion. At **each level**, the total work done by all `merge()` calls combined is **O(N)** (every element is processed once per level). So total = N × log N.
- **Space O(N):** The temporary `leftArr` and `rightArr` arrays created during merging together use O(N) extra space at most. The recursion call stack also uses O(log N) space.

> ✅ **Merge Sort always runs in O(N log N)** — it never degrades to O(N²), unlike Bubble, Selection, or Insertion Sort.

---

## Sorting Algorithms — Master Comparison Cheat Sheet

| Algorithm      | Best       | Average    | Worst      | Space | Stable? | Adaptive? | Best Used When                         |
|----------------|-----------|-----------|-----------|-------|---------|-----------|----------------------------------------|
| Bubble Sort    | O(N)      | O(N²)     | O(N²)     | O(1)  | ✅ Yes  | ✅ Yes    | Teaching only; rarely used in practice |
| Selection Sort | O(N²)     | O(N²)     | O(N²)     | O(1)  | ❌ No   | ❌ No     | When write/swap operations are costly  |
| Insertion Sort | O(N)      | O(N²)     | O(N²)     | O(1)  | ✅ Yes  | ✅ Yes    | Small arrays or nearly-sorted data     |
| Merge Sort     | O(N log N)| O(N log N)| O(N log N)| O(N)  | ✅ Yes  | ❌ No     | Large arrays; when stability matters   |

> 📌 **Stable Sort:** Equal elements maintain their original relative order after sorting.
> 📌 **Adaptive:** Performs fewer operations when the input is already (partially) sorted.

---

# Practice Problems (Session 5)

---

## 1. Sort Colors

🔗 [LeetCode 75 — Sort Colors](https://leetcode.com/problems/sort-colors/)

### Problem Statement

Given an array `nums` containing only the integers **0**, **1**, and **2** (representing the colors **red**, **white**, and **blue** respectively), **sort the array in-place** so that all 0s come first, followed by all 1s, and then all 2s.

**Constraint:** You must solve this **without using the built-in library sort function**.

**Examples:**

| Input                  | Output               | Reason                              |
|------------------------|----------------------|-------------------------------------|
| [2, 0, 2, 1, 1, 0]    | [0, 0, 1, 1, 2, 2]  | All 0s first, then 1s, then 2s      |
| [2, 0, 1]             | [0, 1, 2]            | Each color appears exactly once     |
| [1, 1, 1, 0, 0, 2]    | [0, 0, 1, 1, 1, 2]  | Mixed input sorted into correct zones|

---

### Approach 1: Brute Force (Count and Fill)

Count the number of 0s, 1s, and 2s in the array in a first pass. Then, overwrite the array in a second pass by filling in the correct count of 0s first, then 1s, then 2s. This is a simple two-pass approach.

#### The Math/Logic Behind It

We know there are only 3 possible values: 0, 1, 2. So instead of a general comparison-based sort, we can **count** how many of each exist, then **reconstruct** the array in the correct order.

If `count0 = 2`, `count1 = 2`, `count2 = 2` → result: `[0, 0, 1, 1, 2, 2]`

#### Dry Run

**Input:** `[2, 0, 2, 1, 1, 0]`

| Step       | Action                              | Array State          |
|------------|-------------------------------------|----------------------|
| Count pass | count0=2, count1=2, count2=2        | [2, 0, 2, 1, 1, 0]  |
| Fill 0s    | arr[0]=0, arr[1]=0                  | [0, 0, ?, ?, ?, ?]  |
| Fill 1s    | arr[2]=1, arr[3]=1                  | [0, 0, 1, 1, ?, ?]  |
| Fill 2s    | arr[4]=2, arr[5]=2                  | [0, 0, 1, 1, 2, 2]  |

```java
import java.util.Arrays;

public class SortColorsBrute {

    public static void sortColors(int[] nums) {
        int count0 = 0, count1 = 0, count2 = 0;

        // First pass: count how many 0s, 1s, and 2s exist
        for (int num : nums) {
            if (num == 0) count0++;
            else if (num == 1) count1++;
            else count2++;              // num == 2
        }

        int i = 0; // index pointer for filling

        // Second pass: fill the array with the correct number of each value
        while (count0-- > 0) nums[i++] = 0; // fill 0s first
        while (count1-- > 0) nums[i++] = 1; // then fill 1s
        while (count2-- > 0) nums[i++] = 2; // finally fill 2s
    }

    public static void main(String[] args) {
        int[] arr1 = {2, 0, 2, 1, 1, 0};
        sortColors(arr1);
        System.out.println(Arrays.toString(arr1)); // Expected: [0, 0, 1, 1, 2, 2]

        int[] arr2 = {2, 0, 1};
        sortColors(arr2);
        System.out.println(Arrays.toString(arr2)); // Expected: [0, 1, 2]

        int[] arr3 = {1, 1, 1, 0, 0, 2};
        sortColors(arr3);
        System.out.println(Arrays.toString(arr3)); // Expected: [0, 0, 1, 1, 1, 2]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — Two separate passes over the array of N elements: the first counts (O(N)), the second fills (O(N)), giving 2N = O(N) total.
* **Space Complexity:** `O(1)` — Only 3 counter variables and 1 index pointer are used, regardless of input size.

---

### Approach 2: Optimal (Dutch National Flag Algorithm — Single Pass)

Instead of two passes, we can sort everything in a **single pass** using **3 pointers**. This is the classic **Dutch National Flag algorithm**, proposed by Edsger W. Dijkstra.

The key idea: maintain 3 boundaries that together divide the array into 4 regions at all times:

```
[ 0...0 | 1...1 | ?...? | 2...2 ]
  ^      ^        ^      ^
  0     low      mid   high+1
```

- Everything **before `low`** is `0` ✅
- Everything **between `low` and `mid-1`** is `1` ✅
- Everything **between `mid` and `high`** is **unknown** (not yet processed)
- Everything **after `high`** is `2` ✅

We process `arr[mid]` and decide:
- `arr[mid] == 0` → swap with `arr[low]`, increment both `low` and `mid`
- `arr[mid] == 1` → it's already in the right zone, just increment `mid`
- `arr[mid] == 2` → swap with `arr[high]`, decrement `high` only (**do NOT** increment `mid` — the element swapped from `high` is still unknown)

Stop when `mid > high` (unknown region is empty).

#### Dry Run

**Input:** `[2, 0, 2, 1, 1, 0]`, `low=0, mid=0, high=5`

| mid | arr[mid] | Action             | low | mid | high | Array State               |
|-----|----------|--------------------|-----|-----|------|---------------------------|
| 0   | 2        | swap(0,5), high--  | 0   | 0   | 4    | [0, 0, 2, 1, 1, **2**]   |
| 0   | 0        | swap(0,0), low++, mid++ | 1 | 1  | 4    | [**0**, 0, 2, 1, 1, 2]   |
| 1   | 0        | swap(1,1), low++, mid++ | 2 | 2  | 4    | [0, **0**, 2, 1, 1, 2]   |
| 2   | 2        | swap(2,4), high--  | 2   | 2   | 3    | [0, 0, 1, 1, **2**, 2]   |
| 2   | 1        | mid++              | 2   | 3   | 3    | [0, 0, **1**, 1, 2, 2]   |
| 3   | 1        | mid++              | 2   | 4   | 3    | [0, 0, 1, **1**, 2, 2]   |
| mid > high → stop ✅ | | | | | | [0, 0, 1, 1, 2, 2] |

```java
import java.util.Arrays;

public class SortColorsOptimal {

    public static void sortColors(int[] nums) {
        int low = 0;                    // left boundary: everything before low is 0
        int mid = 0;                    // current element being examined
        int high = nums.length - 1;    // right boundary: everything after high is 2

        while (mid <= high) {           // keep processing until the unknown region is empty

            if (nums[mid] == 0) {
                // current element is 0 — swap it into the 0-zone
                int temp = nums[low];
                nums[low] = nums[mid];
                nums[mid] = temp;
                low++;                  // expand the 0-zone to the right
                mid++;                  // element at mid is now confirmed as 1, move forward
            } else if (nums[mid] == 1) {
                mid++;                  // 1 is already in the correct middle zone, just advance
            } else {                    // nums[mid] == 2
                // current element is 2 — swap it into the 2-zone
                int temp = nums[mid];
                nums[mid] = nums[high];
                nums[high] = temp;
                high--;                 // expand the 2-zone to the left
                // DO NOT increment mid — the element swapped from high is still unknown
            }
        }
    }

    public static void main(String[] args) {
        int[] arr1 = {2, 0, 2, 1, 1, 0};
        sortColors(arr1);
        System.out.println(Arrays.toString(arr1)); // Expected: [0, 0, 1, 1, 2, 2]

        int[] arr2 = {2, 0, 1};
        sortColors(arr2);
        System.out.println(Arrays.toString(arr2)); // Expected: [0, 1, 2]

        int[] arr3 = {0, 0, 0}; // all zeros — no swaps needed
        sortColors(arr3);
        System.out.println(Arrays.toString(arr3)); // Expected: [0, 0, 0]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — The array is traversed exactly once; `mid` only moves forward and `high` only moves backward, so they meet after at most N steps.
* **Space Complexity:** `O(1)` — Only 3 pointer variables (`low`, `mid`, `high`) are used — no extra array is created.

### Teaching Tips
1. **Introduce with colored physical objects:** Bring 3 colored markers/cards to class. Lay them out in random order on a desk. Let students physically sort them in one pass using the 3-pointer rule. This builds muscle memory for the algorithm.
2. **Common mistake — incrementing `mid` after swapping with `high`:** Students almost always make this error. Explain: "When you swap with `high`, the element that just arrived at `mid` came from an unknown position — we haven't inspected it yet. We must NOT skip it."
3. **Draw and label the 4 regions on the whiteboard:** Draw a box representing the array and label the 4 regions (0-zone | 1-zone | unknown | 2-zone). At each step, update the arrows for `low`, `mid`, and `high`. Students understand the algorithm far better when they can *see* the regions shrinking in real time.

---

## 2. Merge Sorted Arrays

🔗 [LeetCode 88 — Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)

### Problem Statement

You are given two **sorted integer arrays** `nums1` and `nums2`. `nums1` has a total length of `m + n`, where the **first `m` elements are valid** and the **last `n` elements are zeros** (placeholders). `nums2` has exactly `n` elements.

**Merge `nums2` into `nums1` in-place** so that `nums1` becomes a single sorted array.

**Examples:**

| nums1              | m | nums2   | n | Output             | Reason                             |
|--------------------|---|---------|---|--------------------|------------------------------------|
| [1,2,3,0,0,0]      | 3 | [2,5,6] | 3 | [1,2,2,3,5,6]     | Merge two sorted sequences         |
| [1]                | 1 | []      | 0 | [1]               | nums2 is empty, no change          |
| [0]                | 0 | [1]     | 1 | [1]               | nums1 has no valid elements; copy nums2 |

---

### Approach 1: Brute Force (Copy and Sort)

The simplest approach: copy all elements of `nums2` into the empty slots at the end of `nums1` (which are the trailing zeros), then sort the entire `nums1` using `Arrays.sort()`. Since `nums1` already has `n` zero-slots at the end, we just overwrite them.

#### Dry Run

**Input:** `nums1 = [1, 2, 3, 0, 0, 0]`, `m = 3`, `nums2 = [2, 5, 6]`, `n = 3`

| Step                   | Action                        | nums1 State         |
|------------------------|-------------------------------|---------------------|
| Start                  | —                             | [1, 2, 3, 0, 0, 0]  |
| Copy nums2[0]=2        | nums1[3] = 2                  | [1, 2, 3, 2, 0, 0]  |
| Copy nums2[1]=5        | nums1[4] = 5                  | [1, 2, 3, 2, 5, 0]  |
| Copy nums2[2]=6        | nums1[5] = 6                  | [1, 2, 3, 2, 5, 6]  |
| Arrays.sort(nums1)     | Sort all 6 elements           | [1, 2, 2, 3, 5, 6] ✅|

```java
import java.util.Arrays;

public class MergeSortedArrayBrute {

    public static void merge(int[] nums1, int m, int[] nums2, int n) {
        // copy all n elements of nums2 into the empty slots at the end of nums1
        for (int i = 0; i < n; i++) {
            nums1[m + i] = nums2[i]; // fill starting from index m (the first zero-slot)
        }

        // sort the entire nums1 array which now contains all m+n elements
        Arrays.sort(nums1); // O((m+n) log(m+n))
    }

    public static void main(String[] args) {
        int[] nums1a = {1, 2, 3, 0, 0, 0};
        merge(nums1a, 3, new int[]{2, 5, 6}, 3);
        System.out.println(Arrays.toString(nums1a)); // Expected: [1, 2, 2, 3, 5, 6]

        int[] nums1b = {1};
        merge(nums1b, 1, new int[]{}, 0);
        System.out.println(Arrays.toString(nums1b)); // Expected: [1]

        int[] nums1c = {0};
        merge(nums1c, 0, new int[]{1}, 1);
        System.out.println(Arrays.toString(nums1c)); // Expected: [1]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O((m + n) log(m + n))` — Copying takes O(n), but `Arrays.sort()` on the combined array of size m+n dominates the total cost.
* **Space Complexity:** `O(1)` — No extra arrays are created; all modifications happen in-place within `nums1`.

---

### Approach 2: Optimal (Two-Pointer from the End)

**Key Insight:** Both arrays are already sorted. If we fill `nums1` **from the front**, we'd overwrite valid elements we still need. But if we fill **from the back** (right to left), we safely use the empty zero-slots in `nums1` without touching any valid data.

Use 3 pointers:
- `i = m - 1` → points to the last **valid** element in `nums1`
- `j = n - 1` → points to the last element in `nums2`
- `k = m + n - 1` → points to the last position in `nums1` (the fill position)

At each step, compare `nums1[i]` and `nums2[j]`, place the **larger** of the two at `nums1[k]`, and advance the respective pointer left.

#### The Math/Logic Behind It

Filling from the right guarantees we never overwrite a valid element we still need. The `k` pointer decreases steadily, and the sorted region grows from right to left. If `nums2` still has remaining elements when `i < 0`, we copy them directly into `nums1[0..j]`.

#### Dry Run

**Input:** `nums1 = [1, 2, 3, 0, 0, 0]`, `m=3`, `nums2 = [2, 5, 6]`, `n=3`
**Initial:** `i=2 (val=3), j=2 (val=6), k=5`

| k | nums1[i] | nums2[j] | Larger | nums1 After          | Pointer Move |
|---|----------|----------|--------|----------------------|--------------|
| 5 | 3        | 6        | 6      | [1,2,3,0,0,**6**]   | j-- → j=1    |
| 4 | 3        | 5        | 5      | [1,2,3,0,**5**,6]   | j-- → j=0    |
| 3 | 3        | 2        | 3      | [1,2,3,**3**,5,6]   | i-- → i=1    |
| 2 | 2        | 2        | 2(j)   | [1,2,**2**,3,5,6]   | j-- → j=-1   |
| 1 | 2        | —        | 2      | [1,**2**,2,3,5,6]   | i-- → i=0    |
| 0 | 1        | —        | 1      | [**1**,2,2,3,5,6]   | i-- → i=-1   |

Final: `[1, 2, 2, 3, 5, 6]` ✅

```java
import java.util.Arrays;

public class MergeSortedArrayOptimal {

    public static void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1;           // pointer to last valid element in nums1
        int j = n - 1;           // pointer to last element in nums2
        int k = m + n - 1;       // pointer to the last position in nums1 (fill from here)

        // merge from right to left — always place the larger element at position k
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) {
                nums1[k] = nums1[i]; // nums1's element is larger — place it at k
                i--;                 // move the nums1 pointer left
            } else {
                nums1[k] = nums2[j]; // nums2's element is larger or equal — place it at k
                j--;                 // move the nums2 pointer left
            }
            k--;                     // move the fill position left
        }

        // if nums2 still has remaining elements, copy them into nums1
        // (if nums1 still has remaining elements, they're already in the right place — no action needed)
        while (j >= 0) {
            nums1[k] = nums2[j];
            j--;
            k--;
        }
    }

    public static void main(String[] args) {
        int[] nums1a = {1, 2, 3, 0, 0, 0};
        merge(nums1a, 3, new int[]{2, 5, 6}, 3);
        System.out.println(Arrays.toString(nums1a)); // Expected: [1, 2, 2, 3, 5, 6]

        int[] nums1b = {1};
        merge(nums1b, 1, new int[]{}, 0);
        System.out.println(Arrays.toString(nums1b)); // Expected: [1]

        int[] nums1c = {0};
        merge(nums1c, 0, new int[]{1}, 1);
        System.out.println(Arrays.toString(nums1c)); // Expected: [1]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(m + n)` — Each of the m elements from `nums1` and each of the n elements from `nums2` is visited exactly once; the total number of iterations is at most m + n.
* **Space Complexity:** `O(1)` — Everything is done in-place within the existing `nums1` array; only 3 pointer variables are used.

### Teaching Tips
1. **Use a physical card demonstration:** Lay out two sorted rows of numbered cards on a table. Starting from the rightmost card of each row, pick the larger card and place it at the back of a result row. Students can see the right-to-left fill process clearly and intuitively.
2. **Common mistake — filling from the left:** Students instinctively try to merge from index 0, which causes them to overwrite valid `nums1` elements. Emphasize the key rule: "We fill from the END because the zeros are already there — free real estate!"
3. **Whiteboard step-by-step with pointer arrows:** Draw `nums1` and `nums2` on the board. Draw 3 colored arrows labeled `i`, `j`, and `k`. At each iteration, erase and re-draw the arrows. Students instantly see why the pointers move in the directions they do and why `j`'s remaining elements need copying but `i`'s do not.

---

# Class 5 Homework

### Easy (Mandatory)
1. [Squares of a Sorted Array — LeetCode 977](https://leetcode.com/problems/squares-of-a-sorted-array/)
2. [Check if Array Is Sorted and Rotated — LeetCode 1752](https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/)
3. [Contains Duplicate — LeetCode 217](https://leetcode.com/problems/contains-duplicate/)

### Medium (Recommended)
1. [Sort Array by Parity — LeetCode 905](https://leetcode.com/problems/sort-array-by-parity/)
2. [Largest Number — LeetCode 179](https://leetcode.com/problems/largest-number/)
3. [Interval List Intersections — LeetCode 986](https://leetcode.com/problems/interval-list-intersections/)

### Challenge Problem (Optional)
[Count of Smaller Numbers After Self — LeetCode 315](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) *(Hint: Uses a modified Merge Sort — the merge step can count inversions!)*
