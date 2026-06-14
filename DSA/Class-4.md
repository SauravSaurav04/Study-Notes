# [Week 2] – Class 4: Strings

---

# Theory: Strings in Java

## 1. What is a String?

A **String** is a sequence of characters. In Java, a `String` is an **immutable object** — once created, its value **cannot be changed**. Any operation that appears to modify a string actually creates a **new String object** in memory.

> **Real-World Analogy:** Think of a String like a printed page. Once printed, you can't change individual letters on the page. If you want a modified version, you must print an entirely new page. That's exactly how Java Strings work.

### String Declaration & Initialization

```java
public class StringBasics {
    public static void main(String[] args) {
        // Method 1: String literal (stored in the String Pool — shared memory area)
        String name = "Alice";

        // Method 2: Using the 'new' keyword (creates a new object in heap memory)
        String greeting = new String("Hello");

        // Concatenation using + operator (creates a new String object each time)
        String full = name + " says " + greeting;
        System.out.println(full); // Alice says Hello

        // String length
        System.out.println("Length: " + name.length()); // 5

        // Accessing a character by index (0-based)
        System.out.println("First char: " + name.charAt(0)); // A
        System.out.println("Last char:  " + name.charAt(name.length() - 1)); // e
    }
}
```

---

## 2. Common String Methods (Cheat Sheet)

| Method | Description | Example | Result |
|--------|-------------|---------|--------|
| `length()` | Returns number of characters | `"hello".length()` | `5` |
| `charAt(i)` | Returns character at index i | `"hello".charAt(1)` | `'e'` |
| `indexOf(c)` | Returns index of first occurrence | `"hello".indexOf('l')` | `2` |
| `substring(s, e)` | Returns substring from s to e-1 | `"hello".substring(1, 4)` | `"ell"` |
| `toLowerCase()` | Converts to lowercase | `"HELLO".toLowerCase()` | `"hello"` |
| `toUpperCase()` | Converts to uppercase | `"hello".toUpperCase()` | `"HELLO"` |
| `trim()` | Removes leading/trailing spaces | `"  hi  ".trim()` | `"hi"` |
| `equals(s)` | Compares content (case-sensitive) | `"hi".equals("hi")` | `true` |
| `equalsIgnoreCase(s)` | Compares content (case-insensitive) | `"Hi".equalsIgnoreCase("hi")` | `true` |
| `contains(s)` | Checks if substring exists | `"hello".contains("ell")` | `true` |
| `replace(a, b)` | Replaces all occurrences of a with b | `"aabb".replace('a','x')` | `"xxbb"` |
| `toCharArray()` | Converts String to `char[]` | `"ab".toCharArray()` | `{'a','b'}` |

```java
public class StringMethodsDemo {
    public static void main(String[] args) {
        String s = "  Hello, World!  ";

        System.out.println(s.trim());                     // "Hello, World!"
        System.out.println(s.trim().toLowerCase());        // "hello, world!"
        System.out.println(s.trim().substring(0, 5));      // "Hello"
        System.out.println(s.trim().replace("World", "Java")); // "Hello, Java!"
        System.out.println(s.trim().contains("World"));    // true
        System.out.println(s.trim().indexOf('o'));          // 4 (first 'o')
    }
}
```

---

## 3. String Immutability & Why It Matters

```java
public class ImmutabilityDemo {
    public static void main(String[] args) {
        String s = "hello";

        // This does NOT modify s — it creates a new String "HELLO" and assigns it
        s = s.toUpperCase();
        System.out.println(s); // HELLO

        // Attempting to "concatenate in a loop" is expensive!
        String result = "";
        for (int i = 0; i < 5; i++) {
            result += i; // Creates a NEW String object on every iteration — O(N²) total!
        }
        System.out.println(result); // 01234
    }
}
```

> ⚠️ **Critical:** Never build strings with `+` inside a loop. Use `StringBuilder` instead (see Section 4).

---

## 4. StringBuilder — Mutable String Builder

`StringBuilder` is a **mutable** sequence of characters. Unlike `String`, it modifies the same object internally — no new object is created on each append. Use it when building strings **inside loops** or when doing many modifications.

> **Real-World Analogy:** Think of a `String` as a whiteboard that erases and reprints everything for every change. A `StringBuilder` is a whiteboard with a running draft — you just add text to the end without starting over.

