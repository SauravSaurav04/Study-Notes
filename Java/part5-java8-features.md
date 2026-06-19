# Part 5: Java 8+ Features

**Focus:** Lambda Expressions, Functional Interfaces, Streams API, Optional, Method References, Default & Static Methods in Interfaces, Date/Time API

---

## 📋 Table of Contents
1. [Java 8+ Overview](#-java-8-overview)
2. [Lambda Expressions](#-lambda-expressions)
3. [Functional Interfaces](#-functional-interfaces)
4. [Streams API](#-streams-api)
5. [Optional Class](#-optional-class)
6. [Method References](#-method-references)
7. [Default Methods in Interfaces](#-default-methods-in-interfaces)
8. [Static Methods in Interfaces](#-static-methods-in-interfaces)
9. [Date/Time API](#-datetime-api-java-8)
10. [Interview Questions](#-interview-questions)
11. [Practice Coding Questions](#-practice-coding-questions)
12. [One-Page Cheatsheet](#-one-page-cheatsheet)

---

## 🎯 Java 8+ Overview

### What is Java 8?

**Java 8** (Released March 2014) was a **MAJOR RELEASE** that introduced **functional programming** features to Java.

**Why Java 8 is Important?**
- Biggest change since Java 5
- Introduced functional programming paradigm
- Made code more concise and readable
- Better support for parallel processing
- Modern API for date/time handling

---

### Major Features Introduced

| Feature | Purpose | Benefit |
|---------|---------|---------|
| **Lambda Expressions** | Anonymous functions | Less boilerplate code |
| **Functional Interfaces** | Single abstract method interfaces | Enable lambda expressions |
| **Stream API** | Process collections functionally | Declarative, parallel processing |
| **Optional Class** | Better null handling | Avoid NullPointerException |
| **Method References** | Shorthand for lambdas | More concise code |
| **Default Methods** | Methods in interfaces | Interface evolution |
| **Static Methods in Interface** | Utility methods | No separate utility class |
| **Date/Time API** | New java.time package | Better date/time handling |
| **forEach() method** | Iterate collections | Functional iteration |

---

### Why These Features?

**Before Java 8:**
```java
// Anonymous class - Verbose and hard to read
List<String> names = Arrays.asList("John", "Alice", "Bob");
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});
```

**After Java 8:**
```java
// Lambda - Concise and readable
List<String> names = Arrays.asList("John", "Alice", "Bob");
Collections.sort(names, (s1, s2) -> s1.compareTo(s2));

// Even better with method reference
names.sort(String::compareTo);
```

**Benefits:**
- ✅ **Less Code**: More concise
- ✅ **More Readable**: Clear intent
- ✅ **Functional Style**: Declarative, not imperative
- ✅ **Better Performance**: Easier parallelization

---

## 🎯 Lambda Expressions

### What is Lambda Expression?

**Lambda Expression** is an **anonymous function** (function without a name) that can be treated as data and passed around.

**Definition:** A concise way to represent **one method interface** using an expression.

---

### Syntax

```
(parameters) -> expression
OR
(parameters) -> { statements; }
```

**Components:**
```
(a, b) -> a + b
  ↓       ↓    ↓
 params  arrow body
```

1. **Parameter List**: `(a, b)` - Input parameters (can be empty)
2. **Arrow Token**: `->` - Separates parameters from body
3. **Body**: `a + b` - Expression or block of statements

---

### Rules for Lambda Expressions

#### 1️⃣ Parameter Rules

**Zero Parameters:**
```java
() -> System.out.println("Hello");
```

**One Parameter:**
```java
// Parentheses optional for single parameter
x -> x * x
(x) -> x * x  // Also valid
```

**Multiple Parameters:**
```java
// Parentheses required
(a, b) -> a + b
```

**Type Inference:**
```java
// Type optional (compiler infers)
(int x, int y) -> x + y  // Explicit type
(x, y) -> x + y          // Type inferred (preferred)
```

---

#### 2️⃣ Body Rules

**Single Expression:**
```java
// No braces, no return keyword
x -> x * x

// Automatically returns result
```

**Multiple Statements:**
```java
// Braces required, explicit return
(a, b) -> {
    int sum = a + b;
    System.out.println("Sum: " + sum);
    return sum;
}
```

---

### Before vs After Lambda

#### Example 1: Runnable

**Before Lambda (Anonymous Class):**
```java
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello World");
    }
};
new Thread(r1).start();
```

**After Lambda:**
```java
Runnable r2 = () -> System.out.println("Hello World");
new Thread(r2).start();

// Even more concise
new Thread(() -> System.out.println("Hello World")).start();
```

**Result:** 8 lines → 1 line! 🎉

---

#### Example 2: Comparator

**Before Lambda:**
```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);

Collections.sort(numbers, new Comparator<Integer>() {
    @Override
    public int compare(Integer a, Integer b) {
        return a - b;
    }
});
```

**After Lambda:**
```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);

Collections.sort(numbers, (a, b) -> a - b);

// Even better
numbers.sort((a, b) -> a - b);
```

---

### Lambda Examples

```java
import java.util.*;

public class LambdaExamples {
    public static void main(String[] args) {
        // 1. No parameters
        Runnable r = () -> System.out.println("Hello");
        r.run();
        
        // 2. Single parameter
        List<String> names = Arrays.asList("John", "Alice", "Bob");
        names.forEach(name -> System.out.println(name));
        
        // 3. Multiple parameters
        Comparator<Integer> comp = (a, b) -> a - b;
        
        // 4. With braces and return
        Comparator<String> strComp = (s1, s2) -> {
            System.out.println("Comparing " + s1 + " and " + s2);
            return s1.compareTo(s2);
        };
        
        // 5. Type declared
        Comparator<Integer> typedComp = (Integer a, Integer b) -> a.compareTo(b);
        
        // 6. Single statement returning value
        Function<Integer, Integer> square = x -> x * x;
        System.out.println(square.apply(5)); // 25
    }
}
```

---

### Benefits of Lambda Expressions

| Benefit | Description | Example |
|---------|-------------|---------|
| **Less Boilerplate** | No verbose anonymous classes | 8 lines → 1 line |
| **Functional Programming** | Treat code as data | Pass behavior as argument |
| **Better Readability** | Clear and concise | Intent is obvious |
| **Enables Stream API** | Essential for streams | `filter()`, `map()`, etc. |
| **Encourages Immutability** | Stateless operations | No side effects |
| **Easier Parallelization** | Parallel streams | Better performance |

---

### Closure in Lambda

**Closure:** Lambda can access variables from **outer scope**.

**Key Rule:** Variables must be **effectively final** (not modified after initialization).

```java
public class LambdaClosure {
    public static void main(String[] args) {
        int factor = 10;  // Effectively final
        
        // Lambda accessing outer variable
        Function<Integer, Integer> multiplier = x -> x * factor;
        
        System.out.println(multiplier.apply(5)); // 50
        
        // factor = 20;  // ❌ ERROR: Would make 'factor' not effectively final
    }
}
```

**Why Effectively Final?**
- Lambda might be executed later or in different thread
- Must guarantee variable value doesn't change
- Ensures thread safety and predictability

---

### 🔥 Interview Questions - Lambda

**Q1: What is a Lambda Expression?**

**Answer:** Lambda expression is an anonymous function that can be passed as argument or returned from method. It enables functional programming in Java and makes code more concise.

---

**Q2: What are the components of Lambda Expression?**

**Answer:** Three components:
1. **Parameter List**: `(a, b)` - Input parameters
2. **Arrow Token**: `->` - Separates parameters from body
3. **Body**: `a + b` - Expression or statements

---

**Q3: Can Lambda expression throw checked exceptions?**

**Answer:** Yes, but the functional interface method signature must declare the exception. If not, you need to handle it inside lambda.

```java
// ❌ ERROR if interface doesn't declare exception
Runnable r = () -> Thread.sleep(1000);

// ✅ CORRECT - handle exception
Runnable r = () -> {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
};
```

---

**Q4: What is effectively final?**

**Answer:** Variable that is not explicitly declared `final` but whose value never changes after initialization. Lambda can only access effectively final variables from outer scope.

---

**Q5: Can we use Lambda without Functional Interface?**

**Answer:** No. Lambda expressions can only be used to implement functional interfaces (interfaces with single abstract method).

---

## 🎯 Functional Interfaces

### What is Functional Interface?

**Functional Interface** = Interface with **EXACTLY ONE abstract method**.

**KEY RULE:** 
- **ONE abstract method** (REQUIRED)
- **Unlimited default methods** (ALLOWED)
- **Unlimited static methods** (ALLOWED)

---

### @FunctionalInterface Annotation

**Purpose:** Mark interface as functional interface.

**Benefits:**
1. ✅ **Compile-time Check**: Ensures only one abstract method
2. ✅ **Self-documenting**: Clear intent
3. ✅ **Prevents Mistakes**: Error if multiple abstract methods added

```java
@FunctionalInterface
interface Calculator {
    // ONLY ONE abstract method - REQUIRED
    int calculate(int a, int b);
    
    // Multiple default methods - ALLOWED
    default void printResult(int result) {
        System.out.println("Result: " + result);
    }
    
    default void printOperation(String op) {
        System.out.println("Operation: " + op);
    }
    
    // Multiple static methods - ALLOWED
    static void displayInfo() {
        System.out.println("Calculator Interface");
    }
}
```

---

### Valid vs Invalid Functional Interface

#### ✅ VALID Examples

```java
// 1. Single abstract method
@FunctionalInterface
interface Runnable {
    void run();
}

// 2. One abstract + multiple default methods
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
    
    default void print() { }
    default void display() { }
}

// 3. One abstract + multiple static methods
@FunctionalInterface
interface MathOperation {
    int operate(int a, int b);
    
    static void info() { }
    static void version() { }
}

// 4. Extending functional interface (no new abstract method)
@FunctionalInterface
interface ExtendedRunnable extends Runnable {
    // Inherits run() from Runnable
    
    default void start() {
        System.out.println("Starting...");
    }
}
```

---

#### ❌ INVALID Examples

```java
// ❌ TWO abstract methods
@FunctionalInterface  // COMPILATION ERROR!
interface InvalidCalculator {
    int calculate(int a, int b);  // First abstract method
    int compute(int x, int y);    // Second abstract method - NOT ALLOWED!
}

// ❌ NO abstract method
@FunctionalInterface  // COMPILATION ERROR!
interface InvalidEmpty {
    default void print() { }  // Only default method
    static void display() { } // Only static method
}

// ❌ Extending and adding new abstract method
@FunctionalInterface  // COMPILATION ERROR!
interface InvalidExtended extends Runnable {
    void run();        // From Runnable
    void execute();    // New abstract method - NOT ALLOWED!
}
```

---

### Built-in Functional Interfaces

Java provides **43+ built-in functional interfaces** in `java.util.function` package.

**Four Main Categories:**

---

#### 1️⃣ Predicate<T> - Test Condition

**Purpose:** Test condition, return boolean.

**Method Signature:**
```java
boolean test(T t)
```

**Use Cases:**
- Filtering
- Conditional logic
- Validation

**Example:**
```java
import java.util.function.Predicate;

public class PredicateDemo {
    public static void main(String[] args) {
        // Check if number is even
        Predicate<Integer> isEven = n -> n % 2 == 0;
        System.out.println(isEven.test(4));  // true
        System.out.println(isEven.test(5));  // false
        
        // Check if string is not empty
        Predicate<String> isNotEmpty = s -> !s.isEmpty();
        System.out.println(isNotEmpty.test("Hello"));  // true
        System.out.println(isNotEmpty.test(""));       // false
        
        // Combining predicates
        Predicate<Integer> isPositive = n -> n > 0;
        Predicate<Integer> isEvenAndPositive = isEven.and(isPositive);
        
        System.out.println(isEvenAndPositive.test(4));   // true
        System.out.println(isEvenAndPositive.test(-4));  // false
    }
}
```

**Predicate Methods:**
```java
and(Predicate)      // Logical AND
or(Predicate)       // Logical OR
negate()            // Logical NOT
isEqual(Object)     // Static method for equality
```

---

#### 2️⃣ Consumer<T> - Accept and Consume

**Purpose:** Accept input, perform operation, no return (side effect).

**Method Signature:**
```java
void accept(T t)
```

**Use Cases:**
- Printing
- Logging
- Updating objects
- forEach operation

**Example:**
```java
import java.util.function.Consumer;
import java.util.*;

public class ConsumerDemo {
    public static void main(String[] args) {
        // Print consumer
        Consumer<String> printer = s -> System.out.println(s);
        printer.accept("Hello");  // Hello
        
        // Uppercase printer
        Consumer<String> upperPrinter = s -> System.out.println(s.toUpperCase());
        upperPrinter.accept("hello");  // HELLO
        
        // Chain consumers
        Consumer<String> chain = printer.andThen(upperPrinter);
        chain.accept("Java");  
        // Output:
        // Java
        // JAVA
        
        // With list
        List<String> names = Arrays.asList("John", "Alice", "Bob");
        names.forEach(name -> System.out.println("Hello, " + name));
    }
}
```

**Consumer Methods:**
```java
andThen(Consumer)   // Chain consumers
```

---

#### 3️⃣ Supplier<T> - Supply Value

**Purpose:** Supply/produce value, no input.

**Method Signature:**
```java
T get()
```

**Use Cases:**
- Lazy initialization
- Factory methods
- Random value generation
- Default value provision

**Example:**
```java
import java.util.function.Supplier;
import java.util.*;

public class SupplierDemo {
    public static void main(String[] args) {
        // Random number supplier
        Supplier<Double> randomSupplier = () -> Math.random();
        System.out.println(randomSupplier.get());  // Random number
        
        // Date supplier
        Supplier<Date> dateSupplier = () -> new Date();
        System.out.println(dateSupplier.get());  // Current date
        
        // Lazy initialization
        Supplier<List<String>> listSupplier = () -> new ArrayList<>();
        List<String> list = listSupplier.get();
        
        // Default value supplier
        Supplier<String> defaultName = () -> "Unknown";
        String name = getName().orElseGet(defaultName);
    }
    
    static Optional<String> getName() {
        return Optional.empty();
    }
}
```

---

#### 4️⃣ Function<T, R> - Transform

**Purpose:** Transform input T to output R.

**Method Signature:**
```java
R apply(T t)
```

**Use Cases:**
- Mapping/Conversion
- Transformation
- Data processing

**Example:**
```java
import java.util.function.Function;
import java.util.*;
import java.util.stream.*;

public class FunctionDemo {
    public static void main(String[] args) {
        // String to length
        Function<String, Integer> length = s -> s.length();
        System.out.println(length.apply("Hello"));  // 5
        
        // String to uppercase
        Function<String, String> toUpper = s -> s.toUpperCase();
        System.out.println(toUpper.apply("hello"));  // HELLO
        
        // Integer to String
        Function<Integer, String> intToString = n -> "Number: " + n;
        System.out.println(intToString.apply(10));  // Number: 10
        
        // Chaining functions
        Function<String, String> addExclamation = s -> s + "!";
        Function<String, String> combined = toUpper.andThen(addExclamation);
        System.out.println(combined.apply("hello"));  // HELLO!
        
        // With streams
        List<String> names = Arrays.asList("john", "alice", "bob");
        List<String> upperNames = names.stream()
                                      .map(toUpper)
                                      .collect(Collectors.toList());
    }
}
```

**Function Methods:**
```java
andThen(Function)    // Apply this, then that
compose(Function)    // Apply that, then this
identity()           // Returns input unchanged (static)
```

---

### Other Important Functional Interfaces

#### 5️⃣ BiFunction<T, U, R> - Two Inputs, One Output

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(5, 3));  // 8

BiFunction<String, String, String> concat = (s1, s2) -> s1 + s2;
System.out.println(concat.apply("Hello", "World"));  // HelloWorld
```

---

#### 6️⃣ UnaryOperator<T> - T → T Transformation

```java
UnaryOperator<Integer> square = n -> n * n;
System.out.println(square.apply(5));  // 25

UnaryOperator<String> toUpper = s -> s.toUpperCase();
System.out.println(toUpper.apply("hello"));  // HELLO
```

---

#### 7️⃣ BinaryOperator<T> - Two T Inputs, One T Output

```java
BinaryOperator<Integer> multiply = (a, b) -> a * b;
System.out.println(multiply.apply(4, 5));  // 20

BinaryOperator<String> concat = (s1, s2) -> s1 + s2;
System.out.println(concat.apply("Hello", "World"));  // HelloWorld
```

---

### Complete Functional Interfaces Example

```java
import java.util.function.*;

public class FunctionalInterfaceDemo {
    public static void main(String[] args) {
        // Predicate<T> - takes T, returns boolean
        Predicate<Integer> isEven = num -> num % 2 == 0;
        System.out.println(isEven.test(4)); // true
        
        // Consumer<T> - takes T, returns void
        Consumer<String> print = str -> System.out.println(str);
        print.accept("Hello");
        
        // Supplier<T> - takes nothing, returns T
        Supplier<Double> random = () -> Math.random();
        System.out.println(random.get());
        
        // Function<T, R> - takes T, returns R
        Function<String, Integer> length = str -> str.length();
        System.out.println(length.apply("Hello")); // 5
        
        // BiFunction<T, U, R> - takes T and U, returns R
        BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
        System.out.println(add.apply(5, 3)); // 8
        
        // UnaryOperator<T> - takes T, returns T
        UnaryOperator<Integer> square = num -> num * num;
        System.out.println(square.apply(5)); // 25
        
        // BinaryOperator<T> - takes two T, returns T
        BinaryOperator<Integer> multiply = (a, b) -> a * b;
        System.out.println(multiply.apply(4, 5)); // 20
    }
}
```

---

### Summary of Built-in Functional Interfaces

| Interface | Parameters | Return | Method | Use Case |
|-----------|------------|--------|--------|----------|
| **Predicate<T>** | T | boolean | test(T) | Filtering, validation |
| **Consumer<T>** | T | void | accept(T) | Side effects, printing |
| **Supplier<T>** | None | T | get() | Factory, lazy init |
| **Function<T, R>** | T | R | apply(T) | Transformation |
| **BiPredicate<T, U>** | T, U | boolean | test(T, U) | Two-input condition |
| **BiConsumer<T, U>** | T, U | void | accept(T, U) | Two-input side effect |
| **BiFunction<T, U, R>** | T, U | R | apply(T, U) | Two-input transformation |
| **UnaryOperator<T>** | T | T | apply(T) | Same type transformation |
| **BinaryOperator<T>** | T, T | T | apply(T, T) | Reduce operations |

---

### Custom Functional Interface Example

```java
@FunctionalInterface
interface TriFunction<T, U, V, R> {
    // Single abstract method
    R apply(T t, U u, V v);
    
    // Multiple default methods - ALLOWED
    default void printInputs(T t, U u, V v) {
        System.out.println("Inputs: " + t + ", " + u + ", " + v);
    }
    
    default void printResult(R result) {
        System.out.println("Result: " + result);
    }
    
    // Multiple static methods - ALLOWED
    static void displayInfo() {
        System.out.println("TriFunction Interface");
    }
    
    static void displayVersion() {
        System.out.println("Version 1.0");
    }
}

public class CustomFunctionalInterfaceDemo {
    public static void main(String[] args) {
        // Lambda implements only the abstract method
        TriFunction<Integer, Integer, Integer, Integer> sum3 = 
            (a, b, c) -> a + b + c;
        
        int result = sum3.apply(10, 20, 30);
        System.out.println(result);  // 60
        
        // Using default methods
        sum3.printInputs(10, 20, 30);
        sum3.printResult(result);
        
        // Using static methods
        TriFunction.displayInfo();
        TriFunction.displayVersion();
    }
}
```

---

### 🔥 Interview Questions - Functional Interface

**Q1: What is a Functional Interface?**

**Answer:** Interface with **exactly one abstract method**. Can have multiple default and static methods. Marked with `@FunctionalInterface` annotation (optional but recommended).

---

**Q2: Can Functional Interface have multiple default methods?**

**Answer:** Yes! Functional interface can have **unlimited default and static methods**. Only restriction is it must have **exactly one abstract method**.

---

**Q3: What are the four main functional interfaces?**

**Answer:** 
1. **Predicate<T>**: `test(T) → boolean` - Condition testing
2. **Consumer<T>**: `accept(T) → void` - Side effects
3. **Supplier<T>**: `get() → T` - Value supply
4. **Function<T, R>**: `apply(T) → R` - Transformation

---

**Q4: Difference between Function and UnaryOperator?**

**Answer:**
- **Function<T, R>**: Transforms T to R (different types)
- **UnaryOperator<T>**: Transforms T to T (same type)
- UnaryOperator extends Function<T, T>

---

**Q5: Can we create our own Functional Interface?**

**Answer:** Yes! Create interface with one abstract method and mark with `@FunctionalInterface` annotation.

---

## 🎯 Streams API

### What is Stream?

**Stream** is a sequence of elements that supports **sequential and parallel aggregate operations**.

**KEY CONCEPT:** Stream is **NOT a data structure** - it's a **pipeline of operations** to process data.

---

### Stream vs Collection

| Feature | Collection | Stream |
|---------|------------|--------|
| **Purpose** | Store data | Process data |
| **Storage** | Stores elements | Doesn't store elements |
| **Iteration** | External (for loop) | Internal (automatic) |
| **Reusability** | Can reuse | One-time use only |
| **Modification** | Can modify | Cannot modify source |
| **Eagerness** | Eager (all data loaded) | Lazy (computed on demand) |
| **Parallelization** | Manual | Easy (parallelStream()) |

---

### Stream Pipeline

```
Source → Intermediate Operations → Terminal Operation
```

**Visual Representation:**
```
List<Integer> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
         ↓
    numbers.stream()                    ← Source
         ↓
    .filter(n -> n % 2 == 0)           ← Intermediate (Lazy)
         ↓
    .map(n -> n * n)                   ← Intermediate (Lazy)
         ↓
    .collect(Collectors.toList())      ← Terminal (Eager)
         ↓
    Result: [4, 16, 36, 64, 100]
```

---

### Creating Streams

#### 1️⃣ From Collection

```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();
```

---

#### 2️⃣ From Array

```java
String[] array = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(array);
```

---

#### 3️⃣ Using Stream.of()

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
```

---

#### 4️⃣ Using Stream.builder()

```java
Stream<String> stream = Stream.<String>builder()
                              .add("a")
                              .add("b")
                              .add("c")
                              .build();
```

---

#### 5️⃣ Infinite Streams

```java
// Generate
Stream<Double> random = Stream.generate(Math::random);

// Iterate
Stream<Integer> numbers = Stream.iterate(0, n -> n + 1);
```

---

#### 6️⃣ Range (IntStream, LongStream)

```java
IntStream range = IntStream.range(1, 10);      // 1 to 9
IntStream rangeClosed = IntStream.rangeClosed(1, 10); // 1 to 10
```

---

### Intermediate Operations (Lazy)

**Key Characteristic:** Return a **Stream**, execution is **lazy** (not executed until terminal operation).

---

#### 1️⃣ filter() - Select Elements

**Purpose:** Select elements matching condition.

**Signature:**
```java
Stream<T> filter(Predicate<T> predicate)
```

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Filter even numbers
List<Integer> evens = numbers.stream()
                            .filter(n -> n % 2 == 0)
                            .collect(Collectors.toList());
// Result: [2, 4, 6, 8, 10]

// Filter numbers > 5
List<Integer> greaterThan5 = numbers.stream()
                                   .filter(n -> n > 5)
                                   .collect(Collectors.toList());
// Result: [6, 7, 8, 9, 10]

// Multiple filters (chained)
List<Integer> evenAndGreaterThan5 = numbers.stream()
                                          .filter(n -> n % 2 == 0)
                                          .filter(n -> n > 5)
                                          .collect(Collectors.toList());
// Result: [6, 8, 10]
```

---

#### 2️⃣ map() - Transform Elements

**Purpose:** Transform each element using a function (one-to-one mapping).

**Signature:**
```java
<R> Stream<R> map(Function<T, R> mapper)
```

**Example:**
```java
List<String> names = Arrays.asList("john", "alice", "bob");

// Convert to uppercase
List<String> upperNames = names.stream()
                              .map(String::toUpperCase)
                              .collect(Collectors.toList());
// Result: [JOHN, ALICE, BOB]

// Get lengths
List<Integer> lengths = names.stream()
                            .map(String::length)
                            .collect(Collectors.toList());
// Result: [4, 5, 3]

// Square numbers
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> squares = numbers.stream()
                              .map(n -> n * n)
                              .collect(Collectors.toList());
// Result: [1, 4, 9, 16, 25]
```

---

#### 3️⃣ flatMap() - Flatten Nested Structures

**Purpose:** Transform and flatten (one-to-many mapping).

**Signature:**
```java
<R> Stream<R> flatMap(Function<T, Stream<R>> mapper)
```

**Visual Explanation:**
```
map():      [[1,2], [3,4]] → Stream<List<Integer>>
flatMap():  [[1,2], [3,4]] → Stream<Integer> = [1, 2, 3, 4]
```

**Example:**
```java
// Flatten list of lists
List<List<Integer>> nestedList = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5, 6)
);

List<Integer> flatList = nestedList.stream()
                                  .flatMap(List::stream)
                                  .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5, 6]

// Split strings and flatten
List<String> sentences = Arrays.asList("Hello World", "Java Stream");
List<String> words = sentences.stream()
                              .flatMap(s -> Arrays.stream(s.split(" ")))
                              .collect(Collectors.toList());
// Result: [Hello, World, Java, Stream]
```

---

#### 4️⃣ distinct() - Remove Duplicates

**Purpose:** Remove duplicate elements (uses `equals()`).

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 4, 5, 5);

List<Integer> unique = numbers.stream()
                             .distinct()
                             .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5]
```

---

#### 5️⃣ sorted() - Sort Elements

**Purpose:** Sort elements (natural order or custom comparator).

**Example:**
```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9, 3);

// Natural order (ascending)
List<Integer> sorted = numbers.stream()
                             .sorted()
                             .collect(Collectors.toList());
// Result: [1, 2, 3, 5, 8, 9]

// Descending order
List<Integer> sortedDesc = numbers.stream()
                                 .sorted(Comparator.reverseOrder())
                                 .collect(Collectors.toList());
// Result: [9, 8, 5, 3, 2, 1]

// Custom comparator
List<String> names = Arrays.asList("John", "Alice", "Bob");
List<String> sortedByLength = names.stream()
                                  .sorted(Comparator.comparing(String::length))
                                  .collect(Collectors.toList());
// Result: [Bob, John, Alice]
```

---

#### 6️⃣ limit() - Limit Size

**Purpose:** Truncate stream to n elements.

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> first5 = numbers.stream()
                             .limit(5)
                             .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5]

// Useful for infinite streams
Stream<Integer> infinite = Stream.iterate(1, n -> n + 1);
List<Integer> first10 = infinite.limit(10)
                               .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

---

#### 7️⃣ skip() - Skip Elements

**Purpose:** Skip first n elements.

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> after5 = numbers.stream()
                             .skip(5)
                             .collect(Collectors.toList());
// Result: [6, 7, 8, 9, 10]

// Pagination: Skip 10, take 5
List<Integer> page2 = numbers.stream()
                            .skip(5)
                            .limit(5)
                            .collect(Collectors.toList());
// Result: [6, 7, 8, 9, 10]
```

---

#### 8️⃣ peek() - Debug Stream

**Purpose:** Perform action on each element without changing stream (useful for debugging).

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

List<Integer> result = numbers.stream()
                             .peek(n -> System.out.println("Original: " + n))
                             .map(n -> n * 2)
                             .peek(n -> System.out.println("Doubled: " + n))
                             .collect(Collectors.toList());

// Output:
// Original: 1
// Doubled: 2
// Original: 2
// Doubled: 4
// ...
```

---

### Terminal Operations (Eager)

**Key Characteristic:** Trigger execution of pipeline, produce **result** (not Stream).

---

#### 1️⃣ forEach() - Iterate

**Purpose:** Perform action on each element.

**Example:**
```java
List<String> names = Arrays.asList("John", "Alice", "Bob");

names.stream()
     .forEach(name -> System.out.println(name));

// With method reference
names.stream()
     .forEach(System.out::println);
```

---

#### 2️⃣ collect() - Accumulate to Collection

**Purpose:** Accumulate elements into collection or other result.

**Example:**
```java
List<String> names = Arrays.asList("john", "alice", "bob");

// To List
List<String> upperList = names.stream()
                             .map(String::toUpperCase)
                             .collect(Collectors.toList());

// To Set
Set<String> upperSet = names.stream()
                           .map(String::toUpperCase)
                           .collect(Collectors.toSet());

// To Map
Map<String, Integer> nameLength = names.stream()
    .collect(Collectors.toMap(
        name -> name,           // Key
        name -> name.length()   // Value
    ));
// Result: {john=4, alice=5, bob=3}

// Joining strings
String joined = names.stream()
                    .collect(Collectors.joining(", "));
// Result: "john, alice, bob"

// Grouping by length
Map<Integer, List<String>> groupedByLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
// Result: {3=[bob], 4=[john], 5=[alice]}

// Partitioning (true/false groups)
Map<Boolean, List<Integer>> partitioned = Arrays.asList(1,2,3,4,5,6).stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
// Result: {false=[1,3,5], true=[2,4,6]}
```

---

#### 3️⃣ reduce() - Reduce to Single Value

**Purpose:** Combine elements into single result using binary operation.

**Signatures:**
```java
Optional<T> reduce(BinaryOperator<T> accumulator)
T reduce(T identity, BinaryOperator<T> accumulator)
<U> U reduce(U identity, BiFunction<U,T,U> accumulator, BinaryOperator<U> combiner)
```

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Sum
int sum = numbers.stream()
                .reduce(0, (a, b) -> a + b);
// Result: 15

// Using method reference
int sum2 = numbers.stream()
                 .reduce(0, Integer::sum);

// Product
int product = numbers.stream()
                    .reduce(1, (a, b) -> a * b);
// Result: 120

// Max
Optional<Integer> max = numbers.stream()
                              .reduce((a, b) -> a > b ? a : b);
// Result: Optional[5]

// String concatenation
List<String> words = Arrays.asList("Java", "Stream", "API");
String combined = words.stream()
                      .reduce("", (a, b) -> a + b);
// Result: "JavaStreamAPI"
```

**Visual Explanation of reduce():**
```
numbers = [1, 2, 3, 4, 5]
reduce(0, (a, b) -> a + b)

Step 1: 0 + 1 = 1
Step 2: 1 + 2 = 3
Step 3: 3 + 3 = 6
Step 4: 6 + 4 = 10
Step 5: 10 + 5 = 15

Result: 15
```

---

#### 4️⃣ count() - Count Elements

**Purpose:** Count number of elements.

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

long count = numbers.stream()
                   .filter(n -> n % 2 == 0)
                   .count();
// Result: 2 (numbers 2 and 4)
```

---

#### 5️⃣ anyMatch() / allMatch() / noneMatch()

**Purpose:** Check if elements match condition (short-circuiting).

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// anyMatch - at least one matches
boolean hasEven = numbers.stream()
                        .anyMatch(n -> n % 2 == 0);
// Result: true

// allMatch - all elements match
boolean allPositive = numbers.stream()
                            .allMatch(n -> n > 0);
// Result: true

// noneMatch - no element matches
boolean noneNegative = numbers.stream()
                             .noneMatch(n -> n < 0);
// Result: true
```

---

#### 6️⃣ findFirst() / findAny()

**Purpose:** Find first or any element (returns Optional).

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// findFirst
Optional<Integer> first = numbers.stream()
                                .filter(n -> n > 3)
                                .findFirst();
// Result: Optional[4]

// findAny (useful in parallel streams)
Optional<Integer> any = numbers.parallelStream()
                              .filter(n -> n > 3)
                              .findAny();
// Result: Optional[4] or Optional[5] (any match)
```

---

#### 7️⃣ min() / max()

**Purpose:** Find minimum or maximum element.

**Example:**
```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);

Optional<Integer> min = numbers.stream()
                              .min(Integer::compareTo);
// Result: Optional[1]

Optional<Integer> max = numbers.stream()
                              .max(Integer::compareTo);
// Result: Optional[9]

// With custom comparator
List<String> names = Arrays.asList("John", "Alice", "Bob");
Optional<String> longest = names.stream()
                               .max(Comparator.comparing(String::length));
// Result: Optional[Alice]
```

---

#### 8️⃣ toArray() - Convert to Array

**Purpose:** Convert stream to array.

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Object array
Object[] array1 = numbers.stream().toArray();

// Typed array
Integer[] array2 = numbers.stream().toArray(Integer[]::new);

String[] names = List.of("John", "Alice", "Bob")
                    .stream()
                    .toArray(String[]::new);
```

---

### Complete Stream Example

```java
import java.util.*;
import java.util.stream.*;

public class StreamCompleteExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // Example 1: Filter even numbers and square them
        List<Integer> result1 = numbers.stream()
                                      .filter(n -> n % 2 == 0)
                                      .map(n -> n * n)
                                      .collect(Collectors.toList());
        // Result: [4, 16, 36, 64, 100]
        
        // Example 2: Sum of all numbers
        int sum = numbers.stream()
                        .reduce(0, Integer::sum);
        // Result: 55
        
        // Example 3: Get first 5 odd numbers
        List<Integer> oddNumbers = numbers.stream()
                                         .filter(n -> n % 2 != 0)
                                         .limit(5)
                                         .collect(Collectors.toList());
        // Result: [1, 3, 5, 7, 9]
        
        // Example 4: Check if any number > 8
        boolean anyGreaterThan8 = numbers.stream()
                                        .anyMatch(n -> n > 8);
        // Result: true
        
        // Example 5: Get sorted list of squares of even numbers
        List<Integer> sortedSquares = numbers.stream()
                                            .filter(n -> n % 2 == 0)
                                            .map(n -> n * n)
                                            .sorted(Comparator.reverseOrder())
                                            .collect(Collectors.toList());
        // Result: [100, 64, 36, 16, 4]
        
        // Example 6: String operations
        List<String> names = Arrays.asList("john", "alice", "bob", "charlie");
        
        String result = names.stream()
                            .filter(name -> name.length() > 3)
                            .map(String::toUpperCase)
                            .sorted()
                            .collect(Collectors.joining(", "));
        // Result: "ALICE, CHARLIE, JOHN"
        
        // Example 7: Complex example - Group employees by department
        class Employee {
            String name;
            String dept;
            int salary;
            
            Employee(String name, String dept, int salary) {
                this.name = name;
                this.dept = dept;
                this.salary = salary;
            }
            
            String getName() { return name; }
            String getDept() { return dept; }
            int getSalary() { return salary; }
        }
        
        List<Employee> employees = Arrays.asList(
            new Employee("John", "IT", 50000),
            new Employee("Alice", "HR", 45000),
            new Employee("Bob", "IT", 55000),
            new Employee("Charlie", "HR", 48000)
        );
        
        // Group by department
        Map<String, List<Employee>> byDept = employees.stream()
            .collect(Collectors.groupingBy(Employee::getDept));
        
        // Average salary by department
        Map<String, Double> avgSalaryByDept = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDept,
                Collectors.averagingInt(Employee::getSalary)
            ));
        
        // Highest paid employee
        Optional<Employee> highestPaid = employees.stream()
            .max(Comparator.comparing(Employee::getSalary));
    }
}
```

---

### Parallel Streams

**Purpose:** Utilize multiple CPU cores for parallel processing.

**When to Use:**
- ✅ Large datasets
- ✅ Computationally expensive operations
- ✅ Independent operations (no shared state)

**When NOT to Use:**
- ❌ Small datasets (overhead > benefit)
- ❌ Operations with side effects
- ❌ Operations requiring order
- ❌ Shared mutable state

**Example:**
```java
List<Integer> numbers = IntStream.rangeClosed(1, 1000000)
                                .boxed()
                                .collect(Collectors.toList());

// Sequential stream
long startSeq = System.currentTimeMillis();
int sumSeq = numbers.stream()
                   .mapToInt(Integer::intValue)
                   .sum();
long endSeq = System.currentTimeMillis();
System.out.println("Sequential: " + (endSeq - startSeq) + "ms");

// Parallel stream
long startPar = System.currentTimeMillis();
int sumPar = numbers.parallelStream()
                   .mapToInt(Integer::intValue)
                   .sum();
long endPar = System.currentTimeMillis();
System.out.println("Parallel: " + (endPar - startPar) + "ms");
```

---

### 🔥 Interview Questions - Streams

**Q1: What is a Stream?**

**Answer:** Stream is a sequence of elements that supports sequential and parallel aggregate operations. It's NOT a data structure but a pipeline to process data. Streams are lazy (intermediate operations) and one-time use.

---

**Q2: Difference between Collection and Stream?**

**Answer:**
- **Collection**: Stores data, eager, reusable, external iteration
- **Stream**: Processes data, lazy, one-time use, internal iteration

---

**Q3: What are intermediate and terminal operations?**

**Answer:**
- **Intermediate**: Return Stream, lazy execution (filter, map, sorted)
- **Terminal**: Trigger execution, produce result (collect, forEach, reduce)

---

**Q4: What is lazy evaluation in streams?**

**Answer:** Intermediate operations are not executed until a terminal operation is invoked. This allows optimization by avoiding unnecessary computations.

---

**Q5: Difference between map() and flatMap()?**

**Answer:**
- **map()**: One-to-one transformation (T → R)
- **flatMap()**: One-to-many transformation and flattening (T → Stream<R>)

Example:
```java
// map: [[1,2], [3,4]] → Stream<List<Integer>>
// flatMap: [[1,2], [3,4]] → [1, 2, 3, 4]
```

---

**Q6: How to convert Stream to List?**

**Answer:**
```java
List<Integer> list = stream.collect(Collectors.toList());
```

---

**Q7: Difference between findFirst() and findAny()?**

**Answer:**
- **findFirst()**: Returns first element (deterministic)
- **findAny()**: Returns any element (non-deterministic, useful in parallel)

---

**Q8: When to use parallel streams?**

**Answer:** Use when:
- Large dataset
- Computationally expensive operations
- Independent operations (no shared state)
- Don't care about order

---

## 🎯 Optional Class

### What is Optional?

**Optional** is a **container object** that may or may not contain a value. It's a better approach to handling `null` values.

**Purpose:** Avoid `NullPointerException` and make absence of value explicit.

---

### Why Optional?

**Problem with null:**
```java
String name = getName();  // Can return null
int length = name.length();  // NullPointerException!
```

**Solution with Optional:**
```java
Optional<String> optName = getName();  // Can be empty
int length = optName.orElse("Unknown").length();  // Safe!
```

---

### Creating Optional

#### 1️⃣ Optional.of(value) - Non-null Value

```java
Optional<String> opt1 = Optional.of("Hello");

// Throws NullPointerException if value is null
// Optional<String> opt2 = Optional.of(null);  // ❌ ERROR!
```

---

#### 2️⃣ Optional.ofNullable(value) - Nullable Value

```java
String value = null;
Optional<String> opt = Optional.ofNullable(value);  // ✅ OK (empty Optional)

String value2 = "Hello";
Optional<String> opt2 = Optional.ofNullable(value2);  // ✅ OK (value present)
```

**Use This:** Safest way to create Optional.

---

#### 3️⃣ Optional.empty() - Empty Optional

```java
Optional<String> emptyOpt = Optional.empty();
```

---

### Checking if Value Present

#### isPresent() - Check Presence

```java
Optional<String> opt = Optional.of("Hello");

if (opt.isPresent()) {
    System.out.println(opt.get());
}
```

**❌ NOT RECOMMENDED:** Defeats purpose of Optional.

---

#### isEmpty() - Check Absence (Java 11+)

```java
Optional<String> opt = Optional.empty();

if (opt.isEmpty()) {
    System.out.println("No value");
}
```

---

### Getting Value from Optional

#### 1️⃣ get() - Direct Access (AVOID!)

```java
Optional<String> opt = Optional.of("Hello");
String value = opt.get();  // Returns "Hello"

Optional<String> empty = Optional.empty();
// String value2 = empty.get();  // ❌ NoSuchElementException!
```

**❌ AVOID:** Throws exception if empty. Defeats purpose of Optional.

---

#### 2️⃣ orElse(defaultValue) - Provide Default

```java
Optional<String> opt1 = Optional.of("Hello");
String value1 = opt1.orElse("Default");  // Returns "Hello"

Optional<String> opt2 = Optional.empty();
String value2 = opt2.orElse("Default");  // Returns "Default"
```

**Note:** Default value is **always evaluated** (even if value present).

---

#### 3️⃣ orElseGet(Supplier) - Lazy Default

```java
Optional<String> opt = Optional.empty();
String value = opt.orElseGet(() -> "Expensive Computation");

// Supplier called only if empty (LAZY)
```

**✅ PREFERRED** over `orElse()` for expensive operations.

---

#### 4️⃣ orElseThrow() - Throw Exception

```java
Optional<String> opt = Optional.empty();

// Default exception
// String value = opt.orElseThrow();  // NoSuchElementException

// Custom exception
String value = opt.orElseThrow(() -> 
    new IllegalArgumentException("Value not present"));
```

---

### Transforming Optional

#### 1️⃣ map() - Transform Value

**Purpose:** Transform value if present, return empty if absent.

```java
Optional<String> name = Optional.of("john");

Optional<String> upperName = name.map(String::toUpperCase);
// Result: Optional["JOHN"]

Optional<Integer> length = name.map(String::length);
// Result: Optional[4]

// If empty
Optional<String> empty = Optional.empty();
Optional<Integer> result = empty.map(String::length);
// Result: Optional.empty
```

---

#### 2️⃣ flatMap() - Transform to Optional

**Purpose:** Transform value to another Optional (avoids Optional<Optional<T>>).

```java
class Person {
    Optional<String> getName() {
        return Optional.of("John");
    }
}

Optional<Person> person = Optional.of(new Person());

// Using map - returns Optional<Optional<String>>
Optional<Optional<String>> name1 = person.map(Person::getName);

// Using flatMap - returns Optional<String>
Optional<String> name2 = person.flatMap(Person::getName);
```

---

#### 3️⃣ filter() - Conditional Filtering

**Purpose:** Keep value if matches predicate, otherwise empty.

```java
Optional<Integer> number = Optional.of(25);

Optional<Integer> filtered = number.filter(n -> n > 20);
// Result: Optional[25]

Optional<Integer> filtered2 = number.filter(n -> n > 30);
// Result: Optional.empty

// Chaining
Optional<String> name = Optional.of("John");
Optional<String> longName = name.filter(n -> n.length() > 5);
// Result: Optional.empty
```

---

### Consuming Optional

#### 1️⃣ ifPresent(Consumer) - Execute if Present

```java
Optional<String> name = Optional.of("John");

name.ifPresent(n -> System.out.println("Hello, " + n));
// Output: Hello, John

Optional<String> empty = Optional.empty();
empty.ifPresent(n -> System.out.println("Hello, " + n));
// No output
```

---

#### 2️⃣ ifPresentOrElse(Consumer, Runnable) - Both Branches (Java 9+)

```java
Optional<String> name = Optional.of("John");

name.ifPresentOrElse(
    n -> System.out.println("Hello, " + n),
    () -> System.out.println("No name")
);
// Output: Hello, John

Optional<String> empty = Optional.empty();
empty.ifPresentOrElse(
    n -> System.out.println("Hello, " + n),
    () -> System.out.println("No name")
);
// Output: No name
```

---

### Complete Optional Example

```java
import java.util.*;

public class OptionalCompleteExample {
    public static void main(String[] args) {
        // Creating Optional
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.ofNullable(null);
        Optional<String> opt3 = Optional.empty();
        
        // Getting value
        String value1 = opt1.orElse("Default");  // "Hello"
        String value2 = opt2.orElse("Default");  // "Default"
        String value3 = opt2.orElseGet(() -> "Lazy Default");  // "Lazy Default"
        
        // Transforming
        Optional<Integer> length = opt1.map(String::length);  // Optional[5]
        Optional<String> upper = opt1.map(String::toUpperCase);  // Optional["HELLO"]
        
        // Filtering
        Optional<String> filtered = opt1.filter(s -> s.length() > 3);  // Optional["Hello"]
        
        // Consuming
        opt1.ifPresent(System.out::println);  // Hello
        
        // Chaining
        String result = getUserName()
            .filter(name -> name.length() > 3)
            .map(String::toUpperCase)
            .orElse("UNKNOWN");
    }
    
    static Optional<String> getUserName() {
        return Optional.of("John");
    }
}
```

---

### Common Mistakes with Optional

#### ❌ Mistake 1: Using get() Without Checking

```java
// BAD
Optional<String> opt = getOptional();
String value = opt.get();  // Can throw exception!

// GOOD
String value = opt.orElse("default");
```

---

#### ❌ Mistake 2: Using isPresent() + get()

```java
// BAD
if (opt.isPresent()) {
    return opt.get();
} else {
    return "default";
}

// GOOD
return opt.orElse("default");
```

---

#### ❌ Mistake 3: Using Optional as Field

```java
// BAD
public class User {
    private Optional<String> name;  // ❌ Don't use Optional as field!
}

// GOOD
public class User {
    private String name;  // Can be null
    
    public Optional<String> getName() {
        return Optional.ofNullable(name);
    }
}
```

---

#### ❌ Mistake 4: Using Optional for Collections

```java
// BAD
Optional<List<String>> names = Optional.of(new ArrayList<>());

// GOOD
List<String> names = new ArrayList<>();  // Return empty list, not Optional
```

---

### Best Practices

✅ **DO:**
1. Return Optional from methods that may not have value
2. Use `orElse()` or `orElseGet()` instead of `isPresent()` + `get()`
3. Use `map()` and `flatMap()` for transformations
4. Use `orElseThrow()` to make absence explicit

❌ **DON'T:**
1. Use Optional as method parameter
2. Use Optional as class field
3. Use Optional for collections (return empty collection)
4. Call `get()` without checking

---

### 🔥 Interview Questions - Optional

**Q1: What is Optional?**

**Answer:** Optional is a container object that may or may not contain a value. It's used to represent absence of value explicitly and avoid NullPointerException.

---

**Q2: Difference between orElse() and orElseGet()?**

**Answer:**
- **orElse(value)**: Value always evaluated (even if Optional has value)
- **orElseGet(Supplier)**: Supplier called only if Optional is empty (LAZY)

Use `orElseGet()` for expensive operations.

---

**Q3: When should you use Optional?**

**Answer:** Use Optional as **return type** of methods that may not have value. Don't use as:
- Method parameters
- Class fields
- Collection types

---

**Q4: Difference between map() and flatMap() in Optional?**

**Answer:**
- **map()**: Transforms value, returns Optional<R>
- **flatMap()**: Transforms value to Optional, avoids Optional<Optional<R>>

---

## 🎯 Method References

### What is Method Reference?

**Method Reference** is a **shorthand notation** for calling a method using lambda expression.

**Purpose:** Make code more concise and readable when lambda just calls an existing method.

---

### Syntax

```
ClassName::methodName
```

**Lambda vs Method Reference:**
```java
// Lambda
names.forEach(name -> System.out.println(name));

// Method Reference
names.forEach(System.out::println);
```

---

### Types of Method References

---

#### 1️⃣ Static Method Reference

**Syntax:** `ClassName::staticMethod`

**Lambda Equivalent:** `(args) -> ClassName.staticMethod(args)`

**Example:**
```java
import java.util.*;

public class StaticMethodReference {
    public static void main(String[] args) {
        List<String> numbers = Arrays.asList("1", "2", "3", "4", "5");
        
        // Lambda
        List<Integer> list1 = numbers.stream()
                                    .map(s -> Integer.parseInt(s))
                                    .collect(Collectors.toList());
        
        // Method Reference
        List<Integer> list2 = numbers.stream()
                                    .map(Integer::parseInt)
                                    .collect(Collectors.toList());
        
        // More examples
        List<Double> doubles = Arrays.asList("1.5", "2.5").stream()
                                    .map(Double::parseDouble)
                                    .collect(Collectors.toList());
    }
}
```

---

#### 2️⃣ Instance Method on Particular Object

**Syntax:** `object::instanceMethod`

**Lambda Equivalent:** `(args) -> object.instanceMethod(args)`

**Example:**
```java
import java.util.*;

public class InstanceMethodReference {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Alice", "Bob");
        
        // Lambda
        names.forEach(name -> System.out.println(name));
        
        // Method Reference
        names.forEach(System.out::println);
        
        // Another example
        String prefix = "Hello, ";
        
        // Lambda
        names.forEach(name -> System.out.println(prefix + name));
        
        // Cannot use method reference here (additional logic)
    }
}
```

---

#### 3️⃣ Instance Method on Arbitrary Object of Class

**Syntax:** `ClassName::instanceMethod`

**Lambda Equivalent:** `(obj, args) -> obj.instanceMethod(args)`

**Key:** First parameter becomes the target object.

**Example:**
```java
import java.util.*;
import java.util.stream.*;

public class InstanceMethodOnClass {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("john", "alice", "bob");
        
        // Lambda
        List<String> upper1 = names.stream()
                                  .map(s -> s.toUpperCase())
                                  .collect(Collectors.toList());
        
        // Method Reference
        List<String> upper2 = names.stream()
                                  .map(String::toUpperCase)
                                  .collect(Collectors.toList());
        
        // More examples
        List<Integer> lengths = names.stream()
                                    .map(String::length)
                                    .collect(Collectors.toList());
        
        // Sorting
        names.sort(String::compareTo);
        names.sort(String::compareToIgnoreCase);
    }
}
```

---

#### 4️⃣ Constructor Reference

**Syntax:** `ClassName::new`

**Lambda Equivalent:** `(args) -> new ClassName(args)`

**Example:**
```java
import java.util.*;
import java.util.stream.*;

public class ConstructorReference {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Alice", "Bob");
        
        // Lambda
        List<String> list1 = names.stream()
                                 .collect(Collectors.toCollection(() -> new ArrayList<>()));
        
        // Constructor Reference
        List<String> list2 = names.stream()
                                 .collect(Collectors.toCollection(ArrayList::new));
        
        // Array constructor
        String[] array = names.stream()
                             .toArray(String[]::new);
        
        // Custom class
        class Person {
            String name;
            Person(String name) { this.name = name; }
        }
        
        List<Person> persons = names.stream()
                                   .map(Person::new)
                                   .collect(Collectors.toList());
    }
}
```

---

### Method Reference Examples

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.*;

public class MethodReferenceExamples {
    public static void main(String[] args) {
        // 1. Static method reference
        Function<String, Integer> parseInt = Integer::parseInt;
        System.out.println(parseInt.apply("123"));  // 123
        
        // 2. Instance method on object
        String str = "Hello";
        Supplier<Integer> lengthSupplier = str::length;
        System.out.println(lengthSupplier.get());  // 5
        
        // 3. Instance method on class
        Function<String, String> toUpper = String::toUpperCase;
        System.out.println(toUpper.apply("hello"));  // HELLO
        
        // 4. Constructor reference
        Supplier<List<String>> listSupplier = ArrayList::new;
        List<String> list = listSupplier.get();
        
        // With streams
        List<String> names = Arrays.asList("john", "alice", "bob");
        
        names.stream()
             .map(String::toUpperCase)
             .forEach(System.out::println);
        
        // Comparator
        names.sort(String::compareToIgnoreCase);
        
        // Method reference with multiple parameters
        BiFunction<String, String, String> concat = String::concat;
        System.out.println(concat.apply("Hello", "World"));  // HelloWorld
    }
}
```

