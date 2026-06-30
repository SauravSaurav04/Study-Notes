# [Week 6] – Class 11: Hashing

---

# Theory: Hashing

## 1. What is Hashing?

**Hashing** is a technique that maps data (keys) to fixed-size values (called **hash codes** or **indices**) using a special function called a **hash function**. It allows us to perform **search, insert, and delete operations in O(1) average time** — making it one of the most powerful tools in DSA.

> 🔑 **Key Idea:** Instead of searching through all elements one by one (like an array), hashing lets you jump directly to the location of any element — like a dictionary where you look up a word directly instead of reading every page.

---

### Real-World Analogy

Imagine a **library with a card catalog**. Each book has a unique code (like ISBN). The librarian uses that code to directly find the shelf where the book is stored — without checking every shelf. That code-to-shelf mapping is exactly what a hash function does!

Another analogy: **Phone contacts** on your phone. When you search "Ravi", your phone does not scan all 500 contacts one by one — it uses an internal hash to jump straight to names starting with "R". That is hashing in real life.

---

## 2. How Hashing Works (Internally)

```
Key  ──→  Hash Function  ──→  Index in Array (Hash Table)
"apple"  ──→  hashCode() % size  ──→  index 3
```

### Step-by-step:
1. You provide a **key** (e.g., "apple", 42, "Ravi")
2. Java calls `key.hashCode()` internally
3. That hash code is **compressed** using `% tableSize` to get an array index
4. The key-value pair is stored at that index

---

## 3. Collision and Collision Handling

A **collision** happens when two different keys produce the **same hash index**.

```
"abc" → hashCode() % 10 → index 5
"xyz" → hashCode() % 10 → index 5   ← COLLISION!
```

### Common Collision Handling Techniques:

