# Spring Ecosystem - Complete Guide

## Table of Contents

### [1. What is Spring?](#1-what-is-spring)
   - [1.1 Why Java Alone Was Not Enough](#11-why-java-alone-was-not-enough)
   - [1.2 Definition](#12-definition)
   - [1.3 Breakdown of the Definition](#13-breakdown-of-the-definition)
     - [1.3.1 Spring is a Java Framework](#131-spring-is-a-java-framework)
     - [1.3.2 Designed for Enterprise Applications](#132-designed-for-enterprise-applications)
     - [1.3.3 Encourages Cleaner Code](#133-encourages-cleaner-code)
     - [1.3.4 Supports Modular Design](#134-supports-modular-design)
     - [1.3.5 Improves Maintainability](#135-improves-maintainability)
   - [1.4 Core Idea of Spring](#14-core-idea-of-spring)
   - [1.5 Two Most Important Concepts](#15-two-most-important-concepts)
     - [1.5.1 IoC (Inversion of Control)](#151-ioc-inversion-of-control)
     - [1.5.2 DI (Dependency Injection)](#152-di-dependency-injection)
     - [1.5.3 IoC vs DI](#153-ioc-vs-di)
     - [1.5.4 DI in Spring - Real Example](#154-di-in-spring---real-example)
         - [1.5.5 How ApplicationContext Uses DI to Wire Beans](#155-how-applicationcontext-uses-di-to-wire-beans)
   - [1.6 Common Doubts](#16-common-doubts)
     - [1.6.1 What Does "Spring Container Creates Bean" Mean?](#161-what-does-spring-container-creates-bean-mean)
     - [1.6.2 How Does Spring Choose Constructor?](#162-how-does-spring-choose-constructor)
     - [1.6.3 Which Annotations Lead to Bean Creation?](#163-which-annotations-lead-to-bean-creation)
     - [1.6.4 Bean Creation Using @Bean](#164-bean-creation-using-bean)
   - [1.7 Types of Dependency Injection](#17-types-of-dependency-injection)
   - [1.8 Which DI is Best and How to Decide?](#18-which-di-is-best-and-how-to-decide)
     - [1.8.1 Constructor Injection (Recommended)](#181-constructor-injection-recommended)
     - [1.8.2 Using Lombok for Constructor Injection](#182-using-lombok-for-constructor-injection)
     - [1.8.3 When to Use Setter Injection](#183-when-to-use-setter-injection)
     - [1.8.4 Why Field Injection is Not Preferred](#184-why-field-injection-is-not-preferred)
     - [1.8.5 Quick Decision Rule](#185-quick-decision-rule)

### [2. What is Spring MVC?](#2-what-is-spring-mvc)
   - [2.1 Definition](#21-definition)
   - [2.2 MVC Pattern](#22-mvc-pattern)
   - [2.3 Key Components in Spring MVC](#23-key-components-in-spring-mvc)
   - [2.4 Request Flow in Spring MVC](#24-request-flow-in-spring-mvc)
   - [2.5 Spring MVC Example](#25-spring-mvc-example)
   - [2.6 Traditional XML-based Configuration](#26-traditional-xml-based-configuration)
     - [2.6.1 Required Configuration Files](#261-required-configuration-files)
     - [2.6.2 Why This is Manual and Complex](#262-why-this-is-manual-and-complex)
   - [2.7 Component Scanning in Spring MVC: Deep Dive](#27-component-scanning-in-spring-mvc-deep-dive)
     - [2.7.1 Key Concepts](#271-key-concepts)
     - [2.7.2 Example Project Structure](#272-example-project-structure)
     - [2.7.3 Using Java Configuration](#273-using-java-configuration)
     - [2.7.4 Using XML Configuration](#274-using-xml-configuration)
     - [2.7.5 Spring Boot Approach](#275-spring-boot-approach)
     - [2.7.6 Explicit Bean Definition with @Bean](#276-explicit-bean-definition-with-bean)
     - [2.7.7 Common Pitfalls and Fixes](#277-common-pitfalls-and-fixes)
     - [2.7.8 How to Verify Beans Are Registered](#278-how-to-verify-beans-are-registered)

### [3. What is Spring Boot?](#3-what-is-spring-boot)
   - [3.1 Definition](#31-definition)
   - [3.2 Problems Spring Boot Solves](#32-problems-spring-boot-solves)
     - [3.2.1 Manual XML/Java Configuration](#321-manual-xmljava-configuration)
   - [3.3 Main Features](#33-main-features)
   - [3.4 Minimal Spring Boot Application](#34-minimal-spring-boot-application)
   - [3.5 Auto-Configuration vs XML Configuration](#35-auto-configuration-vs-xml-configuration)
     - [3.5.1 What Spring MVC XML Required](#351-what-spring-mvc-xml-required)
     - [3.5.2 What Spring Boot Auto-Configuration Does](#352-what-spring-boot-auto-configuration-does)
     - [3.5.3 How @SpringBootApplication Works](#353-how-springbootapplication-works)
     - [3.5.4 Key Differences Table](#354-key-differences-table)
     - [3.5.5 Why Developers Prefer Spring Boot](#355-why-developers-prefer-spring-boot)
     - [3.5.6 Is XML Completely Eliminated?](#356-is-xml-completely-eliminated)

### [4. Relationship Between Spring, Spring MVC, and Spring Boot](#4-relationship-between-spring-spring-mvc-and-spring-boot)
   - [4.1 Simple Hierarchy](#41-simple-hierarchy)
   - [4.2 Easy Analogy](#42-easy-analogy)
   - [4.3 In One Line](#43-in-one-line)

### [5. Code Comparison: Plain Java vs Spring MVC vs Spring Boot](#5-code-comparison-plain-java-vs-spring-mvc-vs-spring-boot)
   - [5.1 Plain Java (No Spring)](#51-plain-java-no-spring)
   - [5.2 Spring (Core + MVC)](#52-spring-core--mvc)
   - [5.3 Spring Boot (Modern Approach)](#53-spring-boot-modern-approach)

### [6. Quick Summary](#6-quick-summary)

---

---

## 1. What is Spring?

### 1.1 Why Java Alone Was Not Enough

Before frameworks like Spring existed, developers built web applications using core Java + Servlets + JSP.

Example using Java Servlet API:

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
 
    protected void doGet(HttpServletRequest req, HttpServletResponse res)
            throws IOException {
 
        res.setContentType("text/plain");
        res.getWriter().write("Hello World");
    }
}
```

Problems with plain Java for enterprise applications:
- Too much boilerplate code
- Manual object wiring and dependency management
- Manual servlet setup and lifecycle management
- Manual transaction handling
- Manual configuration management
- Hard to test due to tight coupling
- No built-in architecture or standard patterns
- Configuration hell (XML files)

Java alone can build anything, but for enterprise applications it becomes slow, repetitive, and hard to maintain. As applications grew bigger, the pain of maintaining them increased.

So frameworks like Spring were created to solve these problems.

### 1.2 Definition
Spring is a Java framework used to build enterprise applications in a cleaner, modular, and maintainable way.

Java is a programming language. Spring is an application framework built on top of Java.

### 1.3 Breakdown of the Definition

#### 1.3.1 Spring is a Java Framework
- A framework gives structure and reusable tools to build applications faster.
- Spring is built for Java applications and provides ready solutions for common backend needs.
- Instead of building everything from scratch, Spring helps with:
    - creating APIs
    - component management
    - database integration
    - security integration
    - configuration management

This allows developers to focus more on business logic and less on plumbing code.

#### 1.3.2 Designed for Enterprise Applications
Enterprise applications are usually:
- large-scale
- complex
- used by many users
- expected to be secure, reliable, and high-performance

Examples:
- banking systems
- e-commerce platforms
- insurance/healthcare platforms
- large backend microservices

Spring was created to support these real-world enterprise requirements.

#### 1.3.3 Encourages Cleaner Code
"Cleaner" in Spring typically means:
- less boilerplate code
- easier-to-read class structure
- less manual wiring of objects
- cleaner separation of concerns

Spring helps this through:
- annotation-based configuration
- dependency injection (DI)
- aspect-oriented programming (AOP) for cross-cutting concerns like logging and security

#### 1.3.4 Supports Modular Design
Modular means the application is split into small focused parts.

Spring ecosystem is modular, so you choose only what you need:
- Spring Web: REST APIs and web apps
- Spring Data: database access layer
- Spring Security: authentication and authorization
- Spring Boot: fast startup and auto-configuration
- Spring Cloud: distributed systems and microservices

This keeps applications flexible, reusable, and easier to evolve.

#### 1.3.5 Improves Maintainability
Maintainable applications are easier to:
- update
- test
- debug
- scale

Spring improves maintainability via:
- loose coupling using DI
- consistent architecture patterns
- strong testing support with Spring Test and JUnit
- clear layering (controller, service, repository)

### 1.4 Core Idea of Spring
Spring solves common enterprise problems like:
- object creation and dependency wiring
- transaction management
- database integration
- security integration
- easier testing

### 1.5 Two Most Important Concepts

#### 1.5.1 IoC (Inversion of Control)
IoC means the control of object creation and lifecycle is moved from your application code to the Spring container.

In plain Java, classes often create their own dependencies using `new`. In Spring, you declare dependencies and the container creates, wires, and manages them.

Without IoC (class controls dependencies):

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

Issue: `OrderService` is tightly coupled to `PaymentService` implementation.

#### 1.5.2 DI (Dependency Injection)
DI is the practical way to implement IoC. Instead of creating dependencies inside a class, dependencies are provided from outside.

With DI (dependency provided from outside):

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

#### 1.5.3 IoC vs DI
**Important interview point:**
- IoC is a principle: framework controls object lifecycle.
- DI is a technique: dependencies are injected into objects.
- In Spring, IoC container (`ApplicationContext`) uses DI to wire beans.

#### 1.5.4 DI in Spring - Real Example

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

Flow:
1. Spring container creates `UserRepository` bean.
2. Spring creates `UserService` and injects `UserRepository`.
3. Spring creates `UserController` and injects `UserService`.
4. Request to `/user` returns value from layered components.

#### 1.5.5 How ApplicationContext Uses DI to Wire Beans

Start with a simple class design:

```java
class Engine {
    public void start() {
        System.out.println("Engine started");
    }
}

class Car {
    private final Engine engine;

    // Constructor injection keeps Car loosely coupled.
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is moving");
    }
}
```

Register these classes as Spring beans:

```java
import org.springframework.stereotype.Component;

@Component
class Engine {
    public void start() {
        System.out.println("Engine started");
    }
}

@Component
class Car {
    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is moving");
    }
}
```

```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "com.example")
class AppConfig {
}
```

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        Car car = context.getBean(Car.class);
        car.drive();
    }
}
```

What happens internally in `ApplicationContext`:
1. Scans packages from `@ComponentScan`.
2. Finds bean candidates (`Engine`, `Car`).
3. Creates `Engine` bean.
4. Sees `Car(Engine engine)` constructor and resolves the dependency.
5. Creates `Car` bean by injecting `Engine`.
6. Stores both beans in the container for reuse.

Quick flow:
`@Configuration/@ComponentScan` -> scan classes -> create beans -> resolve constructor dependencies -> inject dependencies -> store in `ApplicationContext`.

### 1.6 Common Doubts

#### 1.6.1 What Does "Spring Container Creates Bean" Mean?
Conceptually, yes, Spring does something like:

```java
UserRepository userRepository = new UserRepository(...);
```

But unlike manual Java code, Spring first decides which constructor to use, resolves constructor arguments from other beans/configuration, and then stores the created object inside `ApplicationContext`.

#### How does Spring choose constructor (default vs parameterized)?
- If no constructor is written: Java gives a default no-arg constructor, Spring uses it.
- If one parameterized constructor exists: Spring uses it and injects required dependencies.
- If multiple constructors exist: mark one with `@Autowired` (or keep one constructor only).
- If dependencies cannot be resolved, startup fails with a bean creation error.

Example with parameterized constructor:

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

#### 1.6.3 Which Annotations Lead to Bean Creation?
Spring container creates beans for classes discovered via component scanning or Java config:
1. `@Component`
2. `@Service`
3. `@Repository`
4. `@Controller`
5. `@RestController`
6. Methods annotated with `@Bean` inside `@Configuration`

Notes:
- `@Service`, `@Repository`, `@Controller`, and `@RestController` are specialized forms of `@Component`.
- `@Autowired` does not create beans; it injects already created beans.
- Beans are created only if classes are in the component scan path (usually main class package and subpackages).

#### 1.6.4 Bean Creation Using @Bean
Use this approach when you want to create beans explicitly in Java config, especially for:
- third-party classes (where you cannot add `@Component`)
- custom initialization logic
- conditional or environment-based wiring

Example:

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

How Spring handles this:
1. Detects `@Configuration` class during startup.
2. Executes `@Bean` method `emailClient()`.
3. Registers returned object as a bean named `emailClient`.
4. Injects it wherever `EmailClient` is required.

Usage in a service:

```java
import org.springframework.stereotype.Service;

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

`@Component` vs `@Bean`:
- Use `@Component` for your own application classes.
- Use `@Bean` when object creation needs custom logic or class is external.

### 1.7 Types of Dependency Injection
1. Constructor Injection (recommended, test-friendly, immutable)
2. Setter Injection (useful for optional dependencies)
3. Field Injection (common in tutorials, but not preferred in production)

### 1.8 Which DI is Best and How to Decide?

#### 1.8.1 Constructor Injection (Recommended)
Use constructor injection in most real projects.

Why it is best:
- makes required dependencies explicit
- object is created only in a valid state
- supports immutability (`final` fields)
- easiest for unit testing (no reflection needed)
- works well with clean architecture and SOLID design

Example:

```java
import org.springframework.stereotype.Service;

@Service
class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

#### 1.8.2 Using Lombok for Constructor Injection
**Can we use Lombok `@AllArgsConstructor` for constructor injection?**
Yes, you can. Spring can inject dependencies through Lombok-generated constructors.

Example with `@AllArgsConstructor`:

```java
import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@AllArgsConstructor
class UserService {
    private UserRepository userRepository;
    private EmailService emailService;
}
```

Recommended in most projects: `@RequiredArgsConstructor`.

```java
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
}
```

Why `@RequiredArgsConstructor` is usually better:
- includes only required (`final`/`@NonNull`) fields
- encourages immutability
- avoids accidental constructor parameters for non-dependency fields

Rule of thumb:
1. Use `@RequiredArgsConstructor` by default.
2. Use `@AllArgsConstructor` only when you intentionally want every field in constructor.

#### 1.8.3 When to Use Setter Injection
Use setter injection only for optional dependencies or values that may change after object creation.

Good fit:
- optional collaborator
- backward-compatible legacy components
- rare cases where dependency is not mandatory at startup

Example:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
class ReportService {
    private ExportService exportService; // optional

    @Autowired(required = false)
    public void setExportService(ExportService exportService) {
        this.exportService = exportService;
    }
}
```

#### 1.8.4 Why Field Injection is Not Preferred
Field injection is short, but has drawbacks:
- hard to unit test without Spring/reflection
- hides dependencies (less explicit design)
- cannot make dependencies `final`
- encourages tightly coupled and less maintainable code

Example (avoid in production code):

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
class InvoiceService {
    @Autowired
    private TaxService taxService;
}
```

#### 1.8.5 Quick Decision Rule
1. Is dependency required for class to work? Use Constructor Injection.
2. Is dependency optional? Use Setter Injection.
3. Field Injection? Avoid except very small demos/tests.

**Interview-friendly one-liner:**
"In Spring, constructor injection should be the default choice; use setter injection for optional dependencies; avoid field injection in production for better testability and maintainability."

### Why this matters
- loose coupling
- easier unit testing (you can mock dependencies)
- cleaner architecture
- better maintainability for large codebases

In this example, `UserService` does not create `UserRepository` itself. Spring injects it.

---

## 2. What is Spring MVC?

### 2.1 Definition
Spring MVC is a module of Spring used to build web applications (especially REST APIs and server-side web apps) using the Model-View-Controller pattern.

### 2.2 MVC Pattern
- Model: business/data layer
- View: UI response (HTML, JSON, etc.)
- Controller: handles HTTP requests and returns responses

### 2.3 Key Components in Spring MVC
- `DispatcherServlet`: front controller that receives every request
- `@Controller` / `@RestController`: request handling classes
- `@RequestMapping`, `@GetMapping`, `@PostMapping`: URL mapping
- `Model`: data passed to view
- View Resolver: maps logical view names to actual templates

### 2.4 Request Flow in Spring MVC
1. Client sends HTTP request.
2. `DispatcherServlet` receives it.
3. It finds matching controller method.
4. Controller calls service layer.
5. Response returned as view or JSON.

### 2.5 Spring MVC Example

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
class GreetingController {

    @GetMapping("/greet/{name}")
    public String greet(@PathVariable String name) {
        return "Hello, " + name + "!";
    }
}
```

This code maps `GET /greet/Saurav` to `Hello, Saurav!`.

### 2.6 Traditional XML-based Configuration

In traditional Spring MVC (without Spring Boot), you had to manually configure the web application using XML files. This requires multiple configuration files and explicit setup of components like `DispatcherServlet`, view resolvers, component scanning, and more.

#### 2.6.1 Required Configuration Files

**1. web.xml (Deployment Descriptor)**

This file is required to configure the servlet container and register the `DispatcherServlet`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!-- Configure DispatcherServlet -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring-mvc-config.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- Map all requests to DispatcherServlet -->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

**2. spring-mvc-config.xml (Spring MVC Configuration)**

This file configures Spring MVC components, view resolvers, and enables component scanning.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- Enable component scanning -->
    <context:component-scan base-package="com.example.controller"/>

    <!-- Enable Spring MVC annotations -->
    <mvc:annotation-driven/>

    <!-- Configure View Resolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- Static resource handling -->
    <mvc:resources mapping="/resources/**" location="/resources/"/>
</beans>
```

**3. Controller class (same as modern approach)**

```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class GreetingController {

    @GetMapping("/greet/{name}")
    @ResponseBody
    public String greet(@PathVariable String name) {
        return "Hello, " + name + "!";
    }
}
```

**Project structure for traditional Spring MVC:**
```
src/
  main/
    java/
      com/example/controller/
        GreetingController.java
    webapp/
      WEB-INF/
        web.xml
        spring-mvc-config.xml
        views/
          home.jsp
```

#### 2.6.2 Why This is Manual and Complex
- You must create and maintain multiple XML files
- Manually configure `DispatcherServlet` in `web.xml`
- Manually set up component scanning
- Manually configure view resolvers, static resources, etc.
- Deploy as WAR file to external servlet container (Tomcat, Jetty)
- No embedded server support

---

### 2.7 Component Scanning in Spring MVC: Deep Dive

In **Spring MVC**, simply putting `@Component` (or a stereotype like `@Controller`, `@Service`, `@Repository`) on a class **does not automatically** create a bean unless **component scanning** is enabled. You can enable component scanning either:

1.  **Via XML**: using `<context:component-scan>`, or
2.  **Via Java config**: using `@ComponentScan` (or via `@SpringBootApplication` in Spring Boot, which implies it).

Below is a structured walkthrough with code for both approaches, plus common pitfalls.

***

#### 2.7.1 Key Concepts

*   **Stereotype annotations**:
    *   `@Component`: generic component
    *   `@Controller`: MVC controllers
    *   `@Service`: service layer
    *   `@Repository`: persistence layer (adds exception translation)
*   **Component scanning**:
    *   Tells Spring *where* to look for those annotated classes.
    *   No scanning = no beans from annotations.
*   **Alternative**: You can also define beans explicitly using `<bean>` in XML or using `@Bean` methods in a `@Configuration` class.

***

#### 2.7.2 Example Project Structure

    com.example.demo
    ├─ config/
    │  ├─ WebConfig.java        // Java-based config (if using Java config)
    │  └─ WebAppInitializer.java // Java-based initializer (if no web.xml)
    ├─ controller/
    │  └─ HomeController.java
    ├─ service/
    │  └─ GreetingService.java
    └─ repository/
       └─ UserRepository.java

***

#### 2.7.3 Using Java Configuration (No XML)

##### Enable Component Scanning

```java
// src/main/java/com/example/demo/config/WebConfig.java
package com.example.demo.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@EnableWebMvc                   // enables Spring MVC
@ComponentScan("com.example.demo") // scans this base package and subpackages
public class WebConfig {
    // You can also add ViewResolvers, MessageConverters, etc.
}
```

If you're deploying to a servlet container without `web.xml`, you can register the `DispatcherServlet` using a `WebApplicationInitializer`:

```java
// src/main/java/com/example/demo/config/WebAppInitializer.java
package com.example.demo.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[]{}; // e.g., security or JPA config goes here
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[]{ WebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{ "/" };
    }
}
```

##### Annotated Components

```java
// src/main/java/com/example/demo/controller/HomeController.java
package com.example.demo.controller;

import com.example.demo.service.GreetingService;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.ui.Model;

@Controller
public class HomeController {

    private final GreetingService greetingService;

    public HomeController(GreetingService greetingService) { // constructor injection
        this.greetingService = greetingService;
    }

    @GetMapping("/")
    public String index(Model model) {
        model.addAttribute("message", greetingService.greet("Saurav"));
        return "home"; // returns view name "home"
    }
}
```

```java
// src/main/java/com/example/demo/service/GreetingService.java
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service
public class GreetingService {
    public String greet(String name) {
        return "Hello, " + name + "!";
    }
}
```

```java
// src/main/java/com/example/demo/repository/UserRepository.java
package com.example.demo.repository;

import org.springframework.stereotype.Repository;

@Repository
public class UserRepository {
    // data access methods
}
```

**Result**: Because `@ComponentScan("com.example.demo")` is present, Spring picks up all classes annotated with `@Controller`, `@Service`, `@Repository`, `@Component` under that package and registers them as beans.

***

#### 2.7.4 Using XML Configuration

If you prefer XML (classic Spring MVC), you must:

1.  Define the `DispatcherServlet` in `web.xml`.
2.  Enable component scanning in the servlet's context file.

##### web.xml

```xml
<!-- src/main/webapp/WEB-INF/web.xml -->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="3.1">

    <display-name>DemoApp</display-name>

    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- Load Spring context from /WEB-INF/dispatcher-servlet.xml -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value> <!-- empty: default to /WEB-INF/dispatcher-servlet.xml -->
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

##### Dispatcher Servlet Context (XML)

```xml
<!-- src/main/webapp/WEB-INF/dispatcher-servlet.xml -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         https://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         https://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/mvc
         https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- Enable @Controller, @RequestMapping, etc. -->
    <mvc:annotation-driven />

    <!-- THIS enables component scanning -->
    <context:component-scan base-package="com.example.demo" />

    <!-- Example: explicitly defined bean (optional) -->
    <!--
    <bean id="greetingService" class="com.example.demo.service.GreetingService" />
    -->

    <!-- View resolver example -->
    <!--
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    -->
</beans>
```

**Result**: With `<context:component-scan base-package="com.example.demo" />`, Spring will discover the annotated classes and register beans automatically. Without that line, your annotated classes **won't** become beans unless you define them with `<bean>` entries manually.

***

#### 2.7.5 Spring Boot Approach

If you're using Spring Boot, `@SpringBootApplication` includes `@ComponentScan` with a base package of the class it's declared on.

```java
// src/main/java/com/example/demo/DemoApplication.java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // implies @Configuration + @EnableAutoConfiguration + @ComponentScan
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

**Important**: place `DemoApplication` in a **top-level package** (e.g., `com.example.demo`) above your `controller`, `service`, `repository` packages, so component scanning reaches them.

***

#### 2.7.6 Explicit Bean Definition with @Bean

If you don't want component scanning, you can define beans explicitly:

```java
@Configuration
public class ManualConfig {

    @Bean
    public GreetingService greetingService() {
        return new GreetingService();
    }
}
```

Then you would **omit** `@Component` on `GreetingService`. This approach is useful for library classes or when you need fine-grained control over bean creation.

***

#### 2.7.7 Common Pitfalls and Fixes

1.  **No component scan configured**
    *   Symptom: `NoSuchBeanDefinitionException` when autowiring.
    *   Fix: Add `@ComponentScan` (Java) or `<context:component-scan>` (XML) or ensure `@SpringBootApplication` is in the right package.

2.  **Wrong base package**
    *   Symptom: Some beans are detected, others aren't.
    *   Fix: Ensure your base package covers all subpackages containing components.

3.  **Multiple contexts** (root vs servlet context)
    *   Symptom: Beans visible in one context but not the other.
    *   Fix: Put web-layer beans (controllers) in the **servlet** context; service/repo in **root** context—or scan both where needed.

4.  **Component not picked up due to classpath/package move**
    *   Symptom: Works locally, fails after refactor.
    *   Fix: Update `@ComponentScan` base packages or package structure.

5.  **Bean naming conflicts**
    *   Symptom: `NoUniqueBeanDefinitionException`.
    *   Fix: Use `@Qualifier`, unique bean names, or refine component scan filters.

6.  **Proxying / Scopes**
    *   For request/session-scoped beans in MVC (`@Scope("request")`, etc.), ensure `mvc:annotation-driven` or `@EnableWebMvc` is active.

***

#### 2.7.8 How to Verify Beans Are Registered

*   **Logs**: Enable debug logs for `org.springframework` to see bean creation.
*   **Programmatically**:

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import java.util.Arrays;

public class InspectContext {
    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(WebConfig.class);
        Arrays.stream(ctx.getBeanDefinitionNames()).sorted().forEach(System.out::println);
    }
}
```

***

**TL;DR:**

*   **Annotating a class is not enough by itself.**
*   You **must enable component scanning**:
    *   **XML**: `<context:component-scan base-package="..."/>`
    *   **Java**: `@ComponentScan("...")`
    *   **Spring Boot**: `@SpringBootApplication` (with correct package placement)
*   Alternatively, define beans explicitly via `<bean>` (XML) or `@Bean` (Java config).

***

## 3. What is Spring Boot?

### 3.1 Definition
Spring Boot is an opinionated extension of Spring that makes it very fast and easy to create production-ready Spring applications.

Spring Boot is not a replacement for Spring. It is built on top of Spring.

### 3.2 Problems Spring Boot Solves
Without Spring Boot, Spring projects need lots of manual setup:
- XML or Java config for many beans
- dependency version management
- web server setup (Tomcat/Jetty)
- environment and property setup

#### 3.2.1 Manual XML/Java Configuration
**Why this is manual:**
In classic Spring (without Boot auto-configuration), you usually define and wire a lot of application objects (beans) yourself.
For every layer object such as `DataSource`, `UserRepository`, and `UserService`, you write explicit configuration.

If the project grows, configuration also grows, and wiring dependencies manually becomes repetitive.

Example using XML configuration:

```xml
<!-- applicationContext.xml -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="url" value="jdbc:mysql://localhost:3306/appdb"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>

    <bean id="userRepository" class="com.example.repo.UserRepository">
        <constructor-arg ref="dataSource"/>
    </bean>

    <bean id="userService" class="com.example.service.UserService">
        <constructor-arg ref="userRepository"/>
    </bean>
</beans>
```

Example using Java configuration:

```java
import javax.sql.DataSource;
import org.apache.commons.dbcp2.BasicDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        BasicDataSource ds = new BasicDataSource();
        ds.setUrl("jdbc:mysql://localhost:3306/appdb");
        ds.setUsername("root");
        ds.setPassword("root");
        return ds;
    }

    @Bean
    public UserRepository userRepository(DataSource dataSource) {
        return new UserRepository(dataSource);
    }

    @Bean
    public UserService userService(UserRepository userRepository) {
        return new UserService(userRepository);
    }
}
```

Both approaches work, but for large projects this manual wiring is one reason Spring Boot became popular.

Spring Boot reduces this with:
- auto-configuration
- starter dependencies
- embedded server
- production-ready features (`actuator`, metrics, health checks)

### 3.3 Main Features
- `@SpringBootApplication`
- Auto Configuration
- Starter POMs (`spring-boot-starter-web`, etc.)
- Embedded Tomcat (run as a standalone jar)
- Externalized config (`application.properties` / `application.yml`)
- Actuator endpoints

### 3.4 Minimal Spring Boot Application

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}

@RestController
class HomeController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello from Spring Boot";
    }
}
```

Run this class and access `http://localhost:8080/hello`.

### 3.5 Auto-Configuration vs XML Configuration

This is the key difference many developers ask about: **"Spring MVC follows XML-based configuration and Spring Boot follows automatic configuration."**

Let's break down what this means with a clear comparison.

#### 3.5.1 What Spring MVC XML Required

Traditional Spring MVC needed:
1. **web.xml** - to register and configure `DispatcherServlet`
2. **spring-mvc-config.xml** - to configure Spring MVC components, view resolvers, component scanning
3. **applicationContext.xml** - to define application beans
4. Manual deployment to external Tomcat/Jetty server

Example setup required multiple files with 50+ lines of XML just for basic configuration.

#### 3.5.2 What Spring Boot Auto-Configuration Does

Spring Boot **eliminates all XML** and **automatically configures everything**:

**Same functionality with Spring Boot (zero XML):**

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication  // This one annotation does magic
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}

@RestController
class GreetingController {

    @GetMapping("/greet/{name}")
    public String greet(@PathVariable String name) {
        return "Hello, " + name + "!";
    }
}
```

**Project structure with Spring Boot:**
```
src/
  main/
    java/
      com/myapp/
        MyApp.java
        GreetingController.java
    resources/
      application.properties  (optional config)
```

No `web.xml`, no `spring-mvc-config.xml`, no WAR deployment needed.

#### 3.5.3 How @SpringBootApplication Works

The `@SpringBootApplication` annotation is a combination of three annotations:

```java
@SpringBootConfiguration  // Marks this as config class
@EnableAutoConfiguration  // Enables auto-configuration magic
@ComponentScan            // Scans for components in this package and below
```

**What `@EnableAutoConfiguration` does automatically:**
1. Detects `spring-webmvc` in classpath → auto-configures `DispatcherServlet`
2. Detects `spring-boot-starter-web` → starts embedded Tomcat server
3. Configures default view resolver, message converters, exception handlers
4. Sets up component scanning for `@Controller`, `@Service`, `@Repository`
5. Configures static resource handling (`/static`, `/public` folders)
6. Sets up default error handling (`/error`)

All of this happens **without you writing a single line of XML or explicit Java configuration**.

#### 3.5.4 Key Differences Table

| Aspect | Traditional Spring MVC | Spring Boot |
|--------|------------------------|-------------|
| Configuration | XML files (web.xml + spring-mvc-config.xml) | `@SpringBootApplication` annotation |
| DispatcherServlet setup | Manual in web.xml | Auto-configured |
| Component scanning | Explicit in XML | Auto-enabled by `@SpringBootApplication` |
| View resolver | Manual bean definition | Auto-configured with sensible defaults |
| Server | External Tomcat/Jetty (deploy WAR) | Embedded Tomcat (run JAR) |
| Dependencies | Manual version management | Starter POMs with managed versions |
| Configuration lines | 50-100+ lines of XML | 1 annotation + main method |

#### 3.5.5 Why Developers Prefer Spring Boot

1. **No XML boilerplate**: Zero XML configuration needed
2. **Convention over configuration**: Sensible defaults work for 90% of use cases
3. **Fast setup**: New project ready in minutes
4. **Embedded server**: Run as standalone Java application
5. **Override when needed**: You can still customize via `application.properties` or Java config

**Example: Override default server port in Spring Boot**

Instead of XML configuration, just add one line to `application.properties`:

```properties
server.port=9090
```

That's it! Spring Boot auto-configures everything else.

#### 3.5.6 Is XML Completely Eliminated?

Modern Spring Boot projects typically use **zero XML**. However:
- You can still use XML if needed (for legacy integration)
- Most projects use annotations + `application.properties/yml`
- Java-based `@Configuration` classes are preferred over XML when custom config is needed

**Bottom line:**
- Spring MVC (traditional) = XML-heavy manual configuration
- Spring Boot = Auto-configuration with zero or minimal XML

---

## 4. Relationship Between Spring, Spring MVC, and Spring Boot

### 4.1 Simple Hierarchy
- Spring: Core framework (IoC, DI, AOP, transactions, data access, etc.)
- Spring MVC: Web module inside Spring
- Spring Boot: Tooling + convention layer that simplifies building Spring apps (including Spring MVC apps)

### 4.2 Easy Analogy
- Java = language
- Spring = toolkit for enterprise architecture
- Spring MVC = web toolkit inside Spring
- Spring Boot = fast setup + sensible defaults to use Spring/Spring MVC quickly

### 4.3 In One Line
Most modern Java backend apps are built as:
- Java language + Spring framework + Spring MVC (for API/web) + Spring Boot (for fast setup and production readiness)

---

## 5. Code Comparison: Plain Java vs Spring MVC vs Spring Boot

### 5.1 Plain Java (No Spring)

```java
class GreetingService {
    public String greet(String name) {
        return "Hello, " + name;
    }
}

public class PlainJavaApp {
    public static void main(String[] args) {
        GreetingService service = new GreetingService();
        System.out.println(service.greet("Saurav"));
    }
}
```

This works, but there is no HTTP endpoint, no DI container, and no web framework support.

### 5.2 Spring (Core + MVC)

```java
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@Service
class GreetingService {
    public String greet(String name) {
        return "Hello, " + name;
    }
}

@RestController
class GreetingController {
    private final GreetingService greetingService;

    public GreetingController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }

    @GetMapping("/greet")
    public String greet(@RequestParam String name) {
        return greetingService.greet(name);
    }
}
```

Here you get proper layering and dependency injection.

### 5.3 Spring Boot (Modern Approach)

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class GreetingApplication {
    public static void main(String[] args) {
        SpringApplication.run(GreetingApplication.class, args);
    }
}

@Service
class GreetingService {
    public String greet(String name) {
        return "Hello, " + name;
    }
}

@RestController
class GreetingController {
    private final GreetingService greetingService;

    public GreetingController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }

    @GetMapping("/greet")
    public String greet(@RequestParam String name) {
        return greetingService.greet(name);
    }
}
```

Now you can run a production-style app with minimal configuration.

---

## 6. Quick Summary

- Spring is a powerful framework for enterprise Java development.
- Spring MVC is the web layer module of Spring.
- Spring Boot makes Spring application setup fast and production-ready.
- They are complementary, not competitors.
- Java is the language foundation; Spring ecosystem provides productivity and architecture for real-world applications.
