# 🧱 Phase 0: Prerequisites — Study Notes

> **Phase**: 0 of 8 | **Estimated Time**: 2–4 weeks
> **Goal**: Build a solid foundation in Java 17+, Spring Boot 3.x, and AI/ML concepts before diving into Spring AI.
> **Skip if**: You already work with Spring Boot 3.x daily and understand LLM basics.

---

## 📋 Table of Contents

1. [Java 17+ Modern Features](#java-17-modern-features)
2. [Spring Boot 3.x Fundamentals](#spring-boot-3x-fundamentals)
3. [Dependency Injection & IoC](#dependency-injection--ioc)
4. [Spring MVC & REST APIs](#spring-mvc--rest-apis)
5. [Spring Data JPA](#spring-data-jpa)
6. [Configuration — application.yml](#configuration--applicationyml)
7. [Maven & Gradle Build Tools](#maven--gradle-build-tools)
8. [REST Clients — Postman & HTTPie](#rest-clients--postman--httpie)
9. [AI/ML Concepts for Spring AI](#aiml-concepts-for-spring-ai)
10. [What is Generative AI?](#what-is-generative-ai)
11. [Large Language Models (LLMs)](#large-language-models-llms)
12. [Tokens, Temperature & Parameters](#tokens-temperature--parameters)
13. [Embeddings](#embeddings)
14. [Vector Databases](#vector-databases)
15. [RAG — Retrieval-Augmented Generation](#rag--retrieval-augmented-generation)
16. [Tools to Install](#tools-to-install)
17. [Phase 0 Checklist](#phase-0-checklist)

---

<a id="java-17-modern-features"></a>
## 1. Java 17+ Modern Features

Spring AI **requires Java 17 minimum** (Java 21 recommended). These are the modern Java features you'll see constantly in Spring AI code.

---

### 1.1 Records (Java 16+)

A **Record** is a concise way to create immutable data classes. Spring AI uses records heavily for structured AI output mapping.

```java
// OLD WAY — boilerplate class
public class MovieRecommendation {
    private final String title;
    private final String genre;
    private final int year;

    public MovieRecommendation(String title, String genre, int year) {
        this.title = title;
        this.genre = genre;
        this.year = year;
    }

    public String getTitle() { return title; }
    public String getGenre() { return genre; }
    public int getYear() { return year; }
    // equals, hashCode, toString also needed...
}

// NEW WAY — Record (Java 16+)
// Compiler generates: constructor, getters, equals, hashCode, toString automatically
record MovieRecommendation(String title, String genre, int year) {}
```

**Using a record:**
```java
// Creating an instance
MovieRecommendation movie = new MovieRecommendation("Inception", "Sci-Fi", 2010);

// Accessing fields — use field name directly (no "get" prefix)
System.out.println(movie.title());   // "Inception"
System.out.println(movie.genre());   // "Sci-Fi"
System.out.println(movie.year());    // 2010

// Records are immutable — you can't change fields after creation
// movie.title = "Avatar";  // ❌ COMPILE ERROR
```

**Why it matters for Spring AI:**
```java
// Spring AI maps AI JSON responses directly to records
record WeatherInfo(String city, double temperature, String condition) {}

WeatherInfo weather = chatClient.prompt()
    .user("What's the weather in Mumbai?")
    .call()
    .entity(WeatherInfo.class);  // AI response auto-mapped to this record!
```

---

### 1.2 Sealed Classes (Java 17)

Sealed classes restrict which classes can extend them. Used in Spring AI's message hierarchy.

```java
// Only SystemMessage, UserMessage, AssistantMessage can implement Message
public sealed interface Message
    permits SystemMessage, UserMessage, AssistantMessage, ToolResponseMessage {
    String getContent();
    MessageType getMessageType();
}
```

You'll see sealed classes when working with Spring AI's message types — understanding this helps you know which message types exist.

---

### 1.3 `var` Keyword (Java 10+)

Local variable type inference — the compiler figures out the type.

```java
// Without var
ChatClient chatClient = ChatClient.builder(chatModel).build();
List<Document> documents = vectorStore.similaritySearch("Spring AI");

// With var — cleaner, especially for long type names
var chatClient = ChatClient.builder(chatModel).build();
var documents = vectorStore.similaritySearch("Spring AI");
```

---

### 1.4 Text Blocks (Java 15+)

Multi-line strings — heavily used in Spring AI for prompts and JSON.

```java
// OLD WAY — concatenating strings
String prompt = "You are a Java expert.\n" +
                "Answer the following: " + question + "\n" +
                "Keep it concise.";

// NEW WAY — Text Block (Java 15+)
String prompt = """
        You are a Java expert.
        Answer the following: %s
        Keep it concise.
        """.formatted(question);
```

**Spring AI uses text blocks for prompt templates:**
```java
String systemPrompt = """
        You are an AI assistant specialized in Spring Boot.
        Always provide working Java code examples.
        Format code in markdown code blocks.
        """;

chatClient.prompt()
    .system(systemPrompt)
    .user(userQuestion)
    .call()
    .content();
```

---

### 1.5 Switch Expressions (Java 14+)

```java
// Spring AI message type handling
String role = switch (message.getMessageType()) {
    case USER      -> "User";
    case SYSTEM    -> "System";
    case ASSISTANT -> "Assistant";
    case TOOL      -> "Tool";
};
```

---

### 1.6 Pattern Matching `instanceof` (Java 16+)

```java
// OLD WAY
if (message instanceof UserMessage) {
    UserMessage userMsg = (UserMessage) message;
    System.out.println(userMsg.getContent());
}

// NEW WAY — Pattern matching
if (message instanceof UserMessage userMsg) {
    System.out.println(userMsg.getContent());  // userMsg is already cast!
}
```

---

<a id="spring-boot-3x-fundamentals"></a>
## 2. Spring Boot 3.x Fundamentals

Spring Boot is the foundation that Spring AI is built on. You need to understand how Spring Boot works before Spring AI makes sense.

### What is Spring Boot?

```
Traditional Spring (before Spring Boot):
  ❌ Manual XML configuration
  ❌ Manual server setup (Tomcat, Jetty)
  ❌ Manual dependency version management
  ❌ 100s of lines of boilerplate config

Spring Boot:
  ✅ Auto-configuration (detects what's on classpath)
  ✅ Embedded server (Tomcat built in)
  ✅ Starter dependencies (curated, compatible versions)
  ✅ Minimal configuration needed
```

### A Spring Boot Application Anatomy

```java
// This ONE annotation does everything
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
        // 1. Creates Spring ApplicationContext
        // 2. Starts embedded Tomcat server
        // 3. Scans all @Component, @Service, @Repository, @Controller classes
        // 4. Runs auto-configuration
    }
}
```

`@SpringBootApplication` is a combination of:
| Annotation | What it does |
|---|---|
| `@SpringBootConfiguration` | Marks as configuration class |
| `@EnableAutoConfiguration` | Enables Spring Boot's auto-config magic |
| `@ComponentScan` | Scans current package + sub-packages for beans |

### Auto-Configuration — The Magic

When you add `spring-ai-starter-model-openai` to `pom.xml`:

```
Spring Boot detects OpenAI library on classpath
        ↓
OpenAiAutoConfiguration runs
        ↓
Reads spring.ai.openai.api-key from application.yml
        ↓
Creates OpenAiChatModel bean
        ↓
Creates ChatClient.Builder bean
        ↓
You just inject ChatClient.Builder — no manual setup!
```

---

<a id="dependency-injection--ioc"></a>
## 3. Dependency Injection & IoC

This is the **most important Spring concept**. Every Spring AI example uses it.

### What is IoC (Inversion of Control)?

```
WITHOUT IoC — You create dependencies yourself:
  ChatController creates → ChatService creates → ChatClient creates → OpenAiChatModel

WITH IoC — Spring creates and "injects" dependencies:
  Spring creates OpenAiChatModel
  Spring creates ChatClient (using ChatModel)
  Spring creates ChatService (using ChatClient)
  Spring creates ChatController (using ChatService)
  Spring gives you the ready-to-use ChatController
```

### Three Ways to Inject Dependencies

#### 1. Constructor Injection (✅ RECOMMENDED)
```java
@RestController
public class ChatController {

    private final ChatClient chatClient;

    // Spring sees this constructor and injects ChatClient.Builder automatically
    public ChatController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }
}
```
**Why preferred:** Makes dependencies explicit, enables immutability (`final`), easier to test.

#### 2. Field Injection (⚠️ Avoid)
```java
@RestController
public class ChatController {

    @Autowired
    private ChatClient.Builder builder;  // ❌ Can't be final, harder to test
}
```

#### 3. Setter Injection (Rarely used)
```java
@RestController
public class ChatController {
    private ChatClient chatClient;

    @Autowired
    public void setChatClient(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }
}
```

### Spring Stereotypes (Bean Annotations)

| Annotation | Used on | Purpose |
|---|---|---|
| `@Component` | Any class | Generic Spring-managed bean |
| `@Service` | Business logic | Service layer |
| `@Repository` | Data access | Database layer |
| `@RestController` | HTTP endpoints | REST API layer |
| `@Configuration` | Config classes | Bean definitions |
| `@Bean` | Methods in `@Configuration` | Manual bean creation |

```java
// Example of full DI chain in Spring AI context
@Configuration
public class AiConfig {

    @Bean
    public ChatClient chatClient(ChatClient.Builder builder) {
        return builder
            .defaultSystem("You are a helpful Java assistant.")
            .build();
    }
}

@Service
public class ChatService {

    private final ChatClient chatClient;

    public ChatService(ChatClient chatClient) {  // Spring injects the @Bean above
        this.chatClient = chatClient;
    }

    public String askQuestion(String question) {
        return chatClient.prompt().user(question).call().content();
    }
}

@RestController
@RequestMapping("/api")
public class ChatController {

    private final ChatService chatService;

    public ChatController(ChatService chatService) {  // Spring injects the @Service above
        this.chatService = chatService;
    }

    @GetMapping("/ask")
    public String ask(@RequestParam String q) {
        return chatService.askQuestion(q);
    }
}
```

---

<a id="spring-mvc--rest-apis"></a>
## 4. Spring MVC & REST APIs

Spring AI outputs are served through REST APIs. You must be comfortable building them.

### Key Annotations

| Annotation | Description | Example |
|---|---|---|
| `@RestController` | Marks class as REST controller | `@RestController` |
| `@RequestMapping` | Base URL path for the class | `@RequestMapping("/api/chat")` |
| `@GetMapping` | Handle GET requests | `@GetMapping("/ask")` |
| `@PostMapping` | Handle POST requests | `@PostMapping("/chat")` |
| `@RequestParam` | Query parameter from URL | `?message=hello` |
| `@RequestBody` | JSON body of the request | `{ "message": "hello" }` |
| `@PathVariable` | Part of the URL path | `/chat/{id}` |
| `@ResponseBody` | Return value as JSON/text | Auto with `@RestController` |

### Complete REST Controller Example

```java
@RestController
@RequestMapping("/api/chat")
public class ChatController {

    private final ChatClient chatClient;

    public ChatController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    // GET /api/chat/simple?message=Hello
    @GetMapping("/simple")
    public String simpleChat(@RequestParam String message) {
        return chatClient.prompt().user(message).call().content();
    }

    // POST /api/chat/ask   Body: {"message": "Hello"}
    @PostMapping("/ask")
    public ChatResponse askChat(@RequestBody ChatRequest request) {
        String answer = chatClient.prompt()
            .user(request.message())
            .call()
            .content();
        return new ChatResponse(answer);
    }

    // GET /api/chat/user/42?topic=Spring
    @GetMapping("/user/{userId}")
    public String userChat(@PathVariable Long userId,
                           @RequestParam String topic) {
        return chatClient.prompt()
            .user("Tell me about " + topic + " for user " + userId)
            .call()
            .content();
    }

    // DTOs as records
    record ChatRequest(String message) {}
    record ChatResponse(String answer) {}
}
```

### HTTP Status Codes You'll Use

| Code | Meaning | When |
|---|---|---|
| `200 OK` | Success | Default for GET |
| `201 Created` | Resource created | POST that creates data |
| `400 Bad Request` | Invalid input | Validation failure |
| `401 Unauthorized` | Not authenticated | Missing/bad API key |
| `429 Too Many Requests` | Rate limit hit | Too many LLM calls |
| `500 Internal Server Error` | Server crash | Bug or LLM API failure |

---

<a id="spring-data-jpa"></a>
## 5. Spring Data JPA

Spring AI stores chat history and documents in databases. Understanding Spring Data JPA helps.

### Core Concepts

```
JPA (Jakarta Persistence API) = Standard for Java ORM (Object-Relational Mapping)
Hibernate = The JPA implementation (under the hood)
Spring Data JPA = Spring's abstraction on top of JPA (even easier)
```

### Entity (Maps Java class → Database Table)

```java
@Entity
@Table(name = "chat_history")
public class ChatHistory {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "session_id")
    private String sessionId;

    @Column(columnDefinition = "TEXT")
    private String userMessage;

    @Column(columnDefinition = "TEXT")
    private String aiResponse;

    @CreationTimestamp
    private LocalDateTime createdAt;

    // Constructors, getters, setters
}
```

### Repository (Auto-generates SQL queries)

```java
// Spring Data JPA auto-implements all CRUD operations
@Repository
public interface ChatHistoryRepository extends JpaRepository<ChatHistory, Long> {

    // Spring auto-generates: SELECT * FROM chat_history WHERE session_id = ?
    List<ChatHistory> findBySessionId(String sessionId);

    // Spring auto-generates: SELECT * FROM chat_history WHERE session_id = ? ORDER BY created_at DESC
    List<ChatHistory> findBySessionIdOrderByCreatedAtDesc(String sessionId);

    // Custom JPQL query
    @Query("SELECT c FROM ChatHistory c WHERE c.sessionId = :sessionId AND c.createdAt > :since")
    List<ChatHistory> findRecentHistory(@Param("sessionId") String sessionId,
                                        @Param("since") LocalDateTime since);
}
```

### application.yml for Database

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/springai_db
    username: postgres
    password: ${DB_PASSWORD}
    driver-class-name: org.postgresql.Driver

  jpa:
    hibernate:
      ddl-auto: update          # Auto-create/update tables
    show-sql: true              # Log SQL queries (useful for learning)
    properties:
      hibernate:
        format_sql: true
```

---

<a id="configuration--applicationyml"></a>
## 6. Configuration — application.yml

Spring Boot reads configuration from `application.yml` or `application.properties`. Spring AI is entirely configured here.

### YAML Basics

```yaml
# YAML uses indentation (spaces, NOT tabs) for hierarchy

# Simple key-value
server:
  port: 8080

# Nested properties
spring:
  application:
    name: my-app
  datasource:
    url: jdbc:postgresql://localhost/db

# Lists
allowed-origins:
  - http://localhost:3000
  - https://myapp.com

# Using environment variables (${VAR_NAME})
spring:
  ai:
    openai:
      api-key: ${OPENAI_API_KEY}        # Required — must be set
      model: ${AI_MODEL:gpt-4o-mini}    # Optional — defaults to gpt-4o-mini
```

### Multi-Environment Configuration

```
src/main/resources/
├── application.yml           ← Common config (all environments)
├── application-dev.yml       ← Development overrides
├── application-prod.yml      ← Production overrides
└── application-test.yml      ← Test overrides
```

```yaml
# application.yml (common)
spring:
  application:
    name: spring-ai-app

logging:
  level:
    root: INFO

---
# application-dev.yml (development)
spring:
  ai:
    openai:
      api-key: ${OPENAI_API_KEY}
      chat:
        options:
          model: gpt-4o-mini    # Cheaper model in dev

logging:
  level:
    org.springframework.ai: DEBUG    # Verbose logging in dev

---
# application-prod.yml (production)
spring:
  ai:
    openai:
      api-key: ${OPENAI_API_KEY}
      chat:
        options:
          model: gpt-4o          # Full model in production

logging:
  level:
    root: WARN
```

**Activate a profile:**
```powershell
# Run with dev profile
mvn spring-boot:run -Dspring-boot.run.profiles=dev

# Or set environment variable
$env:SPRING_PROFILES_ACTIVE = "dev"
```

### Reading Custom Properties

```yaml
# application.yml
myapp:
  ai:
    max-tokens: 500
    system-prompt: "You are a helpful assistant."
    allowed-topics:
      - Spring Boot
      - Java
      - AI
```

```java
@ConfigurationProperties(prefix = "myapp.ai")
@Component
public class AiProperties {
    private int maxTokens;
    private String systemPrompt;
    private List<String> allowedTopics;
    // getters, setters
}

// Inject anywhere
@Service
public class ChatService {
    private final AiProperties props;

    public ChatService(AiProperties props) {
        this.props = props;
    }

    public String chat(String msg) {
        // Use props.getSystemPrompt(), props.getMaxTokens() etc.
    }
}
```

---

<a id="maven--gradle-build-tools"></a>
## 7. Maven & Gradle Build Tools

Spring AI uses Maven or Gradle to manage dependencies. Most Spring AI examples use Maven.

### Maven — Key Commands

```powershell
# Compile the project
mvn compile

# Run tests
mvn test

# Package as JAR
mvn package

# Run the Spring Boot app
mvn spring-boot:run

# Download dependencies (skip tests)
mvn dependency:resolve -DskipTests

# Clean build output and rebuild
mvn clean install

# Check for dependency conflicts
mvn dependency:tree
```

### Maven `pom.xml` Structure for Spring AI

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <!-- 1. Project identity -->
    <groupId>com.example</groupId>
    <artifactId>spring-ai-app</artifactId>
    <version>1.0.0</version>

    <!-- 2. Parent — handles Spring Boot & plugin versions -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.4.1</version>
    </parent>

    <!-- 3. Properties — define reusable values -->
    <properties>
        <java.version>21</java.version>
        <spring-ai.version>1.0.0</spring-ai.version>
    </properties>

    <!-- 4. BOM — Spring AI version management -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.ai</groupId>
                <artifactId>spring-ai-bom</artifactId>
                <version>${spring-ai.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!-- 5. Your actual dependencies -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-starter-model-openai</artifactId>
        </dependency>
    </dependencies>
</project>
```

### Dependency Scopes

| Scope | Available At | Packaged In JAR |
|---|---|---|
| `compile` (default) | Everywhere | Yes |
| `test` | Tests only | No |
| `provided` | Compile + test | No (server provides it) |
| `runtime` | Runtime + test | Yes |

---

<a id="rest-clients--postman--httpie"></a>
## 8. REST Clients — Postman & HTTPie

You need to test your Spring AI REST endpoints.

### Postman (GUI — Recommended for Beginners)

1. Download from [postman.com](https://postman.com)
2. Create a new **Collection** called "Spring AI Tests"
3. Add a **GET request**: `http://localhost:8080/api/chat?message=Hello`
4. Click **Send** → See AI response in the body panel

**For POST with JSON body:**
- Method: `POST`
- URL: `http://localhost:8080/api/chat/ask`
- Headers: `Content-Type: application/json`
- Body (raw JSON):
  ```json
  {
    "message": "What is Spring AI?"
  }
  ```

### PowerShell / curl Commands

```powershell
# GET request
Invoke-WebRequest "http://localhost:8080/api/chat?message=Hello" | Select-Object -ExpandProperty Content

# POST with JSON body (PowerShell)
$body = '{"message": "What is Spring AI?"}'
Invoke-WebRequest -Uri "http://localhost:8080/api/chat/ask" `
    -Method POST `
    -ContentType "application/json" `
    -Body $body | Select-Object -ExpandProperty Content

# Using curl (if installed)
curl -X GET "http://localhost:8080/api/chat?message=Hello"
curl -X POST http://localhost:8080/api/chat/ask `
     -H "Content-Type: application/json" `
     -d '{"message": "What is Spring AI?"}'
```

---

<a id="aiml-concepts-for-spring-ai"></a>
## 9. AI/ML Concepts for Spring AI

You don't need to know the math — but understanding these concepts will make Spring AI's APIs make sense.

---

<a id="what-is-generative-ai"></a>
## 10. What is Generative AI?

### Traditional AI vs Generative AI

```
Traditional AI (Discriminative):
  Input: Image of a cat
  Output: "This is a cat" (classification)
  → Recognizes and categorizes existing patterns

Generative AI:
  Input: "Write a poem about a cat"
  Output: "A feline friend, with eyes so bright..." (new content)
  → Creates NEW content that didn't exist before
```

### Types of Generative AI

| Type | What it generates | Examples |
|---|---|---|
| **LLMs** | Text | GPT-4, Claude, Gemini, LLaMA |
| **Image Models** | Images | DALL·E, Stable Diffusion, Midjourney |
| **Audio Models** | Speech, music | Whisper (STT), ElevenLabs (TTS) |
| **Code Models** | Code | GitHub Copilot, CodeLlama |
| **Multimodal** | Text + Image + Audio | GPT-4o, Gemini 1.5 Pro |

### How does text generation work (conceptually)?

```
You type: "The capital of France is"

Model thinks:
  "The" → P("capital") = 0.3, P("cat") = 0.01, P("quick") = 0.05
  "capital" → P("of") = 0.8, P("is") = 0.1
  "of" → P("France") = 0.6, P("England") = 0.2
  "France" → P("is") = 0.9
  "is" → P("Paris") = 0.95 ← Highest probability!

Output: "Paris"
```

It's predicting the **most likely next word/token**, over and over. This is why LLMs are sometimes called "fancy autocomplete."

---

<a id="large-language-models-llms"></a>
## 11. Large Language Models (LLMs)

### What makes an LLM "Large"?

| Model | Parameters | Size |
|---|---|---|
| GPT-2 (2019) | 1.5 Billion | "Small" at the time |
| LLaMA 3.1 8B | 8 Billion | Small/fast |
| GPT-4 (estimated) | ~1 Trillion | Very large |
| Gemini Ultra | ~1 Trillion | Very large |

More parameters = more knowledge + better reasoning (usually).

### LLM Providers Used in Spring AI

```
┌─────────────────────────────────────────────────────┐
│                  CLOUD PROVIDERS                    │
│                                                     │
│  OpenAI       → GPT-4o, GPT-4o-mini, o1            │
│  Google       → Gemini 1.5 Pro, Gemini 1.5 Flash   │
│  Anthropic    → Claude 3.5 Sonnet, Claude 3 Opus   │
│  Mistral AI   → Mistral Large, Mistral 7B           │
│  Azure OpenAI → OpenAI models via Microsoft Azure   │
│  AWS Bedrock  → Multiple models via Amazon          │
├─────────────────────────────────────────────────────┤
│                  LOCAL (FREE)                       │
│                                                     │
│  Ollama       → Run LLaMA, Mistral, Gemma locally  │
└─────────────────────────────────────────────────────┘
```

### Chat vs Completion vs Embedding Models

| Model Type | Input | Output | Use Case |
|---|---|---|---|
| **Chat Model** | Conversation (messages) | Text response | Chatbots, Q&A |
| **Completion Model** | Text prefix | Continued text | Text generation |
| **Embedding Model** | Text | Numbers (vector) | Semantic search, RAG |
| **Image Model** | Text prompt | Image | Image generation |

---

<a id="tokens-temperature--parameters"></a>
## 12. Tokens, Temperature & Parameters

These are the knobs you'll tune constantly in Spring AI.

### Tokens

A **token** ≈ 4 characters ≈ ¾ of a word.

```
"Hello World"    = 2 tokens
"Spring AI"      = 2 tokens
"Unbelievable"   = 3 tokens (un-believ-able)
```

**Why tokens matter:**
- API **cost** is measured in tokens (input + output)
- Models have a **context window** (max tokens they can process)
  - GPT-4o: 128,000 tokens ≈ 300 pages of text
  - LLaMA 3.2: 128,000 tokens
- `maxTokens` limits how long the AI's response can be

```yaml
# application.yml — token configuration
spring:
  ai:
    openai:
      chat:
        options:
          max-tokens: 500    # Limit response to ~375 words max
```

### Temperature

Controls **randomness/creativity** of the AI's output.

```
Temperature = 0.0  →  Always picks the MOST likely next token
                       Deterministic, focused, repetitive
                       Use for: factual Q&A, code generation

Temperature = 0.7  →  Balanced creativity (DEFAULT)
                       Good for: general chatbots

Temperature = 1.0  →  High randomness
                       Creative, varied, sometimes weird
                       Use for: story writing, brainstorming

Temperature = 2.0  →  Very random, often incoherent
                       Rarely useful
```

```java
// Setting temperature in Spring AI
ChatOptions options = OpenAiChatOptions.builder()
    .temperature(0.0)   // Deterministic — good for code generation
    .build();

chatClient.prompt()
    .user("Write a Java method to sort a list")
    .options(options)
    .call()
    .content();
```

### Top-P (Nucleus Sampling)

Alternative to temperature. Instead of setting randomness directly, it sets the **cumulative probability threshold** of tokens to consider.

```
Top-P = 0.1 → Only consider the top 10% most likely tokens (conservative)
Top-P = 0.9 → Consider tokens covering 90% of probability mass (diverse)
Top-P = 1.0 → Consider all tokens (same as no restriction)
```

> 💡 **Rule of thumb**: Use EITHER temperature OR top-p, not both. Spring AI defaults work well — don't touch these until you're advanced.

### Max Tokens

```java
// Max tokens = maximum length of the AI response
ChatOptions options = OpenAiChatOptions.builder()
    .maxTokens(100)    // Response will be cut off at 100 tokens (~75 words)
    .build();
```

### Frequency Penalty & Presence Penalty

| Parameter | Range | Effect |
|---|---|---|
| `frequencyPenalty` | -2.0 to 2.0 | Reduces repetition of already-used words |
| `presencePenalty` | -2.0 to 2.0 | Encourages talking about new topics |

> 💡 **For beginners**: Leave these at 0 (default). They matter for advanced use cases like long-form content generation.

---

<a id="embeddings"></a>
## 13. Embeddings

This is one of the most important concepts for Spring AI's RAG feature.

### What is an Embedding?

An **embedding** converts text into a list of numbers (a vector) that captures the **semantic meaning** of the text.

```
"dog"           → [0.2, -0.5, 0.8, 0.1, ...]  (1536 numbers for OpenAI)
"puppy"         → [0.19, -0.48, 0.79, 0.11, ...]  ← VERY similar!
"cat"           → [0.15, -0.3, 0.6, 0.4, ...]   ← Somewhat similar
"Java Spring"   → [-0.3, 0.7, -0.2, 0.9, ...]   ← Very different
```

Words/sentences with similar **meaning** → similar vectors → close in "vector space."

### Why Embeddings Matter (Visual)

```
                 Vector Space (simplified to 2D)

    dog ●   ● puppy
        ↑ Similar meaning = close together
    cat ●

    ↓ Different meaning = far apart

    Java ●    ● Spring Boot
```

### Cosine Similarity — How "Similar" is Measured

```
Similarity = 1.0  → Identical meaning
Similarity = 0.8  → Very similar
Similarity = 0.5  → Somewhat related
Similarity = 0.0  → Completely different
Similarity = -1.0 → Opposite meaning
```

### Embeddings in Spring AI

```java
// Generate an embedding from text
@Autowired
private EmbeddingModel embeddingModel;

// Single text → vector of floats
float[] vector = embeddingModel.embed("What is Spring AI?");
System.out.println("Vector dimensions: " + vector.length); // 1536 for OpenAI

// Multiple texts → list of vectors
List<float[]> vectors = embeddingModel.embed(List.of(
    "Spring AI is a framework",
    "I love Java programming",
    "Dogs are great pets"
));
```

### Popular Embedding Models

| Model | Provider | Dimensions | Use Case |
|---|---|---|---|
| `text-embedding-3-small` | OpenAI | 1536 | General, cost-effective |
| `text-embedding-3-large` | OpenAI | 3072 | Higher accuracy |
| `nomic-embed-text` | Ollama (local) | 768 | Free, runs locally |
| `mxbai-embed-large` | Ollama (local) | 1024 | Best local option |

---

<a id="vector-databases"></a>
## 14. Vector Databases

Vector databases store embeddings and allow extremely fast **similarity search**.

### How Vector Search Works

```
STEP 1 — Ingest (done once):
  "Spring AI is a framework"  →  embed  →  [0.2, 0.8, ...]  →  Store in DB
  "Java needs JDK 17+"        →  embed  →  [0.5, 0.3, ...]  →  Store in DB
  "Docker helps deployment"   →  embed  →  [-0.1, 0.9, ...] →  Store in DB

STEP 2 — Query (every search):
  User asks: "What version of Java do I need?"
                    ↓ embed query
         query vector: [0.48, 0.31, ...]
                    ↓ similarity search
  MATCH FOUND: "Java needs JDK 17+" (similarity: 0.94) ← 🎯
  NO MATCH:    "Docker helps deployment" (similarity: 0.21)
```

### Vector DBs Supported by Spring AI

| Vector Store | Type | Best For |
|---|---|---|
| **PGVector** | PostgreSQL extension | Teams already using PostgreSQL |
| **Chroma** | Standalone, Python-based | Development/prototyping |
| **Pinecone** | Managed cloud | Production, serverless |
| **Weaviate** | Managed/self-hosted | Feature-rich production apps |
| **Qdrant** | Rust-based, fast | High-performance apps |
| **Redis** | In-memory | Low-latency apps |
| **SimpleVectorStore** | In-memory (Spring AI) | Testing only — data lost on restart |

### Setting Up Chroma with Docker (for learning)

```powershell
# Pull and run Chroma locally
docker run -p 8000:8000 chromadb/chroma

# Spring AI config for Chroma
```
```yaml
spring:
  ai:
    vectorstore:
      chroma:
        client:
          host: http://localhost
          port: 8000
```

---

<a id="rag--retrieval-augmented-generation"></a>
## 15. RAG — Retrieval-Augmented Generation

RAG is the technique that makes AI answer questions from **your own data** (PDFs, databases, documents).

### The Problem RAG Solves

```
PROBLEM: LLMs only know what they were trained on.

User: "What does our company refund policy say?"
AI:   "I don't have information about your company's policies."  ❌

       OR worse:

AI:   "I believe your refund policy is 30 days..." (HALLUCINATION) ❌
```

### How RAG Works

```
┌──────────────────────────────────────────────────┐
│                 INGESTION (One time)             │
│                                                  │
│  Your PDF  →  Split into chunks  →  Embed each  │
│                chunk                  chunk      │
│                                      ↓           │
│                               Store in Vector DB │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│              RETRIEVAL (Every query)             │
│                                                  │
│  User Question                                   │
│       ↓                                          │
│  Embed question → Search Vector DB               │
│       ↓                                          │
│  Find top-K most similar document chunks         │
│       ↓                                          │
│  Build prompt: "Context: [chunks]\n              │
│                Question: [user question]"        │
│       ↓                                          │
│  Send to LLM → Get accurate, grounded answer    │
└──────────────────────────────────────────────────┘
```

### RAG in Spring AI (Preview — covered fully in Phase 5)

```java
// INGESTION: Load PDF, chunk it, embed, store
@Bean
public CommandLineRunner ingestDocuments(VectorStore vectorStore) {
    return args -> {
        var pdfReader = new PagePdfDocumentReader("classpath:company-policy.pdf");
        var splitter = new TokenTextSplitter();
        vectorStore.accept(splitter.apply(pdfReader.get()));
    };
}

// RETRIEVAL: Query using RAG advisor
ChatClient ragClient = ChatClient.builder(chatModel)
    .defaultAdvisors(new QuestionAnswerAdvisor(vectorStore))
    .build();

String answer = ragClient.prompt()
    .user("What is the refund policy?")
    .call()
    .content();
// Now AI answers from YOUR document! ✅
```

---

<a id="tools-to-install"></a>
## 16. Tools to Install

Get your development environment ready before Phase 1.

### Step 1: Install JDK 21

```powershell
# Option A: Using winget (recommended for Windows)
winget install Microsoft.OpenJDK.21

# Verify installation
java -version
# Expected: openjdk version "21.x.x" ...

javac -version
# Expected: javac 21.x.x
```

### Step 2: Install IntelliJ IDEA

```powershell
# Community Edition (Free)
winget install JetBrains.IntelliJIDEA.Community

# Ultimate Edition (Paid, 30-day trial)
winget install JetBrains.IntelliJIDEA.Ultimate
```

**Essential IntelliJ Plugins for Spring AI:**
- **Spring Boot** (bundled in Ultimate, install in Community)
- **Lombok** (reduces boilerplate)
- **HTTP Client** (test REST APIs without Postman)
- **Rainbow Brackets** (code readability)

**Key IntelliJ Shortcuts (Windows):**
| Shortcut | Action |
|---|---|
| `Ctrl+Shift+F10` | Run current file |
| `Shift+F10` | Re-run last configuration |
| `Ctrl+B` | Go to declaration |
| `Alt+Enter` | Quick fix / Show suggestions |
| `Ctrl+P` | Show method parameters |
| `Ctrl+Alt+L` | Format code |

### Step 3: Install Git

```powershell
winget install Git.Git

# Verify
git --version

# Configure (one time setup)
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

### Step 4: Install Docker Desktop

Docker is needed to run local vector databases (Chroma, PGVector) without installing them natively.

```powershell
winget install Docker.DockerDesktop

# Verify (after restarting)
docker --version
docker run hello-world
```

**Docker commands you'll use:**
```powershell
# Run PostgreSQL with PGVector extension (for RAG in Phase 5)
docker run -d `
  --name pgvector `
  -e POSTGRES_USER=postgres `
  -e POSTGRES_PASSWORD=password `
  -e POSTGRES_DB=springai `
  -p 5432:5432 `
  pgvector/pgvector:pg16

# Run Chroma vector DB
docker run -d --name chroma -p 8000:8000 chromadb/chroma

# Useful Docker commands
docker ps              # List running containers
docker stop pgvector   # Stop a container
docker start pgvector  # Start a stopped container
docker logs pgvector   # View container logs
```

### Step 5: Install Ollama (Free AI Models — No API Key!)

```powershell
winget install Ollama.Ollama

# Pull a model (downloads ~4GB — do on good internet)
ollama pull llama3.2         # Meta's LLaMA 3.2 (recommended)
ollama pull nomic-embed-text # Embedding model (for RAG)

# Test it works
ollama run llama3.2
# Type "Hello" — you should get a response
# Type /bye to exit

# List installed models
ollama list
```

### Step 6: Install Postman

```powershell
winget install Postman.Postman
```

### Summary: Tools Checklist

```
[ ] JDK 21 installed — java -version works
[ ] JAVA_HOME environment variable set
[ ] IntelliJ IDEA installed
[ ] Git installed and configured
[ ] Docker Desktop installed and running
[ ] Ollama installed with llama3.2 model pulled
[ ] Postman installed
```

**Set JAVA_HOME (if not set automatically):**
```powershell
# Find where Java is installed
where.exe java

# Set JAVA_HOME (replace path as needed)
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Microsoft\jdk-21.0.x.x-hotspot", "User")

# Add to PATH
$path = [System.Environment]::GetEnvironmentVariable("PATH", "User")
[System.Environment]::SetEnvironmentVariable("PATH", "$path;%JAVA_HOME%\bin", "User")

# Restart terminal and verify
java -version
```

---

<a id="phase-0-checklist"></a>
## 17. Phase 0 Checklist

### Java 17+ Modern Features
- [ ] Can write and use Java Records
- [ ] Understand Text Blocks and use them for prompts
- [ ] Comfortable with `var` keyword
- [ ] Understand sealed classes (for reading Spring AI source)

### Spring Boot Fundamentals
- [ ] Can create a Spring Boot project from start.spring.io
- [ ] Understand `@SpringBootApplication` and auto-configuration
- [ ] Can build a REST API with `@RestController`, `@GetMapping`, `@PostMapping`
- [ ] Understand `@RequestParam`, `@RequestBody`, `@PathVariable`

### Dependency Injection
- [ ] Understand Constructor Injection (preferred pattern)
- [ ] Know the difference between `@Component`, `@Service`, `@Repository`
- [ ] Can wire multiple beans together

### Configuration
- [ ] Can write YAML configuration
- [ ] Understand environment variables in config (`${VAR_NAME}`)
- [ ] Know how Spring profiles work (`application-dev.yml`)

### Build Tools
- [ ] Can run `mvn spring-boot:run`
- [ ] Understand `pom.xml` structure
- [ ] Know what `<dependencyManagement>` / BOM is

### AI/ML Concepts
- [ ] Understand what Generative AI is
- [ ] Know what an LLM is and how it generates text
- [ ] Understand tokens and why they matter for cost
- [ ] Know what temperature does (creativity dial)
- [ ] Understand embeddings (text → numbers → similarity)
- [ ] Know what a vector database is and why it's needed
- [ ] Can explain RAG at a high level

### Tools
- [ ] JDK 21 installed and working
- [ ] IntelliJ IDEA set up with Spring project
- [ ] Docker Desktop running
- [ ] Ollama installed with `llama3.2` model
- [ ] Postman installed
- [ ] Git configured

---

### 🏁 Phase 0 Milestone: "Hello Spring Boot"

Before moving to Phase 1, build this small project to confirm your setup works:

```
Create a Spring Boot app with these endpoints:

GET /hello
→ Returns: "Hello, Spring Boot!"

GET /info
→ Returns: { "java": "21", "springBoot": "3.x", "status": "ready" }

POST /echo
Body: {"message": "test"}
→ Returns: {"you said": "test", "length": 4}
```

If this runs without errors, you're ready for **Phase 1: Introduction to Spring AI**. 🎉

---

### ➡️ What's Next

- 📄 **Phase 0 done?** → Move to [Phase 1: Introduction to Spring AI](./Phase1_Introduction_to_Spring_AI.md)
- 📋 **Track your progress** → [Spring AI Complete Roadmap](./Spring_AI_Roadmap.md)
