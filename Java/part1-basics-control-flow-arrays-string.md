# Part 1: Basics of Java, Control Flow, Arrays, String

**Focus Areas:** Java Fundamentals, Control Flow, Arrays, String Operations

---

## 📋 Table of Contents
1. [Java Fundamentals](#-java-fundamentals)
2. [Data Types and Variables](#-data-types-and-variables)
3. [Control Flow Statements](#-control-flow-statements)
4. [Arrays](#-arrays)
5. [String Internals](#-string-internals)
6. [Cheatsheet - Quick Reference](#-cheatsheet---quick-reference)
7. [MCQ Practice Questions](#-mcq-practice-questions)

---

## 🎯 Java Fundamentals

### What is Java?
Java is a **High-level**, **Object-Oriented**, and **Platform Independent** programming language developed by Sun Microsystems (now Oracle) in 1995.

### Why is Java Platform Independent?
- Java code compiles into **Bytecode** (.class files)
- Bytecode runs on **JVM (Java Virtual Machine)**
- Principle: **"Write Once, Run Anywhere" (WORA)**

### Java Architecture Flow
```
Java Code (.java)
       ↓
   Compiler (javac)
       ↓
   Bytecode (.class)
       ↓
      JVM
       ↓
  Machine Code
       ↓
   Execution
```

### Important Terms: JDK vs JRE vs JVM

**Theory:**
Java's architecture is divided into three main components that work together to compile and execute Java programs.

#### 1. JDK (Java Development Kit)
- **Complete software development kit** for Java developers
- **Contains**: JRE + development tools
- **Tools included**: javac (compiler), debugger, javadoc, jar, etc.
- **Required for**: Developing Java applications
- **Size**: Larger in size compared to JRE

#### 2. JRE (Java Runtime Environment)
- **Provides runtime environment** to execute Java applications
- **Contains**: JVM + core libraries + supporting files
- **Required for**: Running Java applications (not for development)
- **Who needs it**: End users need only JRE installed

#### 3. JVM (Java Virtual Machine)
- **Abstract machine** that provides runtime environment for Java bytecode execution
- **Platform-dependent**: Different JVM for Windows, Linux, Mac
- **Functions**:
  - Loading code
  - Verifying code
  - Executing code
  - Providing runtime environment
- **Makes Java platform-independent**: "Write Once, Run Anywhere"

**Execution Flow:**
```
1. Java source code (.java file) → Compiled by javac (JDK)
2. Bytecode generated (.class file) → Platform independent
3. JVM loads and executes bytecode → Converts to machine code
4. Operating system executes machine code
```

| Term | Description | Purpose |
|------|-------------|---------|
| **JDK** | Java Development Kit - Contains tools for development | For developers to write code |
| **JRE** | Java Runtime Environment - Contains libraries to run applications | For users to run Java apps |
| **JVM** | Java Virtual Machine - Executes Java bytecode | Platform independence |

### 🔥 Key Interview Questions

**Q1: What is the difference between JDK, JRE, and JVM?**

**Answer:**
- **JVM**: Executes Java bytecode, platform-dependent, makes Java platform-independent
- **JRE**: Contains JVM + libraries needed to run Java applications
- **JDK**: Contains JRE + development tools (compiler, debugger, etc.)

**Relationship**: `JDK = JRE + Development Tools` and `JRE = JVM + Libraries`

**Q2: Can JVM run C++ code?**  
**Answer:** No. JVM only executes Java bytecode. C++ compiles directly to machine code, not bytecode.

**Q3: Why is Java called "Platform Independent"?**  
**Answer:** Because Java code compiles to bytecode (not machine code), which can run on any platform with JVM installed. Same .class file works on Windows, Linux, Mac, etc.

---

### Java Features

**Theory:**
Java is designed with several key features that make it a powerful and versatile programming language.

#### 1. Platform Independent (WORA - Write Once, Run Anywhere)
- Java code is compiled to **bytecode**, not machine code
- Bytecode can run on any platform with JVM installed
- Same .class file works on Windows, Linux, Mac, etc.
- **How it works**: JVM acts as an interpreter between bytecode and machine code

#### 2. Object-Oriented
- Everything revolves around **objects and classes**
- Supports OOP principles: Encapsulation, Inheritance, Polymorphism, Abstraction
- Promotes code reusability and modularity
- Real-world modeling becomes easier

#### 3. Simple and Easy
- Syntax based on C++, but removes complex features:
  - ❌ No pointers (explicit pointer arithmetic)
  - ❌ No operator overloading
  - ❌ No multiple inheritance (with classes)
- ✅ Automatic memory management (Garbage Collection)
- ✅ Rich API and extensive documentation
- ✅ Large community support

#### 4. Secure
- ✅ No explicit pointers, preventing direct memory access
- ✅ Bytecode verification before execution
- ✅ Security Manager defines access to system resources
- ✅ Built-in security features for web applications
- ✅ Exception handling prevents crashes

#### 5. Robust (Strong/Reliable)
- ✅ Strong type checking at compile-time
- ✅ Exception handling mechanism
- ✅ Automatic garbage collection prevents memory leaks
- ✅ Eliminates error-prone situations (pointer arithmetic)
- ✅ Strict compile-time and runtime checks

#### 6. Multithreaded
- ✅ Built-in support for concurrent programming
- ✅ Can execute multiple threads simultaneously
- ✅ Improves performance in multiprocessor systems
- ✅ Simplified thread management with Thread class

#### 7. Distributed
- ✅ Supports distributed computing with RMI and EJB
- ✅ Easy to create distributed applications
- ✅ Network-centric programming capabilities
- ✅ Built-in networking support

#### 8. High Performance
- ✅ JIT (Just-In-Time) compiler improves performance
- ✅ Bytecode converted to native machine code at runtime
- ✅ Optimizations performed during execution
- ✅ Faster than interpreted languages

**Java Features Summary:**

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Platform Independent** | Bytecode runs on any OS with JVM | Write once, run anywhere |
| **Object-Oriented** | Based on objects and classes | Code reusability, modularity |
| **Simple** | No pointers, automatic GC | Easy to learn and use |
| **Secure** | No pointers, bytecode verification | Safe for web applications |
| **Robust** | Strong typing, exception handling | Reliable applications |
| **Multithreaded** | Built-in threading support | Better performance |
| **Distributed** | RMI, EJB support | Network applications |
| **High Performance** | JIT compiler | Fast execution |

---

### First Java Program

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}

/*
Explanation:
- public: Access modifier (accessible everywhere)
- class: Keyword to define a class
- HelloWorld: Class name (should match filename)
- static: Can be called without creating object
- void: Returns nothing
- main: Entry point of program
- String[] args: Command line arguments
- System.out.println(): Prints output to console
*/
```

**How to Run:**
```bash
# Compile the program
javac HelloWorld.java     # Creates HelloWorld.class (bytecode)

# Run the program
java HelloWorld           # JVM executes the bytecode
```

---

## 🎯 Data Types and Variables

**Theory:**
Data types specify the type of data that a variable can hold. Java is a **strongly-typed** language, meaning every variable must have a declared type.

### Categories of Data Types

Java has two main categories of data types:

#### 1. Primitive Data Types
- **Predefined** by Java language
- Store **simple values**
- **Not objects** (lowercase names)
- Stored in **stack memory** (for local variables)
- **8 primitive types**: byte, short, int, long, float, double, char, boolean
- **Performance**: Faster than reference types

#### 2. Non-Primitive (Reference) Data Types
- **Created** by programmer (except String, Array)
- Store **addresses/references**
- Called **objects** (class instances)
- Stored in **heap memory**
- **Examples**: Classes, Interfaces, Arrays, String
- **Default value**: null

---

### Primitive Data Types

**Size and Range Table:**

| Type | Size | Range | Default Value | Description |
|------|------|-------|---------------|-------------|
| **byte** | 8-bit | -128 to 127 | 0 | Small integers |
| **short** | 16-bit | -32,768 to 32,767 | 0 | Short integers |
| **int** | 32-bit | -2³¹ to 2³¹-1 | 0 | Standard integers |
| **long** | 64-bit | -2⁶³ to 2⁶³-1 | 0L | Large integers |
| **float** | 32-bit | ±3.4e38 (7 decimal digits) | 0.0f | Decimal numbers |
| **double** | 64-bit | ±1.7e308 (16 decimal digits) | 0.0d | Precise decimals |
| **char** | 16-bit | 0 to 65,535 (Unicode) | '\u0000' | Single character |
| **boolean** | 1-bit | true or false | false | Logical values |

**Example:**
```java
public class DataTypes {
    public static void main(String[] args) {
        // Integer types
        byte age = 25;              // -128 to 127
        short year = 2024;          // -32768 to 32767
        int population = 1000000;   // Most commonly used
        long distance = 123456789L; // Note: L suffix for long
        
        // Floating-point types
        float price = 99.99f;       // Note: f suffix for float
        double salary = 50000.50;   // More precise, default for decimals
        
        // Character and boolean
        char grade = 'A';           // Single character in single quotes
        boolean isPassed = true;    // true or false only
        
        // Printing values
        System.out.println("Age: " + age);
        System.out.println("Price: " + price);
        System.out.println("Grade: " + grade);
        System.out.println("Passed: " + isPassed);
    }
}
```

---

### Type Casting

**Theory:**
Type casting is converting one data type to another.

#### 1. Widening (Implicit/Automatic Casting)
- Converting **smaller type to larger type**
- Done **automatically** by compiler
- **No data loss**
- **Widening order**: byte → short → int → long → float → double

```java
public class WideningCasting {
    public static void main(String[] args) {
        // Automatic casting (widening)
        int num = 100;
        long bigNum = num;        // int to long (automatic)
        float decimal = bigNum;    // long to float (automatic)
        double precise = decimal;  // float to double (automatic)
        
        System.out.println("int: " + num);
        System.out.println("long: " + bigNum);
        System.out.println("float: " + decimal);
        System.out.println("double: " + precise);
        
        // Another example
        char ch = 'A';            // A = 65 in ASCII
        int asciiValue = ch;      // char to int (automatic)
        System.out.println("ASCII value of A: " + asciiValue); // 65
    }
}
```

#### 2. Narrowing (Explicit Casting)
- Converting **larger type to smaller type**
- Must be done **manually**
- **May cause data loss**
- **Syntax**: `(targetType) value`

```java
public class NarrowingCasting {
    public static void main(String[] args) {
        // Manual casting (narrowing)
        double price = 99.99;
        int roundedPrice = (int) price;  // Explicit cast needed
        System.out.println("Original: " + price);      // 99.99
        System.out.println("Rounded: " + roundedPrice); // 99 (decimal lost)
        
        // Data loss example
        int bigNumber = 300;
        byte smallNumber = (byte) bigNumber;  // Data loss!
        System.out.println("Big: " + bigNumber);      // 300
        System.out.println("Small: " + smallNumber);  // 44 (overflow)
        
        // Another example
        long bigValue = 1000L;
        int normalValue = (int) bigValue;
        System.out.println("Long to int: " + normalValue); // 1000
    }
}
```

**Widening vs Narrowing:**

| Feature | Widening | Narrowing |
|---------|----------|-----------|
| **Direction** | Smaller → Larger | Larger → Smaller |
| **Automatic** | Yes | No (manual) |
| **Data Loss** | No | Possible |
| **Syntax** | `type var = value;` | `type var = (type) value;` |
| **Example** | `int → long` | `double → int` |

---

### Variables Types

**Theory:**
Variables are containers for storing data values. Java has three types of variables based on their scope and lifetime.

#### 1. Local Variables
- **Declared inside**: methods, constructors, or blocks
- **Lifetime**: Created when method/block is entered, destroyed when exited
- **Memory**: Stored in **stack memory**
- **Default value**: ❌ **No default value** - must be initialized before use
- **Access modifiers**: ❌ Cannot use access modifiers
- **Scope**: Not accessible outside the method/block

```java
public class LocalVariableExample {
    public void display() {
        int count = 10;  // Local variable
        System.out.println(count);
    } // count destroyed here
}
```

#### 2. Instance Variables (Non-static)
- **Declared inside**: class but outside methods
- **Lifetime**: Created when object is created, destroyed when object is destroyed
- **Memory**: Stored in **heap memory**
- **Default value**: ✅ Have default values (0, null, false, etc.)
- **Access modifiers**: ✅ Can use access modifiers
- **Scope**: Accessed using object reference
- **Copies**: Each object has its own copy

```java
public class InstanceVariableExample {
    int age;           // Instance variable (default = 0)
    String name;       // Instance variable (default = null)
    
    public static void main(String[] args) {
        InstanceVariableExample obj1 = new InstanceVariableExample();
        InstanceVariableExample obj2 = new InstanceVariableExample();
        
        obj1.age = 25;
        obj2.age = 30;
        
        System.out.println(obj1.age); // 25
        System.out.println(obj2.age); // 30 (separate copy)
    }
}
```

#### 3. Static Variables (Class Variables)
- **Declared with**: `static` keyword inside class
- **Lifetime**: Created when program starts, destroyed when program ends
- **Memory**: Stored in **method area** (shared memory)
- **Default value**: ✅ Have default values
- **Access**: Using class name (ClassName.variableName)
- **Copies**: **Only one copy** shared among all objects

```java
public class StaticVariableExample {
    static int count = 0;  // Static variable (shared)
    int id;                // Instance variable (separate for each object)
    
    public StaticVariableExample() {
        count++;           // Shared counter
        id = count;        // Unique id for each object
    }
    
    public static void main(String[] args) {
        StaticVariableExample obj1 = new StaticVariableExample();
        StaticVariableExample obj2 = new StaticVariableExample();
        StaticVariableExample obj3 = new StaticVariableExample();
        
        System.out.println("Total objects: " + StaticVariableExample.count); // 3
        System.out.println("obj1 id: " + obj1.id); // 1
        System.out.println("obj2 id: " + obj2.id); // 2
        System.out.println("obj3 id: " + obj3.id); // 3
    }
}
```

**Variable Types Comparison:**

| Feature | Local | Instance | Static |
|---------|-------|----------|--------|
| **Location** | Inside method/block | Inside class | Inside class |
| **Keyword** | None | None | `static` |
| **Memory** | Stack | Heap | Method Area |
| **Lifetime** | Method execution | Object lifetime | Program lifetime |
| **Default Value** | ❌ No | ✅ Yes | ✅ Yes |
| **Access Modifier** | ❌ No | ✅ Yes | ✅ Yes |
| **Copies** | Per method call | Per object | One copy (shared) |
| **Access** | Inside method only | Through object | Through class name |

---

### Wrapper Classes

**Theory:**
Wrapper classes convert primitive data types into objects. Each primitive type has a corresponding wrapper class.

**Why Wrapper Classes?**

1. ✅ **Collections Framework**: Collections (ArrayList, HashMap) can only store objects, not primitives
2. ✅ **Utility Methods**: Wrapper classes provide useful methods for conversion
3. ✅ **Null Assignment**: Primitives cannot be null, but wrapper objects can
4. ✅ **Generics**: Requires objects, not primitives
5. ✅ **Synchronization**: Object reference needed for synchronization

**Primitive to Wrapper Mapping:**

| Primitive | Wrapper Class |
|-----------|---------------|
| byte | Byte |
| short | Short |
| int | Integer |
| long | Long |
| float | Float |
| double | Double |
| char | Character |
| boolean | Boolean |

#### Autoboxing and Unboxing

**Autoboxing:**
Automatic conversion of primitive to wrapper class object (Java 5+).

**Unboxing:**
Automatic conversion of wrapper class object to primitive.

```java
public class WrapperDemo {
    public static void main(String[] args) {
        // Autoboxing - primitive to wrapper (automatic)
        int num = 10;
        Integer obj = num;  // Autoboxing (int to Integer)
        
        // Unboxing - wrapper to primitive (automatic)
        Integer wrapperNum = 20;
        int primitiveNum = wrapperNum;  // Unboxing (Integer to int)
        
        System.out.println("Primitive: " + num);
        System.out.println("Wrapper: " + obj);
        System.out.println("Unboxed: " + primitiveNum);
        
        // Wrapper class methods
        String str = "100";
        int converted = Integer.parseInt(str);  // String to int
        System.out.println("Converted: " + converted);
        
        // valueOf() method
        Integer num2 = Integer.valueOf("200");
        System.out.println("valueOf: " + num2);
        
        // Null assignment (only with wrapper)
        Integer nullableNum = null;  // OK
        // int primitiveNull = null;  // ERROR! Primitives can't be null
        
        // Collections with wrappers
        java.util.ArrayList<Integer> list = new java.util.ArrayList<>();
        list.add(10);  // Autoboxing
        list.add(20);
        System.out.println("List: " + list);
    }
}
```

**Important Methods:**

| Method | Description | Example |
|--------|-------------|---------|
| `parseInt()` | String to int | `Integer.parseInt("100")` |
| `parseDouble()` | String to double | `Double.parseDouble("99.9")` |
| `toString()` | Any to String | `Integer.toString(100)` |
| `valueOf()` | String to wrapper | `Integer.valueOf("100")` |
| `intValue()` | Wrapper to int | `obj.intValue()` |
| `doubleValue()` | Wrapper to double | `obj.doubleValue()` |

**Performance Consideration:**

```java
// ❌ BAD - Slow due to auto-boxing
Integer sum = 0;
for (int i = 0; i < 1000; i++) {
    sum += i;  // Auto-boxing overhead
}

// ✅ GOOD - Fast, no object creation
int sum2 = 0;
for (int i = 0; i < 1000; i++) {
    sum2 += i;
}
```

---

### Operators

**Theory:**
Operators are special symbols that perform operations on variables and values.

#### Types of Operators:

#### 1. Arithmetic Operators
```java
int a = 10, b = 3;
System.out.println(a + b);  // 13 (Addition)
System.out.println(a - b);  // 7  (Subtraction)
System.out.println(a * b);  // 30 (Multiplication)
System.out.println(a / b);  // 3  (Division - integer division)
System.out.println(a % b);  // 1  (Modulus - remainder)
```

#### 2. Relational Operators (Comparison)
```java
int x = 5, y = 10;
System.out.println(x == y);  // false (Equal to)
System.out.println(x != y);  // true  (Not equal to)
System.out.println(x > y);   // false (Greater than)
System.out.println(x < y);   // true  (Less than)
System.out.println(x >= y);  // false (Greater than or equal)
System.out.println(x <= y);  // true  (Less than or equal)
```

#### 3. Logical Operators
```java
boolean t = true, f = false;
System.out.println(t && f);  // false (AND - both must be true)
System.out.println(t || f);  // true  (OR - at least one true)
System.out.println(!t);      // false (NOT - inverts value)
```

#### 4. Assignment Operators
```java
int num = 10;    // Simple assignment
num += 5;        // num = num + 5  (15)
num -= 3;        // num = num - 3  (12)
num *= 2;        // num = num * 2  (24)
num /= 4;        // num = num / 4  (6)
num %= 4;        // num = num % 4  (2)
```

#### 5. Unary Operators
```java
int a = 10;
System.out.println(+a);   // 10 (Unary plus)
System.out.println(-a);   // -10 (Unary minus)
System.out.println(++a);  // 11 (Pre-increment)
System.out.println(a++);  // 11 (Post-increment, then a becomes 12)
System.out.println(--a);  // 11 (Pre-decrement)
System.out.println(a--);  // 11 (Post-decrement, then a becomes 10)
```

**Pre-increment vs Post-increment:**
```java
int x = 5;
int y = ++x;  // Pre-increment: x becomes 6, then y = 6
System.out.println("x: " + x + ", y: " + y); // x: 6, y: 6

int a = 5;
int b = a++;  // Post-increment: b = 5, then a becomes 6
System.out.println("a: " + a + ", b: " + b); // a: 6, b: 5
```

#### 6. Ternary Operator (Conditional)
```java
int age = 20;
String result = (age >= 18) ? "Adult" : "Minor";
System.out.println(result); // Adult

// Syntax: condition ? value_if_true : value_if_false
```

#### 7. Bitwise Operators (Advanced)
```java
int a = 5;   // Binary: 0101
int b = 3;   // Binary: 0011

System.out.println(a & b);   // 1 (AND:  0001)
System.out.println(a | b);   // 7 (OR:   0111)
System.out.println(a ^ b);   // 6 (XOR:  0110)
System.out.println(~a);      // -6 (NOT: inverts bits)
System.out.println(a << 1);  // 10 (Left shift: 1010)
System.out.println(a >> 1);  // 2 (Right shift: 0010)
```

**Operator Precedence (High to Low):**
1. Postfix: `expr++`, `expr--`
2. Unary: `++expr`, `--expr`, `+`, `-`, `!`, `~`
3. Multiplicative: `*`, `/`, `%`
4. Additive: `+`, `-`
5. Relational: `<`, `>`, `<=`, `>=`
6. Equality: `==`, `!=`
7. Logical AND: `&&`
8. Logical OR: `||`
9. Ternary: `?:`
10. Assignment: `=`, `+=`, `-=`, etc.

---

## 🎯 Control Flow Statements

**Theory:**
Control flow statements control the order in which statements are executed in a program. They allow the program to make decisions, repeat actions, and branch in different directions.

**Types:**
1. **Decision Making**: if, if-else, if-else-if, nested if, switch
2. **Looping**: for, while, do-while, enhanced for
3. **Branching**: break, continue, return

---

### 1. If-Else Statements

**Theory:**
Executes a block of code based on a condition (true or false).

#### a) Simple if Statement
```java
int age = 20;
if (age >= 18) {
    System.out.println("You are eligible to vote");
}
```

#### b) if-else Statement
```java
int age = 15;
if (age >= 18) {
    System.out.println("Adult");
} else {
    System.out.println("Minor");
}
```

#### c) if-else-if Ladder
```java
int marks = 85;

if (marks >= 90) {
    System.out.println("Grade: A+");
} else if (marks >= 80) {
    System.out.println("Grade: A");
} else if (marks >= 70) {
    System.out.println("Grade: B");
} else if (marks >= 60) {
    System.out.println("Grade: C");
} else {
    System.out.println("Grade: F");
}
```

#### d) Nested if Statement
```java
int age = 25;
boolean hasLicense = true;

if (age >= 18) {
    if (hasLicense) {
        System.out.println("You can drive");
    } else {
        System.out.println("Get a license first");
    }
} else {
    System.out.println("Too young to drive");
}
```

#### e) Ternary Operator (Shorthand if-else)
```java
int age = 20;
String result = (age >= 18) ? "Adult" : "Minor";
System.out.println(result);

// Nested ternary (avoid for readability)
int marks = 85;
String grade = (marks >= 90) ? "A" : (marks >= 80) ? "B" : "C";
```

---

### 2. Switch Statement

**Theory:**
Executes specific block based on matching case. Alternative to long if-else-if ladder. More readable when checking single variable against multiple values.

**Features:**
- ✅ Works with: byte, short, int, char, String (Java 7+), enum
- ❌ Does NOT work with: long, float, double, boolean
- `break` statement exits the switch
- Without break, **falls through** to next case
- `default` case executes if no match found (optional)
- Case values must be **constant or literal**

```java
public class SwitchDemo {
    public static void main(String[] args) {
        int day = 3;
        
        // Traditional switch
        switch (day) {
            case 1:
                System.out.println("Monday");
                break;
            case 2:
                System.out.println("Tuesday");
                break;
            case 3:
                System.out.println("Wednesday");
                break;
            case 4:
                System.out.println("Thursday");
                break;
            case 5:
                System.out.println("Friday");
                break;
            case 6:
            case 7:  // Fall-through for weekend
                System.out.println("Weekend");
                break;
            default:
                System.out.println("Invalid day");
        }
        
        // Switch with String (Java 7+)
        String month = "January";
        switch (month) {
            case "January":
            case "February":
            case "December":
                System.out.println("Winter");
                break;
            case "March":
            case "April":
            case "May":
                System.out.println("Spring");
                break;
            default:
                System.out.println("Other season");
        }
    }
}
```

**Fall-through Behavior:**
```java
int number = 2;
switch (number) {
    case 1:
        System.out.println("One");
        // No break - falls through
    case 2:
        System.out.println("Two");
        // No break - falls through
    case 3:
        System.out.println("Three");
        break;
}
// Output: Two, Three (falls through from case 2 to 3)
```

---

### 3. Loops

**Theory:**
Loops execute a block of code repeatedly until a condition is met.

#### a) for Loop

**Use when**: Number of iterations is **known**.

**Syntax:**
```java
for (initialization; condition; increment/decrement) {
    // code block
}
```

**Example:**
```java
// Print 1 to 5
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}

// Infinite loop (all parts optional)
for (;;) {
    System.out.println("Infinite loop");
    break;  // Exit condition
}

// Multiple variables
for (int i = 0, j = 10; i < j; i++, j--) {
    System.out.println("i: " + i + ", j: " + j);
}

// Nested for loop (Multiplication table)
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        System.out.print(i * j + " ");
    }
    System.out.println();
}
```

#### b) Enhanced for Loop (for-each)

**Use when**: Iterating through **arrays or collections**.

**Features:**
- ✅ More readable and less error-prone
- ❌ Cannot modify elements
- ❌ Cannot access index
- ❌ Cannot iterate backwards

```java
// Array iteration
int[] numbers = {1, 2, 3, 4, 5};
for (int num : numbers) {
    System.out.println(num);
}

// String array
String[] names = {"Alice", "Bob", "Charlie"};
for (String name : names) {
    System.out.println("Hello, " + name);
}
```

#### c) while Loop

**Use when**: Number of iterations is **unknown** (condition-based).

**Features:**
- Condition checked **before** execution
- May **not execute** even once if condition is false initially
- **Entry-controlled loop**

```java
// Basic while loop
int count = 1;
while (count <= 5) {
    System.out.println("Count: " + count);
    count++;
}

// Reading input until condition met
int number = 0;
while (number != -1) {
    System.out.println("Enter -1 to exit");
    // number = scanner.nextInt();
    number = -1; // Example
}

// Infinite loop
while (true) {
    System.out.println("Infinite loop");
    break;  // Exit condition
}
```

#### d) do-while Loop

**Use when**: Loop must execute **at least once**.

**Features:**
- Condition checked **after** execution
- **Always executes at least once**
- **Exit-controlled loop**

```java
// Basic do-while
int num = 1;
do {
    System.out.println("Number: " + num);
    num++;
} while (num <= 5);

// Menu-driven program example
int choice;
do {
    System.out.println("1. Option A");
    System.out.println("2. Option B");
    System.out.println("3. Exit");
    // choice = scanner.nextInt();
    choice = 3; // Example
} while (choice != 3);

// Executes even when condition is false
int x = 10;
do {
    System.out.println("Executes once: " + x);
} while (x < 5);  // Condition false, but executes once
```

**for vs while vs do-while:**

| Feature | for | while | do-while |
|---------|-----|-------|----------|
| **Use Case** | Known iterations | Unknown iterations | At least one execution |
| **Condition Check** | Before execution | Before execution | After execution |
| **Minimum Executions** | 0 | 0 | 1 |
| **Initialization** | In loop syntax | Before loop | Before loop |
| **Best For** | Counting, arrays | Event-driven | Menu systems |

---

### 4. Loop Control Statements

#### a) break Statement

**Purpose**: Exits the loop immediately.

```java
// Exit loop when condition met
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break;  // Exit loop when i is 5
    }
    System.out.println(i);
}
// Output: 1 2 3 4

// break in nested loop (exits inner loop only)
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (j == 2) {
            break;  // Exits inner loop only
        }
        System.out.println("i: " + i + ", j: " + j);
    }
}
```

#### b) continue Statement

**Purpose**: Skips current iteration and continues with next.

```java
// Skip even numbers
for (int i = 1; i <= 10; i++) {
    if (i % 2 == 0) {
        continue;  // Skip even numbers
    }
    System.out.println(i);
}
// Output: 1 3 5 7 9