### Key StringBuilder Methods

| Method | Description |
|--------|-------------|
| `append(x)` | Adds x to the end |
| `insert(i, x)` | Inserts x at position i |
| `delete(s, e)` | Removes characters from s to e-1 |
| `reverse()` | Reverses the entire content |
| `toString()` | Converts back to a regular String |
| `length()` | Returns current length |
| `charAt(i)` | Returns char at index i |

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();

        // Efficient string building inside a loop — O(N) total, not O(N²)
        for (int i = 1; i <= 5; i++) {
            sb.append(i);       // Append number
            if (i < 5) sb.append("-"); // Append separator
        }
        System.out.println(sb.toString()); // 1-2-3-4-5

        // Reverse a string using StringBuilder
        String original = "algorithm";
        String reversed = new StringBuilder(original).reverse().toString();
        System.out.println(reversed); // mhtirogla

        // Insert at a specific position
        StringBuilder sb2 = new StringBuilder("HelloWorld");
        sb2.insert(5, ", "); // Insert ", " between Hello and World
        System.out.println(sb2); // Hello, World

        // Delete a range
        sb2.delete(5, 7); // Remove the ", "
        System.out.println(sb2); // HelloWorld
    }
}
```

**Complexity Comparison:**

| Operation | Using `String` (+ operator) | Using `StringBuilder` |
|-----------|-----------------------------|-----------------------|
| N appends | `O(N²)` total | `O(N)` total |
| Space | New object each time | One object, grows internally |

---

## 5. Character Manipulation

In Java, characters (`char`) are stored as their **ASCII integer values**. This enables powerful arithmetic on characters.

### Key ASCII Values to Remember

| Character | ASCII Value |
|-----------|-------------|
| `'A'` | 65 |
| `'Z'` | 90 |
| `'a'` | 97 |
| `'z'` | 122 |
| `'0'` | 48 |
| `'9'` | 57 |

```java
public class CharManipulation {
    public static void main(String[] args) {
        char ch = 'A';

        // Convert char to int (get ASCII value)
        int ascii = (int) ch;
        System.out.println("ASCII of 'A': " + ascii); // 65

        // Convert lowercase to uppercase by subtracting 32
        char lower = 'g';
        char upper = (char)(lower - 32);
        System.out.println("Uppercase: " + upper); // G

        // Check if a character is a letter or digit
        System.out.println(Character.isLetter('a'));  // true
        System.out.println(Character.isDigit('5'));   // true
        System.out.println(Character.isUpperCase('A')); // true
        System.out.println(Character.toLowerCase('B')); // b

        // Use a character as an array index (frequency counting trick)
        // 'a' - 'a' = 0, 'b' - 'a' = 1, ... 'z' - 'a' = 25
        int[] freq = new int[26]; // freq[0] = count of 'a', freq[25] = count of 'z'
        String word = "banana";
        for (char c : word.toCharArray()) {
            freq[c - 'a']++; // Map 'a'→0, 'b'→1, ..., 'z'→25
        }

        for (int i = 0; i < 26; i++) {
            if (freq[i] > 0) {
                System.out.println((char)('a' + i) + ": " + freq[i]);
                // b: 1, a: 3, n: 2
            }
        }
    }
}
```

---

## 6. Traversing a String

```java
public class StringTraversal {
    public static void main(String[] args) {
        String s = "hello";

        // Method 1: Using index with charAt()
        for (int i = 0; i < s.length(); i++) {
            System.out.print(s.charAt(i) + " "); // h e l l o
        }
        System.out.println();

        // Method 2: Convert to char array and use for-each loop
        for (char c : s.toCharArray()) {
            System.out.print(c + " "); // h e l l o
        }
        System.out.println();

        // Reverse traversal
        for (int i = s.length() - 1; i >= 0; i--) {
            System.out.print(s.charAt(i) + " "); // o l l e h
        }
    }
}
```

---

## Strings Cheat Sheet

| Concept | Key Point | Java Tool |
|---------|-----------|-----------|
| Immutability | Strings cannot be changed in-place | Use `StringBuilder` for mutations |
| Character access | `charAt(i)` → O(1) | Index-based, 0-indexed |
| Comparison | Never use `==` for content; use `.equals()` | `==` checks memory address |
| Frequency counting | `freq[c - 'a']++` | `int[26]` array for lowercase letters |
| Conversion | `String ↔ char[]` | `toCharArray()` / `new String(charArr)` |
| Efficient building | Loop with `+` is O(N²) | `StringBuilder` is O(N) |

---

# Practice Problems (Session 4)

---

## 1. Reverse a String

[Reverse String (LeetCode 344)](https://leetcode.com/problems/reverse-string/)

### Problem Statement

Write a function that **reverses a character array** `s` **in-place**. You must do this by modifying the input array directly with **O(1) extra memory**. Do not allocate extra space for another array.

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| `['h','e','l','l','o']` | `['o','l','l','e','h']` | Characters reversed end-to-end |
| `['H','a','n','n','a','h']` | `['h','a','n','n','a','H']` | Each character swapped with its mirror |
| `['a']` | `['a']` | Single character — unchanged |
| `['A','B','C','D']` | `['D','C','B','A']` | All 4 characters reversed |

---

### Approach 1: Brute Force (New Array)

Create a new character array of the same size. Copy elements from the original array in **reverse order** into the new array, then copy everything back. This is straightforward but uses extra memory.

#### The Math/Logic Behind It

If the original string is at indices `0, 1, 2, ..., n-1`, then the reversed version maps:
- index `0` ← original index `n-1`
- index `1` ← original index `n-2`
- In general: `reversed[i] = original[n - 1 - i]`

**Dry Run for s = {'h','e','l','l','o'}:**

| i (new index) | n-1-i (source) | Character Copied |
|---------------|----------------|-----------------|
| 0             | 4              | 'o'             |
| 1             | 3              | 'l'             |
| 2             | 2              | 'l'             |
| 3             | 1              | 'e'             |
| 4             | 0              | 'h'             |

Result: `{'o','l','l','e','h'}` ✅

```java
public class ReverseString {
    public static void reverseStringBrute(char[] s) {
        int n = s.length;

        // Step 1: Create a temporary array to hold the reversed characters
        char[] temp = new char[n];

        // Step 2: Fill temp in reverse order from s
        for (int i = 0; i < n; i++) {
            temp[i] = s[n - 1 - i]; // Map position i to the mirror position in s
        }

        // Step 3: Copy temp back into the original array s
        for (int i = 0; i < n; i++) {
            s[i] = temp[i];
        }
    }

