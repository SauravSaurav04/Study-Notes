# Part 7: JVM Internals & Memory Management

**Focus Areas:** JVM Architecture, Memory Management, Garbage Collection, Class Loading, Memory Leaks

---

## 📋 Table of Contents
1. [JVM Architecture Overview](#-jvm-architecture-overview)
2. [JVM Memory Structure](#-jvm-memory-structure)
3. [Heap Memory Deep Dive](#-heap-memory-deep-dive)
4. [Stack Memory Deep Dive](#-stack-memory-deep-dive)
5. [Method Area / Metaspace](#-method-area--metaspace)
6. [Heap vs Stack - Complete Comparison](#-heap-vs-stack---complete-comparison)
7. [Primitive vs Wrapper: Memory Location](#-primitive-vs-wrapper-memory-location)
8. [Garbage Collection](#-garbage-collection)
9. [GC Algorithms & Types](#-gc-algorithms--types)
10. [Reference Types](#-reference-types)
11. [Class Loading Mechanism](#-class-loading-mechanism)
12. [Memory Leaks in Java](#-memory-leaks-in-java)
13. [JVM Tuning & Best Practices](#-jvm-tuning--best-practices)
14. [Interview Questions](#-interview-questions)
15. [📄 One-Page Cheatsheet](#-one-page-cheatsheet)

---

## 🎯 JVM Architecture Overview

### What is JVM?

**JVM (Java Virtual Machine)** is an abstract machine that provides runtime environment to execute Java bytecode.

**Key Characteristics:**
- **Platform Dependent**: Different JVM for different OS (Windows, Linux, Mac)
- **Specification**: Defined behavior, multiple implementations (HotSpot, OpenJ9, GraalVM)
- **Execution Engine**: Converts bytecode to machine code
- **Memory Manager**: Handles memory allocation and garbage collection

---

### JVM Architecture Components

```
┌─────────────────────────────────────────────────────────────┐
│                    JVM ARCHITECTURE                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌───────────────────────────────────────────────────┐    │
│  │         CLASS LOADER SUBSYSTEM                    │    │
│  │  ┌──────────┬──────────┬──────────────┐          │    │
│  │  │ Loading  │ Linking  │ Initialization│          │    │
│  │  └──────────┴──────────┴──────────────┘          │    │
│  └───────────────────────────────────────────────────┘    │
│                          ↓                                  │
│  ┌───────────────────────────────────────────────────┐    │
│  │           RUNTIME DATA AREAS                      │    │
│  │  ┌───────────────┬────────────────────────────┐  │    │
│  │  │               │                            │  │    │
│  │  │  METHOD AREA  │       HEAP MEMORY          │  │    │
│  │  │  (Metaspace)  │  ┌────────┬────────────┐  │  │    │
│  │  │               │  │ Young  │    Old     │  │  │    │
│  │  │  - Class      │  │ Gen    │  Generation│  │  │    │
│  │  │    Metadata   │  │        │            │  │  │    │
│  │  │  - Static     │  └────────┴────────────┘  │  │    │
│  │  │    Variables  │   (All Objects Live Here) │  │    │
│  │  └───────────────┴────────────────────────────┘  │    │
│  │                                                   │    │
│  │  ┌─────────────────────────────────────────┐    │    │
│  │  │         STACK MEMORY (Per Thread)        │    │    │
│  │  │  Thread 1  │  Thread 2  │  Thread 3     │    │    │
│  │  │  [Frame 1] │  [Frame 1] │  [Frame 1]    │    │    │
│  │  │  [Frame 2] │  [Frame 2] │               │    │    │
│  │  └─────────────────────────────────────────┘    │    │
│  │                                                   │    │
│  │  ┌────────────────┬──────────────────────┐      │    │
│  │  │  PC Register   │  Native Method Stack  │      │    │
│  │  │  (Per Thread)  │  (Per Thread)        │      │    │
│  │  └────────────────┴──────────────────────┘      │    │
│  └───────────────────────────────────────────────────┘    │
│                          ↓                                  │
│  ┌───────────────────────────────────────────────────┐    │
│  │         EXECUTION ENGINE                          │    │
│  │  ┌──────────┬──────────┬────────────────┐        │    │
│  │  │Interpreter│  JIT     │  Garbage      │        │    │
│  │  │          │ Compiler │  Collector    │        │    │
│  │  └──────────┴──────────┴────────────────┘        │    │
│  └───────────────────────────────────────────────────┘    │
│                          ↓                                  │
│  ┌───────────────────────────────────────────────────┐    │
│  │         NATIVE METHOD INTERFACE (JNI)             │    │
│  └───────────────────────────────────────────────────┘    │
│                          ↓                                  │
│  ┌───────────────────────────────────────────────────┐    │
│  │         NATIVE METHOD LIBRARIES                   │    │
│  │         (C, C++ libraries)                        │    │
│  └───────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

---

### JVM Components Explained

#### 1️⃣ Class Loader Subsystem
- **Loads** .class files into memory
- **Verifies** bytecode for security
- **Prepares** memory for static variables
- **Resolves** symbolic references
- **Initializes** static variables and blocks

#### 2️⃣ Runtime Data Areas
- **Method Area**: Class metadata, static variables
- **Heap**: Objects and arrays
- **Stack**: Method calls, local variables
- **PC Register**: Current instruction address
- **Native Method Stack**: Native method information

#### 3️⃣ Execution Engine
- **Interpreter**: Executes bytecode line by line
- **JIT Compiler**: Compiles frequently used code to native code
- **Garbage Collector**: Reclaims unused memory

#### 4️⃣ Native Interface
- Connects Java code with native libraries (C/C++)
- Enables platform-specific operations

---

### Java Code Execution Flow

```
┌──────────────────────────────────────────────────────────────┐
│                  JAVA EXECUTION FLOW                         │
└──────────────────────────────────────────────────────────────┘

Step 1: Write Code
   ↓
   MyProgram.java (Source Code)

Step 2: Compile
   ↓
   javac MyProgram.java
   ↓
   MyProgram.class (Bytecode - Platform Independent)

Step 3: Load Class
   ↓
   Class Loader → Loads .class into memory
   ↓
   Linking (Verify → Prepare → Resolve)
   ↓
   Initialization (Static blocks execute)

Step 4: Execute
   ↓
   Execution Engine
   ├── Interpreter (Line by line)
   └── JIT Compiler (Hot code → Native code)

Step 5: Runtime
   ↓
   ┌─────────────────────────────────┐
   │  Memory Allocation              │
   │  - Objects → Heap               │
   │  - Method calls → Stack         │
   │  - Class data → Method Area     │
   └─────────────────────────────────┘
   ↓
   Garbage Collector (Automatic cleanup)
   ↓
   Program Output
```

---

### 🎯 Bytecode vs Machine Code

#### What is Bytecode?

**Bytecode** is an **intermediate representation** between source code and machine code.

**Characteristics:**
- **Platform-Independent**: Same bytecode works on any OS with JVM
- **Not directly executable**: Needs JVM to interpret/compile it
- **JVM Instructions**: Uses stack-based operations (bipush, istore, iadd, etc.)
- **Portable**: Can copy .class file to any system with JVM

**Example:**

```java
// Java Source Code
int sum = 10 + 20;
```

**Compiles to Bytecode:**
```
0: bipush        10     // Push constant 10 onto stack
2: bipush        20     // Push constant 20 onto stack
4: iadd                 // Add top two stack values
5: istore_1             // Store result in local variable 1
```

---

#### What is Machine Code?

**Machine Code** is the **final binary code** that the CPU directly executes.

**Characteristics:**
- **Platform-Dependent**: Different for each CPU (x86, ARM, etc.)
- **Directly executable**: CPU understands these instructions natively
- **Register-based**: Uses CPU registers (EAX, EBX, etc.)
- **Binary format**: Pure 0s and 1s

**Example (x86 Machine Code):**
```
B8 0A 00 00 00    // MOV EAX, 10    (Move 10 to register)
BB 14 00 00 00    // MOV EBX, 20    (Move 20 to register)
01 D8             // ADD EAX, EBX   (Add registers)
```

---

#### Complete Journey: Source → Bytecode → Machine Code

```
┌─────────────────────────────────────────────────────────────┐
│           JAVA CODE TRANSFORMATION JOURNEY                  │
└─────────────────────────────────────────────────────────────┘

Step 1: Source Code (What you write)
┌──────────────────────────────┐
│ Hello.java                   │
│ public class Hello {         │
│   public static void main... │
│     int sum = 10 + 20;       │
│   }                          │
│ }                            │
└──────────────────────────────┘
   ↓ javac Hello.java (Compilation - happens ONCE)
   
Step 2: Bytecode (Platform-Independent)
┌──────────────────────────────┐
│ Hello.class                  │
│ CA FE BA BE 00 00 00 34     │ ← Magic number
│ bipush 10                    │ ← JVM instructions
│ bipush 20                    │ ← Stack-based
│ iadd                         │
└──────────────────────────────┘
   ✅ Same on Windows, Linux, Mac!
   ✅ Can be copied anywhere!
   
   ↓ java Hello (Run on JVM - at RUNTIME)
   ↓ JIT Compilation happens here
   
Step 3: Machine Code (Platform-Dependent)
┌─────────────┬─────────────┬─────────────┐
│  Windows    │   Linux     │    Mac      │
│  (x86)      │   (x86)     │   (ARM)     │
├─────────────┼─────────────┼─────────────┤
│ B8 0A 00... │ B8 0A 00... │ E3 A0 10... │ ← Different!
│ 01 D8       │ 01 D8       │ E0 80 00... │ ← Register-based
│ (Intel CPU) │ (Intel CPU) │ (ARM CPU)   │
└─────────────┴─────────────┴─────────────┘
   ❌ Different for each platform!
   ✅ Directly executed by CPU!
```

---

#### Comparison: Bytecode vs Machine Code

| Feature | Bytecode | Machine Code |
|---------|----------|--------------|
| **Target** | JVM (Virtual Machine) | CPU (Physical Processor) |
| **Platform** | Platform-Independent ✅ | Platform-Dependent ❌ |
| **Format** | Intermediate code | Native binary code |
| **Readability** | Semi-readable (with javap) | Not readable (binary) |
| **Execution** | Interpreted/JIT by JVM | Directly by CPU |
| **Speed** | Slower (needs translation) | Fastest (no translation) |
| **Portability** | Highly portable ✅ | Not portable ❌ |
| **Architecture** | Stack-based | Register-based |
| **Example Instruction** | `iadd` (add integers) | `01 D8` (ADD EAX, EBX) |

---

#### How JVM Executes Bytecode

```
┌─────────────────────────────────────────────────────────────┐
│         JVM EXECUTION STRATEGIES                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. INTERPRETER (Initially)                                │
│     Bytecode → Read one instruction at a time              │
│             → Convert to machine code                      │
│             → Execute immediately                          │
│     ✅ Fast startup                                         │
│     ❌ Slow execution (translates every time)              │
│                                                             │
│  2. JIT COMPILER (For "hot" code)                         │
│     Bytecode → Analyze frequently executed code            │
│             → Compile entire method to machine code        │
│             → Cache the machine code                       │
│             → Execute cached version (fast!)               │
│     ❌ Slower startup (compilation time)                   │
│     ✅ Fast execution (native speed!)                      │
│                                                             │
│  Result: Bytecode (Portability) + JIT (Performance)       │
│          = Best of Both Worlds! 🎯                         │
└─────────────────────────────────────────────────────────────┘
```

---

#### Why Two Steps?

**Java's Approach (Two Steps):**
```
Source Code → Bytecode → Machine Code
   (Once)    (Portable)   (At Runtime)
```

**Benefits:**
1. ✅ **Write Once, Run Anywhere**: Bytecode is portable
2. ✅ **Security**: Bytecode verification before execution
3. ✅ **Runtime Optimization**: JIT optimizes based on actual usage
4. ✅ **Dynamic Loading**: Load classes on demand

**C/C++ Approach (One Step):**
```
Source Code → Machine Code
   (Once)     (Not Portable - must recompile for each OS)
```

---

### 🔥 Key Interview Question

**Q: What is the difference between JDK, JRE, and JVM?**

```
┌─────────────────────────────────────────┐
│              JDK                        │
│  ┌───────────────────────────────────┐ │
│  │            JRE                    │ │
│  │  ┌─────────────────────────────┐ │ │
│  │  │         JVM                 │ │ │
│  │  │  - Executes bytecode        │ │ │
│  │  │  - Runtime environment      │ │ │
│  │  └─────────────────────────────┘ │ │
│  │  + Core Libraries               │ │
│  │  + Java API                     │ │
│  └───────────────────────────────────┘ │
│  + Development Tools                  │
│  + Compiler (javac)                   │
│  + Debugger, javadoc, jar             │
└─────────────────────────────────────────┘
```

**Answer:**

| Component | Purpose | Contains | Who Needs It? |
|-----------|---------|----------|---------------|
| **JVM** | Execute bytecode | Execution Engine, Runtime Data Areas | Everyone (inside JRE) |
| **JRE** | Run Java apps | JVM + Libraries | End users |
| **JDK** | Develop Java apps | JRE + Dev Tools | Developers |

---

## 🎯 JVM Memory Structure

### Complete Memory Architecture

Java divides memory into **5 main areas**:

```
┌────────────────────────────────────────────────────────────┐
│              JVM MEMORY STRUCTURE                          │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  1️⃣  HEAP MEMORY (Shared across all threads)              │
│  ┌──────────────────────────────────────────────────────┐ │
│  │                    HEAP                              │ │
│  │  ┌─────────────────┬──────────────────────────┐    │ │
│  │  │  Young Gen      │   Old Generation         │    │ │
│  │  │  ┌────┬────┐    │                          │    │ │
│  │  │  │Eden│S0  │S1  │   (Tenured)              │    │ │
│  │  │  │    │    │    │                          │    │ │
│  │  │  └────┴────┴────┴──────────────────────────┘    │ │
│  │  - All objects and instance variables             │ │
│  │  - Garbage collected                               │ │
│  │  - Size: -Xms (initial), -Xmx (maximum)           │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  2️⃣  STACK MEMORY (One per thread - Thread-local)         │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  Thread 1 Stack  │  Thread 2 Stack  │  Thread 3     │ │
│  │  ┌───────────┐   │  ┌───────────┐   │  ┌─────────┐ │ │
│  │  │ Frame 3   │   │  │ Frame 2   │   │  │ Frame 1 │ │ │
│  │  ├───────────┤   │  ├───────────┤   │  └─────────┘ │ │
│  │  │ Frame 2   │   │  │ Frame 1   │   │              │ │
│  │  ├───────────┤   │  └───────────┘   │              │ │
│  │  │ Frame 1   │   │                  │              │ │
│  │  └───────────┘   │                  │              │ │
│  │  - Method calls and local variables               │ │
│  │  - LIFO structure                                  │ │
│  │  - Size: -Xss                                      │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  3️⃣  METHOD AREA / METASPACE (Shared across all threads)  │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  - Class metadata (structure, methods, fields)      │ │
│  │  - Static variables                                 │ │
│  │  - Constant pool                                    │ │
│  │  - Method bytecode                                  │ │
│  │  Size: -XX:MetaspaceSize, -XX:MaxMetaspaceSize    │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  4️⃣  PC REGISTER (One per thread)                         │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  - Holds address of current instruction             │ │
│  │  - Updated after each instruction                   │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  5️⃣  NATIVE METHOD STACK (One per thread)                 │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  - Used for native methods (C/C++)                  │ │
│  │  - Similar to Stack but for native code             │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

---

### Memory Area Characteristics

| Memory Area | Shared? | Purpose | Size | GC? |
|-------------|---------|---------|------|-----|
| **Heap** | ✅ Yes | Objects, arrays | Large (GBs) | ✅ Yes |
| **Stack** | ❌ No (per thread) | Method calls, local vars | Small (MBs) | ❌ No |
| **Method Area** | ✅ Yes | Class metadata, statics | Medium | ✅ Yes (rarely) |
| **PC Register** | ❌ No (per thread) | Instruction pointer | Tiny | ❌ No |
| **Native Stack** | ❌ No (per thread) | Native methods | Small | ❌ No |

---

## 🎯 Heap Memory Deep Dive

### What is Heap Memory?

**Heap** is the runtime data area where **all objects and arrays** are allocated.

**Key Features:**
- **Shared**: Among all threads
- **Created**: When JVM starts
- **Destroyed**: When JVM shuts down
- **Size**: Configurable (can be GBs)
- **Garbage Collected**: Yes
- **Slower**: Than stack (but still fast)

---

### Heap Structure - Generations

Heap is divided into **generations** for efficient garbage collection:

```
┌────────────────────────────────────────────────────────────┐
│                    HEAP MEMORY                             │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  ┌──────────────────────────┬──────────────────────────┐  │
│  │   YOUNG GENERATION       │   OLD GENERATION         │  │
│  │   (Minor GC - Frequent)  │   (Major GC - Rare)      │  │
│  │                          │                          │  │
│  │  ┌──────┬──────┬──────┐ │                          │  │
│  │  │ Eden │  S0  │  S1  │ │   TENURED                │  │
│  │  │ Space│      │      │ │   (Long-lived objects)   │  │
│  │  │      │      │      │ │                          │  │
│  │  │ New  │Survivor     │ │   Survived multiple GCs  │  │
│  │  │Objects  regions     │ │                          │  │
│  │  └──────┴──────┴──────┘ │                          │  │
│  │                          │                          │  │
│  │  Size: ~1/3 of heap     │   Size: ~2/3 of heap     │  │
│  └──────────────────────────┴──────────────────────────┘  │
│                                                            │
│  Object Lifecycle:                                        │
│  New → Eden → S0 → S1 → ... → Old Gen → GC (if unused)   │
└────────────────────────────────────────────────────────────┘
```

---

### Heap Generations Explained

#### 🌱 Young Generation (1/3 of heap)

**Purpose:** Store newly created objects

**Components:**

**1. Eden Space (8/10 of Young Gen):**
- **All new objects** created here
- Gets filled quickly
- Most objects die here (short-lived)

**2. Survivor Space S0 (1/10 of Young Gen):**
- Objects that survived first GC
- Copied from Eden

**3. Survivor Space S1 (1/10 of Young Gen):**
- Objects that survived second GC
- Alternates with S0

**Garbage Collection:**
- **Minor GC**: Frequent, fast
- Cleans Eden and Survivor spaces
- Stop-the-world (short pause)

---

#### 🌳 Old Generation / Tenured (2/3 of heap)

**Purpose:** Store long-lived objects

**Characteristics:**
- Objects that survived **multiple** Minor GCs
- Threshold: Default 15 GC cycles (age)
- Larger objects may go directly here

**Garbage Collection:**
- **Major GC / Full GC**: Rare, slower
- Cleans Old Generation
- Stop-the-world (longer pause)
- Triggers when Old Gen is full

---

### Object Lifecycle in Heap

```
┌─────────────────────────────────────────────────────────────┐
│              OBJECT LIFECYCLE IN HEAP                       │
└─────────────────────────────────────────────────────────────┘

Step 1: Object Created
   ↓
   new Object() → Eden Space
   [Age: 0]

Step 2: First Minor GC
   ↓
   Still referenced? 
   ├─ Yes → Move to S0 [Age: 1]
   └─ No → Garbage collected ♻️

Step 3: Second Minor GC
   ↓
   S0 → S1 [Age: 2]

Step 4: Third Minor GC
   ↓
   S1 → S0 [Age: 3]
   (Ping-pong between S0 and S1)

Step 5: Age Threshold Reached (Age = 15)
   ↓
   Promoted to Old Generation (Tenured)
   [Long-lived object]

Step 6: Object No Longer Referenced
   ↓
   Eligible for Major GC
   ↓
   Garbage collected ♻️
```

---

### Heap Configuration

**JVM Options:**

```bash
# Set initial heap size to 512 MB
-Xms512m

# Set maximum heap size to 2 GB
-Xmx2g

# Set Young Generation size
-Xmn512m

# Example: Run Java app with custom heap
java -Xms512m -Xmx2g -Xmn512m MyApp
```

**Heap Size Guidelines:**

| Application Type | Recommended Heap |
|------------------|------------------|
| **Small apps** | 256 MB - 512 MB |
| **Medium apps** | 512 MB - 2 GB |
| **Large apps** | 2 GB - 8 GB |
| **Enterprise apps** | 8 GB+ |

**Best Practice:** Set `-Xms` = `-Xmx` to avoid resizing overhead

---

### What Goes in Heap?

```java
public class HeapExample {
    // Instance variables → Heap (inside object)
    int instanceVar = 10;
    String name = "Java";
    
    public void method() {
        // Object → Heap
        Employee emp = new Employee();
        
        // Array → Heap
        int[] arr = new int[10];
        
        // Wrapper objects → Heap
        Integer num = 100;
        
        // String → Heap (String Pool or regular heap)
        String str = "Hello";
    }
}

class Employee {
    // All instance variables → Heap (part of object)
    int id;
    String name;
}
```

---

### Heap Memory Example

```java
public class HeapMemoryDemo {
    public static void main(String[] args) {
        // All these objects created in Heap
        Person p1 = new Person("Alice", 25);  // Heap
        Person p2 = new Person("Bob", 30);    // Heap
        
        int[] numbers = new int[100];  // Array in Heap
        
        // Even wrapper objects
        Integer count = 100;  // Object in Heap (cached for -128 to 127)
    }
}

class Person {
    String name;  // Instance variable in Heap
    int age;      // Instance variable in Heap
    
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

**Memory Visualization:**

```
HEAP MEMORY
───────────
[Person Object 1]
├─ name → "Alice" (String object)
└─ age = 25

[Person Object 2]
├─ name → "Bob" (String object)
└─ age = 30

[int[] array]
└─ [0, 0, 0, ... 100 elements]

[Integer Object]
└─ value = 100 (cached)
```

---

## 🎯 Stack Memory Deep Dive

### What is Stack Memory?

**Stack** is a memory area for **method execution and local variables**.

**Key Features:**
- **Thread-local**: Each thread has its own stack
- **LIFO**: Last In, First Out
- **Fast**: Faster than heap
- **Size**: Limited (usually 512 KB - 1 MB per thread)
- **No GC**: Automatic cleanup when method returns
- **Stores**: Method calls, local variables, references

---

### Stack Structure

```
┌────────────────────────────────────────────────────────────┐
│                  STACK MEMORY (Per Thread)                 │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Thread-1 Stack              Thread-2 Stack               │
│  ┌────────────────┐          ┌────────────────┐          │
│  │  Frame 4       │ ← Top    │  Frame 2       │ ← Top    │
│  │  method4()     │          │  method2()     │          │
│  │  - local vars  │          │  - local vars  │          │
│  ├────────────────┤          ├────────────────┤          │
│  │  Frame 3       │          │  Frame 1       │          │
│  │  method3()     │          │  method1()     │          │
│  │  - local vars  │          │  - local vars  │          │
│  ├────────────────┤          └────────────────┘          │
│  │  Frame 2       │                                       │
│  │  method2()     │                                       │
│  │  - local vars  │                                       │
│  ├────────────────┤                                       │
│  │  Frame 1       │                                       │
│  │  main()        │                                       │
│  │  - local vars  │                                       │
│  └────────────────┘ ← Bottom                             │
│                                                            │
│  Each Frame Contains:                                     │
│  - Local variables (primitives)                           │
│  - Object references (not objects themselves)             │
│  - Return address                                         │
│  - Partial results                                        │
└────────────────────────────────────────────────────────────┘
```

---

### Stack Frame Structure

**Each method call creates a Stack Frame containing:**

```
┌────────────────────────────────────┐
│       STACK FRAME                  │
├────────────────────────────────────┤
│  Method: add(int a, int b)         │
│                                    │
│  1. Local Variables                │
│     - a = 10                       │
│     - b = 20                       │
│     - result = 30                  │
│                                    │
│  2. Operand Stack                  │
│     - Temporary values             │
│                                    │
│  3. Frame Data                     │
│     - Return address               │
│     - Exception table              │
│     - Constant pool reference      │
└────────────────────────────────────┘
```

---

### Stack Execution Flow

```java
public class StackFlowDemo {
    public static void main(String[] args) {
        int x = 10;
        int y = 20;
        int result = add(x, y);
        System.out.println(result);
    }
    
    public static int add(int a, int b) {
        int sum = a + b;
        return sum;
    }
}
```

**Stack Visualization (Step-by-Step):**

```
Step 1: main() called
┌──────────────────┐
│  main()          │
│  - x = 10        │
│  - y = 20        │
│  - result = ?    │
└──────────────────┘

Step 2: add() called from main()
┌──────────────────┐
│  add(10, 20)     │ ← Current executing
│  - a = 10        │
│  - b = 20        │
│  - sum = 30      │
├──────────────────┤
│  main()          │
│  - x = 10        │
│  - y = 20        │
│  - result = ?    │
└──────────────────┘

Step 3: add() returns 30
┌──────────────────┐
│  main()          │ ← add() frame removed
│  - x = 10        │
│  - y = 20        │
│  - result = 30   │ ← Value updated
└──────────────────┘

Step 4: main() completes
[Stack Empty] ← All frames removed
```

---

### What Goes in Stack?

```java
public class StackContentDemo {
    public void method() {
        // ✅ Stack: Local primitive variables
        int primitiveInt = 10;
        double primitiveDouble = 5.5;
        boolean primitiveBoolean = true;
        char primitiveChar = 'A';
        
        // ✅ Stack: Object REFERENCES (not objects)
        String str = "Hello";        // Reference in stack, object in heap
        Employee emp = new Employee(); // Reference in stack, object in heap
        int[] arr = new int[10];     // Reference in stack, array in heap
        
        // ✅ Stack: Method parameters
        // ✅ Stack: Return addresses
    }
}
```

**Memory Visualization:**

```
STACK                          HEAP
─────                          ────
method()                       String "Hello"
├─ primitiveInt = 10          Employee object
├─ primitiveDouble = 5.5      int[] array
├─ primitiveBoolean = true
├─ primitiveChar = 'A'
├─ str = 0x100 ───────────────→ "Hello"
├─ emp = 0x200 ───────────────→ Employee object
└─ arr = 0x300 ───────────────→ int[10]
```

---

### Stack Configuration

```bash
# Set stack size per thread
-Xss512k    # 512 KB per thread
-Xss1m      # 1 MB per thread

# Example: Run with custom stack size
java -Xss1m MyApp
```

**Stack Size Guidelines:**

| Scenario | Recommended Stack Size |
|----------|------------------------|
| **Default** | 512 KB - 1 MB |
| **Deep recursion** | 2 MB - 4 MB |
| **Many threads** | 256 KB - 512 KB (save memory) |

---

### StackOverflowError

**When it occurs:**
- **Too many method calls** (deep recursion)
- **Stack size exceeded**
- **No more space** for new frames

**Example:**

```java
public class StackOverflowDemo {
    public static void main(String[] args) {
        recursiveMethod(1);
    }
    
    public static void recursiveMethod(int count) {
        System.out.println("Count: " + count);
        recursiveMethod(count + 1);  // Infinite recursion
        // Eventually throws: java.lang.StackOverflowError
    }
}
```

**Why it happens:**
- Each call adds a frame to stack
- Stack has limited size
- No more space → StackOverflowError

**Solution:**
- Increase stack size: `-Xss2m`
- Fix infinite recursion
- Use iteration instead of recursion

---

## 🎯 Method Area / Metaspace

### What is Method Area?

**Method Area** (called **Metaspace** in Java 8+) stores class-level data.

**Key Features:**
- **Shared**: Among all threads
- **Created**: When JVM starts
- **Stores**: Class metadata, static variables
- **Size**: Configurable
- **GC**: Rarely (class unloading)

---

### Method Area vs PermGen vs Metaspace

**Evolution:**

```
┌────────────────────────────────────────────────────────────┐
│              HISTORY OF METHOD AREA                        │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Java 7 and Earlier:                                      │
│  ┌──────────────────────────────────────────────────┐    │
│  │  PERMGEN (Permanent Generation)                  │    │
│  │  - Fixed size (default 64 MB)                    │    │
│  │  - Part of heap                                  │    │
│  │  - Caused OutOfMemoryError: PermGen space        │    │
│  │  - Hard to tune                                  │    │
│  └──────────────────────────────────────────────────┘    │
│                        ↓                                   │
│  Java 8+:                                                 │
│  ┌──────────────────────────────────────────────────┐    │
│  │  METASPACE                                       │    │
│  │  - Dynamic size (grows as needed)                │    │
│  │  - Native memory (outside heap)                  │    │
│  │  - No more PermGen errors                        │    │
│  │  - Better for dynamic class loading              │    │
│  └──────────────────────────────────────────────────┘    │
└────────────────────────────────────────────────────────────┘
```

---

### What's Stored in Method Area?

```
┌────────────────────────────────────────────────────────────┐
│              METHOD AREA / METASPACE                       │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  1. Class Metadata                                        │
│     - Class name, package, modifiers                      │
│     - Superclass, interfaces                              │
│     - Method signatures                                   │
│     - Field declarations                                  │
│     - Annotations                                         │
│                                                            │
│  2. Static Variables                                      │
│     - Class-level variables (shared)                      │
│     - static int count = 0;                               │
│     - static String name = "Java";                        │
│                                                            │
│  3. Constant Pool                                         │
│     - String literals                                     │
│     - Numeric constants                                   │
│     - Symbolic references                                 │
│                                                            │
│  4. Method Bytecode                                       │
│     - Compiled method code                                │
│     - Instructions for JVM                                │
│                                                            │
│  5. Static Blocks                                         │
│     - Initialization code                                 │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

---

### Method Area Example

```java
public class MethodAreaDemo {
    // Static variable → Method Area
    static int staticCounter = 0;
    static String staticName = "Java";
    
    // Static block → Method Area (code)
    static {
        System.out.println("Static block executed");
        staticCounter = 10;
    }
    
    // Instance variable → Heap (inside object)
    int instanceVar = 20;
    
    // Method code → Method Area (bytecode)
    public static void staticMethod() {
        System.out.println("Static method");
    }
    
    public void instanceMethod() {
        System.out.println("Instance method");
    }
}
```

**Memory Allocation:**

```
METHOD AREA                    HEAP                    STACK
───────────                    ────                    ─────
MethodAreaDemo class           MethodAreaDemo obj      main()
├─ Class metadata              ├─ instanceVar = 20     └─ obj = 0x100 →
├─ staticCounter = 10
├─ staticName = 0x200 ──→ "Java" (in heap)
├─ staticMethod() bytecode
└─ instanceMethod() bytecode
```

---

### Metaspace Configuration

```bash
# Set initial metaspace size
-XX:MetaspaceSize=128m

# Set maximum metaspace size
-XX:MaxMetaspaceSize=512m

# Example: Run with custom metaspace
java -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m MyApp
```

**Metaspace Size Guidelines:**

| Application Type | Recommended Size |
|------------------|------------------|
| **Small apps** | 64 MB - 128 MB |
| **Medium apps** | 128 MB - 256 MB |
| **Large apps** | 256 MB - 512 MB |
| **Dynamic class loading** | 512 MB+ |

---

## 🎯 Heap vs Stack - Complete Comparison

### Detailed Comparison Table

| Feature | Heap | Stack |
|---------|------|-------|
| **Purpose** | Store objects, arrays | Method calls, local variables |
| **Scope** | Global (all threads) | Thread-local (one per thread) |
| **Size** | Large (GBs) | Small (MBs) |
| **Speed** | Slower | Faster |
| **Structure** | Generational (Young, Old) | LIFO (Last In First Out) |
| **Lifecycle** | JVM start → JVM shutdown | Method start → Method end |
| **Access** | Accessible globally | Only current method |
| **Allocation** | Dynamic (runtime) | Static (compile-time) |
| **Deallocation** | Garbage Collection | Automatic (method return) |
| **Memory Management** | Complex (GC) | Simple (automatic) |
| **Fragmentation** | Can occur | No fragmentation |
| **Stores** | Objects, instance variables | Primitives, references |
| **Reference** | Can have multiple references | Single reference (method) |
| **Thread Safety** | Needs synchronization | Thread-safe (isolated) |
| **Error** | OutOfMemoryError: Java heap space | StackOverflowError |
| **Configuration** | -Xms, -Xmx | -Xss |
| **Growth** | Can grow (up to max) | Fixed per thread |

---

### Visual Comparison

```
┌────────────────────────────────────────────────────────────┐
│              HEAP vs STACK COMPARISON                      │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  HEAP MEMORY                      STACK MEMORY            │
│  ┌─────────────────────┐         ┌──────────────┐        │
│  │   [Object 1]        │         │  Frame 3     │ ← Top  │
│  │   [Object 2]        │         ├──────────────┤        │
│  │   [Object 3]        │         │  Frame 2     │        │
│  │   [Object 4]        │         ├──────────────┤        │
│  │   [Array]           │         │  Frame 1     │        │
│  │   [String Pool]     │         └──────────────┘        │
│  │   ...               │         ← Bottom                 │
│  │   (Random access)   │         (LIFO access)            │
│  └─────────────────────┘                                  │
│                                                            │
│  ✅ Shared               ✅ Thread-local                    │
│  ✅ Large size           ✅ Small size                      │
│  ✅ GC managed           ✅ Auto managed                    │
│  🐌 Slower              ⚡ Faster                          │
│  📦 Objects              📝 Method calls                   │
└────────────────────────────────────────────────────────────┘
```

---

### Complete Example: Heap vs Stack

```java
public class HeapStackDemo {
    // Static variable → Method Area
    static int staticVar = 100;
    
    // Instance variable → Heap (inside object)
    int instanceVar = 200;
    
    public static void main(String[] args) {
        // Local primitive → Stack
        int localPrimitive = 10;
        
        // Reference → Stack, Object → Heap
        HeapStackDemo obj = new HeapStackDemo();
        
        // Call method
        obj.method(5);
    }
    
    public void method(int param) {  // param → Stack
        // Local primitive → Stack
        int local = 20;
        
        // Reference → Stack, Array → Heap
        int[] arr = new int[5];
        
        // Reference → Stack, String → Heap (String Pool)
        String str = "Hello";
        
        // Reference → Stack, Object → Heap
        Employee emp = new Employee("Alice", 25);
    }
}

class Employee {
    // Instance variables → Heap (inside Employee object)
    String name;
    int age;
    
    Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

**Complete Memory Visualization:**

```
┌────────────────────────────────────────────────────────────┐
│                  COMPLETE MEMORY MAP                       │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  METHOD AREA                                              │
│  ┌──────────────────────────────────────────────────┐    │
│  │  HeapStackDemo class metadata                    │    │
│  │  Employee class metadata                         │    │
│  │  staticVar = 100                                 │    │
│  └──────────────────────────────────────────────────┘    │
│                                                            │
│  HEAP MEMORY                                              │
│  ┌──────────────────────────────────────────────────┐    │
│  │  HeapStackDemo object                            │    │
│  │  ├─ instanceVar = 200                            │    │
│  │                                                   │    │
│  │  int[] array                                     │    │
│  │  └─ [0, 0, 0, 0, 0]                              │    │
│  │                                                   │    │
│  │  String "Hello" (String Pool)                    │    │
│  │                                                   │    │
│  │  Employee object                                 │    │
│  │  ├─ name = 0x500 ───→ "Alice" (String Pool)     │    │
│  │  └─ age = 25                                     │    │
│  └──────────────────────────────────────────────────┘    │
│                                                            │
│  STACK MEMORY (Thread-1)                                  │
│  ┌──────────────────────────────────────────────────┐    │
│  │  method(5)                    ← Current Frame    │    │
│  │  ├─ param = 5                                    │    │
│  │  ├─ local = 20                                   │    │
│  │  ├─ arr = 0x300 ────→ (points to array in heap) │    │
│  │  ├─ str = 0x400 ────→ (points to "Hello")       │    │
│  │  └─ emp = 0x500 ────→ (points to Employee)      │    │
│  ├──────────────────────────────────────────────────┤    │
│  │  main()                                          │    │
│  │  ├─ args = 0x100 ───→ (String[] in heap)        │    │
│  │  ├─ localPrimitive = 10                          │    │
│  │  └─ obj = 0x200 ────→ (HeapStackDemo object)    │    │
│  └──────────────────────────────────────────────────┘    │
└────────────────────────────────────────────────────────────┘
```

---

### When to Use What?

**Use Stack when:**
- ✅ Local variables (method scope)
- ✅ Method parameters
- ✅ Short-lived data
- ✅ Fixed size data

**Use Heap when:**
- ✅ Objects need to survive beyond method
- ✅ Objects shared across methods/threads
- ✅ Dynamic size data
- ✅ Long-lived data

---

## 🎯 Primitive vs Wrapper: Memory Location

### 🔥 CRITICAL CONCEPT

**Memory location depends on WHERE the variable is declared, not just the type!**

---

### Rule Summary

```
┌────────────────────────────────────────────────────────────┐
│          MEMORY LOCATION DECISION TREE                     │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  WHERE is the variable declared?                          │
│            │                                               │
│            ├─ Inside Method (Local Variable)              │
│            │      ├─ Primitive: ENTIRE value in Stack     │
│            │      └─ Wrapper/Object: Ref in Stack,        │
│            │                         Object in Heap       │
│            │                                               │
│            ├─ Instance Variable (Class Field)             │
│            │      ├─ Primitive: Heap (inside object)      │
│            │      └─ Wrapper/Object: Ref in Heap,         │
│            │                         Object in Heap       │
│            │                                               │
│            └─ Static Variable (Class-Level)               │
│                   ├─ Primitive: Method Area               │
│                   └─ Wrapper/Object: Ref in Method Area,  │
│                                      Object in Heap       │
└────────────────────────────────────────────────────────────┘
```

---

### 📍 Scenario 1: Local Variables (Inside Methods)

```java
public void method() {
    // LOCAL VARIABLES
    
    // Primitives - ENTIRE value in Stack
    int primitiveInt = 10;           // Stack
    double primitiveDouble = 5.5;    // Stack
    boolean primitiveBoolean = true; // Stack
    char primitiveChar = 'A';        // Stack
    
    // Wrappers - Reference in Stack, Object in Heap
    Integer wrapperInt = 20;         // Ref: Stack, Obj: Heap
    Double wrapperDouble = 6.6;      // Ref: Stack, Obj: Heap
    Boolean wrapperBoolean = false;  // Ref: Stack, Obj: Heap
    
    // String - Reference in Stack, Object in Heap
    String str = "Hello";            // Ref: Stack, Obj: Heap (String Pool)
}
```

**Memory Visualization:**

```
STACK (Local Variables)              HEAP
───────────────────────              ────
method()
├─ primitiveInt = 10
├─ primitiveDouble = 5.5
├─ primitiveBoolean = true
├─ primitiveChar = 'A'
├─ wrapperInt = 0x100 ──────────────→ Integer(20)
├─ wrapperDouble = 0x200 ───────────→ Double(6.6)
├─ wrapperBoolean = 0x300 ──────────→ Boolean(false)
└─ str = 0x400 ─────────────────────→ "Hello" (String Pool)
```

---

### 📍 Scenario 2: Instance Variables (Class Fields)

```java
public class MyClass {
    // INSTANCE VARIABLES - All stored in Heap (part of object)
    
    // Primitives - Inside object in Heap
    int primitiveField = 10;
    double doubleField = 5.5;
    boolean booleanField = true;
    
    // Wrappers - Reference in Heap, Object in Heap
    Integer wrapperField = 20;
    String stringField = "Java";
}

public static void main(String[] args) {
    MyClass obj = new MyClass();  // obj ref in Stack
}
```

**Memory Visualization:**

```
STACK                               HEAP
─────                               ────
main()                              MyClass Object (0x500)
└─ obj = 0x500 ────────────────────→ ├─ primitiveField = 10
                                     ├─ doubleField = 5.5
                                     ├─ booleanField = true
                                     ├─ wrapperField = 0x600 ──→ Integer(20)
                                     └─ stringField = 0x700 ───→ "Java"
```

---

### 📍 Scenario 3: Static Variables (Class-Level)

```java
public class MyClass {
    // STATIC VARIABLES - Stored in Method Area
    
    // Static primitives - Method Area
    static int staticPrimitive = 100;
    static double staticDouble = 5.5;
    
    // Static wrappers - Ref in Method Area, Object in Heap
    static Integer staticWrapper = 200;
    static String staticString = "Static";
}
```

**Memory Visualization:**

```
METHOD AREA                         HEAP
───────────                         ────
MyClass metadata
├─ staticPrimitive = 100
├─ staticDouble = 5.5
├─ staticWrapper = 0x800 ──────────→ Integer(200)
└─ staticString = 0x900 ───────────→ "Static" (String Pool)
```

---

### 📊 Complete Memory Location Table

| Variable Type | Local Variable | Instance Variable | Static Variable |
|---------------|----------------|-------------------|-----------------|
| **Primitive** (`int`, `double`, `boolean`) | ✅ Stack | ✅ Heap (inside object) | ✅ Method Area |
| **Wrapper** (`Integer`, `Double`, `Boolean`) | Ref→Stack<br/>Obj→Heap | Ref→Heap<br/>Obj→Heap | Ref→Method Area<br/>Obj→Heap |
| **String** | Ref→Stack<br/>Obj→Heap | Ref→Heap<br/>Obj→Heap | Ref→Method Area<br/>Obj→Heap |
| **Custom Object** | Ref→Stack<br/>Obj→Heap | Ref→Heap<br/>Obj→Heap | Ref→Method Area<br/>Obj→Heap |

---

### 💡 Integer Caching (Important!)

Java **caches** Integer objects from **-128 to 127** for performance:

```java
public void testCaching() {
    // Cached Integers (-128 to 127)
    Integer a = 100;
    Integer b = 100;
    System.out.println(a == b);  // ✅ true (same cached object)
    System.out.println(a.equals(b));  // ✅ true
    
    // Non-cached Integers (outside -128 to 127)
    Integer x = 200;
    Integer y = 200;
    System.out.println(x == y);  // ❌ false (different objects)
    System.out.println(x.equals(y));  // ✅ true (same value)
}
```

**Visual:**

```
HEAP (Integer Cache)
────────────────────
[-128] → Integer(-128)  ← Cached
[  -1] → Integer(-1)
[   0] → Integer(0)
[   1] → Integer(1)
[ 100] → Integer(100)  ← a and b point here
[ 127] → Integer(127)  ← Cached

[ 200] → Integer(200)  ← x points here
[ 200] → Integer(200)  ← y points here (different object!)
```

**Cached Wrapper Ranges:**

| Wrapper Type | Cached Range |
|--------------|--------------|
| **Integer** | -128 to 127 |
| **Long** | -128 to 127 |
| **Short** | -128 to 127 |
| **Byte** | All values (-128 to 127) |
| **Character** | 0 to 127 |
| **Boolean** | true and false |
| **Float/Double** | ❌ Not cached |

---

### 🎯 Performance: Primitive vs Wrapper

```java
// ❌ BAD - Slow due to auto-boxing
public int sumWithWrapper() {
    Integer sum = 0;  // Wrapper
    for (int i = 0; i < 1000000; i++) {
        sum += i;  // Auto-boxing/unboxing on each iteration!
    }
    return sum;
}

// ✅ GOOD - Fast, no object creation
public int sumWithPrimitive() {
    int sum = 0;  // Primitive
    for (int i = 0; i < 1000000; i++) {
        sum += i;  // Direct addition
    }
    return sum;
}
```

**Performance Comparison:**
- **Primitive**: ~10x faster
- **No garbage collection overhead**
- **Less memory usage**

---

### 🔥 Interview Questions: Primitive vs Wrapper

**Q1: Where is `int x = 10;` stored if declared inside a method?**
- **A:** Stack (local primitive variable)

**Q2: Where is `Integer x = 10;` stored if declared inside a method?**
- **A:** Reference (`x`) → Stack, Object (Integer(10)) → Heap (cached)

**Q3: Where is `int age;` stored if it's an instance variable?**
- **A:** Heap (inside the object that contains it)

**Q4: Why is `Integer a = 127; Integer b = 127; a == b` true?**
- **A:** Java caches Integer objects from -128 to 127, so both point to the same cached object

**Q5: When should we use primitives vs wrappers?**
- **A:** 
  - Use **primitives** for: Performance, simple calculations, local variables
  - Use **wrappers** for: Collections (List<Integer>), null values, generics

---

## 🎯 Garbage Collection

### What is Garbage Collection?

**Garbage Collection (GC)** is Java's automatic memory management that identifies and removes unused objects to reclaim memory.

**Definition:**
- Automatic process to free up memory
- Removes objects with **no references**
- Runs in background (separate thread)
- Prevents memory leaks

**Benefits:**
1. ✅ **Automatic**: No manual memory management
2. ✅ **Prevents Leaks**: Unused objects removed
3. ✅ **Safer**: No dangling pointers
4. ✅ **Productivity**: Focus on logic, not memory

**Drawbacks:**
1. ❌ **Performance Overhead**: GC pauses application
2. ❌ **Unpredictable**: Cannot control when GC runs
3. ❌ **Stop-the-World**: Application pauses during GC

---

### How Garbage Collection Works

**GC Process (3 Phases):**

```
┌────────────────────────────────────────────────────────────┐
│              GARBAGE COLLECTION PROCESS                    │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Phase 1: MARK                                            │
│  ┌──────────────────────────────────────────────────┐    │
│  │  - Identify all reachable objects                │    │
│  │  - Start from GC Roots:                          │    │
│  │    * Static variables                            │    │
│  │    * Active threads                              │    │
│  │    * JNI references                              │    │
│  │  - Mark all referenced objects as "alive"        │    │
│  └──────────────────────────────────────────────────┘    │
│            ↓                                               │
│  Phase 2: SWEEP                                           │
│  ┌──────────────────────────────────────────────────┐    │
│  │  - Remove unmarked (unreachable) objects         │    │
│  │  - Reclaim memory                                │    │
│  │  - Add to free list                              │    │
│  └──────────────────────────────────────────────────┘    │
│            ↓                                               │
│  Phase 3: COMPACT (Optional)                              │
│  ┌──────────────────────────────────────────────────┐    │
│  │  - Move surviving objects together               │    │
│  │  - Reduce fragmentation                          │    │
│  │  - Create contiguous free space                  │    │
│  └──────────────────────────────────────────────────┘    │
└────────────────────────────────────────────────────────────┘
```

---

### GC Roots

**What are GC Roots?**

Objects that are **always reachable** and serve as starting points for marking:

```
┌────────────────────────────────────────────────────────────┐
│                    GC ROOTS                                │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  1. Static Variables                                      │
│     static Employee manager = new Employee();             │
│                                                            │
│  2. Active Threads                                        │
│     Current thread, thread pool threads                   │
│                                                            │
│  3. Local Variables (on Stack)                            │
│     Variables in currently executing methods              │
│                                                            │
│  4. JNI References                                        │
│     Native code references                                │
│                                                            │
│  5. Class Objects                                         │
│     Loaded classes                                        │
└────────────────────────────────────────────────────────────┘
```

**Reachability:**
- Object is **reachable** if path exists from any GC Root
- Object is **unreachable** if no path from any GC Root → Eligible for GC

---

### When Object Becomes Eligible for GC

#### 1️⃣ Nullifying Reference

```java
Employee emp = new Employee();
emp = null;  // Now eligible for GC
```

**Visual:**
```
Before:
Stack: emp = 0x100 ──→ Heap: Employee object

After:
Stack: emp = null      Heap: Employee object ← Eligible for GC!
```

---

#### 2️⃣ Re-assigning Reference

```java
Employee emp1 = new Employee("Alice");
Employee emp2 = new Employee("Bob");
emp1 = emp2;  // First Employee object eligible for GC
```

**Visual:**
```
Before:
emp1 = 0x100 ──→ Employee("Alice")
emp2 = 0x200 ──→ Employee("Bob")

After:
emp1 = 0x200 ─┐
emp2 = 0x200 ─┴──→ Employee("Bob")
Employee("Alice") ← Eligible for GC!
```

---

#### 3️⃣ Object Created Inside Method

```java
public void createEmployee() {
    Employee emp = new Employee();
    // After method ends, 'emp' reference removed from stack
    // Employee object eligible for GC
}
```

**Visual:**
```
During method:
Stack: emp = 0x100 ──→ Heap: Employee object

After method ends:
Stack: [empty]         Heap: Employee object ← Eligible for GC!
```

---

#### 4️⃣ Island of Isolation

```java
class Node {
    Node next;
}

public void createIsland() {
    Node n1 = new Node();
    Node n2 = new Node();
    n1.next = n2;
    n2.next = n1;  // Circular reference
    
    n1 = null;
    n2 = null;
    // Both objects only reference each other (island)
    // No external reference → Both eligible for GC
}
```

**Visual:**
```
┌─────────────────────────────────────────┐
│  Island of Isolation                    │
├─────────────────────────────────────────┤
│                                         │
│   Node 1  ←─────→  Node 2              │
│     ↑                  ↓                │
│     └──────────────────┘                │
│                                         │
│   No external references!               │
│   Both eligible for GC                  │
└─────────────────────────────────────────┘
```

---

#### 5️⃣ Anonymous Object

```java
new Employee();  // No reference, immediately eligible for GC
```

---

### Types of Garbage Collection

#### 1️⃣ Minor GC (Young Generation)

**Target:** Young Generation (Eden + Survivor spaces)

**Frequency:** Very frequent

**Speed:** Fast (small area)

**Pause:** Short (milliseconds)

**Trigger:** Eden space full

**Process:**
1. Mark live objects in Eden and S0/S1
2. Copy live objects to other Survivor space
3. Clear Eden and source Survivor space
4. Increment age of surviving objects
5. Promote old objects (age ≥ 15) to Old Gen

---

#### 2️⃣ Major GC (Old Generation)

**Target:** Old Generation

**Frequency:** Less frequent

**Speed:** Slower (larger area)

**Pause:** Longer (can be seconds)

**Trigger:** Old Gen nearly full

**Process:**
1. Mark live objects in Old Gen
2. Sweep dead objects
3. Compact (optional, reduces fragmentation)

---

#### 3️⃣ Full GC (Entire Heap)

**Target:** Both Young and Old Generations

**Frequency:** Rare

**Speed:** Slowest

**Pause:** Longest (stop-the-world)

**Trigger:**
- Old Gen full
- Metaspace full
- System.gc() called (not recommended)
- Concurrent GC failure

---

### GC Algorithms & Collectors

```
┌────────────────────────────────────────────────────────────┐
│              GC ALGORITHMS COMPARISON                      │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  1. Serial GC (-XX:+UseSerialGC)                          │
│     - Single thread for GC                                │
│     - Stop-the-world pauses                               │
│     - Use: Small applications, single CPU                 │
│     - Best: Client-side apps                              │
│                                                            │
│  2. Parallel GC (-XX:+UseParallelGC) [Default Java 8]    │
│     - Multiple threads for GC                             │
│     - Throughput-focused (max work done)                  │
│     - Stop-the-world pauses                               │
│     - Use: Batch processing, background tasks             │
│     - Best: High throughput requirements                  │
│                                                            │
│  3. CMS - Concurrent Mark Sweep (-XX:+UseConcMarkSweepGC) │
│     - Concurrent with application                         │
│     - Low pause times                                     │
│     - No compaction (fragmentation)                       │
│     - Deprecated (removed in Java 14)                     │
│     - Use: Low latency requirements (legacy)              │
│                                                            │
│  4. G1 GC - Garbage First (-XX:+UseG1GC) [Default Java 9+]│
│     - Region-based heap                                   │
│     - Predictable pause times                             │
│     - Concurrent + incremental                            │
│     - Compacting (reduces fragmentation)                  │
│     - Use: Large heaps (>4GB), balanced performance       │
│     - Best: Most modern applications                      │
│                                                            │
│  5. ZGC (-XX:+UseZGC) [Java 11+]                          │
│     - Ultra-low latency (< 10ms pauses)                   │
│     - Concurrent operations                               │
│     - Scalable (handles TB heaps)                         │
│     - Use: Real-time systems, very low latency            │
│     - Best: Mission-critical, low-latency apps            │
│                                                            │
│  6. Shenandoah (-XX:+UseShenandoahGC) [Java 12+]         │
│     - Low pause times (similar to ZGC)                    │
│     - Concurrent compaction                               │
│     - Use: Low latency requirements                       │
│     - Alternative: To ZGC                                 │
└────────────────────────────────────────────────────────────┘
```

---

### GC Algorithm Comparison Table

| Algorithm | Threads | Pause Time | Throughput | Compacting | Best For |
|-----------|---------|------------|------------|------------|----------|
| **Serial** | Single | High | Low | Yes | Small apps |
| **Parallel** | Multiple | High | High | Yes | Batch processing |
| **CMS** | Multiple | Low | Medium | No | Low latency (legacy) |
| **G1** | Multiple | Predictable | High | Yes | General purpose |
| **ZGC** | Multiple | Ultra-low | High | Yes | Real-time systems |
| **Shenandoah** | Multiple | Low | High | Yes | Low latency |

---

### G1 GC Detailed (Default in Java 9+)

**G1 (Garbage First) Features:**

```
┌────────────────────────────────────────────────────────────┐
│              G1 GC ARCHITECTURE                            │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Heap divided into REGIONS (each 1-32 MB):                │
│                                                            │
│  ┌──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┐      │
│  │E │E │S │S │O │O │O │O │O │O │O │O │O │H │H │ │      │
│  └──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┘      │
│   E = Eden   S = Survivor   O = Old   H = Humongous      │
│                                                            │
│  Key Features:                                            │
│  1. Predictable Pause Times                               │
│     -XX:MaxGCPauseMillis=200 (target 200ms)              │
│                                                            │
│  2. Garbage First Strategy                                │
│     - Collects regions with most garbage first            │
│     - Maximizes reclaimed space                           │
│                                                            │
│  3. Concurrent Marking                                    │
│     - Runs concurrently with application                  │
│     - Identifies live objects                             │
│                                                            │
│  4. Compacting                                            │
│     - Reduces fragmentation                               │
│     - Improves allocation performance                     │
└────────────────────────────────────────────────────────────┘
```

**G1 GC Configuration:**

```bash
# Enable G1 GC (default in Java 9+)
-XX:+UseG1GC

# Set target pause time (default 200ms)
-XX:MaxGCPauseMillis=200

# Set heap region size (1-32 MB, automatic by default)
-XX:G1HeapRegionSize=4m

# Example: Run with G1 GC
java -XX:+UseG1GC -XX:MaxGCPauseMillis=100 -Xms2g -Xmx2g MyApp
```

---

### finalize() Method (Deprecated)

**What is finalize()?**
- Method called by GC **before** object is removed
- Gives object chance to clean up resources
- **Deprecated in Java 9**

**Problems with finalize():**
1. ❌ **No guarantee when** (or if) it will be called
2. ❌ **Can resurrect objects** (prevent GC)
3. ❌ **Performance overhead** (slows GC)
4. ❌ **Can cause memory leaks**
5. ❌ **Unpredictable behavior**

**Example:**

```java
public class FinalizeDemo {
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Finalize called");
        // Cleanup code here
        super.finalize();
    }
}

// ❌ DON'T USE finalize()!
```

---

### Modern Alternatives to finalize()

#### 1️⃣ try-with-resources (Recommended)

```java
// ✅ GOOD: Automatic resource management
try (FileInputStream fis = new FileInputStream("file.txt");
     BufferedReader br = new BufferedReader(new InputStreamReader(fis))) {
    // Use resources
} // Automatically closed, even if exception occurs
```

---

#### 2️⃣ Cleaner API (Java 9+)

```java
import java.lang.ref.Cleaner;

public class CleanerDemo implements AutoCloseable {
    private static final Cleaner cleaner = Cleaner.create();
    
    private final Cleaner.Cleanable cleanable;
    
    public CleanerDemo() {
        this.cleanable = cleaner.register(this, new CleanupAction());
    }
    
    @Override
    public void close() {
        cleanable.clean();
    }
    
    private static class CleanupAction implements Runnable {
        @Override
        public void run() {
            // Cleanup code
            System.out.println("Cleanup performed");
        }
    }
}
```

---

### Requesting Garbage Collection

```java
// Request GC (not guaranteed to run!)
System.gc();  // or
Runtime.getRuntime().gc();
```

**❌ DON'T call System.gc()!**
- GC knows best when to run
- Calling it can hurt performance
- Only JVM should decide

**When to call System.gc():**
- ✅ In testing/debugging only
- ❌ Never in production code

---

### GC Monitoring & Tuning

**Enable GC Logging:**

```bash
# Java 8
-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:gc.log

# Java 9+
-Xlog:gc*:file=gc.log:time,level,tags

# Example
java -Xlog:gc*:file=gc.log -Xms512m -Xmx2g MyApp
```

**GC Monitoring Tools:**
1. **jstat**: Command-line GC statistics
2. **jconsole**: GUI monitoring tool
3. **VisualVM**: Visual monitoring and profiling
4. **JProfiler**: Commercial profiling tool
5. **GC logs**: Analyze GC behavior

**jstat Example:**

```bash
# Monitor GC statistics every 1 second
jstat -gc <pid> 1000

# Output columns:
# S0C, S1C, S0U, S1U, EC, EU, OC, OU, MC, MU, CCSC, CCSU, YGC, YGCT, FGC, FGCT, GCT
```

---

### GC Tuning Goals

```
┌────────────────────────────────────────────────────────────┐
│              GC TUNING GOALS                               │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  1. THROUGHPUT                                            │
│     Goal: Maximize application work                       │
│     Metric: % time NOT in GC                              │
│     Target: > 95%                                         │
│     Algorithm: Parallel GC                                │
│                                                            │
│  2. LATENCY                                               │
│     Goal: Minimize pause times                            │
│     Metric: Max pause time                                │
│     Target: < 100ms                                       │
│     Algorithm: G1, ZGC, Shenandoah                        │
│                                                            │
│  3. FOOTPRINT                                             │
│     Goal: Minimize memory usage                           │
│     Metric: Heap size                                     │
│     Target: As small as possible                          │
│     Trade-off: Lower throughput/higher latency            │
└────────────────────────────────────────────────────────────┘
```

**Trade-offs:**
- High throughput ↔ Higher latency
- Low latency ↔ Lower throughput
- Small footprint ↔ Lower throughput, higher latency

---

### GC Best Practices

1. ✅ **Choose right GC algorithm** based on requirements
2. ✅ **Set appropriate heap size** (-Xms, -Xmx)
3. ✅ **Monitor GC behavior** (use logs/tools)
4. ✅ **Avoid large objects** (they go directly to Old Gen)
5. ✅ **Nullify large references** when done
6. ✅ **Use object pooling** for frequently created objects (carefully!)
7. ✅ **Profile your application** before tuning
8. ❌ **Don't call System.gc()**
9. ❌ **Don't use finalize()**
10. ❌ **Don't over-tune** (default settings often good)

---

## 🎯 Reference Types

Java provides **4 types of references** with different GC behaviors:

```
┌────────────────────────────────────────────────────────────┐
│              REFERENCE TYPES HIERARCHY                     │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Strong (Default)                                         │
│  ├─ Never GC'd while reachable                            │
│  └─ Object obj = new Object();                            │
│                                                            │
│  Soft (java.lang.ref.SoftReference)                       │
│  ├─ GC'd before OutOfMemoryError                          │
│  └─ new SoftReference<>(object);                          │
│                                                            │
│  Weak (java.lang.ref.WeakReference)                       │
│  ├─ GC'd in next GC cycle                                 │
│  └─ new WeakReference<>(object);                          │
│                                                            │
│  Phantom (java.lang.ref.PhantomReference)                 │
│  ├─ GC'd immediately, for cleanup                         │
│  └─ new PhantomReference<>(object, queue);                │
└────────────────────────────────────────────────────────────┘
```

---

### 1️⃣ Strong Reference (Default)

**Definition:** Normal reference (default behavior)

**GC Behavior:** **Never** collected while reachable

**Use Case:** Normal objects

```java
// Strong reference (default)
Object obj = new Object();

// Object will NOT be GC'd until:
// - obj is set to null
// - obj goes out of scope
// - No other strong references exist
```

**Memory:**
```
Stack: obj = 0x100 ──→ Heap: Object (NEVER GC'd while referenced)
```

---

### 2️⃣ Soft Reference

**Definition:** Reference cleared **before** OutOfMemoryError

**Package:** `java.lang.ref.SoftReference`

**GC Behavior:**
- Kept as long as memory available
- Cleared when memory low (before OOM)
- JVM guarantees all soft refs cleared before throwing OOM

**Use Case:** **Memory-sensitive caches**

```java
import java.lang.ref.SoftReference;

// Create soft reference
Object obj = new Object();
SoftReference<Object> softRef = new SoftReference<>(obj);
obj = null;  // Remove strong reference

// Get object (may return null if GC'd)
Object retrieved = softRef.get();
if (retrieved != null) {
    // Object still available
} else {
    // Object was GC'd due to memory pressure
}
```

**Example: Image Cache**

```java
import java.lang.ref.SoftReference;
import java.util.*;

public class ImageCache {
    private Map<String, SoftReference<Image>> cache = new HashMap<>();
    
    public Image getImage(String filename) {
        SoftReference<Image> ref = cache.get(filename);
        
        if (ref != null) {
            Image img = ref.get();
            if (img != null) {
                return img;  // Cache hit
            }
        }
        
        // Cache miss - load image
        Image img = loadImage(filename);
        cache.put(filename, new SoftReference<>(img));
        return img;
    }
    
    private Image loadImage(String filename) {
        // Load image from disk
        return new Image(filename);
    }
}

class Image {
    String filename;
    Image(String filename) { this.filename = filename; }
}
```

---

### 3️⃣ Weak Reference

**Definition:** Reference cleared **in next GC cycle**

**Package:** `java.lang.ref.WeakReference`

**GC Behavior:**
- Cleared in next GC (if no strong references)
- More aggressive than soft reference
- No memory pressure needed

**Use Case:** **Canonical mappings**, **metadata**

```java
import java.lang.ref.WeakReference;

// Create weak reference
Object obj = new Object();
WeakReference<Object> weakRef = new WeakReference<>(obj);
obj = null;  // Remove strong reference

// Get object (may return null after GC)
Object retrieved = weakRef.get();
if (retrieved != null) {
    // Object still available
} else {
    // Object was GC'd
}
```

**WeakHashMap Example:**

```java
import java.util.WeakHashMap;

public class WeakHashMapDemo {
    public static void main(String[] args) {
        WeakHashMap<Employee, String> map = new WeakHashMap<>();
        
        Employee emp1 = new Employee("Alice");
        Employee emp2 = new Employee("Bob");
        
        map.put(emp1, "Developer");
        map.put(emp2, "Manager");
        
        emp1 = null;  // Remove strong reference
        
        System.gc();  // Request GC
        
        // emp1 entry will be removed from map
        // emp2 entry still there (strong reference exists)
    }
}

class Employee {
    String name;
    Employee(String name) { this.name = name; }
}
```

---

### 4️⃣ Phantom Reference

**Definition:** Weakest reference, for **post-mortem cleanup**

**Package:** `java.lang.ref.PhantomReference`

**GC Behavior:**
- get() **always returns null**
- Object already finalized
- Used for cleanup actions
- Requires ReferenceQueue

**Use Case:** **Cleanup after GC**, **alternative to finalize()**

```java
import java.lang.ref.*;

public class PhantomReferenceDemo {
    public static void main(String[] args) {
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
        
        Object obj = new Object();
        PhantomReference<Object> phantomRef = 
            new PhantomReference<>(obj, queue);
        
        System.out.println(phantomRef.get());  // Always null!
        
        obj = null;  // Remove strong reference
        
        System.gc();  // Request GC
        
        // After GC, reference will be enqueued
        Reference<?> ref = queue.poll();
        if (ref != null) {
            // Object was finalized, perform cleanup
            System.out.println("Cleanup can be performed");
        }
    }
}
```

---

### Reference Comparison Table

| Type | GC Behavior | get() After GC | Use Case | Package |
|------|-------------|----------------|----------|---------|
| **Strong** | Never (while reachable) | Accessible | Normal objects | Default |
| **Soft** | Before OutOfMemoryError | null | Memory-sensitive cache | java.lang.ref.SoftReference |
| **Weak** | Next GC cycle | null | Canonical mappings | java.lang.ref.WeakReference |
| **Phantom** | After finalization | Always null | Post-mortem cleanup | java.lang.ref.PhantomReference |

---

### Reference Queue

**What is ReferenceQueue?**
- Queue where references are enqueued after GC
- Notifies when objects are collected
- Used with Soft, Weak, Phantom references

```java
import java.lang.ref.*;

public class ReferenceQueueDemo {
    public static void main(String[] args) throws InterruptedException {
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
        
        Object obj1 = new Object();
        Object obj2 = new Object();
        
        WeakReference<Object> weak1 = new WeakReference<>(obj1, queue);
        WeakReference<Object> weak2 = new WeakReference<>(obj2, queue);
        
        obj1 = null;
        obj2 = null;
        
        System.gc();
        Thread.sleep(100);
        
        // Check which references were collected
        Reference<?> ref;
        while ((ref = queue.poll()) != null) {
            System.out.println("Reference collected: " + ref);
        }
    }
}
```

---

### Soft vs Weak Reference

| Feature | Soft Reference | Weak Reference |
|---------|----------------|----------------|
| **Cleared When** | Memory low (before OOM) | Next GC cycle |
| **Survival** | Survives multiple GCs | Survives until next GC |
| **Use Case** | Caches | Canonical mappings |
| **Memory Sensitivity** | Yes | No |
| **Performance** | Better for caches | More aggressive cleanup |

**When to Use:**
- **Soft**: Long-term cache that survives until memory needed
- **Weak**: Short-term cache or metadata

---

## 🎯 Class Loading Mechanism

### What is Class Loading?

**Class Loading** is the process of **loading .class files into memory** and making them available for execution.

**When Classes are Loaded:**
- First time class is referenced
- Creating instance (new)
- Accessing static member
- Loading child class (parent loaded first)

---

### Class Loader Hierarchy

```
┌────────────────────────────────────────────────────────────┐
│              CLASS LOADER HIERARCHY                        │
├────────────────────────────────────────────────────────────┤
│                                                            │
│           Bootstrap ClassLoader (null)                     │
│           (Native C/C++)                                   │
│           ├─ Loads: JRE core classes                       │
│           ├─ Location: <JAVA_HOME>/jre/lib/rt.jar         │
│           └─ Example: java.lang.*, java.util.*            │
│                       ↓                                     │
│           Extension ClassLoader                            │
│           (Java: sun.misc.Launcher$ExtClassLoader)         │
│           ├─ Loads: Extension classes                      │
│           ├─ Location: <JAVA_HOME>/jre/lib/ext            │
│           └─ Example: javax.*, third-party libs            │
│                       ↓                                     │
│           Application ClassLoader                          │
│           (Java: sun.misc.Launcher$AppClassLoader)         │
│           ├─ Loads: Application classes                    │
│           ├─ Location: CLASSPATH                           │
│           └─ Example: Your .class files                    │
│                       ↓                                     │
│           Custom ClassLoader (Optional)                    │
│           (User-defined)                                   │
│           ├─ Loads: Dynamic classes                        │
│           └─ Example: Servlet containers, plugins          │
└────────────────────────────────────────────────────────────┘
```

---

### Parent Delegation Model

**How it Works:**
1. Class loader receives request to load class
2. Delegates to parent class loader first
3. Parent tries to load (recursive)
4. If parent can't load, child tries
5. If no one can load → ClassNotFoundException

```
┌────────────────────────────────────────────────────────────┐
│          PARENT DELEGATION MODEL                           │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Request: Load MyClass.class                              │
│                                                            │
│  Step 1: Application ClassLoader                          │
│          ↓ (Delegate to parent)                           │
│  Step 2: Extension ClassLoader                            │
│          ↓ (Delegate to parent)                           │
│  Step 3: Bootstrap ClassLoader                            │
│          ├─ Can load? → Yes → Load & Return               │
│          └─ Can load? → No  → Return to child             │
│                ↓                                           │
│  Step 4: Extension ClassLoader                            │
│          ├─ Can load? → Yes → Load & Return               │
│          └─ Can load? → No  → Return to child             │
│                ↓                                           │
│  Step 5: Application ClassLoader                          │
│          ├─ Can load? → Yes → Load & Return               │
│          └─ Can load? → No  → ClassNotFoundException      │
└────────────────────────────────────────────────────────────┘
```

**Benefits:**
1. ✅ **Prevents duplicate loading** of classes
2. ✅ **Security** - core classes can't be replaced
3. ✅ **Namespace separation** - same class from different loaders

---

### Class Loading Process (3 Phases)

```
┌────────────────────────────────────────────────────────────┐
│              CLASS LOADING PHASES                          │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Phase 1: LOADING                                         │
│  ┌──────────────────────────────────────────────────┐    │
│  │  - Find .class file                              │    │
│  │  - Read bytecode into memory                     │    │
│  │  - Create Class object in method area            │    │
│  │  - If not found → ClassNotFoundException         │    │
│  └──────────────────────────────────────────────────┘    │
│            ↓                                               │
│  Phase 2: LINKING                                         │
│  ┌──────────────────────────────────────────────────┐    │
│  │  Step 2.1: VERIFICATION                          │    │
│  │  - Verify bytecode structure                     │    │
│  │  - Check security constraints                    │    │
│  │  - Ensure JVM safety                             │    │
│  │  - If invalid → VerifyError                      │    │
│  │                                                   │    │
│  │  Step 2.2: PREPARATION                           │    │
│  │  - Allocate memory for static variables          │    │
│  │  - Set default values (0, null, false)           │    │
│  │  - NOT initialize (happens in initialization)    │    │
│  │                                                   │    │
│  │  Step 2.3: RESOLUTION                            │    │
│  │  - Replace symbolic references with direct refs  │    │
│  │  - Resolve class/field/method references         │    │
│  │  - Load referenced classes if needed             │    │
│  └──────────────────────────────────────────────────┘    │
│            ↓                                               │
│  Phase 3: INITIALIZATION                                  │
│  ┌──────────────────────────────────────────────────┐    │
│  │  - Execute static initializers                   │    │
│  │  - Assign actual values to static variables      │    │
│  │  - Run static blocks                             │    │
│  │  - Top-down order (parent before child)          │    │
│  └──────────────────────────────────────────────────┘    │
└────────────────────────────────────────────────────────────┘
```

---

### Class Loading Example

```java
class Parent {
    static int parentStatic = 10;
    
    static {
        System.out.println("Parent static block");
    }
    
    {
        System.out.println("Parent instance block");
    }
    
    Parent() {
        System.out.println("Parent constructor");
    }
}

class Child extends Parent {
    static int childStatic = 20;
    
    static {
        System.out.println("Child static block");
    }
    
    {
        System.out.println("Child instance block");
    }
    
    Child() {
        System.out.println("Child constructor");
    }
}

public class ClassLoadingDemo {
    public static void main(String[] args) {
        System.out.println("Main started");
        Child obj = new Child();
        System.out.println("Main ended");
    }
}
```

**Output:**

```
Main started
Parent static block       ← Parent class loaded & initialized
Child static block        ← Child class loaded & initialized
Parent instance block     ← Parent object initialization
Parent constructor        ← Parent object construction
Child instance block      ← Child object initialization
Child constructor         ← Child object construction
Main ended
```

**Execution Order:**
1. **Static blocks** (class loading - once)
2. **Instance blocks** (object creation)
3. **Constructors** (object creation)

**Class vs Instance:**
- **Static blocks**: Execute when class is **loaded** (once)
- **Instance blocks + Constructors**: Execute when object is **created** (each time)

---

### Custom Class Loader

**Why Custom Class Loaders?**
- Load classes from non-standard sources (network, database)
- Hot deployment (reload classes without restart)
- Class isolation (different versions of same class)

**Example:**

```java
public class CustomClassLoader extends ClassLoader {
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        try {
            // Load class bytecode from custom source
            byte[] classBytes = loadClassBytes(name);
            
            // Define class from bytecode
            return defineClass(name, classBytes, 0, classBytes.length);
        } catch (Exception e) {
            throw new ClassNotFoundException(name, e);
        }
    }
    
    private byte[] loadClassBytes(String className) {
        // Load bytecode from file, network, database, etc.
        // Return byte array of .class file
        return new byte[0]; // Placeholder
    }
}

// Usage
public class CustomClassLoaderDemo {
    public static void main(String[] args) throws Exception {
        CustomClassLoader loader = new CustomClassLoader();
        
        Class<?> clazz = loader.loadClass("com.example.MyClass");
        Object obj = clazz.newInstance();
    }
}
```

---

## 🎯 Memory Leaks in Java

### What is Memory Leak?

**Memory Leak** occurs when **unused objects are NOT garbage collected** because they still have references.

**Problem:**
- Heap memory gradually fills up
- Performance degrades
- Eventually → OutOfMemoryError

**Java has GC, but leaks still possible!**

---

### Common Causes of Memory Leaks

#### 1️⃣ Static Fields Holding References

```java
// ❌ Memory Leak: Static list holds references forever
public class LeakExample {
    private static List<Object> list = new ArrayList<>();
    
    public void addObject(Object obj) {
        list.add(obj);  // Objects never removed!
    }
}
```

**Why it leaks:**
- Static field lives for entire application lifetime
- Objects in list never eligible for GC
- Heap fills up over time

**Solution:**

```java
// ✅ Clear list when done
list.clear();

// Or use weak references
private static List<WeakReference<Object>> list = new ArrayList<>();
```

---

#### 2️⃣ Unclosed Resources

```java
// ❌ Memory Leak: Connection not closed
public void query() {
    Connection conn = DriverManager.getConnection(url);
    Statement stmt = conn.createStatement();
    ResultSet rs = stmt.executeQuery("SELECT * FROM users");
    // Forgot to close! Resources leak
}
```

**Solution:**

```java
// ✅ Use try-with-resources
public void query() {
    try (Connection conn = DriverManager.getConnection(url);
         Statement stmt = conn.createStatement();
         ResultSet rs = stmt.executeQuery("SELECT * FROM users")) {
        // Use resources
    } // Automatically closed
}
```

---

#### 3️⃣ HashMap with Improper hashCode()/equals()

```java
// ❌ Memory Leak: Objects accumulate in HashMap
class Employee {
    int id;
    String name;
    
    // equals() overridden, but NOT hashCode()!
    @Override
    public boolean equals(Object obj) {
        Employee e = (Employee) obj;
        return this.id == e.id;
    }
    // Missing hashCode()!
}

Map<Employee, String> map = new HashMap<>();
for (int i = 0; i < 1000000; i++) {
    Employee emp = new Employee(1, "John");  // All "equal" employees
    map.put(emp, "Data");  // All stored as different keys!
}
// Map grows huge because employees not properly identified as equal
```

**Solution:**

```java
// ✅ Override both equals() AND hashCode()
@Override
public int hashCode() {
    return Objects.hash(id);
}
```

---

#### 4️⃣ Inner Classes Holding Outer Class References

```java
// ❌ Memory Leak: Inner class holds reference to outer class
public class OuterClass {
    private byte[] data = new byte[1000000];  // 1 MB
    
    class InnerClass {
        public void doSomething() {
            // Has implicit reference to OuterClass
        }
    }
    
    public InnerClass getInner() {
        return new InnerClass();
    }
}

// Usage
List<OuterClass.InnerClass> list = new ArrayList<>();
for (int i = 0; i < 1000; i++) {
    OuterClass outer = new OuterClass();
    list.add(outer.getInner());  // Keeps outer class alive!
}
// 1000 OuterClass instances can't be GC'd (1000 MB leaked!)
```

**Solution:**

```java
// ✅ Use static nested class (no implicit reference)
public class OuterClass {
    private byte[] data = new byte[1000000];
    
    static class InnerClass {  // static = no reference to outer
        public void doSomething() {
            // No implicit reference to OuterClass
        }
    }
}
```

---

#### 5️⃣ Listeners Not Removed

```java
// ❌ Memory Leak: Listener not removed
public class EventSource {
    private List<Listener> listeners = new ArrayList<>();
    
    public void addListener(Listener listener) {
        listeners.add(listener);
    }
    
    // Missing: removeListener() method!
}

public class MyClass {
    public void setup(EventSource source) {
        source.addListener(new Listener() {
            // This anonymous inner class holds reference to MyClass!
        });
    }
}
// MyClass instances can't be GC'd even after they're no longer needed
```

**Solution:**

```java
// ✅ Provide remove method and use it
public void removeListener(Listener listener) {
    listeners.remove(listener);
}

// ✅ Or use WeakHashMap for listeners
private Map<Listener, Object> listeners = new WeakHashMap<>();
```

---

#### 6️⃣ ThreadLocal Not Cleaned

```java
// ❌ Memory Leak: ThreadLocal not removed
public class ThreadLocalLeak {
    private static ThreadLocal<byte[]> threadLocal = new ThreadLocal<>();
    
    public void process() {
        threadLocal.set(new byte[1000000]);  // 1 MB
        // Process...
        // Forgot to remove!
    }
}

// In thread pool, threads are reused
// ThreadLocal values accumulate for each thread
```

**Solution:**

```java
// ✅ Always remove ThreadLocal
public void process() {
    try {
        threadLocal.set(new byte[1000000]);
        // Process...
    } finally {
        threadLocal.remove();  // Clean up!
    }
}
```

---

#### 7️⃣ Cache Without Eviction

```java
// ❌ Memory Leak: Cache grows forever
public class CacheManager {
    private static Map<String, Object> cache = new HashMap<>();
    
    public void cache(String key, Object value) {
        cache.put(key, value);  // Never evicts old entries!
    }
}
```

**Solution:**

```java
// ✅ Use WeakHashMap or proper cache library
private static Map<String, Object> cache = new WeakHashMap<>();

// ✅ Or use Guava Cache with eviction policy
Cache<String, Object> cache = CacheBuilder.newBuilder()
    .maximumSize(1000)
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .build();
```

---

### Detecting Memory Leaks

#### 1️⃣ Heap Dump

```bash
# Generate heap dump
jmap -dump:live,format=b,file=heap.bin <pid>

# Or use flag on OutOfMemoryError
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/path/to/dump
```

#### 2️⃣ Memory Profiling Tools

- **VisualVM**: Free, included with JDK
- **JProfiler**: Commercial profiler
- **YourKit**: Commercial profiler
- **Eclipse MAT**: Memory Analyzer Tool (free)

#### 3️⃣ Monitoring

```bash
# Monitor heap usage
jstat -gc <pid> 1000

# Monitor class loading
jstat -class <pid>
```

---

### Preventing Memory Leaks

1. ✅ **Close resources** (use try-with-resources)
2. ✅ **Remove listeners** when done
3. ✅ **Clean ThreadLocal** in finally block
4. ✅ **Implement equals() AND hashCode()** together
5. ✅ **Use static nested classes** instead of inner classes (when possible)
6. ✅ **Clear collections** when done
7. ✅ **Use weak references** for caches
8. ✅ **Avoid static collections** (or manage carefully)
9. ✅ **Profile regularly** in development
10. ✅ **Monitor production** applications

---

## 🎯 JVM Tuning & Best Practices

### JVM Parameters Overview

```bash
# HEAP SIZE
-Xms512m          # Initial heap size (512 MB)
-Xmx2g            # Maximum heap size (2 GB)
-Xmn512m          # Young generation size (512 MB)

# STACK SIZE
-Xss1m            # Stack size per thread (1 MB)

# METASPACE (Java 8+)
-XX:MetaspaceSize=128m        # Initial metaspace
-XX:MaxMetaspaceSize=512m     # Maximum metaspace

# GC SELECTION
-XX:+UseG1GC                  # Use G1 GC (default Java 9+)
-XX:+UseParallelGC            # Use Parallel GC
-XX:+UseZGC                   # Use ZGC (Java 11+)

# GC TUNING
-XX:MaxGCPauseMillis=200      # Target pause time (ms)
-XX:GCTimeRatio=99            # Throughput goal

# GC LOGGING (Java 9+)
-Xlog:gc*:file=gc.log:time,level,tags

# HEAP DUMP ON OOM
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/path/to/dumps

# DEBUGGING
-XX:+PrintGCDetails           # Print GC details (Java 8)
-XX:+PrintGCDateStamps        # Print GC timestamps
-verbose:gc                   # Enable GC logging

# PERFORMANCE
-XX:+UseStringDeduplication   # Deduplicate strings (G1)
-XX:+UseCompressedOops        # Use compressed pointers (< 32 GB heap)
```

---

### Recommended Configurations

#### 🔹 Small Application (< 512 MB heap)

```bash
java -Xms256m -Xmx512m \
     -XX:+UseG1GC \
     -XX:MaxGCPauseMillis=200 \
     -XX:+HeapDumpOnOutOfMemoryError \
     -jar myapp.jar
```

---

#### 🔹 Medium Application (512 MB - 4 GB heap)

```bash
java -Xms2g -Xmx2g \
     -Xmn512m \
     -XX:+UseG1GC \
     -XX:MaxGCPauseMillis=200 \
     -XX:+UseStringDeduplication \
     -XX:+HeapDumpOnOutOfMemoryError \
     -XX:HeapDumpPath=/var/logs/heapdumps \
     -Xlog:gc*:file=/var/logs/gc.log:time,level,tags \
     -jar myapp.jar
```

---

#### 🔹 Large Application (> 4 GB heap)

```bash
java -Xms8g -Xmx8g \
     -Xmn2g \
     -XX:+UseG1GC \
     -XX:MaxGCPauseMillis=100 \
     -XX:+UseStringDeduplication \
     -XX:+UseCompressedOops \
     -XX:+HeapDumpOnOutOfMemoryError \
     -XX:HeapDumpPath=/var/logs/heapdumps \
     -Xlog:gc*:file=/var/logs/gc.log:time,level,tags \
     -jar myapp.jar
```

---

#### 🔹 Low-Latency Application

```bash
java -Xms4g -Xmx4g \
     -XX:+UseZGC \
     -XX:+HeapDumpOnOutOfMemoryError \
     -Xlog:gc*:file=/var/logs/gc.log:time,level,tags \
     -jar myapp.jar
```

---

#### 🔹 High-Throughput Batch Processing

```bash
java -Xms8g -Xmx8g \
     -XX:+UseParallelGC \
     -XX:GCTimeRatio=99 \
     -XX:+HeapDumpOnOutOfMemoryError \
     -Xlog:gc*:file=/var/logs/gc.log:time,level,tags \
     -jar myapp.jar
```

---

### Best Practices Summary

#### ✅ DO

1. **Set -Xms = -Xmx** (avoid resizing overhead)
2. **Choose appropriate GC** based on requirements
3. **Monitor GC behavior** (use logs/tools)
4. **Set heap size** based on actual usage (not too large!)
5. **Enable heap dump** on OutOfMemoryError
6. **Profile before tuning** (don't guess!)
7. **Test changes** in staging environment
8. **Use compressed oops** for heaps < 32 GB
9. **Close resources** properly (try-with-resources)
10. **Remove unused references** (nullify when done)

#### ❌ DON'T

1. **Don't call System.gc()** (let JVM decide)
2. **Don't use finalize()** (use try-with-resources)
3. **Don't over-tune** (default settings often good)
4. **Don't set heap too large** (causes long GC pauses)
5. **Don't ignore GC logs** (they provide insights)
6. **Don't leak resources** (connections, files, etc.)
7. **Don't use inner classes** unnecessarily (use static nested)
8. **Don't forget to clean ThreadLocal** values
9. **Don't mix GC algorithms** without understanding
10. **Don't tune in production** first (test in staging!)

---

## 🎯 Interview Questions

### Basic Questions

**Q1: What is JVM?**
- **A:** Java Virtual Machine - provides runtime environment to execute Java bytecode. Platform-dependent but makes Java platform-independent.

**Q2: Difference between JDK, JRE, and JVM?**
- **A:** JDK = JRE + Development Tools. JRE = JVM + Libraries. JVM = Execution Engine.

**Q3: What is bytecode?**
- **A:** Intermediate code generated by javac compiler from .java files. Platform-independent, executed by JVM. Uses stack-based instructions (bipush, iadd, istore, etc.).

**Q4: What is the difference between bytecode and machine code?**
- **A:** 
  - **Bytecode**: Platform-independent, intermediate code for JVM, stack-based, portable (.class files)
  - **Machine Code**: Platform-dependent, native code for CPU, register-based, not portable (binary)

**Q5: What is JIT Compiler?**
- **A:** Just-In-Time Compiler converts frequently executed bytecode (hot code) to native machine code at runtime for better performance. Combines portability of bytecode with speed of native code.

**Q6: Why does Java use bytecode instead of compiling directly to machine code?**
- **A:** For platform independence. Same bytecode runs on any OS with JVM. Each platform's JVM translates bytecode to its specific machine code, achieving "Write Once, Run Anywhere".

**Q7: What are the main memory areas in JVM?**
- **A:** Heap (objects), Stack (method calls), Method Area (class metadata), PC Register, Native Method Stack.

---

### Heap vs Stack Questions

**Q6: Difference between Heap and Stack?**

| Feature | Heap | Stack |
|---------|------|-------|
| **Stores** | Objects, arrays | Method calls, local variables |
| **Scope** | Global (all threads) | Thread-local |
| **Size** | Large (GBs) | Small (MBs) |
| **Speed** | Slower | Faster |
| **GC** | Yes | No (automatic) |
| **Error** | OutOfMemoryError | StackOverflowError |

**Q7: Where are primitives stored?**
- **A:** Depends on where declared:
  - **Local variable**: Stack
  - **Instance variable**: Heap (inside object)
  - **Static variable**: Method Area

**Q8: Where are wrapper objects stored?**
- **A:** Objects always in Heap. References depend on where declared (Stack/Heap/Method Area).

**Q9: Why Stack is faster than Heap?**
- **A:** Stack uses LIFO (simple), no GC needed, thread-local (no synchronization), contiguous memory allocation.

**Q10: When do we get StackOverflowError?**
- **A:** When stack size exceeded due to too many method calls (usually infinite recursion).

---

### Garbage Collection Questions

**Q11: What is Garbage Collection?**
- **A:** Automatic memory management that identifies and removes unused objects to reclaim memory.

**Q12: How does GC work?**
- **A:** Three phases: Mark (identify live objects), Sweep (remove dead objects), Compact (defragment memory).

**Q13: When is object eligible for GC?**
- **A:** When no references exist:
  1. Reference set to null
  2. Reference reassigned
  3. Method ends (local variable)
  4. Island of isolation
  5. Anonymous object

**Q14: Types of GC?**
- **A:** Minor GC (Young Gen), Major GC (Old Gen), Full GC (entire heap).

**Q15: What are GC algorithms?**
- **A:** Serial, Parallel, CMS, G1 (default Java 9+), ZGC, Shenandoah.

**Q16: What is G1 GC?**
- **A:** Garbage First collector. Region-based, predictable pause times, default since Java 9. Best for most applications.

**Q17: What is ZGC?**
- **A:** Ultra-low latency GC (< 10ms pauses), concurrent, handles large heaps (TBs). For real-time systems.

**Q18: Difference between Minor and Major GC?**

| Feature | Minor GC | Major GC |
|---------|----------|----------|
| **Target** | Young Generation | Old Generation |
| **Frequency** | Very frequent | Rare |
| **Speed** | Fast | Slow |
| **Pause** | Short (ms) | Long (can be seconds) |

**Q19: What is Stop-the-World?**
- **A:** GC pause when all application threads are stopped. Needed for safe garbage collection.

**Q20: Can we force GC?**
- **A:** Can request with System.gc(), but not guaranteed. JVM decides when to run GC. Never call in production!

**Q21: What is finalize() method? Should we use it?**
- **A:** Called before object is GC'd. **Deprecated in Java 9**. Don't use! Use try-with-resources or Cleaner API instead.

---

### Heap Generations Questions

**Q22: What are heap generations?**
- **A:** Young Generation (Eden + Survivor), Old Generation (Tenured).

**Q23: What is Eden Space?**
- **A:** Part of Young Gen where all new objects are created (8/10 of Young Gen).

**Q24: What are Survivor spaces?**
- **A:** S0 and S1 - hold objects that survived Minor GC. Objects alternate between S0 and S1.

**Q25: What is object promotion?**
- **A:** Moving object from Young Gen to Old Gen after surviving multiple GCs (default age threshold: 15).

**Q26: Why generational heap?**
- **A:** Most objects die young (short-lived). Separating generations makes GC more efficient - frequently collect Young Gen (small, fast) and rarely collect Old Gen.

---

### Method Area / Metaspace Questions

**Q27: What is Method Area?**
- **A:** Stores class metadata, static variables, constant pool, method bytecode. Shared among all threads.

**Q28: Difference between PermGen and Metaspace?**

| Feature | PermGen (Java 7) | Metaspace (Java 8+) |
|---------|------------------|---------------------|
| **Size** | Fixed | Dynamic (grows as needed) |
| **Location** | Part of heap | Native memory |
| **Error** | OutOfMemoryError: PermGen | Rare |

**Q29: What's stored in Method Area?**
- **A:** Class metadata, static variables, constant pool, method bytecode, static blocks.

---

### Memory Allocation Questions

**Q30: Where is `String str = "Hello";` stored?**
- **A:** Reference in Stack (if local variable), String object in Heap (String Pool).

**Q31: Where are static variables stored?**
- **A:** Method Area / Metaspace. (Object references in Method Area, objects themselves in Heap).

**Q32: What is Integer caching?**
- **A:** Java caches Integer objects from -128 to 127. `Integer a = 100; Integer b = 100;` → a == b is true (same cached object).

**Q33: Where are arrays stored?**
- **A:** Always in Heap (reference in Stack/Heap/Method Area depending on where declared).

---

### Memory Leak Questions

**Q34: Can memory leak occur in Java?**
- **A:** Yes! Even with GC. When unused objects still have references, they can't be GC'd.

**Q35: Common causes of memory leaks?**
- **A:**
  1. Static collections holding references
  2. Unclosed resources (connections, files)
  3. HashMap without proper hashCode()/equals()
  4. Inner classes holding outer class references
  5. Listeners not removed
  6. ThreadLocal not cleaned
  7. Caches without eviction

**Q36: How to detect memory leaks?**
- **A:** Heap dumps (jmap), profiling tools (VisualVM, JProfiler, Eclipse MAT), monitoring (jstat).

**Q37: How to prevent memory leaks?**
- **A:** Close resources, remove listeners, clean ThreadLocal, implement hashCode()/equals(), use static nested classes, use weak references for caches.

---

### Class Loading Questions

**Q38: What is class loading?**
- **A:** Process of loading .class files into memory and making them available for execution.

**Q39: What are class loaders?**
- **A:** Bootstrap (core classes), Extension (extensions), Application (classpath), Custom (user-defined).

**Q40: What is parent delegation model?**
- **A:** Class loader delegates to parent first. If parent can't load, child tries. Prevents duplicate loading and ensures security.

**Q41: What are class loading phases?**
- **A:** Loading (read .class), Linking (Verify, Prepare, Resolve), Initialization (static blocks, static variables).

**Q42: When is class initialized?**
- **A:** First time class is referenced: creating instance, accessing static member, loading child class.

**Q43: Order of static vs instance blocks?**
- **A:** Static blocks (class loading - once) → Instance blocks (object creation) → Constructors (object creation).

---

### Reference Types Questions

**Q44: What are reference types in Java?**
- **A:** Strong (default), Soft, Weak, Phantom.

**Q45: Difference between Soft and Weak references?**

| Feature | Soft | Weak |
|---------|------|------|
| **GC'd when** | Before OutOfMemoryError | Next GC cycle |
| **Use case** | Memory-sensitive cache | Canonical mappings |
| **Survival** | Survives multiple GCs | Until next GC |

**Q46: What is WeakHashMap?**
- **A:** Map where keys are weak references. Entry removed when key has no strong reference. Used for metadata, caches.

**Q47: What is Phantom reference?**
- **A:** Weakest reference. get() always returns null. Used for post-mortem cleanup, alternative to finalize().

---

### Performance Questions

**Q48: Primitive vs Wrapper performance?**
- **A:** Primitives ~10x faster (no object creation, no GC overhead, less memory).

**Q49: When to use primitives vs wrappers?**
- **A:** 
  - **Primitives**: Performance, local variables, calculations
  - **Wrappers**: Collections, null values, generics

**Q50: How to tune JVM for performance?**
- **A:**
  1. Choose appropriate GC
  2. Set appropriate heap size (-Xms, -Xmx)
  3. Monitor GC behavior
  4. Profile before tuning
  5. Test in staging
  6. Enable heap dumps on OOM

---

### Advanced Questions

**Q51: What is OutOfMemoryError? Types?**
- **A:** Thrown when JVM can't allocate memory.
  - **Java heap space**: Heap exhausted
  - **Metaspace**: Metaspace exhausted (too many classes)
  - **Unable to create new native thread**: Too many threads

**Q52: What is memory fragmentation?**
- **A:** When free memory is scattered in small pieces. Objects can't be allocated despite enough total free space. GC compaction solves this.

**Q53: What is Escape Analysis?**
- **A:** JVM optimization that analyzes if object escapes method scope. If not, can allocate on stack (faster) or eliminate allocation entirely.

**Q54: What is TLAB?**
- **A:** Thread-Local Allocation Buffer. Small portion of Eden allocated to each thread for faster object allocation (no synchronization).

**Q55: How does String Pool save memory?**
- **A:** String literals are interned (stored once). Multiple references to same literal point to same object. Saves memory for duplicate strings.

---

## 📚 Summary

### Key Takeaways

1. **JVM Architecture**: Class Loader + Runtime Data Areas + Execution Engine
2. **Memory Areas**: Heap (objects), Stack (method calls), Method Area (class data)
3. **Heap vs Stack**: Heap = large, shared, GC'd; Stack = small, thread-local, auto-managed
4. **Primitives vs Wrappers**: Location depends on where declared (local/instance/static)
5. **Garbage Collection**: Automatic memory management (Mark, Sweep, Compact)
6. **GC Algorithms**: Serial, Parallel, CMS, G1 (default), ZGC, Shenandoah
7. **Memory Leaks**: Can happen in Java! Close resources, remove listeners, clean ThreadLocal
8. **Class Loading**: Parent delegation model (Bootstrap → Extension → Application)
9. **Reference Types**: Strong (default), Soft (cache), Weak (metadata), Phantom (cleanup)
10. **Best Practices**: Close resources, monitor GC, profile first, don't call System.gc()

---

### Must Remember

- **Heap**: Objects live here, shared, GC'd
- **Stack**: Method calls, thread-local, auto-managed  
- **Method Area**: Class metadata, static variables
- **Young Gen**: Eden + S0 + S1 (short-lived objects)
- **Old Gen**: Tenured (long-lived objects)
- **Minor GC**: Young Gen (frequent, fast)
- **Major GC**: Old Gen (rare, slow)
- **G1 GC**: Default since Java 9, best for most apps
- **Memory Leak**: Unused objects with references
- **Class Loading**: Loading → Linking → Initialization

---

### Quick Reference

```
Memory Errors:
- OutOfMemoryError: Java heap space → Increase heap (-Xmx)
- StackOverflowError → Increase stack (-Xss) or fix recursion
- OutOfMemoryError: Metaspace → Increase metaspace (-XX:MaxMetaspaceSize)

JVM Flags:
-Xms512m                     # Initial heap
-Xmx2g                       # Max heap
-Xss1m                       # Stack size
-XX:+UseG1GC                 # Use G1 GC
-XX:MaxMetaspaceSize=512m    # Max metaspace

GC Selection:
- Small apps: Serial GC
- Batch processing: Parallel GC
- General purpose: G1 GC (default)
- Low latency: ZGC or Shenandoah

Monitoring:
jstat -gc <pid>              # GC statistics
jmap -dump:format=b,file=heap.bin <pid>  # Heap dump
jconsole                     # GUI monitoring
VisualVM                     # Profiling
```

---

## 📄 One-Page Cheatsheet

### 🔷 JVM Memory Areas (5 Areas)

```
┌─────────────────────────────────────────────────────────────┐
│ HEAP (Shared)          │ STACK (Per Thread)                 │
│ - All objects          │ - Method calls                     │
│ - Instance variables   │ - Local primitives                 │
│ - GC managed           │ - Object references                │
│ - Large (GBs)          │ - Small (MBs)                      │
│ - Slower               │ - Faster                           │
│ - OutOfMemoryError     │ - StackOverflowError               │
│ - Config: -Xms, -Xmx   │ - Config: -Xss                     │
├─────────────────────────────────────────────────────────────┤
│ METHOD AREA (Shared)   │ PC REGISTER (Per Thread)           │
│ - Class metadata       │ - Current instruction              │
│ - Static variables     │ NATIVE STACK (Per Thread)          │
│ - Constant pool        │ - Native methods                   │
│ - Method bytecode      │                                    │
└─────────────────────────────────────────────────────────────┘
```

---

### 🔷 Bytecode vs Machine Code (Critical!)

| Aspect | Bytecode | Machine Code |
|--------|----------|--------------|
| **For** | JVM (Virtual) | CPU (Physical) |
| **Portable?** | ✅ YES | ❌ NO |
| **Speed** | Slower (needs JIT) | Fastest |
| **Example** | `bipush 10; iadd` | `B8 0A 00 00 00` |
| **Architecture** | Stack-based | Register-based |
| **File** | .class | .exe, binary |

**Journey:** `Source Code → Bytecode (Portable) → Machine Code (Fast)`

**Why Two Steps?**
- Bytecode = Portability ("Write Once, Run Anywhere")
- Machine Code = Performance (CPU native execution)
- JIT Compiler = Best of both worlds! 🎯

---

### 🔷 Memory Location Rules (MEMORIZE!)

| Variable Type | Local (Method) | Instance (Field) | Static (Class) |
|--------------|----------------|------------------|----------------|
| **Primitive** | ✅ Stack | ✅ Heap (in object) | ✅ Method Area |
| **Wrapper** | Ref: Stack<br>Obj: Heap | Ref: Heap<br>Obj: Heap | Ref: Method Area<br>Obj: Heap |
| **Object** | Ref: Stack<br>Obj: Heap | Ref: Heap<br>Obj: Heap | Ref: Method Area<br>Obj: Heap |

**Rule:** Memory location depends on WHERE declared, not WHAT type!

### 🔷 Heap Generations

```
Young Generation (1/3)          Old Generation (2/3)
┌──────────────────────┐        ┌─────────────────────┐
│ Eden │ S0 │ S1       │   →    │ Tenured (Age ≥ 15)  │
│ 8/10 │1/10│1/10      │        │ Long-lived objects  │
└──────────────────────┘        └─────────────────────┘
  Minor GC (Frequent)              Major GC (Rare)
  Fast (ms)                        Slow (seconds)
```

**Object Lifecycle:** `New → Eden → S0 ↔ S1 → Old Gen → GC`

### 🔷 Garbage Collection - When Eligible?

1. ❌ **Nullify**: `obj = null`
2. ❌ **Reassign**: `obj1 = obj2` (obj1's object eligible)
3. ❌ **Method ends**: Local variable out of scope
4. ❌ **Island**: Circular references, no external refs
5. ❌ **Anonymous**: `new Object()` with no reference

**GC Phases:** Mark → Sweep → Compact

### 🔷 GC Algorithms (Quick Reference)

| Algorithm | Pause | Throughput | Use Case |
|-----------|-------|------------|----------|
| **Serial** | High | Low | Small apps (< 100 MB) |
| **Parallel** | High | High | Batch processing |
| **CMS** | Low | Medium | Legacy low-latency |
| **G1** ⭐ | Predictable | High | **Default** - Most apps |
| **ZGC** | Ultra-low | High | Real-time (< 10ms) |
| **Shenandoah** | Low | High | Low latency |

**Default:** G1 GC (Java 9+)

### 🔷 Reference Types

| Type | GC Behavior | Use Case |
|------|-------------|----------|
| **Strong** (default) | Never GC'd (while reachable) | Normal objects |
| **Soft** | GC'd before OutOfMemoryError | Memory-sensitive cache |
| **Weak** | GC'd in next GC cycle | Canonical mappings |
| **Phantom** | GC'd immediately, get()=null | Post-mortem cleanup |

### 🔷 Integer Caching

```java
Integer a = 127;  Integer b = 127;  // a == b ✅ (cached)
Integer x = 200;  Integer y = 200;  // x == y ❌ (different objects)
```

**Cached Ranges:**
- Integer, Long, Short: `-128 to 127`
- Byte: All values
- Character: `0 to 127`
- Boolean: `true` and `false`
- Float/Double: ❌ Not cached

### 🔷 Class Loading (3 Phases)

```
LOADING → LINKING (Verify → Prepare → Resolve) → INITIALIZATION
```

**Hierarchy:** Bootstrap → Extension → Application → Custom

**Parent Delegation:** Child delegates to parent first

**Execution Order:**
1. Static blocks (class loading - **once**)
2. Instance blocks (object creation)
3. Constructors (object creation)

### 🔷 Memory Leaks - Common Causes

1. ❌ **Static collections** holding references
2. ❌ **Unclosed resources** (connections, files)
3. ❌ **HashMap** without proper `hashCode()`/`equals()`
4. ❌ **Inner classes** holding outer class refs
5. ❌ **Listeners** not removed
6. ❌ **ThreadLocal** not cleaned
7. ❌ **Caches** without eviction

**Prevention:** Close resources, remove listeners, clean ThreadLocal, use try-with-resources

### 🔷 JVM Tuning - Essential Flags

```bash
# Heap Size
-Xms2g              # Initial heap (set = -Xmx)
-Xmx2g              # Maximum heap
-Xmn512m            # Young generation

# Stack & Metaspace
-Xss1m              # Stack per thread
-XX:MetaspaceSize=128m
-XX:MaxMetaspaceSize=512m

# GC Selection
-XX:+UseG1GC        # G1 (default Java 9+)
-XX:+UseZGC         # ZGC (low latency)
-XX:+UseParallelGC  # Parallel (throughput)

# GC Tuning
-XX:MaxGCPauseMillis=200  # Target pause

# Debugging
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/path/to/dumps
-Xlog:gc*:file=gc.log:time,level,tags
```

### 🔷 Quick Comparison Tables

#### Heap vs Stack

| Feature | Heap | Stack |
|---------|------|-------|
| Stores | Objects | Method calls, primitives |
| Scope | All threads | Per thread |
| Size | Large (GBs) | Small (MBs) |
| Speed | Slower | Faster |
| GC | Yes | No (auto) |
| Error | OutOfMemoryError | StackOverflowError |

#### PermGen vs Metaspace

| Feature | PermGen (≤ Java 7) | Metaspace (≥ Java 8) |
|---------|-------------------|----------------------|
| Size | Fixed (64 MB) | Dynamic (grows) |
| Location | Heap | Native memory |
| Error | OutOfMemoryError: PermGen | Rare |

#### Minor vs Major GC

| Feature | Minor GC | Major GC |
|---------|----------|----------|
| Target | Young Gen | Old Gen |
| Frequency | Very frequent | Rare |
| Speed | Fast (ms) | Slow (seconds) |
| Impact | Low | High |

### 🔷 Top 10 Must-Know Facts

1. **Heap** = Objects (shared, GC'd) | **Stack** = Methods (per thread, auto)
2. **Primitive location** = WHERE declared (local→Stack, instance→Heap, static→Method Area)
3. **Integer caching**: -128 to 127 (a==b true for cached values)
4. **Object lifecycle**: Eden → S0 → S1 → Old Gen (age 15)
5. **GC eligible**: No references (null, reassign, method end, island, anonymous)
6. **G1 GC** = Default (Java 9+), best for most apps
7. **Memory leak** = Unused objects with references (static collections, unclosed resources)
8. **finalize()** = ❌ Deprecated! Use try-with-resources
9. **Class loading**: Loading → Linking → Initialization (parent delegation)
10. **Metaspace** = Replaced PermGen (Java 8+), dynamic size, native memory

### 🔷 Critical Interview Answers

**Q: Where is `int x = 10;` stored if local?**
**A:** Stack (local primitive)

**Q: Where is `Integer x = 10;` stored if local?**
**A:** Reference in Stack, Object in Heap (cached)

**Q: Where is `int age;` stored if instance variable?**
**A:** Heap (inside object)

**Q: Why `Integer a=127; Integer b=127; a==b` is true?**
**A:** Cached (-128 to 127), same object

**Q: Can memory leak occur in Java?**
**A:** Yes! Unused objects with references can't be GC'd

**Q: What is Stop-the-World?**
**A:** GC pause when all app threads stop

**Q: Difference between Soft and Weak references?**
**A:** Soft = GC'd before OOM (cache), Weak = GC'd next cycle (metadata)

**Q: What's stored in Method Area?**
**A:** Class metadata, static variables, constant pool, bytecode

**Q: Order: static vs instance blocks?**
**A:** Static (class load, once) → Instance → Constructors (each object)

**Q: Best GC for most applications?**
**A:** G1 GC (default Java 9+)

### 🔷 Memory Formulas & Rules

```
Heap Size:
  - Set -Xms = -Xmx (avoid resizing)
  - Young Gen = 1/3 of Heap
  - Eden = 8/10 of Young
  - Each Survivor = 1/10 of Young
  - Old Gen = 2/3 of Heap

Promotion Threshold:
  - Default age = 15 GC cycles
  - Objects surviving 15 Minor GCs → Old Gen

Performance:
  - Primitive ~10x faster than Wrapper
  - Stack ~10x faster than Heap
  - G1 target pause = 200ms (default)
  - ZGC pause = < 10ms
```

### 🔷 Best Practices (Do's & Don'ts)

#### ✅ DO
- Set `-Xms = -Xmx` (avoid resizing)
- Use **G1 GC** for most apps (default)
- Close resources with **try-with-resources**
- Override **both** `equals()` AND `hashCode()`
- Use **static nested** classes (not inner)
- Clean **ThreadLocal** in finally
- Profile **before** tuning
- Enable heap dump on OOM

#### ❌ DON'T
- Call `System.gc()` (let JVM decide)
- Use `finalize()` (deprecated!)
- Set heap too large (long GC pauses)
- Ignore GC logs
- Leak resources (connections, files)
- Use inner classes unnecessarily
- Forget to remove listeners
- Over-tune (defaults are good)

### 🔷 Command Reference

```bash
# Monitor GC
jstat -gc <pid> 1000

# Heap dump
jmap -dump:live,format=b,file=heap.bin <pid>

# Monitor with GUI
jconsole
VisualVM

# Run with tuning
java -Xms2g -Xmx2g -XX:+UseG1GC -XX:+HeapDumpOnOutOfMemoryError MyApp
```

### 🔷 Error Reference

| Error | Cause | Solution |
|-------|-------|----------|
| **OutOfMemoryError: Java heap space** | Heap full | Increase -Xmx, fix leaks |
| **StackOverflowError** | Stack full (deep recursion) | Increase -Xss, fix recursion |
| **OutOfMemoryError: Metaspace** | Too many classes | Increase -XX:MaxMetaspaceSize |
| **OutOfMemoryError: Unable to create new native thread** | Too many threads | Reduce threads, increase OS limit |

---

**💡 Memorization Tip:** Focus on WHERE (location depends on where declared), WHEN (GC eligibility), and WHY (understand the reasoning). Practice drawing memory diagrams!

---

## 🎓 Practice Tasks

### Task 1: Memory Allocation Exercise
Write a program that demonstrates memory allocation for primitives, wrappers, and objects in different scopes (local, instance, static).

### Task 2: GC Behavior Analysis
Create a program that creates objects in a loop and monitors GC behavior using GC logs.

### Task 3: Memory Leak Simulation
Create a program that simulates a memory leak and fix it.

### Task 4: Class Loading Order
Write a program with parent and child classes that demonstrates class loading order (static blocks, instance blocks, constructors).

### Task 5: Reference Types
Implement a cache using SoftReference and compare it with strong references under memory pressure.

---

**End of Part 7: JVM Internals & Memory Management**