// continue in while loop
int count = 0;
while (count < 5) {
    count++;
    if (count == 3) {
        continue;  // Skip when count is 3
    }
    System.out.println("Count: " + count);
}
// Output: Count: 1, Count: 2, Count: 4, Count: 5
```

#### c) Labeled Loops (break and continue with labels)

**Purpose**: Control outer loops from inner loops.

```java
// Labeled break
outerLoop:
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (i == 2 && j == 2) {
            break outerLoop;  // Exits both loops
        }
        System.out.println("i: " + i + ", j: " + j);
    }
}

// Labeled continue
outerLoop:
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (j == 2) {
            continue outerLoop;  // Skips to next iteration of outer loop
        }
        System.out.println("i: " + i + ", j: " + j);
    }
}
```

---

## 🎯 Arrays

**Theory:**
An array is a container object that holds a **fixed number** of values of a **single type**.

### Key Characteristics:

1. ✅ **Fixed Size**: Size determined at creation time, cannot be changed
2. ✅ **Contiguous Memory**: Elements stored in consecutive memory locations
3. ✅ **Zero-Indexed**: First element at index 0, last at (length - 1)
4. ✅ **Homogeneous**: All elements must be of same type
5. ✅ **Reference Type**: Array is an object in Java
6. ✅ **Length Property**: `.length` gives number of elements

**Benefits:**
- ✅ Access elements by index (O(1) time complexity)
- ✅ Store multiple values in single variable
- ✅ Efficient memory usage
- ✅ Can be passed to methods

**Limitations:**
- ❌ Fixed size (use ArrayList for dynamic size)
- ❌ Can only store one type of data
- ❌ No built-in methods for operations (use Arrays class)

**Memory Allocation:**
- Array **reference** stored in **stack**
- Array **object** stored in **heap**
- Elements stored **contiguously** in heap

---

### Single Dimensional Arrays

**Declaration Syntax:**
```java
dataType[] arrayName;           // Preferred way
dataType arrayName[];           // Valid but not preferred (C-style)
```

**Initialization Methods:**

#### 1. Static Initialization (Values known at compile-time)
```java
int[] numbers = {1, 2, 3, 4, 5};
String[] names = {"Alice", "Bob", "Charlie"};
```

#### 2. Dynamic Initialization (Values assigned later)
```java
int[] numbers = new int[5];  // Creates array of size 5
numbers[0] = 10;
numbers[1] = 20;
numbers[2] = 30;
numbers[3] = 40;
numbers[4] = 50;
```

#### 3. Declaration and Initialization Separately
```java
int[] numbers;              // Declaration
numbers = new int[5];       // Initialization
numbers[0] = 100;           // Assignment
```

**Default Values:**
- **int, short, byte, long**: 0
- **float, double**: 0.0
- **char**: '\u0000' (null character)
- **boolean**: false
- **Object references**: null

**Complete Example:**
```java
public class SingleArrayDemo {
    public static void main(String[] args) {
        // Method 1: Static initialization
        int[] marks = {85, 90, 78, 92, 88};
        
        // Method 2: Dynamic initialization
        String[] names = new String[3];
        names[0] = "Alice";
        names[1] = "Bob";
        names[2] = "Charlie";
        
        // Access elements
        System.out.println("First mark: " + marks[0]);
        System.out.println("Last mark: " + marks[marks.length - 1]);
        
        // Traverse using for loop
        System.out.println("All marks:");
        for (int i = 0; i < marks.length; i++) {
            System.out.println("marks[" + i + "] = " + marks[i]);
        }
        
        // Traverse using enhanced for loop
        System.out.println("All names:");
        for (String name : names) {
            System.out.println(name);
        }
        
        // Array length
        System.out.println("Total marks: " + marks.length);
        
        // Modify element
        marks[0] = 95;
        System.out.println("Updated first mark: " + marks[0]);
    }
}
```

**Common Operations:**
```java
import java.util.Arrays;

