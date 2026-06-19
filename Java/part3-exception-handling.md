# Backend Engineer Study Plan - Exception Handling

**Focus Areas:** Exception Handling, Try-Catch-Finally, throw vs throws, Custom Exceptions, Best Practices

---

## 📋 Table of Contents
1. [Exception Handling Overview](#-exception-handling-overview)
2. [Exception Hierarchy](#-exception-hierarchy)
3. [Types of Exceptions](#-types-of-exceptions)
4. [Try-Catch-Finally](#-try-catch-finally)
5. [Try-with-Resources](#-try-with-resources-java-7)
6. [throw vs throws](#-throw-vs-throws)
7. [Custom Exceptions](#-custom-exceptions)
8. [Exception Methods](#-exception-methods)
9. [Exception Propagation](#-exception-propagation)
10. [Common Exceptions Explained](#-common-exceptions-explained)
11. [Best Practices](#-best-practices)
12. [Coding Practice](#-coding-practice)
13. [Interview Questions](#-interview-questions-practice-aloud)
14. [Homework Checklist](#-homework-checklist)
15. [Exception Handling Cheatsheet](#-exception-handling-cheatsheet-one-page)

---

## 🎯 Exception Handling Overview

### What is an Exception?

**Definition:** An exception is an **unwanted or unexpected event** that occurs during program execution and **disrupts the normal flow** of instructions.

**Real-World Analogy:**

Imagine you're driving to work:
- **Normal flow**: Start car → Drive → Reach office
- **Exception occurs**: Flat tire on the way 🚗💥
- **Exception handling**: Stop, change tire, continue driving

Without exception handling, your program would **crash** (like abandoning the car). With exception handling, your program **handles the issue and continues** (like fixing the tire).

---

### Why Exception Handling?

#### ❌ Without Exception Handling:

```java
public class NoExceptionHandling {
    public static void main(String[] args) {
        System.out.println("Program started");
        
        int result = 10 / 0;  // 💥 Program crashes here!
        
        System.out.println("This line will NEVER execute");
        System.out.println("Program ended");
    }
}
```

**Output:**
```
Program started
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at NoExceptionHandling.main(NoExceptionHandling.java:5)
```

**Problem:** Program terminates abruptly. Remaining code doesn't execute.

---

#### ✅ With Exception Handling:

```java
public class WithExceptionHandling {
    public static void main(String[] args) {
        System.out.println("Program started");
        
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            System.out.println("Error: Cannot divide by zero!");
        }
        
        System.out.println("This line WILL execute");
        System.out.println("Program ended gracefully");
    }
}
```

**Output:**
```
Program started
Error: Cannot divide by zero!
This line WILL execute
Program ended gracefully
```

**Benefit:** Program continues to execute despite the error! ✨

---

### Benefits of Exception Handling

| Benefit | Description | Example |
|---------|-------------|---------|
| **1. Normal Flow Maintained** | Program continues after handling exception | File not found? Use default config |
| **2. Separate Error Handling** | Error code separate from business logic | Clean, readable code |
| **3. Categorize Errors** | Different exceptions handled differently | Network error vs DB error |
| **4. Meaningful Messages** | Provide helpful error information | "Invalid email format" |
| **5. Graceful Degradation** | System fails gracefully, not abruptly | Show error page vs crash |
| **6. Resource Cleanup** | Ensures resources are released (finally) | Close file connections |

---

### Exception vs Error

**Critical Concept:** Not all problems are the same!

| Feature | Exception | Error |
|---------|-----------|-------|
| **Definition** | Unexpected condition in program | Serious problem in JVM |
| **Recoverable?** | ✅ Yes (can be handled) | ❌ No (should not catch) |
| **Caused by** | Application logic | JVM or system issues |
| **Examples** | IOException, SQLException | OutOfMemoryError, StackOverflowError |
| **Should handle?** | ✅ Yes (try-catch) | ❌ No (fix code/config) |
| **Superclass** | `Exception` | `Error` |

**Visual Representation:**

```
Problem occurs:

Is it recoverable?
    ├─ YES → Exception (handle it!)
    │         └─ IOException, SQLException
    │
    └─ NO → Error (fix the root cause!)
              └─ OutOfMemoryError, StackOverflowError
```

---

### Exception Handling Keywords

Java provides **5 keywords** for exception handling:

| Keyword | Purpose | Location | Example |
|---------|---------|----------|---------|
| **try** | Contains code that might throw exception | Block | `try { code }` |
| **catch** | Handles the exception | After try | `catch (Exception e) {}` |
| **finally** | Always executes (cleanup code) | After catch | `finally { cleanup }` |
| **throw** | Manually throw an exception | Inside method | `throw new Exception()` |
| **throws** | Declare exceptions method might throw | Method signature | `void method() throws Exception` |

---

## 🎯 Exception Hierarchy

### Complete Exception Hierarchy

```
java.lang.Object
       |
   Throwable  ← Root of exception hierarchy
       |
       ├───────────────────┬───────────────────┐
       |                   |                   |
    Error            Exception           RuntimeException
  (Unchecked)        (Checked)            (Unchecked)
       |                   |                   |
       |                   |                   |
OutOfMemoryError    IOException      NullPointerException
StackOverflowError  SQLException     ArithmeticException
VirtualMachineError ClassNotFoundException  ArrayIndexOutOfBoundsException
                    FileNotFoundException  NumberFormatException
                    InterruptedException   IllegalArgumentException
                                          ClassCastException
```

---

### Visual Breakdown

```
                    Throwable (Root)
                        |
        ┌───────────────┴───────────────┐
        |                               |
     Error                         Exception
  (Unchecked)                          |
        |                   ┌───────────┴─────────┐
        |                   |                     |
  OutOfMemoryError    Checked Exceptions   RuntimeException
  StackOverflowError        |                (Unchecked)
                            |                     |
                      IOException          NullPointerException
                      SQLException         ArithmeticException
                      FileNotFoundException
```

---

### Key Classes Explained

#### 1️⃣ Throwable (Parent of All)

**Purpose:** Root class for all exceptions and errors.

**Key Methods:**
- `getMessage()` - Returns error message
- `printStackTrace()` - Prints stack trace
- `toString()` - Returns string representation
- `getCause()` - Returns cause of exception

---

#### 2️⃣ Exception (Checked Exceptions)

**Characteristics:**
- Must be handled or declared
- Checked at compile time
- Recoverable conditions
- Extend `Exception` class

**Common Checked Exceptions:**
- `IOException` - Input/output operations fail
- `SQLException` - Database access errors
- `ClassNotFoundException` - Class not found
- `FileNotFoundException` - File not found
- `InterruptedException` - Thread interrupted

---

#### 3️⃣ RuntimeException (Unchecked Exceptions)

**Characteristics:**
- No need to handle or declare
- Occur at runtime
- Programming errors
- Extend `RuntimeException` class

**Common Runtime Exceptions:**
- `NullPointerException` - Null reference used
- `ArithmeticException` - Invalid arithmetic (divide by zero)
- `ArrayIndexOutOfBoundsException` - Invalid array index
- `NumberFormatException` - Invalid number format
- `IllegalArgumentException` - Invalid method argument
- `ClassCastException` - Invalid type casting

---

#### 4️⃣ Error (JVM Problems)

**Characteristics:**
- Should NOT be caught
- Serious JVM issues
- Irrecoverable
- Extend `Error` class

**Common Errors:**
- `OutOfMemoryError` - Heap memory exhausted
- `StackOverflowError` - Stack memory exhausted (infinite recursion)
- `VirtualMachineError` - JVM crashed
- `AssertionError` - Assertion failed

---

## 🎯 Types of Exceptions

Java exceptions are categorized into **3 types**:

```
Exceptions
    ├── Checked Exceptions (Compile-time)
    ├── Unchecked Exceptions (Runtime)
    └── Errors (JVM issues)
```

---

### 1️⃣ Checked Exceptions (Compile-time Exceptions)

#### 🎯 What are Checked Exceptions?

**Definition:** Exceptions that are **checked by the compiler at compile time**.

**Key Characteristics:**
- ✅ Checked at **compile time**
- ✅ Must be **handled or declared**
- ✅ Recoverable conditions
- ✅ Extend `Exception` class (but not `RuntimeException`)

**Real-World Analogy:**

Imagine airport security (compiler):
- **Checked Exception**: Security checks your boarding pass (compiler checks exception handling)
- If you don't have a boarding pass → Can't proceed (compilation error)
- Solution: Either handle it (buy ticket) or declare it (show existing ticket)

---

#### 🔑 Why "Checked"?

**Compiler forces you to handle these exceptions!**

**Example (Will NOT Compile):**

```java
import java.io.*;

public class CheckedExceptionDemo {
    public static void main(String[] args) {
        // ❌ Compilation Error: Unhandled IOException
        FileReader fr = new FileReader("file.txt");
    }
}
```

**Compiler Error:**
```
Error: Unreported exception java.io.IOException; must be caught or declared to be thrown
```

---

#### ✅ Solution 1: Handle with try-catch

```java
import java.io.*;

public class CheckedExceptionDemo {
    public static void main(String[] args) {
        // ✅ Handled with try-catch
        try {
            FileReader fr = new FileReader("file.txt");
            System.out.println("File opened successfully");
        } catch (IOException e) {
            System.out.println("File not found: " + e.getMessage());
        }
    }
}
```

---

#### ✅ Solution 2: Declare with throws

```java
import java.io.*;

public class CheckedExceptionDemo {
    // ✅ Declared with throws (caller must handle)
    public static void main(String[] args) throws IOException {
        FileReader fr = new FileReader("file.txt");
        System.out.println("File opened successfully");
    }
}
```

---

#### 📋 Common Checked Exceptions

| Exception | When it Occurs | Example |
|-----------|----------------|---------|
| **IOException** | Input/output operation fails | Reading from closed stream |
| **FileNotFoundException** | File not found at specified path | Opening non-existent file |
| **SQLException** | Database access error | Invalid SQL query |
| **ClassNotFoundException** | Class not found at runtime | Loading class dynamically |
| **InterruptedException** | Thread interrupted while waiting | Thread.sleep() interrupted |
| **ParseException** | String parsing fails | Parsing invalid date string |

---

#### 🎨 Visual Flow: Checked Exception

```
Compiler checks code:

FileReader fr = new FileReader("file.txt");
        ↓
Does this throw checked exception?
        ↓
    YES (IOException)
        ↓
Is it handled or declared?
    ├─ NO  → ❌ Compilation Error
    └─ YES → ✅ Compilation Success
            ├─ try-catch (handled)
            └─ throws (declared)
```

---

### 2️⃣ Unchecked Exceptions (Runtime Exceptions)

#### 🎯 What are Unchecked Exceptions?

**Definition:** Exceptions that are **NOT checked by the compiler**. They occur at **runtime** due to **programming errors**.

**Key Characteristics:**
- ❌ NOT checked at compile time
- ❌ No need to handle or declare (optional)
- ⚠️ Occur at runtime
- ✅ Extend `RuntimeException` class
- 💡 Indicate programming bugs

**Real-World Analogy:**

Imagine driving a car:
- **Unchecked Exception**: Driving without checking fuel gauge
- Car stops mid-journey (runtime) because fuel ran out
- Could have been prevented by checking beforehand (coding carefully)

---

#### 🔑 Why "Unchecked"?

**Compiler does NOT force you to handle these exceptions.**

**Example (Compiles Fine):**

```java
public class UncheckedExceptionDemo {
    public static void main(String[] args) {
        // ✅ Compiles fine (no error)
        int result = 10 / 0;  // But crashes at runtime!
        
        String str = null;
        str.length();  // Also crashes at runtime!
    }
}
```

**Compilation:** ✅ Success  
**Runtime:** ❌ Crash!

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
```

---

#### 📋 Common Unchecked Exceptions (Detailed)

---

##### 1. NullPointerException (NPE) - Most Common!

**Cause:** Trying to use a reference that points to null.

```java
public class NPEDemo {
    public static void main(String[] args) {
        String name = null;
        
        // ❌ Crashes - cannot call method on null
        System.out.println(name.length());
        
        // ✅ Solution: Check for null
        if (name != null) {
            System.out.println(name.length());
        }
    }
}
```

**Common Scenarios:**
- Calling method on null object
- Accessing field of null object
- Getting length of null array

---

##### 2. ArithmeticException

**Cause:** Invalid arithmetic operation.

```java
public class ArithmeticExceptionDemo {
    public static void main(String[] args) {
        int a = 10;
        int b = 0;
        
        // ❌ Crashes - division by zero
        int result = a / b;
        
        // ✅ Solution: Check divisor
        if (b != 0) {
            result = a / b;
        } else {
            System.out.println("Cannot divide by zero");
        }
    }
}
```

**Note:** Only for integer division! `10.0 / 0.0` returns `Infinity` (no exception).

---

##### 3. ArrayIndexOutOfBoundsException

**Cause:** Accessing array with invalid index (negative or >= length).

```java
public class ArrayIndexDemo {
    public static void main(String[] args) {
        int[] arr = {10, 20, 30};
        
        // ❌ Crashes - index 5 doesn't exist (valid: 0-2)
        System.out.println(arr[5]);
        
        // ✅ Solution: Check bounds
        int index = 5;
        if (index >= 0 && index < arr.length) {
            System.out.println(arr[index]);
        } else {
            System.out.println("Invalid index");
        }
    }
}
```

---

##### 4. NumberFormatException

**Cause:** Converting invalid string to number.

```java
public class NumberFormatDemo {
    public static void main(String[] args) {
        String str = "abc";
        
        // ❌ Crashes - "abc" is not a valid number
        int num = Integer.parseInt(str);
        
        // ✅ Solution: Use try-catch
        try {
            num = Integer.parseInt(str);
        } catch (NumberFormatException e) {
            System.out.println("Invalid number format");
        }
    }
}
```

---

##### 5. ClassCastException

**Cause:** Invalid type casting.

```java
public class ClassCastDemo {
    public static void main(String[] args) {
        Object obj = "Hello";
        
        // ❌ Crashes - String cannot be cast to Integer
        Integer num = (Integer) obj;
        
        // ✅ Solution: Check type before casting
        if (obj instanceof Integer) {
            Integer num = (Integer) obj;
        } else {
            System.out.println("Not an Integer");
        }
    }
}
```

---

##### 6. IllegalArgumentException

**Cause:** Method receives invalid argument.

```java
public class IllegalArgumentDemo {
    public static void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("Invalid age: " + age);
        }
        System.out.println("Age set to: " + age);
    }
    
    public static void main(String[] args) {
        setAge(25);   // ✅ Valid
        setAge(-5);   // ❌ Throws IllegalArgumentException
    }
}
```

---

#### 🎨 Visual: Checked vs Unchecked

```
CHECKED EXCEPTIONS               UNCHECKED EXCEPTIONS
──────────────────               ────────────────────
Compile Time ✓                   Runtime Only ✗
Must Handle ✓                    Optional Handling ✗
Recoverable ✓                    Programming Error ⚠️
IOException                      NullPointerException
SQLException                     ArithmeticException

Example:                         Example:
FileReader fr =                  int x = 10 / 0;
  new FileReader("x.txt");       (Compiles, crashes later)
(Won't compile without handling)
```

---

### 3️⃣ Errors (JVM Issues)

#### 🎯 What are Errors?

**Definition:** Serious problems that **should NOT be caught** by applications. Indicate **JVM or system-level issues**.

**Key Characteristics:**
- ❌ Should NOT be caught
- ⚠️ Indicate serious problems
- 🔴 Irrecoverable (usually)
- ✅ Extend `Error` class

---

#### 📋 Common Errors

##### 1. OutOfMemoryError

**Cause:** Heap memory exhausted.

```java
public class OutOfMemoryDemo {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        
        // ❌ Keep adding until memory runs out
        while (true) {
            list.add(1);  // Eventually throws OutOfMemoryError
        }
    }
}
```

**Solution:**
- Increase heap size: `-Xmx2g`
- Fix memory leak in code

---

##### 2. StackOverflowError

**Cause:** Stack memory exhausted (usually infinite recursion).

```java
public class StackOverflowDemo {
    public static void recursiveMethod() {
        recursiveMethod();  // No base case!
    }
    
    public static void main(String[] args) {
        recursiveMethod();  // ❌ Throws StackOverflowError
    }
}
```

**Solution:**
- Add base case to recursion
- Increase stack size: `-Xss2m`

---

### 🔥 Comparison Table: All Three Types

| Feature | Checked Exception | Unchecked Exception | Error |
|---------|-------------------|---------------------|-------|
| **Checked by compiler** | ✅ Yes | ❌ No | ❌ No |
| **Must handle/declare** | ✅ Yes | ❌ No | ❌ No |
| **When occurs** | Compile time check | Runtime | Runtime |
| **Superclass** | Exception | RuntimeException | Error |
| **Recoverable** | ✅ Yes | ✅ Usually | ❌ Usually not |
| **Caused by** | External factors | Programming errors | JVM/System issues |
| **Should catch** | ✅ Yes | ✅ Optional | ❌ No |
| **Examples** | IOException<br/>SQLException | NullPointerException<br/>ArithmeticException | OutOfMemoryError<br/>StackOverflowError |

---

## 🎯 Try-Catch-Finally

### Basic Syntax

```java
try {
    // Code that might throw exception
} catch (ExceptionType e) {
    // Handle the exception
} finally {
    // Always executes (cleanup code)
}
```

---

### How Try-Catch-Finally Works

#### 🎨 Visual Flow

```
Program Flow:
    ↓
┌─────────────┐
│  try block  │
└──────┬──────┘
       │
       ├─ Exception occurs?
       │       │
       │  ┌────┴────┐
       │  NO      YES
       │  │        │
       │  │   ┌────────────┐
       │  │   │ catch block│
       │  │   └────────────┘
       │  │        │
       └──┴────────┘
              │
       ┌──────────────┐
       │finally block │ ← ALWAYS executes
       └──────────────┘
              │
       Continue program
```

---

### Example 1: Basic Try-Catch

```java
public class TryCatchDemo {
    public static void main(String[] args) {
        System.out.println("Program started");
        
        try {
            int result = 10 / 0;  // ArithmeticException
            System.out.println("Result: " + result);
        } catch (ArithmeticException e) {
            System.out.println("Error: Cannot divide by zero");
        }
        
        System.out.println("Program continues...");
        System.out.println("Program ended");
    }
}
```

**Output:**
```
Program started
Error: Cannot divide by zero
Program continues...
Program ended
```

---

### Example 2: Multiple Catch Blocks

**Important:** Catch blocks are checked **top to bottom**. First matching catch executes.

```java
public class MultipleCatchDemo {
    public static void main(String[] args) {
        try {
            int[] arr = {1, 2, 3};
            System.out.println(arr[5]);  // ArrayIndexOutOfBoundsException
            
            int result = 10 / 0;  // This line won't execute
            
            String str = null;
            str.length();  // This line won't execute either
            
        } catch (ArithmeticException e) {
            System.out.println("Arithmetic error: " + e.getMessage());
            
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Array index error: " + e.getMessage());
            
        } catch (NullPointerException e) {
            System.out.println("Null pointer error: " + e.getMessage());
            
        } catch (Exception e) {
            // Generic catch - catches all other exceptions
            System.out.println("Some other error: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Array index error: Index 5 out of bounds for length 3
```

**Note:** Only ONE catch block executes (the first matching one).

---

### ⚠️ Important Rules for Multiple Catch Blocks

#### Rule 1: Order Matters (Specific to General)

```java
// ❌ WRONG - Won't compile!
try {
    // code
} catch (Exception e) {          // Generic exception first
    // handle
} catch (IOException e) {        // Specific exception after
    // This is unreachable code! Compilation error!
}
```

**Compiler Error:**
```
Exception java.io.IOException has already been caught
```

---

```java
// ✅ CORRECT - Specific to general
try {
    // code
} catch (IOException e) {          // Specific exception first
    System.out.println("IO Error");
} catch (Exception e) {            // Generic exception last
    System.out.println("General Error");
}
```

**Rule:** **More specific exceptions BEFORE more general ones.**

---

#### Rule 2: Parent-Child Relationship

```java
Exception (Parent)
    ├── IOException (Child)
    └── SQLException (Child)
```

**Catch order:**
```java
// ✅ CORRECT
catch (IOException e) { }       // Child first
catch (Exception e) { }         // Parent last

// ❌ WRONG
catch (Exception e) { }         // Parent first (catches everything!)
catch (IOException e) { }       // Unreachable!
```

---

### Example 3: Multi-catch (Java 7+)

**Catch multiple exceptions in ONE catch block** (when handling is same).

#### Before Java 7 (Duplicate Code):

```java
try {
    // code
} catch (IOException e) {
    System.out.println("Error: " + e.getMessage());
    e.printStackTrace();
} catch (SQLException e) {
    System.out.println("Error: " + e.getMessage());
    e.printStackTrace();
}
```

---

#### Java 7+ (Multi-catch - Cleaner):

```java
try {
    // code
} catch (IOException | SQLException e) {  // Multi-catch with | operator
    System.out.println("Error: " + e.getMessage());
    e.printStackTrace();
}
```

**Benefits:**
- ✅ Less code duplication
- ✅ Cleaner and more maintainable
- ✅ Easier to read

**Rules:**
- Exception types must be separated by `|`
- Cannot be in parent-child relationship
- Variable `e` is implicitly **final**

---

### Example 4: Finally Block

**Purpose:** Code that **ALWAYS executes**, regardless of exception occurrence.

**Use Cases:**
- Close resources (files, connections, streams)
- Release locks
- Cleanup operations
- Logging

---

#### When Finally Executes:

```
Scenario 1: No exception
    try → finally → continue

Scenario 2: Exception caught
    try → catch → finally → continue

Scenario 3: Exception NOT caught
    try → finally → program terminates

Scenario 4: return in try
    try (return) → finally → return

Scenario 5: return in catch
    catch (return) → finally → return
```

**Key Point:** Finally **ALWAYS** executes (except System.exit() or JVM crash).

---

#### Example: Finally Always Executes

```java
public class FinallyDemo {
    public static void main(String[] args) {
        System.out.println("Program started");
        
        try {
            System.out.println("Inside try");
            int result = 10 / 0;
            System.out.println("After division (won't execute)");
        } catch (ArithmeticException e) {
            System.out.println("Inside catch");
        } finally {
            System.out.println("Inside finally (ALWAYS executes)");
        }
        
        System.out.println("Program ended");
    }
}
```

**Output:**
```
Program started
Inside try
Inside catch
Inside finally (ALWAYS executes)
Program ended
```

---

#### Example: Finally with Return

**Surprising behavior!**

```java
public class FinallyWithReturn {
    public static int testFinally() {
        try {
            System.out.println("Inside try");
            return 1;  // Return statement
        } catch (Exception e) {
            System.out.println("Inside catch");
            return 2;
        } finally {
            System.out.println("Inside finally");
            // Finally executes BEFORE return!
        }
    }
    
    public static void main(String[] args) {
        int result = testFinally();
        System.out.println("Result: " + result);
    }
}
```

**Output:**
```
Inside try
Inside finally
Result: 1
```

**Important:** Finally executes **before** the return statement!

---

#### ⚠️ Finally Does NOT Execute in These Cases:

```java
// Case 1: System.exit()
try {
    System.exit(0);  // JVM terminates
} finally {
    System.out.println("Won't execute");  // ❌ Doesn't execute
}

// Case 2: JVM Crash
try {
    // Code that crashes JVM
} finally {
    System.out.println("Won't execute");  // ❌ Doesn't execute
}

// Case 3: Infinite Loop in try
try {
    while(true) { }  // Never exits
} finally {
    System.out.println("Won't execute");  // ❌ Doesn't execute
}
```

---

### Example 5: Try-Catch-Finally Complete Example

```java
import java.io.*;

public class CompleteTryCatchFinally {
    public static void main(String[] args) {
        FileReader fr = null;
        BufferedReader br = null;
        
        try {
            System.out.println("Opening file...");
            fr = new FileReader("data.txt");
            br = new BufferedReader(fr);
            
            String line = br.readLine();
            System.out.println("First line: " + line);
            
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
            
        } catch (IOException e) {
            System.out.println("IO Error: " + e.getMessage());
            
        } finally {
            // Cleanup: Close resources
            System.out.println("Closing resources...");
            try {
                if (br != null) br.close();
                if (fr != null) fr.close();
            } catch (IOException e) {
                System.out.println("Error closing resources");
            }
        }
        
        System.out.println("Program ended");
    }
}
```

---

### 🎨 Try-Catch-Finally Flow Chart

```
              START
                ↓
        ┌──────────────┐
        │  try block   │
        │    starts    │
        └───────┬──────┘
                │
         Exception thrown?
                │
        ┌───────┴────────┐
       NO               YES
        │                 │
        │         ┌───────────────┐
        │         │ Find matching │
        │         │ catch block?  │
        │         └───────┬───────┘
        │                 │
        │         ┌───────┴────────┐
        │        YES              NO
        │         │                 │
        │   ┌──────────┐            │
        │   │  catch   │            │
        │   │ executes │            │
        │   └────┬─────┘            │
        │        │                  │
        └────────┴──────────────────┘
                 │
        ┌────────────────┐
        │ finally block  │ ← ALWAYS
        │   executes     │
        └────────┬───────┘
                 │
                END
```

---

### 🔥 Interview Questions: Try-Catch-Finally

**Q1: Can we have try without catch?**
- **A:** Yes, if finally is present: `try { } finally { }`
- Also with try-with-resources: `try (resource) { }`

**Q2: Can we have try without finally?**
- **A:** Yes, if catch is present: `try { } catch { }`

**Q3: Can we have catch without try?**
- **A:** No! catch must follow try.

**Q4: Can we have finally without try?**
- **A:** No! finally must follow try.

**Q5: Can we have multiple catch blocks?**
- **A:** Yes! Order: specific to general.

**Q6: Can we have multiple finally blocks?**
- **A:** No! Only ONE finally block allowed.

**Q7: Which executes first: return or finally?**
- **A:** finally executes **before** return.

**Q8: Can we write code after finally?**
- **A:** Yes! Code after finally executes normally.

---

## 🎯 Try-with-Resources (Java 7+)

### What is Try-with-Resources?

**Definition:** Automatic resource management feature that **automatically closes resources** without needing explicit finally block.

**Why Introduced?**
- Prevents resource leaks
- Reduces boilerplate code
- Cleaner and safer code
- Compiler ensures resources are closed

---

### Problem: Traditional Approach (Before Java 7)

**Lots of boilerplate code!**

```java
import java.io.*;

public class TraditionalApproach {
    public static void main(String[] args) {
        FileReader fr = null;
        BufferedReader br = null;
        
        try {
            fr = new FileReader("data.txt");
            br = new BufferedReader(fr);
            String line = br.readLine();
            System.out.println(line);
            
        } catch (IOException e) {
            e.printStackTrace();
            
        } finally {
            // 😫 Tedious cleanup code
            try {
                if (br != null) br.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            
            try {
                if (fr != null) fr.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**Problems:**
- Too much code for simple task
- Easy to forget to close resources
- Nested try-catch in finally
- Error-prone

---

### Solution: Try-with-Resources (Java 7+)

**Much cleaner!**

```java
import java.io.*;

public class TryWithResourcesDemo {
    public static void main(String[] args) {
        // ✅ Resources auto-closed!
        try (FileReader fr = new FileReader("data.txt");
             BufferedReader br = new BufferedReader(fr)) {
            
            String line = br.readLine();
            System.out.println(line);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        // No finally needed! Resources automatically closed here.
    }
}
```

**Benefits:**
- ✅ Cleaner code
- ✅ Resources automatically closed
- ✅ No need for finally block
- ✅ Closed in reverse order (last opened, first closed)
- ✅ Prevents resource leaks

---

### Syntax

```java
try (Resource1 r1 = new Resource1();
     Resource2 r2 = new Resource2();
     Resource3 r3 = new Resource3()) {
    
    // Use resources
    
} catch (Exception e) {
    // Handle exception
}
// Resources auto-closed in reverse order: r3 → r2 → r1
```

---

### Requirements for Try-with-Resources

**Resource must implement:**
- `AutoCloseable` interface (Java 7+)
- `Closeable` interface (extends AutoCloseable)

```java
public interface AutoCloseable {
    void close() throws Exception;
}

public interface Closeable extends AutoCloseable {
    void close() throws IOException;
}
```

**Common AutoCloseable Resources:**
- File I/O: FileReader, FileWriter, BufferedReader, BufferedWriter
- Streams: FileInputStream, FileOutputStream
- Database: Connection, Statement, ResultSet
- Scanner
- PrintWriter

---

### Example 1: Multiple Resources

```java
import java.io.*;

public class MultipleResources {
    public static void main(String[] args) {
        // Multiple resources (closed in reverse order)
        try (FileReader fr = new FileReader("input.txt");
             BufferedReader br = new BufferedReader(fr);
             FileWriter fw = new FileWriter("output.txt");
             BufferedWriter bw = new BufferedWriter(fw)) {
            
            String line;
            while ((line = br.readLine()) != null) {
                bw.write(line);
                bw.newLine();
            }
            System.out.println("File copied successfully");
            
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
        // All resources auto-closed in order: bw → fw → br → fr
    }
}
```

---

### Example 2: Custom AutoCloseable Resource

```java
class MyResource implements AutoCloseable {
    public MyResource() {
        System.out.println("Resource opened");
    }
    
    public void doSomething() {
        System.out.println("Resource doing work");
    }
    
    @Override
    public void close() {
        System.out.println("Resource closed");
    }
}

public class CustomResourceDemo {
    public static void main(String[] args) {
        try (MyResource resource = new MyResource()) {
            resource.doSomething();
        }
        // close() automatically called here
        System.out.println("After try-with-resources");
    }
}
```

**Output:**
```
Resource opened
Resource doing work
Resource closed
After try-with-resources
```

---

### Example 3: With Exception Handling

```java
import java.io.*;

public class ExceptionInResource {
    public static void main(String[] args) {
        try (FileReader fr = new FileReader("nonexistent.txt");
             BufferedReader br = new BufferedReader(fr)) {
            
            String line = br.readLine();
            System.out.println(line);
            
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("IO Error: " + e.getMessage());
        }
    }
}
```

**Output:**
```
File not found: nonexistent.txt (The system cannot find the file specified)
```

---

### Java 9 Enhancement: Effectively Final Variables

**Before Java 9:** Must declare resource inside try

```java
// Java 7-8
try (FileReader fr = new FileReader("file.txt")) {
    // use fr
}
```

**Java 9+:** Can use effectively final variables

```java
// Java 9+
FileReader fr = new FileReader("file.txt");
try (fr) {  // ✅ Can use existing variable
    // use fr
}
// fr automatically closed
```

---

### 🎨 Traditional vs Try-with-Resources

```
TRADITIONAL APPROACH              TRY-WITH-RESOURCES
───────────────────              ──────────────────

FileReader fr = null;            try (FileReader fr = 
try {                                new FileReader("x.txt")) {
  fr = new FileReader("x.txt");
  // use fr                         // use fr
} finally {
  if (fr != null) {              } // Auto-closed!
    try {
      fr.close();
    } catch (IOException e) {}
  }
}

Lines of code: ~15               Lines of code: ~5
Boilerplate: ❌ Heavy            Boilerplate: ✅ Minimal
Error-prone: ❌ Yes              Error-prone: ✅ No
```

---

### 🔥 Interview Questions: Try-with-Resources

**Q1: What is try-with-resources?**
- **A:** Automatic resource management feature (Java 7+) that automatically closes resources implementing AutoCloseable.

**Q2: Which interface must resource implement?**
- **A:** `AutoCloseable` or `Closeable`

**Q3: In what order are resources closed?**
- **A:** Reverse order (last opened, first closed)

**Q4: Can we have catch/finally with try-with-resources?**
- **A:** Yes! Optional but allowed:
  ```java
  try (resource) {
  } catch (Exception e) {
  } finally {
  }
  ```

**Q5: What if exception occurs in close() method?**
- **A:** Exception is suppressed and can be retrieved using `getSuppressed()`

**Q6: Can we manually close resource in try block?**
- **A:** Yes, but not recommended. Auto-close will still execute.

---

## 🎯 throw vs throws

### Overview

Both are used in exception handling but serve **completely different purposes**.

```
throw  → THROWS an exception (action)
throws → DECLARES an exception (information)
```

---

### 1️⃣ throw Keyword

#### 🎯 What is throw?

**Purpose:** **Explicitly throw an exception** from your code.

**Key Characteristics:**
- Used **inside method body**
- Followed by **exception instance** (object)
- Can throw **only ONE exception** at a time
- Used to **manually trigger** an exception
- Used for **business logic validation**

---

#### Syntax

```java
throw new ExceptionType("error message");
```

---

#### Example 1: Basic throw

```java
public class ThrowDemo {
    public static void main(String[] args) {
        int age = 15;
        
        if (age < 18) {
            // Manually throw exception
            throw new IllegalArgumentException("Age must be 18 or above");
        }
        
        System.out.println("Welcome!");
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.IllegalArgumentException: Age must be 18 or above
```

---

#### Example 2: throw with try-catch

```java
public class ThrowWithCatch {
    public static void validateAge(int age) {
        if (age < 18) {
            throw new IllegalArgumentException("Age must be 18+");
        }
        System.out.println("Age is valid: " + age);
    }
    
    public static void main(String[] args) {
        try {
            validateAge(15);  // Throws exception
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
        
        System.out.println("Program continues...");
    }
}
```

**Output:**
```
Error: Age must be 18+
Program continues...
```

---

#### Example 3: throw Checked Exception

**Important:** If you throw checked exception, you MUST handle or declare it!

```java
import java.io.IOException;

public class ThrowCheckedException {
    // Method throws checked exception, so MUST declare with 'throws'
    public static void readFile() throws IOException {
        // Business logic check
        boolean fileExists = false;
        
        if (!fileExists) {
            throw new IOException("File not found");
        }
    }
    
    public static void main(String[] args) {
        try {
            readFile();
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

---

### 2️⃣ throws Keyword

#### 🎯 What is throws?

**Purpose:** **Declare** that a method might throw exceptions, so caller must handle them.

**Key Characteristics:**
- Used in **method signature** (declaration)
- Followed by **exception class name(s)**
- Can declare **multiple exceptions** (comma-separated)
- **Delegates** exception handling to caller
- Required for **checked exceptions**

---

#### Syntax

```java
returnType methodName() throws ExceptionType1, ExceptionType2 {
    // method body
}
```

---

#### Example 1: Single throws

```java
import java.io.*;

public class ThrowsDemo {
    // Method declares it might throw IOException
    public static void readFile() throws IOException {
        FileReader fr = new FileReader("data.txt");
        System.out.println("File opened");
    }
    
    public static void main(String[] args) {
        try {
            readFile();  // Caller must handle
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

---

#### Example 2: Multiple throws

```java
import java.io.*;
import java.sql.*;

public class MultipleThrows {
    // Declares multiple exceptions
    public static void processData() throws IOException, SQLException {
        // Might throw IOException
        FileReader fr = new FileReader("data.txt");
        
        // Might throw SQLException
        // Connection conn = DriverManager.getConnection("jdbc:...");
    }
    
    public static void main(String[] args) {
        try {
            processData();
        } catch (IOException e) {
            System.out.println("IO Error: " + e.getMessage());
        } catch (SQLException e) {
            System.out.println("DB Error: " + e.getMessage());
        }
    }
}
```

---

#### Example 3: Propagating Exception

**Caller can also declare throws (pass the buck!):**

```java
import java.io.*;

public class ExceptionPropagation {
    public static void method1() throws IOException {
        FileReader fr = new FileReader("file.txt");
    }
    
    public static void method2() throws IOException {
        method1();  // Propagate exception
    }
    
    public static void method3() throws IOException {
        method2();  // Propagate again
    }
    
    public static void main(String[] args) {
        try {
            method3();  // Finally handle here
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

**Flow:** method1 → method2 → method3 → main (handled)

---

### 🔥 throw vs throws Comparison

| Feature | throw | throws |
|---------|-------|--------|
| **Purpose** | Throw an exception | Declare possible exceptions |
| **Location** | Inside method body | Method signature |
| **Keyword type** | Statement | Clause |
| **Followed by** | Exception **instance** (object) | Exception **class name** |
| **Syntax** | `throw new Exception()` | `void method() throws Exception` |
| **Quantity** | One at a time | Multiple (comma-separated) |
| **Use case** | Explicitly create exception | Inform caller about exceptions |
| **Example** | `throw new IOException()` | `void read() throws IOException` |
| **Required for** | Manual exception creation | Checked exceptions (must declare) |

---

### Example: throw AND throws Together

**Most common pattern:**

```java
public class ThrowAndThrows {
    // 'throws' declares, 'throw' actually throws
    public static void validateAge(int age) throws Exception {
        if (age < 0) {
            throw new Exception("Age cannot be negative");  // throw
        }
        if (age < 18) {
            throw new Exception("Must be 18 or older");  // throw
        }
        System.out.println("Valid age: " + age);
    }
    
    public static void main(String[] args) {
        try {
            validateAge(-5);
        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Error: Age cannot be negative
```

---

### 🎨 Visual: throw vs throws

```
THROW                          THROWS
─────                          ──────

throw new Exception();         void method() throws Exception {
    ↓                              ↓
Creates exception              Declares exception
    ↓                              ↓
Inside method body             Method signature
    ↓                              ↓
Action                         Information
    ↓                              ↓
One at a time                  Multiple possible
    ↓                              ↓
Exception object               Exception class


TOGETHER:

void method() throws IOException {  ← throws (declaration)
    throw new IOException();        ← throw (action)
}
```

---

### 🔥 Interview Questions: throw vs throws

**Q1: Difference between throw and throws?**
- **throw**: Throws exception explicitly (inside method)
- **throws**: Declares exception (in method signature)

**Q2: Can we throw multiple exceptions using throw?**
- **A:** No! Only one at a time.

**Q3: Can we declare multiple exceptions using throws?**
- **A:** Yes! Comma-separated: `throws IOException, SQLException`

**Q4: Can we use throw without throws?**
- **A:** Yes! For unchecked exceptions.  
  No! For checked exceptions (must declare with throws).

**Q5: Can we use throws without throw?**
- **A:** Yes! If method calls another method that throws exception.

**Q6: What happens if we don't handle exception declared with throws?**
- **A:** Compilation error (for checked exceptions).

---

## 🎯 Custom Exceptions

### Why Create Custom Exceptions?

**Built-in exceptions are generic.** Sometimes you need **application-specific** exceptions.

**Benefits:**
1. ✅ **Meaningful Names**: `InvalidAgeException` vs generic `Exception`
2. ✅ **Business Logic Specific**: Represent domain-specific errors
3. ✅ **Better Error Handling**: Different exceptions for different scenarios
4. ✅ **Custom Fields/Methods**: Add extra context
5. ✅ **Clarity**: Code more readable and maintainable

---

### Real-World Examples:

| Generic | Custom (Better!) |
|---------|------------------|
| `Exception("Invalid input")` | `InvalidEmailException` |
| `Exception("Balance low")` | `InsufficientBalanceException` |
| `Exception("User not found")` | `UserNotFoundException` |
| `Exception("Payment failed")` | `PaymentProcessingException` |

---

### How to Create Custom Exception

**Two types:**

1. **Checked Exception**: Extend `Exception`
2. **Unchecked Exception**: Extend `RuntimeException`

---

### 1️⃣ Custom Checked Exception

**Extends Exception class.**

```java
// Custom Checked Exception
public class InvalidAgeException extends Exception {
    // Constructor with message
    public InvalidAgeException(String message) {
        super(message);
    }
    
    // Constructor with message and cause
    public InvalidAgeException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

---

#### Using Custom Checked Exception:

```java
public class CheckedExceptionDemo {
    public static void validateAge(int age) throws InvalidAgeException {
        if (age < 18) {
            throw new InvalidAgeException("Age must be 18 or above. Provided: " + age);
        }
        if (age > 150) {
            throw new InvalidAgeException("Invalid age. Must be less than 150");
        }
        System.out.println("Valid age: " + age);
    }
    
    public static void main(String[] args) {
        try {
            validateAge(15);  // Throws InvalidAgeException
        } catch (InvalidAgeException e) {
            System.out.println("Error: " + e.getMessage());
        }
        
        System.out.println("Program continues...");
    }
}
```

**Output:**
```
Error: Age must be 18 or above. Provided: 15
Program continues...
```

---

### 2️⃣ Custom Unchecked Exception

**Extends RuntimeException class.**

```java
// Custom Unchecked Exception
public class InsufficientBalanceException extends RuntimeException {
    private double balance;
    private double withdrawAmount;
    
    // Constructor with custom fields
    public InsufficientBalanceException(String message, double balance, double withdrawAmount) {
        super(message);
        this.balance = balance;
        this.withdrawAmount = withdrawAmount);
    }
    
    // Getter methods
    public double getBalance() {
        return balance;
    }
    
    public double getWithdrawAmount() {
        return withdrawAmount;
    }
}
```

---

#### Using Custom Unchecked Exception:

```java
public class BankAccount {
    private double balance;
    
    public BankAccount(double balance) {
        this.balance = balance;
    }
    
    public void withdraw(double amount) {
        if (amount > balance) {
            throw new InsufficientBalanceException(
                "Cannot withdraw. Insufficient balance!",
                balance,
                amount
            );
        }
        balance -= amount;
        System.out.println("Withdrawal successful. New balance: " + balance);
    }
    
    public static void main(String[] args) {
        BankAccount account = new BankAccount(1000);
        
        try {
            account.withdraw(500);   // ✅ Success
            account.withdraw(700);   // ❌ Throws exception
        } catch (InsufficientBalanceException e) {
            System.out.println("Error: " + e.getMessage());
            System.out.println("Your balance: " + e.getBalance());
            System.out.println("Tried to withdraw: " + e.getWithdrawAmount());
        }
    }
}
```

**Output:**
```
Withdrawal successful. New balance: 500.0
Error: Cannot withdraw. Insufficient balance!
Your balance: 500.0
Tried to withdraw: 700.0
```

---

### Custom Exception with All Constructors (Best Practice)

```java
public class UserNotFoundException extends Exception {
    // 1. No-argument constructor
    public UserNotFoundException() {
        super("User not found");
    }
    
    // 2. Constructor with message
    public UserNotFoundException(String message) {
        super(message);
    }
    
    // 3. Constructor with message and cause
    public UserNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
    
    // 4. Constructor with cause
    public UserNotFoundException(Throwable cause) {
        super(cause);
    }
}
```

---

### Real-World Example: E-commerce Application

```java
// Custom Exceptions for E-commerce
class InvalidEmailException extends RuntimeException {
    public InvalidEmailException(String message) {
        super(message);
    }
}

class ProductOutOfStockException extends Exception {
    private String productId;
    
    public ProductOutOfStockException(String message, String productId) {
        super(message);
        this.productId = productId;
    }
    
    public String getProductId() {
        return productId;
    }
}

class PaymentFailedException extends Exception {
    public PaymentFailedException(String message) {
        super(message);
    }
}

// Using custom exceptions
public class OrderService {
    public void validateEmail(String email) {
        if (!email.contains("@")) {
            throw new InvalidEmailException("Invalid email format: " + email);
        }
    }
    
    public void checkStock(String productId) throws ProductOutOfStockException {
        boolean inStock = false;  // Simulate checking database
        
        if (!inStock) {
            throw new ProductOutOfStockException(
                "Product currently out of stock",
                productId
            );
        }
    }
    
    public void processPayment(double amount) throws PaymentFailedException {
        boolean paymentSuccess = false;  // Simulate payment gateway
        
        if (!paymentSuccess) {
            throw new PaymentFailedException("Payment gateway returned error");
        }
    }
    
    public static void main(String[] args) {
        OrderService service = new OrderService();
        
        try {
            service.validateEmail("userexample.com");  // Invalid email
            service.checkStock("PROD123");
            service.processPayment(100.0);
            
        } catch (InvalidEmailException e) {
            System.out.println("Email Error: " + e.getMessage());
            
        } catch (ProductOutOfStockException e) {
            System.out.println("Stock Error: " + e.getMessage());
            System.out.println("Product ID: " + e.getProductId());
            
        } catch (PaymentFailedException e) {
            System.out.println("Payment Error: " + e.getMessage());
        }
    }
}
```

---

### 🎨 Checked vs Unchecked Custom Exception

```
When to extend Exception (Checked):
───────────────────────────────────
✅ Recoverable conditions
✅ External factors (network, file, database)
✅ Caller MUST handle
✅ Examples:
   - ProductOutOfStockException
   - PaymentFailedException
   - EmailSendFailedException


When to extend RuntimeException (Unchecked):
────────────────────────────────────────────
✅ Programming errors
✅ Validation failures
✅ Optional handling
✅ Examples:
   - InvalidEmailException
   - IllegalAgeException
   - InsufficientBalanceException
```

---

### Best Practices for Custom Exceptions

| Best Practice | Why | Example |
|---------------|-----|---------|
| **1. Meaningful Name** | Clear purpose | `InvalidAgeException` ✅<br/>`MyException` ❌ |
| **2. End with "Exception"** | Convention | `PaymentFailedException` ✅ |
| **3. Provide Constructors** | Flexibility | No-arg, with message, with cause |
| **4. Add Custom Fields** | Extra context | `productId`, `userId` |
| **5. Immutable Fields** | Thread-safety | `private final String userId` |
| **6. Document with JavaDoc** | Clarity | `/** Thrown when... */` |
| **7. Don't Overuse** | Simplicity | Use built-in if suitable |

---

### 🔥 Interview Questions: Custom Exceptions

**Q1: How to create custom exception?**
- **A:** Extend `Exception` (checked) or `RuntimeException` (unchecked)

**Q2: When to extend Exception vs RuntimeException?**
- **Exception**: Recoverable conditions, checked at compile time
- **RuntimeException**: Programming errors, unchecked

**Q3: What constructors should custom exception have?**
- No-arg constructor
- Constructor with message
- Constructor with message and cause
- Constructor with cause

**Q4: Can we add custom fields to exception?**
- **A:** Yes! Add fields for extra context (e.g., `userId`, `productId`)

**Q5: Should all exceptions be custom?**
- **A:** No! Use built-in exceptions when appropriate. Only create custom when needed.

---

## 🎯 Exception Methods

### Common Exception Methods

Every exception object has these useful methods:

| Method | Return Type | Description |
|--------|-------------|-------------|
| `getMessage()` | String | Returns error message |
| `toString()` | String | Returns exception class name + message |
| `printStackTrace()` | void | Prints stack trace to console |
| `getStackTrace()` | StackTraceElement[] | Returns array of stack trace elements |
| `getCause()` | Throwable | Returns cause of exception |
| `getSuppressed()` | Throwable[] | Returns suppressed exceptions (try-with-resources) |

---

### Example: Exception Methods

```java
public class ExceptionMethodsDemo {
    public static void method1() {
        method2();
    }
    
    public static void method2() {
        method3();
    }
    
    public static void method3() {
        int result = 10 / 0;  // ArithmeticException
    }
    
    public static void main(String[] args) {
        try {
            method1();
        } catch (ArithmeticException e) {
            System.out.println("=== getMessage() ===");
            System.out.println(e.getMessage());
            
            System.out.println("\n=== toString() ===");
            System.out.println(e.toString());
            
            System.out.println("\n=== printStackTrace() ===");
            e.printStackTrace();
            
            System.out.println("\n=== getStackTrace() ===");
            StackTraceElement[] trace = e.getStackTrace();
            for (StackTraceElement element : trace) {
                System.out.println("  at " + element);
            }
        }
    }
}
```

**Output:**
```
=== getMessage() ===
/ by zero

=== toString() ===
java.lang.ArithmeticException: / by zero

=== printStackTrace() ===
java.lang.ArithmeticException: / by zero
  at ExceptionMethodsDemo.method3(ExceptionMethodsDemo.java:11)
  at ExceptionMethodsDemo.method2(ExceptionMethodsDemo.java:7)
  at ExceptionMethodsDemo.method1(ExceptionMethodsDemo.java:3)
  at ExceptionMethodsDemo.main(ExceptionMethodsDemo.java:16)

=== getStackTrace() ===
  at ExceptionMethodsDemo.method3(ExceptionMethodsDemo.java:11)
  at ExceptionMethodsDemo.method2(ExceptionMethodsDemo.java:7)
  at ExceptionMethodsDemo.method1(ExceptionMethodsDemo.java:3)
  at ExceptionMethodsDemo.main(ExceptionMethodsDemo.java:16)
```

---

## 🎯 Exception Propagation

### What is Exception Propagation?

**Definition:** When an exception is thrown and not caught, it **propagates up** the call stack until someone catches it or program terminates.

**Flow:** method1 → method2 → method3 → JVM

---

### Example: Uncaught Exception (Propagates to JVM)

```java
public class ExceptionPropagation {
    public static void method1() {
        method2();
    }
    
    public static void method2() {
        method3();
    }
    
    public static void method3() {
        int result = 10 / 0;  // ArithmeticException thrown
    }
    
    public static void main(String[] args) {
        method1();
        System.out.println("This won't execute");
    }
}
```

**Flow:**
```
main() calls method1()
  → method1() calls method2()
    → method2() calls method3()
      → method3() throws ArithmeticException
      → method3() doesn't catch → propagates to method2()
    → method2() doesn't catch → propagates to method1()
  → method1() doesn't catch → propagates to main()
→ main() doesn't catch → propagates to JVM
→ JVM terminates program ❌
```

---

### Example: Caught in Calling Method

```java
public class CaughtPropagation {
    public static void method1() {
        try {
            method2();
        } catch (ArithmeticException e) {
            System.out.println("Caught in method1: " + e.getMessage());
        }
    }
    
    public static void method2() {
        method3();  // Not catching exception
    }
    
    public static void method3() {
        int result = 10 / 0;  // Throws exception
    }
    
    public static void main(String[] args) {
        method1();
        System.out.println("Program continues...");
    }
}
```

**Output:**
```
Caught in method1: / by zero
Program continues...
```

**Flow:**
```
method3() throws → method2() (no catch) → method1() catches ✅
```

---

## 🎯 Common Exceptions Explained

### Quick Reference Table

| Exception | Type | Cause | Prevention |
|-----------|------|-------|------------|
| **NullPointerException** | Unchecked | Null reference used | Check for null |
| **ArithmeticException** | Unchecked | Invalid arithmetic (÷0) | Check divisor |
| **ArrayIndexOutOfBoundsException** | Unchecked | Invalid array index | Check bounds |
| **NumberFormatException** | Unchecked | Invalid number format | Validate before parsing |
| **ClassCastException** | Unchecked | Invalid type casting | Use instanceof |
| **IllegalArgumentException** | Unchecked | Invalid method argument | Validate inputs |
| **IOException** | Checked | I/O operation failed | Handle with try-catch |
| **FileNotFoundException** | Checked | File not found | Check file exists |
| **SQLException** | Checked | Database error | Handle DB exceptions |

---

## 🎯 Best Practices

### ✅ Do's

**1. Catch Specific Exceptions First**
```java
// ✅ GOOD
try {
    // code
} catch (FileNotFoundException e) {
    // handle
} catch (IOException e) {
    // handle
} catch (Exception e) {
    // handle
}
```

**2. Don't Swallow Exceptions**
```java
// ✅ GOOD
catch (Exception e) {
    logger.error("Error occurred", e);
    // or rethrow
    throw new RuntimeException(e);
}

// ❌ BAD
catch (Exception e) {
    // Silent failure
}
```

**3. Use try-with-resources**
```java
// ✅ GOOD
try (FileReader fr = new FileReader("file.txt")) {
    // use resource
}
```

**4. Provide Meaningful Messages**
```java
// ✅ GOOD
throw new IllegalArgumentException("Age must be between 0 and 150. Provided: " + age);

// ❌ BAD
throw new IllegalArgumentException("Invalid age");
```

**5. Log Exceptions**
```java
// ✅ GOOD
catch (Exception e) {
    logger.error("Failed to process order", e);
}
```

---

### ❌ Don'ts

**1. Don't Catch Throwable or Error**
```java
// ❌ BAD
catch (Throwable t) { }
catch (Error e) { }
```

**2. Don't Return in Finally**
```java
// ❌ BAD - Suppresses exception
try {
    return "try";
} finally {
    return "finally";  // Bad!
}
```

**3. Don't Use Exceptions for Control Flow**
```java
// ❌ BAD
try {
    while (true) {
        array[i++];
    }
} catch (ArrayIndexOutOfBoundsException e) {
    // Use exception to exit loop
}

// ✅ GOOD
for (int i = 0; i < array.length; i++) {
    array[i];
}
```

**4. Don't Catch and Ignore**
```java
// ❌ BAD
catch (Exception e) {
    e.printStackTrace();  // Just printing is not enough
}
```

---

## 🎯 Coding Practice

### Exercise 1: Bank Account
Create a banking system with custom exceptions.

### Exercise 2: File Processing
Read a file and handle all possible exceptions.

### Exercise 3: User Registration
Validate user input and throw appropriate exceptions.

---

## 🎯 Interview Questions (Practice Aloud)

**Q1: What is exception handling?**
**Q2: Difference between checked and unchecked exceptions?**
**Q3: Difference between throw and throws?**
**Q4: Can we have try without catch?**
**Q5: What is try-with-resources?**
**Q6: Can finally block be skipped?**
**Q7: How to create custom exception?**
**Q8: What is exception propagation?**
**Q9: Difference between Exception and Error?**
**Q10: What are suppressed exceptions?**

---

## ✅ Homework Checklist

- [ ] Understand all 3 types of exceptions
- [ ] Practice try-catch-finally
- [ ] Implement try-with-resources
- [ ] Create 3 custom exceptions
- [ ] Handle common exceptions
- [ ] Implement exception propagation
- [ ] Write meaningful exception messages
- [ ] Practice interview questions aloud

---

## 📄 Exception Handling Cheatsheet (One Page)

### 🎯 Exception Hierarchy (Quick View)

```
Throwable
├── Error (Unchecked - Don't Catch)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
│
└── Exception
    ├── RuntimeException (Unchecked)
    │   ├── NullPointerException
    │   ├── ArithmeticException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── NumberFormatException
    │   ├── ClassCastException
    │   └── IllegalArgumentException
    │
    └── Checked Exceptions (Must Handle)
        ├── IOException
        ├── SQLException
        ├── FileNotFoundException
        ├── ClassNotFoundException
        └── InterruptedException
```

---

### 🔥 3 Types Comparison

| Feature | Checked | Unchecked | Error |
|---------|---------|-----------|-------|
| **Compile check** | ✅ Yes | ❌ No | ❌ No |
| **Must handle** | ✅ Yes | ❌ No | ❌ No |
| **Extends** | `Exception` | `RuntimeException` | `Error` |
| **When** | Compile time | Runtime | Runtime |
| **Recoverable** | ✅ Yes | ✅ Usually | ❌ No |
| **Cause** | External | Programming | JVM |
| **Should catch** | ✅ Yes | Optional | ❌ No |

---

### 🔑 Keywords & Syntax

```java
// 1️⃣ try-catch-finally
try {
    // risky code
} catch (SpecificException e) {
    // handle specific
} catch (Exception e) {
    // handle generic (last)
} finally {
    // always executes
}

// 2️⃣ Multi-catch (Java 7+)
try {
    // code
} catch (IOException | SQLException e) {
    // handle both
}

// 3️⃣ Try-with-resources (Java 7+)
try (FileReader fr = new FileReader("file.txt")) {
    // use resource
} catch (IOException e) {
    // handle
}
// Auto-closed!

// 4️⃣ throw (inside method)
if (age < 18) {
    throw new IllegalArgumentException("Age < 18");
}

// 5️⃣ throws (method signature)
public void readFile() throws IOException {
    // code that might throw
}
```

---

### ⚡ Try-Catch-Finally Rules

| Rule | Valid? | Example |
|------|--------|------|
| try-catch | ✅ Yes | `try {} catch {}` |
| try-finally | ✅ Yes | `try {} finally {}` |
| try only | ❌ No | `try {}` |
| catch only | ❌ No | `catch {}` |
| finally only | ❌ No | `finally {}` |
| Multiple catch | ✅ Yes | Specific → General |
| Multiple finally | ❌ No | Only ONE allowed |
| try-with-resources | ✅ Yes | `try (resource) {}` |

**Order:** Specific exceptions FIRST, generic LAST

---

### 📋 Common Exceptions

#### Unchecked (RuntimeException)

| Exception | Cause | Prevention |
|-----------|-------|------------|
| **NullPointerException** | Using null reference | `if (obj != null)` |
| **ArithmeticException** | Divide by zero | `if (b != 0)` |
| **ArrayIndexOutOfBoundsException** | Invalid index | `if (i < arr.length)` |
| **NumberFormatException** | Invalid number string | try-catch or validate |
| **ClassCastException** | Wrong type cast | `instanceof` check |
| **IllegalArgumentException** | Invalid argument | Validate input |

#### Checked (Exception)

| Exception | Cause | Must Handle |
|-----------|-------|-------------|
| **IOException** | I/O failed | ✅ Yes |
| **FileNotFoundException** | File missing | ✅ Yes |
| **SQLException** | DB error | ✅ Yes |
| **ClassNotFoundException** | Class not found | ✅ Yes |
| **InterruptedException** | Thread interrupted | ✅ Yes |

---

### 🎨 throw vs throws

| Feature | throw | throws |
|---------|-------|--------|
| **Where** | Inside method | Method signature |
| **Followed by** | Exception object | Exception class |
| **Purpose** | Throw exception | Declare exception |
| **Quantity** | One at a time | Multiple (comma) |
| **Example** | `throw new Exception()` | `throws IOException` |

```java
// TOGETHER:
public void method() throws IOException {  // throws
    throw new IOException("Error");        // throw
}
```

---

### 🛠️ Custom Exception

```java
// Checked (Extend Exception)
public class InvalidAgeException extends Exception {
    public InvalidAgeException(String msg) {
        super(msg);
    }
}

// Unchecked (Extend RuntimeException)
public class InvalidEmailException extends RuntimeException {
    public InvalidEmailException(String msg) {
        super(msg);
    }
}

// Usage
throw new InvalidAgeException("Age must be 18+");
```

**When to use:**
- **Checked**: Recoverable (file, network, DB)
- **Unchecked**: Validation, programming errors

---

### 📊 Exception Methods

| Method | Returns | Use |
|--------|---------|-----|
| `getMessage()` | String | Error message |
| `toString()` | String | Class + message |
| `printStackTrace()` | void | Print stack trace |
| `getCause()` | Throwable | Root cause |
| `getSuppressed()` | Throwable[] | Suppressed exceptions |

```java
try {
    // code
} catch (Exception e) {
    System.out.println(e.getMessage());  // "/ by zero"
    e.printStackTrace();                 // Full trace
}
```

---

### ✅ Best Practices

#### DO ✅

```java
// 1. Catch specific exceptions first
catch (FileNotFoundException e) { }
catch (IOException e) { }
catch (Exception e) { }

// 2. Use try-with-resources
try (FileReader fr = new FileReader("file.txt")) {
    // use
}

// 3. Provide meaningful messages
throw new IllegalArgumentException("Age: " + age + " invalid");

// 4. Log exceptions
logger.error("Error", e);

// 5. Rethrow if needed
catch (IOException e) {
    throw new RuntimeException("Failed", e);
}
```

#### DON'T ❌

```java
// 1. Don't swallow exceptions
catch (Exception e) { }  // ❌ Silent failure

// 2. Don't catch Error or Throwable
catch (Throwable t) { }  // ❌
catch (Error e) { }      // ❌

// 3. Don't return in finally
finally {
    return value;  // ❌ Suppresses exception
}

// 4. Don't use for control flow
try {
    while(true) { array[i++]; }
} catch (ArrayIndexOutOfBoundsException e) { }  // ❌

// 5. Don't just print stack trace
catch (Exception e) {
    e.printStackTrace();  // ❌ Not enough
}
```

---

### 🔥 Quick Interview Answers

**Q: Checked vs Unchecked?**
- **Checked**: Must handle, compile time, IOException
- **Unchecked**: Optional, runtime, NullPointerException

**Q: throw vs throws?**
- **throw**: Throws exception (inside method)
- **throws**: Declares exception (method signature)

**Q: Can try exist without catch?**
- Yes, with finally: `try {} finally {}`
- Yes, with resources: `try (resource) {}`

**Q: When does finally NOT execute?**
- `System.exit(0)`
- JVM crash
- Infinite loop in try

**Q: What is try-with-resources?**
- Auto-close resources (Java 7+)
- Must implement AutoCloseable
- Closed in reverse order

**Q: Custom exception?**
- Extend Exception (checked) or RuntimeException (unchecked)
- Provide constructors (no-arg, message, cause)

**Q: Exception propagation?**
- Exception travels up call stack
- Until caught or reaches JVM

**Q: Multi-catch?**
- Java 7+ feature: `catch (IOException | SQLException e)`
- Cannot be parent-child

---

### 📝 Memory Tricks

**CATCH order:** **S**pecific to **G**eneral (SG)

**5 Keywords:** **T**ry **C**atch **F**inally **T**hrow **T**hrows (TCF-TT)

**Finally executes:** **A**lways (except **S**ystem.exit, **J**VM crash, **I**nfinite loop) - ASJ-I

**3 Types:** **C**hecked, **U**nchecked, **E**rror (CUE)

**NPE Prevention:** **N**ull **C**heck **B**efore **U**se (NCBU)

---

### 🎯 Quick Code Templates

```java
// Template 1: Basic Exception Handling
try {
    // risky code
} catch (SpecificException e) {
    logger.error("Error: " + e.getMessage(), e);
    // handle or rethrow
}

// Template 2: Multiple Exceptions
try {
    // code
} catch (IOException e) {
    // handle IO
} catch (SQLException e) {
    // handle DB
} catch (Exception e) {
    // handle others
} finally {
    // cleanup
}

// Template 3: Try-with-resources
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    String line = br.readLine();
} catch (IOException e) {
    logger.error("File error", e);
}

// Template 4: Custom Exception
public class CustomException extends Exception {
    public CustomException() { super(); }
    public CustomException(String msg) { super(msg); }
    public CustomException(String msg, Throwable cause) {
        super(msg, cause);
    }
}

// Template 5: Validation with Exception
public void validate(int value) throws CustomException {
    if (value < 0) {
        throw new CustomException("Invalid: " + value);
    }
}
```

---

### 🚀 Print This Page for Quick Reference!

**Key Takeaways:**
1. **3 Types**: Checked (must handle), Unchecked (optional), Error (don't catch)
2. **Order**: Specific exceptions → General exceptions
3. **Finally**: Always executes (except System.exit, JVM crash)
4. **throw**: Throws exception | **throws**: Declares exception
5. **Try-with-resources**: Auto-closes (Java 7+)
6. **Custom**: Extend Exception or RuntimeException
7. **Best Practice**: Catch specific, log, provide context

---

**📌 Pro Tip:** Bookmark this page and review before interviews!
