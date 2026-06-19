# ⚙️ Spring Boot Configuration - MUST KNOW

**Priority:** 🔴 CRITICAL | **Interview Weight:** High

---

## 📑 Table of Contents

1. [Configuration Files](#31-configuration-files)
   - [application.properties vs application.yml](#-applicationproperties-vs-applicationyml)
   - [Profile-Specific Configuration](#-profile-specific-configuration)
2. [Environment & Profiles](#32-environment--profiles)
   - [@Profile Annotation](#-profile-annotation)
   - [spring.profiles.active](#-springprofilesactive)
   - [External Configuration Sources](#-external-configuration-sources)
3. [Custom Configuration](#33-custom-configuration)
   - [@Value Annotation](#-value-annotation)
   - [@ConfigurationProperties](#-configurationproperties-type-safe-config)
4. [Logging](#34-logging)
   - [Logback Configuration](#-logback-configuration-default)
5. [Common Interview Questions](#-common-interview-questions)
6. [Quick Revision Checklist](#-quick-revision-checklist)
7. [Practice Exercise](#-practice-exercise)

---

## 3.1 Configuration Files

### 🔹 application.properties vs application.yml

**application.properties:**
```properties
# Server configuration
server.port=8080
server.servlet.context-path=/api

# Database configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# Logging
logging.level.root=INFO
logging.level.com.example=DEBUG
logging.file.name=app.log
```

**application.yml (Same configuration):**
```yaml
server:
  port: 8080
  servlet:
    context-path: /api

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
    driver-class-name: com.mysql.cj.jdbc.Driver
  
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQLDialect

logging:
  level:
    root: INFO
    com.example: DEBUG
  file:
    name: app.log
```

**Comparison:**

| Feature | .properties | .yml |
|---------|------------|------|
| **Readability** | Moderate | High (hierarchical) |
| **Compactness** | Verbose | Concise |
| **Hierarchy** | Flat (dots) | Tree structure |
| **Lists/Arrays** | Limited | Easy |
| **Common Usage** | Traditional | Modern (preferred) |

**Interview Tip:** YAML is preferred for readability but .properties is still widely used. Know both!

---

### 🔹 Profile-Specific Configuration

**File Naming Convention:**
```
application.properties           → Default (all profiles)
application-dev.properties      → Development profile
application-test.properties     → Test profile
application-prod.properties     → Production profile
```

**Example Files:**

**application.properties (Common config):**
```properties
# Common to all environments
app.name=MyApplication
app.version=1.0.0
```

**application-dev.properties:**
```properties
# Development environment
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/mydb_dev
spring.jpa.show-sql=true
logging.level.com.example=DEBUG
```

**application-prod.properties:**
```properties
# Production environment
server.port=80
spring.datasource.url=jdbc:mysql://prod-server:3306/mydb_prod
spring.jpa.show-sql=false
logging.level.com.example=WARN
```

**Activating Profiles:**

```properties
# In application.properties
spring.profiles.active=dev

# OR via command line
java -jar myapp.jar --spring.profiles.active=prod

# OR via environment variable
export SPRING_PROFILES_ACTIVE=prod

# OR in IDE (IntelliJ / VS Code)
# Run Configurations → Environment Variables → SPRING_PROFILES_ACTIVE=dev
```

---

## 3.2 Environment & Profiles

### 🔹 @Profile Annotation

```java
@Configuration
public class DatabaseConfig {
    
    // Only active in 'dev' profile
    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:h2:mem:testdb");  // In-memory DB for dev
        return ds;
    }
    
    // Only active in 'prod' profile
    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://prod-server:3306/mydb");
        ds.setUsername("prod_user");
        ds.setPassword("prod_password");
        ds.setMaximumPoolSize(50);
        return ds;
    }
    
    // Active in multiple profiles
    @Bean
    @Profile({"dev", "test"})
    public DataSource nonProdDataSource() {
        return new HikariDataSource();
    }
    
    // Active when 'prod' is NOT active
    @Bean
    @Profile("!prod")
    public MockEmailService mockEmailService() {
        return new MockEmailService();  // Don't send real emails in dev/test
    }
}
```

**Component-Level Profiles:**

```java
@Service
@Profile("dev")
public class MockPaymentService implements PaymentService {
    @Override
    public boolean processPayment(double amount) {
        System.out.println("MOCK: Payment processed: $" + amount);
        return true;  // Always succeeds in dev
    }
}

@Service
@Profile("prod")
public class RealPaymentService implements PaymentService {
    @Override
    public boolean processPayment(double amount) {
        // Real payment gateway integration
        return paymentGateway.charge(amount);
    }
}
```

---

### 🔹 spring.profiles.active

**Multiple Active Profiles:**

```properties
# Activate multiple profiles
spring.profiles.active=dev,swagger,debug

# Profile-specific properties will be merged
```

**Profile Priority (Higher overrides lower):**
1. Command-line arguments: `--spring.profiles.active=prod`
2. System properties: `-Dspring.profiles.active=prod`
3. Environment variables: `SPRING_PROFILES_ACTIVE=prod`
4. application.properties: `spring.profiles.active=prod`

---

### 🔹 External Configuration Sources

**Configuration Priority (Highest to Lowest):**

1. **Command-line arguments**
   ```bash
   java -jar app.jar --server.port=9090 --spring.datasource.username=admin
   ```

2. **Java System Properties**
   ```bash
   java -Dserver.port=9090 -jar app.jar
   ```

3. **OS Environment Variables**
   ```bash
   export SERVER_PORT=9090
   export SPRING_DATASOURCE_USERNAME=admin
   ```

4. **application-{profile}.properties** outside JAR
   ```
   /config/application-prod.properties  (next to JAR)
   ```

5. **application-{profile}.properties** inside JAR

6. **application.properties** outside JAR

7. **application.properties** inside JAR

8. **@PropertySource** on @Configuration classes

9. **Default properties** (SpringApplication.setDefaultProperties)

**External Config File Location:**

```bash
# Spring Boot searches in this order:
1. /config/application.properties  (in current directory)
2. /application.properties         (in current directory)
3. classpath:/config/application.properties
4. classpath:/application.properties
```

**Custom Config Location:**

```bash
java -jar app.jar --spring.config.location=file:/path/to/config/

# Or multiple locations (comma-separated)
java -jar app.jar --spring.config.location=classpath:/,file:/etc/config/
```

---

## 3.3 Custom Configuration

### 🔹 @Value Annotation

```java
@Service
public class AppService {
    
    // Simple property
    @Value("${app.name}")
    private String appName;
    
    // With default value
    @Value("${app.timeout:30}")
    private int timeout;
    
    // System property
    @Value("${user.home}")
    private String userHome;
    
    // SpEL expression
    @Value("#{${app.max-users} * 2}")
    private int doubleMaxUsers;
    
    // Array/List from comma-separated
    @Value("${app.allowed-origins}")
    private String[] allowedOrigins;  // app.allowed-origins=http://localhost:3000,http://example.com
    
    // Constructor injection (recommended)
    public AppService(@Value("${app.name}") String appName) {
        this.appName = appName;
    }
}
```

**application.properties:**
```properties
app.name=MyApp
app.timeout=60
app.max-users=100
app.allowed-origins=http://localhost:3000,http://example.com
```

---

### 🔹 @ConfigurationProperties (Type-Safe Config)

**Preferred over @Value for complex configurations!**

**application.yml:**
```yaml
app:
  name: MyApplication
  version: 1.0.0
  server:
    host: localhost
    port: 8080
    timeout: 30
  features:
    - feature1
    - feature2
    - feature3
  database:
    username: admin
    password: secret
    max-connections: 50
```

**Configuration Class:**

```java
@Configuration
@ConfigurationProperties(prefix = "app")
@Validated  // Enable validation
public class AppProperties {
    
    @NotBlank
    private String name;
    
    private String version;
    
    private ServerConfig server;
    
    private List<String> features;
    
    private DatabaseConfig database;
    
    // Getters and Setters (REQUIRED!)
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getVersion() { return version; }
    public void setVersion(String version) { this.version = version; }
    
    public ServerConfig getServer() { return server; }
    public void setServer(ServerConfig server) { this.server = server; }
    
    public List<String> getFeatures() { return features; }
    public void setFeatures(List<String> features) { this.features = features; }
    
    public DatabaseConfig getDatabase() { return database; }
    public void setDatabase(DatabaseConfig database) { this.database = database; }
    
    // Nested configuration classes
    public static class ServerConfig {
        private String host;
        private int port;
        private int timeout;
        
        // Getters and Setters
        public String getHost() { return host; }
        public void setHost(String host) { this.host = host; }
        
        public int getPort() { return port; }
        public void setPort(int port) { this.port = port; }
        
        public int getTimeout() { return timeout; }
        public void setTimeout(int timeout) { this.timeout = timeout; }
    }
    
    public static class DatabaseConfig {
        @NotBlank
        private String username;
        
        @NotBlank
        private String password;
        
        @Min(1)
        @Max(100)
        private int maxConnections;
        
        // Getters and Setters
        public String getUsername() { return username; }
        public void setUsername(String username) { this.username = username; }
        
        public String getPassword() { return password; }
        public void setPassword(String password) { this.password = password; }
        
        public int getMaxConnections() { return maxConnections; }
        public void setMaxConnections(int maxConnections) { this.maxConnections = maxConnections; }
    }
}
```

**Using Configuration Properties:**

```java
@Service
public class MyService {
    
    private final AppProperties appProperties;
    
    public MyService(AppProperties appProperties) {
        this.appProperties = appProperties;
    }
    
    public void printConfig() {
        System.out.println("App Name: " + appProperties.getName());
        System.out.println("Server Host: " + appProperties.getServer().getHost());
        System.out.println("Server Port: " + appProperties.getServer().getPort());
        System.out.println("Features: " + appProperties.getFeatures());
    }
}
```

**Enable @ConfigurationProperties:**

```java
@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

**Benefits of @ConfigurationProperties over @Value:**
- ✅ Type-safe
- ✅ Supports validation (@Validated)
- ✅ Hierarchical/nested properties
- ✅ Better IDE support (auto-completion)
- ✅ Easier to test
- ✅ Relaxed binding (app.my-property = app.myProperty = APP_MY_PROPERTY)

---

## 3.4 Logging

### 🔹 Logback Configuration (Default)

Spring Boot uses **Logback** by default.

**Logging Levels:**
- **TRACE** - Very detailed (method entry/exit)
- **DEBUG** - Debugging information
- **INFO** - Important events (default)
- **WARN** - Warning messages
- **ERROR** - Error messages
- **FATAL** - Critical errors

**application.properties:**

```properties
# Root level (all packages)
logging.level.root=INFO

# Package-specific levels
logging.level.com.example=DEBUG
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate.SQL=DEBUG

# Log file
logging.file.name=app.log
logging.file.path=/var/logs

# Log pattern
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss} - %msg%n
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n

# Max file size and retention
logging.logback.rollingpolicy.max-file-size=10MB
logging.logback.rollingpolicy.max-history=30
```

**In Code:**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class UserService {
    
    // Create logger
    private static final Logger log = LoggerFactory.getLogger(UserService.class);
    
    // Or use Lombok @Slf4j
    // @Slf4j  → Automatically creates 'log' field
    
    public User createUser(User user) {
        log.trace("Entering createUser method");
        log.debug("Creating user with email: {}", user.getEmail());
        
        try {
            User saved = userRepository.save(user);
            log.info("User created successfully: {}", saved.getId());
            return saved;
        } catch (Exception e) {
            log.error("Failed to create user: {}", user.getEmail(), e);
            throw e;
        }
    }
}
```

**Custom logback-spring.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- Console Appender -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- File Appender -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/app.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/app-%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- Root Logger -->
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
    </root>
    
    <!-- Package-specific loggers -->
    <logger name="com.example" level="DEBUG" />
    <logger name="org.springframework.web" level="DEBUG" />
</configuration>
```

---

## 🔥 Common Interview Questions

### Q1: What is the difference between application.properties and application.yml?
**A:** Both configure Spring Boot, but YAML is hierarchical/readable while .properties is flat. YAML is preferred for complex configs with nested properties.

### Q2: How do you activate a specific profile?
**A:** 
- In application.properties: `spring.profiles.active=dev`
- Command-line: `--spring.profiles.active=prod`
- Environment variable: `SPRING_PROFILES_ACTIVE=prod`

### Q3: What is @ConfigurationProperties and when to use it?
**A:** Type-safe alternative to @Value for binding external properties to Java objects. Use for:
- Multiple related properties
- Hierarchical configuration
- When validation is needed
- Better maintainability

### Q4: Order of configuration precedence in Spring Boot?
**A:** (Highest to lowest)
1. Command-line args
2. System properties
3. Environment variables
4. External config files
5. Internal config files
6. @PropertySource
7. Default properties

### Q5: How to exclude certain profiles?
**A:** Use `@Profile("!prod")` - this bean will be active in all profiles EXCEPT prod.

### Q6: What is relaxed binding in @ConfigurationProperties?
**A:** Spring Boot accepts multiple formats for same property:
- `app.my-property`
- `app.myProperty`
- `APP_MY_PROPERTY`
All map to `myProperty` field.

### Q7: How to externalize configuration in production?
**A:** 
- Place application-prod.properties in `/config/` directory next to JAR
- Use environment variables
- Use config servers (Spring Cloud Config)
- Mount configuration from volume/secret in Kubernetes

### Q8: Default logging framework in Spring Boot?
**A:** Logback (via SLF4J facade). Can switch to Log4j2 if needed.

---

## 📝 Quick Revision Checklist

- [ ] Know .properties vs .yml syntax
- [ ] Understand profile-specific configuration
- [ ] Master @Profile annotation usage
- [ ] Know configuration precedence order
- [ ] Understand @Value vs @ConfigurationProperties
- [ ] Can use @ConfigurationProperties with validation
- [ ] Know how to configure logging levels
- [ ] Understand external configuration strategies
- [ ] Know relaxed binding rules
- [ ] Can activate/deactivate profiles

---

## 💻 Practice Exercise

Create a Spring Boot app with:
1. Multiple profiles (dev, test, prod) with different DB configs
2. @ConfigurationProperties class for app settings
3. Custom logging configuration with file rotation
4. Externalized secrets (passwords) via environment variables
5. Profile-specific beans for email service (mock in dev, real in prod)
6. Command-line activation of profiles

**Configuration management is tested in 80% of interviews!**