public class ArrayOperations {
    public static void main(String[] args) {
        int[] numbers = {5, 2, 8, 1, 9};
        
        // 1. Print array
        System.out.println(Arrays.toString(numbers));  // [5, 2, 8, 1, 9]
        
        // 2. Sort array
        Arrays.sort(numbers);
        System.out.println(Arrays.toString(numbers));  // [1, 2, 5, 8, 9]
        
        // 3. Binary search (array must be sorted)
        int index = Arrays.binarySearch(numbers, 5);
        System.out.println("Index of 5: " + index);    // 2
        
        // 4. Fill array with value
        int[] filled = new int[5];
        Arrays.fill(filled, 10);
        System.out.println(Arrays.toString(filled));   // [10, 10, 10, 10, 10]
        
        // 5. Copy array
        int[] copy = Arrays.copyOf(numbers, numbers.length);
        System.out.println(Arrays.toString(copy));
        
        // 6. Compare arrays
        boolean isEqual = Arrays.equals(numbers, copy);
        System.out.println("Arrays equal: " + isEqual);
    }
}
```

---

### Multi-dimensional Arrays

**Theory:**
Array of arrays. Represents data in matrix form (rows and columns).

#### 2D Arrays (Most Common)

**Declaration:**
```java
dataType[][] arrayName;
```

**Initialization:**

**Method 1: Static Initialization**
```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