---

### When to Use Method Reference vs Lambda

**Use Method Reference When:**
✅ Lambda just calls existing method
✅ No additional logic needed
✅ Improves readability

**Example:**
```java
// Method Reference - Clear and concise
names.forEach(System.out::println);
```

---

**Use Lambda When:**
✅ Additional logic needed
✅ Multiple statements
✅ Complex operations

**Example:**
```java
// Lambda - Additional logic
names.forEach(name -> {
    System.out.println("Processing: " + name);
    process(name);
});
```

---

### 🔥 Interview Questions - Method References

**Q1: What is Method Reference?**

**Answer:** Method reference is a shorthand notation for lambda expression that just calls an existing method. Syntax: `ClassName::methodName`

---

**Q2: Types of Method References?**

**Answer:** Four types:
1. **Static method**: `ClassName::staticMethod`
2. **Instance method on object**: `object::instanceMethod`
3. **Instance method on class**: `ClassName::instanceMethod`
4. **Constructor**: `ClassName::new`

---

**Q3: Difference between `String::toUpperCase` and `s -> s.toUpperCase()`?**

**Answer:** They are equivalent. First is method reference (concise), second is lambda. Method reference is preferred when lambda just calls a method.

---

## 🎯 Default Methods in Interfaces

### What are Default Methods?

