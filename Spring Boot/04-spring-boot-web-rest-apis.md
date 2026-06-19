# 🌐 Spring Boot Web (REST APIs) - MUST KNOW

**Priority:** 🔴 CRITICAL ⭐ HIGH INTERVIEW FOCUS | **Interview Weight:** EXTREMELY HIGH

---

## 📑 Table of Contents

1. [Building REST APIs](#41-building-rest-apis)
   - [@RestController](#-restcontroller)
   - [HTTP Mapping Annotations](#-http-mapping-annotations)
   - [Path Variables & Query Parameters](#-path-variables--query-parameters)
   - [ResponseEntity](#-responseentity-better-control)
2. [Request/Response Handling](#42-requestresponse-handling)
   - [@RequestBody & @ResponseBody](#-requestbody--responsebody)
   - [DTO Design](#-dto-design-critical)
3. [Exception Handling](#43-exception-handling)
   - [@ExceptionHandler](#-exceptionhandler-controller-level)
   - [@ControllerAdvice](#-controlleradvice-global-exception-handling--best-practice)
4. [Validation](#44-validation)
   - [Bean Validation (JSR-380)](#-bean-validation-jsr-380)
   - [Custom Validation](#-custom-validation)
5. [Common Interview Questions](#-common-interview-questions)
6. [Quick Revision Checklist](#-quick-revision-checklist)
7. [Practice Exercise](#-practice-exercise)

---

## 4.1 Building REST APIs

### 🔹 @RestController

**Definition:** `@RestController = @Controller + @ResponseBody`

Automatically serializes return values to JSON/XML (using Jackson).

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    private final UserService userService;
    
    // Constructor injection (recommended)
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    // Methods defined below...
}
```

---

### 🔹 HTTP Mapping Annotations

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // GET /api/users - Get all users
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    // GET /api/users/123 - Get user by ID
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    // POST /api/users - Create new user
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.create(user);
    }
    
    // PUT /api/users/123 - Update user
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }
    
    // PATCH /api/users/123 - Partial update
    @PatchMapping("/{id}")
    public User partialUpdate(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
        return userService.partialUpdate(id, updates);
    }
    
    // DELETE /api/users/123 - Delete user
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
}
```

**HTTP Methods:**
- **GET** - Retrieve resources (idempotent, safe)
- **POST** - Create new resource (not idempotent)
- **PUT** - Update entire resource (idempotent)
- **PATCH** - Partial update (idempotent)
- **DELETE** - Remove resource (idempotent)

---

### 🔹 Path Variables & Query Parameters

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // Path Variable: /api/users/123
    @GetMapping("/{id}")
    public User getById(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    // Multiple Path Variables: /api/users/123/orders/456
    @GetMapping("/{userId}/orders/{orderId}")
    public Order getUserOrder(
        @PathVariable Long userId,
        @PathVariable Long orderId
    ) {
        return orderService.findByUserAndOrder(userId, orderId);
    }
    
    // Query Parameters: /api/users?name=John&age=25
    @GetMapping
    public List<User> searchUsers(
        @RequestParam String name,
        @RequestParam int age
    ) {
        return userService.searchByNameAndAge(name, age);
    }
    
    // Optional Query Parameter with default
    @GetMapping
    public List<User> getAllUsers(
        @RequestParam(required = false, defaultValue = "0") int page,
        @RequestParam(required = false, defaultValue = "10") int size
    ) {
        return userService.findAll(page, size);
    }
    
    // Multiple values: /api/users?ids=1,2,3 or ?ids=1&ids=2&ids=3
    @GetMapping
    public List<User> getUsersByIds(@RequestParam List<Long> ids) {
        return userService.findByIds(ids);
    }
}
```

**Interview Tip:** @PathVariable for resource identification, @RequestParam for filtering/pagination!

---

### 🔹 ResponseEntity (Better Control)

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // Return with custom status code
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.create(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
        // Returns 201 Created instead of default 200 OK
    }
    
    // Return with headers
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.findById(id);
        
        HttpHeaders headers = new HttpHeaders();
        headers.add("X-Custom-Header", "CustomValue");
        
        return ResponseEntity.ok()
                .headers(headers)
                .body(user);
    }
    
    // Return 404 if not found
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return userService.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }
    
    // Return 204 No Content for DELETE
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();  // 204
    }
    
    // Return Location header after POST
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.create(user);
        
        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(created.getId())
                .toUri();
        
        return ResponseEntity.created(location).body(created);
        // Returns: 201 Created + Location: /api/users/123
    }
}
```

**Common Status Codes:**
- **200 OK** - Success
- **201 Created** - Resource created
- **204 No Content** - Success but no response body
- **400 Bad Request** - Validation error
- **401 Unauthorized** - Not authenticated
- **403 Forbidden** - Not authorized
- **404 Not Found** - Resource doesn't exist
- **500 Internal Server Error** - Server error

---

## 4.2 Request/Response Handling

### 🔹 @RequestBody & @ResponseBody

```java
// @RequestBody - Deserializes JSON to Java object
@PostMapping
public User createUser(@RequestBody User user) {
    // Spring automatically converts JSON to User object
    return userService.create(user);
}

// @ResponseBody - Serializes Java object to JSON
// (Not needed if using @RestController)
@Controller  // Not @RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping
    @ResponseBody  // Needed because @Controller doesn't have it
    public List<User> getAllUsers() {
        return userService.findAll();
    }
}
```

---

### 🔹 DTO Design (CRITICAL!)

**Why DTOs?**
- ✅ Decouples API from database entities
- ✅ Security - hide sensitive fields (passwords, internal IDs)
- ✅ Flexibility - different API versions without changing entities
- ✅ Performance - fetch only required data
- ✅ Validation - API-specific validation rules

**Entity:**
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String username;
    private String email;
    private String password;  // NEVER expose in API!
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    
    @OneToMany(mappedBy = "user")
    private List<Order> orders;  // Can cause N+1 issues if exposed
    
    // Getters and Setters
}
```

**Request DTO:**
```java
public class CreateUserRequest {
    @NotBlank(message = "Username is required")
    @Size(min = 3, max = 20)
    private String username;
    
    @NotBlank(message = "Email is required")
    @Email
    private String email;
    
    @NotBlank(message = "Password is required")
    @Size(min = 8)
    private String password;
    
    // Getters and Setters
}
```

**Response DTO:**
```java
public class UserResponse {
    private Long id;
    private String username;
    private String email;
    // NO PASSWORD!
    private LocalDateTime createdAt;
    
    // Constructor for easy mapping
    public UserResponse(User user) {
        this.id = user.getId();
        this.username = user.getUsername();
        this.email = user.getEmail();
        this.createdAt = user.getCreatedAt();
    }
    
    // Getters and Setters
}
```

**Controller with DTOs:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @PostMapping
    public ResponseEntity<UserResponse> createUser(@Valid @RequestBody CreateUserRequest request) {
        // Convert DTO to Entity
        User user = new User();
        user.setUsername(request.getUsername());
        user.setEmail(request.getEmail());
        user.setPassword(passwordEncoder.encode(request.getPassword()));
        
        User created = userService.create(user);
        
        // Convert Entity to Response DTO
        UserResponse response = new UserResponse(created);
        
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<UserResponse> getUserById(@PathVariable Long id) {
        return userService.findById(id)
                .map(UserResponse::new)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }
}
```

**DTO Mapping with ModelMapper:**
```java
@Configuration
public class AppConfig {
    @Bean
    public ModelMapper modelMapper() {
        return new ModelMapper();
    }
}

@Service
public class UserService {
    private final ModelMapper modelMapper;
    
    public UserResponse createUser(CreateUserRequest request) {
        User user = modelMapper.map(request, User.class);
        User saved = userRepository.save(user);
        return modelMapper.map(saved, UserResponse.class);
    }
}
```

---

## 4.3 Exception Handling

### 🔹 @ExceptionHandler (Controller-Level)

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id)
                .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
    }
    
    // Handle exception at controller level
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}
```

---

### 🔹 @ControllerAdvice (Global Exception Handling) ⭐ BEST PRACTICE

```java
// Custom Exception
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}

public class ValidationException extends RuntimeException {
    public ValidationException(String message) {
        super(message);
    }
}

// Error Response DTO
@Data
@AllArgsConstructor
public class ErrorResponse {
    private int status;
    private String message;
    private LocalDateTime timestamp;
    private List<String> errors;  // For validation errors
    
    public ErrorResponse(int status, String message, LocalDateTime timestamp) {
        this.status = status;
        this.message = message;
        this.timestamp = timestamp;
    }
}

// Global Exception Handler
@ControllerAdvice
public class GlobalExceptionHandler {
    
    // Handle custom UserNotFoundException
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    // Handle validation errors
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(
        MethodArgumentNotValidException ex
    ) {
        List<String> errors = ex.getBindingResult()
                .getFieldErrors()
                .stream()
                .map(error -> error.getField() + ": " + error.getDefaultMessage())
                .collect(Collectors.toList());
        
        ErrorResponse errorResponse = new ErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            "Validation failed",
            LocalDateTime.now(),
            errors
        );
        
        return ResponseEntity.badRequest().body(errorResponse);
    }
    
    // Handle generic exceptions
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.INTERNAL_SERVER_ERROR.value(),
            "An error occurred: " + ex.getMessage(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
    
    // Handle IllegalArgumentException
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<ErrorResponse> handleIllegalArgument(IllegalArgumentException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.BAD_REQUEST.value(),
            ex.getMessage(),
            LocalDateTime.now()
        );
        return ResponseEntity.badRequest().body(error);
    }
}
```

---

## 4.4 Validation

### 🔹 Bean Validation (JSR-380)

**Add Dependency:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

**Common Annotations:**

```java
public class CreateUserRequest {
    
    @NotNull(message = "Username cannot be null")
    @NotBlank(message = "Username cannot be empty")
    @Size(min = 3, max = 20, message = "Username must be 3-20 characters")
    @Pattern(regexp = "^[a-zA-Z0-9]+$", message = "Username must be alphanumeric")
    private String username;
    
    @NotBlank(message = "Email is required")
    @Email(message = "Email must be valid")
    private String email;
    
    @NotBlank(message = "Password is required")
    @Size(min = 8, message = "Password must be at least 8 characters")
    private String password;
    
    @Min(value = 18, message = "Age must be at least 18")
    @Max(value = 100, message = "Age must be less than 100")
    private Integer age;
    
    @Past(message = "Birth date must be in the past")
    private LocalDate birthDate;
    
    @Future(message = "Appointment must be in future")
    private LocalDateTime appointmentTime;
    
    @Positive(message = "Salary must be positive")
    private Double salary;
    
    @NotEmpty(message = "At least one role required")
    private List<String> roles;
    
    // Getters and Setters
}
```

**Use @Valid in Controller:**

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @PostMapping
    public ResponseEntity<UserResponse> createUser(
        @Valid @RequestBody CreateUserRequest request  // @Valid triggers validation
    ) {
        User user = userService.create(request);
        return ResponseEntity.status(HttpStatus.CREATED)
                .body(new UserResponse(user));
    }
    
    // If validation fails, MethodArgumentNotValidException is thrown
    // GlobalExceptionHandler catches it and returns 400 Bad Request
}
```

---

### 🔹 Custom Validation

**Custom Annotation:**
```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = UniqueEmailValidator.class)
public @interface UniqueEmail {
    String message() default "Email already exists";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

**Validator Implementation:**
```java
public class UniqueEmailValidator implements ConstraintValidator<UniqueEmail, String> {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public boolean isValid(String email, ConstraintValidatorContext context) {
        if (email == null) return true;  // Let @NotNull handle null
        return !userRepository.existsByEmail(email);
    }
}
```

**Usage:**
```java
public class CreateUserRequest {
    @NotBlank
    @Email
    @UniqueEmail  // Custom validation
    private String email;
}
```

---

## 🔥 Common Interview Questions

### Q1: Difference between @Controller and @RestController?
**A:** `@RestController = @Controller + @ResponseBody`. RestController automatically serializes return values to JSON/XML.

### Q2: When to use @PathVariable vs @RequestParam?
**A:**
- `@PathVariable` - Part of URL path (resource identification): `/users/123`
- `@RequestParam` - Query parameter (filtering/options): `/users?age=25&city=NYC`

### Q3: What is DTO and why use it?
**A:** Data Transfer Object - decouples API from entities. Benefits:
- Security (hide sensitive fields)
- API versioning
- Performance (selective fields)
- Validation specific to API

### Q4: How to handle validation errors globally?
**A:** Use `@ControllerAdvice` with `@ExceptionHandler(MethodArgumentNotValidException.class)` to catch validation errors and return custom error response.

### Q5: PUT vs PATCH?
**A:**
- **PUT** - Replace entire resource (all fields required)
- **PATCH** - Update specific fields (partial update)

### Q6: How to return custom HTTP status codes?
**A:** Use `ResponseEntity`:
```java
return ResponseEntity.status(HttpStatus.CREATED).body(user);
```

### Q7: What is @ControllerAdvice?
**A:** Global exception handler that applies to all controllers. Centralizes exception handling logic.

### Q8: How does Spring Boot convert JSON to Java objects?
**A:** Uses Jackson library (MessageConverter) automatically included in spring-boot-starter-web.

---

## 📝 Quick Revision Checklist

- [ ] Know @RestController composition
- [ ] Master all HTTP method annotations
- [ ] Understand @PathVariable vs @RequestParam
- [ ] Know ResponseEntity usage
- [ ] Master DTO pattern (Entity vs Request DTO vs Response DTO)
- [ ] Understand @ControllerAdvice for global exception handling
- [ ] Know validation annotations (@Valid, @NotBlank, @Email, etc.)
- [ ] Can create custom validators
- [ ] Know HTTP status codes (200, 201, 204, 400, 404, 500)
- [ ] Understand REST API best practices

---

## 💻 Practice Exercise

Build a complete REST API for Product management:
1. CRUD endpoints (GET, POST, PUT, PATCH, DELETE)
2. DTOs (CreateProductRequest, UpdateProductRequest, ProductResponse)
3. Validation on all request DTOs
4. Global exception handling
5. Custom exceptions (ProductNotFoundException)
6. Pagination and filtering
7. Proper HTTP status codes
8. ResponseEntity for all endpoints

**This is THE MOST IMPORTANT topic - 90% of interviews focus heavily on REST APIs!**