**Method 2: Dynamic Initialization**
```java
int[][] matrix = new int[3][4];  // 3 rows, 4 columns
```

**Complete Example:**
```java
public class TwoDArrayDemo {
    public static void main(String[] args) {
        // Method 1: Static initialization
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // Method 2: Dynamic initialization
        int[][] scores = new int[3][2];  // 3 students, 2 subjects
        scores[0][0] = 85;  // Student 1, Subject 1
        scores[0][1] = 90;  // Student 1, Subject 2
        scores[1][0] = 78;
        scores[1][1] = 82;
        scores[2][0] = 92;
        scores[2][1] = 88;
        
        // Access elements
        System.out.println("Element at [1][2]: " + matrix[1][2]); // 6
        
        // Traverse using nested for loop
        System.out.println("Matrix:");
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                System.out.print(matrix[i][j] + " ");
            }
            System.out.println();
        }
        
        // Traverse using enhanced for loop
        System.out.println("Scores:");
        for (int[] student : scores) {
            for (int score : student) {
                System.out.print(score + " ");
            }
            System.out.println();
        }
        
        // Dimensions
        System.out.println("Rows: " + matrix.length);        // 3
        System.out.println("Columns: " + matrix[0].length);  // 3
    }
}
```

#### Jagged Arrays (Array with different column sizes)

**Theory:**
2D array where each row can have different number of columns.

```java
public class JaggedArrayDemo {
    public static void main(String[] args) {
        // Jagged array - rows with different sizes
        int[][] jagged = new int[3][];  // 3 rows, columns not specified
        
        jagged[0] = new int[2];  // First row: 2 columns
        jagged[1] = new int[4];  // Second row: 4 columns
        jagged[2] = new int[3];  // Third row: 3 columns
        
        // Initialize values
        jagged[0][0] = 1;
        jagged[0][1] = 2;
        
        jagged[1][0] = 3;
        jagged[1][1] = 4;
        jagged[1][2] = 5;
        jagged[1][3] = 6;
        
        jagged[2][0] = 7;
        jagged[2][1] = 8;
        jagged[2][2] = 9;
        
        // Traverse jagged array
        System.out.println("Jagged Array:");
        for (int i = 0; i < jagged.length; i++) {
            for (int j = 0; j < jagged[i].length; j++) {
                System.out.print(jagged[i][j] + " ");
            }
            System.out.println();
        }
        
        // Direct initialization
        int[][] jagged2 = {
            {1, 2},
            {3, 4, 5, 6},
            {7, 8, 9}
        };
    }
}
```

#### 3D Arrays

```java
public class ThreeDArrayDemo {
    public static void main(String[] args) {
        // 3D array: [depth][rows][columns]
        int[][][] cube = new int[2][3][4];
        
        // Initialize some values
        cube[0][0][0] = 1;
        cube[1][2][3] = 100;
        
        // Traverse 3D array
        for (int i = 0; i < cube.length; i++) {
            for (int j = 0; j < cube[i].length; j++) {
                for (int k = 0; k < cube[i][j].length; k++) {
                    System.out.print(cube[i][j][k] + " ");
                }
                System.out.println();
            }
            System.out.println();
        }
    }
}
```

---

### Array vs ArrayList

**Quick Comparison:**

| Feature | Array | ArrayList |
|---------|-------|-----------|
| **Size** | Fixed | Dynamic (grows/shrinks) |
| **Type** | Primitives or objects | Only objects (no primitives) |
| **Performance** | Faster | Slightly slower |
| **Syntax** | `int[] arr = new int[5]` | `ArrayList<Integer> list = new ArrayList<>()` |
| **Methods** | Limited | Rich API (add, remove, contains, etc.) |
| **Memory** | Less overhead | More overhead |
| **Type Safety** | Not type-safe | Type-safe (with generics) |

**Example:**
```java
import java.util.ArrayList;

public class ArrayVsArrayList {
    public static void main(String[] args) {
        // Array - Fixed size
        int[] array = new int[3];
        array[0] = 10;
        array[1] = 20;
        array[2] = 30;
        // array[3] = 40;  // ERROR! ArrayIndexOutOfBoundsException
        
        // ArrayList - Dynamic size
        ArrayList<Integer> list = new ArrayList<>();
        list.add(10);
        list.add(20);
        list.add(30);
        list.add(40);  // OK! Automatically grows
        
        System.out.println("Array length: " + array.length);
        System.out.println("ArrayList size: " + list.size());
        
        // ArrayList methods
        list.remove(1);           // Remove element at index 1
        list.add(1, 25);          // Insert at index 1
        boolean contains = list.contains(30);
        
        System.out.println("ArrayList: " + list);
    }
}
```

**When to Use:**
- **Array**: When size is known and fixed, performance is critical, working with primitives
- **ArrayList**: When size is dynamic, need flexibility, rich operations required

---

## 🎯 String Internals

**Theory:**
String is a sequence of characters. In Java, strings are **objects** of the String class, not primitive types.

### Key Characteristics:

#### 1. Immutable (Cannot be Changed)
- Once created, **cannot be modified**
- Any modification creates a **new String object**
- Original string remains unchanged

**Why Immutable?**
1. ✅ **Security**: Sensitive data (passwords, network connections) remains unchanged
2. ✅ **Thread Safety**: Can be shared across threads without synchronization
3. ✅ **String Pool**: Memory efficiency through object reuse
4. ✅ **Hashcode Caching**: Hash value remains constant for HashMap keys

**Example:**
```java
String str = "Hello";
str.concat(" World");  // Creates new string, but str unchanged
System.out.println(str);  // Output: Hello (not "Hello World")

String newStr = str.concat(" World");  // Must assign to new variable
System.out.println(newStr);  // Output: Hello World
```

#### 2. String Pool (String Constant Pool)

**What is String Pool?**
- Special memory region in **Heap** (Java 7+)
- Stores **string literals** to avoid duplicate objects
- When you create string literal, JVM checks pool first
- If exists, returns reference; otherwise, creates new string

**Visual Representation:**
```
HEAP MEMORY
─────────────────────────────────
│ String Pool:                  │
│ ┌──────────────────┐          │
│ │ "Hello"          │ ←─ str1  │
│ └──────────────────┘ ←─ str2  │
│ ┌──────────────────┐          │
│ │ "World"          │ ←─ str3  │
│ └──────────────────┘          │
│                                │
│ Regular Heap:                 │
│ ┌──────────────────┐          │
│ │ "Hello" (new)    │ ←─ str4  │
│ └──────────────────┘          │
─────────────────────────────────
```

---

### String Creation

#### Method 1: String Literal (Preferred)
```java
String str = "Hello";
```
- ✅ Stored in **String Pool**
- ✅ Reuses existing string if found
- ✅ **Memory efficient**
- ✅ Preferred method

#### Method 2: Using new Keyword
```java
String str = new String("Hello");
```
- ❌ Creates object in **heap memory** (outside pool)
- ❌ Always creates **new object** (even if string exists in pool)
- ❌ **Less efficient**
- ❌ Use only when you need different object

**Example:**
```java
public class StringCreation {
    public static void main(String[] args) {
        // String literal - stored in String Pool
        String s1 = "Hello";
        String s2 = "Hello";
        
        // Using new keyword - stored in heap
        String s3 = new String("Hello");
        String s4 = new String("Hello");
        
        // Reference comparison (==)
        System.out.println(s1 == s2);  // true (same object in pool)
        System.out.println(s1 == s3);  // false (different objects)
        System.out.println(s3 == s4);  // false (different objects)
        
        // Content comparison (.equals())
        System.out.println(s1.equals(s2));  // true (same content)
        System.out.println(s1.equals(s3));  // true (same content)
        System.out.println(s3.equals(s4));  // true (same content)
    }
}
```

---

### String Comparison

#### 1. Using == Operator
- Compares **references** (memory addresses)
- Returns true only if both references point to **same object**

#### 2. Using .equals() Method
- Compares **content** (values)
- Returns true if both strings have **same characters**

#### 3. Using .equalsIgnoreCase() Method
- Compares content **ignoring case**

#### 4. Using .compareTo() Method
- Lexicographic comparison
- Returns:
  - **0**: if strings are equal
  - **Negative**: if first string is lexicographically less
  - **Positive**: if first string is lexicographically greater

**Example:**
```java
public class StringComparison {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hello");
        String s4 = "hello";
        
        // == operator (reference comparison)
        System.out.println("s1 == s2: " + (s1 == s2));  // true (same object in pool)
        System.out.println("s1 == s3: " + (s1 == s3));  // false (different objects)
        
        // .equals() method (content comparison)
        System.out.println("s1.equals(s2): " + s1.equals(s2));  // true
        System.out.println("s1.equals(s3): " + s1.equals(s3));  // true
        System.out.println("s1.equals(s4): " + s1.equals(s4));  // false (case sensitive)
        
        // .equalsIgnoreCase() method
        System.out.println("s1.equalsIgnoreCase(s4): " + s1.equalsIgnoreCase(s4));  // true
        
        // .compareTo() method
        System.out.println("s1.compareTo(s2): " + s1.compareTo(s2));  // 0 (equal)
        System.out.println("s1.compareTo(\"Hi\"): " + s1.compareTo("Hi"));  // Negative
        System.out.println("\"Hi\".compareTo(s1): " + "Hi".compareTo(s1));  // Positive
    }
}
```

### 🔥 Critical Interview Question
**Q: Difference between `==` and `equals()`?**

| `==` Operator | `.equals()` Method |
|---------------|-------------------|
| Compares **reference** (memory address) | Compares **content** (value) |
| Returns true if same object | Returns true if same content |
| Works with primitives and objects | Only for objects |
| Can't be overridden | Can be overridden |

---

### String vs StringBuilder vs StringBuffer

**Theory:**

#### String (Immutable)
- **Immutable**: Cannot be modified after creation
- **Performance**: Slow for concatenation (creates many objects)
- **Thread Safety**: Thread-safe (due to immutability)
- **Use when**: Value won't change frequently

#### StringBuilder (Mutable, Not Thread-Safe)
- **Mutable**: Can be modified without creating new objects
- **Performance**: **Fast** for concatenation
- **Thread Safety**: **NOT thread-safe**
- **Use when**: Single-threaded, lots of modifications

#### StringBuffer (Mutable, Thread-Safe)
- **Mutable**: Can be modified without creating new objects
- **Performance**: Fast (but slower than StringBuilder due to synchronization)
- **Thread Safety**: **Thread-safe** (synchronized methods)
- **Use when**: Multi-threaded environment