**Default Method** = Method with **implementation** in interface (Java 8+).

**Keyword:** `default`

**Syntax:**
```java
interface MyInterface {
    // Abstract method
    void abstractMethod();
    
    // Default method
    default void defaultMethod() {
        System.out.println("Default implementation");
    }
}
```

---

### Why Default Methods?

**Problem Before Java 8:**
```java
interface Vehicle {
    void start();
}

class Car implements Vehicle {
    public void start() { /* implementation */ }
}

// Adding new method breaks existing implementations!
interface Vehicle {
    void start();
    void stop();  // ❌ Breaks Car class!
}
```

**Solution with Default Methods:**
```java
interface Vehicle {
    void start();
    
    // Add new method with default implementation
    default void stop() {
        System.out.println("Vehicle stopped");
    }
}

// Car class still works! No changes needed
```

---

### Key Points

1. ✅ **Backward Compatibility**: Add methods without breaking implementations
2. ✅ **Optional Methods**: Provide default behavior
3. ✅ **Can be Overridden**: Implementing class can override
4. ✅ **Use `default` keyword**: Must have implementation
5. ✅ **Can use `this`**: Access interface members

---

### Example

```java
interface Vehicle {
    // Abstract method
    void start();
    
    // Default method
    default void stop() {
        System.out.println("Vehicle stopped");
    }
    
    // Another default method
    default void displayInfo() {
        System.out.println("This is a vehicle");
    }
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
    
    // Can override default method
    @Override
    public void stop() {
        System.out.println("Car stopped with brakes");
    }
    
    // No need to override displayInfo() - uses default
}

public class DefaultMethodExample {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();        // Car started
        car.stop();         // Car stopped with brakes
        car.displayInfo();  // This is a vehicle
    }
}
```