    public static void main(String[] args) {
        char[] s1 = {'h', 'e', 'l', 'l', 'o'};
        reverseStringBrute(s1);
        System.out.println(new String(s1)); // olleh

        char[] s2 = {'H', 'a', 'n', 'n', 'a', 'h'};
        reverseStringBrute(s2);
        System.out.println(new String(s2)); // hannaH

        char[] s3 = {'A', 'B', 'C', 'D'};
        reverseStringBrute(s3);
        System.out.println(new String(s3)); // DCBA
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — we traverse the array twice: once to fill `temp`, once to copy back.
* **Space Complexity:** `O(N)` — an extra `temp` array of size N is allocated; violates the O(1) space requirement.

---

### Approach 2: Optimal (Two Pointer — In-Place Swap)

Use two pointers: `left` starting at index `0` and `right` starting at index `n-1`. Swap the characters at both pointers, then move them toward each other. Repeat until they meet in the middle. This is the classic in-place reversal pattern.

#### The Math/Logic Behind It

The key insight: the character at position `i` in the original always maps to position `n-1-i` in the reversed version. So we swap them **in pairs** from the outside in. We only need to do `N/2` swaps — not N.

**Dry Run for s = {'h','e','l','l','o'}:**

| Step | left | right | Swap | Array State |
|------|------|-------|------|-------------|
| 1    | 0    | 4     | 'h' ↔ 'o' | `{'o','e','l','l','h'}` |
| 2    | 1    | 3     | 'e' ↔ 'l' | `{'o','l','l','e','h'}` |
| 3    | 2    | 2     | left==right → Stop | `{'o','l','l','e','h'}` ✅ |

```java
public class ReverseString {
    public static void reverseStringOptimal(char[] s) {
        int left = 0;              // Left pointer starts at the beginning
        int right = s.length - 1; // Right pointer starts at the end

        // Keep swapping while the two pointers haven't met or crossed
        while (left < right) {
            // Swap characters at left and right positions
            char temp = s[left];
            s[left]  = s[right];
            s[right] = temp;

            left++;  // Move left pointer inward (toward center)
            right--; // Move right pointer inward (toward center)
        }
    }

    public static void main(String[] args) {
        char[] s1 = {'h', 'e', 'l', 'l', 'o'};
        reverseStringOptimal(s1);
        System.out.println(new String(s1)); // olleh

        char[] s2 = {'H', 'a', 'n', 'n', 'a', 'h'};
        reverseStringOptimal(s2);
        System.out.println(new String(s2)); // hannaH

        char[] s3 = {'A'};
        reverseStringOptimal(s3);
        System.out.println(new String(s3)); // A (single char, unchanged)
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each character is visited at most once; only N/2 swaps are performed.
* **Space Complexity:** `O(1)` — only a single `temp` char variable is used; no extra array needed.

### Teaching Tips
1. **Start with the analogy:** "Imagine a row of students facing forward. To reverse the line, the first and last student swap seats, then the second and second-to-last, and so on. They stop when they meet in the middle." This makes the two-pointer swap intuitive.
2. **Show why brute force fails the constraint:** The problem explicitly says `O(1)` extra memory. Walk through the brute force first so students appreciate WHY the in-place approach is needed.
3. **Common mistake — off-by-one in the middle:** For odd-length arrays (e.g., 5 chars), the middle element (index 2) doesn't need swapping. The `while (left < right)` condition (strict less-than) automatically handles this. Have students trace through a 5-element array to see this.

---

## 2. Valid Palindrome

[Valid Palindrome (LeetCode 125)](https://leetcode.com/problems/valid-palindrome/)

### Problem Statement

A phrase is a **palindrome** if, after converting all uppercase letters to lowercase and **removing all non-alphanumeric characters**, it reads the same forward and backward. Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| `"A man, a plan, a canal: Panama"` | `true` | After cleaning: `"amanaplanacanalpanama"` — palindrome |
| `"race a car"` | `false` | After cleaning: `"raceacar"` — not a palindrome |
| `" "` | `true` | After cleaning: `""` — empty string is a palindrome |
| `"Was it a car or a cat I saw?"` | `true` | After cleaning: `"wasitacaroracatisaw"` — palindrome |

---

### Approach 1: Brute Force (Clean Then Check)

First, **filter** the string to keep only alphanumeric characters and convert to lowercase. Then check if the resulting string equals its own reverse.

**Dry Run for s = "A man, a plan, a canal: Panama":**

| Step | Action | Result |
|------|--------|--------|
| Filter | Keep only letters/digits, lowercase | `"amanaplanacanalpanama"` |
| Reverse | Reverse the cleaned string | `"amanaplanacanalpanama"` |
| Compare | cleaned == reversed? | `true` ✅ |

```java
public class ValidPalindrome {
    public static boolean isPalindromeBrute(String s) {
        // Step 1: Build a cleaned version — only lowercase alphanumeric chars
        StringBuilder cleaned = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                // Convert to lowercase and add to the cleaned builder
                cleaned.append(Character.toLowerCase(c));
            }
        }

        // Step 2: Build the reversed version of the cleaned string
        String cleanedStr = cleaned.toString();
        String reversedStr = cleaned.reverse().toString();
        // Note: after .reverse(), the StringBuilder is already reversed, so cleanedStr must be saved before

        // Step 3: Compare — palindrome if they match
        return cleanedStr.equals(reversedStr);
    }