**Comparison Table:**

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| **Mutability** | Immutable | Mutable | Mutable |
| **Thread-Safe** | Yes (immutable) | ❌ No | ✅ Yes |
| **Performance** | Slow (concatenation) | ✅ Fastest | Fast |
| **Synchronized** | N/A | No | Yes |
| **Memory** | Creates many objects | Efficient | Efficient |
| **Use Case** | Constant strings | Single-threaded | Multi-threaded |
| **Since** | JDK 1.0 | JDK 1.5 | JDK 1.0 |

**Performance Example:**
```java
public class StringPerformance {
    public static void main(String[] args) {
        long startTime, endTime;
        
        // String concatenation (SLOW)
        startTime = System.currentTimeMillis();
        String str = "";
        for (int i = 0; i < 10000; i++) {
            str += "a";  // Creates 10000 new String objects!
        }
        endTime = System.currentTimeMillis();
        System.out.println("String time: " + (endTime - startTime) + " ms");
        
        // StringBuilder (FAST)
        startTime = System.currentTimeMillis();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 10000; i++) {
            sb.append("a");  // Modifies same object
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuilder time: " + (endTime - startTime) + " ms");
        
        // StringBuffer (Fast but slower than StringBuilder)
        startTime = System.currentTimeMillis();
        StringBuffer sbf = new StringBuffer();
        for (int i = 0; i < 10000; i++) {
            sbf.append("a");  // Synchronized, thread-safe
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuffer time: " + (endTime - startTime) + " ms");
    }
}
```

**Complete Example:**
```java
public class StringComparison {
    public static void main(String[] args) {
        // String - Immutable
        String str = "Hello";
        str.concat(" World");          // Creates new string, but str unchanged
        System.out.println(str);       // Output: Hello
        str = str.concat(" World");    // Must reassign
        System.out.println(str);       // Output: Hello World
        
        // StringBuilder - Mutable, Not thread-safe (Fast)
        StringBuilder sb = new StringBuilder("Hello");
        sb.append(" World");           // Modifies same object
        System.out.println(sb);        // Output: Hello World
        sb.insert(5, ",");             // Insert at index
        System.out.println(sb);        // Output: Hello, World
        sb.reverse();                  // Reverse
        System.out.println(sb);        // Output: dlroW ,olleH
        
        // StringBuffer - Mutable, Thread-safe (Slower than StringBuilder)
        StringBuffer sbf = new StringBuffer("Hello");
        sbf.append(" Java");           // Thread-safe operation
        System.out.println(sbf);       // Output: Hello Java
    }
}
```

**When to Use:**

| Scenario | Use | Reason |
|----------|-----|--------|
| String doesn't change | **String** | Immutability benefits |
| Single thread, many modifications | **StringBuilder** | Best performance |
| Multiple threads, modifications | **StringBuffer** | Thread safety |
| Concatenating few strings | **String** | Readability |
| Loop concatenation | **StringBuilder/StringBuffer** | Performance |

---

### Important String Methods

```java
public class StringMethods {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // 1. Length
        System.out.println("Length: " + str.length());  // 11
        
        // 2. Character at index
        System.out.println("charAt(0): " + str.charAt(0));  // H
        
        // 3. Substring
        System.out.println("substring(0, 5): " + str.substring(0, 5));  // Hello
        System.out.println("substring(6): " + str.substring(6));        // World
        
        // 4. Contains
        System.out.println("contains('World'): " + str.contains("World"));  // true
        
        // 5. Index of
        System.out.println("indexOf('o'): " + str.indexOf('o'));         // 4
        System.out.println("lastIndexOf('o'): " + str.lastIndexOf('o')); // 7
        
        // 6. Replace
        System.out.println("replace('World', 'Java'): " + str.replace("World", "Java"));
        
        // 7. Split
        String[] words = str.split(" ");
        System.out.println("split: " + java.util.Arrays.toString(words));  // [Hello, World]
        
        // 8. Trim (removes leading/trailing spaces)
        String spaced = "  Hello  ";
        System.out.println("trim: '" + spaced.trim() + "'");  // 'Hello'
        
        // 9. Case conversion
        System.out.println("toLowerCase: " + str.toLowerCase());  // hello world
        System.out.println("toUpperCase: " + str.toUpperCase());  // HELLO WORLD
        
        // 10. StartsWith / EndsWith
        System.out.println("startsWith('Hello'): " + str.startsWith("Hello"));  // true
        System.out.println("endsWith('World'): " + str.endsWith("World"));      // true
        
        // 11. isEmpty / isBlank (Java 11+)
        String empty = "";
        String blank = "   ";
        System.out.println("isEmpty: " + empty.isEmpty());      // true
        System.out.println("isBlank: " + blank.isBlank());      // true (Java 11+)
        
        // 12. Concatenation
        String concat = str.concat(" !!!");
        System.out.println("concat: " + concat);  // Hello World !!!
        
        // 13. valueOf (primitive to String)
        int num = 100;
        String numStr = String.valueOf(num);
        System.out.println("valueOf: " + numStr);  // "100"
        
        // 14. toCharArray
        char[] chars = str.toCharArray();
        System.out.println("toCharArray: " + java.util.Arrays.toString(chars));
    }
}
```

**StringBuilder / StringBuffer Methods:**
```java
public class StringBuilderMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");
        
        // 1. append (add at end)
        sb.append(" World");
        System.out.println(sb);  // Hello World
        
        // 2. insert (add at specific position)
        sb.insert(5, ",");
        System.out.println(sb);  // Hello, World
        
        // 3. delete (remove characters)
        sb.delete(5, 6);  // Remove comma
        System.out.println(sb);  // Hello World
        
        // 4. deleteCharAt (remove single character)
        sb.deleteCharAt(5);
        System.out.println(sb);  // HelloWorld
        
        // 5. reverse
        sb.reverse();
        System.out.println(sb);  // dlroWolleH
        sb.reverse();            // Back to normal
        
        // 6. replace
        sb.replace(0, 5, "Hi");
        System.out.println(sb);  // HiWorld
        
        // 7. capacity
        System.out.println("Capacity: " + sb.capacity());  // 16 (default) + length
        
        // 8. ensureCapacity
        sb.ensureCapacity(50);
        
        // 9. setCharAt
        sb.setCharAt(0, 'h');
        System.out.println(sb);  // hiWorld
        
        // 10. toString (convert to String)
        String result = sb.toString();
        System.out.println("String: " + result);
    }
}
```

---

### String Pooling (Intern Method)

**Theory:**
The `intern()` method moves a string to the String Pool if it's not already there.

```java
public class StringPooling {
    public static void main(String[] args) {
        // String created with new (not in pool)
        String s1 = new String("Hello");
        String s2 = new String("Hello");
        
        System.out.println(s1 == s2);  // false (different objects in heap)
        
        // Move to String Pool using intern()
        String s3 = s1.intern();  // Returns reference from pool
        String s4 = s2.intern();  // Returns same reference from pool
        
        System.out.println(s3 == s4);  // true (same object in pool)
        
        // String literal (already in pool)
        String s5 = "Hello";
        System.out.println(s3 == s5);  // true (both in pool)
        
        // Visualization:
        /*
        Heap:
        ┌─────────────────┐
        │ String Pool:    │
        │ "Hello" ←── s3  │
        │         ←── s4  │
        │         ←── s5  │
        ├─────────────────┤
        │ Regular Heap:   │
        │ "Hello" ←── s1  │
        │ "Hello" ←── s2  │
        └─────────────────┘
        */
    }
}
```

---

### 🔥 Key Interview Questions

**Q1: Why is String immutable in Java?**

**Answer:**
1. **Security**: Strings are used in network connections, file paths, passwords - immutability prevents unauthorized changes
2. **Thread Safety**: Multiple threads can safely share strings without synchronization
3. **String Pool**: Allows efficient memory usage through object reuse
4. **Hashcode Caching**: Hashcode remains constant, making strings reliable as HashMap keys

**Q2: What is String Pool and where is it located?**

**Answer:**
- String Pool is a special memory region in **Heap** (Java 7+)
- Stores unique string literals to avoid duplicate objects
- When creating string literal, JVM checks pool first
- If exists, returns reference; otherwise, creates new string
- Improves memory efficiency

**Q3: Difference between String, StringBuilder, and StringBuffer?**

**Answer:**
- **String**: Immutable, thread-safe, slow for concatenation
- **StringBuilder**: Mutable, NOT thread-safe, fastest for concatenation
- **StringBuffer**: Mutable, thread-safe (synchronized), slower than StringBuilder

Use **String** for constant values, **StringBuilder** for single-threaded modifications, **StringBuffer** for multi-threaded modifications.

**Q4: What is the difference between `==` and `.equals()` for Strings?**

**Answer:**
- `==` compares **references** (memory addresses)
- `.equals()` compares **content** (character values)
- Example: `"Hello" == "Hello"` → true (same object in pool)
- Example: `new String("Hello") == new String("Hello")` → false (different objects)
- Always use `.equals()` for content comparison

**Q5: How does String Pool work?**

**Answer:**
When you create a string literal:
1. JVM checks if string exists in pool
2. If yes, returns reference to existing string
3. If no, creates new string in pool and returns reference
4. Strings created with `new` keyword are NOT in pool by default
5. Use `.intern()` to move string to pool

**Q6: Can we make String mutable?**

**Answer:**
No, String class is **final** and cannot be extended. All fields are **private final**, and no setter methods exist. However, we can use **StringBuilder** or **StringBuffer** for mutable strings.

**Q7: What happens when we concatenate strings in a loop?**

**Answer:**
```java
// BAD - Creates many objects
String str = "";
for (int i = 0; i < 1000; i++) {
    str += "a";  // Creates 1000 new String objects!
}

// GOOD - Modifies same object
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append("a");  // Efficient
}
```

**Q8: What is the default capacity of StringBuilder?**

**Answer:**
Default capacity is **16 characters**. When exceeded, new capacity = (old capacity * 2) + 2.

---

## ✅ Key Takeaways

### Part 1 Summary:

**1. Java Fundamentals:**
- ✅ JVM makes Java platform-independent
- ✅ JDK = JRE + Development Tools
- ✅ Java is Object-Oriented, Secure, Robust, and Platform Independent

**2. Data Types:**
- ✅ 8 primitive types: byte, short, int, long, float, double, char, boolean
- ✅ Wrapper classes for primitives (Integer, Double, etc.)
- ✅ Autoboxing and Unboxing (Java 5+)
- ✅ Variables: Local, Instance, Static

**3. Control Flow:**
- ✅ Decision making: if-else, switch
- ✅ Loops: for, while, do-while
- ✅ Loop control: break, continue

**4. Arrays:**
- ✅ Fixed size, zero-indexed, homogeneous
- ✅ Single-dimensional and multi-dimensional
- ✅ Use Arrays class for utilities
- ✅ ArrayList for dynamic size