---

### Diamond Problem with Default Methods

**Problem:** Class implements two interfaces with same default method.

```java
interface Interface1 {
    default void display() {
        System.out.println("Interface1");
    }
}

interface Interface2 {
    default void display() {
        System.out.println("Interface2");
    }
}

// ❌ COMPILATION ERROR!
class MyClass implements Interface1, Interface2 {
    // Which display() to use?
}
```

**Solution:** Override the method in class.

```java
class MyClass implements Interface1, Interface2 {
    @Override
    public void display() {
        // Option 1: Custom implementation
        System.out.println("MyClass");
        
        // Option 2: Call specific interface method
        Interface1.super.display();
        
        // Option 3: Call both
        Interface1.super.display();
        Interface2.super.display();
    }
}
```

---

### Real-World Example

```java
interface List {
    // Many methods...
    
    // Added in Java 8
    default void sort(Comparator c) {
        Collections.sort(this, c);
    }
    
    default void forEach(Consumer action) {
        for (Object element : this) {
            action.accept(element);
        }
    }
}

// All existing List implementations automatically get these methods!
List<String> names = new ArrayList<>();
names.forEach(System.out::println);  // Works!
```

---

## 🎯 Static Methods in Interfaces

### What are Static Methods in Interfaces?

**Static Method** = Method that belongs to **interface**, not instance (Java 8+).

