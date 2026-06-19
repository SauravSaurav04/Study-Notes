# Core Java - Complete Interview Guide

## Table of Contents
1. [Java Basics](#java-basics)
2. [OOP Concepts](#oop-concepts)
3. [Data Types and Variables](#data-types-and-variables)
4. [Control Flow Statements](#control-flow-statements)
5. [Arrays](#arrays)
6. [Strings](#strings)
7. [Exception Handling](#exception-handling)
8. [Collections Framework](#collections-framework)
9. [Multithreading & Concurrency](#multithreading--concurrency)
10. [Java 8+ Features](#java-8-features)
11. [Generics](#generics)
12. [File I/O](#file-io)
13. [Annotations](#annotations)
14. [Design Patterns](#design-patterns)
15. [Memory Management & Garbage Collection](#memory-management--garbage-collection)
16. [Important Keywords](#important-keywords)
17. [JDBC](#jdbc)

---

## Java Basics

### JDK vs JRE vs JVM

**Theory:**
Java's architecture is divided into three main components that work together to compile and execute Java programs:

**1. JDK (Java Development Kit):**
- Complete software development kit for Java developers
- Includes JRE + development tools (javac compiler, debugger, javadoc, jar, etc.)
- Required for developing Java applications
- Larger in size compared to JRE

**2. JRE (Java Runtime Environment):**
- Provides the runtime environment to execute Java applications
- Contains JVM, core libraries, and supporting files
- Required only for running Java applications (not for development)
- End users need only JRE installed

**3. JVM (Java Virtual Machine):**
- Abstract machine that provides runtime environment for Java bytecode execution
- Platform-dependent (different JVM for Windows, Linux, Mac)
- Performs: Loading code, Verifying code, Executing code, Providing runtime environment
- Makes Java platform-independent ("Write Once, Run Anywhere")

**Execution Flow:**
1. Java source code (.java file) → Compiled by javac (JDK)
2. Bytecode generated (.class file) → Platform independent
3. JVM loads and executes bytecode → Converts to machine code
4. Operating system executes machine code

```java
/*
JDK (Java Development Kit): Contains JRE + Development Tools (compiler, debugger)
JRE (Java Runtime Environment): Contains JVM + Library Classes
JVM (Java Virtual Machine): Executes bytecode
*/
```

### First Java Program

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}

/*
public: Access modifier
static: Can be called without creating object
void: Returns nothing
main: Entry point of program
String[] args: Command line arguments
*/
```

### Java Features

**Theory:**
Java is designed with several key features that make it a powerful and versatile programming language:

**1. Platform Independent (WORA - Write Once, Run Anywhere):**
- Java code is compiled to bytecode, not machine code
- Bytecode can run on any platform with JVM installed
- Same .class file works on Windows, Linux, Mac, etc.

**2. Object-Oriented:**
- Everything revolves around objects and classes
- Supports OOP principles: Encapsulation, Inheritance, Polymorphism, Abstraction
- Promotes code reusability and modularity

**3. Simple and Easy:**
- Syntax based on C++, but removes complex features (pointers, operator overloading, multiple inheritance)
- Automatic memory management (Garbage Collection)
- Rich API and extensive documentation

**4. Secure:**
- No explicit pointers, preventing direct memory access
- Bytecode verification before execution
- Security Manager defines access to system resources
- Built-in security features for web applications

**5. Robust:**
- Strong type checking at compile-time
- Exception handling mechanism
- Automatic garbage collection prevents memory leaks
- Eliminates error-prone situations (pointer arithmetic)

**6. Multithreaded:**
- Built-in support for concurrent programming
- Can execute multiple threads simultaneously
- Improves performance in multiprocessor systems

**7. Distributed:**
- Supports distributed computing with RMI and EJB
- Easy to create distributed applications
- Network-centric programming capabilities

**8. High Performance:**
- JIT (Just-In-Time) compiler improves performance
- Bytecode converted to native machine code at runtime
- Optimizations performed during execution

---

## OOP Concepts

**Overview Theory:**
Object-Oriented Programming (OOP) is a programming paradigm based on the concept of "objects" which contain data and code. The four main pillars of OOP are:

1. **Encapsulation** - Bundling data and methods that operate on that data
2. **Inheritance** - Creating new classes from existing ones
3. **Polymorphism** - Objects taking many forms
4. **Abstraction** - Hiding complex implementation details

### 1. Class and Object

**Theory:**

**Class:**
- Blueprint or template for creating objects
- Defines properties (attributes/fields) and behaviors (methods)
- Logical entity that doesn't occupy memory
- Can contain: fields, methods, constructors, blocks, nested classes

**Object:**
- Instance of a class
- Real-world entity with state and behavior
- Occupies memory (allocated in heap)
- Has three characteristics:
  - **State**: Represented by attributes (fields)
  - **Behavior**: Represented by methods
  - **Identity**: Unique ID to distinguish from other objects

**Constructor:**
- Special method used to initialize objects
- Same name as class name
- No return type (not even void)
- Called automatically when object is created
- Types: Default constructor, Parameterized constructor, Copy constructor

```java
// Class Definition
public class Car {
    // Instance variables (attributes)
    private String brand;
    private String model;
    private int year;
    
    // Constructor
    public Car(String brand, String model, int year) {
        this.brand = brand;
        this.model = model;
        this.year = year;
    }
    
    // Method
    public void displayInfo() {
        System.out.println(brand + " " + model + " (" + year + ")");
    }
    
    // Getters and Setters
    public String getBrand() {
        return brand;
    }
    
    public void setBrand(String brand) {
        this.brand = brand;
    }
}

// Object Creation
public class Main {
    public static void main(String[] args) {
        Car car1 = new Car("Toyota", "Camry", 2023);
        car1.displayInfo();
    }
}
```

### 2. Encapsulation

**Theory:**
Encapsulation is the bundling of data (variables) and methods that operate on the data into a single unit (class). It's also known as **data hiding** because:

**Key Concepts:**
- **Data Hiding**: Making fields private so they cannot be accessed directly from outside the class
- **Access Control**: Using access modifiers (private, protected, public) to control visibility
- **Getter/Setter Methods**: Providing controlled access to private fields through public methods

**Benefits:**
1. **Security**: Protects object's internal state from unintended modifications
2. **Flexibility**: Implementation can be changed without affecting external code
3. **Maintainability**: Changes in one class don't affect other classes
4. **Reusability**: Well-encapsulated code is easier to reuse
5. **Control**: Can add validation logic in setter methods

**How to Achieve:**
1. Declare class variables as private
2. Provide public getter methods to read field values
3. Provide public setter methods to modify field values (with validation if needed)

```java
public class BankAccount {
    // Private fields (data hiding)
    private double balance;
    private String accountNumber;
    
    public BankAccount(String accountNumber) {
        this.accountNumber = accountNumber;
        this.balance = 0.0;
    }
    
    // Public methods to access private data
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        }
    }
    
    public double getBalance() {
        return balance;
    }
}
```

### 3. Inheritance

**Theory:**
Inheritance is a mechanism where a new class (child/sub class) acquires properties and behaviors of an existing class (parent/super class). It represents an **IS-A** relationship.

**Key Concepts:**
- **Parent/Super/Base Class**: The class being inherited from
- **Child/Sub/Derived Class**: The class that inherits from parent class
- **Code Reusability**: Main advantage - reuse existing code
- **Method Overriding**: Child class can provide specific implementation of parent's method

**Types of Inheritance in Java:**
1. **Single Inheritance**: One class inherits one class (A → B)
2. **Multilevel Inheritance**: Chain of inheritance (A → B → C)
3. **Hierarchical Inheritance**: Multiple classes inherit one class (B ← A → C)
4. **Multiple Inheritance**: One class inherits multiple classes (NOT supported in Java with classes, but possible with interfaces)
5. **Hybrid Inheritance**: Combination of multiple and hierarchical (NOT supported in Java)

**Why Java Doesn't Support Multiple Inheritance (with classes)?**
- **Diamond Problem**: Ambiguity when two parent classes have same method
- Solution: Java uses interfaces to achieve multiple inheritance

**Important Keywords:**
- `extends`: Used to inherit a class
- `super`: Refers to parent class object
- `super()`: Calls parent class constructor
- `@Override`: Annotation to override parent class method

**Benefits:**
1. Code reusability
2. Method overriding (runtime polymorphism)
3. Extensibility of code
4. Data hiding (protected members)

```java
// Parent Class (Super class)
class Animal {
    protected String name;
    
    public Animal(String name) {
        this.name = name;
    }
    
    public void eat() {
        System.out.println(name + " is eating");
    }
}

// Child Class (Sub class)
class Dog extends Animal {
    private String breed;
    
    public Dog(String name, String breed) {
        super(name); // Call parent constructor
        this.breed = breed;
    }
    
    // Method Overriding
    @Override
    public void eat() {
        System.out.println(name + " is eating dog food");
    }
    
    public void bark() {
        System.out.println(name + " is barking");
    }
}

// Types of Inheritance in Java:
// 1. Single Inheritance
// 2. Multilevel Inheritance
// 3. Hierarchical Inheritance
// Note: Multiple Inheritance is NOT supported (but can be achieved via interfaces)
```

### 4. Polymorphism

**Theory:**
Polymorphism means "many forms" - the ability of an object to take many forms. It allows one interface to be used for a general class of actions.

**Types of Polymorphism:**

**1. Compile-time Polymorphism (Static Binding/Early Binding):**
- Resolved at compile time
- Achieved through Method Overloading and Operator Overloading (not in Java)
- Faster execution

**2. Runtime Polymorphism (Dynamic Binding/Late Binding):**
- Resolved at runtime
- Achieved through Method Overriding
- Requires inheritance
- More flexible

**Method Overloading vs Method Overriding:**

| Feature | Overloading | Overriding |
|---------|-------------|------------|
| Definition | Same method name, different parameters | Same method signature in parent and child |
| Binding | Compile-time | Runtime |
| Inheritance | Not required | Required |
| Return Type | Can be different | Must be same (or covariant) |
| Access Modifier | Can be different | Cannot be more restrictive |
| Performance | Faster | Slightly slower |
| Purpose | Add new behavior | Change existing behavior |

#### a) Compile-time Polymorphism (Method Overloading)

**Theory:**
Method Overloading allows a class to have multiple methods with the same name but different parameters.

**Rules:**
1. Method name must be same
2. Parameters must be different (number, type, or order)
3. Return type can be different (but changing return type alone is not overloading)
4. Can have different access modifiers
5. Can throw different exceptions

**Ways to Overload:**
1. Different number of parameters
2. Different data types of parameters
3. Different order of parameters

```java
class Calculator {
    // Method Overloading - Same method name, different parameters
    public int add(int a, int b) {
        return a + b;
    }
    
    public int add(int a, int b, int c) {
        return a + b + c;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
}
```

#### b) Runtime Polymorphism (Method Overriding)

**Theory:**
Method Overriding occurs when a child class provides a specific implementation of a method that is already defined in its parent class.

**Rules:**
1. Method signature must be same (name, parameters, return type)
2. Requires inheritance (IS-A relationship)
3. Cannot override private, static, or final methods
4. Cannot make access modifier more restrictive
5. Can throw same, subclass, or no exception (for checked exceptions)
6. Use @Override annotation (optional but recommended)

**Covariant Return Type (Java 5+):**
- Overriding method can return subtype of the type returned by parent method

**Dynamic Method Dispatch:**
- JVM decides which method to call at runtime based on object type
- Reference type determines which objects can be assigned
- Object type determines which method implementation is executed

```java
class Shape {
    public void draw() {
        System.out.println("Drawing a shape");
    }
}

class Circle extends Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a circle");
    }
}

class Rectangle extends Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a rectangle");
    }
}

public class TestPolymorphism {
    public static void main(String[] args) {
        Shape shape1 = new Circle();    // Upcasting
        Shape shape2 = new Rectangle();
        
        shape1.draw(); // Drawing a circle
        shape2.draw(); // Drawing a rectangle
    }
}
```

### 5. Abstraction

**Theory:**
Abstraction is the process of hiding implementation details and showing only essential features to the user. It focuses on "what an object does" rather than "how it does it".

**Real-World Example:**
- You drive a car without knowing how the engine works internally
- You use a phone without knowing how it processes calls

**Ways to Achieve Abstraction in Java:**
1. Abstract Classes (0-100% abstraction)
2. Interfaces (100% abstraction)

**Benefits:**
1. Reduces complexity by hiding unnecessary details
2. Increases security (internal implementation hidden)
3. Enhances maintainability
4. Supports code reusability
5. Improves modularity

#### a) Abstract Class

**Theory:**
An abstract class is a class declared with `abstract` keyword that cannot be instantiated and may contain abstract methods.

**Key Features:**
1. **Cannot be instantiated**: Cannot create objects directly
2. **Can have abstract methods**: Methods without implementation
3. **Can have concrete methods**: Methods with implementation
4. **Can have constructors**: Used by subclasses
5. **Can have instance variables**: Both static and non-static
6. **Can have any access modifier**: public, protected, default, private
7. **Extended using 'extends' keyword**: Child class must implement all abstract methods

**When to Use:**
- When classes share common behavior but have some differences
- When you want to provide default implementation for some methods
- When you need to use access modifiers other than public
- When you need instance variables or constructors

**Abstract Method:**
- Method declared without implementation (no body)
- Must be declared with `abstract` keyword
- Child class must override and provide implementation
- Cannot be private, static, or final

```java
abstract class Vehicle {
    protected String brand;
    
    public Vehicle(String brand) {
        this.brand = brand;
    }
    
    // Abstract method (no implementation)
    public abstract void start();
    
    // Concrete method
    public void stop() {
        System.out.println("Vehicle stopped");
    }
}

class Car extends Vehicle {
    public Car(String brand) {
        super(brand);
    }
    
    @Override
    public void start() {
        System.out.println(brand + " car started");
    }
}
```

#### b) Interface

**Theory:**
An interface is a reference type in Java, similar to a class, that contains only abstract methods (before Java 8). It's a blueprint of a class and achieves 100% abstraction.

**Key Features:**
1. **All methods are public and abstract by default** (before Java 8)
2. **All fields are public, static, and final by default** (constants)
3. **Cannot have constructors** (cannot be instantiated)
4. **Implemented using 'implements' keyword**
5. **A class can implement multiple interfaces** (multiple inheritance)
6. **An interface can extend multiple interfaces**

**Java 8 Enhancements:**
1. **Default Methods**: Methods with implementation using `default` keyword
2. **Static Methods**: Utility methods that can be called without instance

**Java 9 Enhancements:**
1. **Private Methods**: Helper methods for default methods
2. **Private Static Methods**: Helper methods for static methods

**When to Use:**
- When multiple classes need to implement same methods differently
- To achieve multiple inheritance
- To define contract that multiple classes should follow
- To achieve loose coupling

**Benefits:**
1. Achieves 100% abstraction (before Java 8)
2. Supports multiple inheritance
3. Loose coupling between classes
4. Easy to maintain and extend

```java
interface Drawable {
    // All methods are public and abstract by default
    void draw();
    
    // Default method (Java 8+)
    default void display() {
        System.out.println("Displaying...");
    }
    
    // Static method (Java 8+)
    static void info() {
        System.out.println("This is a drawable interface");
    }
}

interface Resizable {
    void resize(int width, int height);
}

// Multiple interface implementation
class Image implements Drawable, Resizable {
    @Override
    public void draw() {
        System.out.println("Drawing image");
    }
    
    @Override
    public void resize(int width, int height) {
        System.out.println("Resizing to " + width + "x" + height);
    }
}
```

### Abstract Class vs Interface

**Theory:**
Both abstract classes and interfaces are used to achieve abstraction, but they have different purposes and capabilities.

**When to Use Abstract Class:**
- When you want to define common behavior with some default implementation
- When you need instance variables or constructors
- When you need access modifiers other than public
- When classes have "IS-A" relationship
- When you want to share code among closely related classes

**When to Use Interface:**
- When you want to define a contract for unrelated classes
- To achieve multiple inheritance
- When you want complete abstraction
- When classes may not be related but share common behavior
- To define capabilities (e.g., Serializable, Cloneable, Comparable)

**Detailed Comparison:**

| Feature | Abstract Class | Interface |
|---------|---------------|------------|
| **Inheritance** | Single (extends one class) | Multiple (implements multiple interfaces) |
| **Methods** | Can have abstract and concrete methods | All abstract (before Java 8), default/static (Java 8+) |
| **Variables** | Can have any type of variables | Only public, static, final (constants) |
| **Access Modifiers** | Any (public, protected, private, default) | Only public (methods and fields) |
| **Constructors** | Can have constructors | Cannot have constructors |
| **Speed** | Faster | Slightly slower |
| **Use Case** | Related classes with some common code | Unrelated classes implementing same contract |
| **Keyword** | extends | implements |
| **Instantiation** | Cannot be instantiated | Cannot be instantiated |
| **Default Implementation** | Can provide default methods | Can provide default methods (Java 8+) |

```java
/*
Abstract Class:
- Can have both abstract and concrete methods
- Can have constructors
- Can have instance variables
- Supports single inheritance
- Use 'extends' keyword

Interface:
- All methods are abstract by default (before Java 8)
- Cannot have constructors
- All variables are public, static, final
- Supports multiple inheritance
- Use 'implements' keyword
- Can have default and static methods (Java 8+)
*/
```

---

## Data Types and Variables

**Theory:**
Data types specify the type of data that a variable can hold. Java is a strongly-typed language, meaning every variable must have a declared type.

**Categories of Data Types:**

**1. Primitive Data Types:**
- Predefined by Java language
- Store simple values
- Not objects (lowercase names)
- Stored in stack memory
- 8 primitive types: byte, short, int, long, float, double, char, boolean

**2. Non-Primitive (Reference) Data Types:**
- Created by programmer (except String, Array)
- Store addresses/references
- Called objects (class instances)
- Stored in heap memory
- Examples: Classes, Interfaces, Arrays, String

**Size and Range of Primitive Types:**

| Type | Size | Range | Default Value |
|------|------|-------|---------------|
| byte | 8-bit | -128 to 127 | 0 |
| short | 16-bit | -32,768 to 32,767 | 0 |
| int | 32-bit | -2³¹ to 2³¹-1 | 0 |
| long | 64-bit | -2⁶³ to 2⁶³-1 | 0L |
| float | 32-bit | up to 7 decimal digits | 0.0f |
| double | 64-bit | up to 16 decimal digits | 0.0d |
| char | 16-bit | 0 to 65,535 (Unicode) | '\u0000' |
| boolean | 1-bit | true or false | false |

### Primitive Data Types

**Theory:**

**Type Casting:**
Converting one data type to another.

**1. Widening (Implicit/Automatic Casting):**
- Converting smaller type to larger type
- Done automatically by compiler
- No data loss
- Order: byte → short → int → long → float → double

**2. Narrowing (Explicit Casting):**
- Converting larger type to smaller type
- Must be done manually
- May cause data loss
- Syntax: (targetType) value

```java
public class DataTypes {
    public static void main(String[] args) {
        // Integer types
        byte b = 127;           // 8-bit (-128 to 127)
        short s = 32767;        // 16-bit (-32,768 to 32,767)
        int i = 2147483647;     // 32-bit
        long l = 9223372036854775807L; // 64-bit
        
        // Floating point
        float f = 3.14f;        // 32-bit
        double d = 3.14159;     // 64-bit
        
        // Character
        char c = 'A';           // 16-bit Unicode
        
        // Boolean
        boolean bool = true;     // true or false
        
        // Type casting
        int num = 100;
        long longNum = num;      // Implicit casting (widening)
        
        double dbl = 9.78;
        int intVal = (int) dbl;  // Explicit casting (narrowing)
    }
}
```

### Variables Types

**Theory:**
Variables are containers for storing data values. Java has three types of variables:

**1. Local Variables:**
- Declared inside methods, constructors, or blocks
- Created when method/block is entered, destroyed when exited
- Stored in stack memory
- **No default value** - must be initialized before use
- Cannot use access modifiers
- Not accessible outside the method/block

**2. Instance Variables (Non-static):**
- Declared inside class but outside methods
- Belong to object (each object has its own copy)
- Created when object is created using `new` keyword
- Destroyed when object is destroyed
- Stored in heap memory
- Have default values (0, null, false, etc.)
- Can use access modifiers
- Accessed using object reference

**3. Static Variables (Class Variables):**
- Declared with `static` keyword inside class
- Belong to class (shared among all objects)
- Only one copy exists regardless of number of objects
- Created when program starts, destroyed when program ends
- Stored in method area
- Have default values
- Accessed using class name

```java
public class VariableTypes {
    // Instance variable (Non-static)
    int instanceVar = 10;
    
    // Static variable (Class variable)
    static int staticVar = 20;
    
    public void method() {
        // Local variable
        int localVar = 30;
        System.out.println(localVar);
    }
}
```

### Wrapper Classes

**Theory:**
Wrapper classes convert primitive data types into objects. Each primitive type has a corresponding wrapper class.

**Why Wrapper Classes?**
1. **Collections Framework**: Collections (ArrayList, HashMap) can only store objects, not primitives
2. **Utility Methods**: Wrapper classes provide useful methods for conversion
3. **Null Assignment**: Primitives cannot be null, but wrapper objects can
4. **Generics**: Requires objects, not primitives
5. **Synchronization**: Object reference needed for synchronization

**Primitive to Wrapper Mapping:**
- byte → Byte
- short → Short
- int → Integer
- long → Long
- float → Float
- double → Double
- char → Character
- boolean → Boolean

**Autoboxing:**
Automatic conversion of primitive to wrapper class object.
- Example: `Integer num = 10;` (int automatically converted to Integer)

**Unboxing:**
Automatic conversion of wrapper class object to primitive.
- Example: `int value = num;` (Integer automatically converted to int)

**Important Methods:**
- `parseInt()`, `parseDouble()`: String to primitive
- `toString()`: Primitive/wrapper to String
- `valueOf()`: String to wrapper object
- `xxxValue()`: Wrapper to primitive (intValue(), doubleValue(), etc.)

```java
public class WrapperDemo {
    public static void main(String[] args) {
        // Autoboxing (primitive to wrapper)
        Integer num = 10;
        
        // Unboxing (wrapper to primitive)
        int value = num;
        
        // Wrapper classes: Byte, Short, Integer, Long, Float, Double, Character, Boolean
        
        // String to primitive
        int i = Integer.parseInt("123");
        double d = Double.parseDouble("3.14");
        
        // Primitive to String
        String str = Integer.toString(123);
    }
}
```

---

## Control Flow Statements

**Theory:**
Control flow statements control the order in which statements are executed in a program. They allow the program to make decisions, repeat actions, and branch in different directions.

**Types:**
1. **Decision Making**: if, if-else, switch
2. **Looping**: for, while, do-while
3. **Branching**: break, continue, return

### If-Else

**Theory:**
Executes a block of code based on a condition (true or false).

**Types:**
1. **Simple if**: Executes block if condition is true
2. **if-else**: Executes one block if true, another if false
3. **if-else-if ladder**: Multiple conditions checked sequentially
4. **Nested if**: if statement inside another if statement
5. **Ternary operator**: Shorthand for if-else: `condition ? value1 : value2`

```java
public class IfElseDemo {
    public static void main(String[] args) {
        int score = 85;
        
        if (score >= 90) {
            System.out.println("Grade: A");
        } else if (score >= 80) {
            System.out.println("Grade: B");
        } else if (score >= 70) {
            System.out.println("Grade: C");
        } else {
            System.out.println("Grade: F");
        }
        
        // Ternary operator
        String result = (score >= 60) ? "Pass" : "Fail";
    }
}
```

### Switch Statement

**Theory:**
Executes specific block based on matching case. Alternative to long if-else-if ladder.

**Features:**
- Works with: byte, short, int, char, String (Java 7+), enum
- Does NOT work with: long, float, double, boolean
- `break` statement exits the switch
- Without break, falls through to next case
- `default` case executes if no match found (optional)
- Case values must be constant or literal

**Java 12+ Enhancement (Switch Expressions):**
- Can return values
- Arrow syntax: `case value -> expression`
- No fall-through with arrow syntax

```java
public class SwitchDemo {
    public static void main(String[] args) {
        int day = 3;
        
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
            default:
                System.out.println("Invalid day");
        }
        
        // Switch with String (Java 7+)
        String fruit = "Apple";
        switch (fruit) {
            case "Apple":
                System.out.println("Red fruit");
                break;
            case "Banana":
                System.out.println("Yellow fruit");
                break;
        }
    }
}
```

### Loops

**Theory:**
Loops execute a block of code repeatedly until a condition is met.

**Types:**

**1. for loop:**
- Used when number of iterations is known
- Three parts: initialization, condition, increment/decrement
- All three parts are optional

**2. Enhanced for loop (for-each):**
- Used to iterate through arrays or collections
- Cannot modify elements or access index
- More readable and less error-prone

**3. while loop:**
- Used when number of iterations is unknown
- Condition checked before execution
- May not execute even once if condition is false initially
- Entry-controlled loop

**4. do-while loop:**
- Similar to while, but condition checked after execution
- Executes at least once
- Exit-controlled loop

**Loop Control Statements:**
- **break**: Exits the loop immediately
- **continue**: Skips current iteration and continues with next
- **return**: Exits from method (and loop)

**for vs while vs do-while:**
| Feature | for | while | do-while |
|---------|-----|-------|----------|
| Use | Known iterations | Unknown iterations | At least one execution |
| Check | Before execution | Before execution | After execution |
| Initialization | In loop syntax | Before loop | Before loop |

```java
public class LoopDemo {
    public static void main(String[] args) {
        // For loop
        for (int i = 0; i < 5; i++) {
            System.out.println("Iteration: " + i);
        }
        
        // Enhanced for loop (for-each)
        int[] numbers = {1, 2, 3, 4, 5};
        for (int num : numbers) {
            System.out.println(num);
        }
        
        // While loop
        int i = 0;
        while (i < 5) {
            System.out.println(i);
            i++;
        }
        
        // Do-while loop
        int j = 0;
        do {
            System.out.println(j);
            j++;
        } while (j < 5);
        
        // Break and Continue
        for (int k = 0; k < 10; k++) {
            if (k == 3) continue;  // Skip iteration
            if (k == 7) break;     // Exit loop
            System.out.println(k);
        }
    }
}
```

---

## Arrays

**Theory:**
An array is a container object that holds a fixed number of values of a single type.

**Key Characteristics:**
1. **Fixed Size**: Size determined at creation time, cannot be changed
2. **Contiguous Memory**: Elements stored in consecutive memory locations
3. **Zero-Indexed**: First element at index 0
4. **Homogeneous**: All elements must be of same type
5. **Reference Type**: Array is an object in Java
6. **Length Property**: `.length` gives number of elements

**Benefits:**
- Access elements by index (O(1) time complexity)
- Store multiple values in single variable
- Efficient memory usage
- Can be passed to methods

**Limitations:**
- Fixed size (use ArrayList for dynamic size)
- Can only store one type of data
- No built-in methods for operations

**Memory Allocation:**
- Array reference stored in stack
- Array object stored in heap
- Elements stored contiguously in heap

### Single Dimensional Arrays

**Theory:**

**Declaration Syntax:**
```
dataType[] arrayName;           // Preferred
dataType arrayName[];           // Valid but not preferred
```

**Initialization:**
1. **Static Initialization**: `int[] arr = {1, 2, 3, 4, 5};`
2. **Dynamic Initialization**: `int[] arr = new int[5];`
3. **Declaration and Initialization Separately**:
   ```
   int[] arr;
   arr = new int[5];
   ```

**Default Values:**
- int, short, byte, long: 0
- float, double: 0.0
- char: '\u0000'
- boolean: false
- Object references: null

```java
public class ArrayDemo {
    public static void main(String[] args) {
        // Array declaration and initialization
        int[] arr1 = new int[5];
        int[] arr2 = {1, 2, 3, 4, 5};
        
        // Accessing elements
        arr1[0] = 10;
        System.out.println(arr2[0]);
        
        // Array length
        System.out.println("Length: " + arr2.length);
        
        // Iterating array
        for (int i = 0; i < arr2.length; i++) {
            System.out.println(arr2[i]);
        }
        
        // Enhanced for loop
        for (int num : arr2) {
            System.out.println(num);
        }
    }
}
```

### Multi-dimensional Arrays

**Theory:**
Array of arrays. Represents data in matrix form (rows and columns).

**Types:**
1. **2D Array**: Two indices [row][column]
   - Example: Student marks in multiple subjects
2. **3D Array**: Three indices [depth][row][column]
   - Example: Marks of multiple classes
3. **Jagged Array**: Array with different column sizes
   - Not all rows need same number of columns

**Declaration:**
```
int[][] arr;                    // 2D array
int[][][] arr;                  // 3D array
```

**Initialization:**
```
int[][] arr = new int[3][4];   // 3 rows, 4 columns
int[][] arr = {{1,2}, {3,4}};  // Direct initialization
```

**Memory Layout:**
- 2D array is array of references to 1D arrays
- Each row can have different size (jagged array)

```java
public class MultiArrayDemo {
    public static void main(String[] args) {
        // 2D Array
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // Accessing elements
        System.out.println(matrix[0][0]); // 1
        
        // Iterating 2D array
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                System.out.print(matrix[i][j] + " ");
            }
            System.out.println();
        }
        
        // 3D Array
        int[][][] arr3D = new int[3][3][3];
    }
}
```

### Array Operations

```java
import java.util.Arrays;

public class ArrayOperations {
    public static void main(String[] args) {
        int[] arr = {5, 2, 8, 1, 9};
        
        // Sorting
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
        
        // Binary Search (array must be sorted)
        int index = Arrays.binarySearch(arr, 8);
        
        // Copy array
        int[] copy = Arrays.copyOf(arr, arr.length);
        
        // Fill array
        int[] filled = new int[5];
        Arrays.fill(filled, 10);
        
        // Compare arrays
        boolean equal = Arrays.equals(arr, copy);
    }
}
```

---

## Strings

**Theory:**
String is a sequence of characters. In Java, strings are objects of the String class.

**Key Characteristics:**

**1. Immutable:**
- Once created, cannot be changed
- Any modification creates a new String object
- Why immutable?
  - **Security**: Sensitive data (passwords, network connections) remains unchanged
  - **Thread Safety**: Can be safely shared between threads
  - **String Pool**: Memory optimization through string interning
  - **Hashcode Caching**: Hash value remains constant

**2. String Pool (String Constant Pool):**
- Special memory region in heap (before Java 7) or method area (Java 7+)
- Stores string literals to avoid duplicate objects
- When you create string literal, JVM checks pool first
- If exists, returns reference; otherwise, creates new string

**3. Two Ways to Create String:**

**a) String Literal:**
```java
String str = "Hello";
```
- Stored in String Pool
- Reuses existing string if found
- More memory efficient
- Preferred method

**b) Using new Keyword:**
```java
String str = new String("Hello");
```
- Creates object in heap memory
- Always creates new object (even if string exists in pool)
- Less efficient
- Use when you need different object

**String Comparison:**
- `==` operator: Compares references (memory addresses)
- `.equals()` method: Compares content (values)
- `.equalsIgnoreCase()`: Compares content ignoring case
- `.compareTo()`: Lexicographic comparison (returns int)

### String Creation

```java
public class StringDemo {
    public static void main(String[] args) {
        // String literal (stored in String Pool)
        String str1 = "Hello";
        
        // Using new keyword (stored in Heap)
        String str2 = new String("Hello");
        
        // String comparison
        System.out.println(str1 == str2);        // false (different references)
        System.out.println(str1.equals(str2));   // true (same content)
        
        // String is immutable
        String s = "Java";
        s.concat(" Programming"); // Creates new string
        System.out.println(s);    // Still "Java"
        
        s = s.concat(" Programming"); // Must reassign
        System.out.println(s);        // "Java Programming"
    }
}
```

### String Methods

```java
public class StringMethods {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // Length
        int len = str.length(); // 11
        
        // Character at index
        char ch = str.charAt(0); // 'H'
        
        // Substring
        String sub1 = str.substring(6);      // "World"
        String sub2 = str.substring(0, 5);   // "Hello"
        
        // Contains
        boolean contains = str.contains("World"); // true
        
        // Index of
        int index = str.indexOf("World"); // 6
        
        // Replace
        String replaced = str.replace("World", "Java"); // "Hello Java"
        
        // Split
        String[] words = str.split(" "); // ["Hello", "World"]
        
        // Case conversion
        String upper = str.toUpperCase(); // "HELLO WORLD"
        String lower = str.toLowerCase(); // "hello world"
        
        // Trim
        String trimmed = "  Hello  ".trim(); // "Hello"
        
        // Starts with / Ends with
        boolean starts = str.startsWith("Hello"); // true
        boolean ends = str.endsWith("World");     // true
        
        // Compare
        int compare = str.compareTo("Hello World"); // 0
        
        // Empty check
        boolean empty = str.isEmpty(); // false
        boolean blank = "   ".isBlank(); // true (Java 11+)
    }
}
```

### StringBuilder and StringBuffer

**Theory:**
Both are mutable alternatives to String class. Used when you need to modify strings frequently.

**Why Use StringBuilder/StringBuffer?**
- String concatenation with + operator creates new objects
- For multiple concatenations, this is inefficient
- StringBuilder/StringBuffer modify same object
- Better performance for string manipulation

**Comparison:**

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|---------------|
| **Mutability** | Immutable | Mutable | Mutable |
| **Thread Safety** | Immutable (thread-safe) | Not synchronized (not thread-safe) | Synchronized (thread-safe) |
| **Performance** | Slow (for concatenation) | Fast | Slower than StringBuilder |
| **Memory** | New object per change | Same object modified | Same object modified |
| **When to Use** | Constant strings | Single-threaded env | Multi-threaded env |
| **Since** | JDK 1.0 | Java 5 | JDK 1.0 |

**Common Methods:**
- `append()`: Add string at end
- `insert()`: Insert string at specified position
- `delete()`: Remove characters from start to end
- `replace()`: Replace characters from start to end
- `reverse()`: Reverse the string
- `capacity()`: Current capacity
- `length()`: Current length
- `charAt()`: Character at index
- `substring()`: Extract substring
- `toString()`: Convert to String

**When to Use What?**
- **String**: When string value won't change (constants, configuration)
- **StringBuilder**: Single-threaded environment with frequent modifications
- **StringBuffer**: Multi-threaded environment with frequent modifications

**Performance Example:**
```
String: 1000 concatenations = 1000 new objects
StringBuilder: 1000 concatenations = same object, more efficient
```

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        // StringBuilder (Not thread-safe, faster)
        StringBuilder sb = new StringBuilder("Hello");
        sb.append(" World");      // "Hello World"
        sb.insert(5, ",");        // "Hello, World"
        sb.delete(5, 6);          // "Hello World"
        sb.reverse();             // "dlroW olleH"
        sb.reverse();             // "Hello World"
        
        // StringBuffer (Thread-safe, slower)
        StringBuffer sbf = new StringBuffer("Hello");
        sbf.append(" World");
        
        // Convert to String
        String result = sb.toString();
        
        /*
        Comparison:
        String: Immutable, slow for concatenation
        StringBuilder: Mutable, fast, not thread-safe
        StringBuffer: Mutable, slower than StringBuilder, thread-safe
        */
    }
}
```

---

## Exception Handling

**Theory:**
An exception is an unwanted or unexpected event that occurs during program execution and disrupts normal flow.

**Why Exception Handling?**
1. **Maintain Normal Flow**: Program continues after handling exception
2. **Separate Error Handling**: Error handling code separate from main logic
3. **Categorize Exceptions**: Different types handled differently
4. **Provide Meaningful Messages**: Help debug issues
5. **Graceful Degradation**: Program fails gracefully rather than crashing

**Exception vs Error:**
- **Exception**: Recoverable, can be handled (IOException, SQLException)
- **Error**: Irrecoverable, JVM issues (OutOfMemoryError, StackOverflowError)

**Types of Exceptions:**

**1. Checked Exceptions (Compile-time):**
- Checked by compiler at compile time
- Must be handled or declared
- Examples: IOException, SQLException, ClassNotFoundException
- Use: For recoverable conditions

**2. Unchecked Exceptions (Runtime):**
- Not checked by compiler
- Occur at runtime
- Extend RuntimeException class
- Examples: ArithmeticException, NullPointerException, ArrayIndexOutOfBoundsException
- Use: For programming errors

**3. Errors:**
- Serious problems that applications should not try to catch
- Extend Error class
- Examples: OutOfMemoryError, StackOverflowError

**Exception Handling Keywords:**
1. **try**: Block containing code that might throw exception
2. **catch**: Block that handles the exception
3. **finally**: Block that always executes (cleanup code)
4. **throw**: Manually throw an exception
5. **throws**: Declare exceptions that method might throw

**try-catch-finally Flow:**
1. Code in try block executes
2. If exception occurs, matching catch block executes
3. finally block always executes (even if return statement present)
4. If no exception, catch block skipped but finally executes

### Try-Catch-Finally

```java
public class ExceptionDemo {
    public static void main(String[] args) {
        try {
            int result = 10 / 0; // ArithmeticException
            
            int[] arr = new int[5];
            arr[10] = 50; // ArrayIndexOutOfBoundsException
            
            String str = null;
            str.length(); // NullPointerException
            
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero");
            e.printStackTrace();
            
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Array index out of bounds");
            
        } catch (Exception e) {
            // Generic catch block (should be last)
            System.out.println("Exception: " + e.getMessage());
            
        } finally {
            // Always executes (even if exception occurs)
            System.out.println("Finally block executed");
        }
    }
}
```

### Try-with-Resources (Java 7+)

**Theory:**
Automatic resource management feature that automatically closes resources without explicit finally block.

**Key Features:**
1. **Automatic Resource Closure**: Resources closed automatically
2. **Cleaner Code**: No need for finally block
3. **Multiple Resources**: Can declare multiple resources separated by semicolon
4. **Must Implement AutoCloseable**: Resource must implement AutoCloseable or Closeable
5. **Resources Closed in Reverse Order**: Last opened, first closed

**Benefits:**
- Prevents resource leaks
- Less boilerplate code
- Resources closed even if exception occurs
- Compiler ensures resources are closed

**Syntax:**
```java
try (ResourceType resource = new ResourceType()) {
    // Use resource
} catch (Exception e) {
    // Handle exception
}
// Resource automatically closed here
```

**Common AutoCloseable Resources:**
- FileReader, FileWriter
- BufferedReader, BufferedWriter
- FileInputStream, FileOutputStream
- Connection, Statement, ResultSet (JDBC)
- Scanner

```java
import java.io.*;

public class TryWithResources {
    public static void main(String[] args) {
        // Automatically closes resources
        try (FileReader fr = new FileReader("file.txt");
             BufferedReader br = new BufferedReader(fr)) {
            
            String line = br.readLine();
            System.out.println(line);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        // No need for finally block to close resources
    }
}
```

### Throw and Throws

**Theory:**
Both are used in exception handling but serve different purposes.

**throw Keyword:**
- Used to explicitly throw an exception
- Used inside method body
- Can throw only one exception at a time
- Followed by exception object (instance)
- Syntax: `throw new ExceptionType("message");`
- Used for custom business logic validation

**throws Keyword:**
- Used to declare exceptions
- Used in method signature
- Can declare multiple exceptions (comma-separated)
- Followed by exception class name(s)
- Syntax: `void method() throws Exception1, Exception2`
- Indicates method might throw these exceptions
- Caller must handle or propagate

**Comparison:**
| Feature | throw | throws |
|---------|-------|--------|
| Purpose | Throw exception | Declare exception |
| Location | Inside method body | Method signature |
| Exceptions | One at a time | Multiple (comma-separated) |
| Followed by | Exception instance | Exception class |
| Use | Explicitly throw | Delegate handling to caller |

**When to Use:**
- **throw**: When you want to explicitly create and throw exception
- **throws**: When method might throw checked exception and you want caller to handle it

```java
public class ThrowThrows {
    // throws: Declares exception
    public static void validateAge(int age) throws Exception {
        if (age < 18) {
            // throw: Actually throws exception
            throw new Exception("Age must be 18 or above");
        }
        System.out.println("Valid age");
    }
    
    public static void main(String[] args) {
        try {
            validateAge(15);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

### Custom Exception

**Theory:**
User-defined exceptions created by extending Exception class (checked) or RuntimeException class (unchecked).

**Why Custom Exceptions?**
1. **Business Logic Specific**: Represent application-specific error conditions
2. **Meaningful Names**: Exception name indicates exact problem
3. **Better Error Handling**: Can add custom fields and methods
4. **Categorization**: Group related exceptions
5. **Clarity**: Makes code more readable and maintainable

**Creating Custom Exception:**
1. Create class extending Exception (checked) or RuntimeException (unchecked)
2. Provide constructors:
   - No-arg constructor
   - Constructor with message
   - Constructor with message and cause
3. Add custom fields/methods if needed

**Checked vs Unchecked Custom Exception:**

**Extend Exception (Checked):**
- For recoverable conditions
- Compiler forces handling
- Example: InvalidAgeException, InsufficientBalanceException

**Extend RuntimeException (Unchecked):**
- For programming errors
- Optional handling
- Example: InvalidInputException, ConfigurationException

**Best Practices:**
1. Use meaningful exception names ending with "Exception"
2. Provide detailed error messages
3. Include constructors for different scenarios
4. Document with JavaDoc
5. Make exception class immutable
6. Provide relevant context in exception

```java
// Custom Checked Exception
class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}

// Custom Unchecked Exception
class InvalidInputException extends RuntimeException {
    public InvalidInputException(String message) {
        super(message);
    }
}

public class CustomExceptionDemo {
    public static void validateAge(int age) throws InvalidAgeException {
        if (age < 0 || age > 150) {
            throw new InvalidAgeException("Invalid age: " + age);
        }
    }
    
    public static void main(String[] args) {
        try {
            validateAge(-5);
        } catch (InvalidAgeException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

### Exception Hierarchy

```java
/*
Throwable
├── Error (Unchecked - JVM errors)
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
    │   └── IllegalArgumentException
    │
    └── Checked Exceptions
        ├── IOException
        ├── SQLException
        ├── ClassNotFoundException
        └── InterruptedException
*/
```

---

## Collections Framework

**Theory:**
The Java Collections Framework is a unified architecture for representing and manipulating collections of objects.

**What is a Collection?**
- A collection is an object that groups multiple elements into a single unit
- Used to store, retrieve, manipulate, and communicate aggregate data

**Benefits:**
1. **Reduces Programming Effort**: Pre-built data structures and algorithms
2. **Increases Performance**: High-performance implementations
3. **Interoperability**: Common interfaces for different implementations
4. **Reduces Learning**: Consistent API design
5. **Reusability**: Standard interfaces and implementations

**Core Interfaces Hierarchy:**
```
Iterable
    └── Collection
            ├── List (Ordered, allows duplicates)
            │     ├── ArrayList
            │     ├── LinkedList
            │     └── Vector
            │           └── Stack
            ├── Set (Unordered, no duplicates)
            │     ├── HashSet
            │     ├── LinkedHashSet
            │     └── SortedSet
            │           └── TreeSet
            └── Queue (Orders elements for processing)
                  ├── PriorityQueue
                  ├── Deque
                  │     ├── ArrayDeque
                  │     └── LinkedList
                  └── BlockingQueue

Map (Key-Value pairs, not part of Collection)
    ├── HashMap
    ├── LinkedHashMap
    ├── Hashtable
    └── SortedMap
          └── TreeMap
```

**Key Interfaces:**

| Interface | Ordered | Sorted | Duplicates | Null | Implementation Examples |
|-----------|---------|--------|------------|------|-------------------------|
| List | Yes | No | Yes | Yes | ArrayList, LinkedList, Vector |
| Set | No | No | No | Yes (one) | HashSet, LinkedHashSet |
| SortedSet | No | Yes | No | No | TreeSet |
| Queue | Yes | No | Yes | Depends | LinkedList, PriorityQueue |
| Deque | Yes | No | Yes | No | ArrayDeque, LinkedList |
| Map | No | No | No (keys) | Yes | HashMap, LinkedHashMap |
| SortedMap | No | Yes | No (keys) | No | TreeMap |

### List Interface

**Theory:**
Ordered collection (sequence) that allows duplicate elements. Users can access elements by integer index.

**Characteristics:**
1. **Ordered**: Maintains insertion order
2. **Index-based**: Access elements by position (0-based)
3. **Duplicates Allowed**: Can contain duplicate elements
4. **Null Elements**: Allows multiple null elements
5. **Dynamic Size**: Can grow or shrink

**Common Methods:**
- `add(element)`, `add(index, element)`
- `get(index)`, `set(index, element)`
- `remove(index)`, `remove(object)`
- `indexOf(object)`, `lastIndexOf(object)`
- `subList(fromIndex, toIndex)`
- `size()`, `isEmpty()`, `clear()`
- `contains(object)`, `containsAll(collection)`

**When to Use:**
- When you need to maintain insertion order
- When you need index-based access
- When duplicates are acceptable
- When frequent access by position is needed

#### ArrayList

**Theory:**
Dynamic array implementation of List interface.

**How it Works:**
- Internally uses dynamic array to store elements
- Default initial capacity: 10
- When full, creates new array with increased capacity
- Growth: new capacity = (old capacity * 3/2) + 1
- Copies elements from old array to new array

**Characteristics:**
1. **Random Access**: Fast access by index O(1)
2. **Slow Insertion/Deletion**: O(n) in worst case (element shifts required)
3. **Not Synchronized**: Not thread-safe
4. **Dynamic Size**: Grows automatically
5. **Memory Efficient**: Less memory overhead than LinkedList

**Time Complexity:**
- Access: O(1)
- Search: O(n)
- Insertion at end: O(1) amortized
- Insertion at middle/start: O(n)
- Deletion: O(n)

**When to Use:**
- Frequent access by index
- Read-heavy operations
- Infrequent insertions/deletions
- Memory is a concern

**When NOT to Use:**
- Frequent insertions/deletions at beginning or middle
- Thread safety required (use Vector or Collections.synchronizedList())

```java
import java.util.*;

public class ArrayListDemo {
    public static void main(String[] args) {
        // Creating ArrayList
        ArrayList<String> list = new ArrayList<>();
        
        // Adding elements
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");
        list.add(1, "Avocado"); // Insert at index
        
        // Accessing elements
        String fruit = list.get(0);
        
        // Updating element
        list.set(0, "Apricot");
        
        // Removing element
        list.remove("Banana");
        list.remove(0); // Remove by index
        
        // Size
        int size = list.size();
        
        // Check if contains
        boolean contains = list.contains("Cherry");
        
        // Iterating
        for (String item : list) {
            System.out.println(item);
        }
        
        // Using Iterator
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        
        // Clear all elements
        list.clear();
        
        // Check if empty
        boolean empty = list.isEmpty();
    }
}
```

#### LinkedList

**Theory:**
Doubly-linked list implementation of List and Deque interfaces.

**How it Works:**
- Each element (node) contains:
  - Data
  - Reference to next node
  - Reference to previous node
- Nodes can be anywhere in memory (not contiguous)
- First node: head, Last node: tail

**Characteristics:**
1. **Sequential Access**: Slow random access O(n)
2. **Fast Insertion/Deletion**: O(1) at known positions
3. **Doubly Linked**: Can traverse forward and backward
4. **Implements Deque**: Can be used as stack or queue
5. **More Memory**: Extra space for node references

**Time Complexity:**
- Access: O(n) - must traverse
- Search: O(n)
- Insertion at start/end: O(1)
- Insertion at middle: O(n) - need to find position
- Deletion: O(1) if position known, O(n) otherwise

**When to Use:**
- Frequent insertions/deletions at beginning
- Need to implement queue or stack
- Sequential access is acceptable
- Don't need random access

**When NOT to Use:**
- Need fast random access
- Memory is limited
- Frequent access by index

**ArrayList vs LinkedList:**

| Feature | ArrayList | LinkedList |
|---------|-----------|------------|
| **Implementation** | Dynamic array | Doubly linked list |
| **Access Time** | O(1) - Fast | O(n) - Slow |
| **Insertion (start)** | O(n) - Slow | O(1) - Fast |
| **Insertion (end)** | O(1) - Fast | O(1) - Fast |
| **Memory** | Less overhead | More overhead (node pointers) |
| **Cache Performance** | Better (contiguous) | Worse (scattered) |
| **Use Case** | Random access | Frequent insert/delete |

```java
import java.util.*;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>();
        
        // Add elements
        list.add(10);
        list.add(20);
        list.addFirst(5);   // Add at beginning
        list.addLast(30);   // Add at end
        
        // Get elements
        int first = list.getFirst();
        int last = list.getLast();
        
        // Remove elements
        list.removeFirst();
        list.removeLast();
        
        /*
        ArrayList vs LinkedList:
        
        ArrayList:
        - Dynamic array
        - Fast random access O(1)
        - Slow insertion/deletion O(n)
        - Less memory overhead
        
        LinkedList:
        - Doubly linked list
        - Slow random access O(n)
        - Fast insertion/deletion O(1)
        - More memory overhead
        */
    }
}
```

#### Vector

**Theory:**
Legacy synchronized implementation of List interface (since JDK 1.0).

**Characteristics:**
1. **Synchronized**: Thread-safe (all methods synchronized)
2. **Slow Performance**: Due to synchronization overhead
3. **Dynamic Array**: Similar to ArrayList
4. **Legacy Class**: Part of original Java (before Collections Framework)
5. **Growth**: Doubles in size when capacity exceeded

**Vector vs ArrayList:**

| Feature | Vector | ArrayList |
|---------|--------|------------|
| **Synchronized** | Yes (thread-safe) | No (not thread-safe) |
| **Performance** | Slower | Faster |
| **Growth** | 100% (doubles) | 50% (old * 1.5) |
| **Legacy** | Since JDK 1.0 | Since Java 1.2 |
| **When to Use** | Multi-threaded | Single-threaded |
| **Modern Alternative** | Collections.synchronizedList() | Preferred |

**When to Use:**
- Thread safety is required (though better alternatives exist)
- Working with legacy code

**Better Alternatives:**
- ArrayList with external synchronization
- Collections.synchronizedList(new ArrayList<>())
- CopyOnWriteArrayList for concurrent reads

```java
import java.util.*;

public class VectorDemo {
    public static void main(String[] args) {
        Vector<Integer> vector = new Vector<>();
        
        // Similar to ArrayList but synchronized (thread-safe)
        vector.add(10);
        vector.add(20);
        
        // Additional methods
        vector.addElement(30);
        vector.removeElement(20);
        
        /*
        ArrayList vs Vector:
        - Vector is synchronized (thread-safe)
        - Vector is slower than ArrayList
        - Vector grows by 100% when capacity exceeds
        - ArrayList grows by 50%
        */
    }
}
```

### Set Interface

**Theory:**
Collection that contains no duplicate elements. Models mathematical set abstraction.

**Characteristics:**
1. **No Duplicates**: Each element occurs at most once
2. **Unordered**: No guaranteed order (except LinkedHashSet and TreeSet)
3. **Null Elements**: At most one null (HashSet, LinkedHashSet)
4. **No Index**: Cannot access elements by position
5. **Based on equals()**: Duplicates determined using equals() method

**Common Methods:**
- `add(element)`: Returns false if duplicate
- `remove(element)`
- `contains(element)`
- `size()`, `isEmpty()`, `clear()`
- `iterator()`

**Set Implementations Comparison:**

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---------------|----------|
| **Order** | No order | Insertion order | Sorted (natural/custom) |
| **Performance** | O(1) | O(1) | O(log n) |
| **Null** | One null allowed | One null allowed | No null |
| **Implementation** | HashMap | LinkedHashMap | TreeMap (Red-Black tree) |
| **Memory** | Less | More (links) | More (tree structure) |
| **Use Case** | Performance | Order + Performance | Sorted data |

**When to Use Set:**
- Need to ensure uniqueness
- Order doesn't matter (or use LinkedHashSet/TreeSet)
- Frequent contains() operations
- Mathematical set operations (union, intersection)

#### HashSet

**Theory:**
Implements Set interface using hash table (HashMap internally).

**How it Works:**
- Uses HashMap internally
- Elements stored as keys in HashMap
- All keys mapped to same dummy value (PRESENT object)
- Uses hashCode() and equals() to determine uniqueness

**Characteristics:**
1. **No Duplicates**: Determined by equals() and hashCode()
2. **No Order**: Elements not ordered
3. **Allows Null**: One null element allowed
4. **Fast Operations**: O(1) for add, remove, contains
5. **Not Synchronized**: Not thread-safe

**Important:**
- Must override hashCode() and equals() for custom objects
- Poor hashCode() implementation can degrade performance to O(n)

**When to Use:**
- Need fast operations (add, remove, contains)
- Don't care about order
- Need to eliminate duplicates

```java
import java.util.*;

public class HashSetDemo {
    public static void main(String[] args) {
        HashSet<String> set = new HashSet<>();
        
        // Add elements (no duplicates)
        set.add("Apple");
        set.add("Banana");
        set.add("Apple"); // Duplicate, won't be added
        
        // Size
        System.out.println(set.size()); // 2
        
        // Contains
        boolean contains = set.contains("Apple");
        
        // Remove
        set.remove("Banana");
        
        // Iterate (no guaranteed order)
        for (String item : set) {
            System.out.println(item);
        }
        
        // Clear
        set.clear();
    }
}
```

#### LinkedHashSet

**Theory:**
HashSet with predictable iteration order. Maintains doubly-linked list across all elements.

**How it Works:**
- Extends HashSet
- Uses LinkedHashMap internally
- Each element has link to next and previous element
- Maintains insertion order through linked list

**Characteristics:**
1. **Insertion Order**: Maintains order in which elements were inserted
2. **Performance**: Slightly slower than HashSet (due to linked list maintenance)
3. **Allows Null**: One null element allowed
4. **Duplicates**: No duplicates

**Time Complexity:**
- Add, Remove, Contains: O(1)
- Iteration: O(n)

**When to Use:**
- Need insertion order
- Need Set behavior (no duplicates)
- Performance slightly less critical than HashSet

**HashSet vs LinkedHashSet:**
- HashSet: Faster, no order guarantee
- LinkedHashSet: Slightly slower, maintains insertion order

```java
import java.util.*;

public class LinkedHashSetDemo {
    public static void main(String[] args) {
        // Maintains insertion order
        LinkedHashSet<String> set = new LinkedHashSet<>();
        
        set.add("Apple");
        set.add("Banana");
        set.add("Cherry");
        
        // Iterates in insertion order
        for (String item : set) {
            System.out.println(item);
        }
    }
}
```

#### TreeSet

**Theory:**
Navigable set implementation based on TreeMap. Elements stored in sorted order.

**How it Works:**
- Uses TreeMap internally (Red-Black tree)
- Elements stored as keys in TreeMap
- Self-balancing binary search tree
- Natural ordering or custom Comparator

**Characteristics:**
1. **Sorted**: Elements in ascending order
2. **No Null**: Does not allow null (throws NullPointerException)
3. **Slower Operations**: O(log n) time
4. **NavigableSet**: Additional navigation methods
5. **Implements SortedSet**: Provides range operations

**Time Complexity:**
- Add, Remove, Contains: O(log n)
- Iteration: O(n)

**Sorting:**
1. **Natural Ordering**: Elements must implement Comparable
2. **Custom Comparator**: Provide Comparator in constructor

**Additional Methods:**
- `first()`, `last()`: First and last element
- `lower(element)`, `higher(element)`: Elements strictly less/greater
- `floor(element)`, `ceiling(element)`: Elements less/greater or equal
- `subSet(from, to)`: Elements in range
- `headSet(toElement)`, `tailSet(fromElement)`

**When to Use:**
- Need sorted elements
- Need range operations
- Need navigation methods (floor, ceiling, etc.)
- Can accept O(log n) performance

**When NOT to Use:**
- Need faster operations (use HashSet)
- Elements cannot be compared
- Need null elements

```java
import java.util.*;

public class TreeSetDemo {
    public static void main(String[] args) {
        // Sorted set (natural ordering)
        TreeSet<Integer> set = new TreeSet<>();
        
        set.add(50);
        set.add(20);
        set.add(80);
        set.add(10);
        
        // Iterates in sorted order: 10, 20, 50, 80
        for (int num : set) {
            System.out.println(num);
        }
        
        // Additional methods
        int first = set.first();      // 10
        int last = set.last();        // 80
        int lower = set.lower(50);    // 20
        int higher = set.higher(50);  // 80
        
        /*
        HashSet vs LinkedHashSet vs TreeSet:
        
        HashSet:
        - No order guaranteed
        - O(1) operations
        - Allows null
        
        LinkedHashSet:
        - Insertion order maintained
        - O(1) operations
        - Allows null
        
        TreeSet:
        - Sorted order (natural or custom)
        - O(log n) operations
        - Does NOT allow null
        */
    }
}
```

### Queue Interface

**Theory:**
Collection designed for holding elements prior to processing. Typically orders elements in FIFO (First-In-First-Out) manner.

**Characteristics:**
1. **FIFO Order**: First element inserted is first to be removed (typical)
2. **Priority Queue**: Can order by priority instead of FIFO
3. **Two Method Forms**:
   - **Throws Exception**: add(), remove(), element()
   - **Returns Special Value**: offer(), poll(), peek()
4. **Bounded/Unbounded**: Can have capacity limit or not

**Queue Methods:**

| Operation | Throws Exception | Returns Special Value |
|-----------|------------------|------------------------|
| **Insert** | add(e) | offer(e) - returns false if full |
| **Remove** | remove() | poll() - returns null if empty |
| **Examine** | element() | peek() - returns null if empty |

**Queue Implementations:**
1. **LinkedList**: General-purpose FIFO queue
2. **PriorityQueue**: Orders elements by priority
3. **ArrayDeque**: Resizable array implementation
4. **BlockingQueue**: Thread-safe queue for producer-consumer

**When to Use Queue:**
- Task scheduling
- Resource sharing between threads
- Breadth-first search in graphs
- Print job spooling
- Handling asynchronous requests

**PriorityQueue Theory:**
- Elements ordered by natural ordering or Comparator
- Head is smallest element (min-heap by default)
- Not thread-safe
- Does not allow null
- O(log n) for insertion and removal
- O(1) for peek

**Min-Heap vs Max-Heap:**
- Default: Min-heap (smallest element at head)
- Max-heap: Use `Collections.reverseOrder()` as comparator

```java
import java.util.*;

public class QueueDemo {
    public static void main(String[] args) {
        // Queue using LinkedList
        Queue<String> queue = new LinkedList<>();
        
        // Add elements
        queue.offer("First");  // Preferred
        queue.add("Second");   // Can throw exception
        queue.offer("Third");
        
        // Peek elements
        String peek = queue.peek();  // Returns null if empty
        String element = queue.element(); // Throws exception if empty
        
        // Remove elements (FIFO)
        String removed = queue.poll();   // Returns null if empty
        String remove = queue.remove();  // Throws exception if empty
        
        // PriorityQueue (min-heap by default)
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        pq.offer(30);
        pq.offer(10);
        pq.offer(20);
        
        System.out.println(pq.poll()); // 10 (smallest element)
        
        // Max-heap
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.offer(30);
        maxHeap.offer(10);
        maxHeap.offer(20);
        
        System.out.println(maxHeap.poll()); // 30 (largest element)
    }
}
```

### Deque Interface

**Theory:**
Double Ended Queue - linear collection that supports element insertion and removal at both ends.

**Characteristics:**
1. **Both Ends**: Can add/remove from both front and rear
2. **Can be FIFO or LIFO**: Work as queue or stack
3. **More Flexible**: More operations than Queue
4. **Not Thread-Safe**: ArrayDeque and LinkedList implementations

**Deque as Stack:**
- push() = addFirst()
- pop() = removeFirst()
- peek() = peekFirst()

**Deque as Queue:**
- offer() = offerLast()
- poll() = pollFirst()
- peek() = peekFirst()

**Methods:**

| Operation | First Element | Last Element |
|-----------|---------------|---------------|
| **Insert** | addFirst(), offerFirst() | addLast(), offerLast() |
| **Remove** | removeFirst(), pollFirst() | removeLast(), pollLast() |
| **Examine** | getFirst(), peekFirst() | getLast(), peekLast() |

**ArrayDeque vs LinkedList:**

| Feature | ArrayDeque | LinkedList |
|---------|------------|------------|
| **Implementation** | Resizable array | Doubly linked list |
| **Null** | Not allowed | Allowed |
| **Performance** | Faster (cache-friendly) | Slower |
| **Memory** | Less | More (node pointers) |
| **Use as Stack/Queue** | Preferred | Acceptable |

**When to Use Deque:**
- Need stack (prefer over Stack class)
- Need queue with both-end access
- Implementing undo/redo functionality
- Palindrome checking
- Sliding window problems

```java
import java.util.*;

public class DequeDemo {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();
        
        // Add elements
        deque.addFirst("First");
        deque.addLast("Last");
        deque.offerFirst("New First");
        deque.offerLast("New Last");
        
        // Remove elements
        String first = deque.removeFirst();
        String last = deque.removeLast();
        
        // Peek elements
        String peekFirst = deque.peekFirst();
        String peekLast = deque.peekLast();
        
        // Can be used as Stack
        deque.push("Element");  // Push to front
        String pop = deque.pop(); // Pop from front
    }
}
```

### Map Interface

**Theory:**
Object that maps keys to values. Cannot contain duplicate keys; each key maps to at most one value.

**Characteristics:**
1. **Key-Value Pairs**: Stores entries as key-value pairs
2. **No Duplicate Keys**: Keys must be unique
3. **Duplicate Values**: Values can be duplicate
4. **Not Part of Collection**: Separate hierarchy
5. **Entry Interface**: Each key-value pair is Map.Entry object

**Common Methods:**
- `put(key, value)`: Add/update entry
- `get(key)`: Get value for key
- `remove(key)`: Remove entry
- `containsKey(key)`, `containsValue(value)`
- `keySet()`: Set of all keys
- `values()`: Collection of all values
- `entrySet()`: Set of all entries (Map.Entry)
- `size()`, `isEmpty()`, `clear()`

**Map Implementations Comparison:**

| Feature | HashMap | LinkedHashMap | TreeMap | Hashtable |
|---------|---------|---------------|---------|------------|
| **Order** | No order | Insertion order | Sorted | No order |
| **Null Key** | One null | One null | No null | No null |
| **Null Value** | Multiple | Multiple | Multiple | No null |
| **Thread-Safe** | No | No | No | Yes |
| **Performance** | O(1) | O(1) | O(log n) | O(1) |
| **Since** | Java 1.2 | Java 1.4 | Java 1.2 | JDK 1.0 |
| **Use** | General purpose | Maintain order | Sorted keys | Legacy/thread-safe |

#### HashMap

**Theory:**
Hash table-based implementation of Map interface. Most commonly used Map implementation.

**How it Works:**
1. **Hashing**: Uses hashCode() of key to determine bucket
2. **Buckets**: Internal array of linked lists (or trees in Java 8+)
3. **Bucket Index**: `index = hashCode() % array length`
4. **Collision Handling**: Multiple entries in same bucket (linked list or tree)
5. **Load Factor**: Threshold for resizing (default 0.75)
6. **Capacity**: Size of internal array (default 16)
7. **Rehashing**: When size > capacity * load factor, array doubles

**Java 8+ Improvement:**
- When bucket has > 8 entries, converts to balanced tree (Red-Black tree)
- Improves worst-case performance from O(n) to O(log n)

**Characteristics:**
1. **No Order**: No guaranteed iteration order
2. **One Null Key**: Allows one null key
3. **Multiple Null Values**: Allows multiple null values
4. **Fast Operations**: O(1) average case
5. **Not Synchronized**: Not thread-safe

**Important:**
- Override hashCode() and equals() for custom key objects
- Good hashCode() distributes keys evenly across buckets
- Poor hashCode() can degrade performance

**Time Complexity:**
- Best/Average Case: O(1) for put, get, remove
- Worst Case: O(n) - O(log n) in Java 8+

**When to Use:**
- Most common use case
- Need fast operations
- Order doesn't matter
- Single-threaded environment

```java
import java.util.*;

public class HashMapDemo {
    public static void main(String[] args) {
        HashMap<String, Integer> map = new HashMap<>();
        
        // Put key-value pairs
        map.put("John", 25);
        map.put("Alice", 30);
        map.put("Bob", 28);
        
        // Get value
        int age = map.get("John"); // 25
        
        // Check if key exists
        boolean hasKey = map.containsKey("Alice");
        
        // Check if value exists
        boolean hasValue = map.containsValue(30);
        
        // Remove entry
        map.remove("Bob");
        
        // Size
        int size = map.size();
        
        // Iterate using entrySet
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
        
        // Iterate using keySet
        for (String key : map.keySet()) {
            System.out.println(key + ": " + map.get(key));
        }
        
        // Iterate using values
        for (Integer value : map.values()) {
            System.out.println(value);
        }
        
        // Get or default
        int value = map.getOrDefault("Unknown", 0);
        
        // Put if absent
        map.putIfAbsent("Charlie", 35);
        
        // Compute
        map.compute("John", (k, v) -> v + 1); // Increase age by 1
        
        // Merge
        map.merge("John", 1, (oldVal, newVal) -> oldVal + newVal);
    }
}
```

#### LinkedHashMap

**Theory:**
Hash table and linked list implementation of Map. Maintains insertion order.

**How it Works:**
- Extends HashMap
- Additionally maintains doubly-linked list across all entries
- Each entry has link to next and previous entry
- Slightly slower than HashMap due to linked list maintenance

**Characteristics:**
1. **Insertion Order**: Maintains order of insertion
2. **Access Order**: Optional - can maintain access order (LRU cache)
3. **Performance**: Slightly slower than HashMap
4. **Predictable Iteration**: Iteration in insertion order

**Constructor Options:**
```java
LinkedHashMap()                          // Insertion order
LinkedHashMap(int capacity)              // Insertion order
LinkedHashMap(int capacity, float load)  // Insertion order
LinkedHashMap(int capacity, float load, boolean accessOrder)  // Access order
```

**Access Order Mode:**
- When `accessOrder = true`
- Accessing entry moves it to end
- Useful for implementing LRU (Least Recently Used) cache

**When to Use:**
- Need fast operations + insertion order
- Implementing LRU cache (with access order)
- Predictable iteration order needed

```java
import java.util.*;

public class LinkedHashMapDemo {
    public static void main(String[] args) {
        // Maintains insertion order
        LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
        
        map.put("One", 1);
        map.put("Two", 2);
        map.put("Three", 3);
        
        // Iterates in insertion order
        for (String key : map.keySet()) {
            System.out.println(key + ": " + map.get(key));
        }
    }
}
```

#### TreeMap

**Theory:**
Red-Black tree-based NavigableMap implementation. Keys stored in sorted order.

**How it Works:**
- Implements NavigableMap interface
- Uses Red-Black tree (self-balancing BST)
- Keys sorted by natural ordering or Comparator
- Maintains sorted order always

**Characteristics:**
1. **Sorted Keys**: Always maintains sorted order
2. **No Null Key**: Does not allow null key (NullPointerException)
3. **Null Values**: Allows null values
4. **Slower**: O(log n) for operations
5. **NavigableMap**: Rich navigation methods

**Time Complexity:**
- put, get, remove, containsKey: O(log n)
- Iteration: O(n)

**Sorting:**
1. **Natural Ordering**: Keys must implement Comparable
2. **Custom Comparator**: Provide Comparator in constructor

**Additional Methods:**
- `firstKey()`, `lastKey()`
- `lowerKey(k)`, `higherKey(k)`
- `floorKey(k)`, `ceilingKey(k)`
- `subMap(from, to)`
- `headMap(toKey)`, `tailMap(fromKey)`

**When to Use:**
- Need sorted keys
- Need range operations on keys
- Need navigation methods
- Can accept O(log n) performance

**When NOT to Use:**
- Need faster operations (use HashMap)
- Keys cannot be compared
- Need null keys

```java
import java.util.*;

public class TreeMapDemo {
    public static void main(String[] args) {
        // Sorted map (keys in natural order)
        TreeMap<String, Integer> map = new TreeMap<>();
        
        map.put("Charlie", 30);
        map.put("Alice", 25);
        map.put("Bob", 28);
        
        // Iterates in sorted order of keys
        for (String key : map.keySet()) {
            System.out.println(key + ": " + map.get(key));
        }
        
        // Additional methods
        String firstKey = map.firstKey();
        String lastKey = map.lastKey();
        String lowerKey = map.lowerKey("Bob");
        String higherKey = map.higherKey("Bob");
        
        // SubMap
        SortedMap<String, Integer> subMap = map.subMap("Alice", "Charlie");
    }
}
```

#### Hashtable

**Theory:**
Legacy synchronized implementation of Map interface (since JDK 1.0).

**Characteristics:**
1. **Synchronized**: All methods synchronized (thread-safe)
2. **Slow**: Due to synchronization overhead
3. **No Null**: Doesn't allow null key or null value
4. **Legacy**: Part of original Java (before Collections Framework)
5. **Dictionary Subclass**: Extends Dictionary class

**HashMap vs Hashtable:**

| Feature | HashMap | Hashtable |
|---------|---------|------------|
| **Synchronized** | No (faster) | Yes (slower) |
| **Null Key** | One null allowed | Not allowed |
| **Null Value** | Multiple nulls | Not allowed |
| **Legacy** | No (Java 1.2+) | Yes (JDK 1.0) |
| **Iterator** | fail-fast | fail-fast (Enumerator is fail-safe) |
| **Performance** | Faster | Slower |
| **Recommended** | Yes (with external sync if needed) | No (legacy) |

**Better Alternatives:**
- HashMap with external synchronization
- Collections.synchronizedMap(new HashMap<>())
- ConcurrentHashMap for concurrent access

**When to Use:**
- Working with legacy code
- Thread safety required (though better alternatives exist)

**Why Hashtable is Obsolete:**
- Synchronizes every operation (overhead even in single-threaded)
- Better alternatives available (ConcurrentHashMap)
- Doesn't allow null (less flexible)
- Part of legacy API

```java
import java.util.*;

public class HashtableDemo {
    public static void main(String[] args) {
        // Legacy, synchronized (thread-safe)
        Hashtable<String, Integer> table = new Hashtable<>();
        
        table.put("Key1", 100);
        table.put("Key2", 200);
        
        // Does NOT allow null keys or values
        // table.put(null, 300); // NullPointerException
        
        /*
        HashMap vs Hashtable:
        - HashMap is not synchronized, Hashtable is synchronized
        - HashMap allows one null key and multiple null values
        - Hashtable does NOT allow null keys or values
        - HashMap is faster
        */
    }
}
```

### Collections Utility Class

**Theory:**
Utility class that provides static methods for operating on collections. All methods are static.

**Categories of Methods:**

**1. Sorting and Searching:**
- `sort()`: Sort list in natural or custom order
- `binarySearch()`: Search element in sorted list
- `reverse()`: Reverse order of elements
- `shuffle()`: Randomly permutes elements

**2. Finding Extremes:**
- `max()`: Maximum element
- `min()`: Minimum element
- `frequency()`: Count occurrences

**3. Modification:**
- `fill()`: Replace all elements with specified value
- `copy()`: Copy one list to another
- `swap()`: Swap elements at two positions
- `replaceAll()`: Replace all occurrences

**4. Synchronization Wrappers:**
- `synchronizedList()`: Thread-safe list
- `synchronizedSet()`: Thread-safe set
- `synchronizedMap()`: Thread-safe map
- Returns synchronized view backed by original collection

**5. Unmodifiable Wrappers:**
- `unmodifiableList()`: Read-only list
- `unmodifiableSet()`: Read-only set
- `unmodifiableMap()`: Read-only map
- Attempts to modify throw UnsupportedOperationException

**6. Empty Collections:**
- `emptyList()`, `emptySet()`, `emptyMap()`
- Returns immutable empty collection
- No memory allocation

**7. Singleton Collections:**
- `singleton()`: Immutable set with one element
- `singletonList()`: Immutable list with one element
- `singletonMap()`: Immutable map with one entry

**8. Other Utilities:**
- `addAll()`: Add elements to collection
- `rotate()`: Rotate elements
- `nCopies()`: Immutable list with n copies
- `disjoint()`: Check if two collections have no common elements

**Important Notes:**
- Synchronized wrappers: Must sychronize on returned collection when iterating
- Unmodifiable views: Modifications to original collection reflect in view
- Empty collections are immutable and singleton instances

```java
import java.util.*;

public class CollectionsUtility {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(Arrays.asList(5, 2, 8, 1, 9));
        
        // Sort
        Collections.sort(list); // Ascending order
        Collections.sort(list, Collections.reverseOrder()); // Descending
        
        // Reverse
        Collections.reverse(list);
        
        // Shuffle
        Collections.shuffle(list);
        
        // Binary search (list must be sorted)
        int index = Collections.binarySearch(list, 8);
        
        // Max and Min
        int max = Collections.max(list);
        int min = Collections.min(list);
        
        // Frequency
        int freq = Collections.frequency(list, 5);
        
        // Fill
        Collections.fill(list, 0);
        
        // Copy
        List<Integer> dest = new ArrayList<>(Collections.nCopies(list.size(), 0));
        Collections.copy(dest, list);
        
        // Unmodifiable collection
        List<Integer> unmodifiable = Collections.unmodifiableList(list);
        
        // Synchronized collection
        List<Integer> syncList = Collections.synchronizedList(list);
        
        // Empty collections
        List<String> emptyList = Collections.emptyList();
        Set<String> emptySet = Collections.emptySet();
        Map<String, String> emptyMap = Collections.emptyMap();
    }
}
```

### Comparable vs Comparator

**Theory:**
Both interfaces used for sorting objects. Different purposes and use cases.

**Comparable Interface:**
- **Package**: java.lang
- **Method**: `int compareTo(T o)`
- **Purpose**: Define natural ordering of objects
- **Location**: Implemented by class itself
- **Sorting**: Single sorting sequence
- **Modification**: Must modify class
- **Usage**: When class has natural ordering (e.g., integers, strings)

**Comparator Interface:**
- **Package**: java.util
- **Method**: `int compare(T o1, T o2)`
- **Purpose**: Define custom ordering
- **Location**: Separate class or lambda
- **Sorting**: Multiple sorting sequences possible
- **Modification**: No need to modify original class
- **Usage**: When you need different sorting criteria

**Comparison:**

| Feature | Comparable | Comparator |
|---------|------------|-------------|
| **Package** | java.lang | java.util |
| **Method** | compareTo(T o) | compare(T o1, T o2) |
| **Implementation** | By class itself | Separate class |
| **Modify Class** | Yes | No |
| **Sorting Orders** | One (natural) | Multiple (custom) |
| **Collections.sort()** | sort(list) | sort(list, comparator) |
| **Use Case** | Default ordering | Custom ordering |

**Return Values:**
- Negative: this/first object is less
- Zero: objects are equal
- Positive: this/first object is greater

**When to Use:**

**Comparable:**
- Class has obvious natural ordering
- You control the class source code
- Single sorting order sufficient
- Example: Roll number of students

**Comparator:**
- Need multiple sorting criteria (name, age, salary)
- Cannot modify class (third-party library)
- Different sorting in different contexts
- Example: Sort students by name, age, or grade

**Java 8+ Comparator Methods:**
- `comparing()`: Create comparator from function
- `thenComparing()`: Chain multiple comparisons
- `reversed()`: Reverse order
- `naturalOrder()`, `reverseOrder()`: Natural ordering
- `nullsFirst()`, `nullsLast()`: Handle nulls

```java
import java.util.*;

// Comparable - Natural ordering
class Student implements Comparable<Student> {
    String name;
    int age;
    
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public int compareTo(Student other) {
        return this.age - other.age; // Sort by age
    }
    
    @Override
    public String toString() {
        return name + "(" + age + ")";
    }
}

// Comparator - Custom ordering
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name); // Sort by name
    }
}

public class ComparableComparatorDemo {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("John", 25));
        students.add(new Student("Alice", 22));
        students.add(new Student("Bob", 28));
        
        // Sort using Comparable (natural ordering)
        Collections.sort(students);
        System.out.println("Sorted by age: " + students);
        
        // Sort using Comparator
        Collections.sort(students, new NameComparator());
        System.out.println("Sorted by name: " + students);
        
        // Lambda expression (Java 8+)
        Collections.sort(students, (s1, s2) -> s1.name.compareTo(s2.name));
        
        // Comparator methods (Java 8+)
        students.sort(Comparator.comparing(s -> s.name));
        students.sort(Comparator.comparing(Student::getName));
        students.sort(Comparator.comparingInt(Student::getAge));
        
        // Reverse order
        students.sort(Comparator.comparing(Student::getName).reversed());
        
        // Multiple criteria
        students.sort(Comparator.comparing(Student::getAge)
                               .thenComparing(Student::getName));
    }
}
```

---

## Multithreading & Concurrency

**Theory:**
Multithreading is a Java feature allowing concurrent execution of two or more parts of a program for maximum CPU utilization.

**Key Concepts:**

**Process:**
- Instance of a program in execution
- Has its own memory space
- Heavy-weight
- Inter-process communication is expensive

**Thread:**
- Lightweight subprocess
- Smallest unit of processing
- Shares same memory space within process
- Multiple threads in same process
- Less overhead than processes

**Multitasking vs Multithreading:**

| Feature | Multitasking | Multithreading |
|---------|--------------|----------------|
| **Definition** | Multiple processes | Multiple threads |
| **Unit** | Process | Thread |
| **Memory** | Separate | Shared |
| **Overhead** | More | Less |
| **Context Switching** | Expensive | Cheaper |
| **Communication** | Difficult (IPC) | Easy (shared memory) |

**Advantages of Multithreading:**
1. **Better CPU Utilization**: Multiple tasks execute concurrently
2. **Reduced Idle Time**: While one thread waits, others execute
3. **Responsiveness**: UI remains responsive during long operations
4. **Resource Sharing**: Threads share process resources
5. **Economy**: Creating threads is cheaper than processes
6. **Scalability**: Utilize multi-core processors effectively

**Thread States (Life Cycle):**
```
New → Runnable → Running → Terminated
            ↑           ↓
            ←─── Blocked/Waiting/Timed Waiting
```

1. **New**: Thread created but not started
2. **Runnable**: Ready to run, waiting for CPU
3. **Running**: Thread executing
4. **Blocked/Waiting**: Waiting for resource or notification
5. **Timed Waiting**: Sleeping for specified time
6. **Terminated**: Thread completed execution

**Thread Scheduler:**
- Part of JVM
- Decides which thread runs when
- Uses thread priorities
- Not guaranteed (depends on OS)

**Thread Priority:**
- Range: 1 (MIN_PRIORITY) to 10 (MAX_PRIORITY)
- Default: 5 (NORM_PRIORITY)
- Higher priority threads preferred (not guaranteed)

### Creating Threads

**Theory:**
Two ways to create threads in Java:

**1. Extending Thread Class:**
- Create subclass of Thread
- Override run() method
- Call start() to begin execution
- Cannot extend another class (single inheritance)

**2. Implementing Runnable Interface:**
- Implement Runnable interface
- Implement run() method
- Create Thread object with Runnable
- Call start() on Thread object
- Can extend other classes (preferred)

**Why Implementing Runnable is Better:**
1. Follows OOP principles (composition over inheritance)
2. Can extend other classes
3. Multiple threads can share same Runnable instance
4. Separates task from thread mechanism
5. Can use with Executor framework

**Important Methods:**
- `start()`: Starts thread execution (creates new call stack)
- `run()`: Contains thread's code (don't call directly)
- `sleep()`: Pauses thread for specified time
- `join()`: Wait for thread to complete
- `yield()`: Hint to scheduler to give chance to other threads
- `interrupt()`: Interrupt thread

**start() vs run():**
- `start()`: Creates new thread, calls run() internally
- `run()`: Executes in same thread, no parallel execution

#### Method 1: Extending Thread Class

```java
class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
            try {
                Thread.sleep(500); // Sleep for 500ms
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class ThreadDemo1 {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        
        t1.start(); // Start thread
        t2.start();
        
        // t1.run(); // Wrong! This calls method directly, doesn't create thread
    }
}
```

#### Method 2: Implementing Runnable Interface

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class ThreadDemo2 {
    public static void main(String[] args) {
        MyRunnable runnable = new MyRunnable();
        Thread t1 = new Thread(runnable);
        Thread t2 = new Thread(runnable);
        
        t1.start();
        t2.start();
        
        // Using lambda (Java 8+)
        Thread t3 = new Thread(() -> {
            System.out.println("Lambda thread");
        });
        t3.start();
    }
}
```

### Thread Methods

```java
public class ThreadMethods {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("Thread running");
        });
        
        // Set thread name
        t.setName("MyThread");
        String name = t.getName();
        
        // Set priority (1-10, default 5)
        t.setPriority(Thread.MAX_PRIORITY); // 10
        int priority = t.getPriority();
        
        // Start thread
        t.start();
        
        // Check if alive
        boolean alive = t.isAlive();
        
        // Wait for thread to complete
        t.join();
        
        // Current thread
        Thread current = Thread.currentThread();
        
        // Sleep
        Thread.sleep(1000); // Sleep for 1 second
        
        // Yield (hint to scheduler)
        Thread.yield();
        
        // Daemon thread (background thread)
        Thread daemon = new Thread(() -> {
            while (true) {
                System.out.println("Daemon running");
            }
        });
        daemon.setDaemon(true); // Must be set before start()
        daemon.start();
    }
}
```

### Thread Synchronization

**Theory:
**Synchronization is the capability to control access to shared resources by multiple threads.

**Why Synchronization?**
- Multiple threads accessing shared resource simultaneously
- **Race Condition**: Multiple threads try to modify shared data concurrently
- Can lead to data inconsistency
- Example: Two threads incrementing same counter

**Problems Without Synchronization:**
1. **Thread Interference**: Threads interfere with each other
2. **Memory Consistency Errors**: Different threads have inconsistent views of data
3. **Data Corruption**: Partial updates visible to other threads

**Synchronization Mechanisms:**

**1. Synchronized Method:**
- Entire method is synchronized
- Lock acquired on object (instance method) or class (static method)
- Only one thread can execute the method at a time
- Simple but can be inefficient (locks entire method)

**2. Synchronized Block:**
- Only specific code block synchronized
- More granular control
- Better performance
- Can synchronize on any object

**3. Static Synchronization:**
- Synchronizes on class object
- Multiple objects won't interfere with static data
- Lock acquired on Class object

**How Synchronization Works:**
1. Thread acquires lock (monitor) on object
2. Executes synchronized code
3. Releases lock when done
4. Other threads wait for lock

**Lock (Monitor):**
- Every object has intrinsic lock/monitor
- Only one thread can hold lock at a time
- Other threads blocked until lock released

**Important Points:**
- Synchronized keyword ensures visibility and atomicity
- Performance overhead (context switching, waiting)
- Can lead to deadlock if not careful
- Constructor cannot be synchronized
- Lock is reentrant (same thread can acquire again)

**When to Use:**
- Multiple threads access shared mutable state
- Need to ensure data consistency
- Critical section must execute atomically

#### Synchronized Method

```java
class Counter {
    private int count = 0;
    
    // Synchronized method
    public synchronized void increment() {
        count++;
    }
    
    public int getCount() {
        return count;
    }
}

public class SynchronizedDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.getCount()); // 2000
    }
}
```

#### Synchronized Block

```java
class Counter2 {
    private int count = 0;
    private Object lock = new Object();
    
    public void increment() {
        // Synchronized block
        synchronized (lock) {
            count++;
        }
    }
    
    public int getCount() {
        return count;
    }
}
```

#### Static Synchronization

```java
class StaticCounter {
    private static int count = 0;
    
    // Synchronizes on class object
    public static synchronized void increment() {
        count++;
    }
}
```

### Inter-thread Communication

**Theory:**
Mechanism for synchronized threads to communicate with each other. Solves the problem of threads waiting efficiently.

**Without Communication:**
- Thread continuously checks condition (busy waiting)
- Wastes CPU cycles
- Inefficient

**With Communication:**
- Thread waits and releases lock
- Another thread notifies when condition met
- Efficient CPU usage

**Methods (in Object class):**
1. **wait()**: 
   - Releases lock and waits
   - Thread enters waiting state
   - Must be called from synchronized context
   - Can be interrupted
   
2. **notify()**:
   - Wakes up one waiting thread
   - Thread moves to runnable state
   - Must be called from synchronized context
   - Which thread wakes up is not guaranteed
   
3. **notifyAll()**:
   - Wakes up all waiting threads
   - All threads compete for lock
   - Safer than notify() (avoids missed signals)

**Why in Object Class?**
- Every object can be used as lock
- Methods work on object's monitor
- Makes sense for synchronization

**Important Rules:**
1. Must call from synchronized context (IllegalMonitorStateException)
2. Always use in loop (check condition)
3. Prefer notifyAll() over notify() (avoids missed signals)
4. Handle InterruptedException

**Producer-Consumer Problem:**
- Classic multi-threading problem
- Producer produces data
- Consumer consumes data
- Shared buffer between them
- Producer waits if buffer full
- Consumer waits if buffer empty
- Perfect use case for inter-thread communication

**wait() vs sleep():**

| Feature | wait() | sleep() |
|---------|--------|----------|
| **Class** | Object | Thread |
| **Lock** | Releases lock | Holds lock |
| **Wake Up** | notify()/notifyAll() | After time expires |
| **Context** | Must be synchronized | Any context |
| **Purpose** | Inter-thread communication | Pause execution |

```java
class SharedResource {
    private int data;
    private boolean hasData = false;
    
    public synchronized void produce(int value) throws InterruptedException {
        while (hasData) {
            wait(); // Release lock and wait
        }
        this.data = value;
        hasData = true;
        System.out.println("Produced: " + value);
        notify(); // Wake up one waiting thread
    }
    
    public synchronized int consume() throws InterruptedException {
        while (!hasData) {
            wait();
        }
        hasData = false;
        System.out.println("Consumed: " + data);
        notify();
        return data;
    }
}

public class ProducerConsumer {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    resource.produce(i);
                    Thread.sleep(500);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    resource.consume();
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

### Deadlock Example

**Theory:**
Deadlock is a situation where two or more threads are blocked forever, waiting for each other.

**Conditions for Deadlock (All must be true):**
1. **Mutual Exclusion**: At least one resource must be non-shareable
2. **Hold and Wait**: Thread holding resource requests another resource
3. **No Preemption**: Resource cannot be forcibly taken from thread
4. **Circular Wait**: Circular chain of threads waiting for resources

**Example Scenario:**
- Thread 1 locks Resource A, needs Resource B
- Thread 2 locks Resource B, needs Resource A
- Both wait forever

**How to Avoid Deadlock:**
1. **Lock Ordering**: Always acquire locks in same order
2. **Lock Timeout**: Use tryLock() with timeout
3. **Avoid Nested Locks**: Don't acquire multiple locks if possible
4. **Use Thread Join**: Join threads carefully
5. **Use Concurrent Collections**: Built-in deadlock prevention

**Detection:**
- ThreadMXBean can detect deadlocks
- jconsole, jvisualvm tools
- Thread dumps

**Prevention vs Avoidance:**
- **Prevention**: Structure code to make deadlock impossible
- **Avoidance**: Runtime algorithm to avoid deadlock states

```java
public class DeadlockDemo {
    private static Object lock1 = new Object();
    private static Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock1");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                
                System.out.println("Thread 1: Waiting for lock2");
                synchronized (lock2) {
                    System.out.println("Thread 1: Holding lock1 and lock2");
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Holding lock2");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                
                System.out.println("Thread 2: Waiting for lock1");
                synchronized (lock1) {
                    System.out.println("Thread 2: Holding lock1 and lock2");
                }
            }
        });
        
        t1.start();
        t2.start();
    }
}
```

### Executor Framework

**Theory:**
High-level replacement for working with threads directly. Part of java.util.concurrent package.

**Why Executor Framework?**
1. **Thread Pooling**: Reuse threads instead of creating new ones
2. **Simplified Management**: Framework handles thread lifecycle
3. **Better Performance**: Reduced thread creation overhead
4. **Resource Management**: Control number of threads
5. **Task Scheduling**: Built-in support for scheduling
6. **Separation of Concerns**: Task submission separate from execution

**Core Interfaces:**

**1. Executor:**
- Simple interface for executing tasks
- Method: `void execute(Runnable command)`

**2. ExecutorService:**
- Extends Executor
- Manages lifecycle (shutdown, termination)
- Can submit Callable tasks (return values)
- Methods: submit(), shutdown(), shutdownNow(), awaitTermination()

**3. ScheduledExecutorService:**
- Extends ExecutorService
- Schedule tasks for future execution
- Methods: schedule(), scheduleAtFixedRate(), scheduleWithFixedDelay()

**Thread Pool Types (Executors factory methods):**

**1. newFixedThreadPool(n):**
- Fixed number of threads
- Tasks queued if all threads busy
- Threads reused for multiple tasks
- Good for: Known, limited number of tasks

**2. newCachedThreadPool():**
- Creates threads as needed
- Reuses idle threads
- Threads terminated if idle for 60 seconds
- Good for: Many short-lived tasks

**3. newSingleThreadExecutor():**
- Single worker thread
- Tasks executed sequentially
- Good for: Sequential execution required

**4. newScheduledThreadPool(n):**
- For scheduled/periodic tasks
- Fixed number of threads
- Good for: Delayed or periodic tasks

**5. newWorkStealingPool() (Java 8+):**
- Uses ForkJoinPool
- Work-stealing algorithm
- Good for: Recursive tasks

**Shutdown Methods:**
- `shutdown()`: 
  - Initiates orderly shutdown
  - No new tasks accepted
  - Existing tasks complete
  
- `shutdownNow()`:
  - Attempts to stop all tasks
  - Returns list of waiting tasks
  - No guarantee of immediate stop
  
- `awaitTermination(timeout, unit)`:
  - Blocks until all tasks complete or timeout

**Benefits:**
- Reuses threads (better performance)
- Limits resource usage
- Easy task scheduling
- Better than manual thread management
- Graceful shutdown

```java
import java.util.concurrent.*;

public class ExecutorDemo {
    public static void main(String[] args) {
        // Fixed thread pool
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        for (int i = 1; i <= 10; i++) {
            int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " by " + 
                                 Thread.currentThread().getName());
            });
        }
        
        executor.shutdown(); // Stop accepting new tasks
        
        // Cached thread pool (creates threads as needed)
        ExecutorService cachedExecutor = Executors.newCachedThreadPool();
        
        // Single thread executor
        ExecutorService singleExecutor = Executors.newSingleThreadExecutor();
        
        // Scheduled executor
        ScheduledExecutorService scheduledExecutor = 
            Executors.newScheduledThreadPool(2);
        
        scheduledExecutor.schedule(() -> {
            System.out.println("Executed after 5 seconds");
        }, 5, TimeUnit.SECONDS);
        
        scheduledExecutor.scheduleAtFixedRate(() -> {
            System.out.println("Repeating task");
        }, 0, 2, TimeUnit.SECONDS);
    }
}
```

### Callable and Future

**Theory:**
Enhancements to Runnable that allow tasks to return values and throw checked exceptions.

**Runnable vs Callable:**

| Feature | Runnable | Callable<V> |
|---------|----------|-------------|
| **Method** | run() | call() |
| **Return Value** | void | V (generic type) |
| **Exception** | Cannot throw checked | Can throw Exception |
| **Since** | Java 1.0 | Java 5 |
| **Use** | Simple tasks | Tasks returning results |

**Callable<V>:**
- Functional interface
- Method: `V call() throws Exception`
- Can return result
- Can throw checked exception
- Submit to ExecutorService

**Future<V>:**
- Represents result of asynchronous computation
- Placeholder for result that will be available later
- Returned by ExecutorService.submit()

**Future Methods:**

**1. get():**
- Waits for computation to complete
- Returns result
- Blocks until result available
- Throws ExecutionException if computation threw exception

**2. get(timeout, unit):**
- Waits for at most given time
- Throws TimeoutException if timeout expires
- Useful to avoid indefinite waiting

**3. isDone():**
- Returns true if task completed
- Non-blocking
- Use to check before calling get()

**4. cancel(mayInterruptIfRunning):**
- Attempts to cancel task
- If not started, won't run
- If running, may or may not stop
- Returns false if cannot cancel

**5. isCancelled():**
- Returns true if task was cancelled

**Use Cases:**
1. **Asynchronous Computation**: Get result later
2. **Parallel Processing**: Submit multiple tasks, collect results
3. **Timeout Handling**: Limit execution time
4. **Exception Handling**: Catch exceptions from worker threads

**CompletableFuture (Java 8+):**
- Enhanced Future
- Non-blocking operations
- Chaining multiple operations
- Combining multiple futures
- Better than Future for complex scenarios

```java
import java.util.concurrent.*;

public class CallableFutureDemo {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        // Callable returns a value
        Callable<Integer> task = () -> {
            Thread.sleep(2000);
            return 42;
        };
        
        // Future holds the result
        Future<Integer> future = executor.submit(task);
        
        System.out.println("Task submitted");
        
        // Check if done
        boolean done = future.isDone();
        
        // Get result (blocks until complete)
        Integer result = future.get();
        System.out.println("Result: " + result);
        
        // Get with timeout
        try {
            result = future.get(1, TimeUnit.SECONDS);
        } catch (TimeoutException e) {
            System.out.println("Timeout");
        }
        
        // Cancel task
        future.cancel(true);
        
        executor.shutdown();
    }
}
```

### Thread-safe Collections

**Theory:**
Collections designed for concurrent access by multiple threads without external synchronization.

**Why Thread-safe Collections?**
- Regular collections not thread-safe
- Manual synchronization error-prone and tedious
- Performance issues with synchronized wrappers
- Better alternatives in java.util.concurrent

**Types:**

**1. ConcurrentHashMap:**
- Thread-safe HashMap
- Better than Hashtable and synchronized HashMap
- **Lock Striping**: Divides map into segments
- Multiple threads can access different segments
- No locking for read operations
- High concurrency
- Doesn't allow null key or value
- **When to use**: High concurrency map operations

**2. CopyOnWriteArrayList:**
- Thread-safe ArrayList
- **Copy-on-Write**: Creates copy on modification
- Read operations don't need synchronization
- Expensive write operations
- Iterator won't throw ConcurrentModificationException
- Snapshot isolation for iterators
- **When to use**: Read-heavy, few writes (listeners, observers)

**3. CopyOnWriteArraySet:**
- Thread-safe Set
- Based on CopyOnWriteArrayList
- Same characteristics as CopyOnWriteArrayList
- **When to use**: Read-heavy set operations

**4. BlockingQueue:**
- Queue with blocking operations
- **put()**: Waits if queue full
- **take()**: Waits if queue empty
- Thread-safe
- Implementations: ArrayBlockingQueue, LinkedBlockingQueue
- **When to use**: Producer-consumer scenarios

**5. ConcurrentLinkedQueue:**
- Thread-safe queue
- Non-blocking (lock-free)
- Based on CAS (Compare-And-Swap)
- Fast for high concurrency
- **When to use**: High-throughput scenarios

**6. ConcurrentSkipListMap:**
- Thread-safe sorted map
- Alternative to TreeMap
- Based on skip list
- **When to use**: Concurrent sorted map

**7. ConcurrentSkipListSet:**
- Thread-safe sorted set
- Based on ConcurrentSkipListMap
- **When to use**: Concurrent sorted set

**Comparison:**

| Collection | Thread-Safe | Performance | Use Case |
|------------|-------------|-------------|----------|
| **Hashtable** | Yes (synchronized) | Slow | Legacy code |
| **Vector** | Yes (synchronized) | Slow | Legacy code |
| **Collections.synchronized()** | Yes | Moderate | Simple cases |
| **ConcurrentHashMap** | Yes (lock striping) | Fast | High concurrency |
| **CopyOnWriteArrayList** | Yes (copy-on-write) | Fast reads | Read-heavy |
| **BlockingQueue** | Yes (blocking) | Good | Producer-consumer |

**Synchronized vs Concurrent Collections:**

| Feature | Synchronized | Concurrent |
|---------|--------------|------------|
| **Locking** | Entire collection | Fine-grained |
| **Performance** | Poor | Better |
| **Iterator** | fail-fast | fail-safe |
| **Null** | Depends | Usually not allowed |
| **Modern** | No | Yes |

```java
import java.util.concurrent.*;

public class ConcurrentCollections {
    public static void main(String[] args) {
        // ConcurrentHashMap (thread-safe, better than Hashtable)
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("Key1", 100);
        
        // CopyOnWriteArrayList (thread-safe, good for read-heavy operations)
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("Item1");
        
        // BlockingQueue
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(10);
        try {
            queue.put("Element"); // Blocks if full
            String element = queue.take(); // Blocks if empty
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        // ConcurrentLinkedQueue
        ConcurrentLinkedQueue<String> concurrentQueue = new ConcurrentLinkedQueue<>();
    }
}
```

---

## Java 8+ Features

**Theory:**
Java 8 (March 2014) was a major release introducing functional programming features.

**Major Features:**
1. **Lambda Expressions**: Functional programming
2. **Functional Interfaces**: Single abstract method interfaces
3. **Stream API**: Process collections functionally
4. **Optional Class**: Better null handling
5. **Default Methods**: Methods with implementation in interfaces
6. **Method References**: Shorthand for lambdas
7. **Date/Time API**: New java.time package
8. **Nashorn JavaScript Engine**
9. **CompletableFuture**: Asynchronous programming
10. **forEach() method**: Iterate with lambda

**Why These Features?**
- **Less Code**: More concise and readable
- **Functional Style**: Declarative, not imperative
- **Better Readability**: Code expresses intent clearly
- **Parallel Processing**: Easy parallelization with streams
- **Immutability**: Encourages immutable operations

### Lambda Expressions

**Theory:**
Lambda expression is an anonymous function (function without name) that can be passed around.

**What is Lambda?**
- Short block of code taking parameters and returning value
- Similar to methods but doesn't need name
- Can be passed as argument to methods
- Provides implementation of functional interface

**Syntax:**
```
(parameters) -> expression
(parameters) -> { statements; }
```

**Components:**
1. **Parameter List**: Can be empty, one, or multiple parameters
2. **Arrow Token** (->): Separates parameters from body
3. **Body**: Expression or block of statements

**Rules:**
1. **Type Inference**: Parameter types optional (compiler infers)
2. **Parentheses**: Optional for single parameter without type
3. **Braces**: Optional for single expression
4. **return**: Optional for single expression

**Benefits:**
1. **Reduced Boilerplate**: No more anonymous class verbosity
2. **Functional Programming**: Treat code as data
3. **Enables Stream API**: Essential for stream operations
4. **Better Readability**: More concise code
5. **Encourages Immutability**: Stateless operations

**When to Use:**
- Implementing functional interfaces
- Event listeners
- Collection operations (forEach, map, filter)
- Stream operations
- Callbacks

**Limitations:**
1. Can only implement functional interfaces
2. Cannot return multiple values directly
3. Cannot throw checked exceptions easily
4. Debugging can be harder

**Closure:**
- Lambda can access variables from outer scope
- Variables must be "effectively final"
- Cannot modify non-final variables

```java
import java.util.*;

public class LambdaDemo {
    public static void main(String[] args) {
        // Old way (Anonymous class)
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello");
            }
        };
        
        // Lambda expression
        Runnable r2 = () -> System.out.println("Hello");
        
        // With parameters
        Comparator<Integer> comp1 = (a, b) -> a - b;
        
        // With multiple statements
        Comparator<Integer> comp2 = (a, b) -> {
            System.out.println("Comparing");
            return a - b;
        };
        
        // Lambda with list
        List<String> list = Arrays.asList("Apple", "Banana", "Cherry");
        
        // forEach with lambda
        list.forEach(item -> System.out.println(item));
        
        // Method reference
        list.forEach(System.out::println);
    }
}
```

### Functional Interfaces

**Theory:**
Interface with exactly one abstract method. Can have multiple default or static methods.

**KEY RULE:** Only ONE abstract method is required. You can have unlimited default and static methods!

**Characteristics:**
1. **Single Abstract Method (SAM)**: Only one abstract method (REQUIRED)
2. **@FunctionalInterface**: Optional annotation (compile-time check)
3. **Can have default methods**: Any number - unlimited (they have implementation)
4. **Can have static methods**: Any number - unlimited (they have implementation)
5. **Can have methods from Object class**: equals(), hashCode(), toString()
6. **Target Type for Lambda**: Lambdas implement functional interfaces

**Example of Valid Functional Interface:**
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
    
    static void displayVersion() {
        System.out.println("Version 1.0");
    }
}
```

**Why Functional Interfaces?**
- Enable lambda expressions
- Functional programming in Java
- Used extensively in Stream API
- Represent behavior as data

**@FunctionalInterface Annotation:**
- Optional but recommended
- Compiler ensures exactly one abstract method
- Prevents accidental addition of abstract methods
- Self-documenting code
- Compilation error if more than one abstract method added

**Built-in Functional Interfaces (java.util.function):**

**1. Predicate<T>:**
- **Purpose**: Test condition, return boolean
- **Method**: `boolean test(T t)`
- **Use**: Filtering, conditional logic
- **Example**: `Predicate<Integer> isEven = n -> n % 2 == 0`

**2. Consumer<T>:**
- **Purpose**: Accept input, no return (side effect)
- **Method**: `void accept(T t)`
- **Use**: Printing, logging, updating
- **Example**: `Consumer<String> printer = s -> System.out.println(s)`

**3. Supplier<T>:**
- **Purpose**: Supply value, no input
- **Method**: `T get()`
- **Use**: Lazy initialization, factory methods
- **Example**: `Supplier<Double> random = () -> Math.random()`

**4. Function<T, R>:**
- **Purpose**: Transform T to R
- **Method**: `R apply(T t)`
- **Use**: Mapping, conversion
- **Example**: `Function<String, Integer> length = s -> s.length()`

**5. BiFunction<T, U, R>:**
- **Purpose**: Two inputs, one output
- **Method**: `R apply(T t, U u)`
- **Use**: Operations on two values
- **Example**: `BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b`

**6. UnaryOperator<T>:**
- **Purpose**: T to T transformation
- **Extends**: Function<T, T>
- **Method**: `T apply(T t)`
- **Use**: Transform same type
- **Example**: `UnaryOperator<Integer> square = n -> n * n`

**7. BinaryOperator<T>:**
- **Purpose**: Two T inputs, one T output
- **Extends**: BiFunction<T, T, T>
- **Method**: `T apply(T t1, T t2)`
- **Use**: Reduce operations
- **Example**: `BinaryOperator<Integer> multiply = (a, b) -> a * b`

**Other Functional Interfaces:**
- **BiPredicate<T, U>**: Two inputs, boolean result
- **BiConsumer<T, U>**: Two inputs, no return
- **ToIntFunction<T>**, **ToDoubleFunction<T>**: Specialized conversions
- **IntPredicate**, **IntConsumer**, etc.: Primitive specializations

**Primitive Specializations:**
- Avoid autoboxing overhead
- IntPredicate, LongPredicate, DoublePredicate
- IntFunction, LongFunction, DoubleFunction
- IntConsumer, LongConsumer, DoubleConsumer
- IntSupplier, LongSupplier, DoubleSupplier
- IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator
- IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator

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

### Custom Functional Interface

**Example showing ONE abstract method with MULTIPLE default and static methods:**

```java
@FunctionalInterface
interface Calculator {
    // ONLY ONE abstract method - this is what lambda implements
    int calculate(int a, int b);
    
    // Can have MULTIPLE default methods
    default void printResult(int result) {
        System.out.println("Result: " + result);
    }
    
    default void printOperation(String operation) {
        System.out.println("Operation: " + operation);
    }
    
    default int multiplyBy2(int num) {
        return num * 2;
    }
    
    // Can have MULTIPLE static methods
    static void displayInfo() {
        System.out.println("This is a calculator");
    }
    
    static void displayVersion() {
        System.out.println("Version 1.0");
    }
}

public class CustomFunctionalInterface {
    public static void main(String[] args) {
        // Lambda only implements the abstract method
        Calculator add = (a, b) -> a + b;
        Calculator subtract = (a, b) -> a - b;
        
        // Using abstract method
        int sum = add.calculate(10, 5);
        System.out.println(sum);  // 15
        
        // Using default methods
        add.printResult(sum);  // Result: 15
        add.printOperation("Addition");  // Operation: Addition
        
        // Using static methods
        Calculator.displayInfo();     // This is a calculator
        Calculator.displayVersion();  // Version 1.0
        
        System.out.println(subtract.calculate(10, 5)); // 5
    }
}

/*
KEY POINTS:
1. Only ONE abstract method (calculate) - this is what lambda implements
2. Multiple default methods are allowed (printResult, printOperation, multiplyBy2)
3. Multiple static methods are allowed (displayInfo, displayVersion)
4. @FunctionalInterface ensures only one abstract method
*/
```

**Invalid Example - NOT a Functional Interface:**

```java
@FunctionalInterface  // ❌ COMPILATION ERROR
interface InvalidCalculator {
    int calculate(int a, int b);  // First abstract method
    int compute(int x, int y);    // Second abstract method - NOT ALLOWED!
    
    // This will cause compilation error:
    // "InvalidCalculator is not a functional interface
    //  multiple non-overriding abstract methods found"
}

/*
WHY IT FAILS:
- Has TWO abstract methods (calculate and compute)
- Functional interface can have ONLY ONE abstract method
- @FunctionalInterface annotation catches this at compile time
*/
```

### Stream API

**Theory:**
Stream is a sequence of elements supporting sequential and parallel aggregate operations.

**What is Stream?**
- **Not a data structure**: Doesn't store elements
- **Pipeline of operations**: Transforms data
- **Lazy evaluation**: Operations executed only when terminal operation invoked
- **Possibly infinite**: Can represent infinite sequences
- **Consumable**: Can be used only once

**Stream vs Collection:**

| Feature | Collection | Stream |
|---------|------------|--------|
| **Purpose** | Store data | Process data |
| **Iteration** | External (for loop) | Internal (forEach) |
| **Reusability** | Reusable | One-time use |
| **Modification** | Can modify | Cannot modify source |
| **Eagerness** | Eager | Lazy |
| **Parallelization** | Manual | Easy (parallelStream()) |

**Stream Pipeline:**
```
Source → Intermediate Operations → Terminal Operation
```

1. **Source**: Collection, array, generator function
2. **Intermediate Operations**: Transform stream (lazy)
   - Return stream
   - filter(), map(), sorted(), distinct(), limit(), skip()
3. **Terminal Operations**: Produce result (eager)
   - Trigger pipeline execution
   - forEach(), collect(), reduce(), count(), anyMatch(), findFirst()

**Intermediate Operations (Lazy):**

**1. filter(Predicate):**
- Select elements matching condition
- Returns stream of matching elements

**2. map(Function):**
- Transform each element
- One-to-one mapping

**3. flatMap(Function):**
- Transform and flatten
- One-to-many mapping
- Useful for nested structures

**4. distinct():**
- Remove duplicates
- Uses equals() for comparison

**5. sorted():**
- Sort elements
- Natural order or custom comparator

**6. peek(Consumer):**
- Perform action without changing elements
- Useful for debugging

**7. limit(n):**
- Truncate stream to n elements
- Useful for infinite streams

**8. skip(n):**
- Skip first n elements

**Terminal Operations (Eager):**

**1. forEach(Consumer):**
- Perform action on each element
- Returns void

**2. collect(Collector):**
- Accumulate elements into collection
- Most versatile terminal operation
- Collectors: toList(), toSet(), toMap(), joining(), groupingBy(), etc.

**3. reduce(BinaryOperator):**
- Combine elements to single result
- Sum, product, concatenation

**4. count():**
- Count elements
- Returns long

**5. anyMatch(), allMatch(), noneMatch():**
- Check if any/all/none elements match predicate
- Short-circuiting

**6. findFirst(), findAny():**
- Find first or any element
- Returns Optional
- Short-circuiting

**7. min(), max():**
- Find minimum or maximum
- Requires Comparator
- Returns Optional

**8. toArray():**
- Convert to array

**Short-circuiting Operations:**
- Don't need to process all elements
- anyMatch(), allMatch(), noneMatch()
- findFirst(), findAny()
- limit()

**Parallel Streams:**
- `parallelStream()` or `stream().parallel()`
- Utilizes multiple CPU cores
- Good for large datasets
- Not always faster (overhead of thread management)
- Use when:
  - Large amount of data
  - Computationally expensive operations
  - Independent operations (no shared state)

**Benefits:**
1. **Declarative**: What, not how
2. **Composable**: Chain operations
3. **Parallelizable**: Easy parallel processing
4. **Lazy**: Efficient (processes only when needed)
5. **Clean Code**: More readable

**Best Practices:**
1. Prefer stream operations over loops
2. Use method references when possible
3. Avoid side effects in stream operations
4. Use parallel streams judiciously
5. Close streams that use I/O resources

```java
import java.util.*;
import java.util.stream.*;

public class StreamDemo {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // Filter
        List<Integer> evens = numbers.stream()
                                     .filter(n -> n % 2 == 0)
                                     .collect(Collectors.toList());
        
        // Map
        List<Integer> squares = numbers.stream()
                                       .map(n -> n * n)
                                       .collect(Collectors.toList());
        
        // Filter and Map
        List<Integer> result = numbers.stream()
                                      .filter(n -> n % 2 == 0)
                                      .map(n -> n * n)
                                      .collect(Collectors.toList());
        
        // forEach
        numbers.stream()
               .filter(n -> n % 2 == 0)
               .forEach(System.out::println);
        
        // Reduce
        int sum = numbers.stream()
                        .reduce(0, (a, b) -> a + b);
        
        Optional<Integer> max = numbers.stream()
                                       .reduce((a, b) -> a > b ? a : b);
        
        // Count
        long count = numbers.stream()
                           .filter(n -> n > 5)
                           .count();
        
        // Sorted
        List<Integer> sorted = numbers.stream()
                                      .sorted()
                                      .collect(Collectors.toList());
        
        List<Integer> sortedDesc = numbers.stream()
                                          .sorted(Comparator.reverseOrder())
                                          .collect(Collectors.toList());
        
        // Distinct
        List<Integer> distinct = Arrays.asList(1, 2, 2, 3, 3, 4).stream()
                                      .distinct()
                                      .collect(Collectors.toList());
        
        // Limit and Skip
        List<Integer> limited = numbers.stream()
                                       .limit(5)
                                       .collect(Collectors.toList());
        
        List<Integer> skipped = numbers.stream()
                                       .skip(5)
                                       .collect(Collectors.toList());
        
        // anyMatch, allMatch, noneMatch
        boolean anyEven = numbers.stream().anyMatch(n -> n % 2 == 0);
        boolean allPositive = numbers.stream().allMatch(n -> n > 0);
        boolean noneNegative = numbers.stream().noneMatch(n -> n < 0);
        
        // findFirst, findAny
        Optional<Integer> first = numbers.stream()
                                         .filter(n -> n > 5)
                                         .findFirst();
        
        // min, max
        Optional<Integer> min = numbers.stream().min(Integer::compareTo);
        Optional<Integer> maximum = numbers.stream().max(Integer::compareTo);
        
        // toArray
        Integer[] array = numbers.stream().toArray(Integer[]::new);
        
        // Collectors
        String joined = numbers.stream()
                              .map(String::valueOf)
                              .collect(Collectors.joining(", "));
        
        // groupingBy
        List<String> words = Arrays.asList("apple", "banana", "apricot", "cherry");
        Map<Character, List<String>> grouped = words.stream()
            .collect(Collectors.groupingBy(s -> s.charAt(0)));
        
        // partitioningBy
        Map<Boolean, List<Integer>> partitioned = numbers.stream()
            .collect(Collectors.partitioningBy(n -> n % 2 == 0));
        
        // Stream from array
        int[] arr = {1, 2, 3, 4, 5};
        IntStream intStream = Arrays.stream(arr);
        
        // Stream from range
        IntStream range = IntStream.range(1, 10);      // 1 to 9
        IntStream rangeClosed = IntStream.rangeClosed(1, 10); // 1 to 10
        
        // Parallel stream
        int parallelSum = numbers.parallelStream()
                                .reduce(0, Integer::sum);
    }
}
```

### Optional Class

**Theory:**
Container object that may or may not contain a value. Better approach to handling null values.

**Why Optional?**
- **Avoid NullPointerException**: Explicit handling of absence
- **Self-documenting**: Method signature indicates possible absence
- **Functional Style**: Chain operations safely
- **Forces Null Handling**: Cannot ignore null possibility

**What is Optional?**
- Container with at most one value
- Can be empty or contain non-null value
- Cannot contain null (throws NullPointerException)
- Immutable

**Creating Optional:**

**1. Optional.of(value):**
- Value must be non-null
- Throws NullPointerException if null
- Use when you're sure value is not null

**2. Optional.ofNullable(value):**
- Value can be null
- Returns empty Optional if null
- Safest way to create Optional

**3. Optional.empty():**
- Returns empty Optional
- Use to represent absence

**Checking Value:**

**1. isPresent():**
- Returns true if value present
- Often paired with get()
- Not the best way (defeats purpose)

**2. isEmpty() (Java 11+):**
- Returns true if empty
- Opposite of isPresent()

**Getting Value:**

**1. get():**
- Returns value
- Throws NoSuchElementException if empty
- Avoid using directly (defeats purpose)

**2. orElse(defaultValue):**
- Returns value or default if empty
- Default always evaluated (even if value present)

**3. orElseGet(Supplier):**
- Returns value or result of supplier if empty
- Supplier evaluated only if empty (lazy)
- Prefer over orElse() for expensive operations

**4. orElseThrow():**
- Returns value or throws exception
- Can provide custom exception
- Makes absence explicit

**Transforming Value:**

**1. map(Function):**
- Transform value if present
- Returns Optional of transformed value
- Returns empty if original is empty

**2. flatMap(Function):**
- Transform value to another Optional
- Avoids Optional<Optional<T>>
- Use when transformation returns Optional

**3. filter(Predicate):**
- Keep value if matches predicate
- Returns empty if doesn't match
- Returns empty if original is empty

**Consuming Value:**

**1. ifPresent(Consumer):**
- Execute consumer if value present
- Do nothing if empty
- Replacement for if(isPresent())

**2. ifPresentOrElse(Consumer, Runnable) (Java 9+):**
- Execute consumer if present, runnable if empty
- Both branches handled

**Common Mistakes:**

**1. Using get() without checking:**
```java
// Bad
String value = optional.get();

// Good
String value = optional.orElse("default");
```

**2. Using isPresent() + get():**
```java
// Bad
if (optional.isPresent()) {
    return optional.get();
}

// Good
return optional.orElse(defaultValue);
```

**3. Using Optional as field:**
```java
// Bad (not serializable, overhead)
private Optional<String> name;

// Good
private String name; // Can be null
```

**Best Practices:**
1. **Return Optional from methods** that may not have value
2. **Don't use for fields or parameters** (just use null)
3. **Use orElse/orElseGet** instead of isPresent() + get()
4. **Never call get() directly** without checking
5. **Use map/flatMap** for transformations
6. **Don't create Optional<Collection>**: Return empty collection
7. **Use Stream of Optional**: Stream<Optional<T>>

**When to Use:**
- Method return types when absence is valid
- API design to indicate optional values
- Chaining operations on potentially absent values

**When NOT to Use:**
- Class fields (serialization issues, overhead)
- Method parameters (just use null)
- Collections (return empty collection instead)
- Performance-critical code (overhead)

```java
import java.util.*;

public class OptionalDemo {
    public static void main(String[] args) {
        // Creating Optional
        Optional<String> optional1 = Optional.of("Hello");
        Optional<String> optional2 = Optional.ofNullable(null);
        Optional<String> optional3 = Optional.empty();
        
        // Check if present
        if (optional1.isPresent()) {
            System.out.println(optional1.get());
        }
        
        // ifPresent with lambda
        optional1.ifPresent(value -> System.out.println(value));
        
        // orElse (return default if empty)
        String value1 = optional2.orElse("Default");
        
        // orElseGet (lazy evaluation)
        String value2 = optional2.orElseGet(() -> "Default");
        
        // orElseThrow
        try {
            String value3 = optional2.orElseThrow(() -> 
                new Exception("Value not present"));
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
        
        // map
        Optional<Integer> length = optional1.map(String::length);
        
        // filter
        Optional<String> filtered = optional1.filter(s -> s.length() > 3);
        
        // flatMap
        Optional<String> result = optional1.flatMap(s -> Optional.of(s.toUpperCase()));
    }
}
```

### Method References

**Theory:**
Shorthand notation for calling a method using lambda expression. Makes code more concise and readable.

**Syntax:**
```
ClassName::methodName
```

**When to Use:**
- Lambda just calls an existing method
- Makes code more concise
- Improves readability

**Types of Method References:**

**1. Static Method Reference:**
- **Syntax**: `ClassName::staticMethod`
- **Lambda**: `(args) -> ClassName.staticMethod(args)`
- **Example**: `Integer::parseInt`
- **Use**: Calling static methods

**2. Instance Method on Particular Object:**
- **Syntax**: `object::instanceMethod`
- **Lambda**: `(args) -> object.instanceMethod(args)`
- **Example**: `System.out::println`
- **Use**: Calling method on specific object

**3. Instance Method on Arbitrary Object of Class:**
- **Syntax**: `ClassName::instanceMethod`
- **Lambda**: `(obj, args) -> obj.instanceMethod(args)`
- **Example**: `String::toUpperCase`
- **Use**: Calling instance method where first parameter becomes target

**4. Constructor Reference:**
- **Syntax**: `ClassName::new`
- **Lambda**: `(args) -> new ClassName(args)`
- **Example**: `ArrayList::new`
- **Use**: Creating objects

**Benefits:**
1. **More Concise**: Less code than lambda
2. **More Readable**: Intent clear from method name
3. **Reusability**: Reuse existing methods
4. **Performance**: Potentially better (JVM optimization)

**Limitations:**
- Can only reference methods, not create new logic
- Method signature must match functional interface
- Less flexible than lambdas

**Method Reference vs Lambda:**

| Scenario | Lambda | Method Reference |
|----------|--------|------------------|
| Calls existing method | `x -> System.out.println(x)` | `System.out::println` |
| Additional logic | `x -> System.out.println(x + 1)` | Cannot use |
| Multiple statements | `x -> { ... }` | Cannot use |
| Simple method call | Verbose | Concise |

**Best Practices:**
1. Use method references when lambda just calls method
2. Use lambda when additional logic needed
3. Prefer method references for readability
4. Don't force method references if lambda is clearer

```java
import java.util.*;

public class MethodReferenceDemo {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("Apple", "Banana", "Cherry");
        
        // 1. Static method reference
        list.forEach(System.out::println);
        // Equivalent to: list.forEach(s -> System.out.println(s));
        
        // 2. Instance method reference on object
        String prefix = "Item: ";
        list.forEach(prefix::concat);
        
        // 3. Instance method reference on class
        list.stream()
            .map(String::toUpperCase)
            .forEach(System.out::println);
        // Equivalent to: .map(s -> s.toUpperCase())
        
        // 4. Constructor reference
        List<Integer> lengths = list.stream()
                                   .map(String::new) // Copy constructor
                                   .map(String::length)
                                   .collect(ArrayList::new, 
                                          ArrayList::add, 
                                          ArrayList::addAll);
    }
    
    public static void printMessage(String msg) {
        System.out.println(msg);
    }
}
```

### Default and Static Methods in Interface (Java 8)

**Theory:**
Java 8 allows interfaces to have method implementations.

**Default Methods:**
- Methods with implementation in interface
- Use `default` keyword
- Implementing class inherits default implementation
- Can be overridden by implementing class

**Why Default Methods?**
1. **Backward Compatibility**: Add methods to interface without breaking implementations
2. **Multiple Inheritance of Behavior**: Class can inherit multiple default implementations
3. **Optional Methods**: Provide default behavior
4. **Evolution of APIs**: Extend interfaces without breaking existing code

**Rules for Default Methods:**
1. Must use `default` keyword
2. Must have body (implementation)
3. Cannot be abstract, static, or final
4. Can be overridden by implementing class
5. Class implementation takes precedence

**Diamond Problem:**
- Class implements two interfaces with same default method
- **Compilation Error**: Must override method in class
- Class explicitly chooses which implementation

**Static Methods in Interface:**
- Methods that belong to interface, not instance
- Use `static` keyword
- Cannot be overridden
- Called using interface name: `Interface.method()`

**Why Static Methods in Interface?**
1. **Utility Methods**: Helper methods related to interface
2. **Factory Methods**: Create instances
3. **Namespace**: Group related methods
4. **Alternative to Utility Classes**: No need for separate utility class

**Default vs Static Methods:**

| Feature | Default | Static |
|---------|---------|--------|
| **Keyword** | default | static |
| **Override** | Can be overridden | Cannot be overridden |
| **Access** | Through instance | Through interface name |
| **Inheritance** | Inherited | Not inherited |
| **this** | Can use this | Cannot use this |

**Benefits:**
1. **Interface Evolution**: Add methods without breaking code
2. **Multiple Inheritance**: Inherit behavior from multiple interfaces
3. **Utility Methods**: No need for separate utility classes
4. **Default Implementations**: Reduce boilerplate in implementing classes

**Example Use Cases:**
- **Collection.forEach()**: Added to Iterable interface
- **List.sort()**: Added to List interface
- **Comparator methods**: comparing(), thenComparing()

```java
interface Vehicle {
    // Abstract method
    void start();
    
    // Default method
    default void stop() {
        System.out.println("Vehicle stopped");
    }
    
    // Static method
    static void clean() {
        System.out.println("Cleaning vehicle");
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
        System.out.println("Car stopped");
    }
}

public class DefaultStaticInterface {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();
        car.stop();
        
        // Call static method
        Vehicle.clean();
    }
}
```

### Date/Time API (Java 8)

**Theory:**
New comprehensive date and time API in java.time package. Replacement for old java.util.Date and Calendar.

**Why New API?**

**Problems with Old API:**
1. **Mutable**: Date and Calendar are mutable (thread-unsafe)
2. **Poor Design**: Confusing month numbering (0-11)
3. **Difficult to Use**: Complex calculations
4. **Not Thread-Safe**: Need external synchronization
5. **Timezone Issues**: Poor timezone handling
6. **Year starts from 1900**: Confusing
7. **No Type Safety**: Date can represent both date and time

**New API Benefits:**
1. **Immutable**: All classes immutable and thread-safe
2. **Clear API**: Intuitive methods
3. **Type Safety**: Separate classes for date, time, datetime
4. **Better Timezone**: ZonedDateTime for timezones
5. **Period and Duration**: Clear time-based and date-based amounts
6. **Fluent API**: Method chaining
7. **ISO-8601**: Based on ISO calendar system

**Main Classes:**

**1. LocalDate:**
- **Purpose**: Date without time or timezone
- **Format**: 2024-12-25
- **Use**: Birthdate, holiday
- **Example**: `LocalDate.now()`, `LocalDate.of(2024, 12, 25)`

**2. LocalTime:**
- **Purpose**: Time without date or timezone
- **Format**: 10:30:00
- **Use**: Time of day
- **Example**: `LocalTime.now()`, `LocalTime.of(10, 30)`

**3. LocalDateTime:**
- **Purpose**: Date and time without timezone
- **Format**: 2024-12-25T10:30:00
- **Use**: Event timestamp (local)
- **Example**: `LocalDateTime.now()`, `LocalDateTime.of(date, time)`

**4. ZonedDateTime:**
- **Purpose**: Date, time, and timezone
- **Format**: 2024-12-25T10:30:00+05:30[Asia/Kolkata]
- **Use**: Store timestamps with timezone
- **Example**: `ZonedDateTime.now()`, `ZonedDateTime.now(ZoneId.of("UTC"))`

**5. Instant:**
- **Purpose**: Point on timeline (Unix timestamp)
- **Format**: 2024-12-25T10:30:00Z
- **Use**: Timestamps, logging
- **Example**: `Instant.now()`, `Instant.ofEpochMilli(millis)`

**6. Period:**
- **Purpose**: Date-based amount (years, months, days)
- **Format**: P2Y3M10D (2 years, 3 months, 10 days)
- **Use**: Age, duration between dates
- **Example**: `Period.between(date1, date2)`

**7. Duration:**
- **Purpose**: Time-based amount (hours, minutes, seconds)
- **Format**: PT5H30M (5 hours, 30 minutes)
- **Use**: Duration between times
- **Example**: `Duration.between(time1, time2)`

**8. DateTimeFormatter:**
- **Purpose**: Format and parse date/time
- **Predefined**: ISO_LOCAL_DATE, ISO_LOCAL_TIME, etc.
- **Custom**: ofPattern("dd-MM-yyyy")
- **Thread-Safe**: Unlike SimpleDateFormat

**Common Operations:**

**1. Current Date/Time:**
```java
LocalDate.now()
LocalTime.now()
LocalDateTime.now()
ZonedDateTime.now()
```

**2. Specific Date/Time:**
```java
LocalDate.of(2024, 12, 25)
LocalTime.of(10, 30, 0)
LocalDateTime.of(2024, 12, 25, 10, 30)
```

**3. Parsing:**
```java
LocalDate.parse("2024-12-25")
LocalDate.parse("25-12-2024", DateTimeFormatter.ofPattern("dd-MM-yyyy"))
```

**4. Formatting:**
```java
date.format(DateTimeFormatter.ISO_DATE)
date.format(DateTimeFormatter.ofPattern("dd/MM/yyyy"))
```

**5. Manipulation:**
```java
date.plusDays(7)
date.minusMonths(2)
date.withYear(2025)
```

**6. Comparison:**
```java
date1.isBefore(date2)
date1.isAfter(date2)
date1.isEqual(date2)
```

**7. Extracting:**
```java
date.getYear()
date.getMonth()
date.getDayOfMonth()
date.getDayOfWeek()
```

**LocalDate vs LocalDateTime vs ZonedDateTime:**

| Class | Date | Time | Timezone | Use Case |
|-------|------|------|----------|----------|
| **LocalDate** | Yes | No | No | Birthdate, holiday |
| **LocalTime** | No | Yes | No | Business hours |
| **LocalDateTime** | Yes | Yes | No | Meeting time (local) |
| **ZonedDateTime** | Yes | Yes | Yes | International events |
| **Instant** | Yes | Yes | UTC | Timestamp, logging |

**Period vs Duration:**

| Feature | Period | Duration |
|---------|--------|----------|
| **Type** | Date-based | Time-based |
| **Units** | Years, months, days | Hours, minutes, seconds |
| **Use With** | LocalDate | LocalTime, LocalDateTime |
| **Example** | "2 years 3 months" | "5 hours 30 minutes" |
| **Precision** | Days | Nanoseconds |

**Best Practices:**
1. Use LocalDate for dates without timezone
2. Use ZonedDateTime for dates with timezone
3. Use Instant for UTC timestamps
4. Use Period for date-based durations
5. Use Duration for time-based durations
6. Don't use java.util.Date (legacy)
7. Format/parse using DateTimeFormatter
8. Store timestamps in UTC (Instant or ZonedDateTime)

```java
import java.time.*;
import java.time.format.*;

