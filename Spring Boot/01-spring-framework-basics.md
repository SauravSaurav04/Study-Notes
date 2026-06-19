# 🌱 Spring Framework Basics - MUST KNOW

**Priority:** 🔴 CRITICAL | **Interview Weight:** Very High

---

## 📑 Table of Contents

1. [Why Spring Framework?](#1-why-spring-framework)
2. [Core Concepts](#2-core-concepts)
   - [Inversion of Control (IoC)](#-inversion-of-control-ioc)
   - [Dependency Injection (DI)](#-dependency-injection-di)
   - [Common Doubts About IoC and Bean Creation](#-common-doubts-about-ioc-and-bean-creation)
   - [What are Spring Beans?](#-what-are-spring-beans)
   - [Bean Lifecycle](#-bean-lifecycle)
   - [ApplicationContext vs BeanFactory](#-applicationcontext-vs-beanfactory)
     - [What is ApplicationContext?](#what-is-applicationcontext)
     - [What is BeanFactory?](#what-is-beanfactory)
     - [Comparison Table](#-applicationcontext-vs-beanfactory---comparison-table)
     - [ApplicationContext in Practice](#-applicationcontext-in-practice)
3. [Spring Modules](#3-spring-modules)
   - [Spring Core](#-spring-core)
   - [Spring Context](#-spring-context)
   - [Spring AOP](#-spring-aop-aspect-oriented-programming)
   - [Spring Expression Language (SpEL)](#-spring-expression-language-spel)
4. [Annotation-based Configuration](#4-annotation-based-configuration)
   - [Component Scanning Annotations](#-component-scanning-annotations)
   - [Configuration Annotations](#-configuration-annotations)
   - [Dependency Resolution Annotations](#-dependency-resolution-annotations)
5. [Bean Scopes](#5-bean-scopes-critical-concept)
6. [Common Interview Questions](#6-common-interview-questions)
7. [Quick Revision Checklist](#7-quick-revision-checklist)
8. [Practice Exercise](#8-practice-exercise)
9. [Final Summary](#9-final-summary)

---

## 1. Why Spring Framework?

### The Problem with Plain Java

Before frameworks like Spring existed, developers built enterprise applications using core Java + Servlets + JSP.

**Example using Plain Java Servlet:**
```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse res)
            throws IOException {
        res.setContentType("text/html");
        res.getWriter().write("Hello World");
    }
}
```

### Problems with Plain Java for Enterprise Applications:
- ❌ Too much boilerplate code
- ❌ Manual object wiring and dependency management
- ❌ Manual servlet setup and lifecycle management
- ❌ Manual transaction handling
- ❌ Hard to test due to tight coupling
- ❌ No built-in architecture or standard patterns
- ❌ Configuration hell (XML files)
- ❌ Difficult to maintain as applications grow

**Java alone can build anything, but for enterprise applications it becomes slow, repetitive, and hard to maintain.**

---

### What is Spring?

**Spring is a Java framework used to build enterprise applications in a cleaner, modular, and maintainable way.**

**Spring provides:**
- 🔹 **IoC Container** - Manages object lifecycle automatically
- 🔹 **Dependency Injection** - Wires dependencies without manual creation
- 🔹 **Ready-made solutions** - Database integration, security, REST APIs, etc.
- 🔹 **Better architecture** - Clear separation of concerns (Controller → Service → Repository)
- 🔹 **Easier testing** - Mock dependencies easily
- 🔹 **Less boilerplate** - Focus on business logic, not plumbing code

**Spring allows developers to focus on business logic instead of infrastructure code.**

---

## 2. Core Concepts

### 🔹 Inversion of Control (IoC)

**Definition:** IoC means the control of object creation and lifecycle is moved from your application code to the Spring container.

In plain Java, classes often create their own dependencies using `new`. In Spring, you declare dependencies and the container creates, wires, and manages them.

---

**Without IoC (class controls dependencies):**

```java
class PaymentService {
    public void pay() {
        System.out.println("Payment done");
    }
}

class OrderService {
    private final PaymentService paymentService = new PaymentService();

    public void placeOrder() {
        paymentService.pay();
    }
}
```

**Issue:** `OrderService` is tightly coupled to `PaymentService` implementation.

---

### 🔹 Dependency Injection (DI)

**Definition:** DI is the practical way to implement IoC. Instead of creating dependencies inside a class, dependencies are provided from outside.

---

**With DI (dependency provided from outside):**

```java
interface PaymentService {
    void pay();
}

class CardPaymentService implements PaymentService {
    public void pay() {
        System.out.println("Paid using card");
    }
}

class UpiPaymentService implements PaymentService {
    public void pay() {
        System.out.println("Paid using UPI");
    }
}

class OrderService {
    private final PaymentService paymentService;

    // Constructor DI
    OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public void placeOrder() {
        paymentService.pay();
    }
}

public class App {
    public static void main(String[] args) {
        PaymentService paymentService = new UpiPaymentService();
        OrderService orderService = new OrderService(paymentService);
        orderService.placeOrder();
    }
}
```

Now `OrderService` depends on an abstraction (`PaymentService`), so implementation can change easily.

---

#### 🔑 IoC vs DI - Key Interview Point

**Important distinction:**
- **IoC is a principle:** Framework controls object lifecycle
- **DI is a technique:** Dependencies are injected into objects
- **In Spring:** IoC container (`ApplicationContext`) uses DI to wire beans

---

#### 💡 DI in Spring - Real Example

```java
import org.springframework.stereotype.Repository;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@Repository
class UserRepository {
    public String findUserName() {
        return "Saurav";
    }
}

@Service
class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public String getUserName() {
        return userRepository.findUserName();
    }
}

@RestController
class UserController {
    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/user")
    public String user() {
        return userService.getUserName();
    }
}
```

**Flow:**
1. Spring container creates `UserRepository` bean
2. Spring creates `UserService` and injects `UserRepository`
3. Spring creates `UserController` and injects `UserService`
4. Request to `/user` returns value from layered components

---

**Benefits of DI:**
- Loose coupling between components
- Easier testing (mock dependencies easily)
- Better code maintainability
- Separation of concerns
- Easy to swap implementations

---

**Types of DI in Spring:**

```java
// 1. Constructor Injection (RECOMMENDED)
@Service
public class UserService {
    private final UserRepository userRepository;
    
    @Autowired  // Optional since Spring 4.3 for single constructor
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// 2. Setter Injection (for optional dependencies)
@Service
public class ProductService {
    private ProductRepository productRepository;
    
    @Autowired
    public void setProductRepository(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }
}

// 3. Field Injection (NOT RECOMMENDED - hard to test)
@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;
}
```

---

#### 🎯 Which DI Type is Best and How to Decide?

**1. Constructor Injection (Recommended for 90% of cases)**

Use constructor injection in most real projects.

**Why it is best:**
- Makes required dependencies explicit
- Object is created only in a valid state
- Supports immutability (`final` fields)
- Easiest for unit testing (no reflection needed)
- Works well with clean architecture and SOLID design
- Prevents partial initialization

**Example:**
```java
@Service
public class OrderService {
    private final PaymentService paymentService;
    private final InventoryService inventoryService;
    
    public OrderService(PaymentService paymentService, 
                        InventoryService inventoryService) {
        this.paymentService = paymentService;
        this.inventoryService = inventoryService;
    }
}
```

---

**2. Using Lombok for Constructor Injection**

**Can we use Lombok `@AllArgsConstructor` for constructor injection?**  
Yes, you can. Spring can inject dependencies through Lombok-generated constructors.

**Example with `@AllArgsConstructor`:**
```java
import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@AllArgsConstructor
public class UserService {
    private UserRepository userRepository;
    private EmailService emailService;
}
```

**Recommended in most projects: `@RequiredArgsConstructor`**
```java
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
}
```

**Why `@RequiredArgsConstructor` is usually better:**
- Includes only required (`final`/`@NonNull`) fields
- Encourages immutability
- Avoids accidental constructor parameters for non-dependency fields

**Rule of thumb:**
1. Use `@RequiredArgsConstructor` by default
2. Use `@AllArgsConstructor` only when you intentionally want every field in constructor

---

**3. When to Use Setter Injection**

Use setter injection only for **optional dependencies** or values that may change after object creation.

**Good fit for:**
- Optional collaborator
- Backward-compatible legacy components
- Rare cases where dependency is not mandatory at startup

**Example:**
```java
@Service
public class ReportService {
    private ExportService exportService; // optional
    
    @Autowired(required = false)
    public void setExportService(ExportService exportService) {
        this.exportService = exportService;
    }
}
```

---

**4. Why Field Injection is Not Preferred**

Field injection is short, but has drawbacks:
- Hard to unit test without Spring/reflection
- Hides dependencies (less explicit design)
- Cannot make dependencies `final`
- Encourages tightly coupled and less maintainable code

**Example (avoid in production code):**
```java
@Service
public class InvoiceService {
    @Autowired
    private TaxService taxService; // Not recommended
}
```

---

#### 📋 Quick Decision Rule

1. **Is dependency required for class to work?** → Use **Constructor Injection**
2. **Is dependency optional?** → Use **Setter Injection**
3. **Field Injection?** → Avoid except very small demos/tests

**Interview-friendly one-liner:**  
*"In Spring, constructor injection should be the default choice; use setter injection for optional dependencies; avoid field injection in production for better testability and maintainability."*

---

### 🔹 Common Doubts About IoC and Bean Creation

**Q1: What does "Spring Container Creates Bean" mean?**

Conceptually, Spring does something like:
```java
UserRepository userRepository = new UserRepository(...);
```

But unlike manual Java code, Spring:
1. Decides which constructor to use
2. Resolves constructor arguments from other beans/configuration
3. Stores the created object inside `ApplicationContext`

---

**Q2: How does Spring choose constructor (default vs parameterized)?**

- **No constructor written:** Java gives a default no-arg constructor, Spring uses it
- **One parameterized constructor exists:** Spring uses it and injects required dependencies
- **Multiple constructors exist:** Mark one with `@Autowired` (or keep one constructor only)
- **Dependencies cannot be resolved:** Startup fails with a bean creation error

**Example with parameterized constructor:**
```java
import javax.sql.DataSource;
import org.springframework.stereotype.Repository;

@Repository
class UserRepository {
    private final DataSource dataSource;
    
    public UserRepository(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```
Here Spring creates `UserRepository` only when it can provide a `DataSource` bean.

---

**Q3: Which annotations lead to bean creation?**

Spring container creates beans for classes discovered via component scanning or Java config:

1. `@Component` - Generic component
2. `@Service` - Service layer
3. `@Repository` - Data access layer
4. `@Controller` - MVC controller
5. `@RestController` - REST controller
6. Methods annotated with `@Bean` inside `@Configuration`

**Notes:**
- `@Service`, `@Repository`, `@Controller`, and `@RestController` are specialized forms of `@Component`
- `@Autowired` does NOT create beans; it injects already created beans
- Beans are created only if classes are in the component scan path (usually main class package and subpackages)

---

**Q4: Bean creation using @Bean**

Use this approach when you want to create beans explicitly in Java config, especially for:
- Third-party classes (where you cannot add `@Component`)
- Custom initialization logic
- Conditional or environment-based wiring

**Example:**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

class EmailClient {
    private final String host;
    private final int port;
    
    EmailClient(String host, int port) {
        this.host = host;
        this.port = port;
    }
    
    public String details() {
        return "EmailClient{" + host + ":" + port + "}";
    }
}

@Configuration
class AppConfig {
    
    @Bean
    public EmailClient emailClient() {
        return new EmailClient("smtp.gmail.com", 587);
    }
}
```

**How Spring handles this:**
1. Detects `@Configuration` class during startup
2. Executes `@Bean` method `emailClient()`
3. Registers returned object as a bean named `emailClient`
4. Injects it wherever `EmailClient` is required

**Usage in a service:**
```java
@Service
class NotificationService {
    private final EmailClient emailClient;
    
    public NotificationService(EmailClient emailClient) {
        this.emailClient = emailClient;
    }
    
    public String info() {
        return emailClient.details();
    }
}
```

**@Component vs @Bean:**
- Use `@Component` for your own application classes
- Use `@Bean` when object creation needs custom logic or class is external

---

### 🔹 What are Spring Beans?

**Simple Definition:**  
A **Spring bean** is simply a Java object that is created, managed, and configured by the Spring IoC container.

---

#### Understanding the Difference

**Regular Java Object (NOT a Spring bean):**
```java
// You create and manage this object manually
public class UserService {
    private UserRepository repository;
}

// In your code
UserService service = new UserService(); // You control creation and lifecycle
```

**Spring Bean:**
```java
// Spring creates and manages this object
@Service
public class UserService {
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}

// Spring does this automatically:
// 1. Creates UserRepository instance
// 2. Creates UserService instance
// 3. Injects UserRepository into UserService
// 4. Stores both in ApplicationContext
// You just use: @Autowired or constructor injection
```

---

#### What "Spring Bean" Really Means

When we say something is a "Spring bean", it means:

1. ✅ **Spring creates it** - You don't use `new`, Spring does
2. ✅ **Spring manages it** - Lifecycle, dependencies, configuration
3. ✅ **Spring stores it** - Kept in ApplicationContext container
4. ✅ **Spring injects it** - Automatically wired where needed
5. ✅ **Spring destroys it** - Cleanup when application shuts down

---

#### How Objects Become Spring Beans

**Method 1: Using Stereotype Annotations**
```java
@Component    // Generic bean - any Spring-managed component
@Service      // Business logic bean - service layer
@Repository   // Data access bean - persistence layer
@Controller   // Web MVC bean - presentation layer
@RestController // REST API bean - RESTful web services
```

**Example:**
```java
@Service // This makes UserService a Spring bean
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}
```

**Method 2: Using @Bean in Configuration**
```java
@Configuration
public class AppConfig {
    
    @Bean // This method returns a Spring bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    
    @Bean
    public DataSource dataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        return ds;
    }
}
```

---

#### Real-World Analogy

**Without Spring (You're the manager):**
- You hire employees (`new Employee()`)
- You tell them who to work with (manual wiring)
- You manage their schedule (lifecycle management)
- You handle their payroll (resource management)

**With Spring (Spring is the manager):**
- Spring hires employees (creates beans)
- Spring assigns teams (dependency injection)
- Spring manages schedules (bean lifecycle)
- Spring handles resources (automatic cleanup)

---

#### Key Characteristics of Spring Beans

| Characteristic | Description |
|----------------|-------------|
| **Managed by Spring** | Container controls creation, not your code |
| **Dependency Injection** | Spring wires dependencies automatically |
| **Lifecycle Management** | Spring calls initialization and cleanup methods |
| **Configuration** | Can be configured via annotations or Java/XML config |
| **Singleton by Default** | One instance shared across application (can be changed) |
| **Discoverable** | Can be retrieved from ApplicationContext |

---

#### Complete Example - Spring Beans in Action

```java
// 1. Repository bean - Data access
@Repository
public class UserRepository {
    public User findById(Long id) {
        // Database logic
        return new User(id, "John", "john@example.com");
    }
}

// 2. Service bean - Business logic
@Service
public class UserService {
    private final UserRepository userRepository;
    
    // Spring injects UserRepository automatically
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}

// 3. Controller bean - REST API
@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;
    
    // Spring injects UserService automatically
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUser(id);
    }
}
```

**What Spring does behind the scenes:**
1. Scans for classes with `@Component`, `@Service`, `@Repository`, `@Controller`
2. Creates instances (beans) of these classes
3. Resolves dependencies (UserController needs UserService, UserService needs UserRepository)
4. Injects dependencies via constructors
5. Stores all beans in ApplicationContext
6. Makes them available for use throughout the application

---

#### What is NOT a Spring Bean

❌ **Regular POJOs without annotations:**
```java
public class User {
    private Long id;
    private String name;
    // NOT a Spring bean - just a data class
}
```

❌ **Objects created with `new` keyword:**
```java
public class MyClass {
    public void doSomething() {
        UserService service = new UserService(); // NOT a Spring bean
        // This object is not managed by Spring
    }
}
```

❌ **DTO/Entity classes without @Component:**
```java
public class UserDTO {
    private String name;
    private String email;
    // NOT a Spring bean - just a data transfer object
}
```

✅ **These ARE Spring Beans:**
```java
@Service
public class UserService { } // Spring bean

@Repository
public class UserRepository { } // Spring bean

@Configuration
public class AppConfig {
    @Bean
    public RestTemplate restTemplate() { // Returns a Spring bean
        return new RestTemplate();
    }
}
```

---

#### Interview-Ready Summary

**Q: What are Spring Beans?**  
**A:** Spring beans are Java objects whose lifecycle is managed by the Spring IoC container. Instead of creating objects with `new`, we annotate classes with `@Component`, `@Service`, `@Repository`, etc., and Spring creates, configures, wires, and manages them automatically. This enables loose coupling, easier testing, and better maintainability.

**Key points to remember:**
- Bean = Object managed by Spring
- Created via annotations (`@Component`, `@Service`, etc.) or `@Bean` methods
- Stored in ApplicationContext
- Singleton scope by default (one instance per container)
- Dependencies injected automatically
- Lifecycle managed by Spring (creation, initialization, destruction)

---

### 🔹 Bean Lifecycle

**Key Phases:**

1. **Instantiation** → Spring creates bean instance
2. **Populate Properties** → DI happens (dependencies injected)
3. **setBeanName()** → If implements BeanNameAware
4. **setBeanFactory()** → If implements BeanFactoryAware
5. **setApplicationContext()** → If implements ApplicationContextAware
6. **@PostConstruct** → Custom initialization (most commonly used)
7. **afterPropertiesSet()** → If implements InitializingBean
8. **custom init-method** → From @Bean(initMethod)
9. **Bean Ready for Use** → Bean is fully initialized and available
10. **@PreDestroy** → Before container shutdown (cleanup logic)
11. **destroy()** → If implements DisposableBean
12. **custom destroy-method** → From @Bean(destroyMethod)

---

**Practical Example:**

```java
@Component
public class DatabaseConnection {
    
    @PostConstruct
    public void init() {
        System.out.println("Initializing database connection...");
        // Open connection, load resources
        // This runs AFTER dependency injection
    }
    
    @PreDestroy
    public void cleanup() {
        System.out.println("Closing database connection...");
        // Close connection, release resources
        // This runs BEFORE application shutdown
    }
}
```

---

**When to use @PostConstruct?**

Use `@PostConstruct` for initialization logic that needs injected dependencies (DI happens before `@PostConstruct`).

**Common use cases:**
- Opening database/network connections
- Loading cache or configuration
- Validating configuration
- Registering listeners or callbacks
- Initializing resources that depend on injected beans

**Example with dependencies:**
```java
@Service
public class CacheService {
    private final ConfigProperties config;
    
    public CacheService(ConfigProperties config) {
        this.config = config;
    }
    
    @PostConstruct
    public void initializeCache() {
        // config is available here (injected via constructor)
        System.out.println("Initializing cache with size: " + config.getCacheSize());
        // Load cache data
    }
    
    @PreDestroy
    public void clearCache() {
        System.out.println("Clearing cache before shutdown");
    }
}
```

---

**Interview Tip:** Know the order:  
**Instantiation → DI → Awareness → @PostConstruct → InitializingBean → Custom Init → Ready → @PreDestroy → DisposableBean → Custom Destroy**

**Memory trick:** "**I**nitialize, **D**ependency inject, **A**wareness, **P**ost-construct, **I**nitializingBean"

---

### 🔹 ApplicationContext vs BeanFactory

**What is ApplicationContext?**

ApplicationContext is Spring's advanced IoC (Inversion of Control) container that manages the complete lifecycle of your application beans.

**What it does:**
- Creates and manages all Spring beans
- Handles dependency injection automatically
- Provides infrastructure services (event publishing, AOP, internationalization)
- Acts as the central interface to Spring's IoC container

**Main Features:**
1. **Eager initialization** - Creates all singleton beans at application startup (catches errors early)
2. **Auto-wiring** - Automatically injects dependencies without manual configuration
3. **Event handling** - Publishes and listens to application events (ApplicationEvent)
4. **Annotation support** - Built-in support for `@Autowired`, `@Component`, `@Value`, etc.
5. **AOP support** - Full aspect-oriented programming capabilities
6. **Internationalization (i18n)** - Support for multiple languages via MessageSource
7. **Resource loading** - Easy access to files, classpath resources, URLs

**Common Implementations:**
- `AnnotationConfigApplicationContext` - For Java-based configuration
- `ClassPathXmlApplicationContext` - For XML-based configuration (legacy)
- `GenericWebApplicationContext` - For web applications
- In Spring Boot: `SpringApplication.run()` returns an ApplicationContext

---

**What is BeanFactory?**

BeanFactory is Spring's basic, lightweight IoC container - it's the root interface for accessing Spring beans and the foundation of the Spring container hierarchy.

**What it is:**
- The simplest and most basic container in Spring
- Root interface for Spring's IoC container
- Provides basic bean lifecycle management
- Core mechanism for accessing Spring beans
- ApplicationContext extends BeanFactory (adds more features)

**Main Characteristics:**
1. **Lazy initialization** - Creates beans only when requested via `getBean()`
2. **Manual configuration** - Requires manual setup for auto-wiring, AOP, etc.
3. **Minimal features** - Basic bean creation and dependency injection only
4. **Lightweight** - Lower memory footprint
5. **Basic lifecycle** - Limited lifecycle callback support

**Basic Usage (Legacy):**
```java
// Rarely used in modern applications
BeanFactory factory = new XmlBeanFactory(
    new ClassPathResource("beans.xml"));
    
UserService service = (UserService) factory.getBean("userService");
```

**Why it exists:**
- **Historical** - BeanFactory was Spring's original container (Spring 1.x)
- **Extreme resource constraints** - Very limited memory environments (IoT devices, embedded systems)
- **Legacy code** - Maintaining old Spring applications
- **Foundation** - ApplicationContext is built on top of BeanFactory

**Modern Reality:**
- ❌ **Rarely used in production** - ApplicationContext is the standard
- ✅ **ApplicationContext extends BeanFactory** - Gets all BeanFactory features plus more
- 📚 **Interview knowledge** - Good to know the difference for interviews
- 🎯 **Best practice** - Always use ApplicationContext in modern applications

**Interview Tip:**  
BeanFactory is Spring's basic container with lazy loading and minimal features. It's the foundation interface that ApplicationContext extends. In modern Spring applications, always use ApplicationContext because it provides eager initialization, auto-wiring, AOP, events, internationalization, and catches configuration errors at startup instead of runtime. BeanFactory is legacy and only relevant for very resource-constrained environments.

---

#### 🔑 ApplicationContext vs BeanFactory - Comparison Table

| Feature | BeanFactory | ApplicationContext |
|---------|-------------|-------------------|
| **Initialization** | Lazy - beans created on demand | Eager - beans created at startup |
| **Auto-wiring** | No | Yes |
| **Event Publication** | No | Yes (ApplicationEvent) |
| **Internationalization** | No | Yes (MessageSource) |
| **Annotation Support** | Manual | Built-in |
| **Bean Post Processors** | Manual registration | Automatic detection |
| **AOP Support** | Limited | Full support |
| **Use Case** | Legacy, resource-constrained | Modern applications (ALWAYS USE THIS) |

---

**Key Differences Explained:**

**1. Lazy vs Eager Loading:**
- **BeanFactory:** Beans are created only when you call `getBean()`
- **ApplicationContext:** All singleton beans are created at application startup
- **Benefit of Eager:** Catch configuration errors early (fail-fast)

**2. Why ApplicationContext is Preferred:**
- More features (event handling, AOP, internationalization)
- Better integration with Spring's infrastructure
- Standard for Spring Boot and modern Spring applications
- Catches errors at startup instead of runtime
- Provides a centralized configuration and lifecycle management

---

#### 💡 ApplicationContext in Practice

**Basic Usage in Spring Boot:**
```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = 
            SpringApplication.run(MyApp.class, args);
        
        // Get a specific bean
        UserService service = context.getBean(UserService.class);
        
        // Inspect all beans
        String[] beanNames = context.getBeanDefinitionNames();
        System.out.println("Total beans: " + beanNames.length);
        
        // Check if bean exists
        boolean hasBean = context.containsBean("userService");
    }
}
```

**Using ApplicationContextAware (Access Context in Beans):**
```java
@Component
public class BeanDiscovery implements ApplicationContextAware {
    private ApplicationContext context;
    
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        this.context = applicationContext;
    }
    
    public void listAllBeans() {
        String[] beans = context.getBeanDefinitionNames();
        Arrays.stream(beans).forEach(System.out::println);
    }
    
    public <T> T getBean(Class<T> clazz) {
        return context.getBean(clazz);
    }
}
```

**Standalone ApplicationContext (Non-Boot):**
```java
// Java-based configuration
public class StandaloneApp {
    public static void main(String[] args) {
        ApplicationContext context = 
            new AnnotationConfigApplicationContext(AppConfig.class);
        
        UserService service = context.getBean(UserService.class);
        service.doSomething();
        
        // Close context when done
        ((AnnotationConfigApplicationContext) context).close();
    }
}
```

**Common ApplicationContext Methods:**
```java
// Get bean by type
UserService service = context.getBean(UserService.class);

// Get bean by name
Object bean = context.getBean("userService");

// Get bean by name and type
UserService service = context.getBean("userService", UserService.class);

// Check if bean exists
boolean exists = context.containsBean("userService");

// Get all beans of a type
Map<String, UserService> beans = context.getBeansOfType(UserService.class);

// Publish application event
context.publishEvent(new OrderCreatedEvent(this, orderId));

// Get environment properties
String value = context.getEnvironment().getProperty("app.name");
```

---

**Interview Tip:**  
Always use `ApplicationContext` in production. `BeanFactory` is only for very resource-constrained environments or legacy code.

---

## 3. Spring Modules

### 🔹 Spring Core
**Purpose:** Foundation of Spring Framework

**Provides:**
- IoC Container - manages object lifecycle
- Bean management - creates, configures, and wires beans
- DI support - injects dependencies automatically

**This is the heart of Spring - everything else builds on top of it.**

---

### 🔹 Spring Context

**Definition:**  
Spring Context is a Spring Framework module that builds on top of Spring Core to provide enterprise-level services and advanced IoC container features.

---

#### Understanding the Architecture

```
Spring Context (Advanced Features)
        ↓
   Built on top of
        ↓
Spring Core (Basic IoC + DI)
```

**Spring Core provides:**
- Basic BeanFactory container
- Bean lifecycle management
- Dependency injection

**Spring Context adds:**
- ApplicationContext (enhanced container)
- Event handling system
- Internationalization (i18n)
- Resource loading mechanisms
- Scheduled task support
- Environment abstraction

---

#### Real-World Analogy

**Spring Core** is like a basic **warehouse**:
- Stores items (beans)
- Tracks inventory (bean registry)
- Delivers items when requested (dependency injection)

**Spring Context** is like a **smart logistics system** built on that warehouse:
- Communicates events (shipment notifications)
- Supports multiple languages (international operations)
- Schedules deliveries automatically
- Loads resources from multiple sources
- Manages environment-specific configurations

---

#### Key Features of Spring Context

---

##### 1. ApplicationContext - The Enhanced Container

ApplicationContext is the main interface of Spring Context module. It extends BeanFactory and adds enterprise features.

```java
// Spring Boot automatically creates ApplicationContext
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = 
            SpringApplication.run(MyApp.class, args);
        
        // Access the enhanced container
        UserService service = context.getBean(UserService.class);
    }
}
```

**What it does:**
- Creates and manages all beans
- Handles dependency injection automatically
- Provides infrastructure services (events, AOP, i18n)
- Eager initialization (creates beans at startup, not on-demand)

---

##### 2. Event Propagation System

Spring Context provides a publish-subscribe pattern for application events, allowing different parts of your application to communicate without tight coupling.

**Complete Example:**

```java
// 1. Define custom event
public class UserRegisteredEvent extends ApplicationEvent {
    private final String email;
    private final String userId;
    
    public UserRegisteredEvent(Object source, String userId, String email) {
        super(source);
        this.userId = userId;
        this.email = email;
    }
    
    public String getEmail() { return email; }
    public String getUserId() { return userId; }
}

// 2. Publish event (in UserService)
@Service
public class UserService {
    private final ApplicationEventPublisher eventPublisher;
    
    public UserService(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }
    
    public User registerUser(String email, String password) {
        // Create user logic
        User user = createUser(email, password);
        
        // Publish event - other parts can react
        eventPublisher.publishEvent(
            new UserRegisteredEvent(this, user.getId(), email)
        );
        
        return user;
    }
}

// 3. Listen to event (in EmailService)
@Component
public class WelcomeEmailListener {
    @EventListener
    public void handleUserRegistered(UserRegisteredEvent event) {
        System.out.println("Sending welcome email to: " + event.getEmail());
        // Send welcome email logic
    }
}

// 4. Listen to same event (in AnalyticsService)
@Component
public class AnalyticsListener {
    @EventListener
    public void handleUserRegistered(UserRegisteredEvent event) {
        System.out.println("Tracking new user: " + event.getUserId());
        // Track analytics logic
    }
}
```

**Benefits:**
- ✅ Loose coupling - services don't need to know about each other
- ✅ Single Responsibility - UserService focuses on user creation, not emails/analytics
- ✅ Extensibility - Add new listeners without changing existing code

---

##### 3. Internationalization (i18n) Support

Spring Context provides MessageSource for supporting multiple languages.

**Setup:**

```properties
# messages_en.properties
greeting=Hello, {0}!
welcome=Welcome to our application

# messages_es.properties  
greeting=¡Hola, {0}!
welcome=Bienvenido a nuestra aplicación

# messages_fr.properties
greeting=Bonjour, {0}!
welcome=Bienvenue dans notre application
```

**Usage:**
```java
@Service
public class GreetingService {
    private final MessageSource messageSource;
    
    public GreetingService(MessageSource messageSource) {
        this.messageSource = messageSource;
    }
    
    public String greet(String name, Locale locale) {
        return messageSource.getMessage(
            "greeting", 
            new Object[]{name}, 
            locale
        );
    }
}

// Usage
greet("Saurav", Locale.ENGLISH);              // "Hello, Saurav!"
greet("Saurav", Locale.forLanguageTag("es")); // "¡Hola, Saurav!"
greet("Saurav", Locale.FRENCH);               // "Bonjour, Saurav!"
```

---

##### 4. Resource Loading

Spring Context simplifies loading resources from various locations.

```java
@Service
public class ConfigLoader {
    private final ResourceLoader resourceLoader;
    
    public ConfigLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }
    
    public void loadResources() throws IOException {
        // Load from classpath
        Resource classpathResource = 
            resourceLoader.getResource("classpath:config.properties");
        
        // Load from file system
        Resource fileResource = 
            resourceLoader.getResource("file:/path/to/file.txt");
        
        // Load from URL
        Resource urlResource = 
            resourceLoader.getResource("https://example.com/config.json");
        
        // Read content
        try (InputStream is = classpathResource.getInputStream()) {
            String content = new String(is.readAllBytes());
            System.out.println(content);
        }
    }
}
```

---

##### 5. Scheduled Tasks

Spring Context enables task scheduling with `@Scheduled` annotation.

```java
@Component
@EnableScheduling
public class DataSyncJob {
    
    // Run every 5 seconds
    @Scheduled(fixedRate = 5000)
    public void syncData() {
        System.out.println("Syncing data: " + LocalDateTime.now());
    }
    
    // Run at 2 AM every day
    @Scheduled(cron = "0 0 2 * * *")
    public void dailyBackup() {
        System.out.println("Running daily backup...");
    }
    
    // Run 10 seconds after previous execution completes
    @Scheduled(fixedDelay = 10000)
    public void processQueue() {
        System.out.println("Processing queue...");
    }
}
```

---

##### 6. Environment Abstraction

Spring Context provides Environment interface for accessing properties and profiles.

```java
@Component
public class DatabaseConfig {
    private final Environment env;
    
    public DatabaseConfig(Environment env) {
        this.env = env;
    }
    
    @PostConstruct
    public void init() {
        // Get properties
        String dbUrl = env.getProperty("spring.datasource.url");
        String dbUser = env.getProperty("spring.datasource.username");
        
        // Check active profiles
        String[] profiles = env.getActiveProfiles();
        System.out.println("Active profiles: " + Arrays.toString(profiles));
        
        // Conditional logic based on profile
        if (env.acceptsProfiles(Profiles.of("dev"))) {
            System.out.println("Running in DEV mode");
        } else if (env.acceptsProfiles(Profiles.of("prod"))) {
            System.out.println("Running in PROD mode");
        }
    }
}
```

---

#### 🎯 Interview-Ready Summary

**Q: What is Spring Context?**

**A:** Spring Context is a Spring Framework module that builds on top of Spring Core to provide enterprise-level features. While Spring Core provides basic IoC container and dependency injection, Spring Context adds ApplicationContext (enhanced container with eager initialization), event publishing/listening system, internationalization support, resource loading from multiple sources, scheduled task execution, and environment abstraction for profiles and properties. It's essential for building real-world enterprise applications.

**Key Points:**
- Built on Spring Core
- Provides ApplicationContext (enhanced BeanFactory)
- Event-driven architecture support (publish-subscribe)
- i18n support for multi-language applications
- Resource loading abstraction
- Scheduling capabilities
- Environment and profile management

---

#### 📊 Spring Core vs Spring Context Comparison

| Feature | Spring Core | Spring Context |
|---------|-------------|----------------|
| **Container** | BeanFactory (basic) | ApplicationContext (advanced) |
| **Bean Creation** | Basic instantiation | Eager initialization + lifecycle |
| **Events** | ❌ No | ✅ Yes (ApplicationEvent) |
| **i18n** | ❌ No | ✅ Yes (MessageSource) |
| **Scheduling** | ❌ No | ✅ Yes (@Scheduled) |
| **Resource Loading** | ❌ Limited | ✅ Yes (multi-source) |
| **Environment/Profiles** | ❌ No | ✅ Yes (Environment) |
| **Use Case** | Foundation only | Production applications |

**Bottom Line:** You always use Spring Context in real applications because ApplicationContext (from Context module) is the standard container that includes all Core features plus enterprise capabilities.

---

**Interview Tip:** Remember that Spring Context = Spring Core + Enterprise Features. ApplicationContext extends BeanFactory, so you get everything from Core plus advanced functionality.

---

### 🔹 Spring AOP (Aspect-Oriented Programming)

**Definition:**  
AOP (Aspect-Oriented Programming) is a programming paradigm that allows you to separate **cross-cutting concerns** from your business logic, making code cleaner, more maintainable, and reusable.

---

#### What Problem Does AOP Solve?

**The Problem - Code Duplication Across Layers:**

Imagine you need to add logging to every service method:

```java
@Service
public class UserService {
    public User createUser(User user) {
        System.out.println("Starting createUser..."); // Logging
        long startTime = System.currentTimeMillis();   // Performance monitoring
        
        try {
            // Actual business logic
            User savedUser = userRepository.save(user);
            
            System.out.println("createUser completed"); // Logging
            long duration = System.currentTimeMillis() - startTime;
            System.out.println("Execution time: " + duration + "ms"); // Performance
            
            return savedUser;
        } catch (Exception e) {
            System.out.println("Error in createUser: " + e); // Error logging
            throw e;
        }
    }
    
    public User getUser(Long id) {
        System.out.println("Starting getUser...");
        long startTime = System.currentTimeMillis();
        
        try {
            User user = userRepository.findById(id);
            
            System.out.println("getUser completed");
            long duration = System.currentTimeMillis() - startTime;
            System.out.println("Execution time: " + duration + "ms");
            
            return user;
        } catch (Exception e) {
            System.out.println("Error in getUser: " + e);
            throw e;
        }
    }
    
    // Same boilerplate repeated for every method!
}
```

**Problems:**
- ❌ Logging/monitoring code clutters business logic
- ❌ Same code repeated in every method
- ❌ Hard to maintain (change logging? update everywhere!)
- ❌ Violates Single Responsibility Principle
- ❌ Makes code harder to read and understand

---

**The Solution - AOP:**

With AOP, separate the cross-cutting concerns:

```java
@Service
public class UserService {
    // Clean business logic only!
    public User createUser(User user) {
        return userRepository.save(user);
    }
    
    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}

// Logging handled separately by AOP
@Aspect
@Component
public class LoggingAspect {
    @Around("execution(* com.example.service.*.*(..))")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("Starting " + joinPoint.getSignature());
        long startTime = System.currentTimeMillis();
        
        try {
            Object result = joinPoint.proceed(); // Execute actual method
            
            long duration = System.currentTimeMillis() - startTime;
            System.out.println("Completed in " + duration + "ms");
            
            return result;
        } catch (Exception e) {
            System.out.println("Error: " + e);
            throw e;
        }
    }
}
```

**Benefits:**
- ✅ Business logic stays clean and focused
- ✅ Cross-cutting concerns centralized in one place
- ✅ Easy to maintain and modify
- ✅ Reusable across multiple classes
- ✅ Can be enabled/disabled easily

---

#### Real-World Analogy

**Without AOP - Every Chef Cleans Their Own Station:**
- Chef A cooks pasta + cleans station
- Chef B cooks pizza + cleans station
- Chef C cooks dessert + cleans station
- Problem: Cleaning code mixed with cooking, repeated everywhere

**With AOP - Dedicated Cleaning Staff:**
- Chefs focus only on cooking (business logic)
- Cleaning staff handles all stations automatically (aspect)
- Separation of concerns, more efficient

---

#### What Are Cross-Cutting Concerns?

**Cross-cutting concerns** are functionalities that span multiple layers of your application:

| Concern | Description | Example |
|---------|-------------|---------|
| **Logging** | Recording method calls, parameters, results | Log every service method call |
| **Security** | Authentication/authorization checks | Verify user permissions before method execution |
| **Transactions** | Database transaction management | Auto-commit or rollback based on success/failure |
| **Performance Monitoring** | Track method execution time | Measure how long each method takes |
| **Caching** | Store frequently accessed data | Cache method results to avoid repeated DB calls |
| **Exception Handling** | Centralized error handling | Log all exceptions, send alerts |
| **Auditing** | Track who did what and when | Record user actions for compliance |

**These concerns "cut across" multiple parts of your application - hence "cross-cutting".**

---

#### Core AOP Terminology (Explained Simply)

---

##### 1. Aspect

**What:** A module that encapsulates a cross-cutting concern  
**Think:** The "what" - what functionality you want to add (logging, security, etc.)

```java
@Aspect
@Component
public class LoggingAspect {
    // This entire class is an Aspect
    // It encapsulates logging logic
}
```

---

##### 2. Join Point

**What:** A point in your program's execution where an aspect can be applied  
**Think:** Potential points where you can inject behavior (method calls, exceptions, etc.)

**In Spring AOP, Join Points are:**
- Method execution (most common)
- Method entry
- Method exit
- Exception throwing

```java
@Service
public class UserService {
    public User createUser(User user) {  // ← Join Point (method execution)
        return userRepository.save(user); // ← Join Point (method call)
    } // ← Join Point (method exit)
}
```

**Note:** In Spring AOP, you can only apply aspects to method executions (not field access, constructor calls, etc.)

---

##### 3. Advice

**What:** The action taken by an aspect at a particular join point  
**Think:** The "when" and "what to do" - code that runs before/after/around the method

**Types of Advice:**

| Advice Type | When It Runs | Use Case |
|-------------|--------------|----------|
| **@Before** | Before method execution | Input validation, logging parameters |
| **@After** | After method execution (always) | Cleanup, resource release |
| **@AfterReturning** | After successful execution | Log return value, post-processing |
| **@AfterThrowing** | After exception is thrown | Error logging, alerts |
| **@Around** | Before AND after (most powerful) | Performance monitoring, caching |

---

##### 4. Pointcut

**What:** An expression that matches join points  
**Think:** The "where" - which methods should the advice apply to

```java
@Pointcut("execution(* com.example.service.*.*(..))")
//           ↑          ↑        ↑         ↑  ↑  ↑
//        keyword   return   package   class method params
//                   type
```

**Pointcut Expression Parts:**
- `execution(...)` - matches method execution
- `*` (first) - any return type
- `com.example.service` - package
- `*` (second) - any class in package
- `*(..)` - any method with any parameters

---

##### 5. Target Object

**What:** The actual object being advised (the object whose method is intercepted)  
**Think:** The original object you're adding behavior to

```java
@Service
public class UserService {  // ← Target Object
    public void createUser() { ... }
}
```

---

##### 6. Proxy

**What:** An object created by AOP framework that wraps the target object  
**Think:** A wrapper that intercepts calls and adds aspect behavior

```
Client → Proxy (with aspects) → Target Object (your service)
         ↑
      Adds logging, security, etc.
```

---

#### Types of Advice - Detailed Examples

---

##### 1. @Before - Execute Before Method

Runs before the target method executes. Cannot prevent method execution.

```java
@Aspect
@Component
public class SecurityAspect {
    
    @Before("execution(* com.example.service.UserService.*(..))")
    public void checkSecurity(JoinPoint joinPoint) {
        System.out.println("Checking security for: " + joinPoint.getSignature());
        
        // Validate user permissions
        String methodName = joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();
        
        System.out.println("Method: " + methodName);
        System.out.println("Arguments: " + Arrays.toString(args));
        
        // Could throw exception to prevent execution
        if (!hasPermission()) {
            throw new SecurityException("Access denied!");
        }
    }
    
    private boolean hasPermission() {
        // Check user permissions
        return true;
    }
}
```

**When to use:**
- Input validation
- Security checks
- Logging input parameters
- Pre-conditions

---

##### 2. @After - Execute After Method (Always)

Runs after method completes, regardless of outcome (success or exception).

```java
@Aspect
@Component
public class ResourceAspect {
    
    @After("execution(* com.example.service.*.*(..))")
    public void cleanup(JoinPoint joinPoint) {
        System.out.println("Cleaning up resources after: " + joinPoint.getSignature());
        
        // Cleanup code that runs always
        // closeConnections();
        // releaseResources();
    }
}
```

**When to use:**
- Resource cleanup
- Releasing locks
- Closing connections
- Audit logging

---

##### 3. @AfterReturning - Execute After Successful Return

Runs only if method completes successfully (no exception).

```java
@Aspect
@Component
public class AuditAspect {
    
    @AfterReturning(
        pointcut = "execution(* com.example.service.UserService.createUser(..))",
        returning = "result"
    )
    public void logSuccess(JoinPoint joinPoint, User result) {
        System.out.println("User created successfully: " + result.getId());
        System.out.println("Method: " + joinPoint.getSignature());
        
        // Log to audit system
        auditLog("User created: " + result.getEmail());
    }
    
    private void auditLog(String message) {
        // Save to audit table
    }
}
```

**When to use:**
- Success logging
- Post-processing return values
- Sending notifications
- Updating statistics

---

##### 4. @AfterThrowing - Execute After Exception

Runs only if method throws an exception.

```java
@Aspect
@Component
public class ErrorHandlingAspect {
    
    @AfterThrowing(
        pointcut = "execution(* com.example.service.*.*(..))",
        throwing = "error"
    )
    public void handleError(JoinPoint joinPoint, Throwable error) {
        System.out.println("Exception in: " + joinPoint.getSignature());
        System.out.println("Error: " + error.getMessage());
        
        // Log to error tracking system
        logError(joinPoint.getSignature().toString(), error);
        
        // Send alert
        sendAlert("Error in " + joinPoint.getSignature());
    }
    
    private void logError(String method, Throwable error) {
        // Log to file or monitoring system
    }
    
    private void sendAlert(String message) {
        // Send email/Slack notification
    }
}
```

**When to use:**
- Error logging
- Sending error alerts
- Error recovery
- Exception tracking

---

##### 5. @Around - Most Powerful (Before AND After)

Wraps around method execution. You control if/when the actual method runs.

```java
@Aspect
@Component
public class PerformanceAspect {
    
    @Around("execution(* com.example.service.*.*(..))")
    public Object measurePerformance(ProceedingJoinPoint joinPoint) throws Throwable {
        // BEFORE method execution
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Starting: " + methodName);
        long startTime = System.currentTimeMillis();
        
        Object result = null;
        try {
            // EXECUTE the actual method
            result = joinPoint.proceed();
            
            // AFTER successful execution
            long duration = System.currentTimeMillis() - startTime;
            System.out.println(methodName + " completed in " + duration + "ms");
            
            if (duration > 1000) {
                System.out.println("WARNING: Slow method detected!");
            }
            
        } catch (Exception e) {
            // AFTER exception
            System.out.println(methodName + " failed: " + e.getMessage());
            throw e;
        }
        
        return result;
    }
}
```

**When to use:**
- Performance monitoring
- Caching (check cache before execution)
- Transaction management
- Retry logic
- Profiling

**Important:** You MUST call `joinPoint.proceed()` to execute the actual method, and MUST return its result.

---

#### Pointcut Expressions - Detailed Guide

**Basic Syntax:**
```
execution(modifiers? return-type package.class.method(params) throws?)
          ↑          ↑          ↑                        ↑       ↑
       optional   required   required               required  optional
```

---

##### Wildcard Patterns

| Pattern | Meaning | Example |
|---------|---------|---------|
| `*` | Any single element | `*(*)` = any method with one param |
| `..` | Zero or more | `*(..)` = any method, any params |
| `+` | Subclasses too | `UserService+` = UserService and subclasses |

---

##### Common Pointcut Expressions

**1. All methods in a package:**
```java
@Before("execution(* com.example.service.*.*(..))")
//                   ↑         ↑            ↑  ↑
//              any return  package      class method
```

**2. Specific method:**
```java
@Before("execution(* com.example.service.UserService.createUser(..))")
//                                                    ↑           ↑
//                                              exact method  any params
```

**3. Methods with specific return type:**
```java
@Before("execution(User com.example.service.*.*(..))")
//                   ↑
//              must return User
```

**4. Methods with specific parameters:**
```java
@Before("execution(* com.example.service.*.*(Long, String))")
//                                           ↑
//                                   must have exactly 2 params
```

**5. Methods starting with 'get':**
```java
@Before("execution(* com.example.service.*.get*(..))")
//                                          ↑
//                                      starts with 'get'
```

**6. Combine multiple conditions (AND):**
```java
@Pointcut("execution(* com.example.service.*.*(..)) && " +
          "execution(public * *(..))")
public void publicServiceMethods() {}
```

**7. Combine multiple conditions (OR):**
```java
@Pointcut("execution(* com.example.service.UserService.*(..)) || " +
          "execution(* com.example.service.OrderService.*(..))")
public void userOrOrderMethods() {}
```

---

##### Annotation-based Pointcuts

**Match methods with specific annotation:**

```java
// Custom annotation
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Loggable {
}

// Use annotation on methods
@Service
public class UserService {
    @Loggable  // ← This method will be logged
    public User createUser(User user) {
        return userRepository.save(user);
    }
    
    public User getUser(Long id) {  // ← This won't be logged
        return userRepository.findById(id);
    }
}

// Aspect that matches annotated methods
@Aspect
@Component
public class LoggingAspect {
    @Before("@annotation(com.example.annotation.Loggable)")
    public void logMethod(JoinPoint joinPoint) {
        System.out.println("Logging: " + joinPoint.getSignature());
    }
}
```

---

#### Complete Real-World Example

Let's create a comprehensive logging and performance monitoring system:

```java
// 1. Custom annotation for methods we want to monitor
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Monitored {
}

// 2. Service with business logic
@Service
public class OrderService {
    
    @Monitored
    public Order createOrder(Order order) {
        // Simulate some processing
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        
        // Business logic
        return orderRepository.save(order);
    }
    
    @Monitored
    public Order getOrder(Long id) {
        return orderRepository.findById(id)
            .orElseThrow(() -> new OrderNotFoundException(id));
    }
    
    public void deleteOrder(Long id) {
        // Not monitored
        orderRepository.deleteById(id);
    }
}

// 3. Comprehensive Aspect
@Aspect
@Component
@Slf4j
public class MonitoringAspect {
    
    // Reusable pointcut definition
    @Pointcut("@annotation(com.example.annotation.Monitored)")
    public void monitoredMethods() {}
    
    // Log before method execution
    @Before("monitoredMethods()")
    public void logBefore(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();
        
        log.info("→ Executing: {} with args: {}", methodName, Arrays.toString(args));
    }
    
    // Log after successful execution
    @AfterReturning(pointcut = "monitoredMethods()", returning = "result")
    public void logAfterSuccess(JoinPoint joinPoint, Object result) {
        String methodName = joinPoint.getSignature().getName();
        log.info("✓ Completed: {} returned: {}", methodName, result);
    }
    
    // Handle exceptions
    @AfterThrowing(pointcut = "monitoredMethods()", throwing = "error")
    public void logError(JoinPoint joinPoint, Throwable error) {
        String methodName = joinPoint.getSignature().getName();
        log.error("✗ Failed: {} with error: {}", methodName, error.getMessage());
        
        // Send alert for critical errors
        if (error instanceof OrderNotFoundException) {
            sendAlert("Order not found in " + methodName);
        }
    }
    
    // Measure performance
    @Around("monitoredMethods()")
    public Object measurePerformance(ProceedingJoinPoint joinPoint) throws Throwable {
        String methodName = joinPoint.getSignature().getName();
        long startTime = System.currentTimeMillis();
        
        try {
            Object result = joinPoint.proceed();
            
            long duration = System.currentTimeMillis() - startTime;
            log.info("⏱ {} executed in {}ms", methodName, duration);
            
            // Alert if method is slow
            if (duration > 500) {
                log.warn("⚠ Slow method detected: {} took {}ms", methodName, duration);
            }
            
            return result;
            
        } catch (Throwable e) {
            long duration = System.currentTimeMillis() - startTime;
            log.error("⏱ {} failed after {}ms", methodName, duration);
            throw e;
        }
    }
    
    private void sendAlert(String message) {
        // Send to monitoring system
        log.warn("ALERT: {}", message);
    }
}
```

**Output when calling `orderService.createOrder(order)`:**
```
→ Executing: createOrder with args: [Order{id=null, amount=100}]
⏱ createOrder executed in 105ms
✓ Completed: createOrder returned: Order{id=1, amount=100}
```

---

#### Real-World Use Cases

##### 1. Transaction Management (Built-in Spring AOP)

```java
@Service
public class BankService {
    
    @Transactional  // AOP-based transaction management
    public void transfer(String from, String to, double amount) {
        accountRepository.debit(from, amount);
        accountRepository.credit(to, amount);
        
        // If any exception occurs, entire transaction rolls back automatically
    }
}
```

##### 2. Security/Authorization

```java
@Aspect
@Component
public class SecurityAspect {
    
    @Before("@annotation(requiresRole)")
    public void checkRole(JoinPoint joinPoint, RequiresRole requiresRole) {
        String requiredRole = requiresRole.value();
        String userRole = getCurrentUserRole();
        
        if (!userRole.equals(requiredRole)) {
            throw new AccessDeniedException("Required role: " + requiredRole);
        }
    }
}

// Usage
@Service
public class AdminService {
    @RequiresRole("ADMIN")
    public void deleteUser(Long id) {
        // Only admins can execute this
    }
}
```

##### 3. Caching

```java
@Aspect
@Component
public class CachingAspect {
    private Map<String, Object> cache = new ConcurrentHashMap<>();
    
    @Around("@annotation(Cacheable)")
    public Object cache(ProceedingJoinPoint joinPoint) throws Throwable {
        String key = joinPoint.getSignature().toString() + 
                     Arrays.toString(joinPoint.getArgs());
        
        // Check cache
        if (cache.containsKey(key)) {
            System.out.println("Cache hit for: " + key);
            return cache.get(key);
        }
        
        // Cache miss - execute method
        Object result = joinPoint.proceed();
        cache.put(key, result);
        System.out.println("Cache miss - stored: " + key);
        
        return result;
    }
}
```

---

#### When to Use AOP

**✅ Good Use Cases:**
- Logging (method entry/exit, parameters, results)
- Performance monitoring (execution time tracking)
- Security (authentication, authorization)
- Transaction management
- Error handling (centralized exception logging)
- Caching
- Audit trails
- Retry logic

**❌ Avoid AOP For:**
- Core business logic (keep it explicit, not hidden)
- Complex conditional logic
- Modifying business data
- Cases where aspect order matters critically
- When debugging becomes difficult

---

#### 🎯 Interview-Ready Summary

**Q: What is Spring AOP?**

**A:** Spring AOP (Aspect-Oriented Programming) is a programming paradigm that separates cross-cutting concerns (like logging, security, transactions) from business logic. It works by creating proxies around target objects and intercepting method calls to apply additional behavior. AOP uses concepts like Aspects (modules with cross-cutting logic), Advice (code to execute - @Before, @After, @Around), Pointcuts (expressions matching where to apply advice), and Join Points (method executions). This keeps business logic clean, centralizes common functionality, and makes code more maintainable. For example, `@Transactional` uses AOP to manage database transactions automatically.

**Key Points:**
- Separates cross-cutting concerns from business logic
- Works via proxies that intercept method calls
- Five advice types: @Before, @After, @AfterReturning, @AfterThrowing, @Around
- Pointcut expressions define where aspects apply
- Built on proxy pattern (JDK or CGLIB proxies)
- Used for logging, security, transactions, caching, monitoring
- Keeps code DRY and maintainable

**Interview Tip:** Always mention that Spring AOP only supports method-level interception (not field access or constructor calls), and it uses proxies (which means self-invocation doesn't trigger aspects)

---

### 🔹 Spring Expression Language (SpEL)
**Purpose:** Powerful expression language for querying and manipulating objects at runtime

**Common use cases:**
```java
// 1. System properties
@Value("#{systemProperties['user.name']}")
private String username;

// 2. Mathematical operations
@Value("#{T(java.lang.Math).random() * 100}")
private double randomNumber;

// 3. Parse lists from properties
@Value("#{'${my.list}'.split(',')}")
private List<String> myList;

// 4. Conditional expressions
@Value("#{${is.production} ? 'prod-db' : 'test-db'}")
private String databaseName;

// 5. Access other beans
@Value("#{userService.getUserCount()}")
private int userCount;

// 6. Null-safe navigation
@Value("#{config.database?.url ?: 'localhost'}")
private String dbUrl;
```

**Interview Tip:** SpEL is useful but avoid overusing it - complex expressions are hard to debug.

---

## 4. Annotation-based Configuration

### 🔹 Component Scanning Annotations

**Key Concept:** These annotations mark classes as Spring-managed beans. Spring discovers them during component scanning and creates bean instances automatically.

---

**1. @Component - Generic Stereotype**

Use for any Spring-managed component that doesn't fit other categories.

```java
@Component
public class EmailUtil {
    public void sendEmail(String to, String message) {
        System.out.println("Sending email to " + to);
        // Email sending logic
    }
}
```

**When to use:**
- Utility classes
- Helper components
- Generic application components

---

**2. @Service - Business Logic Layer**

Marks service layer classes that contain business logic.

```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User createUser(User user) {
        // Business logic
        validateUser(user);
        return userRepository.save(user);
    }
    
    private void validateUser(User user) {
        // Validation logic
    }
}
```

**When to use:**
- Business logic and orchestration
- Transaction management (with `@Transactional`)
- Coordinating between different repositories

**Note:** Semantically same as `@Component`, but clearly indicates service layer.

---

**3. @Repository - Data Access Layer**

Marks DAO/Repository classes. **Important:** Adds automatic exception translation for persistence exceptions.

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByEmail(String email);
}

// For custom repositories
@Repository
public class CustomUserRepository {
    private final JdbcTemplate jdbcTemplate;
    
    public CustomUserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    
    public List<User> findActiveUsers() {
        return jdbcTemplate.query(
            "SELECT * FROM users WHERE active = true",
            new UserRowMapper()
        );
    }
}
```

**When to use:**
- Database access layer
- JPA repositories
- Custom data access logic

**Special benefit:** Converts database-specific exceptions to Spring's `DataAccessException` hierarchy.

---

**4. @Controller - Web MVC Controller**

Marks Spring MVC controllers that return views (HTML pages).

```java
@Controller
public class HomeController {
    
    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("message", "Welcome!");
        return "index"; // Returns view name (e.g., index.html)
    }
    
    @GetMapping("/about")
    public String about() {
        return "about"; // Returns about.html
    }
}
```

**When to use:**
- Traditional MVC applications
- Returning HTML views (JSP, Thymeleaf, etc.)
- Server-side rendering

---

**5. @RestController - REST API Controller**

`@RestController = @Controller + @ResponseBody`

Every method returns data (JSON/XML) instead of view names.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll(); // Returns JSON
    }
    
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id); // Returns JSON
    }
    
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user); // Returns JSON
    }
}
```

**When to use:**
- REST APIs
- Microservices
- Backend for frontend applications (React, Angular, etc.)

---

#### 📊 Component Annotation Comparison

| Annotation | Layer | Purpose | Special Feature |
|------------|-------|---------|----------------|
| @Component | Generic | Any component | Base stereotype |
| @Service | Business | Business logic | Semantic clarity, transaction support |
| @Repository | Data Access | Database operations | Exception translation |
| @Controller | Web | MVC controllers | Returns views |
| @RestController | Web API | REST endpoints | Returns data (JSON/XML) |

**Interview Tip:** All are essentially `@Component` with semantic meaning, except `@Repository` adds exception translation.

### 🔹 Configuration Annotations

**@Configuration** marks a class as a source of bean definitions. Methods annotated with `@Bean` inside a `@Configuration` class will be registered as Spring beans.

**When to use @Configuration + @Bean:**
- Creating beans from third-party libraries (where you can't add `@Component`)
- Custom initialization logic
- Conditional bean creation
- Multiple beans of same type with different configurations

---

**Basic Example:**

```java
@Configuration
public class AppConfig {
    
    @Bean
    public DataSource dataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        ds.setUsername("root");
        ds.setPassword("password");
        ds.setMaximumPoolSize(20);
        return ds;
    }
    
    @Bean
    public UserRepository userRepository() {
        return new UserRepositoryImpl(dataSource());
    }
    
    @Bean
    public UserService userService() {
        return new UserService(userRepository());
    }
}
```

---

**Advanced Example - Multiple Configurations:**

```java
@Configuration
public class DatabaseConfig {
    
    @Bean
    @Primary // This will be used by default
    public DataSource primaryDataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://localhost:3306/primary_db");
        return ds;
    }
    
    @Bean
    public DataSource secondaryDataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://localhost:3306/secondary_db");
        return ds;
    }
}

@Configuration
public class CacheConfig {
    
    @Bean
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager("users", "products");
    }
}
```

---

**@Bean vs @Component:**

| Aspect | @Component | @Bean |
|--------|-----------|-------|
| **Where to use** | On class definition | On method in @Configuration |
| **Use case** | Your own classes | Third-party classes or custom logic |
| **Control** | Spring controls instantiation | You control instantiation |
| **Example** | `@Service public class UserService` | `@Bean public RestTemplate restTemplate()` |

**Example showing both:**
```java
// Your own class - use @Component
@Service
public class UserService {
    private final RestTemplate restTemplate;
    
    public UserService(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }
}

// Third-party class - use @Bean
@Configuration
public class WebConfig {
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate(); // Can't add @Component to RestTemplate
    }
}
```

### 🔹 Dependency Resolution Annotations

**Problem:** What happens when multiple beans of the same type exist?

**Spring throws:** `NoUniqueBeanDefinitionException`

**Solutions:**
1. Use `@Primary` to mark default bean
2. Use `@Qualifier` to specify which bean to inject
3. Use `@Autowired List<Type>` to inject all beans

---

#### Solution 1: Using @Qualifier

`@Qualifier` lets you specify which bean to inject by name.

```java
// Interface
interface MessageSender {
    void send(String message);
}

// Implementation 1
@Component("emailSender")
public class EmailSender implements MessageSender {
    @Override
    public void send(String message) {
        System.out.println("Sending email: " + message);
    }
}

// Implementation 2
@Component("smsSender")
public class SmsSender implements MessageSender {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

// Using @Qualifier to choose specific implementation
@Service
public class NotificationService {
    private final MessageSender messageSender;
    
    // Explicitly choose emailSender
    @Autowired
    public NotificationService(@Qualifier("emailSender") MessageSender messageSender) {
        this.messageSender = messageSender;
    }
    
    public void notify(String message) {
        messageSender.send(message);
    }
}
```

---

#### Solution 2: Using @Primary

`@Primary` marks a bean as the default choice when multiple candidates exist.

```java
@Component
@Primary // This will be injected by default
public class EmailSender implements MessageSender {
    @Override
    public void send(String message) {
        System.out.println("Sending email: " + message);
    }
}

@Component
public class SmsSender implements MessageSender {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

@Service
public class NotificationService {
    private final MessageSender messageSender;
    
    // EmailSender will be injected (marked as @Primary)
    public NotificationService(MessageSender messageSender) {
        this.messageSender = messageSender;
    }
}
```

---

#### Solution 3: Inject All Implementations

Use `List<Type>` to inject all beans of that type.

```java
@Service
public class NotificationService {
    private final List<MessageSender> messageSenders;
    
    public NotificationService(List<MessageSender> messageSenders) {
        this.messageSenders = messageSenders;
    }
    
    public void notifyAll(String message) {
        for (MessageSender sender : messageSenders) {
            sender.send(message);
        }
    }
}
```

---

#### Solution 4: Field Name Matching

Spring can match by field name if no `@Qualifier` is specified.

```java
@Service
public class NotificationService {
    private final MessageSender emailSender; // Matches bean name "emailSender"
    
    public NotificationService(MessageSender emailSender) {
        this.emailSender = emailSender;
    }
}
```

---

#### 🎯 Which Solution to Use?

| Scenario | Solution | Why |
|----------|----------|-----|
| One clear default choice | `@Primary` | Simple, clean code |
| Different beans for different use cases | `@Qualifier` | Explicit, clear intent |
| Need all implementations | `List<Type>` | Strategy/Chain of Responsibility pattern |
| Simple cases | Field name matching | Less verbose |

**Interview Tip:**  
- `@Primary` = "Use this by default"
- `@Qualifier` = "I want this specific one"
- `List<Type>` = "Give me all of them"

---

## 5. Bean Scopes (CRITICAL CONCEPT)

**Bean Scope** defines the lifecycle and visibility of a bean in the Spring container.

---

### 1. Singleton Scope (DEFAULT)

**Definition:** One instance per Spring container (shared across entire application)

```java
@Component
@Scope("singleton") // Default, can be omitted
public class DatabaseConnection {
    private int connectionCount = 0;
    
    public void incrementCount() {
        connectionCount++;
    }
    
    public int getConnectionCount() {
        return connectionCount;
    }
}
```

**Behavior:**
```java
// Both get same instance
DatabaseConnection conn1 = context.getBean(DatabaseConnection.class);
DatabaseConnection conn2 = context.getBean(DatabaseConnection.class);

conn1.incrementCount(); // connectionCount = 1
System.out.println(conn2.getConnectionCount()); // Prints: 1 (same instance!)
System.out.println(conn1 == conn2); // true
```

**When to use:**
- Stateless services
- Repository/DAO classes
- Configuration beans
- **Most common scope - 90% of beans**

**⚠️ Important:** Singleton beans are **NOT thread-safe** by default. Keep them stateless or use synchronization.

---

### 2. Prototype Scope

**Definition:** New instance every time bean is requested

```java
@Component
@Scope("prototype")
public class Order {
    private String orderId;
    private double amount;
    
    // Each Order is a new instance
}
```

**Behavior:**
```java
// Each call creates new instance
Order order1 = context.getBean(Order.class);
Order order2 = context.getBean(Order.class);

System.out.println(order1 == order2); // false - different instances
```

**When to use:**
- Stateful beans (maintaining state per request)
- Objects with mutable state
- Heavy objects that shouldn't be shared
- Command objects, DTOs

**Note:** Spring doesn't manage the complete lifecycle of prototype beans. `@PreDestroy` won't be called automatically.

---

### 3. Request Scope (Web Applications Only)

**Definition:** One instance per HTTP request

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, 
       proxyMode = ScopedProxyMode.TARGET_CLASS)
public class ShoppingCart {
    private List<String> items = new ArrayList<>();
    
    public void addItem(String item) {
        items.add(item);
    }
    
    public List<String> getItems() {
        return items;
    }
}
```

**Behavior:**
- Each HTTP request gets a new `ShoppingCart` instance
- Items added in one request are not visible in another request

**When to use:**
- Request-specific data
- Shopping carts (temporary, per request)
- Form data holders

**Important:** `proxyMode` is required when injecting request-scoped bean into singleton bean.

---

### 4. Session Scope (Web Applications Only)

**Definition:** One instance per HTTP session (same user)

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_SESSION, 
       proxyMode = ScopedProxyMode.TARGET_CLASS)
public class UserPreferences {
    private String theme = "light";
    private String language = "en";
    
    // Getters and setters
}
```

**Behavior:**
- Same instance throughout user's session
- Different users get different instances
- Data persists across multiple requests from same user

**When to use:**
- User preferences
- Authentication data
- Session-specific configuration

---

### 5. Application Scope (Web Applications Only)

**Definition:** One instance per ServletContext (shared across entire web application)

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_APPLICATION, 
       proxyMode = ScopedProxyMode.TARGET_CLASS)
public class AppConfig {
    private Map<String, String> globalSettings = new HashMap<>();
    
    // Shared across all users and sessions
}
```

**Behavior:**
- One instance for entire application
- Similar to singleton but scoped to ServletContext

**When to use:**
- Application-wide configuration
- Global counters/statistics
- Shared resources across all users

---

### 📊 Bean Scope Comparison Table

| Scope | Instances | Lifecycle | Use Case | Thread-Safe? |
|-------|-----------|-----------|----------|--------------|
| **Singleton** | 1 per container | Container lifetime | Stateless services | Make stateless |
| **Prototype** | New each time | Not managed by Spring | Stateful objects | Per instance |
| **Request** | 1 per HTTP request | Request duration | Request data | Yes (isolated) |
| **Session** | 1 per HTTP session | Session duration | User data | Yes (per user) |
| **Application** | 1 per ServletContext | Application lifetime | Global config | Make stateless |

---

### ⚠️ Common Pitfalls

**1. Singleton is NOT Thread-Safe**

```java
@Service // Singleton by default
public class CounterService {
    private int count = 0; // Shared mutable state - DANGER!
    
    public void increment() {
        count++; // Not thread-safe in concurrent requests
    }
}
```

**Fix:** Make it stateless or use `AtomicInteger`:
```java
@Service
public class CounterService {
    private final AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet(); // Thread-safe
    }
}
```

---

**2. Injecting Prototype into Singleton**

```java
@Component
@Scope("prototype")
public class Order {
    // ...
}

@Service // Singleton
public class OrderService {
    @Autowired
    private Order order; // Problem: Order is injected once, not per request
}
```

**Fix:** Use `ObjectProvider` or `@Lookup`:
```java
@Service
public class OrderService {
    @Autowired
    private ObjectProvider<Order> orderProvider;
    
    public void processOrder() {
        Order order = orderProvider.getObject(); // Gets new instance
    }
}
```

---

### 🎓 Interview Tips

**Q: Is Singleton scope thread-safe?**  
A: No! Singleton means one instance per container, not thread-safe. Keep beans stateless or use proper synchronization.

**Q: What's the default scope?**  
A: Singleton

**Q: When to use Prototype?**  
A: When beans have mutable state and shouldn't be shared (e.g., Order, Invoice objects)

**Q: What's the difference between Singleton and Application scope?**  
A: Singleton is per Spring container; Application scope is per ServletContext (web apps). In most cases, they're similar.

**Q: Why do we need proxyMode for Request/Session scopes?**  
A: To inject shorter-lived beans (request/session) into longer-lived beans (singleton). Spring creates a proxy that fetches the correct instance at runtime.

---

## 6. Common Interview Questions

### Q1: What is the difference between @Component, @Service, @Repository?

**Short Answer:**  
All are stereotypes for component scanning, but:
- `@Component` - Generic component
- `@Service` - Business logic layer (semantic clarity)
- `@Repository` - DAO layer + adds automatic exception translation for persistence exceptions

**Detailed Answer:**

**Technical difference:**
- `@Service` and `@Repository` are specializations of `@Component`
- Only `@Repository` provides additional functionality: automatic exception translation
- `@Service` is purely semantic (same as `@Component` technically)

**Exception Translation Example:**
```java
@Repository
public class UserRepository {
    public void save(User user) {
        // If SQLException occurs, Spring translates it to DataAccessException
    }
}
```

**When to use:**
```java
@Repository // Data access layer
public interface UserRepository extends JpaRepository<User, Long> {}

@Service // Business logic
public class UserService {
    private final UserRepository userRepository;
    // Business logic here
}

@Component // Utility classes, helpers
public class EmailUtil {
    public void sendEmail(String to, String message) {}
}
```

**Interview Tip:** Mention that `@Repository` adds exception translation, which is the key difference.

---

### Q2: Constructor vs Setter vs Field Injection - which is best?

**Answer:** Constructor injection is best.

**Reasons:**
1. **Mandatory dependencies are explicit** - clear what's required
2. **Enables immutability** - use `final` fields
3. **Easier to test** - no reflection needed, can pass mocks directly
4. **Prevents partial initialization** - object is always in valid state
5. **Circular dependencies are detected early** - at compile time

**Comparison:**

```java
// ✅ BEST - Constructor Injection
@Service
public class UserService {
    private final UserRepository userRepository; // Can be final
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// ⚠️ OK for optional dependencies - Setter Injection
@Service
public class ReportService {
    private ExportService exportService; // Optional
    
    @Autowired(required = false)
    public void setExportService(ExportService exportService) {
        this.exportService = exportService;
    }
}

// ❌ AVOID - Field Injection
@Service
public class OrderService {
    @Autowired
    private PaymentService paymentService; // Hard to test
}
```

**Why field injection is bad:**
```java
// Testing with field injection - requires reflection
@Test
public void testOrderService() {
    OrderService service = new OrderService();
    // Can't inject mock without reflection - BAD!
}

// Testing with constructor injection - easy
@Test
public void testUserService() {
    UserRepository mockRepo = mock(UserRepository.class);
    UserService service = new UserService(mockRepo); // Easy!
    // Test service...
}
```

---

### Q3: What happens if multiple beans of same type exist?

**Short Answer:**  
Spring throws `NoUniqueBeanDefinitionException`. 

**Solutions:**
1. Use `@Primary` to mark default bean
2. Use `@Qualifier` to specify which bean
3. Use `@Autowired List<Type>` to inject all beans

**Detailed Example:**

```java
// Problem: Two implementations
@Component
public class EmailSender implements MessageSender {}

@Component
public class SmsSender implements MessageSender {}

@Service
public class NotificationService {
    @Autowired
    private MessageSender sender; // Error: NoUniqueBeanDefinitionException
}
```

**Solution 1: @Primary**
```java
@Component
@Primary // This is default
public class EmailSender implements MessageSender {}

@Service
public class NotificationService {
    @Autowired
    private MessageSender sender; // EmailSender injected
}
```

**Solution 2: @Qualifier**
```java
@Service
public class NotificationService {
    @Autowired
    @Qualifier("smsSender")
    private MessageSender sender; // Explicitly choose SmsSender
}
```

**Solution 3: Inject all**
```java
@Service
public class NotificationService {
    @Autowired
    private List<MessageSender> senders; // Gets both
}
```

---

### Q4: Explain Spring Bean lifecycle

**Short Answer:**  
Instantiation → Populate Properties → BeanNameAware → BeanFactoryAware → ApplicationContextAware → @PostConstruct → InitializingBean → Custom init → Ready → @PreDestroy → DisposableBean → Custom destroy

**Detailed Answer with most common phases:**

```java
@Component
public class DatabaseConnection {
    
    @Autowired
    private DataSource dataSource;
    
    // 1. Constructor called
    public DatabaseConnection() {
        System.out.println("1. Constructor called");
    }
    
    // 2. Dependencies injected (@Autowired)
    
    // 3. @PostConstruct - most commonly used
    @PostConstruct
    public void init() {
        System.out.println("3. @PostConstruct - initialize resources");
        // dataSource is available here
    }
    
    // 4. Bean is ready for use
    
    // 5. @PreDestroy - cleanup before shutdown
    @PreDestroy
    public void cleanup() {
        System.out.println("5. @PreDestroy - cleanup resources");
    }
}
```

**Interview Tip:** Remember the most important phases:
1. Constructor
2. Dependency Injection
3. @PostConstruct (initialization)
4. Bean Ready
5. @PreDestroy (cleanup)

---

### Q5: Is Singleton scope thread-safe?

**Short Answer:** No! Singleton scope means one instance per container, **NOT thread-safe**.

**Detailed Answer:**

```java
@Service // Singleton by default
public class UserService {
    private int requestCount = 0; // DANGER: Shared mutable state
    
    public void handleRequest() {
        requestCount++; // NOT thread-safe in concurrent environment
    }
}
```

**Why it's not thread-safe:**
- Multiple threads can access same singleton instance
- If bean has mutable state, race conditions occur
- No built-in synchronization

**Solutions:**

```java
// Solution 1: Make it stateless (BEST)
@Service
public class UserService {
    private final UserRepository userRepository; // Immutable dependency
    
    public void handleRequest(User user) {
        // No mutable state - thread-safe
    }
}

// Solution 2: Use thread-safe data structures
@Service
public class CounterService {
    private final AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet(); // Thread-safe
    }
}

// Solution 3: Synchronization (less preferred)
@Service
public class CounterService {
    private int count = 0;
    
    public synchronized void increment() {
        count++; // Thread-safe but slower
    }
}
```

**Interview Tip:** Emphasize that **stateless beans are thread-safe**. Keep singleton beans stateless whenever possible.

---

### Q6: When to use @PostConstruct?

**Answer:** For initialization logic that needs injected dependencies.

**Why @PostConstruct?**
- Constructor executes before dependency injection
- `@PostConstruct` executes after all dependencies are injected
- Guaranteed that all `@Autowired` fields are set

**Wrong - Dependencies not available:**
```java
@Service
public class CacheService {
    @Autowired
    private DatabaseService databaseService;
    
    public CacheService() {
        // databaseService is NULL here!
        loadCache(); // ERROR: databaseService not injected yet
    }
}
```

**Correct - Use @PostConstruct:**
```java
@Service
public class CacheService {
    @Autowired
    private DatabaseService databaseService;
    
    @PostConstruct
    public void init() {
        // databaseService is available here
        loadCache(); // Works correctly
    }
    
    private void loadCache() {
        List<Data> data = databaseService.fetchAll();
        // Initialize cache
    }
}
```

**Common use cases:**
- Opening database/network connections
- Loading cache from database
- Validating configuration
- Registering listeners
- Initializing third-party libraries

---

### Q7: What is the difference between IoC and DI?

**Answer:**
- **IoC (Inversion of Control)** is a **principle** - control of object lifecycle is inverted from application to framework
- **DI (Dependency Injection)** is a **technique** - dependencies are provided from outside rather than created internally
- DI is one way to implement IoC

**Analogy:**
- IoC = "Don't call us, we'll call you"
- DI = "We'll give you what you need"

**Example:**
```java
// Without IoC/DI - you control everything
public class OrderService {
    private PaymentService paymentService = new PaymentService(); // You create
}

// With IoC/DI - Spring controls lifecycle and injects dependency
@Service
public class OrderService {
    private final PaymentService paymentService;
    
    public OrderService(PaymentService paymentService) { // Spring injects
        this.paymentService = paymentService;
    }
}
```

---

### Q8: Difference between ApplicationContext and BeanFactory?

**Short Answer:** Always use `ApplicationContext` - it's the enhanced, modern container.

**Comparison:**

| Feature | BeanFactory | ApplicationContext |
|---------|-------------|-------------------|
| Loading | Lazy (on-demand) | Eager (startup) |
| Auto-wiring | No | Yes |
| Events | No | Yes |
| i18n | No | Yes |
| AOP | Manual | Automatic |
| Use case | Legacy/constrained | Modern apps |

**Why ApplicationContext is better:**
- Catches configuration errors at startup (fail-fast)
- More features (events, i18n, AOP)
- Standard in Spring Boot
- Better performance in most cases

**Interview Tip:** BeanFactory is rarely used in modern applications. Mention it's legacy.

---

### Q9: Can you inject a Prototype bean into a Singleton?

**Answer:** Yes, but it won't behave as expected without special handling.

**Problem:**
```java
@Component
@Scope("prototype")
public class Order {}

@Service // Singleton
public class OrderService {
    @Autowired
    private Order order; // Injected ONCE during OrderService creation
    
    public void processOrder() {
        // Always uses same Order instance - NOT what we want!
    }
}
```

**Solution 1: Use ObjectProvider**
```java
@Service
public class OrderService {
    @Autowired
    private ObjectProvider<Order> orderProvider;
    
    public void processOrder() {
        Order order = orderProvider.getObject(); // New instance each time
    }
}
```

**Solution 2: Use @Lookup (method injection)**
```java
@Service
public abstract class OrderService {
    
    @Lookup
    protected abstract Order createOrder(); // Spring overrides this
    
    public void processOrder() {
        Order order = createOrder(); // New instance each time
    }
}
```

---

## 7. Quick Revision Checklist

### Core Concepts
- [ ] **Understand DI types** - Constructor (best) > Setter (optional deps) > Field (avoid)
- [ ] **Know IoC principle** - Framework controls object lifecycle
- [ ] **Memorize bean lifecycle** - Instantiation → DI → @PostConstruct → Ready → @PreDestroy
- [ ] **Differentiate IoC vs DI** - IoC is principle, DI is technique

### Containers & Context
- [ ] **ApplicationContext vs BeanFactory** - Always use ApplicationContext (eager, more features)
- [ ] **When to use @PostConstruct** - After DI, for initialization logic
- [ ] **When to use @PreDestroy** - Before shutdown, for cleanup

### Annotations
- [ ] **Component stereotypes** - @Component (generic), @Service (business), @Repository (data + exception translation), @Controller (MVC), @RestController (REST API)
- [ ] **@Qualifier vs @Primary** - @Primary = default, @Qualifier = specific choice
- [ ] **@Configuration + @Bean** - For third-party classes or custom initialization
- [ ] **@Component vs @Bean** - @Component on classes, @Bean on methods

### Bean Scopes
- [ ] **Master bean scopes** - Singleton (default, shared), Prototype (new each time), Request, Session, Application
- [ ] **Singleton is NOT thread-safe** - Keep stateless or use synchronization
- [ ] **When to use Prototype** - For stateful beans (Order, Invoice)
- [ ] **proxyMode for web scopes** - Required when injecting request/session beans into singleton

### Dependency Resolution
- [ ] **Multiple beans problem** - @Primary, @Qualifier, or List<Type>
- [ ] **Field name matching** - Spring can match by parameter/field name
- [ ] **Constructor selection** - Single constructor = auto-used, multiple = use @Autowired

### Advanced
- [ ] **Lombok integration** - @RequiredArgsConstructor for constructor injection
- [ ] **Prototype in Singleton** - Use ObjectProvider or @Lookup
- [ ] **Spring AOP basics** - Cross-cutting concerns (@Before, @After, @Around)
- [ ] **SpEL expressions** - @Value with #{...} for dynamic values

### Interview Preparation
- [ ] Explain why constructor injection is best (immutability, testability)
- [ ] Difference between @Component, @Service, @Repository
- [ ] Bean lifecycle with @PostConstruct and @PreDestroy
- [ ] Singleton scope is not thread-safe - explain with example
- [ ] How to handle multiple beans of same type
- [ ] IoC vs DI explanation with examples
- [ ] When to use @Bean vs @Component

---

## 8. Practice Exercise

Create a complete Spring Boot application demonstrating all core concepts:

### Exercise Requirements:

#### 1. Create Domain Model
```java
// User entity
public class User {
    private Long id;
    private String name;
    private String email;
    // Constructor, getters, setters
}
```

#### 2. Create Multiple Repository Implementations
```java
// Interface
public interface UserRepository {
    User save(User user);
    User findById(Long id);
    List<User> findAll();
}

// Implementation 1: In-Memory
@Repository
public class InMemoryUserRepository implements UserRepository {
    // Use HashMap to store users
}

// Implementation 2: Database (mock)
@Repository
@Primary // This should be default
public class JpaUserRepository implements UserRepository {
    // Mock database operations
}
```

#### 3. Create Service Layer
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    // Use constructor injection with @RequiredArgsConstructor
    // Add @PostConstruct to log initialization
    // Add business logic methods
}
```

#### 4. Create REST Controller
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    // Use constructor injection
    // Implement CRUD endpoints
}
```

#### 5. Demonstrate Bean Scopes
```java
// Singleton service
@Service
public class ConfigService {
    // Shared configuration
}

// Prototype order
@Component
@Scope("prototype")
public class Order {
    private String orderId;
    private double amount;
}

// Request-scoped cart
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, 
       proxyMode = ScopedProxyMode.TARGET_CLASS)
public class ShoppingCart {
    private List<String> items = new ArrayList<>();
}
```

#### 6. Create Configuration Class
```java
@Configuration
public class AppConfig {
    
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    
    @Bean
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager("users");
    }
}
```

#### 7. Add AOP for Logging
```java
@Aspect
@Component
public class LoggingAspect {
    
    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        // Log method execution
    }
    
    @Around("execution(* com.example.service.*.*(..))")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) 
            throws Throwable {
        // Log execution time
    }
}
```

#### 8. Test Dependency Injection
```java
// Create a NotificationService that can send notifications
// via different channels (Email, SMS)

// Use @Qualifier to inject specific implementation
// Use @Primary on one implementation
// Test with List<MessageSender> to get all implementations
```

#### 9. Demonstrate Bean Lifecycle
```java
@Service
public class LifecycleBean {
    
    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }
    
    @PreDestroy
    public void cleanup() {
        System.out.println("Bean destroyed");
    }
}
```

#### 10. Test Application
- Run the application
- Test all REST endpoints
- Verify @Primary and @Qualifier work correctly
- Check @PostConstruct and @PreDestroy logs
- Test bean scopes (singleton vs prototype)

---

### Bonus Challenges:

1. **Add multiple profiles** (dev, prod) with different configurations
2. **Implement custom validation** using `@Valid` and custom validators
3. **Add exception handling** with `@ControllerAdvice`
4. **Create custom annotations** combining multiple Spring annotations
5. **Implement pagination** in repository layer
6. **Add unit tests** for service layer (testing DI)
7. **Add integration tests** for REST endpoints

---

### Expected Outcomes:

After completing this exercise, you should be able to:
- ✅ Use constructor injection with Lombok
- ✅ Understand @Primary vs @Qualifier in action
- ✅ See bean lifecycle with @PostConstruct/@PreDestroy
- ✅ Work with different bean scopes
- ✅ Create configuration beans with @Bean
- ✅ Implement AOP for cross-cutting concerns
- ✅ Distinguish between stereotype annotations
- ✅ Test singleton vs prototype behavior
- ✅ Handle multiple implementations cleanly

---

**This foundation is CRITICAL - 60% of Spring Boot interviews test these concepts!**

**Pro Tip:** Code this exercise from scratch without looking at references. This solidifies your understanding better than reading alone.

---

## 9. Final Summary - Key Takeaways

### 🌟 What Makes Spring Powerful?

Spring transformed Java enterprise development by solving fundamental problems:
1. **Eliminates boilerplate** - No more manual object wiring
2. **Promotes best practices** - Built-in support for design patterns
3. **Testability first** - Easy mocking and unit testing
4. **Modular architecture** - Use only what you need
5. **Production-ready** - Battle-tested in millions of applications

---

### 🔑 The Three Pillars of Spring

#### 1. IoC (Inversion of Control)
**Principle:** Framework controls object lifecycle, not your code
- Spring container manages beans
- You declare dependencies, Spring wires them
- Configuration over manual creation

#### 2. DI (Dependency Injection)
**Technique:** Dependencies injected from outside
- Constructor injection (recommended)
- Setter injection (optional dependencies)
- Loose coupling, easier testing

#### 3. AOP (Aspect-Oriented Programming)
**Approach:** Separate cross-cutting concerns
- Logging, security, transactions
- Keep business logic clean
- Declarative approach (@Transactional, @Cacheable)

---

### 📋 Spring in One Page

**Bean Management:**
- Beans are objects managed by Spring container
- Created via @Component, @Service, @Repository, @Controller, @RestController
- Or created explicitly via @Bean in @Configuration

**Scopes:**
- Singleton (default) - one instance, not thread-safe
- Prototype - new instance each time
- Request/Session/Application - web scopes

**Lifecycle:**
1. Instantiate bean
2. Inject dependencies
3. Call @PostConstruct
4. Bean ready for use
5. Call @PreDestroy before shutdown

**Multiple Beans:**
- Use @Primary for default
- Use @Qualifier for specific bean
- Use List<Type> for all beans

**Best Practices:**
- Constructor injection > Setter > Field
- Keep singleton beans stateless
- Use Lombok @RequiredArgsConstructor
- @Repository adds exception translation
- ApplicationContext > BeanFactory

---

### 💡 Golden Rules

1. **Default to constructor injection** - Immutability, testability, clarity
2. **Singleton beans must be stateless** - Or use thread-safe structures
3. **Use @Primary for defaults** - Cleaner than @Qualifier everywhere
4. **@PostConstruct for initialization** - Dependencies are available
5. **@Component for your classes** - @Bean for third-party/custom logic
6. **Keep it simple** - Don't over-engineer, use Spring's conventions

---

### 🎯 Interview Success Formula

**Top 5 Questions You MUST Know:**

1. **IoC vs DI** - IoC is principle, DI is technique
2. **Constructor vs Field injection** - Constructor is better (testability, immutability)
3. **@Component vs @Service vs @Repository** - @Repository adds exception translation
4. **Singleton thread-safety** - NOT thread-safe, keep stateless
5. **Bean lifecycle** - Constructor ? DI ? @PostConstruct ? Ready ? @PreDestroy

**Answer Framework:**
- Start with short answer
- Provide code example
- Mention real-world use case
- Discuss trade-offs

**Common Mistakes to Avoid:**
- Saying singleton is thread-safe ?
- Recommending field injection ?
- Confusing @Component and @Bean usage ?
- Missing @Primary vs @Qualifier distinction ?

---

### 🚀 Next Steps

**After mastering these basics:**

1. **Spring Boot** - Auto-configuration, starters, embedded server
2. **Spring Data JPA** - Repository pattern, query methods
3. **Spring MVC** - REST APIs, @RestController, exception handling
4. **Spring Security** - Authentication, authorization
5. **Spring AOP** - Custom aspects, pointcut expressions
6. **Spring Cloud** - Microservices, service discovery

---

### ✅ You've Mastered Spring Basics When You Can:

- [ ] Explain IoC and DI with real examples
- [ ] Create beans using @Component and @Bean
- [ ] Implement constructor injection with Lombok
- [ ] Resolve multiple bean conflicts (@Primary/@Qualifier)
- [ ] Use different bean scopes appropriately
- [ ] Manage bean lifecycle (@PostConstruct/@PreDestroy)
- [ ] Explain thread-safety in singleton beans
- [ ] Choose between stereotype annotations
- [ ] Apply AOP for cross-cutting concerns
- [ ] Debug common Spring configuration issues

---

## 📚 Remember

**Spring Framework Basics = Foundation for:**
- Spring Boot
- Spring Data
- Spring Security
- Spring Cloud
- Microservices
- Enterprise Java Development

**Master these concepts and you'll excel in 60% of Spring Boot interviews!**

---

**"Spring is not about doing more, it's about doing less - less boilerplate, less configuration, less coupling. Master the basics, and the rest will follow."**

---

*Last Updated: May 2026*
*Version: 2.0 - Enhanced with practical examples and deep explanations*