**Key Points:**
1. ✅ **Called using Interface name**: `Interface.method()`
2. ✅ **Cannot be Overridden**: Not inherited
3. ✅ **Utility Methods**: Helper methods related to interface
4. ✅ **Cannot use `this`**: No instance context

---

### Syntax

```java
interface MathOperation {
    int operate(int a, int b);
    
    // Static method
    static int add(int a, int b) {
        return a + b;
    }
    
    static int multiply(int a, int b) {
        return a * b;
    }
}

// Usage
int sum = MathOperation.add(5, 3);
```

---

### Example

```java
interface Vehicle {
    void start();
    
    // Static utility method
    static void displayGeneralInfo() {
        System.out.println("Vehicles are means of transportation");
    }
    
    static int getWheelCount(String vehicleType) {
        switch (vehicleType) {
            case "Car": return 4;
            case "Bike": return 2;
            case "Truck": return 6;
            default: return 0;
        }
    }
}

class Car implements Vehicle {
    public void start() {
        System.out.println("Car started");
    }
}

public class StaticMethodExample {
    public static void main(String[] args) {
        // Call static method using interface name
        Vehicle.displayGeneralInfo();
        
        int wheels = Vehicle.getWheelCount("Car");
        System.out.println("Car has " + wheels + " wheels");
        
        Car car = new Car();
        // car.displayGeneralInfo();  // ❌ ERROR! Cannot call through instance
    }
}
```