**5. Strings:**
- ✅ Immutable (thread-safe, secure)
- ✅ String Pool for memory efficiency
- ✅ Use `==` for reference, `.equals()` for content
- ✅ StringBuilder (fast) vs StringBuffer (thread-safe)

---

## 📝 Practice Questions

1. Explain JVM architecture and how Java achieves platform independence
2. What are the differences between JDK, JRE, and JVM?
3. Write a program to demonstrate autoboxing and unboxing
4. Explain the difference between `==` and `.equals()` with examples
5. Why is String immutable? What are the benefits?
6. Write a program to reverse a string without using reverse() method
7. Demonstrate the performance difference between String and StringBuilder
8. What is String Pool? How does intern() method work?
9. Write a program to find the second largest element in an array
10. Explain the difference between for loop and enhanced for loop

---

## 📄 Cheatsheet - Quick Reference

### 🔹 JDK vs JRE vs JVM
```
JDK = JRE + Development Tools (javac, debugger, jar)
JRE = JVM + Libraries
JVM = Executes bytecode (platform-dependent)

Flow: .java → javac → .class → JVM → Machine Code
```

### 🔹 Primitive Data Types
| Type | Size | Range | Default | Suffix |
|------|------|-------|---------|--------|
| `byte` | 8-bit | -128 to 127 | 0 | - |
| `short` | 16-bit | -32,768 to 32,767 | 0 | - |
| `int` | 32-bit | -2³¹ to 2³¹-1 | 0 | - |
| `long` | 64-bit | -2⁶³ to 2⁶³-1 | 0L | L |
| `float` | 32-bit | 7 decimal digits | 0.0f | f |
| `double` | 64-bit | 16 decimal digits | 0.0d | d |
| `char` | 16-bit | 0 to 65,535 | '\u0000' | - |
| `boolean` | 1-bit | true/false | false | - |

**Widening Order**: `byte → short → int → long → float → double`

### 🔹 Variable Types
| Type | Location | Memory | Default | Lifetime | Access |
|------|----------|--------|---------|----------|--------|
| **Local** | Method/Block | Stack | ❌ No | Method execution | Method only |
| **Instance** | Class | Heap | ✅ Yes | Object lifetime | Via object |
| **Static** | Class | Method Area | ✅ Yes | Program lifetime | Via class name |

### 🔹 Wrapper Classes & Autoboxing
```java
Primitive → Wrapper: byte→Byte, int→Integer, char→Character, boolean→Boolean

Autoboxing:   Integer obj = 10;        // int → Integer
Unboxing:     int num = obj;           // Integer → int

Parsing:      int x = Integer.parseInt("100");
valueOf:      Integer y = Integer.valueOf("200");
```

### 🔹 Operators Precedence (High to Low)
```
1. Postfix: expr++, expr--
2. Unary: ++expr, --expr, !, ~
3. Multiplicative: *, /, %
4. Additive: +, -
5. Relational: <, >, <=, >=
6. Equality: ==, !=
7. Logical AND: &&
8. Logical OR: ||
9. Ternary: ? :
10. Assignment: =, +=, -=, *=, /=, %=

Pre-increment:  y = ++x;  // x incremented first, then assigned
Post-increment: y = x++;  // x assigned first, then incremented
```

### 🔹 Control Flow Quick Reference
```java
// If-Else
if (condition) { } 
else if (condition) { } 
else { }

// Ternary
result = (condition) ? value1 : value2;

// Switch (byte, short, int, char, String, enum)
switch (var) {
    case value: break;
    default:
}

// For Loop
for (init; condition; update) { }
for (item : collection) { }  // Enhanced for

// While/Do-While
while (condition) { }
do { } while (condition);  // Executes at least once

// Loop Control
break;           // Exit loop
continue;        // Skip iteration
break label;     // Exit labeled loop
```

### 🔹 Arrays Essentials
```java
// Declaration & Initialization
int[] arr = {1, 2, 3};              // Static
int[] arr = new int[5];             // Dynamic
int[][] matrix = new int[3][4];     // 2D Array

// Key Points
- Fixed size, zero-indexed
- Length: arr.length
- Default values: 0, 0.0, false, null

// Arrays Class Methods
Arrays.toString(arr);               // Print
Arrays.sort(arr);                   // Sort
Arrays.binarySearch(arr, key);      // Search (sorted only)
Arrays.fill(arr, value);            // Fill
Arrays.copyOf(arr, length);         // Copy
Arrays.equals(arr1, arr2);          // Compare
```

### 🔹 Array vs ArrayList
```
Array:           Fixed size, primitives/objects, faster, arr[i]
ArrayList:       Dynamic size, objects only, slower, list.get(i)
```

### 🔹 String Essentials

**Immutability**: Once created, cannot be changed (thread-safe, secure, cacheable)

**String Pool**: Heap memory region for string literals (memory efficient)

#### String Creation & Comparison
```java
String s1 = "Hello";                    // String Pool (preferred)
String s2 = new String("Hello");        // Heap (always new object)

s1 == s2         // false (different objects)
s1.equals(s2)    // true (same content) ✅ Always use for comparison
s1.intern()      // Returns reference from pool
```

#### String vs StringBuilder vs StringBuffer
| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| **Mutable** | ❌ No | ✅ Yes | ✅ Yes |
| **Thread-Safe** | ✅ Yes | ❌ No | ✅ Yes |
| **Performance** | Slow | ✅ Fastest | Fast |
| **Use Case** | Constants | Single-thread | Multi-thread |

#### Important String Methods
```java
// Length & Access
length()                    // Size
charAt(index)               // Character at index
toCharArray()              // Convert to char[]

// Search
indexOf(str)               // First occurrence
lastIndexOf(str)           // Last occurrence
contains(str)              // Check if contains
startsWith(str)            // Check prefix
endsWith(str)              // Check suffix

// Manipulation
substring(start, end)      // Extract substring
concat(str)                // Concatenate
replace(old, new)          // Replace
split(regex)               // Split into array
trim()                     // Remove leading/trailing spaces
toLowerCase()              // Convert to lowercase
toUpperCase()              // Convert to uppercase

// Comparison
equals(str)                // Content comparison (case-sensitive)
equalsIgnoreCase(str)      // Content comparison (ignore case)
compareTo(str)             // Lexicographic comparison (returns int)

// Check
isEmpty()                  // Check if length is 0
isBlank()                  // Check if empty or whitespace (Java 11+)

// Conversion
valueOf(x)                 // Primitive to String
parseInt(str)              // String to int (Integer class)
```

#### StringBuilder/StringBuffer Methods
```java
append(str)                // Add at end
insert(index, str)         // Add at position
delete(start, end)         // Remove characters
deleteCharAt(index)        // Remove single character
replace(start, end, str)   // Replace range
reverse()                  // Reverse
setCharAt(index, ch)       // Set character
toString()                 // Convert to String
capacity()                 // Current capacity (default: 16)
```

### 🔹 Key Interview Points

**Q: JDK vs JRE vs JVM?**
- JVM executes bytecode (platform-dependent)
- JRE = JVM + Libraries (for running apps)
- JDK = JRE + Dev Tools (for development)

**Q: Why Java is Platform Independent?**
- Compiles to bytecode (.class), not machine code
- Bytecode runs on any JVM (any OS)

**Q: Widening vs Narrowing?**
- Widening: Smaller → Larger (automatic, no data loss)
- Narrowing: Larger → Smaller (manual cast, data loss possible)

**Q: Local vs Instance vs Static variables?**
- Local: Method scope, Stack, no default value
- Instance: Object scope, Heap, has default value
- Static: Class scope, Method Area, shared among all objects

**Q: == vs equals()?**
- `==`: Compares reference (memory address)
- `.equals()`: Compares content (value)
- Always use `.equals()` for String comparison

**Q: Why String is immutable?**
1. Security (passwords, URLs unchanged)
2. Thread-safe (no synchronization needed)
3. String Pool (memory efficiency)
4. Hashcode caching (HashMap keys)

**Q: String vs StringBuilder vs StringBuffer?**
- String: Immutable, thread-safe, slow concatenation
- StringBuilder: Mutable, NOT thread-safe, fastest
- StringBuffer: Mutable, thread-safe, synchronized

**Q: What is String Pool?**
- Special memory region in Heap (Java 7+)
- Stores unique string literals
- Avoids duplicate strings (memory efficient)
- String literals go to pool, `new String()` goes to heap
- Use `.intern()` to move string to pool

**Q: Array vs ArrayList?**
- Array: Fixed size, primitives/objects, faster
- ArrayList: Dynamic size, objects only, more methods

**Q: for vs while vs do-while?**
- for: Known iterations
- while: Unknown iterations, may not execute
- do-while: Unknown iterations, executes at least once

**Q: break vs continue?**
- break: Exit loop immediately
- continue: Skip current iteration, continue with next

**Q: Autoboxing vs Unboxing?**
- Autoboxing: Primitive → Wrapper (automatic)
- Unboxing: Wrapper → Primitive (automatic)
- Example: `Integer obj = 10;` (autoboxing)

**Q: When to use StringBuilder vs StringBuffer?**
- StringBuilder: Single-threaded (faster)
- StringBuffer: Multi-threaded (thread-safe)

**Q: Default StringBuilder capacity?**
- 16 characters
- New capacity = (old * 2) + 2

### 🔹 Common Pitfalls to Avoid
```java
// ❌ BAD
String s = "";
for (int i = 0; i < 1000; i++) {
    s += "a";  // Creates 1000 objects!
}

// ✅ GOOD
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append("a");  // Efficient
}

// ❌ BAD - String comparison
if (str1 == str2) { }  // Compares reference

// ✅ GOOD
if (str1.equals(str2)) { }  // Compares content

// ❌ BAD - Wrapper in loop
Integer sum = 0;
for (int i = 0; i < 1000; i++) {
    sum += i;  // Auto-boxing overhead
}

// ✅ GOOD - Primitive
int sum = 0;
for (int i = 0; i < 1000; i++) {
    sum += i;  // No overhead
}
```

### 🔹 Memory Cheat Sheet
```
STACK:
- Local variables (primitives & references)
- Method call frames
- Faster, smaller, LIFO
- Thread-specific

HEAP:
- Objects (including arrays, strings)
- Instance variables
- Slower, larger
- Shared across threads
- Garbage collected

METHOD AREA:
- Static variables
- Class metadata
- Shared across threads
```

### 🔹 Quick Code Templates

**Reverse String:**
```java
StringBuilder sb = new StringBuilder(str);
String reversed = sb.reverse().toString();
// OR
for (int i = str.length() - 1; i >= 0; i--) {
    System.out.print(str.charAt(i));
}
```

**Array Traversal:**
```java
// Method 1: Traditional for
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}

// Method 2: Enhanced for
for (int element : arr) {
    System.out.println(element);
}
```