public class DateTimeDemo {
    public static void main(String[] args) {
        // LocalDate
        LocalDate today = LocalDate.now();
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        int year = date.getYear();
        Month month = date.getMonth();
        int day = date.getDayOfMonth();
        DayOfWeek dayOfWeek = date.getDayOfWeek();
        
        // LocalTime
        LocalTime now = LocalTime.now();
        LocalTime time = LocalTime.of(14, 30, 0);
        
        int hour = time.getHour();
        int minute = time.getMinute();
        
        // LocalDateTime
        LocalDateTime dateTime = LocalDateTime.now();
        LocalDateTime custom = LocalDateTime.of(2024, 12, 25, 10, 30);
        
        // ZonedDateTime
        ZonedDateTime zonedDateTime = ZonedDateTime.now();
        ZonedDateTime nyTime = ZonedDateTime.now(ZoneId.of("America/New_York"));
        
        // Period (date-based)
        Period period = Period.between(LocalDate.of(2020, 1, 1), LocalDate.now());
        int years = period.getYears();
        int months = period.getMonths();
        
        // Duration (time-based)
        Duration duration = Duration.between(LocalTime.of(9, 0), LocalTime.now());
        long hours = duration.toHours();
        
        // Formatting
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss");
        String formatted = dateTime.format(formatter);
        
        // Parsing
        LocalDate parsed = LocalDate.parse("25-12-2024", 
                           DateTimeFormatter.ofPattern("dd-MM-yyyy"));
        
        // Manipulating dates
        LocalDate tomorrow = today.plusDays(1);
        LocalDate nextWeek = today.plusWeeks(1);
        LocalDate nextMonth = today.plusMonths(1);
        LocalDate nextYear = today.plusYears(1);
        
        LocalDate yesterday = today.minusDays(1);
        
        // Instant (timestamp)
        Instant instant = Instant.now();
        long epochSecond = instant.getEpochSecond();
    }
}
```

---

## Generics

**Theory:**
Generics enable types (classes and interfaces) to be parameters when defining classes, interfaces, and methods.

**Why Generics?**

**Before Generics (Java 1.4):**
```java
List list = new ArrayList();
list.add("Hello");
String s = (String) list.get(0); // Cast required
list.add(123); // Compiles but causes runtime error
```

**With Generics (Java 5+):**
```java
List<String> list = new ArrayList<>();
list.add("Hello");
String s = list.get(0); // No cast needed
// list.add(123); // Compile-time error
```

**Benefits:**
1. **Type Safety**: Compile-time type checking
2. **No Casting**: Eliminates explicit casting
3. **Code Reusability**: Generic algorithms for different types
4. **Compile-time Errors**: Catch errors early
5. **Stronger Type Checks**: Compiler can check types

**Generic Concepts:**

**1. Type Parameter:**
- Placeholder for actual type
- Convention: E (Element), K (Key), V (Value), T (Type), N (Number)

**2. Parameterized Type:**
- Generic type with actual type argument
- Example: `List<String>`, `Map<Integer, String>`

**3. Raw Type:**
- Generic class without type parameter
- Example: `List list` (avoid using)
- For backward compatibility

**4. Type Erasure:**
- Generics exist only at compile time
- Erased to raw type at runtime
- Replaced with Object or bound type
- Why? Backward compatibility with pre-Java 5 code

**Type Parameter Naming Conventions:**
- **E**: Element (used in collections)
- **K**: Key
- **V**: Value
- **N**: Number
- **T**: Type
- **S, U, V**: Additional types

### Generic Class

**Theory:**
Class that works with different types while providing compile-time type safety.

**Syntax:**
```java
class ClassName<T> {
    private T value;
    
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

**Key Points:**
1. **Type Parameter in Class Declaration**: `<T>`
2. **Use Type Parameter**: Like any other type in class
3. **Multiple Type Parameters**: `<K, V>`
4. **Cannot Instantiate**: `new T()` not allowed (type erasure)
5. **Cannot Create Array**: `new T[10]` not allowed

**Benefits:**
- Single class works with any type
- Type safety maintained
- Code reusability
- No need for separate classes

```java
// Generic class
class Box<T> {
    private T value;
    
    public void set(T value) {
        this.value = value;
    }
    
    public T get() {
        return value;
    }
}

public class GenericClassDemo {
    public static void main(String[] args) {
        Box<Integer> intBox = new Box<>();
        intBox.set(10);
        Integer value = intBox.get();
        
        Box<String> strBox = new Box<>();
        strBox.set("Hello");
        String str = strBox.get();
    }
}
```

### Generic Method

**Theory:**
Method that introduces its own type parameters. Can be static or non-static.

**Syntax:**
```java
<T> ReturnType methodName(T parameter) {
    // Method body
}
```

**Key Points:**
1. **Type Parameter Before Return Type**: `<T> void method(T param)`
2. **Scope**: Type parameter scoped to method
3. **Different from Class Parameter**: Can have its own type parameter
4. **Can be Static**: Unlike non-static class methods
5. **Type Inference**: Compiler infers type from arguments

**Benefits:**
- Generic behavior without generic class
- Static methods can be generic
- More flexible than class-level generics
- Type safety for specific methods

**Type Inference:**
```java
// Explicit type
ClassName.<String>method("Hello");

// Inferred (preferred)
ClassName.method("Hello");
```

```java
public class GenericMethodDemo {
    // Generic method
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.println(element);
        }
    }
    
    // Multiple type parameters
    public static <K, V> void printKeyValue(K key, V value) {
        System.out.println(key + ": " + value);
    }
    
    public static void main(String[] args) {
        Integer[] intArray = {1, 2, 3, 4, 5};
        String[] strArray = {"A", "B", "C"};
        
        printArray(intArray);
        printArray(strArray);
        
        printKeyValue("Name", "John");
        printKeyValue(1, "One");
    }
}
```

### Bounded Type Parameters

**Theory:**
Restrict types that can be used as type arguments. Specify upper or lower bound for type parameter.

**Upper Bound (extends):**
- **Syntax**: `<T extends UpperBound>`
- **Meaning**: T must be UpperBound or its subtype
- **Multiple Bounds**: `<T extends Class & Interface1 & Interface2>`
- **Use**: When you need to call specific methods on T

**Rules:**
1. Use `extends` for both classes and interfaces
2. Class must come first, then interfaces
3. Only one class bound allowed
4. Multiple interface bounds allowed
5. Cannot use `super` for upper bound

**Why Bounded Types?**
1. **Access Methods**: Call methods of bound type
2. **Restrict Types**: Only compatible types allowed
3. **Type Safety**: Ensure certain capabilities
4. **Polymorphism**: Work with type hierarchy

**Benefits:**
- More control over accepted types
- Can call methods of bound type
- Better type safety
- Document constraints

```java
// Upper bound (extends)
class NumberBox<T extends Number> {
    private T value;
    
    public void set(T value) {
        this.value = value;
    }
    
    public double doubleValue() {
        return value.doubleValue();
    }
}

public class BoundedTypeDemo {
    // Upper bound method
    public static <T extends Comparable<T>> T findMax(T a, T b) {
        return a.compareTo(b) > 0 ? a : b;
    }
    
    public static void main(String[] args) {
        NumberBox<Integer> intBox = new NumberBox<>();
        NumberBox<Double> doubleBox = new NumberBox<>();
        
        // NumberBox<String> strBox = new NumberBox<>(); // Error! String is not Number
        
        Integer max = findMax(10, 20);
        String maxStr = findMax("Apple", "Banana");
    }
}
```

### Wildcards

**Theory:**
Wildcard represents unknown type in generics. Used when you don't care about exact type.

**Why Wildcards?**
- Generic code is too restrictive
- Need to work with family of types
- Read from or write to generic types
- More flexible than specific type parameters

**Types of Wildcards:**

**1. Unbounded Wildcard (?): **
- **Syntax**: `List<?>`
- **Meaning**: List of unknown type
- **Use**: Read-only operations, don't care about type
- **Example**: `printList(List<?> list)`
- **Can**: Read as Object, check size, null operations
- **Cannot**: Add elements (except null)

**2. Upper Bounded Wildcard (? extends Type):**
- **Syntax**: `List<? extends Number>`
- **Meaning**: List of Number or its subtypes
- **Use**: Read data (producer)
- **Can**: Read as Number
- **Cannot**: Add elements (except null)
- **Why**: Don't know exact type, could be Integer, Double, etc.
- **Think**: "Producer extends"

**3. Lower Bounded Wildcard (? super Type):**
- **Syntax**: `List<? super Integer>`
- **Meaning**: List of Integer or its supertype
- **Use**: Write data (consumer)
- **Can**: Add Integer objects
- **Cannot**: Read as Integer (only as Object)
- **Why**: Don't know exact supertype
- **Think**: "Consumer super"

**PECS Principle:**
**Producer Extends, Consumer Super**

- **Producer (read/get)**: Use `? extends T`
  - Reading from structure
  - Getting elements out
  - Example: `copy(List<? extends T> source, List<T> dest)`

- **Consumer (write/put)**: Use `? super T`
  - Writing to structure
  - Putting elements in
  - Example: `addAll(Collection<? super T> c)`

**Comparison:**

| Wildcard | Can Read | Can Write | Use Case |
|----------|----------|-----------|----------|
| **?** | As Object | Only null | Don't care about type |
| **? extends T** | As T | Only null | Read data (producer) |
| **? super T** | As Object | T objects | Write data (consumer) |
| **T** | As T | T objects | Read and write |

**When to Use:**

- **Unbounded (?)**: Method doesn't depend on type
- **Upper Bounded (? extends)**: Method reads from collection
- **Lower Bounded (? super)**: Method writes to collection
- **Type Parameter (T)**: Method reads and writes

**Important Rules:**
1. Cannot instantiate with wildcard: `new ArrayList<?>()` ❌
2. Cannot use wildcard in class definition: `class Box<?>` ❌
3. Can use wildcard in method parameter: `void method(List<?> list)` ✅
4. Cannot add (except null) to `? extends`: Type safety
5. Cannot read (except Object) from `? super`: Limited information

**Examples:**

**Upper Bounded (Read):**
```java
// Can read as Number, cannot add
public static double sum(List<? extends Number> list) {
    double sum = 0;
    for (Number n : list) {  // Reading OK
        sum += n.doubleValue();
    }
    // list.add(5); // Compile error
    return sum;
}
```

**Lower Bounded (Write):**
```java
// Can add integers, cannot read as Integer
public static void addIntegers(List<? super Integer> list) {
    list.add(1);  // Writing OK
    list.add(2);
    // Integer i = list.get(0); // Compile error
    Object obj = list.get(0);  // OK
}
```

**Type Erasure:**
- Generics removed at runtime
- Replaced with Object or bound type
- Bridge methods generated for polymorphism
- Cannot use reflection to determine type parameters
- Cannot distinguish `List<String>` from `List<Integer>` at runtime

```java
import java.util.*;

public class WildcardDemo {
    // Unbounded wildcard
    public static void printList(List<?> list) {
        for (Object obj : list) {
            System.out.println(obj);
        }
    }
    
    // Upper bounded wildcard (read-only)
    public static double sumOfList(List<? extends Number> list) {
        double sum = 0.0;
        for (Number num : list) {
            sum += num.doubleValue();
        }
        return sum;
    }
    
    // Lower bounded wildcard (write-only)
    public static void addNumbers(List<? super Integer> list) {
        list.add(1);
        list.add(2);
        list.add(3);
    }
    
    public static void main(String[] args) {
        List<Integer> intList = Arrays.asList(1, 2, 3);
        List<Double> doubleList = Arrays.asList(1.5, 2.5, 3.5);
        List<String> strList = Arrays.asList("A", "B", "C");
        
        printList(intList);
        printList(strList);
        
        double sum1 = sumOfList(intList);
        double sum2 = sumOfList(doubleList);
        
        List<Number> numList = new ArrayList<>();
        addNumbers(numList);
    }
}
```

---

## File I/O

**Theory:**
Java provides comprehensive APIs for file operations. Two main packages:

**1. java.io (Old I/O):**
- Stream-based I/O
- Blocking operations
- Character and byte streams
- Serialization
- Since: JDK 1.0

**2. java.nio (New I/O - Java 7+):**
- Buffer-based I/O
- Non-blocking operations
- Channels and buffers
- File operations (Path, Files)
- More efficient
- Since: Java 1.4 (enhanced in Java 7)

**Stream Hierarchy:**
```
Byte Streams (binary data):
- InputStream / OutputStream
  - FileInputStream / FileOutputStream
  - BufferedInputStream / BufferedOutputStream
  - DataInputStream / DataOutputStream
  - ObjectInputStream / ObjectOutputStream

Character Streams (text data):
- Reader / Writer
  - FileReader / FileWriter
  - BufferedReader / BufferedWriter
  - InputStreamReader / OutputStreamWriter
```

**Byte vs Character Streams:**

| Feature | Byte Streams | Character Streams |
|---------|--------------|-------------------|
| **Data** | Binary (8-bit) | Text (16-bit Unicode) |
| **Classes** | InputStream/OutputStream | Reader/Writer |
| **Use** | Images, audio, video | Text files |
| **Unit** | byte | char |
| **Encoding** | No encoding | Handles encoding |

**Buffered vs Unbuffered:**

| Feature | Unbuffered | Buffered |
|---------|------------|----------|
| **Speed** | Slow | Fast |
| **I/O Calls** | Many (each operation) | Few (batch) |
| **Memory** | Less | More (buffer) |
| **Use** | Small data | Large data |

**When to Use:**
- **Byte Streams**: Binary files (images, videos, serialization)
- **Character Streams**: Text files (reading lines, characters)
- **Buffered**: Always for better performance
- **DataStreams**: Read/write primitive types
- **ObjectStreams**: Serialization

### File Operations

**Theory:**
File class represents file or directory pathname. Used for file manipulation (not reading/writing content).

**Key Points:**
1. **Abstract Pathname**: Doesn't necessarily exist
2. **Platform Independent**: Works across OS
3. **File or Directory**: Can represent both
4. **No Content Operations**: Use streams for content
5. **Metadata Operations**: File properties, exists, delete, etc.

**Common Operations:**
- **Existence**: exists(), isFile(), isDirectory()
- **Creation**: createNewFile(), mkdir(), mkdirs()
- **Deletion**: delete(), deleteOnExit()
- **Properties**: length(), lastModified(), canRead(), canWrite()
- **Naming**: getName(), getPath(), getAbsolutePath()
- **Listing**: list(), listFiles()
- **Rename**: renameTo()

```java
import java.io.*;

public class FileOperations {
    public static void main(String[] args) {
        File file = new File("test.txt");
        
        // Check if exists
        boolean exists = file.exists();
        
        // Create file
        try {
            boolean created = file.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // File properties
        String name = file.getName();
        String path = file.getPath();
        String absolutePath = file.getAbsolutePath();
        boolean isFile = file.isFile();
        boolean isDirectory = file.isDirectory();
        long length = file.length();
        boolean canRead = file.canRead();
        boolean canWrite = file.canWrite();
        
        // Delete file
        boolean deleted = file.delete();
        
        // Directory operations
        File dir = new File("myDir");
        boolean dirCreated = dir.mkdir();  // Create directory
        boolean dirsCreated = dir.mkdirs(); // Create directory with parents
        
        // List files in directory
        File directory = new File(".");
        String[] files = directory.list();
        File[] fileList = directory.listFiles();
        
        // Rename file
        File newFile = new File("newName.txt");
        boolean renamed = file.renameTo(newFile);
    }
}
```

### FileInputStream and FileOutputStream

```java
import java.io.*;

public class FileStreamDemo {
    public static void main(String[] args) {
        // Write to file
        try (FileOutputStream fos = new FileOutputStream("output.txt")) {
            String data = "Hello, World!";
            fos.write(data.getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Read from file
        try (FileInputStream fis = new FileInputStream("output.txt")) {
            int i;
            while ((i = fis.read()) != -1) {
                System.out.print((char) i);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### BufferedReader and BufferedWriter

```java
import java.io.*;

public class BufferedIODemo {
    public static void main(String[] args) {
        // Write to file
        try (BufferedWriter writer = new BufferedWriter(new FileWriter("output.txt"))) {
            writer.write("Line 1");
            writer.newLine();
            writer.write("Line 2");
            writer.newLine();
            writer.write("Line 3");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Read from file
        try (BufferedReader reader = new BufferedReader(new FileReader("output.txt"))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### DataInputStream and DataOutputStream

```java
import java.io.*;

public class DataStreamDemo {
    public static void main(String[] args) {
        // Write primitive data types
        try (DataOutputStream dos = new DataOutputStream(
                new FileOutputStream("data.dat"))) {
            dos.writeInt(100);
            dos.writeDouble(3.14);
            dos.writeBoolean(true);
            dos.writeUTF("Hello");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Read primitive data types
        try (DataInputStream dis = new DataInputStream(
                new FileInputStream("data.dat"))) {
            int num = dis.readInt();
            double d = dis.readDouble();
            boolean bool = dis.readBoolean();
            String str = dis.readUTF();
            
            System.out.println(num + ", " + d + ", " + bool + ", " + str);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Serialization

**Theory:**
Convert object to byte stream for storage or transmission. Byte stream can be reverted back to object.

**What is Serialization?**
- **Serialization**: Object → Byte Stream
- **Deserialization**: Byte Stream → Object
- **Purpose**: Persist object state or send over network

**Why Serialization?**
1. **Persistence**: Save object to file
2. **Communication**: Send object over network
3. **Caching**: Store objects in memory/disk
4. **Deep Copy**: Clone objects
5. **Session Management**: Store session data

**How to Make Class Serializable:**

1. **Implement Serializable Interface**:
   - Marker interface (no methods)
   - Indicates class can be serialized
   - Must implement: `implements Serializable`

2. **Provide serialVersionUID** (recommended):
   ```java
   private static final long serialVersionUID = 1L;
   ```
   - Version control for class
   - If not provided, JVM generates (based on class structure)
   - Helps in version compatibility

**Serialization Process:**
1. Create ObjectOutputStream
2. Call writeObject(object)
3. Close stream
4. Object converted to bytes and written to file/stream

**Deserialization Process:**
1. Create ObjectInputStream
2. Call readObject()
3. Cast to original type
4. Close stream
5. Bytes converted back to object

**Important Points:**

**1. transient Keyword:**
- Fields marked transient are NOT serialized
- Value will be default (null, 0, false) after deserialization
- Use for: passwords, sensitive data, derived fields

**2. static Fields:**
- Static fields NOT serialized (belong to class, not object)
- Value comes from current class definition

**3. Inheritance:**
- If parent class implements Serializable, child is automatically serializable
- If parent doesn't, parent's fields won't be serialized
- Parent must have no-arg constructor

**4. Composition:**
- All fields must be serializable
- If field is not serializable → NotSerializableException
- Make field transient if cannot serialize

**5. serialVersionUID:**
- Class version identifier
- Must match during deserialization
- If different → InvalidClassException
- Explicitly define to control version compatibility

**Best Practices:**
1. **Always define serialVersionUID**: Avoid version mismatch
2. **Use transient for sensitive data**: Don't serialize passwords
3. **Use transient for derived fields**: Recalculate after deserialization
4. **Document serialization format**: For long-term storage
5. **Custom serialization**: Implement writeObject() and readObject() for control
6. **Prefer alternatives**: Consider JSON, XML for human-readable formats

**Custom Serialization:**
```java
private void writeObject(ObjectOutputStream oos) throws IOException {
    oos.defaultWriteObject();  // Default serialization
    // Custom logic
}

private void readObject(ObjectInputStream ois) 
        throws IOException, ClassNotFoundException {
    ois.defaultReadObject();  // Default deserialization
    // Custom logic
}
```

**Externalization (Advanced):**
- Implement Externalizable interface
- Complete control over serialization
- Must implement writeExternal() and readExternal()
- Higher performance (if optimized)
- More complex

**Serialization vs Externalization:**

| Feature | Serializable | Externalizable |
|---------|--------------|----------------|
| **Control** | Automatic | Manual |
| **Performance** | Slower | Faster (if optimized) |
| **Methods** | Optional custom | Must implement |
| **Complexity** | Simple | Complex |
| **Default** | Serializes all non-transient | Nothing by default |

**Security Considerations:**
1. Don't serialize sensitive data (use transient)
2. Validate during deserialization
3. Be careful with untrusted sources
4. Consider signing serialized data
5. Use Custom serialization for sensitive objects

```java
import java.io.*;

// Class must implement Serializable
class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    
    String name;
    int age;
    transient String password; // Not serialized
    
    public Person(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }
    
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + ", password='" + password + "'}";
    }
}

public class SerializationDemo {
    public static void main(String[] args) {
        Person person = new Person("John", 25, "secret123");
        
        // Serialization (write object to file)
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream("person.ser"))) {
            oos.writeObject(person);
            System.out.println("Object serialized");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Deserialization (read object from file)
        try (ObjectInputStream ois = new ObjectInputStream(
                new FileInputStream("person.ser"))) {
            Person deserializedPerson = (Person) ois.readObject();
            System.out.println("Object deserialized: " + deserializedPerson);
            // Note: password will be null (transient)
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

### NIO (New I/O) - Java 7+

```java
import java.nio.file.*;
import java.io.IOException;
import java.util.List;

public class NIODemo {
    public static void main(String[] args) {
        Path path = Paths.get("test.txt");
        
        // Write to file
        try {
            String content = "Hello, NIO!";
            Files.write(path, content.getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Read from file
        try {
            List<String> lines = Files.readAllLines(path);
            lines.forEach(System.out::println);
            
            // Or read all bytes
            byte[] bytes = Files.readAllBytes(path);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Copy file
        try {
            Path source = Paths.get("source.txt");
            Path target = Paths.get("target.txt");
            Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Move/Rename file
        try {
            Path source = Paths.get("old.txt");
            Path target = Paths.get("new.txt");
            Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Delete file
        try {
            Files.delete(path);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Check file properties
        try {
            boolean exists = Files.exists(path);
            boolean isDirectory = Files.isDirectory(path);
            boolean isReadable = Files.isReadable(path);
            long size = Files.size(path);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## Annotations

**Theory:**
Annotations are metadata that provide data about a program. Don't directly affect code execution.

**What are Annotations?**
- Metadata about program elements
- Indicated by @ symbol
- Can be applied to classes, methods, fields, parameters, etc.
- Provide information to compiler, runtime, or tools

**Why Annotations?**
1. **Information for Compiler**: Detect errors, suppress warnings
2. **Compile-time Processing**: Generate code, XML files
3. **Runtime Processing**: Analyze code at runtime (Reflection)
4. **Documentation**: Self-documenting code
5. **Configuration**: Replace XML configuration

**Categories:**

**1. Marker Annotations:**
- No elements/parameters
- Just marking/tagging
- Example: `@Override`, `@Deprecated`

**2. Single-Value Annotations:**
- One element called "value"
- Can omit element name
- Example: `@SuppressWarnings("unchecked")`

**3. Multi-Value Annotations:**
- Multiple elements
- Must specify element names
- Example: `@MyAnnotation(name="John", age=25)`

**Annotation Retention Policies:**

**1. SOURCE:**
- Discarded by compiler
- Not in .class file
- Example: `@Override`, `@SuppressWarnings`
- Use: Compile-time checks only

**2. CLASS (default):**
- Recorded in .class file
- Not available at runtime
- Use: Bytecode processing

**3. RUNTIME:**
- Recorded in .class file
- Available at runtime via reflection
- Most common for custom annotations
- Use: Runtime processing

**Annotation Targets:**
- **TYPE**: Class, interface, enum
- **METHOD**: Methods
- **FIELD**: Fields, enum constants
- **PARAMETER**: Method parameters
- **CONSTRUCTOR**: Constructors
- **LOCAL_VARIABLE**: Local variables
- **PACKAGE**: Packages
- **TYPE_PARAMETER**: Type parameters (Java 8+)
- **TYPE_USE**: Any use of type (Java 8+)

### Built-in Annotations

**Theory:**

**1. @Override:**
- **Target**: Methods
- **Purpose**: Indicates method overrides superclass method
- **Benefit**: Compile-time error if not actually overriding
- **Use**: Always mark override methods

**2. @Deprecated:**
- **Target**: All types, methods, fields
- **Purpose**: Marks element as deprecated (obsolete)
- **Effect**: Compiler warning when used
- **Use**: When replacing with better alternative
- **Best Practice**: Document replacement in Javadoc

**3. @SuppressWarnings:**
- **Target**: All except packages and annotations
- **Purpose**: Suppress compiler warnings
- **Common Values**:
  - "unchecked": Unchecked operations
  - "deprecation": Use of deprecated elements
  - "rawtypes": Raw type usage
  - "unused": Unused code
- **Use**: When warning is understood and intentional
- **Best Practice**: Use narrowest scope possible

**4. @FunctionalInterface:**
- **Target**: Types
- **Purpose**: Indicates interface is functional (SAM)
- **Benefit**: Compile-time error if not functional
- **Use**: Document functional interfaces

**5. @SafeVarargs:**
- **Target**: Methods and constructors
- **Purpose**: Suppress warnings for generic varargs
- **Restriction**: Only for static, final, or private methods
- **Use**: When varargs usage is type-safe

**Meta-Annotations (for defining annotations):**

**1. @Retention:**
- **Purpose**: Specify how long annotation is retained
- **Values**: SOURCE, CLASS, RUNTIME
- **Example**: `@Retention(RetentionPolicy.RUNTIME)`

**2. @Target:**
- **Purpose**: Specify where annotation can be applied
- **Values**: TYPE, METHOD, FIELD, PARAMETER, etc.
- **Example**: `@Target(ElementType.METHOD)`

**3. @Documented:**
- **Purpose**: Include annotation in Javadoc
- **Use**: When annotation should appear in documentation

**4. @Inherited:**
- **Purpose**: Annotation inherited by subclasses
- **Use**: When subclass should inherit annotation

**5. @Repeatable (Java 8+):**
- **Purpose**: Allow same annotation multiple times
- **Example**: `@Schedule` multiple times on method

```java
// @Override
class Parent {
    public void display() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    @Override
    public void display() {
        System.out.println("Child");
    }
}

// @Deprecated
class OldClass {
    @Deprecated
    public void oldMethod() {
        System.out.println("This method is deprecated");
    }
}

// @SuppressWarnings
public class AnnotationDemo {
    @SuppressWarnings("unchecked")
    public static void main(String[] args) {
        OldClass obj = new OldClass();
        obj.oldMethod(); // Warning: method is deprecated
    }
}

// @FunctionalInterface
@FunctionalInterface
interface MyInterface {
    void method(); // Only one abstract method allowed
}

// @SafeVarargs (suppress warnings for generics varargs)
class VarargsExample {
    @SafeVarargs
    public static <T> void printAll(T... items) {
        for (T item : items) {
            System.out.println(item);
        }
    }
}
```

### Custom Annotations

**Theory:**
User-defined annotations for specific needs. Created using @interface keyword.

**Syntax:**
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyAnnotation {
    String value() default "Default";
    int priority() default 0;
}
```

**Components:**

**1. @interface keyword:**
- Declares annotation type
- Special interface type

**2. Elements:**
- Look like methods
- Can have default values
- Cannot have parameters or throws clause
- Return types: primitive, String, Class, enum, annotation, array of these

**3. Default Values:**
- Use `default` keyword
- Optional element if default provided
- Makes annotation usage simpler

**4. Meta-Annotations:**
- @Retention: When available (SOURCE, CLASS, RUNTIME)
- @Target: Where applicable (METHOD, TYPE, FIELD, etc.)
- @Documented: Include in Javadoc
- @Inherited: Inherit to subclasses

**Creating Custom Annotation:**

**Step 1**: Define annotation
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test {
    String name();
    int timeout() default 1000;
}
```

**Step 2**: Use annotation
```java
@Test(name = "testMethod", timeout = 5000)
public void myTest() {
    // Test code
}
```

**Step 3**: Process annotation (Reflection)
```java
Method method = obj.getClass().getMethod("myTest");
if (method.isAnnotationPresent(Test.class)) {
    Test test = method.getAnnotation(Test.class);
    String name = test.name();
    int timeout = test.timeout();
}
```

**Rules for Elements:**
1. **No parameters**: Elements cannot have parameters
2. **No throws**: Cannot declare exceptions
3. **Return types**: Restricted to specific types
4. **Cannot be generic**: No generic types
5. **Default can refer**: To constant or enum

**Best Practices:**
1. **Use @Retention(RUNTIME)**: For runtime processing
2. **Use @Target**: Specify where annotation applies
3. **Provide defaults**: Make annotation easier to use
4. **Document**: Explain purpose and usage
5. **Keep simple**: Don't overload with elements
6. **Consistent naming**: Follow conventions

**Common Use Cases:**
1. **Testing**: @Test, @Before, @After
2. **Validation**: @NotNull, @Size, @Email
3. **ORM**: @Entity, @Table, @Column
4. **Dependency Injection**: @Autowired, @Inject
5. **Web**: @Controller, @RequestMapping
6. **Logging**: @Log, @Timed
7. **Security**: @Secured, @RolesAllowed

**Processing Annotations:**

**Compile-time Processing:**
- Annotation Processor API
- Generate code, validate, create files
- Part of build process

**Runtime Processing:**
- Reflection API
- Read annotations at runtime
- Make decisions based on annotations

```java
import java.lang.annotation.*;

// Define custom annotation
@Retention(RetentionPolicy.RUNTIME) // Available at runtime
@Target(ElementType.METHOD)         // Can be applied to methods
@interface MyAnnotation {
    String value() default "Default";
    int priority() default 0;
}

// Using custom annotation
class AnnotatedClass {
    @MyAnnotation(value = "Important", priority = 1)
    public void annotatedMethod() {
        System.out.println("Annotated method");
    }
}

// Reading annotations using Reflection
public class CustomAnnotationDemo {
    public static void main(String[] args) throws Exception {
        AnnotatedClass obj = new AnnotatedClass();
        
        // Get method
        java.lang.reflect.Method method = obj.getClass()
                                            .getMethod("annotatedMethod");
        
        // Check if annotation present
        if (method.isAnnotationPresent(MyAnnotation.class)) {
            MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);
            System.out.println("Value: " + annotation.value());
            System.out.println("Priority: " + annotation.priority());
        }
    }
}
```

---

## Design Patterns

**Theory:**
Design patterns are reusable solutions to commonly occurring problems in software design.

**What are Design Patterns?**
- Proven solutions to recurring problems
- Best practices codified
- Common vocabulary for developers
- Not code, but templates

**Why Design Patterns?**
1. **Reusability**: Proven solutions
2. **Communication**: Common language
3. **Efficiency**: Don't reinvent wheel
4. **Best Practices**: Industry standards
5. **Maintainability**: Easier to understand and modify

**Types of Design Patterns:**

**1. Creational Patterns:**
- Object creation mechanisms
- Examples: Singleton, Factory, Builder, Prototype, Abstract Factory

**2. Structural Patterns:**
- Composition of classes/objects
- Examples: Adapter, Decorator, Proxy, Facade, Bridge, Composite, Flyweight

**3. Behavioral Patterns:**
- Communication between objects
- Examples: Observer, Strategy, Command, Iterator, State, Template Method, Chain of Responsibility

### Singleton Pattern

**Theory:**
Ensures class has only one instance and provides global point of access.

**When to Use:**
1. **Exactly one instance**: Configuration, logging, caching
2. **Global access**: Database connection, thread pool
3. **Resource sharing**: Print spooler, file system
4. **Lazy initialization**: Create only when needed

**Key Points:**
1. **Private constructor**: Prevent instantiation from outside
2. **Static instance**: Only one instance
3. **Static method**: Provide global access point
4. **Thread safety**: Consider multi-threaded environment

**Implementations:**

**1. Eager Initialization:**
- **Pros**: Simple, thread-safe
- **Cons**: Instance created even if not used
- **Use**: Small objects, always needed

**2. Lazy Initialization:**
- **Pros**: Created only when needed
- **Cons**: Not thread-safe
- **Use**: Single-threaded, expensive objects

**3. Thread-Safe (Synchronized):**
- **Pros**: Thread-safe
- **Cons**: Slow (synchronization overhead)
- **Use**: Multi-threaded, but better approaches exist

**4. Double-Checked Locking:**
- **Pros**: Thread-safe, efficient
- **Cons**: Complex, requires volatile
- **Use**: Multi-threaded, efficiency important

**5. Bill Pugh (Best):**
- **Pros**: Thread-safe, lazy, efficient, simple
- **Cons**: None (uses inner static class)
- **Use**: Most cases (recommended)
- **How**: JVM guarantees thread-safety for class loading

**6. Enum Singleton (Best for Serialization):**
- **Pros**: Thread-safe, serialization-safe, simple
- **Cons**: Not lazy
- **Use**: When serialization is concern

**Problems with Singleton:**
1. **Global state**: Can be misused
2. **Testing**: Hard to mock
3. **Hidden dependencies**: Not obvious from constructor
4. **Serialization**: Special handling needed
5. **Cloning**: Must prevent
6. **Reflection**: Can break singleton

**Breaking Singleton:**
1. **Reflection**: Can call private constructor
2. **Serialization**: Creates new instance
3. **Cloning**: Can create copy

**Prevention:**
1. **Reflection**: Throw exception in constructor if instance exists
2. **Serialization**: Implement readResolve() method
3. **Cloning**: Don't implement Cloneable or override clone() to throw exception

**Best Practice:**
- Use enum singleton for simple cases
- Use Bill Pugh for complex cases
- Avoid if possible (use dependency injection)

```java
// Eager initialization
class EagerSingleton {
    private static final EagerSingleton instance = new EagerSingleton();
    
    private EagerSingleton() {}
    
    public static EagerSingleton getInstance() {
        return instance;
    }
}

// Lazy initialization (not thread-safe)
class LazySingleton {
    private static LazySingleton instance;
    
    private LazySingleton() {}
    
    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}

// Thread-safe singleton (Double-checked locking)
class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() {}
    
    public static ThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (ThreadSafeSingleton.class) {
                if (instance == null) {
                    instance = new ThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}

// Bill Pugh Singleton (best approach)
class BillPughSingleton {
    private BillPughSingleton() {}
    
    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }
    
    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

### Factory Pattern

**Theory:**
Creational pattern that provides interface for creating objects without specifying exact class.

**What is Factory Pattern?**
- Delegates object creation to factory class
- Client doesn't know exact class being instantiated
- Factory decides which class to instantiate
- Follows "Open/Closed Principle"

**When to Use:**
1. **Class cannot anticipate type**: Don't know which class to instantiate
2. **Subclasses decide**: Subclass specifies objects to create
3. **Encapsulate creation logic**: Complex creation logic
4. **Loose coupling**: Client independent of concrete classes

**Components:**
1. **Product Interface**: Common interface for all products
2. **Concrete Products**: Actual implementations
3. **Factory Class**: Creates and returns products
4. **Client**: Uses factory to get products

**Benefits:**
1. **Loose Coupling**: Client doesn't depend on concrete classes
2. **Open/Closed**: Easy to add new products
3. **Single Responsibility**: Creation logic centralized
4. **Flexibility**: Can return different implementations
5. **Code Reusability**: Factory method reused

**Real-World Examples:**
1. **JDBC**: DriverManager.getConnection()
2. **Collections**: Collections.synchronizedList()
3. **Calendar**: Calendar.getInstance()
4. **NumberFormat**: NumberFormat.getInstance()

**vs new operator:**
| Feature | Factory | new |
|---------|---------|-----|
| **Flexibility** | Can return subtype | Only exact type |
| **Coupling** | Loose | Tight |
| **Extension** | Easy | Modify client code |
| **Logic** | Can have creation logic | Direct instantiation |

```java
// Product interface
interface Shape {
    void draw();
}

// Concrete products
class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Circle");
    }
}

class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Rectangle");
    }
}

class Triangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Triangle");
    }
}

// Factory class
class ShapeFactory {
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (shapeType.equalsIgnoreCase("TRIANGLE")) {
            return new Triangle();
        }
        return null;
    }
}

public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory factory = new ShapeFactory();
        
        Shape circle = factory.getShape("CIRCLE");
        circle.draw();
        
        Shape rectangle = factory.getShape("RECTANGLE");
        rectangle.draw();
    }
}
```

### Builder Pattern

**Theory:**
Creational pattern for constructing complex objects step by step. Separates construction from representation.

**What is Builder Pattern?**
- Constructs complex object step by step
- Provides fluent interface (method chaining)
- Separates construction from representation
- Makes immutable objects easier

**When to Use:**
1. **Many parameters**: Constructor has many parameters
2. **Optional parameters**: Some parameters optional
3. **Immutable objects**: Want immutable object with many fields
4. **Step-by-step construction**: Object created in steps
5. **Readable code**: Telescoping constructors unreadable

**Problems Solved:**

**1. Telescoping Constructor:**
```java
// Bad - too many constructors
public Person(String name) { ... }
public Person(String name, int age) { ... }
public Person(String name, int age, String address) { ... }
public Person(String name, int age, String address, String phone) { ... }
```

**2. JavaBeans Pattern:**
```java
// Bad - mutable, inconsistent state possible
Person p = new Person();
p.setName("John");
p.setAge(25);
// What if we forget to set required fields?
```

**3. Constructor with Parameter Object:**
```java
// Bad - parameter object is mutable
PersonBuilder builder = new PersonBuilder();
builder.setName("John");
Person p = new Person(builder);
builder.setName("Alice"); // Can modify
```

**Builder Solution:**
```java
// Good - fluent, immutable, readable
Person p = new Person.PersonBuilder("John", 25)
                     .address("123 St")
                     .phone("555-1234")
                     .build();
```

**Components:**
1. **Product**: Complex object being built
2. **Builder**: Interface for building parts
3. **Concrete Builder**: Implements building steps
4. **Director**: Controls building process (optional)

**Implementation:**

**Static Inner Class (Most Common):**
- Builder as static inner class
- Product constructor private
- Builder has same fields as product
- Setter methods return builder (fluent)
- build() method creates product

**Benefits:**
1. **Immutability**: Create immutable objects easily
2. **Flexibility**: Only set what you need
3. **Readable**: Code is self-documenting
4. **Validation**: Can validate in build() method
5. **Step-by-step**: Build complex objects incrementally

**Real-World Examples:**
1. **StringBuilder**: Builds strings
2. **Stream.Builder**: Builds streams
3. **ProcessBuilder**: Builds processes
4. **Lombok @Builder**: Annotation for builder

**vs Constructor:**
| Feature | Builder | Constructor |
|---------|---------|-------------|
| **Parameters** | Named | Positional |
| **Optional** | Easy | Multiple constructors |
| **Readability** | Excellent | Poor (many params) |
| **Immutability** | Easy | Need many constructors |
| **Flexibility** | High | Low |

**Best Practices:**
1. Make product constructor private
2. Builder fields should match product
3. Required fields in builder constructor
4. Optional fields as setter methods
5. Return this from setters (fluent)
6. Validate in build() method
7. Make product immutable

```java
class Computer {
    // Required parameters
    private String CPU;
    private String RAM;
    
    // Optional parameters
    private boolean hasGraphicsCard;
    private boolean hasWiFi;
    private boolean hasBluetooth;
    
    private Computer(ComputerBuilder builder) {
        this.CPU = builder.CPU;
        this.RAM = builder.RAM;
        this.hasGraphicsCard = builder.hasGraphicsCard;
        this.hasWiFi = builder.hasWiFi;
        this.hasBluetooth = builder.hasBluetooth;
    }
    
    // Builder class
    public static class ComputerBuilder {
        // Required parameters
        private String CPU;
        private String RAM;
        
        // Optional parameters
        private boolean hasGraphicsCard;
        private boolean hasWiFi;
        private boolean hasBluetooth;
        
        public ComputerBuilder(String CPU, String RAM) {
            this.CPU = CPU;
            this.RAM = RAM;
        }
        
        public ComputerBuilder setGraphicsCard(boolean hasGraphicsCard) {
            this.hasGraphicsCard = hasGraphicsCard;
            return this;
        }
        
        public ComputerBuilder setWiFi(boolean hasWiFi) {
            this.hasWiFi = hasWiFi;
            return this;
        }
        
        public ComputerBuilder setBluetooth(boolean hasBluetooth) {
            this.hasBluetooth = hasBluetooth;
            return this;
        }
        
        public Computer build() {
            return new Computer(this);
        }
    }
    
    @Override
    public String toString() {
        return "Computer{CPU='" + CPU + "', RAM='" + RAM + "'}";
    }
}

public class BuilderPatternDemo {
    public static void main(String[] args) {
        Computer computer = new Computer.ComputerBuilder("Intel i7", "16GB")
                                        .setGraphicsCard(true)
                                        .setWiFi(true)
                                        .setBluetooth(true)
                                        .build();
        
        System.out.println(computer);
    }
}
```

### Observer Pattern

**Theory:**
Behavioral pattern where object (subject) maintains list of dependents (observers) and notifies them of state changes.

**What is Observer Pattern?**
- One-to-many dependency
- Subject notifies observers automatically
- Publish-subscribe mechanism
- Loosely coupled relationship

**When to Use:**
1. **Change propagation**: One object change affects many
2. **Event handling**: UI events, listeners
3. **Loose coupling**: Subject and observers independent
4. **Unknown observers**: Don't know how many observers
5. **Dynamic registration**: Observers can register/unregister

**Components:**
1. **Subject (Observable)**: 
   - Maintains list of observers
   - Methods to attach/detach observers
   - notifyObservers() method

2. **Observer**: 
   - Interface with update() method
   - Receives notifications

3. **Concrete Subject**: 
   - Implements subject
   - Stores state
   - Sends notifications

4. **Concrete Observer**: 
   - Implements observer
   - Reacts to notifications

**Flow:**
1. Observer registers with subject
2. Subject state changes
3. Subject notifies all observers
4. Observers react to notification

**Benefits:**
1. **Loose Coupling**: Subject and observers independent
2. **Dynamic Relationships**: Can add/remove observers at runtime
3. **Broadcast**: One-to-many communication
4. **Open/Closed**: Can add observers without modifying subject

**Drawbacks:**
1. **Memory Leaks**: Observers not properly removed
2. **Unexpected Updates**: Observers notified unexpectedly
3. **Order**: Notification order not guaranteed
4. **Performance**: Many observers can be slow

**Real-World Examples:**
1. **Event Listeners**: Button click listeners
2. **MVC**: Model notifies view of changes
3. **Java Swing**: ActionListener, MouseListener
4. **JavaFX**: Property listeners
5. **RxJava**: Observable and Observer

**Push vs Pull Model:**

**Push Model:**
- Subject sends data to observers
- update(data) method
- Observers receive all data
- More data might be sent than needed

**Pull Model:**
- Observers request data from subject
- update() notifies, observer gets data
- Observers get only what they need
- More flexible

**Java Built-in Support:**
- **Observable class** (deprecated in Java 9)
- **Observer interface** (deprecated in Java 9)
- **PropertyChangeSupport** (JavaBeans)
- **EventListener interfaces** (Swing)
- Reason for deprecation: Not serializable, lack of flexibility

**Best Practices:**
1. Use interfaces for subject and observer
2. Allow observers to unregister
3. Don't call observer methods while holding locks
4. Consider weak references for observers
5. Document notification behavior
6. Handle exceptions in observer code
7. Consider thread safety

**Modern Alternatives:**
- **Event Bus Libraries**: Guava EventBus
- **Reactive Programming**: RxJava, Project Reactor
- **Property Change Listeners**: JavaFX properties
- **CompletableFuture**: For asynchronous notifications

```java
import java.util.*;

// Subject
interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObservers();
}

// Observer
interface Observer {
    void update(String message);
}

// Concrete Subject
class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;
    
    @Override
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    @Override
    public void detach(Observer observer) {
        observers.remove(observer);
    }
    
    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(news);
        }
    }
    
    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }
}

// Concrete Observer
class NewsChannel implements Observer {
    private String name;
    
    public NewsChannel(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String message) {
        System.out.println(name + " received news: " + message);
    }
}

public class ObserverPatternDemo {
    public static void main(String[] args) {
        NewsAgency agency = new NewsAgency();
        
        NewsChannel channel1 = new NewsChannel("Channel 1");
        NewsChannel channel2 = new NewsChannel("Channel 2");
        
        agency.attach(channel1);
        agency.attach(channel2);
        
        agency.setNews("Breaking news!");
    }
}
```

---

## Memory Management & Garbage Collection

**Theory:**
Java's automatic memory management system that handles allocation and deallocation of memory.

**Why Automatic Memory Management?**
1. **Prevents Memory Leaks**: Automatic deallocation
2. **Prevents Dangling Pointers**: No manual pointers
3. **Simplifies Code**: No explicit memory management
4. **Safer**: Reduces programming errors
5. **Productivity**: Focus on logic, not memory

**Memory Structure in JVM:**

**1. Heap Memory:**
- **Purpose**: Store objects and instance variables
- **Scope**: Shared among all threads
- **Lifecycle**: Created at JVM startup, destroyed at shutdown
- **Size**: Configurable (-Xms, -Xmx)
- **Garbage
   - Object instances and arrays
   - Shared among all threads
   - Garbage collected

2. Stack Memory:
   - Method calls and local variables
   - One stack per thread
   - LIFO structure
   - Automatically managed

3. Method Area (Metaspace in Java 8+):
   - Class structures, method data, static variables
   - Shared among all threads

4. Program Counter (PC) Register:
   - Current instruction address
   - One per thread

5. Native Method Stack:
   - Native method information
*/

public class MemoryDemo {
    static int staticVar = 10; // Method Area
    
    public void method() {
        int localVar = 20;  // Stack
        Integer obj = 30;   // obj reference in Stack, object in Heap
    }
}
```

### Garbage Collection

**Detailed Theory:**

**What is Garbage Collection?**
- Automatic memory management
- Identifies and removes unused objects
- Reclaims memory
- Prevents memory leaks

**How GC Works:**
1. **Mark**: Identify objects that are reachable
2. **Sweep**: Remove unreachable objects
3. **Compact** (optional): Defragment memory

**When Object Becomes Eligible for GC:**
1. **No references**: No variable refers to object
2. **Null assignment**: Reference set to null
3. **Reassignment**: Reference assigned to another object
4. **Anonymous object**: Object created but not assigned
5. **Island of Isolation**: Group of objects only reference each other

**Types of Garbage Collectors:**

**1. Serial GC (-XX:+UseSerialGC):**
- **Single thread**: One thread for GC
- **Stop-the-world**: All application threads pause
- **Use**: Small applications, single CPU
- **Best**: Client applications

**2. Parallel GC (-XX:+UseParallelGC):**
- **Multiple threads**: Parallel marking and copying
- **Stop-the-world**: All application threads pause
- **Throughput focused**: Maximum work done
- **Use**: Applications where throughput important
- **Best**: Batch processing

**3. CMS - Concurrent Mark Sweep (-XX:+UseConcMarkSweepGC):**
- **Concurrent**: Runs concurrently with application
- **Low pause time**: Minimizes pause duration
- **No compaction**: Can lead to fragmentation
- **Deprecated**: Removed in Java 14
- **Use**: Applications needing low latency
- **Best**: Interactive applications

**4. G1 (Garbage First) (-XX:+UseG1GC) [Default in Java 9+]:**
- **Region-based**: Heap divided into regions
- **Predictable pauses**: Can set pause time goals
- **Concurrent**: Mostly concurrent
- **Compacting**: Reduces fragmentation
- **Use**: Large heaps (>4GB), balanced performance
- **Best**: Most applications (default)

**5. ZGC (-XX:+UseZGC) [Java 11+]:**
- **Ultra-low latency**: Pause times < 10ms
- **Concurrent**: Almost fully concurrent
- **Scalable**: Handles large heaps (TBs)
- **Use**: Applications requiring very low latency
- **Best**: Real-time systems

**6. Shenandoah (-XX:+UseShenandoahGC) [Java 12+]:**
- **Low pause time**: Similar to ZGC
- **Concurrent**: Concurrent compaction
- **Use**: Low latency requirements
- **Alternative**: To ZGC

**GC Generations:**

**Young Generation:**
- **Eden**: New objects
- **Survivor (S0, S1)**: Survived objects
- **Minor GC**: Only young generation
- **Frequent**: Happens often
- **Fast**: Small area

**Old Generation (Tenured):**
- **Long-lived objects**: Survived multiple GCs
- **Major GC**: Old generation
- **Less frequent**: Happens less often
- **Slow**: Larger area

**Promotion:**
- Object moves from Young to Old
- After surviving multiple minor GCs
- Age threshold (default 15)

**Full GC:**
- Both young and old generations
- Most expensive
- Stop-the-world pause

**GC Tuning Goals:**
1. **Throughput**: Maximize work, minimize GC time
2. **Latency**: Minimize pause times
3. **Footprint**: Minimize heap size

**Trade-offs:**
- High throughput ↔ Higher latency
- Low latency ↔ Lower throughput
- Small footprint ↔ Lower throughput, higher latency

**finalize() Method:**
- Called by GC before object reclaimed
- **Deprecated** in Java 9
- **Problems**:
  - No guarantee when called
  - Can resurrect object
  - Performance overhead
  - Can cause memory leaks
- **Alternative**: try-with-resources, Cleaner API (Java 9+)

**Best Practices:**
1. **Don't call System.gc()**: Let JVM decide
2. **Use try-with-resources**: For resource cleanup
3. **Nullify large objects**: Help GC identify
4. **Avoid finalize()**: Use Cleaner or AutoCloseable
5. **Profile**: Understand GC behavior
6. **Tune parameters**: Based on application needs
7. **Monitor**: Use tools (VisualVM, JConsole)

**Tools:**
- **jstat**: GC statistics
- **jmap**: Heap dump
- **VisualVM**: Visual monitoring
- **JConsole**: Monitoring console
- **GC logs**: -Xlog:gc (Java 9+), -verbose:gc

```java
public class GarbageCollectionDemo {
    public static void main(String[] args) {
        // Creating objects
        String str1 = new String("Hello");
        String str2 = new String("World");
        
        // Making objects eligible for GC
        str1 = null; // Dereferencing
        str2 = null;
        
        // Requesting GC (not guaranteed to run immediately)
        System.gc(); // or Runtime.getRuntime().gc();
        
        // finalize() method (deprecated in Java 9)
        // Called by GC before reclaiming object
    }
    
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Finalize method called");
    }
}

/*
Ways to make object eligible for GC:
1. Nullifying reference: obj = null;
2. Reassigning reference: obj = new Object();
3. Anonymous object: new Object();
4. Island of Isolation: circular references with no external references
*/

/*
Types of GC:
1. Minor GC - Young generation
2. Major GC - Old generation
3. Full GC - Entire heap

GC Algorithms:
1. Serial GC
2. Parallel GC
3. CMS (Concurrent Mark Sweep)
4. G1 (Garbage First)
5. ZGC (Low latency)
*/
```

### Strong, Soft, Weak, and Phantom References

**Theory:**
Java provides different types of references with different GC behaviors.

**1. Strong Reference (Default):**
- **Definition**: Normal reference
- **Syntax**: `Object obj = new Object();`
- **GC Behavior**: Never collected while reachable
- **Use**: Normal references
- **Example**: `String str = "Hello";`

**2. Soft Reference:**
- **Definition**: Reference that may be cleared if memory low
- **Package**: java.lang.ref.SoftReference
- **GC Behavior**: Cleared before OutOfMemoryError
- **Use**: Memory-sensitive caches
- **Guarantee**: Cleared only if memory needed
- **Example**: Image cache

**3. Weak Reference:**
- **Definition**: Reference that doesn't prevent GC
- **Package**: java.lang.ref.WeakReference
- **GC Behavior**: Cleared at next GC (if no strong references)
- **Use**: Canonical mappings (WeakHashMap)
- **Guarantee**: Cleared in next GC cycle
- **Example**: Cache with no strong guarantee

**4. Phantom Reference:**
- **Definition**: Weakest reference type
- **Package**: java.lang.ref.PhantomReference
- **GC Behavior**: get() always returns null
- **Use**: Post-mortem cleanup, finalization alternative
- **Requires**: ReferenceQueue
- **Example**: Track when object finalized

**Comparison:**

| Type | GC Behavior | get() After GC | Use Case |
|------|-------------|----------------|----------|
| **Strong** | Never while reachable | Accessible | Normal references |
| **Soft** | Before OOM | null | Memory-sensitive cache |
| **Weak** | Next GC cycle | null | Canonical mappings |
| **Phantom** | After finalization | Always null | Post-mortem cleanup |

**Reference Queue:**
- Queue where references are enqueued after GC
- Notifies when references are cleared
- Used with Soft, Weak, Phantom references

**SoftReference vs WeakReference:**
- **Soft**: Kept longer, cleared only if needed
- **Weak**: Cleared immediately in next GC
- **Soft**: Better for caches with memory pressure
- **Weak**: Better for canonical mappings

**WeakHashMap:**
- **Keys**: Weak references
- **Behavior**: Entry removed when key has no strong reference
- **Use**: Canonical mappings, caches
- **Example**: Metadata associated with objects

**Cleaner API (Java 9+):**
- Replacement for finalize()
- More reliable cleanup
- Based on PhantomReference
- Register cleanup action

**Best Practices:**
1. **Strong**: Default for normal objects
2. **Soft**: For aches that can be recreated
3. **Weak**: For canonical mappings
4. **Phantom**: For cleanup actions
5. **Avoid finalize()**: Use Cleaner or try-with-resources
6. **ReferenceQueue**: Monitor cleared references

```java
import java.lang.ref.*;

public class ReferenceTypes {
    public static void main(String[] args) {
        // Strong Reference (default)
        Object strongRef = new Object();
        // Not eligible for GC until reference is removed
        
        // Soft Reference (memory-sensitive cache)
        SoftReference<Object> softRef = new SoftReference<>(new Object());
        Object obj1 = softRef.get(); // May return null if memory is low
        
        // Weak Reference (WeakHashMap uses this)
        WeakReference<Object> weakRef = new WeakReference<>(new Object());
        Object obj2 = weakRef.get(); // May return null after GC
        
        // Phantom Reference (post-mortem cleanup)
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
        PhantomReference<Object> phantomRef = 
            new PhantomReference<>(new Object(), queue);
        Object obj3 = phantomRef.get(); // Always returns null
    }
}
```

---

## Important Keywords

**Theory:**
Reserved words in Java with special meaning. Cannot be used as identifiers.

**Access Modifiers:**
- **public**: Accessible everywhere
- **private**: Accessible only within class
- **protected**: Accessible within package and subclasses
- **default** (no keyword): Accessible within package

**Non-Access Modifiers:**
- **static**: Belongs to class, not instance
- **final**: Constant, cannot be changed
- **abstract**: Incomplete, must be implemented
- **synchronized**: Thread-safe
- **volatile**: Visible across threads
- **transient**: Not serialized
- **native**: Implemented in other language

### final Keyword

**Theory:**
final keyword makes entities unchangeable.

**1. final Variable:**
- **Meaning**: Value cannot be changed (constant)
- **Initialization**: Must be initialized
- **When**: During declaration, constructor, or initializer block
- **Naming Convention**: UPPER_CASE
- **Benefits**: Thread-safe, optimized by compiler

**Types:**
- **final primitive**: Value immutable
- **final reference**: Reference immutable (object can change)
- **final parameter**: Parameter cannot be reassigned in method

**2. final Method:**
- **Meaning**: Cannot be overridden
- **Use**: Prevent subclass from changing behavior
- **Benefits**: Security, performance (inlined)
- **Design**: Core functionality that shouldn't change

**3. final Class:**
- **Meaning**: Cannot be subclassed
- **Use**: Prevent inheritance
- **Benefits**: Security, immutability
- **Examples**: String, Integer, System
- **Design**: Complete, shouldn't be extended

**final vs Immutable:**
- **final**: Reference/class unchangeable
- **Immutable**: Object state unchangeable
- **Example**: `final StringBuilder` (reference final, object mutable)

**Benefits:**
1. **Constants**: Define unchangeable values
2. **Method Contract**: Ensure behavior not changed
3. **Immutability**: Help create immutable classes
4. **Performance**: Compiler optimizations
5. **Thread Safety**: final fields thread-safe after construction
6. **Design**: Communicate intent

**Blank final:**
- final variable declared but not initialized
- Must be initialized in constructor
- Different objects can have different values

```java
// final variable (constant)
class FinalVariableDemo {
    final int CONSTANT = 100;          // Must be initialized
    final int value;
    
    FinalVariableDemo() {
        value = 50; // Can be initialized in constructor
    }
    
    public void method() {
        final int localConst = 10;
        // localConst = 20; // Error! Cannot reassign
    }
}

// final method (cannot be overridden)
class Parent {
    public final void display() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    // public void display() {} // Error! Cannot override final method
}

// final class (cannot be extended)
final class FinalClass {
    // ...
}

// class SubClass extends FinalClass {} // Error! Cannot extend final class
```

### static Keyword

**Theory:**
static keyword indicates member belongs to class, not instance.

**1. static Variable (Class Variable):**
- **Meaning**: Shared among all instances
- **Memory**: Allocated once in method area
- **Lifetime**: Program start to end
- **Access**: ClassName.variableName (preferred) or object.variableName
- **Use**: Common property for all objects
- **Examples**: Counter, configuration

**2. static Method:**
- **Meaning**: Belongs to class, not instance
- **Access**: ClassName.methodName() (preferred)
- **Restrictions**:
  - Cannot access instance variables/methods directly
  - Cannot use this or super
  - Can only access static members directly
- **Use**: Utility methods, factory methods
- **Examples**: Math.sqrt(), Integer.parseInt()

**3. static Block:**
- **Meaning**: Initializes static variables
- **Execution**: Once when class loaded
- **Order**: Before main() method
- **Use**: Complex static initialization
- **Multiple**: Can have multiple blocks (executed in order)

**4. static Nested Class:**
- **Meaning**: Static member class
- **Access**: Can access static members of outer class only
- **Instantiation**: Without outer class instance
- **Use**: Group related classes, builder pattern
- **Example**: `Map.Entry`

**static vs Instance:**

| Feature | static | Instance |
|---------|--------|----------|
| **Belongs to** | Class | Object |
| **Memory** | Once per class | Per object |
| **Access** | Class name | Object reference |
| **this/super** | Cannot use | Can use |
| **Non-static access** | Cannot directly | Can |
| **When loaded** | Class loading | Object creation |

**When to Use static:**
1. **Constant**: public static final fields
2. **Utility method**: Math operations, conversions
3. **Counter**: Track number of objects
4. **Factory method**: Alternative constructors
5. **Main method**: Entry point

**Common Mistakes:**
1. Accessing instance members from static context
2. Overusing static (makes testing harder)
3. Static mutable state (threading issues)

**Best Practices:**
1. Use static for constants (public static final)
2. Use static for utility methods
3. Prefer instance methods for object behavior
4. Avoid static mutable state
5. Make utility classes final with private constructor

```java
class StaticDemo {
    // Static variable (class variable)
    static int count = 0;
    
    // Instance variable
    int id;
    
    // Static method
    public static void staticMethod() {
        System.out.println("Static method");
        // Can only access static members directly
    }
    
    // Static block (executed when class is loaded)
    static {
        System.out.println("Static block");
        count = 10;
    }
    
    // Static nested class
    static class NestedClass {
        // ...
    }
}

public class StaticKeywordDemo {
    public static void main(String[] args) {
        // Access static members using class name
        System.out.println(StaticDemo.count);
        StaticDemo.staticMethod();
    }
}
```

### this and super Keywords

```java
class Parent {
    int x = 10;
    
    public Parent() {
        System.out.println("Parent constructor");
    }
    
    public void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    int x = 20;
    
    public Child() {
        super(); // Call parent constructor (must be first statement)
        System.out.println("Child constructor");
    }
    
    public void display() {
        super.display(); // Call parent method
        System.out.println("Child display");
    }
    
    public void showX() {
        System.out.println(super.x); // Parent's x = 10
        System.out.println(this.x);  // Child's x = 20
        System.out.println(x);       // Child's x = 20
    }
    
    public void method(int x) {
        this.x = x; // this refers to current object
    }
}
```

### instanceof Keyword

```java
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

public class InstanceofDemo {
    public static void main(String[] args) {
        Animal animal = new Dog();
        
        if (animal instanceof Dog) {
            System.out.println("It's a Dog");
            Dog dog = (Dog) animal; // Downcasting
        }
        
        if (animal instanceof Animal) {
            System.out.println("It's an Animal");
        }
        
        if (animal instanceof Cat) {
            System.out.println("It's a Cat");
        } else {
            System.out.println("Not a Cat");
        }
    }
}
```

### transient Keyword

```java
import java.io.*;

class User implements Serializable {
    String username;
    transient String password; // Not serialized
    
    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }
}
```

### volatile Keyword

```java
// volatile ensures visibility across threads
class VolatileDemo {
    private volatile boolean flag = true;
    
    public void stop() {
        flag = false; // Visible to all threads immediately
    }
    
    public void run() {
        while (flag) {
            // Do something
        }
    }
}
```

### native Keyword

```java
// native method is implemented in another language (e.g., C, C++)
class NativeDemo {
    public native void nativeMethod();
    
    static {
        System.loadLibrary("nativelib");
    }
}
```

---

## JDBC

**Theory:**
JDBC (Java Database Connectivity) is an API for executing SQL queries and accessing databases in Java.

**What is JDBC?**
- Standard API for database access
- Platform independent
- Database independent
- Part of J2SE (java.sql package)

**Why JDBC?**
1. **Database Independence**: Switch databases without code change
2. **Standard API**: Same interface for all databases
3. **Platform Independence**: Works on any OS
4. **Connection Pooling**: Reuse connections
5. **Transaction Management**: ACID properties

**JDBC Architecture:**

**Two-Tier:**
```
Java Application → JDBC Driver → Database
```

**Three-Tier:**
```
Java Application → Application Server → Database
```

**JDBC Components:**

**1. DriverManager:**
- Manages JDBC drivers
- Establishes connection to database
- Methods: getConnection(), registerDriver()

**2. Driver:**
- Interface between application and database
- Database-specific implementation
- Types: Type 1, 2, 3, 4

**3. Connection:**
- Session with database
- Methods: createStatement(), prepareStatement(), commit(), rollback()

**4. Statement:**
- Executes SQL queries
- Types: Statement, PreparedStatement, CallableStatement

**5. ResultSet:**
- Result of SQL query
- Iterator over result rows
- Methods: next(), getString(), getInt(), etc.

**JDBC Driver Types:**

**Type 1: JDBC-ODBC Bridge:**
- Uses ODBC driver
- Platform dependent
- Slow performance
- Deprecated in Java 8

**Type 2: Native-API Driver:**
- Uses database native client libraries
- Platform dependent
- Better performance than Type 1
- Requires native libraries

**Type 3: Network Protocol Driver:**
- Pure Java driver
- Communicates with middleware server
- Platform independent
- Flexible

**Type 4: Thin Driver (Pure Java):**
- Pure Java driver
- Direct communication with database
- Platform independent
- Best performance
- **Most Common** (preferred)

### JDBC Steps

**Theory:**

Standard steps to work with database:

**Step 1: Load Driver (Optional in JDBC 4.0+)**
- `Class.forName("driver-class-name")`
- Automatically loaded if driver JAR in classpath
- JDBC 4.0+ has auto-discovery

**Step 2: Establish Connection**
- `DriverManager.getConnection(url, username, password)`
- **URL Format**: `jdbc:dbtype://host:port/database`
- **Examples**:
  - MySQL: `jdbc:mysql://localhost:3306/mydb`
  - Oracle: `jdbc:oracle:thin:@localhost:1521:orcl`
  - PostgreSQL: `jdbc:postgresql://localhost:5432/mydb`

**Step 3: Create Statement**
- **Statement**: `connection.createStatement()`
- **PreparedStatement**: `connection.prepareStatement(sql)`
- **CallableStatement**: `connection.prepareCall(sql)`

**Step 4: Execute Query**
- **executeQuery(sql)**: For SELECT (returns ResultSet)
- **executeUpdate(sql)**: For INSERT, UPDATE, DELETE (returns int)
- **execute(sql)**: For any SQL (returns boolean)

**Step 5: Process Results**
- Iterate through ResultSet
- Extract data using getXxx() methods
- Column accessed by index (1-based) or name

**Step 6: Close Resources**
- Close ResultSet, Statement, Connection
- Use try-with-resources (auto-close)
- Important to prevent resource leaks

**Resource Order:**
- **Open**: Connection → Statement → ResultSet
- **Close**: ResultSet → Statement → Connection

```java
import java.sql.*;

public class JDBCDemo {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String username = "root";
        String password = "password";
        
        try {
            // 1. Load Driver (optional in JDBC 4.0+)
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            // 2. Establish Connection
            Connection conn = DriverManager.getConnection(url, username, password);
            
            // 3. Create Statement
            Statement stmt = conn.createStatement();
            
            // 4. Execute Query
            String sql = "SELECT * FROM users";
            ResultSet rs = stmt.executeQuery(sql);
            
            // 5. Process Results
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println(id + ": " + name);
            }
            
            // 6. Close Resources
            rs.close();
            stmt.close();
            conn.close();
            
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### PreparedStatement

**Theory:**
Precompiled SQL statement that accepts parameters. More efficient and secure than Statement.

**What is PreparedStatement?**
- Precompiled SQL statement
- Parameters represented by ?
- Prevents SQL injection
- Better performance for repeated execution
- Extends Statement interface

**Why PreparedStatement?**

**1. SQL Injection Prevention:**
```java
// Vulnerable (Statement)
String query = "SELECT * FROM users WHERE name = '" + name + "'";
// If name = "admin' OR '1'='1", entire table exposed

// Safe (PreparedStatement)
String query = "SELECT * FROM users WHERE name = ?";
pstmt.setString(1, name); // Properly escaped
```

**2. Performance:**
- Parsed and compiled once
- Reused with different parameters
- Database can cache execution plan
- Faster for repeated execution

**3. Readability:**
- Clearer SQL with parameters
- No string concatenation

**Parameter Methods:**
- `setInt(index, value)`: Integer parameter
- `setString(index, value)`: String parameter
- `setDouble(index, value)`: Double parameter
- `setDate(index, value)`: Date parameter
- `setNull(index, sqlType)`: Null value
- Index is 1-based

**Statement Types:**

**1. Statement:**
- Execute different SQL queries
- Not precompiled
- Vulnerable to SQL injection
- Slower for repeated execution
- **Use**: Dynamic queries, DDL statements

**2. PreparedStatement:**
- Precompiled SQL with parameters
- Prevents SQL injection
- Better performance
- **Use**: Parameterized queries (DML)

**3. CallableStatement:**
- Execute stored procedures
- Extends PreparedStatement
- Can have IN, OUT, INOUT parameters
- **Use**: Stored procedures

**Comparison:**

| Feature | Statement | PreparedStatement | CallableStatement |
|---------|-----------|-------------------|-------------------|
| **SQL** | Static | Parameterized | Stored Procedure |
| **Compilation** | Each time | Once | Once |
| **SQL Injection** | Vulnerable | Protected | Protected |
| **Performance** | Slower | Faster | Fastest (in DB) |
| **Parameters** | No | Yes (?) | Yes (?, named) |
| **Use** | Simple queries | Parameterized queries | Stored procedures |

**Best Practices:**
1. **Always use PreparedStatement** for user input
2. **Never concatenate** SQL strings with user input
3. **Close resources** in finally or try-with-resources
4. **Reuse PreparedStatement** for batch operations
5. **Use batch updates** for multiple similar operations
6. **Set appropriate parameter types**

**Batch Processing:**
```java
pstmt.addBatch(); // Add to batch
pstmt.executeBatch(); // Execute all
```

**Benefits:**
- Execute multiple statements at once
- Reduced network round-trips
- Better performance
```java
import java.sql.*;

public class PreparedStatementDemo {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String username = "root";
        String password = "password";
        
        try (Connection conn = DriverManager.getConnection(url, username, password)) {
            
            // Insert
            String insertSQL = "INSERT INTO users (name, email) VALUES (?, ?)";
            PreparedStatement pstmt = conn.prepareStatement(insertSQL);
            pstmt.setString(1, "John");
            pstmt.setString(2, "john@email.com");
            int rowsInserted = pstmt.executeUpdate();
            
            // Update
            String updateSQL = "UPDATE users SET email = ? WHERE name = ?";
            pstmt = conn.prepareStatement(updateSQL);
            pstmt.setString(1, "newemail@email.com");
            pstmt.setString(2, "John");
            int rowsUpdated = pstmt.executeUpdate();
            
            // Delete
            String deleteSQL = "DELETE FROM users WHERE name = ?";
            pstmt = conn.prepareStatement(deleteSQL);
            pstmt.setString(1, "John");
            int rowsDeleted = pstmt.executeUpdate();
            
            pstmt.close();
            
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

/*
PreparedStatement vs Statement:
- PreparedStatement is precompiled and faster for repeated executions
- PreparedStatement prevents SQL injection
- PreparedStatement is preferred for parameterized queries
*/
```

### Transaction Management

**Theory:**
Database transaction is a unit of work that maintains ACID properties.

**ACID Properties:**

**1. Atomicity:**
- All or nothing
- Either all operations succeed or all fail
- No partial updates

**2. Consistency:**
- Database moves from one valid state to another
- Integrity constraints maintained
- Business rules enforced

**3. Isolation:**
- Concurrent transactions don't interfere
- Each transaction isolated from others
- Isolation levels control visibility

**4. Durability:**
- Committed changes are permanent
- Survive system failures
- Written to persistent storage

**Why Transactions?**
1. **Data Integrity**: Ensure consistency
2. **Error Recovery**: Rollback on failure
3. **Concurrent Access**: Handle multiple users
4. **Business Logic**: Group related operations

**Transaction Operations:**

**1. setAutoCommit(false):**
- Disable auto-commit mode
- Manual transaction control
- Default: auto-commit true

**2. commit():**
- Save all changes
- Make permanent
- End transaction

**3. rollback():**
- Undo all changes
- Restore previous state
- End transaction

**4. setSavepoint():**
- Create savepoint
- Rollback to specific point
- Partial rollback

**Transaction Isolation Levels:**

**1. READ_UNCOMMITTED (Level 0):**
- Can read uncommitted changes
- **Problem**: Dirty reads
- **Use**: Rarely used

**2. READ_COMMITTED (Level 1):**
- Can read only committed changes
- **Problem**: Non-repeatable reads, phantom reads
- **Use**: Default for most databases

**3. REPEATABLE_READ (Level 2):**
- Same query returns same result
- **Problem**: Phantom reads
- **Use**: When need consistent view

**4. SERIALIZABLE (Level 3):**
- Highest isolation
- Complete isolation
- **Problem**: Performance overhead
- **Use**: Critical transactions

**Concurrency Problems:**

**1. Dirty Read:**
- Read uncommitted data
- Data might be rolled back

**2. Non-Repeatable Read:**
- Same query returns different result
- Another transaction modified data

**3. Phantom Read:**
- New rows appear in result set
- Another transaction inserted rows

**Best Practices:**
1. **Keep transactions short**: Minimize lock duration
2. **Handle exceptions**: Always rollback on error
3. **Use appropriate isolation**: Balance consistency and performance
4. **Close resources**: Release locks
5. **Use try-with-resources**: Ensure cleanup
6. **Batch operations**: Group similar operations
7. **Connection pooling**: Reuse connections

**Example Pattern:**
```java
Connection conn = null;
try {
    conn = getConnection();
    conn.setAutoCommit(false);
    
    // Operations
    
    conn.commit();
} catch (SQLException e) {
    if (conn != null) {
        conn.rollback();
    }
    throw e;
} finally {
    if (conn != null) {
        conn.setAutoCommit(true);
        conn.close();
    }
}
```

**Connection Pooling:**
- Reuse database connections
- Improves performance
- Libraries: HikariCP, C3P0, Apache DBCP
- Essential for production applications

```java
import java.sql.*;

public class TransactionDemo {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String username = "root";
        String password = "password";
        
        Connection conn = null;
        
        try {
            conn = DriverManager.getConnection(url, username, password);
            
            // Disable auto-commit
            conn.setAutoCommit(false);
            
            // Execute multiple operations
            Statement stmt = conn.createStatement();
            stmt.executeUpdate("UPDATE accounts SET balance = balance - 100 WHERE id = 1");
            stmt.executeUpdate("UPDATE accounts SET balance = balance + 100 WHERE id = 2");
            
            // Commit transaction
            conn.commit();
            System.out.println("Transaction committed");
            
        } catch (SQLException e) {
            // Rollback transaction on error
            if (conn != null) {
                try {
                    conn.rollback();
                    System.out.println("Transaction rolled back");
                } catch (SQLException ex) {
                    ex.printStackTrace();
                }
            }
            e.printStackTrace();
        } finally {
            if (conn != null) {
                try {
                    conn.setAutoCommit(true);
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

---

## Additional Important Concepts

### Immutable Class

```java
// Immutable class example
public final class ImmutablePerson {
    private final String name;
    private final int age;
    private final Address address; // Mutable object
    
    public ImmutablePerson(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        // Deep copy of mutable object
        this.address = new Address(address.getStreet(), address.getCity());
    }
    
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public Address getAddress() {
        // Return copy, not original
        return new Address(address.getStreet(), address.getCity());
    }
}

class Address {
    private String street;
    private String city;
    
    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }
    
    public String getStreet() { return street; }
    public String getCity() { return city; }
}

/*
Rules for Immutable Class:
1. Make class final
2. Make all fields private and final
3. No setter methods
4. Initialize all fields via constructor
5. Deep copy mutable objects
6. Return copies of mutable objects in getters
*/
```

### Cloning

```java
class Person implements Cloneable {
    String name;
    int age;
    Address address;
    
    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }
    
    // Shallow copy
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
    
    // Deep copy
    protected Person deepClone() {
        Address clonedAddress = new Address(address.getStreet(), address.getCity());
        return new Person(this.name, this.age, clonedAddress);
    }
}

public class CloningDemo {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address("123 Street", "City");
        Person person1 = new Person("John", 25, address);
        
        // Shallow copy
        Person person2 = (Person) person1.clone();
        
        // Deep copy
        Person person3 = person1.deepClone();
    }
}
```

### Reflection API

```java
import java.lang.reflect.*;

class Student {
    private String name;
    public int age;
    
    public Student() {}
    
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    private void privateMethod() {
        System.out.println("Private method");
    }
    
    public void display() {
        System.out.println(name + ", " + age);
    }
}

public class ReflectionDemo {
    public static void main(String[] args) throws Exception {
        // Get Class object
        Class<?> clazz = Student.class;
        // Or: Class<?> clazz = Class.forName("Student");
        // Or: Class<?> clazz = obj.getClass();
        
        // Get class name
        String className = clazz.getName();
        String simpleName = clazz.getSimpleName();
        
        // Get constructors
        Constructor<?>[] constructors = clazz.getConstructors();
        Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
        
        // Create instance
        Student student = (Student) constructor.newInstance("John", 25);
        
        // Get fields
        Field[] fields = clazz.getDeclaredFields();
        Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true); // Access private field
        nameField.set(student, "Alice");
        
        // Get methods
        Method[] methods = clazz.getDeclaredMethods();
        Method displayMethod = clazz.getMethod("display");
        displayMethod.invoke(student); // Call method
        
        // Access private method
        Method privateMethod = clazz.getDeclaredMethod("privateMethod");
        privateMethod.setAccessible(true);
        privateMethod.invoke(student);
    }
}
```

### Enum

```java
// Simple enum
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

// Enum with fields and methods
enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    EARTH(5.976e+24, 6.37814e6),
    MARS(6.421e+23, 3.3972e6);
    
    private final double mass;
    private final double radius;
    
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }
    
    public double getMass() {
        return mass;
    }
    
    public double getRadius() {
        return radius;
    }
}

public class EnumDemo {
    public static void main(String[] args) {
        // Using enum
        Day today = Day.MONDAY;
        
        // Enum methods
        String name = today.name();      // "MONDAY"
        int ordinal = today.ordinal();   // 0
        
        // Enum values
        Day[] days = Day.values();
        for (Day day : days) {
            System.out.println(day);
        }
        
        // Switch with enum
        switch (today) {
            case MONDAY:
                System.out.println("Start of week");
                break;
            case FRIDAY:
                System.out.println("End of week");
                break;
            default:
                System.out.println("Midweek");
        }
        
        // valueOf
        Day day = Day.valueOf("MONDAY");
        
        // Enum with fields
        Planet earth = Planet.EARTH;
        System.out.println("Earth mass: " + earth.getMass());
    }
}
```

---

## Summary of Key Interview Topics

### Must-Know Concepts:
1. ✅ OOP Principles (Encapsulation, Inheritance, Polymorphism, Abstraction)
2. ✅ Collections Framework (List, Set, Map, Queue)
3. ✅ Exception Handling (try-catch-finally, custom exceptions)
4. ✅ Multithreading (Thread creation, synchronization, concurrent collections)
5. ✅ Java 8 Features (Lambda, Stream API, Optional, Method References)
6. ✅ String manipulation (String, StringBuilder, StringBuffer)
7. ✅ Generics and Wildcards
8. ✅ Design Patterns (Singleton, Factory, Builder, Observer)
9. ✅ Memory Management & Garbage Collection
10. ✅ JDBC basics
11. ✅ Important Keywords (final, static, this, super, volatile)
12. ✅ Serialization & Deserialization
13. ✅ Immutable classes
14. ✅ equals() and hashCode() methods
15. ✅ Reflection API basics

### Common Interview Questions:
- Difference between == and equals()
- ArrayList vs LinkedList
- HashMap vs Hashtable vs ConcurrentHashMap
- Abstract class vs Interface
- Method Overloading vs Method Overriding
- Checked vs Unchecked Exceptions
- fail-fast vs fail-safe iterators
- Shallow copy vs Deep copy
- Stack vs Heap memory
- String Pool concept

---

**Good luck with your Java interviews! 🚀**
