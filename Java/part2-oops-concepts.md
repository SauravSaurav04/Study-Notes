# Part 2: Object-Oriented Programming (OOP)

**Focus Areas:** Classes, Objects, Constructors, Encapsulation, Inheritance, Polymorphism, Abstraction

---

## 📋 Table of Contents
1. [OOP Overview](#-oop-overview)
2. [Classes and Objects](#-classes-and-objects)
3. [Constructors](#-constructors)
4. [Encapsulation](#-encapsulation)
5. [Inheritance](#-inheritance)
6. [Polymorphism](#-polymorphism)
7. [Abstraction](#-abstraction)
8. [Abstract Class vs Interface](#-abstract-class-vs-interface)
9. [Method Overloading vs Overriding](#-method-overloading-vs-overriding)
10. [The `this` Keyword](#-the-this-keyword)
11. [Interview Questions](#-interview-questions)
12. [📄 One-Page Cheatsheet](#-one-page-cheatsheet)

---

## 🎯 OOP Overview

### What is Object-Oriented Programming?

**Definition:** OOP is a programming paradigm based on the concept of "objects" which contain data (attributes) and code (methods). It's a way to structure programs so that properties and behaviors are bundled into individual objects.

### Real-World Analogy

Think of a **Car**:
- **Attributes (State)**: color, brand, model, speed
- **Behavior (Methods)**: start(), accelerate(), brake(), stop()
- **Encapsulation**: Engine internals are hidden, you just use the steering wheel
- **Inheritance**: Electric Car inherits from Car
- **Polymorphism**: Different cars have different implementations of start()
- **Abstraction**: You drive without knowing engine mechanics

---

### Four Pillars of OOP

```
                    OOP
                     |
        ┌────────────┼────────────┐
        |            |            |
   ENCAPSULATION  INHERITANCE  POLYMORPHISM
        |                          |
    DATA HIDING              ABSTRACTION
```

| Pillar | Definition | Purpose |
|--------|------------|---------|
| **Encapsulation** | Bundling data and methods together | Data hiding, security |
| **Inheritance** | Acquiring properties from parent class | Code reusability |
| **Polymorphism** | One interface, many forms | Flexibility, extensibility |
| **Abstraction** | Hiding implementation details | Simplicity, reduce complexity |

---

### Benefits of OOP

✅ **Modularity**: Code is organized into objects  
✅ **Reusability**: Through inheritance  
✅ **Flexibility**: Through polymorphism  
✅ **Maintainability**: Easy to update and debug  
✅ **Security**: Through encapsulation  
✅ **Code Organization**: Logical structure  

---

## 🎯 Classes and Objects

### What is a Class?

**Definition:** A class is a **blueprint** or **template** for creating objects. It defines the structure and behavior that the objects will have.

**Real-World Analogy:**
- **Class** = Blueprint of a House
- **Object** = Actual House built from the blueprint

**Characteristics:**
- Logical entity (doesn't occupy memory)
- Contains attributes (fields/variables)
- Contains methods (behaviors)
- Can contain constructors, blocks, nested classes

---

### What is an Object?

**Definition:** An object is an **instance** of a class. It's a real-world entity with state and behavior.

**Three Characteristics:**

| Characteristic | Description | Example (Car object) |
|----------------|-------------|---------------------|
| **State** | Represented by attributes | color = "Red", speed = 0 |
| **Behavior** | Represented by methods | start(), accelerate() |
| **Identity** | Unique identifier | Object reference (memory address) |

---

### Class Syntax

```java
// Class Definition
public class ClassName {
    // 1. Instance Variables (Attributes)
    dataType variableName;
    
    // 2. Constructor
    public ClassName() {
        // Initialization code
    }
    
    // 3. Methods (Behaviors)
    public returnType methodName() {
        // Method body
    }
}
```

---

### Complete Example: Car Class

```java
public class Car {
    // ========== INSTANCE VARIABLES (Attributes) ==========
    private String brand;
    private String color;
    private int speed;
    
    // ========== CONSTRUCTOR ==========
    public Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
        this.speed = 0;  // Initial speed
    }
    
    // ========== METHODS (Behaviors) ==========
    
    // Accelerate method
    public void accelerate(int increment) {
        speed += increment;
        System.out.println(brand + " is now going at " + speed + " km/h");
    }
    
    // Brake method
    public void brake(int decrement) {
        speed -= decrement;
        if (speed < 0) speed = 0;
        System.out.println(brand + " slowed down to " + speed + " km/h");
    }
    
    // Display car info
    public void displayInfo() {
        System.out.println("Brand: " + brand);
        System.out.println("Color: " + color);
        System.out.println("Current Speed: " + speed + " km/h");
    }
    
    // ========== GETTERS & SETTERS ==========
    public String getBrand() {
        return brand;
    }
    
    public void setBrand(String brand) {
        this.brand = brand;
    }
    
    public String getColor() {
        return color;
    }
    
    public void setColor(String color) {
        this.color = color;
    }
    
    public int getSpeed() {
        return speed;
    }
}

// ========== MAIN CLASS (Using the Car class) ==========
public class Main {
    public static void main(String[] args) {
        // Creating Objects (Instances of Car class)
        Car car1 = new Car("Toyota", "Red");
        Car car2 = new Car("Honda", "Blue");
        
        // Using object methods
        car1.displayInfo();
        car1.accelerate(50);
        car1.brake(20);
        
        System.out.println("\n---");
        
        car2.displayInfo();
        car2.accelerate(80);
        
        // Accessing through getters
        System.out.println("\nCar 1 brand: " + car1.getBrand());
        System.out.println("Car 2 speed: " + car2.getSpeed());
    }
}
```

**Output:**
```
Brand: Toyota
Color: Red
Current Speed: 0 km/h
Toyota is now going at 50 km/h
Toyota slowed down to 30 km/h

---
Brand: Honda
Color: Blue
Current Speed: 0 km/h
Honda is now going at 80 km/h

Car 1 brand: Toyota
Car 2 speed: 80
```

---

### Memory Allocation

**When you create an object:**
```java
Car car1 = new Car("Toyota", "Red");
```

**What happens in memory:**

```
STACK                          HEAP
─────                          ────
main()                         Car Object (0x100)
└─ car1 = 0x100 ─────────────→ ├─ brand = "Toyota"
                                ├─ color = "Red"
                                └─ speed = 0
```

**Key Points:**
- Reference variable (`car1`) → Stack
- Actual object → Heap
- `new` keyword allocates memory in heap
- Constructor initializes the object

---

### Types of Variables in a Class

```java
public class Student {
    // 1. INSTANCE VARIABLE (Non-static)
    // - Belongs to object
    // - Each object has its own copy
    // - Created when object is created
    // - Stored in Heap
    String name;
    int age;
    
    // 2. STATIC VARIABLE (Class variable)
    // - Belongs to class
    // - Shared among all objects
    // - Created when class is loaded
    // - Stored in Method Area
    static String schoolName = "ABC School";
    static int studentCount = 0;
    
    // 3. LOCAL VARIABLE
    // - Declared inside methods
    // - Exists only during method execution
    // - Stored in Stack
    // - Must be initialized before use
    public void display() {
        String message = "Hello";  // Local variable
        System.out.println(message);
    }
    
    // Constructor
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
        studentCount++;  // Increment static variable
    }
}
```

---

### Variable Comparison

| Feature | Instance Variable | Static Variable | Local Variable |
|---------|------------------|-----------------|----------------|
| **Scope** | Object-level | Class-level | Method/Block level |
| **Memory** | Heap | Method Area | Stack |
| **Default Value** | ✅ Yes | ✅ Yes | ❌ No |
| **Access** | Through object | Through class name | Inside method only |
| **Created** | When object created | When class loaded | When method called |
| **Destroyed** | When object destroyed | When program ends | When method ends |
| **Shared** | ❌ Each object has own | ✅ Shared among all | ❌ Local to method |
| **Keyword** | None | `static` | None |

---

### Example: Understanding Variables

```java
public class Counter {
    // Instance variable - each object has its own
    int instanceCount = 0;
    
    // Static variable - shared by all objects
    static int staticCount = 0;
    
    public void increment() {
        // Local variable - exists only in this method
        int localCount = 0;
        
        instanceCount++;
        staticCount++;
        localCount++;
        
        System.out.println("Instance: " + instanceCount + 
                         ", Static: " + staticCount + 
                         ", Local: " + localCount);
    }
    
    public static void main(String[] args) {
        Counter c1 = new Counter();
        Counter c2 = new Counter();
        
        c1.increment();  // Instance: 1, Static: 1, Local: 1
        c1.increment();  // Instance: 2, Static: 2, Local: 1
        
        c2.increment();  // Instance: 1, Static: 3, Local: 1
        c2.increment();  // Instance: 2, Static: 4, Local: 1
        
        // Note: localCount resets to 0 in each method call
        // instanceCount is separate for c1 and c2
        // staticCount is shared and keeps incrementing
    }
}
```

---

### 🔥 Interview Questions: Classes & Objects

**Q1: What is the difference between a class and an object?**
- **Class**: Blueprint/template (doesn't occupy memory)
- **Object**: Instance of class (occupies memory in heap)

**Q2: Can we have a class without any object?**
- Yes, if all members are static (e.g., Math class, utility classes)

**Q3: How many objects can be created from a class?**
- Unlimited (subject to available memory)

**Q4: What happens if we don't create any object of a class?**
- Class is loaded in memory when first referenced
- Static members are initialized
- No instance variables are created

**Q5: Can we declare a class as static?**
- No, top-level classes cannot be static
- Only nested/inner classes can be static

**Q6: Where are objects stored?**
- Objects → Heap memory
- References → Stack (if local) or Heap (if instance variable)

---

## 🎯 Constructors

### What is a Constructor?

**Definition:** A constructor is a **special method** used to initialize objects. It's called automatically when an object is created using the `new` keyword.

### Characteristics

| Feature | Constructor | Regular Method |
|---------|------------|----------------|
| **Name** | Same as class name | Any valid identifier |
| **Return Type** | No return type (not even void) | Has return type |
| **Called** | Automatically when object created | Explicitly by programmer |
| **Purpose** | Initialize object | Perform operations |
| **Overloading** | ✅ Allowed | ✅ Allowed |
| **Inheritance** | ❌ Not inherited | ✅ Inherited |

---

### Types of Constructors

#### 1️⃣ Default Constructor

**Definition:** Constructor with no parameters. If you don't define any constructor, Java provides a default constructor automatically.

**Java-Provided Default Constructor:**
```java
public class Student {
    String name;
    int age;
    
    // If you don't write any constructor, Java adds this:
    // public Student() {
    //     // Empty
    // }
}
```

**Custom Default Constructor:**
```java
public class Student {
    String name;
    int age;
    
    // Custom default constructor
    public Student() {
        name = "Unknown";
        age = 0;
        System.out.println("Default constructor called");
    }
}

// Usage
Student s = new Student();  // Output: Default constructor called
```

---

#### 2️⃣ Parameterized Constructor

**Definition:** Constructor with parameters to initialize object with specific values.

```java
public class Student {
    String name;
    int age;
    
    // Parameterized constructor
    public Student(String name, int age) {
        this.name = name;  // 'this' refers to current object
        this.age = age;
    }
    
    public void display() {
        System.out.println("Name: " + name + ", Age: " + age);
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Student s1 = new Student("John", 20);
        Student s2 = new Student("Alice", 22);
        
        s1.display();  // Name: John, Age: 20
        s2.display();  // Name: Alice, Age: 22
    }
}
```

---

#### 3️⃣ Copy Constructor

**Definition:** Constructor that creates a new object as a copy of an existing object.

```java
public class Student {
    String name;
    int age;
    
    // Parameterized constructor
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Copy constructor
    public Student(Student other) {
        this.name = other.name;
        this.age = other.age;
        System.out.println("Copy constructor called");
    }
    
    public void display() {
        System.out.println("Name: " + name + ", Age: " + age);
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Student s1 = new Student("John", 20);
        Student s2 = new Student(s1);  // Copy constructor
        
        s1.display();  // Name: John, Age: 20
        s2.display();  // Name: John, Age: 20
        
        // s2 is a separate object with same values
        s2.name = "Alice";
        s1.display();  // Name: John, Age: 20 (unchanged)
        s2.display();  // Name: Alice, Age: 20 (changed)
    }
}
```

---

### Constructor Overloading

**Definition:** Having multiple constructors with different parameter lists.

```java
public class Rectangle {
    int length;
    int width;
    
    // 1. Default constructor
    public Rectangle() {
        length = 1;
        width = 1;
    }
    
    // 2. Constructor with one parameter (square)
    public Rectangle(int side) {
        length = side;
        width = side;
    }
    
    // 3. Constructor with two parameters
    public Rectangle(int length, int width) {
        this.length = length;
        this.width = width;
    }
    
    public int area() {
        return length * width;
    }
    
    public void display() {
        System.out.println("Length: " + length + ", Width: " + width + ", Area: " + area());
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Rectangle r1 = new Rectangle();           // Default
        Rectangle r2 = new Rectangle(5);          // Square
        Rectangle r3 = new Rectangle(4, 6);       // Rectangle
        
        r1.display();  // Length: 1, Width: 1, Area: 1
        r2.display();  // Length: 5, Width: 5, Area: 25
        r3.display();  // Length: 4, Width: 6, Area: 24
    }
}
```

---

### Constructor Chaining

**Definition:** Calling one constructor from another constructor using `this()` or `super()`.

#### Using `this()` - Same Class

```java
public class Employee {
    int id;
    String name;
    double salary;
    
    // Constructor 1
    public Employee() {
        this(0, "Unknown", 0.0);  // Calls Constructor 3
        System.out.println("Default constructor");
    }
    
    // Constructor 2
    public Employee(int id, String name) {
        this(id, name, 50000.0);  // Calls Constructor 3
        System.out.println("Two-parameter constructor");
    }
    
    // Constructor 3 - Master constructor
    public Employee(int id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
        System.out.println("Three-parameter constructor");
    }
}

// Usage
Employee e1 = new Employee();
// Output:
// Three-parameter constructor
// Default constructor

Employee e2 = new Employee(101, "John");
// Output:
// Three-parameter constructor
// Two-parameter constructor
```

**Rules for `this()`:**
- Must be the **first statement** in constructor
- Cannot use both `this()` and `super()` in same constructor
- Avoids code duplication

---

#### Using `super()` - Parent Class

```java
class Vehicle {
    String brand;
    
    public Vehicle() {
        System.out.println("Vehicle constructor");
    }
    
    public Vehicle(String brand) {
        this.brand = brand;
        System.out.println("Vehicle parameterized constructor: " + brand);
    }
}

class Car extends Vehicle {
    String model;
    
    public Car() {
        super();  // Calls parent default constructor
        System.out.println("Car constructor");
    }
    
    public Car(String brand, String model) {
        super(brand);  // Calls parent parameterized constructor
        this.model = model;
        System.out.println("Car parameterized constructor: " + model);
    }
}

// Usage
Car c1 = new Car();
// Output:
// Vehicle constructor
// Car constructor

Car c2 = new Car("Toyota", "Camry");
// Output:
// Vehicle parameterized constructor: Toyota
// Car parameterized constructor: Camry
```

---

### Important Rules for Constructors

| Rule | Description |
|------|-------------|
| **Name** | Must match class name exactly (case-sensitive) |
| **Return Type** | No return type (not even void) |
| **Default** | If no constructor defined, Java provides default |
| **this()** | Must be first statement, calls another constructor in same class |
| **super()** | Must be first statement, calls parent constructor |
| **Overloading** | Multiple constructors with different parameters allowed |
| **Inheritance** | Constructors are NOT inherited |
| **Static** | Cannot be static, abstract, or final |
| **Access Modifiers** | Can have any access modifier (public, private, protected, default) |

---

### Private Constructor (Singleton Pattern)

**Use Case:** Prevent object creation from outside the class (Singleton design pattern)

```java
public class Singleton {
    // Private static instance
    private static Singleton instance;
    
    // Private constructor
    private Singleton() {
        System.out.println("Singleton instance created");
    }
    
    // Public method to get instance
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    
    public void showMessage() {
        System.out.println("Hello from Singleton!");
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        // Singleton s1 = new Singleton();  // ❌ ERROR: Constructor is private
        
        Singleton s1 = Singleton.getInstance();  // ✅ Correct way
        Singleton s2 = Singleton.getInstance();
        
        s1.showMessage();
        
        // Check if both references point to same object
        System.out.println(s1 == s2);  // true (same instance)
    }
}
```

---

### 🔥 Interview Questions: Constructors

**Q1: What is a constructor?**
- Special method to initialize objects, called automatically when object is created, same name as class, no return type

**Q2: Can a constructor return a value?**
- No, it has no return type (not even void)

**Q3: Can we overload constructors?**
- Yes, we can have multiple constructors with different parameters

**Q4: Can we override constructors?**
- No, constructors are not inherited, so cannot be overridden

**Q5: What happens if we don't define a constructor?**
- Java provides a default no-argument constructor automatically

**Q6: Can a constructor be private?**
- Yes, used in Singleton pattern to restrict object creation

**Q7: What is constructor chaining?**
- Calling one constructor from another using `this()` (same class) or `super()` (parent class)

**Q8: Can we call a constructor explicitly?**
- No, constructors are called automatically when object is created with `new`

**Q9: Difference between `this()` and `super()`?**
- `this()`: Calls constructor of same class
- `super()`: Calls constructor of parent class

**Q10: Why constructor cannot be static?**
- Static belongs to class, constructor belongs to object initialization

---

## 🎯 Encapsulation

### What is Encapsulation?

**Definition:** Encapsulation is the bundling of data (variables) and methods that operate on the data into a single unit (class), and restricting direct access to some of the object's components. It's also known as **data hiding**.

### Real-World Analogy

**Medicine Capsule:**
- The medicine (data) is wrapped inside a capsule (class)
- You don't see the medicine directly
- You consume it as a whole unit
- The internal composition is hidden from you

**ATM Machine:**
- You withdraw money (method)
- You don't know the internal mechanism (data hiding)
- You interact through a simple interface (buttons)
- Internal cash storage is protected

---

### Why Encapsulation?

✅ **Data Security**: Protects data from unauthorized access  
✅ **Data Hiding**: Internal implementation hidden  
✅ **Flexibility**: Can change implementation without affecting external code  
✅ **Control**: Validation logic in setter methods  
✅ **Read-Only/Write-Only**: Can create read-only (only getter) or write-only (only setter) classes  
✅ **Maintainability**: Easy to maintain and modify  

---

### How to Achieve Encapsulation?

**Three Steps:**

1. **Declare variables as `private`**
2. **Provide public `getter` methods to read values**
3. **Provide public `setter` methods to modify values (with validation)**

---

### Complete Example: BankAccount

```java
public class BankAccount {
    // ========== PRIVATE DATA (Encapsulated) ==========
    private String accountNumber;
    private String accountHolderName;
    private double balance;
    private String pin;  // Highly sensitive data
    
    // ========== CONSTRUCTOR ==========
    public BankAccount(String accountNumber, String accountHolderName, String pin) {
        this.accountNumber = accountNumber;
        this.accountHolderName = accountHolderName;
        this.balance = 0.0;
        this.pin = pin;
    }
    
    // ========== PUBLIC GETTERS (Read Access) ==========
    
    public String getAccountNumber() {
        return accountNumber;
    }
    
    public String getAccountHolderName() {
        return accountHolderName;
    }
    
    public double getBalance() {
        return balance;
    }
    
    // ❌ NO getter for PIN - Write-only field for security
    
    // ========== PUBLIC SETTERS (Write Access with Validation) ==========
    
    public void setAccountHolderName(String accountHolderName) {
        if (accountHolderName != null && !accountHolderName.isEmpty()) {
            this.accountHolderName = accountHolderName;
        } else {
            System.out.println("Invalid name!");
        }
    }
    
    // ❌ NO setter for accountNumber - Read-only after creation
    // ❌ NO setter for balance - Modified only through deposit/withdraw
    
    // ========== BUSINESS METHODS (Controlled Access) ==========
    
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited: $" + amount);
            System.out.println("New Balance: $" + balance);
        } else {
            System.out.println("Invalid deposit amount!");
        }
    }
    
    public void withdraw(double amount, String enteredPin) {
        // Validate PIN first
        if (!this.pin.equals(enteredPin)) {
            System.out.println("❌ Incorrect PIN!");
            return;
        }
        
        // Validate amount
        if (amount <= 0) {
            System.out.println("❌ Invalid withdrawal amount!");
            return;
        }
        
        // Check sufficient balance
        if (amount > balance) {
            System.out.println("❌ Insufficient balance!");
            return;
        }
        
        // Perform withdrawal
        balance -= amount;
        System.out.println("✅ Withdrawn: $" + amount);
        System.out.println("Remaining Balance: $" + balance);
    }
    
    public void changePin(String oldPin, String newPin) {
        if (this.pin.equals(oldPin)) {
            this.pin = newPin;
            System.out.println("✅ PIN changed successfully!");
        } else {
            System.out.println("❌ Incorrect old PIN!");
        }
    }
    
    public void displayAccountInfo() {
        System.out.println("=== Account Information ===");
        System.out.println("Account Number: " + accountNumber);
        System.out.println("Account Holder: " + accountHolderName);
        System.out.println("Balance: $" + balance);
        // Note: PIN is never displayed (security)
    }
}

// ========== USAGE ==========
public class Main {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("1234567890", "John Doe", "1234");
        
        // ❌ CANNOT access private data directly
        // account.balance = 1000000;  // Compilation Error!
        // account.pin = "0000";       // Compilation Error!
        
        // ✅ CAN access through public methods
        account.displayAccountInfo();
        
        account.deposit(1000);
        account.deposit(-500);  // Invalid, will be rejected
        
        account.withdraw(200, "1234");   // Correct PIN
        account.withdraw(200, "0000");   // Wrong PIN
        account.withdraw(2000, "1234");  // Insufficient balance
        
        account.changePin("1234", "5678");
        
        // Using getters
        System.out.println("\nAccount holder: " + account.getAccountHolderName());
        System.out.println("Current balance: $" + account.getBalance());
    }
}
```

**Output:**
```
=== Account Information ===
Account Number: 1234567890
Account Holder: John Doe
Balance: $0.0
Deposited: $1000.0
New Balance: $1000.0
Invalid deposit amount!
✅ Withdrawn: $200.0
Remaining Balance: $800.0
❌ Incorrect PIN!
❌ Insufficient balance!
✅ PIN changed successfully!

Account holder: John Doe
Current balance: $800.0
```

---

### Getter and Setter Naming Convention

```java
public class Student {
    private int age;
    private boolean active;
    
    // Getter: get + FieldName (capitalize first letter)
    public int getAge() {
        return age;
    }
    
    // Setter: set + FieldName
    public void setAge(int age) {
        if (age > 0 && age < 150) {  // Validation
            this.age = age;
        } else {
            throw new IllegalArgumentException("Invalid age!");
        }
    }
    
    // For boolean: is + FieldName (instead of get)
    public boolean isActive() {
        return active;
    }
    
    public void setActive(boolean active) {
        this.active = active;
    }
}
```

---

### Read-Only Class (Only Getters)

**Use Case:** Immutable objects, configuration settings

```java
public class ReadOnlyStudent {
    private final String name;  // final = cannot be changed after initialization
    private final int rollNo;
    
    public ReadOnlyStudent(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }
    
    // Only getters, no setters
    public String getName() {
        return name;
    }
    
    public int getRollNo() {
        return rollNo;
    }
    
    // ❌ No setter methods - Object is immutable
}

// Usage
ReadOnlyStudent student = new ReadOnlyStudent("John", 101);
System.out.println(student.getName());  // ✅ Can read
// student.setName("Alice");  // ❌ No such method - Cannot modify
```

---

### Write-Only Class (Only Setters)

**Use Case:** Password setting, security-sensitive data

```java
public class PasswordManager {
    private String password;
    
    // Only setter, no getter
    public void setPassword(String password) {
        // Validation logic
        if (password.length() >= 8) {
            this.password = password;
            System.out.println("✅ Password set successfully!");
        } else {
            System.out.println("❌ Password must be at least 8 characters!");
        }
    }
    
    // ❌ No getter - Cannot retrieve password
    
    // Instead, verification method
    public boolean verifyPassword(String enteredPassword) {
        return this.password.equals(enteredPassword);
    }
}

// Usage
PasswordManager pm = new PasswordManager();
pm.setPassword("MySecret123");  // ✅ Can set
// String pwd = pm.getPassword();  // ❌ No such method - Cannot retrieve
boolean isValid = pm.verifyPassword("MySecret123");  // ✅ Can verify
```

---

### Benefits of Encapsulation with Examples

#### 1️⃣ Data Validation

```java
public class Employee {
    private int age;
    
    public void setAge(int age) {
        if (age >= 18 && age <= 65) {
            this.age = age;
        } else {
            throw new IllegalArgumentException("Age must be between 18 and 65");
        }
    }
}
```

#### 2️⃣ Flexibility to Change Implementation

```java
// Version 1: Store age directly
public class Person {
    private int age;
    
    public int getAge() {
        return age;
    }
}

// Version 2: Calculate age from birth year (implementation changed)
public class Person {
    private int birthYear;
    
    public int getAge() {
        return 2026 - birthYear;  // Calculated dynamically
    }
}
// External code using getAge() doesn't need to change!
```

#### 3️⃣ Control Over Data

```java
public class Counter {
    private int count = 0;
    
    // Only increment allowed, not direct setting
    public void increment() {
        count++;
    }
    
    public int getCount() {
        return count;
    }
    
    // ❌ No setCount() - Prevents arbitrary values
}
```

---

### 🔥 Interview Questions: Encapsulation

**Q1: What is encapsulation?**
- Bundling data and methods together, restricting direct access to data, achieved using private variables and public getters/setters

**Q2: How do you achieve encapsulation in Java?**
- Declare variables as private
- Provide public getter and setter methods

**Q3: What is data hiding?**
- Making data members private so they cannot be accessed directly from outside the class

**Q4: What is the difference between encapsulation and abstraction?**
- **Encapsulation**: Data hiding (how to hide)
- **Abstraction**: Hiding complexity (what to hide)

**Q5: Can we have encapsulation without getters and setters?**
- Yes, if we have other public methods that operate on private data

**Q6: Why use getters and setters instead of public variables?**
- Validation logic, future flexibility, control over access, calculated values

**Q7: What is a POJO (Plain Old Java Object)?**
- Simple Java class with private fields and public getters/setters

**Q8: Can we make a read-only class?**
- Yes, provide only getters, use `final` keyword for fields

**Q9: What are JavaBeans?**
- Classes following conventions: private fields, public getters/setters, no-arg constructor, serializable

**Q10: Is encapsulation same as data hiding?**
- Data hiding is a part of encapsulation, encapsulation also includes bundling data and methods

---

## 🎯 Inheritance

### What is Inheritance?

**Definition:** Inheritance is a mechanism where a new class (child/subclass) acquires properties and behaviors of an existing class (parent/superclass). It represents an **IS-A** relationship.

### Real-World Analogy

**Family Tree:**
- Child inherits traits from parents (eye color, height)
- Child has their own unique features too
- Grandchild inherits from both parent and grandparent

**Vehicle Hierarchy:**
- Vehicle (parent) → Car, Bike, Truck (children)
- Car IS-A Vehicle
- Car inherits common properties (wheels, engine) and has specific features (4 wheels, AC)

---

### Why Use Inheritance?

✅ **Code Reusability**: Reuse existing code without rewriting  
✅ **Method Overriding**: Runtime polymorphism  
✅ **Extensibility**: Easy to add new features  
✅ **Organization**: Logical hierarchy  
✅ **Maintenance**: Changes in parent reflect in children  

---

### Syntax

```java
// Parent Class (Super class / Base class)
class ParentClass {
    // fields and methods
}

// Child Class (Sub class / Derived class)
class ChildClass extends ParentClass {
    // inherits parent fields and methods
    // can add its own fields and methods
}
```

---

### Basic Example

```java
// ========== PARENT CLASS ==========
class Animal {
    protected String name;
    protected int age;
    
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public void eat() {
        System.out.println(name + " is eating...");
    }
    
    public void sleep() {
        System.out.println(name + " is sleeping...");
    }
    
    public void displayInfo() {
        System.out.println("Name: " + name + ", Age: " + age);
    }
}

// ========== CHILD CLASS ==========
class Dog extends Animal {
    private String breed;
    
    // Constructor
    public Dog(String name, int age, String breed) {
        super(name, age);  // Call parent constructor
        this.breed = breed;
    }
    
    // Dog-specific method
    public void bark() {
        System.out.println(name + " is barking: Woof! Woof!");
    }
    
    // Override parent method
    @Override
    public void displayInfo() {
        super.displayInfo();  // Call parent method
        System.out.println("Breed: " + breed);
    }
}

// ========== ANOTHER CHILD CLASS ==========
class Cat extends Animal {
    private String color;
    
    public Cat(String name, int age, String color) {
        super(name, age);
        this.color = color;
    }
    
    // Cat-specific method
    public void meow() {
        System.out.println(name + " is meowing: Meow! Meow!");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Color: " + color);
    }
}

// ========== USAGE ==========
public class Main {
    public static void main(String[] args) {
        // Create Dog object
        Dog dog = new Dog("Buddy", 3, "Golden Retriever");
        dog.displayInfo();   // Inherited + overridden
        dog.eat();           // Inherited from Animal
        dog.sleep();         // Inherited from Animal
        dog.bark();          // Dog-specific
        
        System.out.println("\n---\n");
        
        // Create Cat object
        Cat cat = new Cat("Whiskers", 2, "White");
        cat.displayInfo();
        cat.eat();
        cat.sleep();
        cat.meow();
    }
}
```

**Output:**
```
Name: Buddy, Age: 3
Breed: Golden Retriever
Buddy is eating...
Buddy is sleeping...
Buddy is barking: Woof! Woof!

---

Name: Whiskers, Age: 2
Color: White
Whiskers is eating...
Whiskers is sleeping...
Whiskers is meowing: Meow! Meow!
```

---

### Types of Inheritance in Java

```
Types of Inheritance:

1. Single Inheritance:       A → B

2. Multilevel Inheritance:   A → B → C

3. Hierarchical Inheritance:     A
                               /   \
                              B     C

4. Multiple Inheritance:     A   B
                              \ /
                               C
                          (❌ NOT supported with classes)
                          (✅ Supported with interfaces)

5. Hybrid Inheritance:   A
                        / \
                       B   C
                        \ /
                         D
                    (❌ NOT supported with classes)
```

---

#### 1️⃣ Single Inheritance

**Definition:** One class inherits from one parent class.

```java
// Parent
class Vehicle {
    void start() {
        System.out.println("Vehicle started");
    }
}

// Child
class Car extends Vehicle {
    void drive() {
        System.out.println("Car is driving");
    }
}

// Usage
Car car = new Car();
car.start();  // Inherited
car.drive();  // Own method
```

**Diagram:**
```
Vehicle (Parent)
   ↓
 Car (Child)
```

---

#### 2️⃣ Multilevel Inheritance

**Definition:** Chain of inheritance - class inherits from a class that is already inherited.

```java
// Level 1
class Vehicle {
    void start() {
        System.out.println("Vehicle started");
    }
}

// Level 2
class Car extends Vehicle {
    void drive() {
        System.out.println("Car is driving");
    }
}

// Level 3
class SportsCar extends Car {
    void turboBoost() {
        System.out.println("Turbo boost activated!");
    }
}

// Usage
SportsCar sc = new SportsCar();
sc.start();       // From Vehicle (grandparent)
sc.drive();       // From Car (parent)
sc.turboBoost();  // Own method
```

**Diagram:**
```
Vehicle (Grandparent)
   ↓
 Car (Parent)
   ↓
SportsCar (Child)
```

---

#### 3️⃣ Hierarchical Inheritance

**Definition:** Multiple classes inherit from a single parent class.

```java
// Parent
class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    void display() {
        System.out.println("Color: " + color);
    }
}

// Child 1
class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    double area() {
        return Math.PI * radius * radius;
    }
}

// Child 2
class Rectangle extends Shape {
    private double length, width;
    
    public Rectangle(String color, double length, double width) {
        super(color);
        this.length = length;
        this.width = width;
    }
    
    double area() {
        return length * width;
    }
}

// Child 3
class Triangle extends Shape {
    private double base, height;
    
    public Triangle(String color, double base, double height) {
        super(color);
        this.base = base;
        this.height = height;
    }
    
    double area() {
        return 0.5 * base * height;
    }
}

// Usage
Circle c = new Circle("Red", 5);
Rectangle r = new Rectangle("Blue", 4, 6);
Triangle t = new Triangle("Green", 3, 4);

c.display();
System.out.println("Circle area: " + c.area());

r.display();
System.out.println("Rectangle area: " + r.area());

t.display();
System.out.println("Triangle area: " + t.area());
```

**Diagram:**
```
      Shape (Parent)
      /     |      \
  Circle  Rectangle  Triangle
(Children)
```

---

#### 4️⃣ Multiple Inheritance (❌ NOT Supported with Classes)

**Definition:** One class inheriting from multiple parent classes.

**Why NOT Supported?**

**The Diamond Problem:**
```java
// This is NOT valid in Java - demonstration only

class Parent1 {
    void display() {
        System.out.println("Parent 1");
    }
}

class Parent2 {
    void display() {
        System.out.println("Parent 2");
    }
}

// ❌ ILLEGAL - Java doesn't allow this
class Child extends Parent1, Parent2 {
    // Which display() method should Child inherit?
    // Ambiguity! This is the Diamond Problem
}

// When we call:
Child c = new Child();
c.display();  // ??? Which display() - Parent1's or Parent2's?
```

**Diamond Problem Visualization:**
```
  Parent1      Parent2
    \          /
     \        /
      \      /
        Child
        
   Which parent's method to use? 💥 AMBIGUOUS!
```

---

**✅ Solution: Multiple Inheritance Through Interfaces**

```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

// ✅ Multiple interfaces allowed
class Duck implements Flyable, Swimmable {
    @Override
    public void fly() {
        System.out.println("Duck is flying");
    }
    
    @Override
    public void swim() {
        System.out.println("Duck is swimming");
    }
}

// Usage
Duck duck = new Duck();
duck.fly();
duck.swim();
```

---

#### 5️⃣ Hybrid Inheritance (❌ NOT Supported)

**Definition:** Combination of multiple and hierarchical inheritance.

```java
// ❌ NOT Supported in Java with classes
// Can be achieved using interfaces

interface A {
    void methodA();
}

interface B extends A {
    void methodB();
}

interface C extends A {
    void methodC();
}

class D implements B, C {
    public void methodA() { System.out.println("Method A"); }
    public void methodB() { System.out.println("Method B"); }
    public void methodC() { System.out.println("Method C"); }
}
```

---

### Access Modifiers in Inheritance

```java
class Parent {
    public int publicVar = 10;       // ✅ Accessible everywhere
    protected int protectedVar = 20; // ✅ Accessible in subclass
    int defaultVar = 30;             // ✅ Accessible in same package
    private int privateVar = 40;     // ❌ NOT accessible in subclass
    
    public void display() {
        System.out.println("All variables accessible in Parent");
    }
}

class Child extends Parent {
    public void show() {
        System.out.println(publicVar);     // ✅ OK
        System.out.println(protectedVar);  // ✅ OK
        System.out.println(defaultVar);    // ✅ OK (if same package)
        // System.out.println(privateVar); // ❌ ERROR: private not inherited
    }
}
```

**Access Level Table:**

| Modifier | Same Class | Same Package | Subclass | Other Packages |
|----------|------------|--------------|----------|----------------|
| **public** | ✅ | ✅ | ✅ | ✅ |
| **protected** | ✅ | ✅ | ✅ | ❌ |
| **default** | ✅ | ✅ | ❌ | ❌ |
| **private** | ✅ | ❌ | ❌ | ❌ |

---

### The `super` Keyword

**Purpose:** Refers to the immediate parent class object.

**Uses:**

#### 1️⃣ Access Parent Class Variables

```java
class Parent {
    int x = 10;
}

class Child extends Parent {
    int x = 20;
    
    void display() {
        System.out.println("Child x: " + x);         // 20 (child's x)
        System.out.println("Parent x: " + super.x);  // 10 (parent's x)
    }
}
```

---

#### 2️⃣ Call Parent Class Constructor

```java
class Parent {
    Parent() {
        System.out.println("Parent constructor");
    }
    
    Parent(String message) {
        System.out.println("Parent: " + message);
    }
}

class Child extends Parent {
    Child() {
        super();  // Calls Parent()
        System.out.println("Child constructor");
    }
    
    Child(String message) {
        super(message);  // Calls Parent(String)
        System.out.println("Child: " + message);
    }
}

// Usage
Child c1 = new Child();
// Output:
// Parent constructor
// Child constructor

Child c2 = new Child("Hello");
// Output:
// Parent: Hello
// Child: Hello
```

**Important Rules for `super()`:**
- Must be the **first statement** in child constructor
- If not explicitly called, Java automatically adds `super()` (no-arg)
- Cannot use both `this()` and `super()` in same constructor

---

#### 3️⃣ Call Parent Class Methods

```java
class Parent {
    void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    @Override
    void display() {
        super.display();  // Call parent's display()
        System.out.println("Child display");
    }
}

// Usage
Child c = new Child();
c.display();
// Output:
// Parent display
// Child display
```

---

### Constructor Chaining in Inheritance

```java
class GrandParent {
    GrandParent() {
        System.out.println("1. GrandParent constructor");
    }
}

class Parent extends GrandParent {
    Parent() {
        super();  // Implicit call even if not written
        System.out.println("2. Parent constructor");
    }
}

class Child extends Parent {
    Child() {
        super();  // Implicit call even if not written
        System.out.println("3. Child constructor");
    }
}

// Usage
Child c = new Child();
// Output:
// 1. GrandParent constructor
// 2. Parent constructor
// 3. Child constructor

// Constructors execute from top to bottom in hierarchy!
```

---

### Method Overriding in Inheritance

**Definition:** Child class provides a specific implementation of a method already defined in parent class.

```java
class Animal {
    void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Dog barks: Woof!");
    }
}

class Cat extends Animal {
    @Override
    void makeSound() {
        System.out.println("Cat meows: Meow!");
    }
}

// Usage
Animal a1 = new Dog();
Animal a2 = new Cat();

a1.makeSound();  // Dog barks: Woof!
a2.makeSound();  // Cat meows: Meow!
```

*Detailed explanation in Polymorphism section*

---

### What is Inherited?

| Member Type | Inherited? | Notes |
|-------------|-----------|--------|
| **Public fields** | ✅ Yes | Accessible directly |
| **Protected fields** | ✅ Yes | Accessible directly |
| **Default fields** | ✅ Yes (same package) | Only in same package |
| **Private fields** | ❌ No | Not accessible directly |
| **Public methods** | ✅ Yes | Can be overridden |
| **Protected methods** | ✅ Yes | Can be overridden |
| **Default methods** | ✅ Yes (same package) | Can be overridden |
| **Private methods** | ❌ No | Not inherited |
| **Constructors** | ❌ No | Never inherited |
| **Static methods** | ✅ Yes | But not overridden (hidden) |
| **Final methods** | ✅ Yes | But cannot be overridden |

---

### The `final` Keyword with Inheritance

#### 1️⃣ `final` Class - Cannot be Inherited

```java
final class FinalClass {
    void display() {
        System.out.println("Final class");
    }
}

// ❌ ERROR: Cannot inherit from final class
// class Child extends FinalClass { }

// Example: String class is final
// class MyString extends String { }  // ❌ ERROR
```

---

#### 2️⃣ `final` Method - Cannot be Overridden

```java
class Parent {
    final void display() {
        System.out.println("Final method");
    }
}

class Child extends Parent {
    // ❌ ERROR: Cannot override final method
    // void display() { }
}
```

---

#### 3️⃣ `final` Variable - Cannot be Changed

```java
class Example {
    final int MAX_VALUE = 100;
    
    void modify() {
        // MAX_VALUE = 200;  // ❌ ERROR: Cannot modify final variable
    }
}
```

---

### IS-A vs HAS-A Relationship

#### IS-A (Inheritance)

```java
class Animal { }
class Dog extends Animal { }

// Dog IS-A Animal ✅
```

#### HAS-A (Composition)

```java
class Engine { }

class Car {
    private Engine engine;  // Car HAS-A Engine ✅
    
    public Car() {
        engine = new Engine();
    }
}
```

**When to Use:**
- **IS-A (Inheritance)**: When there's a clear parent-child relationship
- **HAS-A (Composition)**: When one class contains another class as a member

---

### 🔥 Interview Questions: Inheritance

**Q1: What is inheritance?**
- Mechanism where child class acquires properties and behaviors of parent class

**Q2: Types of inheritance in Java?**
- Single, Multilevel, Hierarchical
- Multiple and Hybrid NOT supported with classes (but with interfaces)

**Q3: Why multiple inheritance is not supported in Java?**
- To avoid Diamond Problem (ambiguity when two parents have same method)

**Q4: Can we achieve multiple inheritance in Java?**
- Yes, using interfaces

**Q5: What is the diamond problem?**
- Ambiguity when a class inherits from two classes that have the same method

**Q6: What is the use of `super` keyword?**
- Refer to parent class variable, method, or constructor

**Q7: Is constructor inherited?**
- No, constructors are never inherited

**Q8: Can we override private methods?**
- No, private methods are not inherited

**Q9: Can we override static methods?**
- No, static methods are hidden, not overridden

**Q10: Can we override final methods?**
- No, final methods cannot be overridden

**Q11: What is method overriding?**
- Child class provides specific implementation of parent's method

**Q12: Difference between method overloading and overriding?**
- Overloading: Same method name, different parameters, same class
- Overriding: Same method signature, parent-child relationship

**Q13: Can we inherit from multiple classes?**
- No, but can implement multiple interfaces

**Q14: What is the difference between `super()` and `super.method()`?**
- `super()`: Calls parent constructor
- `super.method()`: Calls parent method

**Q15: Can a subclass have more restricted access than superclass method?**
- No, overriding method cannot have more restrictive access modifier

---

## 🎯 Polymorphism

### What is Polymorphism?

**Definition:** Polymorphism means "many forms". It's the ability of an object to take many forms. One interface, multiple implementations.

### Real-World Analogy

**A Person:**
- Same person behaves differently in different situations
- At office → Employee
- At home → Parent/Child
- At school → Teacher/Student
- Same person, multiple roles!

**Smartphone:**
- Can act as Phone, Camera, Music Player, Calculator
- Same device, multiple functionalities!

---

### Types of Polymorphism

```
            Polymorphism
                |
        ┌───────┴───────┐
        |               |
  Compile-time      Runtime
  (Static)          (Dynamic)
        |               |
  Method          Method
  Overloading     Overriding
```

---

## 🎯 Compile-time Polymorphism (Method Overloading)

### What is Method Overloading?

**Definition:** Having multiple methods with the **same name** but **different parameters** in the same class.

**Also called:**
- Compile-time Polymorphism
- Static Polymorphism
- Early Binding

---

### Rules for Method Overloading

✅ **Must have different parameters**:
   - Different number of parameters
   - Different types of parameters
   - Different order of parameters

❌ **Return type alone is NOT enough**

✅ **Can have different return types** (but parameters must be different)

✅ **Can have different access modifiers**

✅ **Can throw different exceptions**

---

### Ways to Overload Methods

#### 1️⃣ Different Number of Parameters

```java
class Calculator {
    // Method with 2 parameters
    int add(int a, int b) {
        return a + b;
    }
    
    // Method with 3 parameters
    int add(int a, int b, int c) {
        return a + b + c;
    }
    
    // Method with 4 parameters
    int add(int a, int b, int c, int d) {
        return a + b + c + d;
    }
}

// Usage
Calculator calc = new Calculator();
System.out.println(calc.add(10, 20));          // 30
System.out.println(calc.add(10, 20, 30));      // 60
System.out.println(calc.add(10, 20, 30, 40));  // 100
```

---

#### 2️⃣ Different Types of Parameters

```java
class Printer {
    // Print integer
    void print(int num) {
        System.out.println("Integer: " + num);
    }
    
    // Print double
    void print(double num) {
        System.out.println("Double: " + num);
    }
    
    // Print string
    void print(String text) {
        System.out.println("String: " + text);
    }
    
    // Print boolean
    void print(boolean flag) {
        System.out.println("Boolean: " + flag);
    }
}

// Usage
Printer printer = new Printer();
printer.print(10);         // Integer: 10
printer.print(10.5);       // Double: 10.5
printer.print("Hello");    // String: Hello
printer.print(true);       // Boolean: true
```

---

#### 3️⃣ Different Order of Parameters

```java
class Display {
    // Order: int, String
    void show(int num, String text) {
        System.out.println("Number: " + num + ", Text: " + text);
    }
    
    // Order: String, int
    void show(String text, int num) {
        System.out.println("Text: " + text + ", Number: " + num);
    }
}

// Usage
Display display = new Display();
display.show(10, "Hello");    // Number: 10, Text: Hello
display.show("Hello", 10);    // Text: Hello, Number: 10
```

---

### Complete Example: Calculator

```java
public class Calculator {
    // Add two integers
    public int add(int a, int b) {
        System.out.println("Adding two integers");
        return a + b;
    }
    
    // Add three integers
    public int add(int a, int b, int c) {
        System.out.println("Adding three integers");
        return a + b + c;
    }
    
    // Add two doubles
    public double add(double a, double b) {
        System.out.println("Adding two doubles");
        return a + b;
    }
    
    // Add integer and double
    public double add(int a, double b) {
        System.out.println("Adding int and double");
        return a + b;
    }
    
    // Add array of integers
    public int add(int[] numbers) {
        System.out.println("Adding array of integers");
        int sum = 0;
        for (int num : numbers) {
            sum += num;
        }
        return sum;
    }
    
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        
        System.out.println("Result: " + calc.add(10, 20));           // 30
        System.out.println("Result: " + calc.add(10, 20, 30));       // 60
        System.out.println("Result: " + calc.add(10.5, 20.5));       // 31.0
        System.out.println("Result: " + calc.add(10, 20.5));         // 30.5
        System.out.println("Result: " + calc.add(new int[]{1,2,3,4})); // 10
    }
}
```

---

### Constructor Overloading

```java
public class Student {
    private String name;
    private int age;
    private String course;
    
    // Constructor 1: No parameters
    public Student() {
        this.name = "Unknown";
        this.age = 0;
        this.course = "Not Assigned";
    }
    
    // Constructor 2: Name only
    public Student(String name) {
        this.name = name;
        this.age = 0;
        this.course = "Not Assigned";
    }
    
    // Constructor 3: Name and age
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
        this.course = "Not Assigned";
    }
    
    // Constructor 4: All parameters
    public Student(String name, int age, String course) {
        this.name = name;
        this.age = age;
        this.course = course;
    }
    
    public void display() {
        System.out.println("Name: " + name + ", Age: " + age + ", Course: " + course);
    }
    
    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student("John");
        Student s3 = new Student("Alice", 20);
        Student s4 = new Student("Bob", 22, "Computer Science");
        
        s1.display();
        s2.display();
        s3.display();
        s4.display();
    }
}
```

---

### Type Promotion in Overloading

**Automatic Type Conversion:**
```
byte → short → int → long → float → double
         ↓
       char → int
```

```java
class Example {
    void method(int a) {
        System.out.println("int method: " + a);
    }
    
    void method(double a) {
        System.out.println("double method: " + a);
    }
}

// Usage
Example ex = new Example();
ex.method(10);      // int method: 10 (exact match)
ex.method(10.5);    // double method: 10.5 (exact match)

byte b = 5;
ex.method(b);       // int method: 5 (byte promoted to int)

float f = 5.5f;
ex.method(f);       // double method: 5.5 (float promoted to double)
```

---

### ❌ Invalid Overloading Examples

#### 1. Only Return Type Different

```java
class Invalid {
    int method(int a) {
        return a;
    }
    
    // ❌ ERROR: Same parameters, only return type different
    // double method(int a) {
    //     return a;
    // }
}
```

#### 2. Only Parameter Names Different

```java
class Invalid {
    int method(int a) {
        return a;
    }
    
    // ❌ ERROR: Parameter names don't matter, types are same
    // int method(int b) {
    //     return b;
    // }
}
```

---

## 🎯 Runtime Polymorphism (Method Overriding)

### What is Method Overriding?

**Definition:** When a child class provides a **specific implementation** of a method that is already defined in its parent class.

**Also called:**
- Runtime Polymorphism
- Dynamic Polymorphism
- Late Binding
- Dynamic Method Dispatch

---

### Rules for Method Overriding

✅ **Inheritance required** (IS-A relationship)

✅ **Same method signature**:
   - Same method name
   - Same parameters (number, type, order)
   - Same or covariant return type

✅ **@Override annotation** (recommended but optional)

✅ **Access modifier cannot be more restrictive**

❌ **Cannot override**:
   - Private methods (not inherited)
   - Final methods
   - Static methods (hidden, not overridden)

✅ **Exception rules**:
   - Can throw same, subclass, or no exception (checked)
   - Can throw any unchecked exception

---

### Basic Example

```java
// ========== PARENT CLASS ==========
class Animal {
    void makeSound() {
        System.out.println("Animal makes a sound");
    }
    
    void eat() {
        System.out.println("Animal is eating");
    }
}

// ========== CHILD CLASS 1 ==========
class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Dog barks: Woof! Woof!");
    }
    
    @Override
    void eat() {
        System.out.println("Dog is eating bones");
    }
}

// ========== CHILD CLASS 2 ==========
class Cat extends Animal {
    @Override
    void makeSound() {
        System.out.println("Cat meows: Meow! Meow!");
    }
    
    @Override
    void eat() {
        System.out.println("Cat is drinking milk");
    }
}

// ========== USAGE ==========
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal();
        animal.makeSound();  // Animal makes a sound
        animal.eat();        // Animal is eating
        
        System.out.println("\n---\n");
        
        Dog dog = new Dog();
        dog.makeSound();     // Dog barks: Woof! Woof!
        dog.eat();           // Dog is eating bones
        
        System.out.println("\n---\n");
        
        Cat cat = new Cat();
        cat.makeSound();     // Cat meows: Meow! Meow!
        cat.eat();           // Cat is drinking milk
    }
}
```

---

### Dynamic Method Dispatch

**Definition:** The process by which a call to an overridden method is resolved at **runtime** rather than compile-time.

```java
class Shape {
    void draw() {
        System.out.println("Drawing a shape");
    }
}

class Circle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a circle");
    }
}

class Rectangle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a rectangle");
    }
}

class Triangle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a triangle");
    }
}

// ========== DYNAMIC METHOD DISPATCH ==========
public class Main {
    public static void main(String[] args) {
        // Reference type: Shape (Parent)
        // Object type: varies (Child)
        
        Shape shape;  // Reference variable
        
        shape = new Circle();
        shape.draw();  // Drawing a circle (Circle's method)
        
        shape = new Rectangle();
        shape.draw();  // Drawing a rectangle (Rectangle's method)
        
        shape = new Triangle();
        shape.draw();  // Drawing a triangle (Triangle's method)
        
        // Same reference variable, different behaviors!
        // Decision made at RUNTIME based on object type
    }
}
```

**Key Point:**
- **Reference type** determines which **objects** can be assigned
- **Object type** determines which **method implementation** is executed

---

### Real-World Example: Payment System

```java
// ========== PARENT CLASS ==========
abstract class Payment {
    protected double amount;
    
    public Payment(double amount) {
        this.amount = amount;
    }
    
    // Abstract method - must be overridden
    abstract void processPayment();
    
    // Concrete method - can be overridden
    void showReceipt() {
        System.out.println("Payment of $" + amount + " processed successfully");
    }
}

// ========== CHILD CLASSES ==========
class CreditCardPayment extends Payment {
    private String cardNumber;
    
    public CreditCardPayment(double amount, String cardNumber) {
        super(amount);
        this.cardNumber = cardNumber;
    }
    
    @Override
    void processPayment() {
        System.out.println("Processing Credit Card payment...");
        System.out.println("Card Number: " + maskCardNumber(cardNumber));
        System.out.println("Amount: $" + amount);
    }
    
    private String maskCardNumber(String card) {
        return "****-****-****-" + card.substring(card.length() - 4);
    }
}

class PayPalPayment extends Payment {
    private String email;
    
    public PayPalPayment(double amount, String email) {
        super(amount);
        this.email = email;
    }
    
    @Override
    void processPayment() {
        System.out.println("Processing PayPal payment...");
        System.out.println("Email: " + email);
        System.out.println("Amount: $" + amount);
    }
}

class BitcoinPayment extends Payment {
    private String walletAddress;
    
    public BitcoinPayment(double amount, String walletAddress) {
        super(amount);
        this.walletAddress = walletAddress;
    }
    
    @Override
    void processPayment() {
        System.out.println("Processing Bitcoin payment...");
        System.out.println("Wallet: " + walletAddress.substring(0, 10) + "...");
        System.out.println("Amount: $" + amount);
    }
}

// ========== USAGE ==========
public class PaymentProcessor {
    // Polymorphic method - accepts any Payment type
    public static void executePayment(Payment payment) {
        payment.processPayment();
        payment.showReceipt();
        System.out.println("---");
    }
    
    public static void main(String[] args) {
        // Create different payment objects
        Payment payment1 = new CreditCardPayment(100.50, "1234567890123456");
        Payment payment2 = new PayPalPayment(250.75, "user@example.com");
        Payment payment3 = new BitcoinPayment(500.00, "1A2b3C4d5E6f7G8h9I0j");
        
        // Process all payments using same method!
        executePayment(payment1);
        executePayment(payment2);
        executePayment(payment3);
        
        // One interface (Payment), multiple implementations!
    }
}
```

---

### Covariant Return Type (Java 5+)

**Definition:** Overriding method can return a **subtype** of the type returned by parent method.

```java
class Animal {
    Animal getAnimal() {
        System.out.println("Returning Animal");
        return new Animal();
    }
}

class Dog extends Animal {
    // ✅ ALLOWED: Dog is subtype of Animal
    @Override
    Dog getAnimal() {
        System.out.println("Returning Dog");
        return new Dog();
    }
}

class Cat extends Animal {
    // ✅ ALLOWED: Cat is subtype of Animal
    @Override
    Cat getAnimal() {
        System.out.println("Returning Cat");
        return new Cat();
    }
}

// Usage
Animal animal = new Dog();
animal.getAnimal();  // Returning Dog
```

---

### Access Modifiers in Overriding

**Rule:** Overriding method **cannot be more restrictive** than parent method.

```java
class Parent {
    public void method1() { }
    protected void method2() { }
    void method3() { }  // default
}

class Child extends Parent {
    // ✅ ALLOWED: Same or less restrictive
    @Override
    public void method1() { }  // public → public ✅
    
    @Override
    public void method2() { }  // protected → public ✅
    
    @Override
    protected void method3() { }  // default → protected ✅
}

class InvalidChild extends Parent {
    // ❌ ERROR: More restrictive
    // @Override
    // protected void method1() { }  // public → protected ❌
    
    // @Override
    // private void method2() { }  // protected → private ❌
}
```

**Access Modifier Hierarchy (Less to More Restrictive):**
```
public > protected > default > private
```

---

### Exception Rules in Overriding

```java
import java.io.*;

class Parent {
    // Parent throws IOException
    void method() throws IOException {
        System.out.println("Parent method");
    }
}

class Child1 extends Parent {
    // ✅ ALLOWED: Subclass exception (FileNotFoundException is subclass of IOException)
    @Override
    void method() throws FileNotFoundException {
        System.out.println("Child1 method");
    }
}

class Child2 extends Parent {
    // ✅ ALLOWED: No exception
    @Override
    void method() {
        System.out.println("Child2 method");
    }
}

class InvalidChild extends Parent {
    // ❌ ERROR: Cannot throw broader exception
    // @Override
    // void method() throws Exception {  // Exception is superclass of IOException
    //     System.out.println("Invalid method");
    // }
}
```

**Rules:**
- ✅ Can throw **same** exception
- ✅ Can throw **subclass** exception (checked)
- ✅ Can throw **no** exception
- ❌ Cannot throw **superclass** exception (checked)
- ✅ Can throw **any** unchecked exception

---

### Cannot Override: Static Methods

**Static methods are HIDDEN, not OVERRIDDEN**

```java
class Parent {
    static void staticMethod() {
        System.out.println("Parent static method");
    }
    
    void instanceMethod() {
        System.out.println("Parent instance method");
    }
}

class Child extends Parent {
    // This is METHOD HIDING, not overriding
    static void staticMethod() {
        System.out.println("Child static method");
    }
    
    // This is METHOD OVERRIDING
    @Override
    void instanceMethod() {
        System.out.println("Child instance method");
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Parent p1 = new Parent();
        Parent p2 = new Child();  // Upcasting
        Child c = new Child();
        
        // Static methods - determined by REFERENCE type
        p1.staticMethod();  // Parent static method
        p2.staticMethod();  // Parent static method (not overridden!)
        c.staticMethod();   // Child static method
        
        System.out.println("---");
        
        // Instance methods - determined by OBJECT type
        p1.instanceMethod();  // Parent instance method
        p2.instanceMethod();  // Child instance method (overridden!)
        c.instanceMethod();   // Child instance method
    }
}
```

---

### Cannot Override: Final Methods

```java
class Parent {
    final void finalMethod() {
        System.out.println("Final method");
    }
    
    void normalMethod() {
        System.out.println("Normal method");
    }
}

class Child extends Parent {
    // ❌ ERROR: Cannot override final method
    // @Override
    // void finalMethod() { }
    
    // ✅ ALLOWED: Can override normal method
    @Override
    void normalMethod() {
        System.out.println("Overridden method");
    }
}
```

---

### Cannot Override: Private Methods

```java
class Parent {
    private void privateMethod() {
        System.out.println("Private method");
    }
    
    public void callPrivateMethod() {
        privateMethod();
    }
}

class Child extends Parent {
    // This is NOT overriding, it's a NEW method
    // Private methods are not inherited
    private void privateMethod() {
        System.out.println("Child private method");
    }
    
    public void testMethod() {
        privateMethod();  // Calls Child's private method
    }
}

// Usage
Child c = new Child();
c.callPrivateMethod();  // Private method (Parent's)
c.testMethod();         // Child private method
```

---

### 🔥 Interview Questions: Polymorphism

**Q1: What is polymorphism?**
- Ability of an object to take many forms, one interface multiple implementations

**Q2: Types of polymorphism?**
- Compile-time (Method Overloading), Runtime (Method Overriding)

**Q3: What is method overloading?**
- Same method name, different parameters in same class

**Q4: What is method overriding?**
- Child class provides specific implementation of parent's method

**Q5: Can we overload main method?**
- Yes, but JVM calls only `public static void main(String[] args)`

**Q6: Can we override main method?**
- No, main method is static

**Q7: Can we override static methods?**
- No, static methods are hidden, not overridden

**Q8: Can we override final methods?**
- No, final methods cannot be overridden

**Q9: Can we override private methods?**
- No, private methods are not inherited

**Q10: What is dynamic method dispatch?**
- Runtime determination of which method to call based on object type

**Q11: Difference between overloading and overriding?**
- See comparison table below

**Q12: Can we change return type in overloading?**
- Yes, but parameters must be different

**Q13: Can we change return type in overriding?**
- Yes, but must be same or covariant (subtype)

**Q14: What is covariant return type?**
- Overriding method can return subtype of parent's return type

**Q15: Can overriding method throw checked exception?**
- Yes, but must be same, subclass, or no exception

---

## 🎯 Method Overloading vs Overriding

### Detailed Comparison Table

| Feature | Method Overloading | Method Overriding |
|---------|-------------------|-------------------|
| **Definition** | Same method name, different parameters | Child provides specific implementation |
| **Also Known As** | Compile-time Polymorphism | Runtime Polymorphism |
| **Binding** | Static/Early Binding | Dynamic/Late Binding |
| **Occurs** | Same class | Parent-child classes |
| **Inheritance** | ❌ Not required | ✅ Required |
| **Method Signature** | Must be different | Must be same |
| **Parameters** | Must be different | Must be same |
| **Return Type** | Can be different | Must be same or covariant |
| **Access Modifier** | Can be different | Cannot be more restrictive |
| **Static Method** | ✅ Can be overloaded | ❌ Cannot be overridden (hidden) |
| **Private Method** | ✅ Can be overloaded | ❌ Cannot be overridden |
| **Final Method** | ✅ Can be overloaded | ❌ Cannot be overridden |
| **Exception** | Can be different | Cannot throw broader checked exception |
| **@Override** | ❌ Not used | ✅ Recommended |
| **Purpose** | Add new behavior with different inputs | Change existing behavior |
| **Performance** | Faster (compile-time) | Slightly slower (runtime) |

---

### Side-by-Side Example

```java
// ========== METHOD OVERLOADING ==========
class Calculator {
    // Same method name, different parameters
    int add(int a, int b) {
        return a + b;
    }
    
    int add(int a, int b, int c) {
        return a + b + c;
    }
    
    double add(double a, double b) {
        return a + b;
    }
}

// Usage
Calculator calc = new Calculator();
calc.add(10, 20);        // Calls first method
calc.add(10, 20, 30);    // Calls second method
calc.add(10.5, 20.5);    // Calls third method

// ========== METHOD OVERRIDING ==========
class Animal {
    void makeSound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }
}

// Usage
Animal animal = new Dog();
animal.makeSound();  // Bark (overridden method called)
```

---

## 🎯 Abstraction

### What is Abstraction?

**Definition:** Abstraction is the process of **hiding implementation details** and showing only **essential features** to the user. It focuses on "WHAT an object does" rather than "HOW it does it".

### Real-World Analogy

**Car:**
- You know: Steering wheel, brake, accelerator (WHAT)
- You don't know: Engine mechanics, transmission system (HOW)
- Interface: Simple controls
- Implementation: Complex machinery (hidden)

**ATM Machine:**
- You see: Screen with options (WHAT)
- You don't see: Database connection, security encryption (HOW)
- You interact with simplified interface

**TV Remote:**
- You press: Volume up/down button (WHAT)
- Hidden: Electronic signals, infrared communication (HOW)

---

### Why Use Abstraction?

✅ **Reduces Complexity**: Hide complex implementation  
✅ **Improves Security**: Hide sensitive details  
✅ **Increases Reusability**: Define common interface  
✅ **Easy Maintenance**: Change implementation without affecting users  
✅ **Focus on Essential Features**: User sees only what's necessary  

---

### Ways to Achieve Abstraction

1. **Abstract Classes** (0-100% abstraction)
2. **Interfaces** (100% abstraction before Java 8)

---

## 🎯 Abstract Class

### What is an Abstract Class?

**Definition:** A class declared with `abstract` keyword that **cannot be instantiated** and may contain **abstract methods** (methods without body).

### Characteristics

✅ Cannot create objects directly  
✅ Can have abstract methods (without body)  
✅ Can have concrete methods (with body)  
✅ Can have constructors  
✅ Can have instance variables  
✅ Can have static methods  
✅ Can have final methods  
✅ Child class must implement all abstract methods  
✅ Use `extends` keyword  

---

### Syntax

```java
abstract class AbstractClass {
    // Abstract method (no body)
    abstract returnType methodName();
    
    // Concrete method (with body)
    returnType concreteMethod() {
        // implementation
    }
}
```

---

### Complete Example: Vehicle

```java
// ========== ABSTRACT CLASS ==========
abstract class Vehicle {
    // ===== INSTANCE VARIABLES =====
    protected String brand;
    protected int wheels;
    
    // ===== CONSTRUCTOR =====
    public Vehicle(String brand, int wheels) {
        this.brand = brand;
        this.wheels = wheels;
        System.out.println("Vehicle constructor called");
    }
    
    // ===== ABSTRACT METHODS (no body) =====
    abstract void start();      // Must be implemented by child
    abstract void stop();       // Must be implemented by child
    abstract void fuelType();   // Must be implemented by child
    
    // ===== CONCRETE METHODS (with body) =====
    public void displayInfo() {
        System.out.println("Brand: " + brand);
        System.out.println("Wheels: " + wheels);
    }
    
    public final void maintenance() {
        System.out.println("Regular maintenance required");
    }
    
    // ===== STATIC METHOD =====
    public static void vehicleInfo() {
        System.out.println("This is a vehicle class");
    }
}

// ========== CONCRETE CLASS 1: CAR ==========
class Car extends Vehicle {
    private int doors;
    
    public Car(String brand, int doors) {
        super(brand, 4);  // Car always has 4 wheels
        this.doors = doors;
        System.out.println("Car constructor called");
    }
    
    @Override
    void start() {
        System.out.println(brand + " car started with key");
    }
    
    @Override
    void stop() {
        System.out.println(brand + " car stopped");
    }
    
    @Override
    void fuelType() {
        System.out.println("Fuel type: Petrol/Diesel");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Doors: " + doors);
        System.out.println("Type: Car");
    }
}

// ========== CONCRETE CLASS 2: BIKE ==========
class Bike extends Vehicle {
    private boolean hasGear;
    
    public Bike(String brand, boolean hasGear) {
        super(brand, 2);  // Bike has 2 wheels
        this.hasGear = hasGear;
        System.out.println("Bike constructor called");
    }
    
    @Override
    void start() {
        System.out.println(brand + " bike started with kick");
    }
    
    @Override
    void stop() {
        System.out.println(brand + " bike stopped");
    }
    
    @Override
    void fuelType() {
        System.out.println("Fuel type: Petrol");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Has Gear: " + (hasGear ? "Yes" : "No"));
        System.out.println("Type: Bike");
    }
}

// ========== CONCRETE CLASS 3: TRUCK ==========
class Truck extends Vehicle {
    private int loadCapacity;
    
    public Truck(String brand, int loadCapacity) {
        super(brand, 6);  // Truck has 6 wheels
        this.loadCapacity = loadCapacity;
        System.out.println("Truck constructor called");
    }
    
    @Override
    void start() {
        System.out.println(brand + " truck started with ignition");
    }
    
    @Override
    void stop() {
        System.out.println(brand + " truck stopped with air brakes");
    }
    
    @Override
    void fuelType() {
        System.out.println("Fuel type: Diesel");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Load Capacity: " + loadCapacity + " tons");
        System.out.println("Type: Truck");
    }
}

// ========== USAGE ==========
public class Main {
    public static void main(String[] args) {
        // ❌ Cannot instantiate abstract class
        // Vehicle v = new Vehicle("Generic", 4);  // ERROR!
        
        // ✅ Create concrete objects
        System.out.println("===== Creating Car =====");
        Vehicle car = new Car("Toyota", 4);
        System.out.println();
        
        System.out.println("===== Car Operations =====");
        car.displayInfo();
        car.start();
        car.fuelType();
        car.maintenance();
        car.stop();
        System.out.println();
        
        System.out.println("===== Creating Bike =====");
        Vehicle bike = new Bike("Honda", true);
        System.out.println();
        
        System.out.println("===== Bike Operations =====");
        bike.displayInfo();
        bike.start();
        bike.fuelType();
        bike.stop();
        System.out.println();
        
        System.out.println("===== Creating Truck =====");
        Vehicle truck = new Truck("Volvo", 20);
        System.out.println();
        
        System.out.println("===== Truck Operations =====");
        truck.displayInfo();
        truck.start();
        truck.fuelType();
        truck.stop();
        System.out.println();
        
        // Static method call
        Vehicle.vehicleInfo();
    }
}
```

---

### Abstract Method Rules

```java
abstract class Rules {
    // ✅ VALID: Abstract method
    abstract void method1();
    
    // ✅ VALID: Abstract method with parameters
    abstract int method2(int a, int b);
    
    // ❌ INVALID: Abstract method cannot have body
    // abstract void method3() { }
    
    // ❌ INVALID: Abstract method cannot be private
    // private abstract void method4();
    
    // ❌ INVALID: Abstract method cannot be final
    // final abstract void method5();
    
    // ❌ INVALID: Abstract method cannot be static
    // static abstract void method6();
    
    // ✅ VALID: Concrete method can be anything
    private void privateMethod() { }
    public final void finalMethod() { }
    public static void staticMethod() { }
}
```

---

### When to Use Abstract Class?

✅ **When classes share common code** with some differences  
✅ **When you want default implementation** for some methods  
✅ **When you need constructors** to initialize state  
✅ **When you need instance variables**  
✅ **When you need access modifiers** other than public  

**Example Scenarios:**
- Vehicle (Car, Bike, Truck)
- Shape (Circle, Rectangle, Triangle)
- Employee (Manager, Developer, Tester)
- Payment (CreditCard, DebitCard, PayPal)

---

## 🎯 Interface

### What is an Interface?

**Definition:** An interface is a **reference type** similar to a class that contains only **abstract methods** (before Java 8). It's a **blueprint** of a class and achieves **100% abstraction**.

### Characteristics

✅ All methods are **public and abstract** by default (before Java 8)  
✅ All fields are **public, static, and final** (constants)  
✅ Cannot have constructors  
✅ Cannot be instantiated  
✅ A class can implement **multiple interfaces**  
✅ An interface can extend **multiple interfaces**  
✅ Use `implements` keyword  

**Java 8+:**  
✅ Can have **default methods** (with body)  
✅ Can have **static methods**  

**Java 9+:**  
✅ Can have **private methods**  

---

### Syntax

```java
interface InterfaceName {
    // Constants (public static final by default)
    int CONSTANT = 100;
    
    // Abstract methods (public abstract by default)
    void method1();
    void method2();
    
    // Default method (Java 8+)
    default void defaultMethod() {
        // implementation
    }
    
    // Static method (Java 8+)
    static void staticMethod() {
        // implementation
    }
    
    // Private method (Java 9+)
    private void privateMethod() {
        // implementation
    }
}
```

---

### Complete Example: Drawable Interface

```java
// ========== INTERFACE ==========
interface Drawable {
    // Constants (public static final by default)
    String TYPE = "2D Shape";
    int MAX_SIZE = 1000;
    
    // Abstract methods (public abstract by default)
    void draw();
    void resize(int size);
    double calculateArea();
    
    // Default method (Java 8+)
    default void display() {
        System.out.println("Displaying shape of type: " + TYPE);
    }
    
    // Static method (Java 8+)
    static void info() {
        System.out.println("This is a drawable interface");
    }
}

// ========== IMPLEMENTING CLASS 1: CIRCLE ==========
class Circle implements Drawable {
    private int radius;
    
    public Circle(int radius) {
        this.radius = radius;
    }
    
    @Override
    public void draw() {
        System.out.println("Drawing a circle with radius: " + radius);
    }
    
    @Override
    public void resize(int size) {
        this.radius = size;
        System.out.println("Circle resized to radius: " + radius);
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

// ========== IMPLEMENTING CLASS 2: RECTANGLE ==========
class Rectangle implements Drawable {
    private int length, width;
    
    public Rectangle(int length, int width) {
        this.length = length;
        this.width = width;
    }
    
    @Override
    public void draw() {
        System.out.println("Drawing a rectangle: " + length + " x " + width);
    }
    
    @Override
    public void resize(int size) {
        this.length = size;
        this.width = size;
        System.out.println("Rectangle resized to: " + length + " x " + width);
    }
    
    @Override
    public double calculateArea() {
        return length * width;
    }
    
    // Can override default method if needed
    @Override
    public void display() {
        System.out.println("Displaying rectangle");
    }
}

// ========== USAGE ==========
public class Main {
    public static void main(String[] args) {
        // Create objects
        Drawable circle = new Circle(5);
        Drawable rectangle = new Rectangle(4, 6);
        
        // Use interface reference
        System.out.println("===== Circle Operations =====");
        circle.draw();
        System.out.println("Area: " + circle.calculateArea());
        circle.display();  // Default method
        circle.resize(7);
        System.out.println("New Area: " + circle.calculateArea());
        System.out.println();
        
        System.out.println("===== Rectangle Operations =====");
        rectangle.draw();
        System.out.println("Area: " + rectangle.calculateArea());
        rectangle.display();  // Overridden method
        rectangle.resize(8);
        System.out.println("New Area: " + rectangle.calculateArea());
        System.out.println();
        
        // Access static method
        Drawable.info();
        
        // Access constants
        System.out.println("Type: " + Drawable.TYPE);
        System.out.println("Max Size: " + Drawable.MAX_SIZE);
    }
}
```

---

### Multiple Interface Implementation

**Java's Solution to Multiple Inheritance:**

```java
// ========== INTERFACE 1 ==========
interface Flyable {
    void fly();
    
    default void move() {
        System.out.println("Moving by flying");
    }
}

// ========== INTERFACE 2 ==========
interface Swimmable {
    void swim();
    
    default void move() {
        System.out.println("Moving by swimming");
    }
}

// ========== INTERFACE 3 ==========
interface Walkable {
    void walk();
}

// ========== IMPLEMENTING MULTIPLE INTERFACES ==========
class Duck implements Flyable, Swimmable, Walkable {
    @Override
    public void fly() {
        System.out.println("Duck is flying");
    }
    
    @Override
    public void swim() {
        System.out.println("Duck is swimming");
    }
    
    @Override
    public void walk() {
        System.out.println("Duck is walking");
    }
    
    // Must resolve conflict when both interfaces have same default method
    @Override
    public void move() {
        // Option 1: Choose one
        Flyable.super.move();  // Call Flyable's move()
        
        // Option 2: Provide custom implementation
        // System.out.println("Duck can fly, swim, and walk!");
    }
}

// Usage
Duck duck = new Duck();
duck.fly();
duck.swim();
duck.walk();
duck.move();
```

---

### Interface Extending Interfaces

```java
// ========== BASE INTERFACES ==========
interface Animal {
    void eat();
    void sleep();
}

interface LandAnimal extends Animal {
    void walk();
}

interface WaterAnimal extends Animal {
    void swim();
}

// Multiple interface inheritance
interface Amphibian extends LandAnimal, WaterAnimal {
    void croakSound();
}

// ========== IMPLEMENTING CLASS ==========
class Frog implements Amphibian {
    @Override
    public void eat() {
        System.out.println("Frog eats insects");
    }
    
    @Override
    public void sleep() {
        System.out.println("Frog sleeps on lily pad");
    }
    
    @Override
    public void walk() {
        System.out.println("Frog hops on land");
    }
    
    @Override
    public void swim() {
        System.out.println("Frog swims in water");
    }
    
    @Override
    public void croakSound() {
        System.out.println("Frog says: Croak! Croak!");
    }
}

// Usage
Frog frog = new Frog();
frog.eat();
frog.sleep();
frog.walk();
frog.swim();
frog.croakSound();
```

---

### Java 8+ Features in Interfaces

#### 1️⃣ Default Methods

**Purpose:** Add new methods to interface without breaking existing implementations

```java
interface Vehicle {
    void start();
    void stop();
    
    // Default method (Java 8+)
    default void honk() {
        System.out.println("Beep! Beep!");
    }
    
    default void displayInfo() {
        System.out.println("This is a vehicle");
    }
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
    
    @Override
    public void stop() {
        System.out.println("Car stopped");
    }
    
    // Can override default method if needed
    @Override
    public void honk() {
        System.out.println("Car horn: Honk! Honk!");
    }
    
    // Can use displayInfo() without overriding
}

// Usage
Car car = new Car();
car.start();
car.honk();          // Overridden default method
car.displayInfo();   // Inherited default method
car.stop();
```

---

#### 2️⃣ Static Methods

**Purpose:** Utility methods that belong to interface

```java
interface MathOperations {
    int add(int a, int b);
    
    // Static method (Java 8+)
    static int multiply(int a, int b) {
        return a * b;
    }
    
    static double divide(double a, double b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return a / b;
    }
}

class Calculator implements MathOperations {
    @Override
    public int add(int a, int b) {
        return a + b;
    }
}

// Usage
Calculator calc = new Calculator();
System.out.println(calc.add(10, 20));  // 30

// Static methods called using interface name
System.out.println(MathOperations.multiply(5, 6));   // 30
System.out.println(MathOperations.divide(20.0, 4));  // 5.0
```

---

#### 3️⃣ Private Methods (Java 9+)

**Purpose:** Helper methods for default/static methods

```java
interface Logger {
    // Private helper method (Java 9+)
    private String formatMessage(String message) {
        return "[" + java.time.LocalTime.now() + "] " + message;
    }
    
    // Private static helper method (Java 9+)
    private static boolean isValidMessage(String message) {
        return message != null && !message.isEmpty();
    }
    
    // Default methods using private methods
    default void logInfo(String message) {
        if (isValidMessage(message)) {
            System.out.println(formatMessage("INFO: " + message));
        }
    }
    
    default void logError(String message) {
        if (isValidMessage(message)) {
            System.out.println(formatMessage("ERROR: " + message));
        }
    }
    
    // Static method using private static method
    static void validate(String message) {
        if (!isValidMessage(message)) {
            throw new IllegalArgumentException("Invalid message");
        }
    }
}

class Application implements Logger {
    public void run() {
        logInfo("Application started");
        logError("Something went wrong");
    }
}

// Usage
Application app = new Application();
app.run();
```

---

### Marker Interface

**Definition:** Interface with **no methods**, used to mark a class for special treatment.

```java
// Marker interface (empty)
interface Serializable {
    // No methods
}

// Class implementing marker interface
class Student implements Serializable {
    private String name;
    private int rollNo;
    
    // Constructor, getters, setters...
}

// JVM checks if class implements Serializable before serializing
```

**Built-in Marker Interfaces:**
- `Serializable` - Object can be serialized
- `Cloneable` - Object can be cloned
- `Remote` - Object can be remote reference

---

### Functional Interface (Java 8+)

**Definition:** Interface with **exactly one abstract method**. Used for lambda expressions.

```java
// Functional interface annotation (optional but recommended)
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);  // Only one abstract method
    
    // Can have default and static methods
    default void display() {
        System.out.println("Calculator interface");
    }
    
    static void info() {
        System.out.println("Performs calculations");
    }
}

// Usage with lambda expression
public class Main {
    public static void main(String[] args) {
        // Lambda expression
        Calculator add = (a, b) -> a + b;
        Calculator subtract = (a, b) -> a - b;
        Calculator multiply = (a, b) -> a * b;
        
        System.out.println("Add: " + add.calculate(10, 20));          // 30
        System.out.println("Subtract: " + subtract.calculate(20, 10)); // 10
        System.out.println("Multiply: " + multiply.calculate(5, 6));   // 30
    }
}
```

---

### Real-World Example: Payment System

```java
// ========== INTERFACE ==========
interface Payment {
    void processPayment(double amount);
    boolean validatePayment(double amount);
    
    default void generateReceipt(double amount) {
        System.out.println("===== RECEIPT =====");
        System.out.println("Amount: $" + amount);
        System.out.println("Status: Success");
        System.out.println("===================");
    }
    
    static void displayPaymentMethods() {
        System.out.println("Available: Credit Card, Debit Card, PayPal, Bitcoin");
    }
}

// ========== IMPLEMENTATIONS ==========
class CreditCardPayment implements Payment {
    private String cardNumber;
    
    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }
    
    @Override
    public void processPayment(double amount) {
        if (validatePayment(amount)) {
            System.out.println("Processing credit card payment: $" + amount);
            System.out.println("Card: ****-****-****-" + cardNumber.substring(12));
            generateReceipt(amount);
        }
    }
    
    @Override
    public boolean validatePayment(double amount) {
        return amount > 0 && amount <= 10000;
    }
}

class PayPalPayment implements Payment {
    private String email;
    
    public PayPalPayment(String email) {
        this.email = email;
    }
    
    @Override
    public void processPayment(double amount) {
        if (validatePayment(amount)) {
            System.out.println("Processing PayPal payment: $" + amount);
            System.out.println("Email: " + email);
            generateReceipt(amount);
        }
    }
    
    @Override
    public boolean validatePayment(double amount) {
        return amount > 0 && email.contains("@");
    }
}

// ========== USAGE ==========
public class PaymentProcessor {
    public static void executePayment(Payment payment, double amount) {
        payment.processPayment(amount);
    }
    
    public static void main(String[] args) {
        Payment.displayPaymentMethods();
        System.out.println();
        
        Payment creditCard = new CreditCardPayment("1234567890123456");
        Payment paypal = new PayPalPayment("user@example.com");
        
        executePayment(creditCard, 500.00);
        System.out.println();
        executePayment(paypal, 250.00);
    }
}
```

---

## 🎯 Abstract Class vs Interface

### Detailed Comparison Table

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| **Keyword** | `abstract class` | `interface` |
| **Inheritance** | `extends` (Single) | `implements` (Multiple) |
| **Methods** | Abstract + Concrete | Abstract (default before Java 8) |
| **Variables** | Any type (instance, static, final) | Only `public static final` (constants) |
| **Constructors** | ✅ Can have | ❌ Cannot have |
| **Access Modifiers** | Any (public, protected, private, default) | Only public |
| **Method Body** | ✅ Concrete methods have body | ✅ Default/static have body (Java 8+) |
| **Multiple Inheritance** | ❌ Not supported | ✅ Supported |
| **Speed** | ⚡ Faster | 🐌 Slightly slower |
| **Fields** | Can have instance variables | Only constants |
| **Abstraction** | 0-100% | 100% (before Java 8) |
| **When to Use** | Common code + some differences | Contract for unrelated classes |
| **Example** | Vehicle, Shape, Employee | Serializable, Comparable, Runnable |
| **Default Methods** | Regular methods | Allowed (Java 8+) |
| **Static Methods** | ✅ Allowed | ✅ Allowed (Java 8+) |
| **Private Methods** | ✅ Allowed | ✅ Allowed (Java 9+) |

---

### When to Use Abstract Class?

✅ **Related classes** with common behavior  
✅ **Shared code** implementation  
✅ **Need constructors** to initialize state  
✅ **Need instance variables**  
✅ **Need access modifiers** other than public  
✅ **Partial implementation** (some methods concrete, some abstract)  

**Examples:**
- `Vehicle` → Car, Bike, Truck
- `Shape` → Circle, Rectangle, Triangle
- `Employee` → Manager, Developer, Tester

---

### When to Use Interface?

✅ **Unrelated classes** implementing same contract  
✅ **Multiple inheritance** needed  
✅ **Complete abstraction**  
✅ **Define capabilities** (can-do relationships)  
✅ **API definition** (contract without implementation)  

**Examples:**
- `Comparable` → String, Integer, Date
- `Runnable` → Thread classes
- `Serializable` → Any class that can be serialized

---

### Side-by-Side Example

```java
// ========== ABSTRACT CLASS ==========
abstract class Animal {
    protected String name;  // Instance variable
    
    // Constructor
    public Animal(String name) {
        this.name = name;
    }
    
    // Abstract method
    abstract void makeSound();
    
    // Concrete method
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }
    
    @Override
    void makeSound() {
        System.out.println(name + " barks: Woof!");
    }
}

// ========== INTERFACE ==========
interface Flyable {
    // Constant
    int MAX_HEIGHT = 10000;
    
    // Abstract method
    void fly();
    
    // Default method
    default void land() {
        System.out.println("Landing...");
    }
}

interface Swimmable {
    void swim();
}

// Multiple interfaces
class Duck implements Flyable, Swimmable {
    @Override
    public void fly() {
        System.out.println("Duck is flying");
    }
    
    @Override
    public void swim() {
        System.out.println("Duck is swimming");
    }
}

// ========== COMBINATION ==========
abstract class Bird {
    protected String species;
    
    public Bird(String species) {
        this.species = species;
    }
    
    abstract void eat();
}

class Parrot extends Bird implements Flyable {
    public Parrot() {
        super("Parrot");
    }
    
    @Override
    void eat() {
        System.out.println("Parrot eats seeds");
    }
    
    @Override
    public void fly() {
        System.out.println("Parrot is flying");
    }
}
```

---

## 🎯 The `this` Keyword

### What is `this`?

**Definition:** `this` is a **reference variable** that refers to the **current object** instance.

### Uses of `this` Keyword

---

### 1️⃣ Refer to Current Class Instance Variables

**Problem:** Parameter name same as instance variable name

```java
class Student {
    private String name;
    private int age;
    
    // Without 'this' - AMBIGUOUS
    public void setData(String name, int age) {
        name = name;  // ❌ Which name? Parameter or instance variable?
        age = age;    // ❌ Which age?
    }
    
    // With 'this' - CLEAR
    public void setDataCorrect(String name, int age) {
        this.name = name;  // ✅ this.name = instance variable
        this.age = age;    // ✅ name = parameter
    }
}
```

**Complete Example:**

```java
class Employee {
    private int id;
    private String name;
    private double salary;
    
    public Employee(int id, String name, double salary) {
        this.id = id;        // this.id = instance variable
        this.name = name;    // id = parameter
        this.salary = salary;
    }
    
    public void displayInfo() {
        System.out.println("ID: " + this.id);      // this is optional here
        System.out.println("Name: " + this.name);
        System.out.println("Salary: $" + this.salary);
    }
}

// Usage
Employee emp = new Employee(101, "John", 50000);
emp.displayInfo();
```

---

### 2️⃣ Invoke Current Class Method

```java
class Calculator {
    private int result;
    
    public void add(int a, int b) {
        result = a + b;
        this.displayResult();  // Call method using this
        // OR
        displayResult();       // this is optional (implicit)
    }
    
    public void displayResult() {
        System.out.println("Result: " + result);
    }
}

// Usage
Calculator calc = new Calculator();
calc.add(10, 20);  // Result: 30
```

---

### 3️⃣ Invoke Current Class Constructor (Constructor Chaining)

**Rule:** `this()` must be the **first statement** in constructor

```java
class Student {
    private int rollNo;
    private String name;
    private String course;
    private double fees;
    
    // Constructor 1: Default
    public Student() {
        this(0, "Unknown", "Not Assigned", 0.0);  // Calls Constructor 4
        System.out.println("Default constructor");
    }
    
    // Constructor 2: Roll number and name
    public Student(int rollNo, String name) {
        this(rollNo, name, "Not Assigned", 0.0);  // Calls Constructor 4
        System.out.println("Two-parameter constructor");
    }
    
    // Constructor 3: Roll number, name, and course
    public Student(int rollNo, String name, String course) {
        this(rollNo, name, course, 0.0);  // Calls Constructor 4
        System.out.println("Three-parameter constructor");
    }
    
    // Constructor 4: All parameters (Master constructor)
    public Student(int rollNo, String name, String course, double fees) {
        this.rollNo = rollNo;
        this.name = name;
        this.course = course;
        this.fees = fees;
        System.out.println("Four-parameter constructor (Master)");
    }
    
    public void display() {
        System.out.println("Roll: " + rollNo + ", Name: " + name + 
                         ", Course: " + course + ", Fees: $" + fees);
    }
}

// Usage
Student s1 = new Student();
// Output:
// Four-parameter constructor (Master)
// Default constructor

Student s2 = new Student(101, "John", "Computer Science");
// Output:
// Four-parameter constructor (Master)
// Three-parameter constructor

s1.display();
s2.display();
```

---

### 4️⃣ Pass `this` as Argument to Method

```java
class Printer {
    public void print(Student student) {
        System.out.println("Printing student info:");
        student.display();
    }
}

class Student {
    private String name;
    private int rollNo;
    
    public Student(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }
    
    public void printInfo(Printer printer) {
        printer.print(this);  // Pass current object as argument
    }
    
    public void display() {
        System.out.println("Name: " + name + ", Roll No: " + rollNo);
    }
}

// Usage
Student student = new Student("John", 101);
Printer printer = new Printer();
student.printInfo(printer);
```

---

### 5️⃣ Return Current Object from Method

**Used for Method Chaining:**

```java
class Calculator {
    private int value;
    
    public Calculator(int value) {
        this.value = value;
    }
    
    // Methods return 'this' for chaining
    public Calculator add(int num) {
        this.value += num;
        return this;  // Return current object
    }
    
    public Calculator subtract(int num) {
        this.value -= num;
        return this;  // Return current object
    }
    
    public Calculator multiply(int num) {
        this.value *= num;
        return this;  // Return current object
    }
    
    public Calculator divide(int num) {
        if (num != 0) {
            this.value /= num;
        }
        return this;  // Return current object
    }
    
    public int getResult() {
        return this.value;
    }
    
    public void display() {
        System.out.println("Value: " + this.value);
    }
}

// Usage - Method Chaining
Calculator calc = new Calculator(10);

calc.add(5)
    .multiply(2)
    .subtract(10)
    .divide(2)
    .display();  // Value: 10

// Same as:
// calc.add(5);       // 10 + 5 = 15
// calc.multiply(2);  // 15 * 2 = 30
// calc.subtract(10); // 30 - 10 = 20
// calc.divide(2);    // 20 / 2 = 10
// calc.display();    // Value: 10
```

**Real-World Example: StringBuilder**

```java
StringBuilder sb = new StringBuilder("Hello");

sb.append(" World")
  .append("!")
  .append(" Java")
  .reverse();

System.out.println(sb);  // avaJ !dlroW olleH
```

---

### 6️⃣ Return Current Object for Builder Pattern

```java
class Student {
    private int rollNo;
    private String name;
    private int age;
    private String course;
    
    private Student() { }  // Private constructor
    
    // Static nested Builder class
    public static class Builder {
        private Student student;
        
        public Builder() {
            student = new Student();
        }
        
        public Builder setRollNo(int rollNo) {
            student.rollNo = rollNo;
            return this;  // Return Builder object
        }
        
        public Builder setName(String name) {
            student.name = name;
            return this;
        }
        
        public Builder setAge(int age) {
            student.age = age;
            return this;
        }
        
        public Builder setCourse(String course) {
            student.course = course;
            return this;
        }
        
        public Student build() {
            return student;
        }
    }
    
    public void display() {
        System.out.println("Roll: " + rollNo + ", Name: " + name + 
                         ", Age: " + age + ", Course: " + course);
    }
}

// Usage - Builder Pattern
Student student = new Student.Builder()
                    .setRollNo(101)
                    .setName("John")
                    .setAge(20)
                    .setCourse("Computer Science")
                    .build();

student.display();
```

---

### `this` vs `super`

| Feature | `this` | `super` |
|---------|--------|---------|
| **Refers to** | Current class object | Parent class object |
| **Variables** | Current class variables | Parent class variables |
| **Methods** | Current class methods | Parent class methods |
| **Constructor** | Current class constructor | Parent class constructor |
| **Usage** | `this.variable`, `this.method()`, `this()` | `super.variable`, `super.method()`, `super()` |
| **Required** | When names conflict | To access parent members |

```java
class Parent {
    int x = 10;
    
    void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    int x = 20;
    
    void display() {
        System.out.println("Child display");
    }
    
    void show() {
        System.out.println("this.x = " + this.x);      // 20 (Child's x)
        System.out.println("super.x = " + super.x);    // 10 (Parent's x)
        
        this.display();   // Child display
        super.display();  // Parent display
    }
}

// Usage
Child child = new Child();
child.show();
```

---

### Important Rules for `this`

❌ **Cannot be used in static context**

```java
class Example {
    static int value = 10;
    
    static void method() {
        // System.out.println(this.value);  // ❌ ERROR: non-static reference
    }
}
```

✅ **Can be used in instance methods and constructors**

✅ **`this()` must be first statement in constructor**

```java
class Example {
    Example() {
        System.out.println("First line");
        this(10);  // ❌ ERROR: this() must be first statement
    }
    
    Example(int x) {
        this();    // ✅ Correct: first statement
        System.out.println("Second constructor");
    }
}
```

❌ **Cannot use both `this()` and `super()` in same constructor**

```java
class Child extends Parent {
    Child() {
        super();   // Call parent constructor
        this(10);  // ❌ ERROR: Cannot call this() after super()
    }
}
```

---

## 🎯 Interview Questions

### 🔥 Top Interview Questions: OOP

**Q1: What are the four pillars of OOP?**
- Encapsulation, Inheritance, Polymorphism, Abstraction

**Q2: What is the difference between class and object?**
- Class is blueprint/template, Object is instance of class with actual memory

**Q3: What is encapsulation and how to achieve it?**
- Data hiding by making variables private and providing public getters/setters

**Q4: What is inheritance? Why use it?**
- Child class acquires parent's properties. For code reusability.

**Q5: Why Java doesn't support multiple inheritance?**
- Diamond problem - ambiguity when two parents have same method

**Q6: Can we achieve multiple inheritance in Java?**
- Yes, using interfaces

**Q7: What is polymorphism? Types?**
- Many forms. Types: Compile-time (overloading) and Runtime (overriding)

**Q8: Difference between method overloading and overriding?**
- Overloading: same name, different parameters, same class
- Overriding: same signature, parent-child, runtime

**Q9: Can we override static methods?**
- No, static methods are hidden, not overridden

**Q10: Can we override private methods?**
- No, private methods are not inherited

**Q11: Can we override final methods?**
- No, final methods cannot be overridden

**Q12: What is abstraction?**
- Hiding implementation details, showing only essential features

**Q13: Difference between abstract class and interface?**
- Abstract class: 0-100% abstraction, single inheritance, can have constructors
- Interface: 100% abstraction, multiple inheritance, no constructors

**Q14: When to use abstract class vs interface?**
- Abstract class: related classes with common code
- Interface: unrelated classes, multiple inheritance, define contract

**Q15: Can interface have constructors?**
- No, interfaces cannot have constructors

**Q16: Can abstract class have constructors?**
- Yes, used by child classes

**Q17: What is constructor?**
- Special method to initialize objects, same name as class, no return type

**Q18: Types of constructors?**
- Default, Parameterized, Copy constructor

**Q19: Can constructor be private?**
- Yes, used in Singleton pattern

**Q20: What is constructor chaining?**
- Calling one constructor from another using this() or super()

**Q21: What is `this` keyword?**
- Reference to current class object

**Q22: What is `super` keyword?**
- Reference to parent class object

**Q23: Can we use both this() and super() in same constructor?**
- No, both must be first statement

**Q24: What is dynamic method dispatch?**
- Runtime decision of which method to call based on object type

**Q25: What is covariant return type?**
- Overriding method can return subtype of parent's return type

---

## ✅ Summary

### Key Concepts Covered

✅ **Classes & Objects**: Blueprint and instances  
✅ **Constructors**: Default, Parameterized, Copy, Chaining  
✅ **Encapsulation**: Data hiding with getters/setters  
✅ **Inheritance**: Code reusability, IS-A relationship  
✅ **Polymorphism**: Overloading (compile-time) and Overriding (runtime)  
✅ **Abstraction**: Abstract classes and Interfaces  
✅ **`this` keyword**: Current object reference  
✅ **Access Modifiers**: public, protected, default, private  

---

### Quick Reference: OOP Principles

| Principle | Purpose | How to Achieve |
|-----------|---------|----------------|
| **Encapsulation** | Data hiding | Private variables + getters/setters |
| **Inheritance** | Code reuse | extends keyword |
| **Polymorphism** | Flexibility | Overloading + Overriding |
| **Abstraction** | Hide complexity | Abstract classes + Interfaces |

---

### Best Practices

✅ Use **encapsulation** for all data members  
✅ Use **inheritance** for IS-A relationships  
✅ Use **composition** for HAS-A relationships  
✅ Override `equals()` and `hashCode()` together  
✅ Use `@Override` annotation  
✅ Prefer **interfaces** for contracts  
✅ Use **abstract classes** for shared code  
✅ Use **constructor chaining** to avoid code duplication  
✅ Make classes **final** if no inheritance needed  
✅ Use **builder pattern** for complex object creation  

---

## 📄 One-Page Cheatsheet

### 🎯 Four Pillars of OOP

| Pillar | Definition | Implementation |
|--------|------------|----------------|
| **Encapsulation** | Bundling data + methods, data hiding | `private` fields + `public` getters/setters |
| **Inheritance** | Child acquires parent properties | `class Child extends Parent` |
| **Polymorphism** | One interface, many forms | Method Overloading + Overriding |
| **Abstraction** | Hide implementation, show essentials | Abstract classes + Interfaces |

---

### 📦 Classes & Objects

```java
class ClassName {                    // Blueprint
    // Variables (State)
    private int field;               // Instance variable
    static int count;                // Static variable
    
    // Constructor (Initialize)
    public ClassName() { }           // Default
    public ClassName(int x) { }      // Parameterized
    
    // Methods (Behavior)
    public void method() { }         // Instance method
    public static void staticMethod() { }  // Static method
}

ClassName obj = new ClassName();     // Object (Instance)
```

**Memory:**
- Object → Heap | Reference → Stack (local) or Heap (instance var)

---

### 🏗️ Constructors

| Type | Syntax | Purpose |
|------|--------|----------|
| **Default** | `ClassName() { }` | Auto-created if none defined |
| **Parameterized** | `ClassName(int x) { this.x = x; }` | Initialize with values |
| **Copy** | `ClassName(ClassName obj) { }` | Copy from existing object |

**Constructor Chaining:**
```java
ClassName() {                        // Constructor 1
    this(10);                        // Calls Constructor 2
}
ClassName(int x) { this.x = x; }    // Constructor 2
```

**Rules:**
- ✅ Same name as class | ❌ No return type | ✅ Can overload
- ❌ Not inherited | `this()` / `super()` must be **first statement**
- ❌ Cannot use both `this()` and `super()` together

---

### 🔒 Encapsulation

```java
class BankAccount {
    private double balance;          // ❌ Direct access denied
    
    public double getBalance() {     // ✅ Getter (Read)
        return balance;
    }
    
    public void setBalance(double b) { // ✅ Setter (Write)
        if (b > 0) balance = b;      // Validation logic
    }
}
```

**Benefits:** Security | Control | Flexibility | Maintainability

---

### 🧬 Inheritance

```java
class Parent {                       // Superclass
    protected int x = 10;
    void display() { }
}

class Child extends Parent {         // Subclass
    void show() {
        System.out.println(x);       // Inherited
        super.display();             // Call parent method
    }
}
```

**Types:** Single ✅ | Multilevel ✅ | Hierarchical ✅ | Multiple ❌ (use interfaces)

**Access in Inheritance:**
- `public` ✅ | `protected` ✅ | `default` ✅ (same package) | `private` ❌

**What's Inherited?** Methods, Fields | **Not Inherited:** Constructors, Private members

---

### 🔄 Polymorphism

#### Compile-time (Method Overloading)
```java
void method(int a) { }               // Different number
void method(int a, int b) { }        
void method(double a) { }            // Different type
void method(int a, double b) { }     // Different order
```
**Rules:** Same name | Different parameters | Same class

#### Runtime (Method Overriding)
```java
class Parent {
    void display() { System.out.println("Parent"); }
}
class Child extends Parent {
    @Override
    void display() { System.out.println("Child"); } // Override
}
Parent obj = new Child();
obj.display();  // Output: Child (Runtime decision)
```
**Rules:** Same signature | Parent-child | `@Override` annotation

**Cannot Override:** `private`, `static`, `final` methods

---

### 🎭 Method Overloading vs Overriding

| Feature | Overloading | Overriding |
|---------|-------------|------------|
| **When** | Compile-time | Runtime |
| **Where** | Same class | Parent-child |
| **Signature** | Different parameters | Same signature |
| **Return Type** | Can differ | Same or covariant |
| **Inheritance** | Not required | Required |
| **Purpose** | Add behavior | Change behavior |

---

### 🌫️ Abstraction

#### Abstract Class
```java
abstract class Animal {              // 0-100% abstraction
    abstract void sound();           // ❌ No body (must override)
    void sleep() { }                 // ✅ Concrete method
    Animal() { }                     // ✅ Constructor allowed
    int age;                         // ✅ Variables allowed
}

class Dog extends Animal {           // Must implement sound()
    void sound() { System.out.println("Bark"); }
}
```

#### Interface
```java
interface Drawable {                 // 100% abstraction
    void draw();                     // public abstract (default)
    int MAX = 100;                   // public static final (constant)
    
    default void display() { }       // Java 8+ (with body)
    static void info() { }           // Java 8+ (static)
}

class Circle implements Drawable {
    public void draw() { }           // Must be public
}
```

---

### 🆚 Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|---------------|------------|
| **Keyword** | `extends` (Single) | `implements` (Multiple) |
| **Methods** | Abstract + Concrete | Abstract (+ default/static Java 8+) |
| **Variables** | Any type | Only `public static final` |
| **Constructor** | ✅ Yes | ❌ No |
| **Access** | Any modifier | Only `public` |
| **When** | Related classes + shared code | Unrelated classes + contract |

---

### 🔑 Keywords: `this` vs `super`

#### `this` - Current Class
```java
class Student {
    int id;
    Student(int id) {
        this.id = id;                // 1. Refer to instance variable
        this.display();              // 2. Call current class method
    }
    Student() {
        this(0);                     // 3. Call current class constructor
    }
    Student getStudent() {
        return this;                 // 4. Return current object
    }
}
```

#### `super` - Parent Class
```java
class Child extends Parent {
    int x = 20;
    Child() {
        super();                     // 1. Call parent constructor
    }
    void display() {
        System.out.println(super.x); // 2. Access parent variable
        super.display();             // 3. Call parent method
    }
}
```

**Rules:** `this()` / `super()` must be **first statement** | ❌ Cannot use both together

---

### 🔐 Access Modifiers

| Modifier | Same Class | Same Package | Subclass | Other Package |
|----------|------------|--------------|----------|---------------|
| **public** | ✅ | ✅ | ✅ | ✅ |
| **protected** | ✅ | ✅ | ✅ | ❌ |
| **default** | ✅ | ✅ | ❌ | ❌ |
| **private** | ✅ | ❌ | ❌ | ❌ |

**Order:** `public` > `protected` > `default` > `private`

---

### ⚡ Quick Rules to Remember

#### Constructors
- ✅ Same name as class | ❌ No return type | ❌ Not inherited
- `this()` / `super()` → first statement | ❌ Can't use both

#### Method Overriding
- ✅ Same signature | ✅ `@Override` | ❌ Can't override: `private`, `static`, `final`
- Access: ❌ Can't be more restrictive | ✅ Can be less restrictive

#### Abstract Class
- ❌ Can't instantiate | ✅ Can have constructors | ✅ 0-100% abstraction
- Abstract method: ❌ Can't be `private`, `static`, `final`

#### Interface
- ❌ Can't instantiate | ❌ No constructors | ✅ Multiple inheritance
- Methods: `public abstract` (default) | Variables: `public static final`
- Java 8+: `default`, `static` | Java 9+: `private`

#### Static Methods
- Called using class name | ❌ Can't use `this` | ❌ Can't be overridden (hidden)

---

### 🏆 Important Interview Points

✅ **Why no multiple inheritance?** → Diamond Problem (ambiguity)  
✅ **Can interface have constructors?** → No  
✅ **Can abstract class have constructors?** → Yes  
✅ **Can we override static methods?** → No (hidden, not overridden)  
✅ **Can we override private methods?** → No (not inherited)  
✅ **Can we override final methods?** → No  
✅ **Can constructor be private?** → Yes (Singleton pattern)  
✅ **Difference between `==` and `equals()`?** → `==` checks reference, `equals()` checks content  
✅ **What is dynamic method dispatch?** → Runtime decision based on object type  
✅ **What is covariant return type?** → Override method can return subtype  

---

### 📝 Code Templates

**Encapsulation Pattern:**
```java
public class ClassName {
    private Type field;
    public Type getField() { return field; }
    public void setField(Type field) { this.field = field; }
}
```

**Singleton Pattern:**
```java
public class Singleton {
    private static Singleton instance;
    private Singleton() { }  // Private constructor
    public static Singleton getInstance() {
        if (instance == null) instance = new Singleton();
        return instance;
    }
}
```

**Builder Pattern:**
```java
class Student {
    private int id;
    private String name;
    private Student() { }
    
    static class Builder {
        private Student student = new Student();
        Builder setId(int id) { student.id = id; return this; }
        Builder setName(String n) { student.name = n; return this; }
        Student build() { return student; }
    }
}
// Usage: new Student.Builder().setId(1).setName("John").build();
```

---

### 💡 Best Practices

✅ Always use **encapsulation** (private fields + getters/setters)  
✅ Use **`@Override`** annotation for clarity  
✅ **Prefer interfaces** over abstract classes for contracts  
✅ Use **inheritance** for IS-A, **composition** for HAS-A  
✅ Make classes **`final`** if inheritance not needed  
✅ Override **`equals()` and `hashCode()`** together  
✅ Use **constructor chaining** to reduce duplication  
✅ Static methods: call using **class name**, not object  

---

**Happy Learning! 🚀**