**2D Array Traversal:**
```java
for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[i].length; j++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

**String Parsing:**
```java
int num = Integer.parseInt("100");
double d = Double.parseDouble("99.9");
Integer obj = Integer.valueOf("200");
String str = String.valueOf(100);
```

---

## 🎯 MCQ Practice Questions

### Section A: Java Fundamentals (20 Questions)

**Q1. What does JVM stand for?**
- A) Java Variable Machine
- B) Java Virtual Machine
- C) Java Version Manager
- D) Java Visual Machine

**Q2. Which component is required to develop Java applications?**
- A) JRE only
- B) JVM only
- C) JDK
- D) Any of the above

**Q3. What is the correct relationship between JDK, JRE, and JVM?**
- A) JVM = JRE + JDK
- B) JDK = JVM + Libraries
- C) JDK = JRE + Development Tools
- D) JRE = JDK + JVM

**Q4. What makes Java platform-independent?**
- A) It compiles to machine code
- B) It compiles to bytecode that runs on JVM
- C) It uses C++ libraries
- D) It requires specific operating system

**Q5. Which file extension is used for Java bytecode?**
- A) .java
- B) .byte
- C) .class
- D) .jvm

**Q6. What does WORA stand for in Java?**
- A) Write Once, Run Always
- B) Write Once, Run Anywhere
- C) Write Object, Run Anywhere
- D) Write Once, Repeat Anywhere

**Q7. Which of the following is NOT a Java feature?**
- A) Platform Independent
- B) Object-Oriented
- C) Multiple Inheritance (using classes)
- D) Robust

**Q8. What does JIT compiler stand for?**
- A) Just In Time
- B) Java In Time
- C) Just Instant Time
- D) Java Instant Translation

**Q9. Which of the following is TRUE about Java?**
- A) Java supports pointers
- B) Java supports operator overloading
- C) Java supports multiple inheritance using classes
- D) Java has automatic garbage collection

**Q10. What is the entry point of a Java program?**
- A) start() method
- B) main() method
- C) run() method
- D) init() method

**Q11. Which keyword is used to define a class in Java?**
- A) define
- B) class
- C) struct
- D) object

**Q12. What is the correct signature of the main method?**
- A) public void main(String args[])
- B) public static void main(String[] args)
- C) static void main(String[] args)
- D) void main(String[] args)

**Q13. Which component of Java is platform-dependent?**
- A) Bytecode
- B) JVM
- C) .class file
- D) Source code

**Q14. What does 'javac' command do?**
- A) Executes Java program
- B) Compiles Java source code
- C) Debugs Java program
- D) Creates documentation

**Q15. Which of the following is a characteristic of Java being "Robust"?**
- A) It supports pointers
- B) It has strong type checking
- C) It allows multiple inheritance
- D) It requires manual memory management

**Q16. What is the default behavior of Java for memory management?**
- A) Manual memory allocation and deallocation
- B) Automatic garbage collection
- C) Requires explicit delete statements
- D) No memory management

**Q17. Which of the following makes Java secure?**
- A) Presence of pointers
- B) No bytecode verification
- C) No explicit pointers
- D) Manual memory management

**Q18. What happens when you run a .class file?**
- A) JVM converts it to source code
- B) JVM executes the bytecode
- C) Compiler generates machine code
- D) Nothing happens

**Q19. Which Java feature allows multiple threads to execute simultaneously?**
- A) Distributed
- B) Multithreaded
- C) Object-Oriented
- D) Robust

**Q20. What is the file extension of a Java source file?**
- A) .jav
- B) .class
- C) .java
- D) .src

---

### Section B: Data Types and Variables (25 Questions)

**Q21. How many primitive data types are there in Java?**
- A) 6
- B) 7
- C) 8
- D) 9

**Q22. What is the size of int data type in Java?**
- A) 16 bits
- B) 32 bits
- C) 64 bits
- D) 8 bits

**Q23. What is the range of byte data type?**
- A) -128 to 127
- B) 0 to 255
- C) -256 to 255
- D) 0 to 127

**Q24. Which suffix is used for long data type?**
- A) l or L
- B) f or F
- C) d or D
- D) No suffix needed

**Q25. What is the default value of boolean variable?**
- A) true
- B) false
- C) 0
- D) null

**Q26. Which data type is used to store a single character?**
- A) String
- B) char
- C) byte
- D) Character

**Q27. What is the default value of local variables?**
- A) 0
- B) null
- C) false
- D) No default value

**Q28. Where are instance variables stored?**
- A) Stack
- B) Heap
- C) Method Area
- D) Register

**Q29. Where are static variables stored?**
- A) Stack
- B) Heap
- C) Method Area
- D) Register

**Q30. What is the process of converting primitive to wrapper class called?**
- A) Boxing
- B) Autoboxing
- C) Casting
- D) Wrapping

**Q31. Which wrapper class is used for int primitive?**
- A) Int
- B) INTEGER
- C) Integer
- D) Integr

**Q32. What is the widening order for data types?**
- A) int → short → byte → long
- B) byte → short → int → long
- C) long → int → short → byte
- D) byte → int → short → long

**Q33. Which casting is done automatically by Java?**
- A) Narrowing
- B) Widening
- C) Both
- D) None

**Q34. What is the output of: int x = (int) 9.9;**
- A) 9.9
- B) 9
- C) 10
- D) Compilation error

**Q35. Which keyword is used for static variables?**
- A) final
- B) static
- C) const
- D) shared

**Q36. How many copies of static variables exist?**
- A) One per object
- B) One per method
- C) One for entire class
- D) Depends on usage

**Q37. Which of the following can be null?**
- A) int
- B) Integer
- C) boolean
- D) char

**Q38. What does Integer.parseInt("100") return?**
- A) Integer object
- B) int primitive
- C) String
- D) Compilation error

**Q39. What is the default value of int instance variable?**
- A) null
- B) 0
- C) -1
- D) No default value

**Q40. Which data type has the highest precision?**
- A) float
- B) double
- C) int
- D) long

**Q41. What is the size of char data type?**
- A) 8 bits
- B) 16 bits
- C) 32 bits
- D) 64 bits

**Q42. Can local variables have access modifiers?**
- A) Yes
- B) No
- C) Only public
- D) Only private

**Q43. What happens during unboxing?**
- A) Primitive to Wrapper
- B) Wrapper to Primitive
- C) String to int
- D) int to String

**Q44. Which is NOT a valid variable name?**
- A) _name
- B) $name
- C) 2name
- D) name2

**Q45. What is the default value of String instance variable?**
- A) ""
- B) null
- C) "null"
- D) No default value

---

### Section C: Operators (15 Questions)

**Q46. What is the output of: int x = 5; System.out.println(x++);**
- A) 5
- B) 6
- C) 4
- D) Compilation error

**Q47. What is the output of: int x = 5; System.out.println(++x);**
- A) 5
- B) 6
- C) 4
- D) Compilation error

**Q48. What does the % operator do?**
- A) Division
- B) Modulus (remainder)
- C) Percentage
- D) Multiplication

**Q49. What is the output of: 10 / 3 in Java?**
- A) 3.333
- B) 3
- C) 3.0
- D) 4

**Q50. Which operator is used for logical AND?**
- A) &
- B) &&
- C) |
- D) ||

**Q51. What is the ternary operator syntax?**
- A) condition ? true : false
- B) condition : true ? false
- C) true ? condition : false
- D) ? condition : true false

**Q52. What is the precedence of ++ operator?**
- A) Lowest
- B) Highest
- C) Medium
- D) Same as +

**Q53. What does the ! operator do?**
- A) Factorial
- B) Logical NOT
- C) Not equal
- D) Bitwise NOT

**Q54. What is the output of: 5 & 3 (bitwise AND)?**
- A) 1
- B) 3
- C) 5
- D) 7

**Q55. Which operator has the highest precedence?**
- A) +
- B) *
- C) ++
- D) =

**Q56. What is the output of: int x = 10; x += 5;**
- A) 10
- B) 5
- C) 15
- D) 50

**Q57. What does the << operator do?**
- A) Right shift
- B) Left shift
- C) Less than comparison
- D) Assignment

**Q58. What is the output of: true && false?**
- A) true
- B) false
- C) 1
- D) 0

**Q59. What is the output of: true || false?**
- A) true
- B) false
- C) 1
- D) 0

**Q60. What is the difference between & and &&?**
- A) No difference
- B) && is short-circuit, & is not
- C) & is logical, && is bitwise
- D) && is faster

---

### Section D: Control Flow (20 Questions)

**Q61. Which of these is NOT a valid loop in Java?**
- A) for
- B) while
- C) do-while
- D) repeat-until

**Q62. What is the minimum number of times a do-while loop executes?**
- A) 0
- B) 1
- C) 2
- D) Depends on condition

**Q63. Which statement is used to exit a loop?**
- A) exit
- B) break
- C) stop
- D) end

**Q64. Which statement skips the current iteration of a loop?**
- A) skip
- B) continue
- C) break
- D) next

**Q65. Which data types work with switch statement?**
- A) int, String, long
- B) int, char, String
- C) float, double, int
- D) All data types

**Q66. What happens if break is missing in switch case?**
- A) Compilation error
- B) Fall-through to next case
- C) Loop continues
- D) Nothing happens

**Q67. Which loop is entry-controlled?**
- A) do-while
- B) while
- C) Both
- D) None

**Q68. Which loop is exit-controlled?**
- A) for
- B) while
- C) do-while
- D) All of them

**Q69. What is the syntax for enhanced for loop?**
- A) for(int i : array)
- B) for(i in array)
- C) foreach(int i : array)
- D) for(array : i)

**Q70. Can you modify array elements in enhanced for loop?**
- A) Yes, always
- B) No, never
- C) Only for primitives
- D) Only for objects

**Q71. What is the output of: for(;;) { break; }**
- A) Infinite loop
- B) Exits immediately
- C) Compilation error
- D) Runtime error

**Q72. Which is true about labeled loops?**
- A) Can break outer loop from inner loop
- B) Not allowed in Java
- C) Only for for loops
- D) Deprecated

**Q73. What is the default case in switch?**
- A) Mandatory
- B) Optional
- C) First case
- D) Last case

**Q74. Can switch work with long data type?**
- A) Yes
- B) No
- C) Only in Java 7+
- D) Only in Java 8+

**Q75. What is the condition check time for while loop?**
- A) Before execution
- B) After execution
- C) During execution
- D) No check

**Q76. What is the output if condition is false in do-while?**
- A) Loop doesn't execute
- B) Loop executes once
- C) Compilation error
- D) Runtime error

**Q77. Can you have nested if-else statements?**
- A) Yes
- B) No
- C) Only 2 levels
- D) Only with switch

**Q78. What is the alternative to if-else for single condition?**
- A) switch
- B) Ternary operator
- C) while loop
- D) do-while

**Q79. Can you access loop index in enhanced for loop?**
- A) Yes
- B) No
- C) Only in Java 8+
- D) Only with ArrayList

**Q80. What happens with continue in nested loop?**
- A) Exits all loops
- B) Skips current iteration of inner loop
- C) Exits outer loop
- D) Compilation error

---

### Section E: Arrays (20 Questions)

**Q81. What is the index of the first element in an array?**
- A) 1
- B) 0
- C) -1
- D) Depends on declaration

**Q82. How do you get the length of an array?**
- A) array.length()
- B) array.length
- C) array.size()
- D) length(array)

**Q83. Can array size be changed after creation?**
- A) Yes
- B) No
- C) Only if dynamic
- D) Only with ArrayList

**Q84. What is the default value of int array elements?**
- A) null
- B) 0
- C) -1
- D) No default value

**Q85. How do you declare a 2D array?**
- A) int array[][];
- B) int[][] array;
- C) Both A and B
- D) int[2D] array;

**Q86. What is a jagged array?**
- A) Array with same column sizes
- B) Array with different column sizes
- C) 3D array
- D) Array of primitives

**Q87. Which method sorts an array?**
- A) Arrays.sort()
- B) array.sort()
- C) sort(array)
- D) Array.sort()

**Q88. Which method prints an array?**
- A) Arrays.print()
- B) Arrays.toString()
- C) array.toString()
- D) print(array)

**Q89. Can arrays store different data types?**
- A) Yes
- B) No
- C) Only with Object array
- D) Only in Java 8+

**Q90. What happens when you access array out of bounds?**
- A) Compilation error
- B) ArrayIndexOutOfBoundsException
- C) Returns null
- D) Returns 0

**Q91. How do you copy an array?**
- A) Arrays.copy()
- B) Arrays.copyOf()
- C) array.copy()
- D) copy(array)

**Q92. What is the syntax for array initialization?**
- A) int[] arr = {1,2,3};
- B) int[] arr = [1,2,3];
- C) int[] arr = new {1,2,3};
- D) int[] arr = array(1,2,3);

**Q93. Can you have an array of arrays?**
- A) No
- B) Yes, called multi-dimensional array
- C) Only 2D
- D) Not in Java

**Q94. What does Arrays.binarySearch() require?**
- A) Unsorted array
- B) Sorted array
- C) ArrayList
- D) String array

**Q95. What is the difference between Array and ArrayList?**
- A) No difference
- B) Array is fixed size, ArrayList is dynamic
- C) Array is slower
- D) ArrayList doesn't store objects

**Q96. Can arrays be passed to methods?**
- A) Yes
- B) No
- C) Only 1D arrays
- D) Only with copy

**Q97. What is array.length for 2D array?**
- A) Total elements
- B) Number of rows
- C) Number of columns
- D) Compilation error

**Q98. Can you use enhanced for loop with arrays?**
- A) Yes
- B) No
- C) Only 1D arrays
- D) Only in Java 8+

**Q99. What is the default value of String array elements?**
- A) ""
- B) null
- C) "null"
- D) No default value

**Q100. Which is faster: Array or ArrayList?**
- A) ArrayList
- B) Array
- C) Same speed
- D) Depends on operation

---

### Section F: Strings (30 Questions)

**Q101. Is String a primitive data type?**
- A) Yes
- B) No
- C) Sometimes
- D) Depends on usage

**Q102. Are Strings immutable in Java?**
- A) Yes
- B) No
- C) Only literals
- D) Only with new keyword

**Q103. Where is String Pool located?**
- A) Stack
- B) Heap
- C) Method Area
- D) Register

**Q104. What is the output of: "Hello" == "Hello"?**
- A) true
- B) false
- C) Compilation error
- D) Runtime error

**Q105. What is the output of: new String("Hello") == new String("Hello")?**
- A) true
- B) false
- C) Compilation error
- D) Runtime error

**Q106. Which method compares string content?**
- A) ==
- B) equals()
- C) compare()
- D) compareTo()

**Q107. What does String.length() return?**
- A) Size in bytes
- B) Number of characters
- C) Number of words
- D) Memory address

**Q108. What is the output of: "Hello".charAt(0)?**
- A) H
- B) e
- C) 0
- D) Compilation error

**Q109. What does substring(2, 5) do?**
- A) Returns characters from index 2 to 5
- B) Returns characters from index 2 to 4
- C) Returns 3 characters starting from index 2
- D) Returns characters from index 2 to end

**Q110. Which is faster for concatenation?**
- A) String
- B) StringBuilder
- C) StringBuffer
- D) All same speed

**Q111. Is StringBuilder thread-safe?**
- A) Yes
- B) No
- C) Sometimes
- D) Depends on usage

**Q112. Is StringBuffer thread-safe?**
- A) Yes
- B) No
- C) Sometimes
- D) Depends on usage

**Q113. What is the default capacity of StringBuilder?**
- A) 10
- B) 16
- C) 32
- D) 64

**Q114. What does trim() method do?**
- A) Removes all spaces
- B) Removes leading and trailing spaces
- C) Removes middle spaces
- D) Converts to lowercase

**Q115. What is the output of: "hello".toUpperCase()?**
- A) hello
- B) HELLO
- C) Hello
- D) Compilation error

**Q116. What does indexOf('o') return if 'o' is not found?**
- A) 0
- B) -1
- C) null
- D) Exception

**Q117. What does split(" ") do?**
- A) Splits string into characters
- B) Splits string into words
- C) Removes spaces
- D) Compilation error

**Q118. What does concat() method do?**
- A) Adds strings
- B) Same as +
- C) Creates new string
- D) All of the above

**Q119. Can you modify a String after creation?**
- A) Yes
- B) No, creates new string
- C) Only with concat
- D) Only with replace

**Q120. What is the output of: "Java".replace('a', 'o')?**
- A) Jovo
- B) Jova
- C) Java
- D) Compilation error

**Q121. What does isEmpty() check?**
- A) If string is null
- B) If length is 0
- C) If string has spaces
- D) If string has characters

**Q122. What does startsWith("He") check?**
- A) If string ends with "He"
- B) If string starts with "He"
- C) If string contains "He"
- D) If string equals "He"

**Q123. What is the output of: "Hello".compareTo("Hello")?**
- A) true
- B) false
- C) 0
- D) 1

**Q124. What does intern() method do?**
- A) Moves string to heap
- B) Moves string to String Pool
- C) Removes string from pool
- D) Creates new string

**Q125. When should you use StringBuilder?**
- A) Multi-threaded environment
- B) Single-threaded environment
- C) For constant strings
- D) Never

**Q126. When should you use StringBuffer?**
- A) Single-threaded environment
- B) Multi-threaded environment
- C) For constant strings
- D) Never

**Q127. What happens when StringBuilder capacity is exceeded?**
- A) Exception
- B) Automatically increases: (old * 2) + 2
- C) Truncates string
- D) Compilation error

**Q128. What does equalsIgnoreCase() do?**
- A) Compares strings case-sensitively
- B) Compares strings ignoring case
- C) Converts to same case
- D) Checks if string has uppercase

**Q129. Why is String immutable?**
- A) Security
- B) Thread safety
- C) String Pool efficiency
- D) All of the above

**Q130. What is the output of: "Hello" + "World"?**
- A) HelloWorld
- B) Hello World
- C) Hello+World
- D) Compilation error

---

## 📝 Answer Key

### Section A: Java Fundamentals (Q1-Q20)
1. B - Java Virtual Machine
2. C - JDK
3. C - JDK = JRE + Development Tools
4. B - It compiles to bytecode that runs on JVM
5. C - .class
6. B - Write Once, Run Anywhere
7. C - Multiple Inheritance (using classes)
8. A - Just In Time
9. D - Java has automatic garbage collection
10. B - main() method
11. B - class
12. B - public static void main(String[] args)
13. B - JVM
14. B - Compiles Java source code
15. B - It has strong type checking
16. B - Automatic garbage collection
17. C - No explicit pointers
18. B - JVM executes the bytecode
19. B - Multithreaded
20. C - .java

### Section B: Data Types and Variables (Q21-Q45)
21. C - 8
22. B - 32 bits
23. A - -128 to 127
24. A - l or L
25. B - false
26. B - char
27. D - No default value
28. B - Heap
29. C - Method Area
30. B - Autoboxing
31. C - Integer
32. B - byte → short → int → long
33. B - Widening
34. B - 9
35. B - static
36. C - One for entire class
37. B - Integer
38. B - int primitive
39. B - 0
40. B - double
41. B - 16 bits
42. B - No
43. B - Wrapper to Primitive
44. C - 2name
45. B - null

### Section C: Operators (Q46-Q60)
46. A - 5
47. B - 6
48. B - Modulus (remainder)
49. B - 3
50. B - &&
51. A - condition ? true : false
52. B - Highest
53. B - Logical NOT
54. A - 1
55. C - ++
56. C - 15
57. B - Left shift
58. B - false
59. A - true
60. B - && is short-circuit, & is not

### Section D: Control Flow (Q61-Q80)
61. D - repeat-until
62. B - 1
63. B - break
64. B - continue
65. B - int, char, String
66. B - Fall-through to next case
67. B - while
68. C - do-while
69. A - for(int i : array)
70. B - No, never
71. B - Exits immediately
72. A - Can break outer loop from inner loop
73. B - Optional
74. B - No
75. A - Before execution
76. B - Loop executes once
77. A - Yes
78. B - Ternary operator
79. B - No
80. B - Skips current iteration of inner loop

### Section E: Arrays (Q81-Q100)
81. B - 0
82. B - array.length
83. B - No
84. B - 0
85. C - Both A and B
86. B - Array with different column sizes
87. A - Arrays.sort()
88. B - Arrays.toString()
89. C - Only with Object array
90. B - ArrayIndexOutOfBoundsException
91. B - Arrays.copyOf()
92. A - int[] arr = {1,2,3};
93. B - Yes, called multi-dimensional array
94. B - Sorted array
95. B - Array is fixed size, ArrayList is dynamic
96. A - Yes
97. B - Number of rows
98. A - Yes
99. B - null
100. B - Array

### Section F: Strings (Q101-Q130)
101. B - No
102. A - Yes
103. B - Heap
104. A - true
105. B - false
106. B - equals()
107. B - Number of characters
108. A - H
109. B - Returns characters from index 2 to 4
110. B - StringBuilder
111. B - No
112. A - Yes
113. B - 16
114. B - Removes leading and trailing spaces
115. B - HELLO
116. B - -1
117. B - Splits string into words
118. D - All of the above
119. B - No, creates new string
120. A - Jovo
121. B - If length is 0
122. B - If string starts with "He"
123. C - 0
124. B - Moves string to String Pool
125. B - Single-threaded environment
126. B - Multi-threaded environment
127. B - Automatically increases: (old * 2) + 2
128. B - Compares strings ignoring case
129. D - All of the above
130. A - HelloWorld

---

## 📊 Scoring Guide

- **110-130 (85%+)**: Excellent! You have mastered Part 1 concepts
- **90-109 (70-84%)**: Very Good! Review weak areas
- **70-89 (54-69%)**: Good! More practice needed
- **Below 70 (Below 54%)**: Need significant review and practice

**Note**: Review the concepts for questions you got wrong. Each question is designed to test a specific concept covered in this study material.

---

**Next Topics:** Part 2 (OOP Concepts), Part 3 (Exception Handling), Part 4 (Collections Framework)