    // Corrected version (save cleaned before reversing)
    public static boolean isPalindromeBruteFixed(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                sb.append(Character.toLowerCase(c));
            }
        }
        String cleaned = sb.toString();
        String reversed = sb.reverse().toString();
        return cleaned.equals(reversed);
    }

    public static void main(String[] args) {
        System.out.println(isPalindromeBruteFixed("A man, a plan, a canal: Panama")); // true
        System.out.println(isPalindromeBruteFixed("race a car"));                     // false
        System.out.println(isPalindromeBruteFixed(" "));                              // true
        System.out.println(isPalindromeBruteFixed("Was it a car or a cat I saw?"));   // true
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — one pass to build cleaned string + O(N) for reverse + O(N) for comparison; all linear in input length.
* **Space Complexity:** `O(N)` — the `cleaned` StringBuilder stores up to N characters from the input.

---

### Approach 2: Optimal (Two Pointer — Skip Non-Alphanumeric)

Use two pointers (`left` and `right`) directly on the original string. Skip any non-alphanumeric characters from both sides. Compare the valid characters at both ends. If they ever differ, it's not a palindrome. This avoids building a new string entirely.

#### The Math/Logic Behind It

We never need to construct the cleaned string. We just need to verify that for every pair of valid characters seen from both ends, they match. The moment a mismatch occurs, we can immediately return `false`.

**Dry Run for s = "A man, a plan, a canal: Panama":**

| left → char | right → char | Action |
|-------------|--------------|--------|
| 'A' | 'a' | Both alphanumeric. toLower: 'a'=='a' ✅ → move both |
| 'm' | 'm' | 'm'=='m' ✅ → move both |
| 'a' | 'a' | 'a'=='a' ✅ → move both |
| 'n' | 'n' | 'n'=='n' ✅ → move both |
| ... | ... | All match → return `true` ✅ |

```java
public class ValidPalindrome {
    public static boolean isPalindromeOptimal(String s) {
        int left  = 0;            // Left pointer starts at the beginning
        int right = s.length() - 1; // Right pointer starts at the end

        while (left < right) {
            // Skip non-alphanumeric characters from the LEFT
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
                left++; // Move left forward past non-alphanumeric chars
            }

            // Skip non-alphanumeric characters from the RIGHT
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
                right--; // Move right backward past non-alphanumeric chars
            }

            // Now both pointers point to valid characters — compare (case-insensitive)
            if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
                return false; // Mismatch found — not a palindrome
            }

            left++;  // Move both pointers inward for the next comparison
            right--;
        }

        return true; // All valid character pairs matched — it is a palindrome
    }

    public static void main(String[] args) {
        System.out.println(isPalindromeOptimal("A man, a plan, a canal: Panama")); // true
        System.out.println(isPalindromeOptimal("race a car"));                     // false
        System.out.println(isPalindromeOptimal(" "));                              // true
        System.out.println(isPalindromeOptimal("Was it a car or a cat I saw?"));   // true
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — each character is visited at most once by either the left or right pointer; the total work across all pointer movements is O(N).
* **Space Complexity:** `O(1)` — no extra strings or arrays are created; only two pointer variables are used.

### Teaching Tips
1. **Clarify what "palindrome" means for strings:** Students know "racecar" is a palindrome, but they might not expect punctuation and spaces to be ignored. Show the problem's cleaning step explicitly before coding.
2. **Nested `while` loops inside the outer `while` — explain why it's still O(N):** Each of the N characters is processed by `left` or `right` at most once in total across ALL iterations of the outer loop. This is a great opportunity to teach amortized analysis.
3. **Common mistake — comparing without `toLowerCase`:** If students forget to normalize case, `'A' != 'a'` will cause `"Aba"` to fail. Stress: always normalize before comparing.

---

## 3. Valid Anagram

[Valid Anagram (LeetCode 242)](https://leetcode.com/problems/valid-anagram/)

### Problem Statement

Given two strings `s` and `t`, return `true` if `t` is an **anagram** of `s`, and `false` otherwise. An **anagram** is a word or phrase formed by **rearranging the letters** of another, using all the original letters **exactly once**.

**Examples:**

| s | t | Output | Reason |
|---|---|--------|--------|
| `"anagram"` | `"nagaram"` | `true` | Same letters, just rearranged |
| `"rat"` | `"car"` | `false` | Different characters (c not in rat) |
| `"listen"` | `"silent"` | `true` | Same characters, different order |
| `"hello"` | `"world"` | `false` | Different character sets |

---

### Approach 1: Brute Force (Sort Both and Compare)

Sort both strings. If they are anagrams, their sorted versions must be **identical**. This is simple but requires O(N log N) time due to sorting.

#### The Math/Logic Behind It

If two strings are anagrams, they contain **exactly the same characters with the same frequencies**. Sorting arranges all characters in the same order — so two anagrams will produce the same sorted string.

**Dry Run for s = "anagram", t = "nagaram":**

| String | Sorted |
|--------|--------|
| `"anagram"` | `"aaagmnr"` |
| `"nagaram"` | `"aaagmnr"` |
| Compare | Equal ✅ → return `true` |

```java
import java.util.Arrays;

public class ValidAnagram {
    public static boolean isAnagramBrute(String s, String t) {
        // Quick check: if lengths differ, they can't be anagrams
        if (s.length() != t.length()) return false;

        // Step 1: Convert both strings to char arrays
        char[] sArr = s.toCharArray();
        char[] tArr = t.toCharArray();

        // Step 2: Sort both char arrays alphabetically
        Arrays.sort(sArr); // Sorts in-place: O(N log N)
        Arrays.sort(tArr);

        // Step 3: Compare sorted arrays — anagram if they are identical
        return Arrays.equals(sArr, tArr);
    }

    public static void main(String[] args) {
        System.out.println(isAnagramBrute("anagram", "nagaram")); // true
        System.out.println(isAnagramBrute("rat",     "car"));     // false
        System.out.println(isAnagramBrute("listen",  "silent"));  // true
        System.out.println(isAnagramBrute("hello",   "world"));   // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N log N)` — sorting two arrays of length N each takes O(N log N); the final comparison is O(N).
* **Space Complexity:** `O(N)` — `toCharArray()` creates two new arrays of size N each.

---

### Approach 2: Optimal (Frequency Count with int[26])

Instead of sorting, count the **frequency of each character** in `s`, then **decrement** the count for each character in `t`. If `t` is an anagram of `s`, all counts in the frequency array will be exactly **zero** at the end.

#### The Math/Logic Behind It

Use a single integer array of size 26 (one slot per lowercase letter). The trick: `freq[c - 'a']` maps `'a'` → slot 0, `'b'` → slot 1, ..., `'z'` → slot 25.
- Increment for every char in `s`.
- Decrement for every char in `t`.
- If all counts are 0 → anagram. If any count is non-zero → not an anagram.

**Dry Run for s = "rat", t = "car":**

| Step | Action | freq['r'-'a'] | freq['a'-'a'] | freq['t'-'a'] | freq['c'-'a'] |
|------|--------|---------|---------|---------|---------|
| Count s | +r, +a, +t | 1 | 1 | 1 | 0 |
| Count t | -c, -a, -r | 0 | 0 | 1 | -1 |
| Check | Any non-zero? | — | — | **1 ≠ 0** | — |
| Result | Not anagram → `false` ✅ | | | | |

```java
public class ValidAnagram {
    public static boolean isAnagramOptimal(String s, String t) {
        // Quick check: anagrams must have the same number of characters
        if (s.length() != t.length()) return false;

        // freq[i] = (count of letter i in s) - (count of letter i in t)
        int[] freq = new int[26]; // 26 slots for 'a' through 'z'

        // Step 1: Increment count for each character in s
        for (char c : s.toCharArray()) {
            freq[c - 'a']++; // Map 'a'→0, 'b'→1, ..., 'z'→25 and increment
        }

        // Step 2: Decrement count for each character in t
        for (char c : t.toCharArray()) {
            freq[c - 'a']--; // Same mapping; subtract t's characters
        }

        // Step 3: If any frequency is non-zero, t has different chars than s
        for (int count : freq) {
            if (count != 0) return false; // Mismatch found — not an anagram
        }

        return true; // All frequencies balanced out — confirmed anagram
    }

    public static void main(String[] args) {
        System.out.println(isAnagramOptimal("anagram", "nagaram")); // true
        System.out.println(isAnagramOptimal("rat",     "car"));     // false
        System.out.println(isAnagramOptimal("listen",  "silent"));  // true
        System.out.println(isAnagramOptimal("hello",   "world"));   // false
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(N)` — two O(N) passes over the strings plus one O(26) = O(1) pass over the frequency array; total is O(N).
* **Space Complexity:** `O(1)` — the frequency array is always exactly size 26, regardless of the input size.

### Teaching Tips
1. **Use a physical analogy for frequency counting:** "Imagine you have two bags of letter tiles (like Scrabble pieces). To check if they're anagrams, you don't sort them — you just count how many A's, B's, etc. are in each bag and compare. That's exactly what the int[26] approach does."
2. **Explain the `c - 'a'` trick clearly on the board:** Write `'a' - 'a' = 0`, `'b' - 'a' = 1`, `'z' - 'a' = 25`. Show that this maps every lowercase letter to a unique index 0–25. This is a foundational trick students will use in many string problems.
3. **Ask: "What if the strings contain Unicode or capital letters?"** The `int[26]` approach only works for lowercase English letters. Use a `HashMap<Character, Integer>` for the general case. Presenting this follow-up trains students to think about constraints.

---

## 4. Longest Common Prefix

[Longest Common Prefix (LeetCode 14)](https://leetcode.com/problems/longest-common-prefix/)

### Problem Statement

Write a function to find the **longest common prefix** string amongst an array of strings. If there is no common prefix, return an **empty string** `""`.

**Examples:**

| Input | Output | Reason |
|-------|--------|--------|
| `["flower","flow","flight"]` | `"fl"` | All start with "fl"; "flo" fails at "flight" |
| `["dog","racecar","car"]` | `""` | No common starting character |
| `["interview","inter","internal"]` | `"inter"` | All share the prefix "inter" |
| `["alone"]` | `"alone"` | Single string — the whole string is the prefix |

---

### Approach 1: Brute Force (Horizontal Scanning)

Start by assuming the first string is the common prefix. Then compare this candidate prefix with each subsequent string and **shrink** it from the right until it matches the start of that string. If the prefix ever becomes empty, return `""` immediately.

#### The Math/Logic Behind It

The longest common prefix can only be **as long as the shortest string** in the array. We progressively narrow the prefix by checking whether each string starts with the current candidate, trimming one character from the end when it doesn't.

**Dry Run for strs = ["flower", "flow", "flight"]:**

| Step | Current Prefix | Compare With | Starts With? | Updated Prefix |
|------|----------------|--------------|--------------|----------------|
| Init | `"flower"` | — | — | `"flower"` |
| 1 | `"flower"` | `"flow"` | No | Trim → `"flowe"` → `"flow"` → check: `"flow".startsWith("flow")` ✅ | `"flow"` |
| 2 | `"flow"` | `"flight"` | No | Trim → `"flo"` → No, `"fl"` → check: `"flight".startsWith("fl")` ✅ | `"fl"` |
| Done | `"fl"` | — | — | Return `"fl"` ✅ |

```java
public class LongestCommonPrefix {
    public static String longestCommonPrefixBrute(String[] strs) {
        if (strs == null || strs.length == 0) return ""; // Edge case: empty input

        // Start with the first string as the assumed prefix
        String prefix = strs[0];

        // Compare the prefix with every other string
        for (int i = 1; i < strs.length; i++) {
            // Shrink the prefix until the current string starts with it
            while (!strs[i].startsWith(prefix)) {
                // Remove the last character from the prefix
                prefix = prefix.substring(0, prefix.length() - 1);

                // If prefix becomes empty, there's no common prefix at all
                if (prefix.isEmpty()) return "";
            }
        }

        return prefix; // What remains is the longest common prefix
    }

    public static void main(String[] args) {
        System.out.println(longestCommonPrefixBrute(new String[]{"flower","flow","flight"}));       // "fl"
        System.out.println(longestCommonPrefixBrute(new String[]{"dog","racecar","car"}));           // ""
        System.out.println(longestCommonPrefixBrute(new String[]{"interview","inter","internal"}));  // "inter"
        System.out.println(longestCommonPrefixBrute(new String[]{"alone"}));                         // "alone"
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(S)` where S = total number of characters across all strings — in the worst case (all strings identical), we compare every character in every string.
* **Space Complexity:** `O(1)` — we only store the `prefix` variable; no extra arrays are created (substring creates new Strings, but the prefix only shrinks).

---

### Approach 2: Optimal (Vertical Scanning — Column by Column)

Instead of comparing full strings, scan **character by character** at each position (column). For each column index `j`, check if all strings have the **same character at position `j`**. Stop as soon as any string is shorter than `j` or has a different character. The prefix is everything up to (not including) that stopping point.

#### The Math/Logic Behind It

Vertical scanning terminates as soon as a mismatch is found — it doesn't need to build and shrink a prefix string. This avoids redundant work in cases where the common prefix is short but individual strings are long.

**Dry Run for strs = ["flower", "flow", "flight"]:**

| Column j | strs[0][j] | strs[1][j] | strs[2][j] | All Same? | Action |
|----------|-----------|-----------|-----------|-----------|--------|
| 0 | 'f' | 'f' | 'f' | ✅ Yes | Continue |
| 1 | 'l' | 'l' | 'l' | ✅ Yes | Continue |
| 2 | 'o' | 'o' | 'i' | ❌ No  | Stop → return `strs[0].substring(0, 2)` = `"fl"` ✅ |

```java
public class LongestCommonPrefix {
    public static String longestCommonPrefixOptimal(String[] strs) {
        if (strs == null || strs.length == 0) return "";

        // Use the first string as the reference for column scanning
        // (the prefix cannot be longer than the first string)
        for (int j = 0; j < strs[0].length(); j++) {
            char currentChar = strs[0].charAt(j); // The character we expect at column j

            // Check this column character across all other strings
            for (int i = 1; i < strs.length; i++) {
                // Stop if: (1) string i is shorter than j, OR (2) char at j differs
                if (j >= strs[i].length() || strs[i].charAt(j) != currentChar) {
                    // The common prefix ends just before column j
                    return strs[0].substring(0, j);
                }
            }
            // All strings matched at column j — continue to the next column
        }

        // All columns of strs[0] matched every other string → strs[0] is the prefix
        return strs[0];
    }

    public static void main(String[] args) {
        System.out.println(longestCommonPrefixOptimal(new String[]{"flower","flow","flight"}));       // "fl"
        System.out.println(longestCommonPrefixOptimal(new String[]{"dog","racecar","car"}));           // ""
        System.out.println(longestCommonPrefixOptimal(new String[]{"interview","inter","internal"}));  // "inter"
        System.out.println(longestCommonPrefixOptimal(new String[]{"alone"}));                         // "alone"
    }
}
```

**Complexity Analysis:**
* **Time Complexity:** `O(S)` where S = total number of characters across all strings — in the worst case (all strings identical), every character is checked; in the best case (first column differs), only N characters are checked.
* **Space Complexity:** `O(1)` — only `j`, `i`, and `currentChar` variables are used; no extra arrays.

### Teaching Tips
1. **Draw the "vertical scanning" as a grid on the board:** Write all the strings aligned vertically (like columns in a table). Draw a vertical line that moves right column by column. Students immediately see which column causes the first mismatch. This visualization makes the algorithm obvious.
2. **Compare with horizontal scanning:** Horizontal starts with a big prefix and shrinks it. Vertical starts small and stops early. Ask: "Which would be faster if the answer is just 1 character long?" (Vertical — it stops after the second column. Horizontal might scan many characters first.)
3. **Edge cases to walk through in class:**
   - All strings are identical → returns entire first string.
   - One empty string in the array → immediately returns `""`.
   - Single string → returns the string itself.
   These edge cases train students to think carefully about constraints.

---

# Class 4 Homework

### Easy (Mandatory)
1. [Reverse Words in a String III (LeetCode 557)](https://leetcode.com/problems/reverse-words-in-a-string-iii/) — Reverse each word in a sentence while preserving whitespace and word order
2. [To Lower Case (LeetCode 709)](https://leetcode.com/problems/to-lower-case/) — Implement your own `toLowerCase()` function without using the built-in method
3. [Detect Capital (LeetCode 520)](https://leetcode.com/problems/detect-capital/) — Determine if the capital usage in a word is correct (all caps, no caps, or only first letter cap)

### Medium (Recommended)
1. [Group Anagrams (LeetCode 49)](https://leetcode.com/problems/group-anagrams/) — Given an array of strings, group all anagrams together using sorting or frequency-map as the key
2. [Longest Substring Without Repeating Characters (LeetCode 3)](https://leetcode.com/problems/longest-substring-without-repeating-characters/) — Use a sliding window + HashSet to find the longest substring with all unique characters
3. [Count and Say (LeetCode 38)](https://leetcode.com/problems/count-and-say/) — Generate the n-th term of the "Count and Say" sequence using StringBuilder for efficient string building

### Challenge Problem (Optional)
[Minimum Window Substring (LeetCode 76)](https://leetcode.com/problems/minimum-window-substring/) — Find the smallest substring of `s` that contains all characters of string `t`, combining frequency maps with a sliding window — a classic hard-level string problem