| Technique | Description |
|-----------|-------------|
| **Chaining** | Each index holds a LinkedList of all colliding entries (Java's HashMap uses this) |
| **Open Addressing** | Find the next empty slot in the array (Linear Probing, Quadratic Probing) |

Java's `HashMap` uses **Chaining** internally (and converts to a Red-Black Tree when a bucket has > 8 entries for efficiency).

---

## 4. HashMap in Java

A `HashMap<K, V>` stores **key-value pairs** where:
- **K** = type of key
- **V** = type of value
- Keys are **unique** — each key maps to exactly one value
- Values can be duplicated

```java
import java.util.HashMap;

public class HashMapDemo {
    public static void main(String[] args) {

        // Create a HashMap to store name → age mapping
        HashMap<String, Integer> map = new HashMap<>();

        // 1. put(key, value) — Insert or update a key-value pair — O(1) avg
        map.put("Alice", 20);
        map.put("Bob", 22);
        map.put("Charlie", 21);
        map.put("Alice", 25); // Updates Alice's age (key already exists)

        // 2. get(key) — Retrieve value for a key — O(1) avg
        System.out.println("Alice's age: " + map.get("Alice")); // 25

        // 3. containsKey(key) — Check if key exists — O(1) avg
        System.out.println("Has Bob? " + map.containsKey("Bob")); // true

        // 4. remove(key) — Delete a key-value pair — O(1) avg
        map.remove("Bob");
        System.out.println("After removing Bob: " + map);

        // 5. getOrDefault(key, defaultValue) — Returns value or default if missing — O(1)
        int age = map.getOrDefault("Dave", 0); // Dave not present → returns 0
        System.out.println("Dave's age (default): " + age); // 0

        // 6. Iterating over a HashMap using entrySet
        System.out.println("\n--- All Entries ---");
        for (java.util.Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " → " + entry.getValue());
        }

        // 7. keySet() — Get all keys
        System.out.println("\n--- All Keys ---");
        for (String key : map.keySet()) {
            System.out.println(key);
        }

        // 8. values() — Get all values
        System.out.println("\n--- All Values ---");
        for (int val : map.values()) {
            System.out.println(val);
        }

        // 9. size() — Number of key-value pairs
        System.out.println("\nSize: " + map.size()); // 2
    }
}
```

---

## 5. HashMap Complexity Cheat Sheet

| Operation | Average Case | Worst Case (all collisions) |
|-----------|--------------|-----------------------------|
| `put(key, val)` | O(1) | O(N) |
| `get(key)` | O(1) | O(N) |
| `remove(key)` | O(1) | O(N) |
| `containsKey(key)` | O(1) | O(N) |
| Iteration | O(N) | O(N) |

> ⚡ **In practice**, Java's HashMap is almost always O(1) because it uses a good hash function and resizes automatically (load factor = 0.75).

---

## 6. HashSet in Java

A `HashSet<E>` is like a `HashMap` but **stores only keys, no values**. It is used when you only care about **presence/absence** of elements (no duplicates allowed).

```java
import java.util.HashSet;

public class HashSetDemo {
    public static void main(String[] args) {

        // Create a HashSet of Strings
        HashSet<String> set = new HashSet<>();

        // 1. add(element) — Insert element — O(1) avg
        set.add("Apple");
        set.add("Banana");
        set.add("Cherry");
        set.add("Apple"); // Duplicate — silently ignored

        System.out.println("Set: " + set); // Order NOT guaranteed

        // 2. contains(element) — Check if element exists — O(1) avg
        System.out.println("Has Apple? " + set.contains("Apple")); // true
        System.out.println("Has Mango? " + set.contains("Mango")); // false

        // 3. remove(element) — Delete element — O(1) avg
        set.remove("Banana");
        System.out.println("After removing Banana: " + set);

        // 4. size() — Number of unique elements
        System.out.println("Size: " + set.size()); // 2

        // 5. Iterating over a HashSet
        System.out.println("\n--- Elements ---");
        for (String fruit : set) {
            System.out.println(fruit);
        }
    }
}
```

---

## 7. HashMap vs HashSet vs Array — When to Use What?

| Feature | Array | HashMap | HashSet |
|---------|-------|---------|---------|
| Lookup by index | O(1) | ❌ | ❌ |
| Lookup by key | O(N) | **O(1)** | **O(1)** |
| Stores key-value pairs | ❌ | ✅ | ❌ |
| Stores only unique values | ❌ | ❌ | ✅ |
| Maintains insertion order | ✅ | ❌ | ❌ |
| Best for frequency counting | ❌ | **✅** | ❌ |
| Best for duplicate detection | ❌ | ❌ | **✅** |

---

## 8. Frequency Counting — The Most Common Hashing Pattern

The most frequent use of HashMap in DSA is **counting how many times each element appears**.

```java
import java.util.HashMap;

public class FrequencyCounter {
    public static void main(String[] args) {

        int[] arr = {1, 3, 2, 1, 4, 3, 1, 2};

        // Build a frequency map: element → count
        HashMap<Integer, Integer> freq = new HashMap<>();

        for (int num : arr) {
            // If key exists → increment count; if not → start from 0 and add 1
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        // Print frequency of each element
        System.out.println("Frequencies:");
        for (java.util.Map.Entry<Integer, Integer> entry : freq.entrySet()) {
            System.out.println(entry.getKey() + " appears " + entry.getValue() + " times");
        }

        // Output:
        // 1 appears 3 times
        // 2 appears 2 times
        // 3 appears 2 times
        // 4 appears 1 time
    }
}
```

> 💡 **Pattern to memorize:** `map.put(key, map.getOrDefault(key, 0) + 1)` — this one-liner is used in dozens of hashing problems!

---

## 9. LinkedHashMap — Hashing with Order

If you need a HashMap that **remembers insertion order**, use `LinkedHashMap`:

```java
import java.util.LinkedHashMap;

public class LinkedHashMapDemo {
    public static void main(String[] args) {
        // LinkedHashMap preserves insertion order
        LinkedHashMap<String, Integer> lmap = new LinkedHashMap<>();
        lmap.put("Z", 1);
        lmap.put("A", 2);
        lmap.put("M", 3);

        // Iterates in insertion order: Z, A, M
        for (String key : lmap.keySet()) {
            System.out.println(key + " → " + lmap.get(key));
        }
    }
}
```

This is very useful when you need to find the **first non-repeating character** or maintain element order.

---

---

# Practice Problems (Session 11)

---

## 1. Two Sum

🔗 [LeetCode #1 – Two Sum](https://leetcode.com/problems/two-sum/)

### Problem Statement

Given an array of integers `nums` and an integer **target**, return the **indices** of the two numbers such that they add up to `target`.

- You may assume that each input would have **exactly one solution**.
- You may **not use the same element twice**.
- Return the answer in **any order**.

**Examples:**

| Input | Target | Output | Reason |
|-------|--------|--------|--------|
| `[2, 7, 11, 15]` | `9` | `[0, 1]` | `nums[0] + nums[1] = 2 + 7 = 9` |
| `[3, 2, 4]` | `6` | `[1, 2]` | `nums[1] + nums[2] = 2 + 4 = 6` |
| `[3, 3]` | `6` | `[0, 1]` | `nums[0] + nums[1] = 3 + 3 = 6` |

---

### Approach 1: Brute Force (Check all pairs)

For every pair of indices (i, j) where i ≠ j, check if `nums[i] + nums[j] == target`. If yes, return `{i, j}`. This is the simplest possible approach — we try every combination.

#### The Math/Logic Behind It

We have N elements. The number of pairs is N*(N-1)/2. We check each pair once.

**Dry Run** on `arr = [2, 7, 11, 15]`, target = 9:

| i | j | nums[i] | nums[j] | Sum | == 9? |
|---|---|---------|---------|-----|-------|
| 0 | 1 | 2 | 7 | 9 | ✅ FOUND → return [0,1] |

```java
public class TwoSumBrute {
    public static int[] twoSum(int[] nums, int target) {
        int n = nums.length;

        // Try every pair (i, j) where i < j
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                // Check if this pair sums to target
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j}; // Return indices immediately
                }
            }
        }

        // Problem guarantees a solution exists, but Java requires a return
        return new int[]{-1, -1};
    }

    public static void main(String[] args) {
        // Test 1: standard case
        int[] result1 = twoSum(new int[]{2, 7, 11, 15}, 9);
        System.out.println("Test 1: [" + result1[0] + ", " + result1[1] + "]"); // expected [0, 1]

        // Test 2: answer not at beginning
        int[] result2 = twoSum(new int[]{3, 2, 4}, 6);
        System.out.println("Test 2: [" + result2[0] + ", " + result2[1] + "]"); // expected [1, 2]

        // Test 3: duplicate elements
        int[] result3 = twoSum(new int[]{3, 3}, 6);
        System.out.println("Test 3: [" + result3[0] + ", " + result3[1] + "]"); // expected [0, 1]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N²)` — We have a nested loop: for each of N elements, we check up to N other elements, giving N * N = N² comparisons.
* **Space Complexity:** `O(1)` — We use no extra data structures; only a few integer variables.

---

### Approach 2: Optimal (HashMap — Complement Lookup)

The key insight: for each number `nums[i]`, its required complement is `target - nums[i]`. If we can check in O(1) whether this complement has already been seen, we solve the problem in one pass. We use a HashMap to store `value → index` as we scan left to right.

#### The Math/Logic Behind It

```
For each nums[i]:
    complement = target - nums[i]
    If complement is in map → return {map[complement], i}
    Else → store map[nums[i]] = i
```

**Dry Run** on `arr = [2, 7, 11, 15]`, target = 9:

| i | nums[i] | complement = 9 - nums[i] | In map? | Action |
|---|---------|--------------------------|---------|--------|
| 0 | 2 | 7 | No | Store map[2] = 0 |
| 1 | 7 | 2 | **Yes** (map[2]=0) | Return [0, 1] ✅ |

```java
import java.util.HashMap;

public class TwoSumOptimal {
    public static int[] twoSum(int[] nums, int target) {
        // Map stores: value → index (so we can look up where a value is)
        HashMap<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i]; // What number do we need to pair with nums[i]?

            // If complement was seen before → we found our pair!
            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i}; // Return stored index and current index
            }

            // Store current number's index so future elements can look it up
            map.put(nums[i], i);
        }

        // Problem guarantees exactly one solution exists
        return new int[]{-1, -1};
    }

    public static void main(String[] args) {
        // Test 1: standard case
        int[] result1 = twoSum(new int[]{2, 7, 11, 15}, 9);
        System.out.println("Test 1: [" + result1[0] + ", " + result1[1] + "]"); // expected [0, 1]

        // Test 2: answer not at beginning
        int[] result2 = twoSum(new int[]{3, 2, 4}, 6);
        System.out.println("Test 2: [" + result2[0] + ", " + result2[1] + "]"); // expected [1, 2]

        // Test 3: duplicate elements
        int[] result3 = twoSum(new int[]{3, 3}, 6);
        System.out.println("Test 3: [" + result3[0] + ", " + result3[1] + "]"); // expected [0, 1]
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — We traverse the array exactly once; each HashMap lookup and insert is O(1), so the total is linear.
* **Space Complexity:** `O(N)` — In the worst case, we store all N elements in the HashMap before finding the pair.

### Teaching Tips
1. **Start with the brute force visually** — draw the array on the board, circle every pair with an arc, then show how many arcs there are for N=5, N=6, N=10 to illustrate why O(N²) gets slow.
2. **Common mistake:** Students often try to use a single variable `complement` without understanding why we store `value → index` (not `index → value`). Emphasize: we need to retrieve the **index** of the complement, not the complement itself.
3. **Board trick:** Draw two columns — "Number Seen" and "Its Index" — and fill in the HashMap row by row as you walk through the dry-run. This makes the HashMap feel like a lookup table students can physically see.

---

## 2. Majority Element

🔗 [LeetCode #169 – Majority Element](https://leetcode.com/problems/majority-element/)

### Problem Statement

Given an array `nums` of size N, return the **majority element** — the element that appears **more than ⌊N/2⌋ times**.

- You may assume that the majority element **always exists** in the array.
- ⌊N/2⌋ means floor division (e.g., ⌊7/2⌋ = 3, so the element must appear > 3 times, i.e., at least 4 times).

**Examples:**

| Input | N | N/2 | Output | Reason |
|-------|---|-----|--------|--------|
| `[3, 2, 3]` | 3 | 1 | `3` | 3 appears 2 times > 1 |
| `[2, 2, 1, 1, 1, 2, 2]` | 7 | 3 | `2` | 2 appears 4 times > 3 |
| `[1]` | 1 | 0 | `1` | 1 appears 1 time > 0 |

---

### Approach 1: Brute Force (Frequency Count with HashMap)

Count the frequency of every element using a HashMap. Then scan the map to find whichever element has a count greater than N/2. This is the most straightforward approach — build a frequency table, then look up the winner.

#### The Math/Logic Behind It

```
Frequency map for [3, 2, 3]:
  3 → 2
  2 → 1
Threshold = N/2 = 3/2 = 1
→ 3 has count 2 > 1 → return 3
```

**Dry Run** on `arr = [2, 2, 1, 1, 1, 2, 2]`, N = 7, threshold = 3:

| Element Processed | freq map |
|-------------------|----------|
| 2 | {2:1} |
| 2 | {2:2} |
| 1 | {2:2, 1:1} |
| 1 | {2:2, 1:2} |
| 1 | {2:2, 1:3} |
| 2 | {2:3, 1:3} |
| 2 | {2:4, 1:3} |

Check: 2 → count 4 > 3 → **return 2** ✅

```java
import java.util.HashMap;

public class MajorityElementBrute {
    public static int majorityElement(int[] nums) {
        int n = nums.length;
        int threshold = n / 2; // The element must appear MORE than this many times

        // Step 1: Count frequency of each element
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int num : nums) {
            // If num already in map → increment; else start count at 1
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        // Step 2: Find which element exceeds the threshold
        for (java.util.Map.Entry<Integer, Integer> entry : freq.entrySet()) {
            if (entry.getValue() > threshold) {
                return entry.getKey(); // This is the majority element
            }
        }

        // Problem guarantees a majority element exists — this line never runs
        return -1;
    }

    public static void main(String[] args) {
        // Test 1: simple case
        System.out.println("Test 1: " + majorityElement(new int[]{3, 2, 3})); // expected 3

        // Test 2: larger array
        System.out.println("Test 2: " + majorityElement(new int[]{2, 2, 1, 1, 1, 2, 2})); // expected 2

        // Test 3: single element
        System.out.println("Test 3: " + majorityElement(new int[]{1})); // expected 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — We traverse the array once to build the frequency map (O(N)), then traverse the map once which is at most O(N) entries total.
* **Space Complexity:** `O(N)` — The HashMap can store up to N distinct entries in the worst case.

---

### Approach 2: Optimal (Boyer-Moore Voting Algorithm)

The insight: if we pair up every occurrence of the majority element with a different element and cancel them out, the majority element will **always survive** because it appears more than N/2 times. We track a "candidate" and a "count" — increment count if we see the candidate, decrement if we see something else; reset the candidate when count hits 0.

#### The Math/Logic Behind It

Think of it like a **battle royale**: every non-candidate "kills" one candidate. Since the majority element has more than N/2 votes, even after all the cancellations, it is the last one standing.

```
candidate = first element, count = 1
For each next element:
    if element == candidate → count++  (same team, grows stronger)
    else → count--                     (they cancel each other)
    if count == 0 → reset candidate to current element (old candidate is dead)
```

**Dry Run** on `arr = [2, 2, 1, 1, 1, 2, 2]`:

| i | nums[i] | candidate | count | Action |
|---|---------|-----------|-------|--------|
| 0 | 2 | 2 | 1 | Init |
| 1 | 2 | 2 | 2 | Same → count++ |
| 2 | 1 | 2 | 1 | Different → count-- |
| 3 | 1 | 2 | 0 | Different → count-- → reset |
| 4 | 1 | 1 | 1 | New candidate = 1 |
| 5 | 2 | 1 | 0 | Different → count-- → reset |
| 6 | 2 | 2 | 1 | New candidate = 2 ✅ |

Final candidate = **2** ✅

```java
public class MajorityElementOptimal {
    public static int majorityElement(int[] nums) {
        int candidate = nums[0]; // Start with first element as tentative majority
        int count = 1;           // Count how "dominant" the current candidate is

        // Boyer-Moore Voting — scan from index 1
        for (int i = 1; i < nums.length; i++) {
            if (count == 0) {
                // Current candidate has been fully cancelled out — pick a new one
                candidate = nums[i];
                count = 1;
            } else if (nums[i] == candidate) {
                count++; // Same as candidate → candidate grows stronger
            } else {
                count--; // Different → one of each cancels out
            }
        }

        // The last surviving candidate is guaranteed to be the majority element
        return candidate;
    }

    public static void main(String[] args) {
        // Test 1: simple case
        System.out.println("Test 1: " + majorityElement(new int[]{3, 2, 3})); // expected 3

        // Test 2: larger array
        System.out.println("Test 2: " + majorityElement(new int[]{2, 2, 1, 1, 1, 2, 2})); // expected 2

        // Test 3: single element
        System.out.println("Test 3: " + majorityElement(new int[]{1})); // expected 1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — We make a single pass through the array, performing O(1) work per element.
* **Space Complexity:** `O(1)` — We only use two variables (`candidate` and `count`), regardless of array size.

### Teaching Tips
1. **Teach HashMap approach first** — it is intuitive and directly reinforces the frequency-counting pattern. Then introduce Boyer-Moore as the "clever trick" so students appreciate the contrast.
2. **Common mistake:** Students confuse "> N/2" with ">= N/2". Be very explicit: if N = 7, threshold is 3, so the element needs at least 4 appearances (not 3).
3. **Board trick for Boyer-Moore:** Write the array elements on the board and act out a "battle" — draw opposing arrows between the majority element and each different element. Show that the majority element always has leftover arrows (votes) after all battles are done. This visual makes the algorithm click instantly.

---

## 3. First Unique Character in a String

🔗 [LeetCode #387 – First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/)

### Problem Statement

Given a string `s`, find the **first non-repeating character** in it and return its **index**. If no such character exists, return `-1`.

- The string contains only lowercase English letters.
- "Non-repeating" means the character appears **exactly once** in the entire string.
- "First" means the one with the **smallest index** among all non-repeating characters.

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| `"leetcode"` | `0` | `'l'` appears once and is at index 0; `'e'` appears 3 times |
| `"loveleetcode"` | `2` | `'l'` at index 0 appears twice; `'o'` at index 1 appears twice; `'v'` at index 2 appears once |
| `"aabb"` | `-1` | All characters repeat; `'a'` appears twice, `'b'` appears twice |

---

### Approach 1: Brute Force (Frequency Array — Two Pass)

Count the frequency of every character using a fixed-size array of 26 (one slot per lowercase letter). Then scan the string a second time and return the index of the first character whose frequency is exactly 1.

#### The Math/Logic Behind It

Since the string only has lowercase letters ('a' to 'z'), we can use an integer array of size 26. The trick is: `char - 'a'` converts any lowercase letter to an index (0 for 'a', 1 for 'b', ..., 25 for 'z').

```
'l' - 'a' = 108 - 97 = 11  → freq[11]++
'e' - 'a' = 101 - 97 = 4   → freq[4]++
```

**Dry Run** on `s = "leetcode"`:

**Pass 1 — Build frequency:**

| Char | Index (char-'a') | freq update |
|------|-----------------|-------------|
| l | 11 | freq[11] = 1 |
| e | 4 | freq[4] = 1 |
| e | 4 | freq[4] = 2 |
| t | 19 | freq[19] = 1 |
| c | 2 | freq[2] = 1 |
| o | 14 | freq[14] = 1 |
| d | 3 | freq[3] = 1 |
| e | 4 | freq[4] = 3 |

**Pass 2 — Find first unique:**

| Index | Char | freq | Unique? |
|-------|------|------|---------|
| 0 | l | 1 | ✅ → return 0 |

```java
public class FirstUniqueCharBrute {
    public static int firstUniqChar(String s) {
        // freq[i] = number of times the (i+1)th letter of alphabet appears
        // e.g., freq[0] = count of 'a', freq[1] = count of 'b', etc.
        int[] freq = new int[26];

        // Pass 1: Count frequency of each character
        for (int i = 0; i < s.length(); i++) {
            freq[s.charAt(i) - 'a']++; // 'a'=0, 'b'=1, ..., 'z'=25
        }

        // Pass 2: Find first character with frequency exactly 1
        for (int i = 0; i < s.length(); i++) {
            if (freq[s.charAt(i) - 'a'] == 1) {
                return i; // This is the first unique character's index
            }
        }

        return -1; // No unique character found
    }

    public static void main(String[] args) {
        // Test 1: unique char at start
        System.out.println("Test 1: " + firstUniqChar("leetcode")); // expected 0

        // Test 2: unique char in middle
        System.out.println("Test 2: " + firstUniqChar("loveleetcode")); // expected 2

        // Test 3: no unique char
        System.out.println("Test 3: " + firstUniqChar("aabb")); // expected -1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — We make two passes through the string, each O(N), and the array operations are O(1), so total is O(N).
* **Space Complexity:** `O(1)` — The frequency array is always size 26 regardless of input size (it never grows).

---

### Approach 2: Optimal using LinkedHashMap (Preserves Insertion Order)

Use a `LinkedHashMap<Character, Integer>` to count frequencies. Since `LinkedHashMap` preserves **insertion order**, we can iterate through it directly and return the first key with value 1 — without needing a second pass over the string.

#### The Math/Logic Behind It

`LinkedHashMap` guarantees that when you iterate with `entrySet()`, entries come out in the **order they were first inserted**. This means the first key with frequency 1 in the map IS the first unique character in the string.

**Dry Run** on `s = "loveleetcode"`:

LinkedHashMap after processing entire string (in insertion order):

| Key (char) | Value (count) |
|------------|---------------|
| l | 2 |
| o | 2 |
| v | 1 ← first with count 1 → **index 2** |
| e | 4 |
| t | 1 |
| c | 1 |
| d | 1 |

```java
import java.util.LinkedHashMap;

public class FirstUniqueCharOptimal {
    public static int firstUniqChar(String s) {
        // LinkedHashMap preserves insertion order — crucial for this approach
        LinkedHashMap<Character, Integer> freq = new LinkedHashMap<>();

        // Pass 1: Count frequency of each character in insertion order
        for (char c : s.toCharArray()) {
            freq.put(c, freq.getOrDefault(c, 0) + 1); // Increment count or start at 1
        }

        // Pass 2: Iterate map in insertion order — first entry with count 1 is the answer
        for (java.util.Map.Entry<Character, Integer> entry : freq.entrySet()) {
            if (entry.getValue() == 1) {
                // Found first unique char — now find its index in original string
                return s.indexOf(entry.getKey());
            }
        }

        return -1; // No unique character found
    }

    public static void main(String[] args) {
        // Test 1: unique char at start
        System.out.println("Test 1: " + firstUniqChar("leetcode")); // expected 0

        // Test 2: unique char in middle
        System.out.println("Test 2: " + firstUniqChar("loveleetcode")); // expected 2

        // Test 3: no unique char
        System.out.println("Test 3: " + firstUniqChar("aabb")); // expected -1
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — One pass to build the map (O(N)) and one pass over the map (at most 26 entries since only lowercase letters) which is O(1), making the total O(N).
* **Space Complexity:** `O(1)` — The map holds at most 26 distinct lowercase letters, so it is a constant-size structure regardless of input length.

### Teaching Tips
1. **Start with the question "how do we know a character is unique?"** — guide students to realize they need to know the TOTAL count of a character across the WHOLE string before deciding if the first occurrence is unique. This naturally leads to the two-pass idea.
2. **Common mistake:** Students try to find the unique character in ONE pass with a single HashMap — but they cannot know if a character is unique until they have seen the whole string. Emphasize this is why we need two passes.
3. **Board trick:** Write `"loveleetcode"` on the board and underline each character as you fill in the frequency table. Then walk through left-to-right and circle the first character with count=1. This physical scan makes the "first unique" concept very visual and intuitive.

---

---

# Class 11 Homework

### Easy (Mandatory)
1. [Contains Duplicate – LeetCode #217](https://leetcode.com/problems/contains-duplicate/) — Return `true` if any value appears at least twice in the array.
2. [Single Number – LeetCode #136](https://leetcode.com/problems/single-number/) — Given a non-empty array where every element appears twice except for one, find that single element. (Hint: try HashMap OR XOR trick)
3. [Ransom Note – LeetCode #383](https://leetcode.com/problems/ransom-note/) — Given two strings `ransomNote` and `magazine`, return `true` if you can construct `ransomNote` using characters from `magazine`.

### Medium (Recommended)
1. [Group Anagrams – LeetCode #49](https://leetcode.com/problems/group-anagrams/) — Group strings that are anagrams of each other together into sublists. (Hint: sorted version of each string as HashMap key)
2. [Longest Consecutive Sequence – LeetCode #128](https://leetcode.com/problems/longest-consecutive-sequence/) — Given an unsorted array, find the length of the longest consecutive elements sequence in O(N) time. (Hint: Use a HashSet)
3. [Subarray Sum Equals K – LeetCode #560](https://leetcode.com/problems/subarray-sum-equals-k/) — Find the total number of contiguous subarrays whose sum equals k. (Hint: Prefix sum + HashMap)

### Challenge Problem (Optional)
[Top K Frequent Elements – LeetCode #347](https://leetcode.com/problems/top-k-frequent-elements/) — Given an integer array and an integer k, return the k most frequent elements. Your algorithm must be better than O(N log N). (Hint: HashMap for frequencies + Bucket Sort or MinHeap of size k)