---

### Default vs Static Methods

| Feature | Default Method | Static Method |
|---------|---------------|---------------|
| **Keyword** | `default` | `static` |
| **Inheritance** | Inherited by implementations | NOT inherited |
| **Override** | Can be overridden | Cannot be overridden |
| **Access** | Through instance | Through interface name |
| **`this` keyword** | Can use `this` | Cannot use `this` |
| **Purpose** | Default behavior | Utility methods |

---

### Real-World Example - Comparator

```java
interface Comparator<T> {
    int compare(T o1, T o2);
    
    // Static factory methods (Java 8+)
    static <T extends Comparable<T>> Comparator<T> naturalOrder() {
        return (c1, c2) -> c1.compareTo(c2);
    }
    
    static <T extends Comparable<T>> Comparator<T> reverseOrder() {
        return (c1, c2) -> c2.compareTo(c1);
    }
    
    // Usage
    List<String> names = Arrays.asList("John", "Alice", "Bob");
    names.sort(Comparator.naturalOrder());
}
```

---

### 🔥 Interview Questions - Default & Static Methods

**Q1: What are Default Methods?**

**Answer:** Methods with implementation in interface (Java 8+). Use `default` keyword. Can be overridden by implementing class. Purpose: Backward compatibility and optional methods.

---

**Q2: What are Static Methods in Interface?**

**Answer:** Methods that belong to interface, not instance. Called using interface name. Cannot be overridden or inherited. Purpose: Utility methods related to interface.

---

**Q3: Can Default Method be private?**

**Answer:** No. Default methods are always public. However, Java 9+ allows private methods in interfaces (as helper methods for default methods).

---

**Q4: How to resolve Diamond Problem with Default Methods?**

**Answer:** Override the conflicting method in the implementing class and explicitly call desired interface method using `InterfaceName.super.methodName()`.

---

**Q5: Difference between Default and Static methods in Interface?**

**Answer:**
- **Default**: Inherited, can override, accessed through instance
- **Static**: Not inherited, cannot override, accessed through interface name

---

## 🎯 Date/Time API (Java 8)

### Why New Date/Time API?

**Problems with Old API (java.util.Date, Calendar):**
1. ❌ **Mutable**: Not thread-safe
2. ❌ **Poor Design**: Month starts from 0, year from 1900
3. ❌ **Confusing**: Date class represents both date and time
4. ❌ **No Timezone Support**: Poor timezone handling
5. ❌ **Complex**: Difficult calculations

**Benefits of New API (java.time):**
1. ✅ **Immutable**: Thread-safe
2. ✅ **Clear API**: Intuitive methods
3. ✅ **Type Safety**: Separate classes for date, time, datetime
4. ✅ **Better Timezone**: ZonedDateTime
5. ✅ **ISO-8601 Standard**: International standard

---

### Main Classes

---

#### 1️⃣ LocalDate - Date Without Time

**Purpose:** Represent date (year-month-day) without time or timezone.

**Format:** `2024-12-25`

**Use Cases:** Birthday, holiday, event date

**Example:**
```java
import java.time.*;

public class LocalDateExample {
    public static void main(String[] args) {
        // Current date
        LocalDate today = LocalDate.now();
        System.out.println(today);  // 2024-12-25
        
        // Specific date
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalDate date2 = LocalDate.of(2024, Month.DECEMBER, 25);
        
        // Parse from string
        LocalDate parsed = LocalDate.parse("2024-12-25");
        
        // Extracting components
        int year = date.getYear();
        Month month = date.getMonth();
        int monthValue = date.getMonthValue();  // 12
        int day = date.getDayOfMonth();
        DayOfWeek dayOfWeek = date.getDayOfWeek();
        
        // Manipulating dates
        LocalDate tomorrow = today.plusDays(1);
        LocalDate nextWeek = today.plusWeeks(1);
        LocalDate nextMonth = today.plusMonths(1);
        LocalDate nextYear = today.plusYears(1);
        
        LocalDate yesterday = today.minusDays(1);
        
        // Setting specific component
        LocalDate modified = today.withYear(2025);
        LocalDate modified2 = today.withMonth(6);
        LocalDate modified3 = today.withDayOfMonth(15);
        
        // Comparison
        boolean isBefore = date.isBefore(today);
        boolean isAfter = date.isAfter(today);
        boolean isEqual = date.isEqual(today);
        
        // First/Last day of month
        LocalDate firstDay = today.withDayOfMonth(1);
        LocalDate lastDay = today.withDayOfMonth(today.lengthOfMonth());
    }
}
```

---

#### 2️⃣ LocalTime - Time Without Date

**Purpose:** Represent time (hour-minute-second) without date or timezone.

**Format:** `14:30:00`

**Use Cases:** Business hours, alarm time

**Example:**
```java
import java.time.*;

public class LocalTimeExample {
    public static void main(String[] args) {
        // Current time
        LocalTime now = LocalTime.now();
        System.out.println(now);  // 14:30:45.123
        
        // Specific time
        LocalTime time = LocalTime.of(14, 30);
        LocalTime time2 = LocalTime.of(14, 30, 45);
        LocalTime time3 = LocalTime.of(14, 30, 45, 123456789);
        
        // Parse from string
        LocalTime parsed = LocalTime.parse("14:30:00");
        
        // Extracting components
        int hour = time.getHour();
        int minute = time.getMinute();
        int second = time.getSecond();
        int nano = time.getNano();
        
        // Manipulating time
        LocalTime later = now.plusHours(2);
        LocalTime later2 = now.plusMinutes(30);
        LocalTime earlier = now.minusHours(1);
        
        // Comparison
        boolean isBefore = time.isBefore(now);
        boolean isAfter = time.isAfter(now);
    }
}
```

---

#### 3️⃣ LocalDateTime - Date and Time

**Purpose:** Represent date and time without timezone.

**Format:** `2024-12-25T14:30:00`

**Use Cases:** Event timestamp (local), meeting time

**Example:**
```java
import java.time.*;

public class LocalDateTimeExample {
    public static void main(String[] args) {
        // Current date-time
        LocalDateTime now = LocalDateTime.now();
        System.out.println(now);  // 2024-12-25T14:30:45.123
        
        // Specific date-time
        LocalDateTime dt = LocalDateTime.of(2024, 12, 25, 14, 30);
        LocalDateTime dt2 = LocalDateTime.of(2024, 12, 25, 14, 30, 45);
        
        // From LocalDate and LocalTime
        LocalDate date = LocalDate.now();
        LocalTime time = LocalTime.now();
        LocalDateTime dt3 = LocalDateTime.of(date, time);
        
        // Parse from string
        LocalDateTime parsed = LocalDateTime.parse("2024-12-25T14:30:00");
        
        // Extracting components
        LocalDate datePart = dt.toLocalDate();
        LocalTime timePart = dt.toLocalTime();
        
        // Manipulating
        LocalDateTime future = now.plusDays(1).plusHours(2);
        LocalDateTime past = now.minusWeeks(1);
    }
}
```

---

#### 4️⃣ ZonedDateTime - With Timezone

**Purpose:** Represent date, time, and timezone.

**Format:** `2024-12-25T14:30:00+05:30[Asia/Kolkata]`

**Use Cases:** International events, storing timestamps

