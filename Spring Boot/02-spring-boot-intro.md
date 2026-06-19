# 🚀 Introduction to Spring Boot - MUST KNOW

**Priority:** 🔴 CRITICAL | **Interview Weight:** Very High

---

## 📑 Table of Contents

1. [What is Spring Boot?](#21-what-is-spring-boot)
   - [Definition](#-definition)
   - [Problems Solved by Spring Boot](#-problems-solved-by-spring-boot)
   - [Spring vs Spring Boot](#-spring-vs-spring-boot)
   - [Key Features](#-key-features-of-spring-boot)
2. [Spring Boot Architecture](#22-spring-boot-architecture)
   - [Layered Architecture](#-layered-architecture)
   - [@SpringBootApplication Annotation](#-springbootapplication-annotation)
   - [Starter POMs](#-starter-poms)
   - [Auto-Configuration Deep Dive](#-auto-configuration-deep-dive)
   - [Embedded Servers](#-embedded-servers)
3. [Project Setup](#23-project-setup)
   - [Spring Initializr](#-spring-initializr)
   - [Maven/Gradle Setup](#-mavengradle-setup)
   - [Project Structure](#-project-structure)
4. [Common Interview Questions](#-common-interview-questions)
5. [Quick Revision Checklist](#-quick-revision-checklist)
6. [Practice Exercise](#-practice-exercise)

---

## 2.1 What is Spring Boot?

### 🔹 Definition
Spring Boot is an opinionated framework built on top of Spring Framework that simplifies the development of production-ready Spring applications with minimal configuration.

### 🔹 Problems Solved by Spring Boot

**Before Spring Boot (Traditional Spring):**
```xml
<!-- Massive XML configuration -->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="password"/>
</bean>

<!-- Manual transaction manager -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!-- DispatcherServlet configuration in web.xml -->
<!-- And many more configurations... -->
```

**With Spring Boot:**
```properties
# application.properties - That's it!
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
```

**Key Problems Solved:**
1. ❌ Complex XML configurations → ✅ Auto-configuration
2. ❌ Dependency hell → ✅ Starter dependencies
3. ❌ Manual server setup → ✅ Embedded servers
4. ❌ Lengthy setup time → ✅ Quick start with Spring Initializr
5. ❌ Production deployment complexity → ✅ Production-ready features (Actuator)

---

### 🔹 Spring vs Spring Boot

| Feature | Spring Framework | Spring Boot |
|---------|-----------------|-------------|
| **Configuration** | Manual XML/Java config | Auto-configuration |
| **Dependency Management** | Manual version management | Starter POMs with compatible versions |
| **Server Setup** | Deploy WAR to external server | Embedded server (Tomcat/Jetty/Undertow) |
| **Setup Time** | Hours/Days | Minutes |
| **Boilerplate Code** | High | Minimal |
| **Production Features** | Manual setup | Built-in (Actuator, metrics) |
| **Deployment** | WAR files | Executable JAR |
| **Learning Curve** | Steep | Moderate |

**Interview Tip:** Spring Boot is NOT a replacement for Spring - it's built ON TOP of Spring to make it easier!

---

### 🔹 Key Features of Spring Boot

#### 1. **Auto-Configuration**
Spring Boot automatically configures application based on dependencies in classpath.

```java
// Just add this dependency in pom.xml:
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

// Spring Boot auto-configures:
// ✅ EntityManagerFactory
// ✅ DataSource
// ✅ TransactionManager
// ✅ JPA repositories
// No manual configuration needed!
```

#### 2. **Starter Dependencies**
Pre-configured dependency bundles for common use cases.

```xml
<!-- Web application -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- This single dependency includes:
    - Spring MVC
    - Tomcat (embedded)
    - Jackson (JSON)
    - Validation
    - And all compatible versions!
-->
```

#### 3. **Embedded Servers**
No need to deploy WAR to external Tomcat/Jetty.

```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
        // Tomcat starts automatically on port 8080!
    }
}
```

#### 4. **Production-Ready Features**
Built-in monitoring, health checks, metrics via Spring Boot Actuator.

---

## 2.2 Spring Boot Architecture

### 🔹 Layered Architecture

```
┌─────────────────────────────────────┐
│     @SpringBootApplication          │ ← Entry Point
├─────────────────────────────────────┤
│     @RestController / @Controller   │ ← Presentation Layer
├─────────────────────────────────────┤
│     @Service                        │ ← Business Logic Layer
├─────────────────────────────────────┤
│     @Repository                     │ ← Data Access Layer
├─────────────────────────────────────┤
│     Database / External Services    │ ← Data Layer
└─────────────────────────────────────┘
```

### 🔹 @SpringBootApplication Annotation

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

**What does @SpringBootApplication do?**

```java
@SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan

// 1. @Configuration - Marks class as configuration source
// 2. @EnableAutoConfiguration - Enables Spring Boot auto-configuration
// 3. @ComponentScan - Scans for components in current package and sub-packages
```

**Detailed Breakdown:**

```java
// Equivalent to:
@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.example")
public class MyApplication {
    // ...
}
```

---

### 🔹 Starter POMs

**Common Starters:**

```xml
<!-- 1. Web applications (REST APIs) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- 2. JPA/Hibernate -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- 3. Security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<!-- 4. Testing -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

<!-- 5. Validation -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>

<!-- 6. Actuator (Production monitoring) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**Benefits:**
- ✅ Version compatibility guaranteed
- ✅ No need to manage individual library versions
- ✅ Reduces dependency conflicts
- ✅ Include all necessary transitive dependencies

---

### 🔹 Auto-Configuration Deep Dive

**How Auto-Configuration Works:**

1. Spring Boot scans classpath for libraries
2. Based on found libraries, applies configurations
3. Uses `@Conditional` annotations to decide what to configure

```java
// Example: DataSource Auto-Configuration
@Configuration
@ConditionalOnClass(DataSource.class)  // Only if DataSource in classpath
@ConditionalOnMissingBean(DataSource.class)  // Only if user hasn't defined one
public class DataSourceAutoConfiguration {
    
    @Bean
    public DataSource dataSource() {
        // Auto-configure DataSource using application.properties
        return new HikariDataSource();
    }
}
```

**Viewing Auto-Configuration Report:**
```properties
# In application.properties
debug=true

# Run app and check console for auto-configuration report
# Shows:
# ✅ Positive matches (what was configured)
# ❌ Negative matches (what was skipped)
```

**Excluding Auto-Configuration:**
```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class MyApp {
    // DataSource won't be auto-configured
}
```

---

### 🔹 Embedded Servers

**Default: Tomcat**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- Includes Tomcat by default -->
</dependency>
```

**Switch to Jetty:**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

**Server Configuration:**

```properties
# Server port
server.port=8081

# Context path
server.servlet.context-path=/api

# Session timeout
server.servlet.session.timeout=30m

# Max thread pool
server.tomcat.threads.max=200
```

---

## 2.3 Project Setup

### 🔹 Spring Initializr

**Web Interface:** https://start.spring.io/

**Options:**
1. **Project:** Maven or Gradle
2. **Language:** Java, Kotlin, Groovy
3. **Spring Boot Version:** (Choose stable version)
4. **Project Metadata:**
   - Group: com.example
   - Artifact: my-app
   - Package name: com.example.myapp
   - Packaging: JAR (executable) or WAR
   - Java Version: 17, 21

**IntelliJ IDEA / VS Code:**
- Both support creating Spring Boot projects via Spring Initializr integration

---

### 🔹 Maven/Gradle Setup

**Maven (pom.xml):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <!-- Parent POM - provides dependency management -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>
    
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    
    <properties>
        <java.version>17</java.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <!-- Spring Boot Maven Plugin - Creates executable JAR -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

**Gradle (build.gradle):**

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.3'
}

group = 'com.example'
version = '1.0.0'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}
```

---

### 🔹 Project Structure

```
my-app/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/myapp/
│   │   │       ├── MyAppApplication.java    ← Main class
│   │   │       ├── controller/              ← REST Controllers
│   │   │       │   └── UserController.java
│   │   │       ├── service/                 ← Business logic
│   │   │       │   └── UserService.java
│   │   │       ├── repository/              ← Data access
│   │   │       │   └── UserRepository.java
│   │   │       ├── model/entity/            ← JPA entities
│   │   │       │   └── User.java
│   │   │       ├── dto/                     ← Data Transfer Objects
│   │   │       │   └── UserDTO.java
│   │   │       ├── config/                  ← Configuration classes
│   │   │       │   └── SecurityConfig.java
│   │   │       └── exception/               ← Custom exceptions
│   │   │           └── GlobalExceptionHandler.java
│   │   └── resources/
│   │       ├── application.properties       ← Main config
│   │       ├── application-dev.properties   ← Dev profile
│   │       ├── application-prod.properties  ← Prod profile
│   │       ├── static/                      ← Static files (CSS, JS)
│   │       └── templates/                   ← Thymeleaf templates
│   └── test/
│       └── java/
│           └── com/example/myapp/
│               ├── controller/
│               │   └── UserControllerTest.java
│               └── service/
│                   └── UserServiceTest.java
├── target/                                  ← Compiled files (Maven)
├── pom.xml                                  ← Maven config
└── README.md
```

---

## 🔥 Common Interview Questions

### Q1: What is Spring Boot and why use it?
**A:** Spring Boot is an opinionated framework on top of Spring that provides auto-configuration, starter dependencies, and embedded servers to quickly create production-ready applications with minimal configuration.

### Q2: What does @SpringBootApplication do?
**A:** It's a combination of three annotations:
- `@Configuration` - Marks as config class
- `@EnableAutoConfiguration` - Enables auto-configuration
- `@ComponentScan` - Scans for components in package

### Q3: How does Spring Boot auto-configuration work?
**A:** Spring Boot:
1. Scans classpath for libraries
2. Uses `@Conditional` annotations to check conditions
3. Applies configurations automatically based on classpath content
4. Can be overridden by user-defined beans

### Q4: What are starter dependencies?
**A:** Pre-configured bundles of dependencies for specific use cases (e.g., spring-boot-starter-web includes Spring MVC, Tomcat, Jackson). They ensure version compatibility and reduce configuration.

### Q5: Difference between JAR and WAR packaging?
**A:**
- **JAR** - Executable JAR with embedded server (modern approach)
- **WAR** - Deployed to external servlet container (traditional)

### Q6: How to change default port 8080?
**A:** In application.properties: `server.port=9090`

### Q7: How to disable specific auto-configuration?
**A:** `@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})`

### Q8: What is Spring Boot Actuator?
**A:** Production-ready features for monitoring and managing applications (health checks, metrics, info endpoints).

---

## 📝 Quick Revision Checklist

- [ ] Understand problems Spring Boot solves
- [ ] Know @SpringBootApplication components
- [ ] Understand auto-configuration mechanism
- [ ] Familiar with common starter dependencies
- [ ] Know embedded server options (Tomcat, Jetty, Undertow)
- [ ] Understand project structure best practices
- [ ] Can create project using Spring Initializr
- [ ] Know Maven/Gradle configuration basics
- [ ] Understand JAR vs WAR packaging

---

## 💻 Practice Exercise

Create a Spring Boot application that:
1. Uses Spring Initializr to generate project
2. Includes starter-web and starter-data-jpa
3. Has proper package structure (controller, service, repository)
4. Configures custom port and context path
5. Displays auto-configuration report (debug=true)
6. Creates executable JAR and runs it

**This is your entry point - master this before moving forward!**
