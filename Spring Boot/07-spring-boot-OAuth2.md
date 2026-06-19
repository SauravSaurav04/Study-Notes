# 🔐 Spring Boot OAuth2.0 - Complete Guide

**Priority:** 🔴 CRITICAL ⭐ HIGH INTERVIEW FOCUS | **Interview Weight:** VERY HIGH

---

## 📑 Table of Contents

1. [What is OAuth2.0?](#71-what-is-oauth20)
   - [OAuth2.0 vs JWT](#-oauth20-vs-jwt)
   - [Real-World Example](#-real-world-example)
2. [OAuth2.0 Key Concepts](#72-oauth20-key-concepts)
   - [Roles](#-roles-in-oauth20)
   - [Grant Types](#-grant-types)
   - [Tokens](#-tokens-in-oauth20)
3. [OAuth2.0 Grant Types Explained](#73-oauth20-grant-types-explained)
   - [Client Credentials (Your Office Project!)](#-client-credentials-grant-your-office-project)
   - [Authorization Code](#-authorization-code-grant)
   - [Password Grant](#-password-grant-deprecated)
   - [Refresh Token](#-refresh-token-grant)
4. [Microsoft/Azure AD OAuth2.0](#74-microsoftazure-ad-oauth20)
   - [Getting Access Token](#-getting-microsoft-access-token)
5. [Spring Boot OAuth2.0 Implementation](#75-spring-boot-oauth20-implementation)
   - [Resource Server](#-resource-server-validate-tokens)
   - [Client Application](#-client-application-call-protected-apis)
6. [OAuth2.0 vs JWT - Complete Comparison](#76-oauth20-vs-jwt---complete-comparison)
7. [Common Interview Questions](#-common-interview-questions)
8. [Quick Revision Checklist](#-quick-revision-checklist)

---

## 7.1 What is OAuth2.0?

**OAuth2.0** is an **authorization framework** (not authentication!) that enables applications to obtain limited access to user accounts or services without exposing passwords.

### 🎯 Simple Definition:

> **OAuth2.0 is a protocol that allows one application to access resources hosted by another application on behalf of a user, WITHOUT sharing the user's password.**

---

### 🔹 OAuth2.0 vs JWT

| Aspect | OAuth2.0 | JWT |
|--------|----------|-----|
| **Type** | Authorization **Framework/Protocol** | Token **Format** |
| **Purpose** | Delegates access to resources | Carries user information |
| **Scope** | Complete authorization flow | Just a token structure |
| **Use Case** | Third-party app access (Google login, etc.) | API authentication |
| **Relationship** | OAuth2.0 **can use** JWT as token format | JWT is used **within** OAuth2.0 |

**🎯 Key Understanding:**
- **JWT** = A token format (like a digital passport)
- **OAuth2.0** = A complete system for getting and using tokens (like the visa application process)
- **OAuth2.0 can use JWT tokens!** (Many implementations do this)

---

### 🔹 Real-World Example

**Scenario:** You want to use "Canva" (design tool) and want to import photos from your Google Drive.

**Without OAuth2.0:** ❌
```
Canva: "Give me your Google password so I can access your photos"
You: "Um... no thanks!" 😰
```

**With OAuth2.0:** ✅
```
Canva: "Click here to grant me access to your Google Drive photos"
You: Redirected to Google
Google: "Canva wants to access your photos. Allow?"
You: "Yes, allow" ✅
Google: Sends Canva an access token
Canva: Uses token to access ONLY your photos (not email, not contacts)
```

**Benefits:**
- ✅ Canva never sees your Google password
- ✅ You control what Canva can access (only photos)
- ✅ You can revoke access anytime
- ✅ Google knows which app accessed your data

---

## 7.2 OAuth2.0 Key Concepts

---

### 🔹 Roles in OAuth2.0

OAuth2.0 defines **4 roles**:

#### 1️⃣ Resource Owner (User)
- **Who:** You (the user)
- **What:** Owns the data/resources
- **Example:** Person with Google account who owns Google Drive photos

#### 2️⃣ Resource Server
- **Who:** Server hosting protected resources
- **What:** Validates access tokens and serves resources
- **Example:** Google Drive API server

#### 3️⃣ Client (Application)
- **Who:** Application wanting to access resources
- **What:** Requests access tokens and uses them
- **Example:** Canva app

#### 4️⃣ Authorization Server
- **Who:** Server that issues access tokens
- **What:** Authenticates user and issues tokens
- **Example:** Google's OAuth2.0 server (accounts.google.com)

---

### 🔹 Grant Types

**Grant Type** = The method/flow used to get an access token

OAuth2.0 defines **4 main grant types**:

| Grant Type | Use Case | Example |
|------------|----------|---------|
| **Authorization Code** | Web apps, mobile apps (most secure) | Login with Google |
| **Client Credentials** | **Server-to-server (your office!)** | Microservice calling another microservice |
| **Password** | Trusted first-party apps (deprecated) | Mobile app for your own service |
| **Refresh Token** | Get new access token without re-login | Renew expired token |

---

### 🔹 Tokens in OAuth2.0

#### 1️⃣ Access Token
- **Purpose:** Grants access to protected resources
- **Lifetime:** Short (15 minutes - 1 hour)
- **Format:** Can be JWT or opaque string
- **Usage:** Sent in Authorization header

#### 2️⃣ Refresh Token
- **Purpose:** Gets new access token without re-authentication
- **Lifetime:** Long (days to months)
- **Format:** Opaque string
- **Usage:** Sent to authorization server only (never to resource server)

#### 3️⃣ ID Token (OpenID Connect)
- **Purpose:** Contains user identity information
- **Lifetime:** Short
- **Format:** Always JWT
- **Usage:** For authentication (who the user is)

---

## 7.3 OAuth2.0 Grant Types Explained

---

## 🔹 Client Credentials Grant (YOUR OFFICE PROJECT!)

**Use Case:** Machine-to-machine communication (no user involved)

### When to Use:
- ✅ Microservice A calls Microservice B
- ✅ Backend server calls external API (Microsoft Graph API)
- ✅ Scheduled jobs/cron jobs accessing resources
- ✅ CLI tools
- ❌ NOT for user-facing apps (no user login)

---

### 🎯 Your Office Scenario Explained

You mentioned: **client-id, client-secret, scope, grant-type** to get Microsoft access token.

This is **Client Credentials Grant**!

```
Your Application (Client)
    ↓
Microsoft Authorization Server (Azure AD)
    ↓
Your Application gets Access Token
    ↓
Calls Microsoft Graph API with token
```

---

### 📋 Client Credentials Flow

```
┌──────────────────────────────────────────────────────────────┐
│         CLIENT CREDENTIALS GRANT FLOW                        │
│         (Server-to-Server, No User Involved)                 │
└──────────────────────────────────────────────────────────────┘

Step 1: Client Authenticates with Authorization Server
────────────────────────────────────────────────────────

Client Application                    Authorization Server
(Your Backend)                        (Microsoft Azure AD)
     │                                         │
     │  POST /oauth2/v2.0/token               │
     │  Content-Type: application/x-www-form-urlencoded
     │                                         │
     │  Body:                                  │
     │  grant_type=client_credentials          │
     │  client_id=abc123                       │
     │  client_secret=xyz789                   │
     │  scope=https://graph.microsoft.com/.default
     │                                         │
     ├────────────────────────────────────────>│
     │                                         │
     │                                         │ Validate:
     │                                         │ • Is client_id valid?
     │                                         │ • Is client_secret correct?
     │                                         │ • Does client have permission
     │                                         │   for requested scope?
     │                                         │
     │                                         │ Generate Access Token:
     │                                         │ {
     │                                         │   "aud": "https://graph.microsoft.com",
     │                                         │   "iss": "https://login.microsoftonline.com/",
     │                                         │   "iat": 1695000000,
     │                                         │   "exp": 1695003600,
     │                                         │   "appid": "abc123",
     │                                         │   "roles": ["User.Read.All"]
     │                                         │ }
     │                                         │
     │  Response:                              │
     │  {                                      │
     │    "token_type": "Bearer",              │
     │    "access_token": "eyJ0eXAi...",       │
     │    "expires_in": 3600                   │
     │  }                                      │
     │<────────────────────────────────────────┤
     │                                         │
     │  Store token in memory                  │
     │                                         │

Step 2: Client Uses Token to Access Resource
──────────────────────────────────────────────

Client Application                    Resource Server
(Your Backend)                        (Microsoft Graph API)
     │                                         │
     │  GET /v1.0/users                        │
     │  Authorization: Bearer eyJ0eXAi...     │
     ├────────────────────────────────────────>│
     │                                         │
     │                                         │ Validate Token:
     │                                         │ • Verify signature
     │                                         │ • Check expiration
     │                                         │ • Check audience
     │                                         │ • Check required scope/roles
     │                                         │
     │  Response:                              │
     │  {                                      │
     │    "value": [                           │
     │      {                                  │
     │        "id": "user1",                   │
     │        "displayName": "John Doe",       │
     │        "mail": "john@company.com"       │
     │      }                                  │
     │    ]                                    │
     │  }                                      │
     │<────────────────────────────────────────┤
     │                                         │

Step 3: Token Expiration & Refresh
────────────────────────────────────

After 1 hour (expires_in: 3600):
     │                                         │
     │  GET /v1.0/users                        │
     │  Authorization: Bearer <expired_token> │
     ├────────────────────────────────────────>│
     │                                         │
     │  Response: 401 Unauthorized             │
     │  {                                      │
     │    "error": "invalid_token",            │
     │    "error_description": "Token expired" │
     │  }                                      │
     │<────────────────────────────────────────┤
     │                                         │
     │  Request NEW token from Auth Server     │
     │  (Repeat Step 1)                        │
     │                                         │
```

---

### 💻 Example: Getting Microsoft Access Token

**Real-World Code (What You Use at Work!):**

```java
@Service
public class MicrosoftGraphService {
    
    @Value("${azure.client-id}")
    private String clientId;
    
    @Value("${azure.client-secret}")
    private String clientSecret;
    
    @Value("${azure.tenant-id}")
    private String tenantId;
    
    private final RestTemplate restTemplate;
    
    /**
     * Get access token using Client Credentials Grant
     * This is what you do at work!
     */
    public String getAccessToken() {
        String tokenUrl = String.format(
            "https://login.microsoftonline.com/%s/oauth2/v2.0/token",
            tenantId
        );
        
        // Create form data
        MultiValueMap<String, String> formData = new LinkedMultiValueMap<>();
        formData.add("grant_type", "client_credentials");
        formData.add("client_id", clientId);
        formData.add("client_secret", clientSecret);
        formData.add("scope", "https://graph.microsoft.com/.default");
        
        // Set headers
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
        
        // Create request
        HttpEntity<MultiValueMap<String, String>> request = 
            new HttpEntity<>(formData, headers);
        
        // Send POST request
        ResponseEntity<TokenResponse> response = restTemplate.postForEntity(
            tokenUrl,
            request,
            TokenResponse.class
        );
        
        return response.getBody().getAccessToken();
    }
    
    /**
     * Call Microsoft Graph API with access token
     */
    public List<User> getUsers() {
        String accessToken = getAccessToken();
        
        String apiUrl = "https://graph.microsoft.com/v1.0/users";
        
        HttpHeaders headers = new HttpHeaders();
        headers.setBearerAuth(accessToken);  // Add token to Authorization header
        
        HttpEntity<String> entity = new HttpEntity<>(headers);
        
        ResponseEntity<UsersResponse> response = restTemplate.exchange(
            apiUrl,
            HttpMethod.GET,
            entity,
            UsersResponse.class
        );
        
        return response.getBody().getValue();
    }
}

// Token Response DTO
@Data
public class TokenResponse {
    @JsonProperty("token_type")
    private String tokenType;
    
    @JsonProperty("access_token")
    private String accessToken;
    
    @JsonProperty("expires_in")
    private Integer expiresIn;
}
```

**application.yml:**
```yaml
azure:
  tenant-id: your-tenant-id
  client-id: your-application-client-id
  client-secret: your-client-secret
```

---

### 🔍 Understanding the Parameters

#### 1️⃣ grant_type=client_credentials
- **What:** Tells OAuth2.0 server which flow to use
- **Value:** `client_credentials` (for server-to-server)
- **Other values:** `authorization_code`, `password`, `refresh_token`

#### 2️⃣ client_id
- **What:** Public identifier for your application
- **Example:** `abc123-def456-ghi789`
- **Where to get:** Azure Portal → App Registrations → Application (client) ID
- **Security:** Can be public (not sensitive)

#### 3️⃣ client_secret
- **What:** Secret password for your application
- **Example:** `xyz789~abc123.def456`
- **Where to get:** Azure Portal → App Registrations → Certificates & secrets
- **Security:** 🔐 HIGHLY SENSITIVE! Never commit to Git!

#### 4️⃣ scope
- **What:** Permissions your app is requesting
- **Example:** `https://graph.microsoft.com/.default`
- **Meaning:** Request all permissions configured for the app in Azure AD
- **Other examples:**
  - `User.Read.All` - Read all user profiles
  - `Mail.Send` - Send mail as any user
  - `Calendars.Read` - Read calendars

---

### 🎯 Key Points About Client Credentials

1. **No User Login:** 
   - No username/password needed
   - App authenticates as itself
   - Perfect for background jobs

2. **Access Token Expiration:**
   - Typically 1 hour (3600 seconds)
   - Must request new token when expired
   - Should implement token caching!

3. **No Refresh Token:**
   - Client Credentials doesn't issue refresh tokens
   - Just request a new access token when needed

4. **Security:**
   - Client secret must be protected
   - Use environment variables or Azure Key Vault
   - Never hardcode or commit to Git

---

## 🔹 Authorization Code Grant

**Use Case:** User-facing web applications (most secure for user login)

### When to Use:
- ✅ Web applications with backend
- ✅ "Login with Google/Facebook/Microsoft" buttons
- ✅ Mobile apps (with PKCE extension)
- ✅ Any app where user needs to grant permission

---

### 📋 Authorization Code Flow

```
┌──────────────────────────────────────────────────────────────┐
│         AUTHORIZATION CODE GRANT FLOW                        │
│         (User Login, Most Common for Web Apps)               │
└──────────────────────────────────────────────────────────────┘

Step 1: User Clicks "Login with Google"
─────────────────────────────────────────

User Browser                Your App                Google
     │                         │                       │
     │  Click "Login"          │                       │
     ├────────────────────────>│                       │
     │                         │                       │
     │  Redirect to Google     │                       │
     │<────────────────────────┤                       │
     │                         │                       │
     │  GET /oauth/authorize?  │                       │
     │    response_type=code   │                       │
     │    &client_id=abc123    │                       │
     │    &redirect_uri=https://yourapp.com/callback
     │    &scope=profile email │                       │
     │    &state=random123     │                       │
     ├──────────────────────────────────────────────────>│
     │                         │                       │
     │                         │                       │ User sees:
     │                         │                       │ "YourApp wants to:"
     │                         │                       │ ✓ View your profile
     │                         │                       │ ✓ View your email
     │                         │                       │
     │  Google Login Page      │                       │ [Allow] [Deny]
     │<──────────────────────────────────────────────────┤
     │                         │                       │
     │  User enters credentials│                       │
     │  User clicks "Allow"    │                       │
     ├──────────────────────────────────────────────────>│
     │                         │                       │
     │                         │                       │ Validate user
     │                         │                       │ Generate auth code
     │                         │                       │

Step 2: Google Redirects Back with Authorization Code
───────────────────────────────────────────────────────

     │  Redirect:              │                       │
     │  yourapp.com/callback?  │                       │
     │    code=AUTH_CODE_123   │                       │
     │    &state=random123     │                       │
     │<──────────────────────────────────────────────────┤
     │                         │                       │
     │  GET /callback?code=... │                       │
     ├────────────────────────>│                       │
     │                         │                       │

Step 3: Your App Exchanges Code for Tokens
────────────────────────────────────────────

     │                         │                       │
     │                         │  POST /oauth/token    │
     │                         │  grant_type=authorization_code
     │                         │  code=AUTH_CODE_123   │
     │                         │  client_id=abc123     │
     │                         │  client_secret=xyz789 │
     │                         │  redirect_uri=https://yourapp.com/callback
     │                         ├──────────────────────>│
     │                         │                       │
     │                         │                       │ Validate:
     │                         │                       │ • Code valid?
     │                         │                       │ • Code not expired?
     │                         │                       │ • Client ID matches?
     │                         │                       │ • Redirect URI matches?
     │                         │                       │
     │                         │  Response:            │
     │                         │  {                    │
     │                         │    "access_token": "eyJ...",
     │                         │    "refresh_token": "refresh123",
     │                         │    "id_token": "eyJhb...",
     │                         │    "token_type": "Bearer",
     │                         │    "expires_in": 3600 │
     │                         │  }                    │
     │                         │<──────────────────────┤
     │                         │                       │
     │                         │  Store tokens         │
     │                         │  Create user session  │
     │                         │                       │

Step 4: Access Protected Resources
────────────────────────────────────

     │                         │                       │
     │  GET /profile           │                       │
     ├────────────────────────>│                       │
     │                         │                       │
     │                         │  GET /userinfo        │
     │                         │  Authorization: Bearer eyJ...
     │                         ├──────────────────────>│
     │                         │                       │
     │                         │  {                    │
     │                         │    "sub": "12345",    │
     │                         │    "name": "John Doe",│
     │                         │    "email": "john@example.com"
     │                         │  }                    │
     │                         │<──────────────────────┤
     │                         │                       │
     │  Render profile page    │                       │
     │<────────────────────────┤                       │
     │                         │                       │
```

---

### 💻 Spring Boot Implementation - Authorization Code

```java
// Add dependency
// <dependency>
//     <groupId>org.springframework.boot</groupId>
//     <artifactId>spring-boot-starter-oauth2-client</artifactId>
// </dependency>

// application.yml
spring:
  security:
    oauth2:
      client:
        registration:
          google:
            client-id: your-google-client-id
            client-secret: your-google-client-secret
            scope:
              - profile
              - email
            redirect-uri: "{baseUrl}/login/oauth2/code/{registrationId}"
        provider:
          google:
            authorization-uri: https://accounts.google.com/o/oauth2/v2/auth
            token-uri: https://oauth2.googleapis.com/token
            user-info-uri: https://www.googleapis.com/oauth2/v3/userinfo
            user-name-attribute: sub

// Security Configuration
@Configuration
@EnableWebSecurity
public class OAuth2SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/", "/login**", "/error").permitAll()
                .anyRequest().authenticated()
            )
            .oauth2Login(oauth2 -> oauth2
                .loginPage("/login")
                .defaultSuccessUrl("/dashboard", true)
                .failureUrl("/login?error=true")
            );
        
        return http.build();
    }
}

// Controller to get user info
@RestController
public class UserController {
    
    @GetMapping("/user")
    public Map<String, Object> user(@AuthenticationPrincipal OAuth2User principal) {
        return principal.getAttributes();
    }
    
    @GetMapping("/dashboard")
    public String dashboard(@AuthenticationPrincipal OAuth2User principal) {
        String name = principal.getAttribute("name");
        String email = principal.getAttribute("email");
        return "Welcome " + name + " (" + email + ")";
    }
}
```

---

## 🔹 Password Grant (Deprecated)

**⚠️ WARNING: This grant type is deprecated and should NOT be used!**

### When it was used:
- First-party mobile apps (your own app, not third-party)
- When you fully trust the client application

### Why deprecated:
- ❌ Client handles user credentials (security risk)
- ❌ No way to enforce MFA
- ❌ Phishing risk
- ✅ Use Authorization Code with PKCE instead

---

## 🔹 Refresh Token Grant

**Use Case:** Get new access token without re-authentication

### Flow:
```
Client has expired access token
    ↓
Client sends refresh token to Authorization Server
    ↓
Authorization Server validates refresh token
    ↓
Returns new access token (and optionally new refresh token)
```

### Implementation:
```java
@Service
public class TokenRefreshService {
    
    public String refreshAccessToken(String refreshToken) {
        String tokenUrl = "https://oauth2.example.com/token";
        
        MultiValueMap<String, String> formData = new LinkedMultiValueMap<>();
        formData.add("grant_type", "refresh_token");
        formData.add("refresh_token", refreshToken);
        formData.add("client_id", clientId);
        formData.add("client_secret", clientSecret);
        
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
        
        HttpEntity<MultiValueMap<String, String>> request = 
            new HttpEntity<>(formData, headers);
        
        ResponseEntity<TokenResponse> response = restTemplate.postForEntity(
            tokenUrl,
            request,
            TokenResponse.class
        );
        
        return response.getBody().getAccessToken();
    }
}
```

---

## 7.4 Microsoft/Azure AD OAuth2.0

### 🔹 Getting Microsoft Access Token

**Azure AD OAuth2.0 Endpoints:**

#### For Client Credentials:
```
Token Endpoint:
https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/token

Authorization Endpoint (not used for client credentials):
https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/authorize
```

#### Step-by-Step Setup:

**1. Register App in Azure Portal:**
```
1. Go to Azure Portal (portal.azure.com)
2. Azure Active Directory → App registrations → New registration
3. Name: "My Backend Service"
4. Supported account types: Single tenant
5. Click "Register"
```

**2. Note down these values:**
```
Application (client) ID: abc123-def456-ghi789
Directory (tenant) ID: xyz789-abc123-def456
```

**3. Create Client Secret:**
```
1. App registration → Certificates & secrets
2. New client secret
3. Description: "Production Secret"
4. Expires: 24 months
5. Copy the VALUE (you'll only see it once!)
```

**4. Configure API Permissions:**
```
1. App registration → API permissions
2. Add a permission → Microsoft Graph
3. Application permissions (not Delegated!)
4. Select permissions:
   - User.Read.All
   - Mail.Send
   - etc.
5. Grant admin consent (important!)
```

**5. Use in Your Application:**
```yaml
# application.yml
azure:
  tenant-id: xyz789-abc123-def456
  client-id: abc123-def456-ghi789
  client-secret: ${AZURE_CLIENT_SECRET}  # From environment variable
  scope: https://graph.microsoft.com/.default
```

---

### 💻 Complete Working Example

```java
@Configuration
public class RestTemplateConfig {
    
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

@Service
@Slf4j
public class MicrosoftGraphService {
    
    @Value("${azure.tenant-id}")
    private String tenantId;
    
    @Value("${azure.client-id}")
    private String clientId;
    
    @Value("${azure.client-secret}")
    private String clientSecret;
    
    private final RestTemplate restTemplate;
    private String cachedToken;
    private Instant tokenExpiryTime;
    
    public MicrosoftGraphService(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }
    
    /**
     * Get access token with caching
     * Avoids requesting new token for every API call
     */
    public String getAccessToken() {
        // Return cached token if still valid
        if (cachedToken != null && Instant.now().isBefore(tokenExpiryTime)) {
            log.debug("Using cached access token");
            return cachedToken;
        }
        
        log.info("Requesting new access token from Azure AD");
        
        String tokenUrl = String.format(
            "https://login.microsoftonline.com/%s/oauth2/v2.0/token",
            tenantId
        );
        
        // Prepare request
        MultiValueMap<String, String> formData = new LinkedMultiValueMap<>();
        formData.add("grant_type", "client_credentials");
        formData.add("client_id", clientId);
        formData.add("client_secret", clientSecret);
        formData.add("scope", "https://graph.microsoft.com/.default");
        
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
        
        HttpEntity<MultiValueMap<String, String>> request = 
            new HttpEntity<>(formData, headers);
        
        try {
            ResponseEntity<TokenResponse> response = restTemplate.postForEntity(
                tokenUrl,
                request,
                TokenResponse.class
            );
            
            TokenResponse tokenResponse = response.getBody();
            cachedToken = tokenResponse.getAccessToken();
            
            // Cache token (expires_in is in seconds, subtract 5 minutes for safety)
            tokenExpiryTime = Instant.now().plusSeconds(tokenResponse.getExpiresIn() - 300);
            
            log.info("Successfully obtained access token, expires at {}", tokenExpiryTime);
            return cachedToken;
            
        } catch (Exception e) {
            log.error("Failed to get access token", e);
            throw new RuntimeException("OAuth2.0 token request failed", e);
        }
    }
    
    /**
     * Get all users from Azure AD
     */
    public List<User> getAllUsers() {
        String accessToken = getAccessToken();
        String apiUrl = "https://graph.microsoft.com/v1.0/users";
        
        HttpHeaders headers = new HttpHeaders();
        headers.setBearerAuth(accessToken);
        
        HttpEntity<String> entity = new HttpEntity<>(headers);
        
        try {
            ResponseEntity<UsersResponse> response = restTemplate.exchange(
                apiUrl,
                HttpMethod.GET,
                entity,
                UsersResponse.class
            );
            
            return response.getBody().getValue();
            
        } catch (HttpClientErrorException.Unauthorized e) {
            log.warn("Token expired or invalid, clearing cache and retrying");
            cachedToken = null;  // Clear cached token
            return getAllUsers();  // Retry
            
        } catch (Exception e) {
            log.error("Failed to get users from Microsoft Graph", e);
            throw new RuntimeException("Microsoft Graph API call failed", e);
        }
    }
    
    /**
     * Send email via Microsoft Graph
     */
    public void sendEmail(String to, String subject, String body) {
        String accessToken = getAccessToken();
        String apiUrl = "https://graph.microsoft.com/v1.0/users/{userId}/sendMail";
        
        // Create email message
        EmailMessage message = new EmailMessage();
        message.setSubject(subject);
        message.setBody(new EmailBody(body));
        message.setToRecipients(List.of(new EmailRecipient(to)));
        
        HttpHeaders headers = new HttpHeaders();
        headers.setBearerAuth(accessToken);
        headers.setContentType(MediaType.APPLICATION_JSON);
        
        HttpEntity<EmailMessage> entity = new HttpEntity<>(message, headers);
        
        restTemplate.postForEntity(apiUrl, entity, Void.class);
        log.info("Email sent successfully to {}", to);
    }
}

// DTOs
@Data
public class TokenResponse {
    @JsonProperty("token_type")
    private String tokenType;
    
    @JsonProperty("access_token")
    private String accessToken;
    
    @JsonProperty("expires_in")
    private Integer expiresIn;  // seconds
}

@Data
public class UsersResponse {
    @JsonProperty("@odata.context")
    private String context;
    
    private List<User> value;
}

@Data
public class User {
    private String id;
    private String displayName;
    private String mail;
    private String userPrincipalName;
}
```

---

## 7.5 Spring Boot OAuth2.0 Implementation

---

## 🔹 Resource Server (Validate Tokens)

**Use Case:** Your API validates OAuth2.0 tokens issued by external Authorization Server

**Add Dependency:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
```

**application.yml:**
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          # Azure AD issuer
          issuer-uri: https://login.microsoftonline.com/{tenant-id}/v2.0
          
          # Or specify JWK Set URI directly
          # jwk-set-uri: https://login.microsoftonline.com/{tenant-id}/discovery/v2.0/keys
```

**Security Configuration:**
```java
@Configuration
@EnableWebSecurity
public class ResourceServerConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt
                    .jwtAuthenticationConverter(jwtAuthenticationConverter())
                )
            );
        
        return http.build();
    }
    
    /**
     * Convert JWT claims to Spring Security authorities
     */
    @Bean
    public JwtAuthenticationConverter jwtAuthenticationConverter() {
        JwtGrantedAuthoritiesConverter grantedAuthoritiesConverter = 
            new JwtGrantedAuthoritiesConverter();
        
        // Azure AD uses "roles" claim
        grantedAuthoritiesConverter.setAuthoritiesClaimName("roles");
        grantedAuthoritiesConverter.setAuthorityPrefix("ROLE_");
        
        JwtAuthenticationConverter jwtAuthenticationConverter = 
            new JwtAuthenticationConverter();
        jwtAuthenticationConverter.setJwtGrantedAuthoritiesConverter(
            grantedAuthoritiesConverter
        );
        
        return jwtAuthenticationConverter;
    }
}

// Controller
@RestController
@RequestMapping("/api")
public class ProtectedController {
    
    @GetMapping("/users")
    public List<User> getUsers(@AuthenticationPrincipal Jwt jwt) {
        String appId = jwt.getClaimAsString("appid");
        List<String> roles = jwt.getClaimAsStringList("roles");
        
        log.info("Request from app: {}, roles: {}", appId, roles);
        
        return userService.getAllUsers();
    }
    
    @PreAuthorize("hasRole('Admin')")
    @DeleteMapping("/users/{id}")
    public void deleteUser(@PathVariable String id) {
        userService.deleteUser(id);
    }
}
```

---

## 🔹 Client Application (Call Protected APIs)

**Use Case:** Your application calls external APIs protected by OAuth2.0

```java
@Configuration
public class OAuth2ClientConfig {
    
    @Bean
    public OAuth2AuthorizedClientManager authorizedClientManager(
        ClientRegistrationRepository clientRegistrationRepository,
        OAuth2AuthorizedClientService authorizedClientService
    ) {
        OAuth2AuthorizedClientProvider authorizedClientProvider =
            OAuth2AuthorizedClientProviderBuilder.builder()
                .clientCredentials()
                .refreshToken()
                .build();
        
        AuthorizedClientServiceOAuth2AuthorizedClientManager authorizedClientManager =
            new AuthorizedClientServiceOAuth2AuthorizedClientManager(
                clientRegistrationRepository,
                authorizedClientService
            );
        
        authorizedClientManager.setAuthorizedClientProvider(authorizedClientProvider);
        
        return authorizedClientManager;
    }
    
    @Bean
    public WebClient webClient(OAuth2AuthorizedClientManager authorizedClientManager) {
        ServletOAuth2AuthorizedClientExchangeFilterFunction oauth2Client =
            new ServletOAuth2AuthorizedClientExchangeFilterFunction(
                authorizedClientManager
            );
        
        return WebClient.builder()
            .apply(oauth2Client.oauth2Configuration())
            .build();
    }
}

@Service
public class ExternalApiService {
    
    private final WebClient webClient;
    
    public ExternalApiService(WebClient webClient) {
        this.webClient = webClient;
    }
    
    /**
     * Call external API with automatic OAuth2.0 token handling
     * Spring automatically gets and refreshes tokens!
     */
    public List<User> getUsersFromExternalApi() {
        return webClient
            .get()
            .uri("https://api.example.com/users")
            .attributes(
                clientRegistrationId("my-client")  // From application.yml
            )
            .retrieve()
            .bodyToMono(new ParameterizedTypeReference<List<User>>() {})
            .block();
    }
}
```

**application.yml:**
```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          my-client:
            provider: azure
            client-id: ${AZURE_CLIENT_ID}
            client-secret: ${AZURE_CLIENT_SECRET}
            authorization-grant-type: client_credentials
            scope: https://graph.microsoft.com/.default
        provider:
          azure:
            token-uri: https://login.microsoftonline.com/${AZURE_TENANT_ID}/oauth2/v2.0/token
```

---

## 7.6 OAuth2.0 vs JWT - Complete Comparison

```
┌─────────────────────────────────────────────────────────────┐
│                  OAuth2.0 vs JWT                            │
└─────────────────────────────────────────────────────────────┘

OAuth2.0                          JWT
────────                          ───
📋 PROTOCOL                       🎫 TOKEN FORMAT
(How to get tokens)              (How tokens look)

┌──────────────────┐             ┌──────────────────┐
│ Authorization    │             │ Header.Payload.  │
│ Framework        │             │ Signature        │
│                  │             │                  │
│ • Grant types    │             │ Base64 encoded   │
│ • Token endpoints│             │ JSON data        │
│ • Scopes         │             │                  │
│ • Refresh tokens │             │ Self-contained   │
└──────────────────┘             └──────────────────┘

    Can Use
    ───────>
    
OAuth2.0 can use JWT as its token format!
JWT is often used as the access token in OAuth2.0
```

### Detailed Comparison Table

| Aspect | OAuth2.0 | JWT |
|--------|----------|-----|
| **Type** | Authorization protocol | Token format |
| **Purpose** | Delegate access rights | Represent claims/identity |
| **Defines** | How to get and use tokens | Structure of tokens |
| **Scope** | Complete flow (authorization, refresh, etc.) | Just the token itself |
| **Token Format** | Can be JWT, opaque string, or any format | Always Header.Payload.Signature |
| **Revocation** | Can revoke via Authorization Server | Hard to revoke (stateless) |
| **Storage** | Tokens stored by client | Token contains data itself |
| **Use Case** | Third-party authorization | API authentication |
| **Standards** | RFC 6749, RFC 6750 | RFC 7519 |

### When to Use Each:

**Use OAuth2.0 When:**
- ✅ Need third-party app access (Login with Google)
- ✅ Need delegated authorization
- ✅ Need refresh tokens
- ✅ Need fine-grained scopes
- ✅ Server-to-server communication

**Use JWT When:**
- ✅ Simple API authentication
- ✅ Microservices communication
- ✅ Stateless authentication
- ✅ Don't need third-party integration

**Use Both Together:**
- ✅ OAuth2.0 for authorization flow
- ✅ JWT as the access token format
- ✅ **This is the most common production setup!**

---

## 🔥 Common Interview Questions

### Q1: What's the difference between OAuth2.0 and JWT?
**A:** OAuth2.0 is an authorization **protocol** that defines how to obtain tokens. JWT is a token **format**. OAuth2.0 can use JWT as its access token format. Think of OAuth2.0 as the "delivery system" and JWT as the "package format."

### Q2: Explain Client Credentials grant type
**A:** Used for machine-to-machine communication. Application authenticates with client_id and client_secret to get access token. No user involvement. Common for microservices calling APIs.

### Q3: What is the most secure OAuth2.0 grant type?
**A:** Authorization Code with PKCE (Proof Key for Code Exchange). It prevents authorization code interception attacks and is recommended for both web and mobile apps.

### Q4: What are scopes in OAuth2.0?
**A:** Scopes define permissions/access levels. Examples: `read:users`, `write:posts`. Client requests scopes, user approves, and access token is limited to approved scopes only.

### Q5: Difference between access token and refresh token?
**A:**
- **Access Token:** Short-lived (minutes to hours), used to access resources
- **Refresh Token:** Long-lived (days to months), used to get new access tokens without re-authentication

### Q6: Can OAuth2.0 access token be revoked?
**A:** Yes, OAuth2.0 defines token revocation endpoint (RFC 7009). Client sends token to Authorization Server to revoke it. However, if using stateless JWT tokens, revocation requires blacklisting.

### Q7: What is the redirect_uri in OAuth2.0?
**A:** The URL where Authorization Server sends the user back after authorization. Must be pre-registered to prevent redirect attacks. Example: `https://yourapp.com/oauth/callback`

### Q8: What is state parameter in OAuth2.0?
**A:** Random string sent in authorization request and verified on callback. Prevents CSRF attacks. Client generates random state, stores it, and verifies it matches when user returns.

### Q9: Your office uses client_credentials - why?
**A:** Because we need server-to-server communication with Microsoft APIs (Graph API). No user login is involved. Our backend service authenticates itself using client_id and client_secret to access Microsoft resources.

### Q10: How to secure client_secret?
**A:**
- ✅ Store in environment variables
- ✅ Use Azure Key Vault or AWS Secrets Manager
- ✅ Never commit to Git
- ✅ Rotate periodically
- ✅ Use different secrets for dev/prod

---

## 📝 Quick Revision Checklist

- [ ] Understand OAuth2.0 vs JWT (protocol vs format)
- [ ] Know all 4 OAuth2.0 roles (Resource Owner, Client, Authorization Server, Resource Server)
- [ ] Master Client Credentials grant (your office scenario!)
- [ ] Understand Authorization Code grant (Login with Google)
- [ ] Know when to use each grant type
- [ ] Understand scopes and permissions
- [ ] Know difference between access token and refresh token
- [ ] Can implement Microsoft/Azure AD OAuth2.0
- [ ] Understand Resource Server configuration
- [ ] Know how to secure client secrets
- [ ] Understand token caching strategy
- [ ] Know OAuth2.0 security best practices

---

## 🎯 Key Takeaways

1. **OAuth2.0 = Authorization Protocol**, not just authentication
2. **Client Credentials** = Server-to-server (what you use at work!)
3. **Authorization Code** = User-facing apps (Login with Google)
4. **OAuth2.0 uses JWT** as token format (often)
5. **Access Token** = Short-lived, used to access resources
6. **Refresh Token** = Long-lived, used to get new access tokens
7. **Scopes** = Fine-grained permissions
8. **client_secret** = Must be protected like a password!

---

## 💡 Your Office Scenario Explained

```
Your Spring Boot Application
    ↓
POST to Microsoft OAuth2.0 Server
    • grant_type = client_credentials
    • client_id = (your app ID)
    • client_secret = (your secret)
    • scope = https://graph.microsoft.com/.default
    ↓
Receives Access Token (JWT format)
    ↓
Uses token to call Microsoft Graph API
    • Authorization: Bearer <token>
    ↓
Gets users, sends emails, etc.
```

**This is OAuth2.0 Client Credentials Grant with JWT tokens!**

You now understand what happens behind the scenes at your office! 🎉