**Example:**
```java
import java.time.*;

public class ZonedDateTimeExample {
    public static void main(String[] args) {
        // Current date-time with timezone
        ZonedDateTime now = ZonedDateTime.now();
        System.out.println(now);
        
        // Specific timezone
        ZonedDateTime tokyo = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
        ZonedDateTime ny = ZonedDateTime.now(ZoneId.of("America/New_York"));
        ZonedDateTime utc = ZonedDateTime.now(ZoneId.of("UTC"));
        
        // From LocalDateTime
        LocalDateTime ldt = LocalDateTime.now();
        ZonedDateTime zdt = ldt.atZone(ZoneId.systemDefault());
        
        // Convert between timezones
        ZonedDateTime tokyoTime = ZonedDateTime.now();
        ZonedDateTime nyTime = tokyoTime.withZoneSameInstant(ZoneId.of("America/New_York"));
    }
}
```

---

#### 5️⃣ Instant - Unix Timestamp

**Purpose:** Point on timeline (machine time).

**Format:** `2024-12-25T14:30:00Z` (UTC)

**Use Cases:** Logging, timestamps

**Example:**
```java
import java.time.*;

public class InstantExample {
    public static void main(String[] args) {
        // Current instant
        Instant now = Instant.now();
        System.out.println(now);
        
        // From epoch seconds
        Instant instant = Instant.ofEpochSecond(1640000000);
        
        // Get epoch seconds
        long epochSecond = now.getEpochSecond();
        long epochMilli = now.toEpochMilli();
        
        // Manipulating
        Instant later = now.plusSeconds(3600);
        Instant earlier = now.minusSeconds(3600);
    }
}
```

---

#### 6️⃣ Period - Date-Based Duration

**Purpose:** Represent duration in years, months, days.

**Format:** `P2Y3M10D` (2 years, 3 months, 10 days)

**Use Cases:** Age, duration between dates

**Example:**
```java
import java.time.*;

public class PeriodExample {
    public static void main(String[] args) {
        LocalDate birthDate = LocalDate.of(1990, 5, 15);
        LocalDate today = LocalDate.now();
        
        // Calculate age
        Period age = Period.between(birthDate, today);
        System.out.println("Age: " + age.getYears() + " years, " +
                          age.getMonths() + " months, " +
                          age.getDays() + " days");
        
        // Create period
        Period period = Period.ofYears(2);
        Period period2 = Period.ofMonths(3);
        Period period3 = Period.ofDays(10);
        Period period4 = Period.of(2, 3, 10);
        
        // Add period to date
        LocalDate future = today.plus(period);
    }
}
```

---

#### 7️⃣ Duration - Time-Based Duration

**Purpose:** Represent duration in hours, minutes, seconds.

**Format:** `PT5H30M` (5 hours, 30 minutes)

**Use Cases:** Duration between times

**Example:**
```java
import java.time.*;

public class DurationExample {
    public static void main(String[] args) {
        LocalTime start = LocalTime.of(9, 0);
        LocalTime end = LocalTime.of(17, 30);
        
        // Calculate duration
        Duration duration = Duration.between(start, end);
        System.out.println("Hours: " + duration.toHours());
        System.out.println("Minutes: " + duration.toMinutes());
        
        // Create duration
        Duration hour = Duration.ofHours(1);
        Duration minute = Duration.ofMinutes(30);
        Duration second = Duration.ofSeconds(60);
        
        // Add duration to time
        LocalTime future = start.plus(duration);
    }
}
```

---

#### 8️⃣ DateTimeFormatter - Format and Parse

**Purpose:** Format and parse date/time.

**Example:**
```java
import java.time.*;
import java.time.format.*;

public class DateTimeFormatterExample {
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();
        
        // Predefined formatters
        String iso = now.format(DateTimeFormatter.ISO_DATE_TIME);
        String isoDate = now.format(DateTimeFormatter.ISO_DATE);
        
        // Custom formatter
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss");
        String formatted = now.format(formatter);
        System.out.println(formatted);  // 25-12-2024 14:30:45
        
        // Parsing
        String dateStr = "25-12-2024 14:30:45";
        LocalDateTime parsed = LocalDateTime.parse(dateStr, formatter);
        
        // Common patterns
        DateTimeFormatter df1 = DateTimeFormatter.ofPattern("dd/MM/yyyy");
        DateTimeFormatter df2 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        DateTimeFormatter df3 = DateTimeFormatter.ofPattern("MMM dd, yyyy");
    }
}
```

---

### Complete Date/Time Example

```java
import java.time.*;
import java.time.format.*;

public class DateTimeCompleteExample {
    public static void main(String[] args) {
        // 1. Current date/time
        LocalDate today = LocalDate.now();
        LocalTime now = LocalTime.now();
        LocalDateTime dateTime = LocalDateTime.now();
        
        // 2. Specific date/time
        LocalDate christmas = LocalDate.of(2024, 12, 25);
        LocalTime meetingTime = LocalTime.of(14, 30);
        
        // 3. Parsing
        LocalDate parsed = LocalDate.parse("2024-12-25");
        
        // 4. Formatting
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
        String formatted = today.format(formatter);
        
        // 5. Manipulating
        LocalDate nextWeek = today.plusWeeks(1);
        LocalDate lastMonth = today.minusMonths(1);
        
        // 6. Comparison
        boolean isBefore = christmas.isBefore(today);
        
        // 7. Period (date-based)
        LocalDate birthDate = LocalDate.of(1990, 5, 15);
        Period age = Period.between(birthDate, today);
        
        // 8. Duration (time-based)
        LocalTime start = LocalTime.of(9, 0);
        LocalTime end = LocalTime.of(17, 30);
        Duration workHours = Duration.between(start, end);
        
        // 9. ZonedDateTime
        ZonedDateTime nowWithZone = ZonedDateTime.now();
        ZonedDateTime tokyo = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
        
        // 10. Instant
        Instant instant = Instant.now();
        long epochSecond = instant.getEpochSecond();
    }
}
```

---

### 🔥 Interview Questions - Date/Time API

**Q1: Why was new Date/Time API introduced in Java 8?**

**Answer:** Old API (Date, Calendar) had problems:
- Mutable (not thread-safe)
- Poor design (month 0-11, year from 1900)
- Complex to use
- Poor timezone support

New API is immutable, thread-safe, clear, and follows ISO-8601.

---

**Q2: Difference between LocalDate, LocalDateTime, and ZonedDateTime?**

**Answer:**
- **LocalDate**: Date only (no time, no timezone) - `2024-12-25`
- **LocalDateTime**: Date + Time (no timezone) - `2024-12-25T14:30:00`
- **ZonedDateTime**: Date + Time + Timezone - `2024-12-25T14:30:00+05:30[Asia/Kolkata]`

---

**Q3: Difference between Period and Duration?**

**Answer:**
- **Period**: Date-based duration (years, months, days) - use with LocalDate
- **Duration**: Time-based duration (hours, minutes, seconds) - use with LocalTime

---

**Q4: What is Instant?**

**Answer:** Instant represents a point on timeline (machine time). It's UTC-based timestamp, similar to Unix timestamp. Used for logging and timestamps.

---

**Q5: How to format date in custom pattern?**

**Answer:**
```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
String formatted = date.format(formatter);
```

---

## 🔥 Interview Questions

### Lambda Expressions

**Q1: Can lambda access outer variables? What is effectively final?**

**Answer:** Yes, lambda can access outer variables but they must be effectively final (value doesn't change). This ensures thread safety and predictability.

---

**Q2: Can lambda throw checked exceptions?**

**Answer:** Only if the functional interface method declares that exception. Otherwise, handle inside lambda.

---

### Functional Interfaces

**Q3: Can functional interface have multiple default methods?**

**Answer:** Yes! Functional interface must have exactly ONE abstract method, but can have unlimited default and static methods.

---

**Q4: What happens if you remove @FunctionalInterface annotation?**

**Answer:** Still works as functional interface if it has one abstract method. Annotation only provides compile-time checking.

---

### Streams

**Q5: Can you reuse a stream?**

**Answer:** No. Stream can be used only once. After terminal operation, stream is consumed. Create new stream for reuse.

---

**Q6: Difference between intermediate and terminal operations?**

**Answer:**
- **Intermediate**: Lazy, return Stream, not executed until terminal operation
- **Terminal**: Eager, trigger execution, produce result

---

**Q7: When to use parallel streams?**

**Answer:** Use when:
- Large dataset
- Computationally expensive operations
- Independent operations (no shared state)
- Don't care about order

---

### Optional

**Q8: Should you use Optional as method parameter?**

**Answer:** No. Use Optional only as return type. For parameters, accept actual type and let caller pass null if needed.

---

**Q9: Should you use Optional as class field?**

**Answer:** No. Optional is not Serializable and adds overhead. Use nullable field and return Optional from getter.

---

### Method References

**Q10: When can't you use method reference?**

**Answer:** When lambda has additional logic beyond just calling method. Use lambda for complex operations.

---

## 🎯 Practice Coding Questions

### Question 1: Filter and Transform

**Problem:** Given list of integers, filter even numbers, square them, and collect to list.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Solution
List<Integer> result = numbers.stream()
                             .filter(n -> n % 2 == 0)
                             .map(n -> n * n)
                             .collect(Collectors.toList());
// Result: [4, 16, 36, 64, 100]
```

---

### Question 2: Find Average Salary

**Problem:** Given list of employees, find average salary of employees in IT department.

```java
class Employee {
    String name;
    String dept;
    int salary;
    
    Employee(String name, String dept, int salary) {
        this.name = name;
        this.dept = dept;
        this.salary = salary;
    }
    
    String getDept() { return dept; }
    int getSalary() { return salary; }
}

List<Employee> employees = Arrays.asList(
    new Employee("John", "IT", 50000),
    new Employee("Alice", "HR", 45000),
    new Employee("Bob", "IT", 55000)
);

// Solution
double avgSalary = employees.stream()
                           .filter(e -> e.getDept().equals("IT"))
                           .mapToInt(Employee::getSalary)
                           .average()
                           .orElse(0.0);
// Result: 52500.0
```

---

### Question 3: Group By and Count

**Problem:** Given list of words, group by first letter and count each group.

```java
List<String> words = Arrays.asList("apple", "banana", "apricot", "cherry", "avocado");

// Solution
Map<Character, Long> grouped = words.stream()
    .collect(Collectors.groupingBy(
        s -> s.charAt(0),
        Collectors.counting()
    ));
// Result: {a=3, b=1, c=1}
```

---

### Question 4: Find Duplicate Elements

**Problem:** Find duplicate elements in list using streams.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 3, 6);

// Solution
Set<Integer> seen = new HashSet<>();
Set<Integer> duplicates = numbers.stream()
                                .filter(n -> !seen.add(n))
                                .collect(Collectors.toSet());
// Result: [2, 3]
```

---

### Question 5: Flatten Nested List

**Problem:** Flatten list of lists into single list.

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5, 6)
);

// Solution
List<Integer> flattened = nested.stream()
                                .flatMap(List::stream)
                                .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5, 6]
```

---

### Question 6: Age Calculation

**Problem:** Calculate age from birthdate using Period.

```java
import java.time.*;

LocalDate birthDate = LocalDate.of(1990, 5, 15);
LocalDate today = LocalDate.now();

// Solution
Period age = Period.between(birthDate, today);
System.out.println("Age: " + age.getYears() + " years");
```

---

### Question 7: Find Longest String

**Problem:** Find longest string in list using Optional.

```java
List<String> names = Arrays.asList("John", "Alice", "Bob", "Alexander");

// Solution
Optional<String> longest = names.stream()
                               .max(Comparator.comparing(String::length));
longest.ifPresent(System.out::println);  // Alexander
```

---

### Question 8: Partition Even/Odd

**Problem:** Partition numbers into even and odd groups.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Solution
Map<Boolean, List<Integer>> partitioned = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
// Result: {false=[1,3,5,7,9], true=[2,4,6,8,10]}
```

---

### Question 9: Custom Functional Interface

**Problem:** Create functional interface for calculation with default method.

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
    
    default void printResult(int result) {
        System.out.println("Result: " + result);
    }
}

// Usage
Calculator add = (a, b) -> a + b;
int result = add.calculate(5, 3);
add.printResult(result);
```

---

### Question 10: Convert List to Map

**Problem:** Convert list of employees to map (name → salary).

```java
List<Employee> employees = Arrays.asList(
    new Employee("John", "IT", 50000),
    new Employee("Alice", "HR", 45000),
    new Employee("Bob", "IT", 55000)
);

// Solution
Map<String, Integer> nameToSalary = employees.stream()
    .collect(Collectors.toMap(
        e -> e.name,
        e -> e.salary
    ));
// Result: {John=50000, Alice=45000, Bob=55000}
```

---

## 📝 Summary

### Key Points to Remember

1. **Lambda Expressions**: Anonymous functions, enable functional programming
2. **Functional Interfaces**: One abstract method, enable lambdas
3. **Streams API**: Process collections functionally, lazy evaluation
4. **Optional**: Better null handling, avoid NullPointerException
5. **Method References**: Shorthand for lambdas calling existing methods
6. **Default Methods**: Methods with implementation in interface
7. **Static Methods**: Utility methods in interface
8. **Date/Time API**: Immutable, thread-safe, clear API

---

### Common Patterns

**Filter → Map → Collect:**
```java
list.stream()
    .filter(predicate)
    .map(function)
    .collect(Collectors.toList());
```

**Optional Chain:**
```java
optional.filter(predicate)
        .map(function)
        .orElse(default);
```

**Method Reference:**
```java
list.forEach(System.out::println);
list.stream().map(String::toUpperCase);
```

---

## 📄 One-Page Cheatsheet

### 🔹 Lambda Expressions

**Syntax:** `(parameters) -> expression` or `(parameters) -> { statements; }`

```java
// Zero parameters
() -> System.out.println("Hello")

// Single parameter (parentheses optional)
x -> x * x

// Multiple parameters
(a, b) -> a + b

// Multiple statements
(a, b) -> {
    int sum = a + b;
    return sum;
}
```

**Key Rules:**
- Variables must be **effectively final**
- Can only implement **functional interfaces**
- Type inference supported

---

### 🔹 Functional Interfaces (4 Main + 3 Others)

| Interface | Input | Output | Method | Use Case |
|-----------|-------|--------|--------|----------|
| **Predicate\<T>** | T | boolean | `test(T)` | Filter, validate |
| **Consumer\<T>** | T | void | `accept(T)` | Print, log, side effects |
| **Supplier\<T>** | None | T | `get()` | Factory, lazy init |
| **Function\<T,R>** | T | R | `apply(T)` | Transform, map |
| **BiFunction\<T,U,R>** | T, U | R | `apply(T,U)` | Two inputs → one output |
| **UnaryOperator\<T>** | T | T | `apply(T)` | Same type transform |
| **BinaryOperator\<T>** | T, T | T | `apply(T,T)` | Reduce, combine |

**Examples:**
```java
Predicate<Integer> isEven = n -> n % 2 == 0;
Consumer<String> print = s -> System.out.println(s);
Supplier<Double> random = () -> Math.random();
Function<String, Integer> length = s -> s.length();
```

---

### 🔹 Stream Operations

**Stream Pipeline:** `Source → Intermediate Ops → Terminal Op`

**Intermediate (Lazy - return Stream):**
```java
filter(Predicate)      // Select matching elements
map(Function)          // Transform one-to-one
flatMap(Function)      // Transform and flatten
distinct()             // Remove duplicates
sorted()               // Sort elements
limit(n)               // Take first n
skip(n)                // Skip first n
peek(Consumer)         // Debug (doesn't modify)
```

**Terminal (Eager - trigger execution):**
```java
forEach(Consumer)      // Iterate
collect(Collector)     // To List/Set/Map
reduce(BinaryOp)       // Combine to single value
count()                // Count elements
anyMatch(Predicate)    // At least one matches
allMatch(Predicate)    // All match
noneMatch(Predicate)   // None match
findFirst()            // First element
findAny()              // Any element
min/max(Comparator)    // Min/max element
toArray()              // To array
```

**Common Patterns:**
```java
// Filter → Map → Collect
list.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .collect(Collectors.toList());

// Reduce sum
list.stream().reduce(0, Integer::sum);

// Group by
list.stream().collect(Collectors.groupingBy(String::length));

// Partition
list.stream().collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

---

### 🔹 Optional Class

**Creation:**
```java
Optional.of(value)           // Non-null (throws if null)
Optional.ofNullable(value)   // Nullable (safest)
Optional.empty()             // Empty optional
```

**Getting Value:**
```java
orElse(default)              // Return default if empty
orElseGet(Supplier)          // Lazy default (preferred)
orElseThrow()                // Throw exception if empty
get()                        // ❌ AVOID - throws exception
```

**Transforming:**
```java
map(Function)                // Transform value
flatMap(Function)            // Transform to Optional
filter(Predicate)            // Keep if matches
```

**Consuming:**
```java
ifPresent(Consumer)          // Execute if present
ifPresentOrElse(Consumer, Runnable)  // Both branches
```

**✅ DO:** Use as return type  
**❌ DON'T:** Use as field, parameter, or for collections

---

### 🔹 Method References (4 Types)

| Type | Syntax | Lambda Equivalent |
|------|--------|-------------------|
| **Static** | `ClassName::method` | `(args) -> ClassName.method(args)` |
| **Instance (object)** | `object::method` | `(args) -> object.method(args)` |
| **Instance (class)** | `ClassName::method` | `(obj, args) -> obj.method(args)` |
| **Constructor** | `ClassName::new` | `(args) -> new ClassName(args)` |

**Examples:**
```java
Integer::parseInt           // Static
System.out::println         // Instance on object
String::toUpperCase         // Instance on class
ArrayList::new              // Constructor
```

---

### 🔹 Default & Static Methods in Interfaces

**Default Method:**
```java
interface Vehicle {
    default void stop() {
        System.out.println("Stopped");
    }
}
```
- Has implementation
- Can be overridden
- Accessed through instance
- Use `InterfaceName.super.method()` to call specific

**Static Method:**
```java
interface MathOp {
    static int add(int a, int b) {
        return a + b;
    }
}
// Call: MathOp.add(5, 3)
```
- Cannot be overridden
- Not inherited
- Accessed through interface name

---

### 🔹 Date/Time API

| Class | Purpose | Format | Use Case |
|-------|---------|--------|----------|
| **LocalDate** | Date only | `2024-12-25` | Birthday, holiday |
| **LocalTime** | Time only | `14:30:00` | Business hours |
| **LocalDateTime** | Date + Time | `2024-12-25T14:30:00` | Meeting time |
| **ZonedDateTime** | Date + Time + TZ | `2024-12-25T14:30:00+05:30[Asia/Kolkata]` | International events |
| **Instant** | Unix timestamp | `2024-12-25T14:30:00Z` | Logging |
| **Period** | Date duration | `P2Y3M10D` | Age (years/months/days) |
| **Duration** | Time duration | `PT5H30M` | Time difference |

**Common Operations:**
```java
// Create
LocalDate.now()
LocalDate.of(2024, 12, 25)
LocalDate.parse("2024-12-25")

// Manipulate
date.plusDays(1)
date.minusMonths(1)
date.withYear(2025)

// Compare
date.isBefore(other)
date.isAfter(other)

// Format
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
date.format(formatter)

// Calculate
Period.between(birthDate, today)
Duration.between(start, end)
```

---

### 🔹 Quick Reference: Stream Collectors

```java
Collectors.toList()
Collectors.toSet()
Collectors.toMap(keyMapper, valueMapper)
Collectors.joining(", ")
Collectors.groupingBy(classifier)
Collectors.partitioningBy(predicate)
Collectors.counting()
Collectors.summingInt(mapper)
Collectors.averagingInt(mapper)
```

---

### 🔹 Key Interview Points

✅ **Lambda:** Anonymous function, enables functional programming, effectively final  
✅ **Functional Interface:** Exactly ONE abstract method, unlimited default/static  
✅ **Stream:** NOT data structure, lazy evaluation, one-time use  
✅ **Optional:** Avoid NPE, use as return type only, don't use get() directly  
✅ **Method Reference:** Shorthand for lambda calling existing method  
✅ **map() vs flatMap():** One-to-one vs one-to-many with flattening  
✅ **Intermediate vs Terminal:** Lazy vs eager, Stream vs result  
✅ **orElse() vs orElseGet():** Always evaluated vs lazy (use orElseGet for expensive ops)  
✅ **Period vs Duration:** Date-based (years/months/days) vs time-based (hours/minutes/seconds)  
✅ **Parallel Stream:** Use for large datasets, expensive ops, independent operations  

---

### 🔹 Common Mistakes to Avoid

❌ Using `get()` on Optional without checking  
❌ Using `isPresent()` + `get()` instead of `orElse()`  
❌ Using Optional as field or parameter  
❌ Reusing streams (create new stream)  
❌ Side effects in stream operations  
❌ Not handling checked exceptions in lambdas  
❌ Using `orElse()` for expensive operations (use `orElseGet()`)  
❌ Calling static interface methods through instance  

---

### 🔹 Practice Pattern

```java
// Filter even, square, collect
numbers.stream()
       .filter(n -> n % 2 == 0)
       .map(n -> n * n)
       .collect(Collectors.toList());

// Group by length
names.stream()
     .collect(Collectors.groupingBy(String::length));

// Find max with Optional
numbers.stream()
       .max(Integer::compareTo)
       .orElse(0);

// Flatten nested list
nested.stream()
      .flatMap(List::stream)
      .collect(Collectors.toList());

// Calculate age
Period.between(birthDate, LocalDate.now()).getYears();
```

---

**🎯 Master these concepts for interviews! Practice daily! 🚀**
