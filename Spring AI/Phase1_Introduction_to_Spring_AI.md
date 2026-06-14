# 🌱 Phase 1: Introduction to Spring AI — Study Notes

> **Phase**: 1 of 8 | **Estimated Time**: 1 Week
> **Prerequisites**: Spring Boot 3.x basics, Java 17+
> **Goal**: Understand what Spring AI is, why it exists, and build your first working AI-powered Spring Boot app.

---

## 📋 Table of Contents

1. [What is Spring AI?](#what-is-spring-ai)
2. [Why Spring AI? The Problem it Solves](#why-spring-ai-the-problem-it-solves)
3. [Spring AI vs Calling LLM APIs Directly](#spring-ai-vs-calling-llm-apis-directly)
4. [Spring AI vs LangChain](#spring-ai-vs-langchain)
5. [Spring AI Architecture Overview](#spring-ai-architecture-overview)
6. [Key Terminology](#key-terminology)
7. [Setting Up Your First Project](#setting-up-your-first-project)
8. [Project Structure Explained](#project-structure-explained)
9. [Understanding the BOM (Bill of Materials)](#understanding-the-bom-bill-of-materials)
10. [Hello AI — First Working Code](#hello-ai--first-working-code)
11. [Running & Testing](#running--testing)
12. [Common Beginner Mistakes](#common-beginner-mistakes)
13. [Phase 1 Summary & Checklist](#phase-1-summary--checklist)

---

<a id="what-is-spring-ai"></a>
## 1. What is Spring AI?

### Simple Definition
> **Spring AI** is a framework built on top of Spring Boot that makes it easy to integrate AI/LLM capabilities (like ChatGPT, Gemini, Claude) into your Java applications — the "Spring way."

Think of it like this:
- **Spring Data JPA** → makes database access easy
- **Spring Security** → makes authentication easy
- **Spring AI** → makes AI/LLM integration easy

### Official Definition
Spring AI is an application framework for AI engineering. It applies core Spring design principles (portability, modularity) to the AI domain — so you can swap AI providers without changing your business logic.

### When was it Created?
- Started as a Spring experiment in **2023**
- Reached **1.0 GA (General Availability)** in **May 2025**
- Actively maintained by VMware/Broadcom Spring team

### What can Spring AI do?
| Capability | Description |
|---|---|
| **Chat** | Talk to LLMs (GPT-4, Gemini, Claude, LLaMA) |
| **Embeddings** | Convert text → numbers (vectors) for similarity search |
| **Image Generation** | Generate images via DALL·E, Stable Diffusion |
| **Audio** | Transcribe speech (Whisper), Text-to-Speech |
| **RAG** | Answer questions from your own documents |
| **Tool/Function Calling** | Let AI trigger real actions in your app |
| **Vector Stores** | Store and search embeddings in a database |

---

<a id="why-spring-ai-the-problem-it-solves"></a>
## 2. Why Spring AI? The Problem it Solves

### The Problem Without Spring AI

Imagine you want to use OpenAI's ChatGPT in your Java app. Without Spring AI, you'd have to:

```java
// WITHOUT Spring AI — raw HTTP call to OpenAI
HttpClient client = HttpClient.newHttpClient();
String requestBody = """
    {
        "model": "gpt-4o",
        "messages": [{"role": "user", "content": "Hello!"}],
        "temperature": 0.7
    }
    """;

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.openai.com/v1/chat/completions"))
    .header("Authorization", "Bearer " + System.getenv("OPENAI_API_KEY"))
    .header("Content-Type", "application/json")
    .POST(HttpRequest.BodyPublishers.ofString(requestBody))
    .build();

HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

// Now parse the response JSON manually...
ObjectMapper mapper = new ObjectMapper();
JsonNode root = mapper.readTree(response.body());
String answer = root.at("/choices/0/message/content").asText();

System.out.println(answer);
```

**Problems with this approach:**
- ❌ Tightly coupled to OpenAI — switching to Gemini requires rewriting everything
- ❌ Manual JSON serialization/deserialization
- ❌ No retry logic, no error handling
- ❌ No Spring dependency injection
- ❌ No configuration management
- ❌ No streaming support easily
- ❌ No RAG, tool calling, memory — you build everything yourself

### The Solution with Spring AI

```java
// WITH Spring AI — clean, provider-agnostic
@RestController
public class ChatController {

    private final ChatClient chatClient;

    public ChatController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    @GetMapping("/chat")
    public String chat(@RequestParam String message) {
        return chatClient.prompt()
            .user(message)
            .call()
            .content();
    }
}
```

**Benefits:**
- ✅ Switch from OpenAI → Ollama → Gemini by changing ONE config line
- ✅ Automatic JSON handling
- ✅ Built-in retry, error handling
- ✅ Full Spring DI and configuration support
- ✅ Streaming out of the box
- ✅ RAG, tool calling, memory all available

---

<a id="spring-ai-vs-calling-llm-apis-directly"></a>
## 3. Spring AI vs Calling LLM APIs Directly

| Aspect | Direct API Calls | Spring AI |
|---|---|---|
| **Provider switching** | Rewrite entire codebase | Change 1 config line |
| **Code style** | Raw HTTP, manual JSON | Fluent, idiomatic Java |
| **Dependency Injection** | Manual wiring | Spring `@Autowired` / constructor injection |
| **Configuration** | Hard-coded or manual | `application.yml` |
| **Streaming** | Complex implementation | `.stream()` method |
| **Structured output** | Manual JSON parsing | Auto-map to Java records/POJOs |
| **RAG** | Build from scratch | Built-in advisors + VectorStore |
| **Tool calling** | Complex protocol handling | `@Tool` annotation |
| **Testing** | Hard to mock | Mock-friendly, Spring test support |
| **Observability** | Manual logging | Micrometer integration built-in |

---

<a id="spring-ai-vs-langchain"></a>
## 4. Spring AI vs LangChain

LangChain is the most popular AI framework — but it's in **Python**. Spring AI is the **Java equivalent**.

| Aspect | LangChain (Python) | Spring AI (Java) |
|---|---|---|
| **Language** | Python | Java / Kotlin |
| **Maturity** | 2022, very mature | 2023, 1.0 GA in 2025 |
| **Ecosystem** | Massive (ML, data science) | Spring ecosystem (enterprise) |
| **Enterprise fit** | Medium | High (security, txn, DI) |
| **Learning curve** | Python-friendly | Java/Spring-friendly |
| **Deployment** | Flask, FastAPI, etc. | Spring Boot (jar/war/Docker) |
| **LLM support** | 100+ providers | 20+ major providers |
| **Best for** | Data scientists, ML engineers | Java backend developers |

> 💡 **Key Insight**: If you're a Java developer working in enterprise environments — Spring AI is the RIGHT choice. You don't need to learn Python to build AI apps.

### LangChain4j — Another Alternative
There's also **LangChain4j**, a Java port of LangChain. Spring AI vs LangChain4j:
| | Spring AI | LangChain4j |
|---|---|---|
| **Backing** | Spring (VMware/Broadcom) | Community |
| **Integration** | Native Spring Boot | Spring Boot + standalone |
| **Philosophy** | Spring conventions | LangChain patterns |

---

<a id="spring-ai-architecture-overview"></a>
## 5. Spring AI Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Your Spring Boot App                      │
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │  Controller  │───▶│  ChatClient  │───▶│  Advisors    │  │
│  │  (REST API)  │    │  (Main API)  │    │  (Middleware)│  │
│  └──────────────┘    └──────┬───────┘    └──────────────┘  │
│                             │                               │
│                    ┌────────▼────────┐                      │
│                    │   ChatModel     │                       │
│                    │ (Provider API)  │                       │
│                    └────────┬────────┘                      │
└─────────────────────────────┼───────────────────────────────┘
                              │  HTTP/API calls
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
   │   OpenAI     │  │    Ollama    │  │   Gemini     │
   │  (GPT-4o)   │  │  (LLaMA 3)  │  │  (Gemini 2)  │
   └──────────────┘  └──────────────┘  └──────────────┘
```

### Key Layers Explained

| Layer | Class/Interface | Role |
|---|---|---|
| **Your Code** | `@RestController` | Handles HTTP requests |
| **ChatClient** | `ChatClient` | Main entry point, fluent API |
| **Advisors** | `CallAroundAdvisor` | Middleware (logging, memory, RAG) |
| **ChatModel** | `ChatModel` interface | Low-level, provider-specific |
| **AI Provider** | OpenAI, Ollama, etc. | Actual LLM that processes text |

> 💡 **You mostly use `ChatClient`** — it's the high-level API. `ChatModel` is the low-level interface that `ChatClient` uses internally.

---

<a id="key-terminology"></a>
## 6. Key Terminology

Before writing code, understand these terms:

| Term | Definition | Analogy |
|---|---|---|
| **LLM** | Large Language Model — AI that understands and generates text | A very smart autocomplete |
| **Token** | A piece of text (~4 characters or 1 word) | A word fragment |
| **Prompt** | The input you send to the AI | Your question |
| **Completion** | The AI's response | The AI's answer |
| **Temperature** | 0.0 = deterministic/focused, 1.0 = creative/random | Creativity dial |
| **Model** | The specific AI version (e.g., `gpt-4o`) | Like choosing Java 17 vs 21 |
| **API Key** | Secret token to authenticate with AI provider | Like a password |
| **Embedding** | Text converted to a list of numbers (vector) | GPS coordinates for meaning |
| **Streaming** | Receiving AI output word by word, not all at once | Like typing while you watch |
| **System Prompt** | Instructions that define the AI's behavior/role | Job description for the AI |
| **User Message** | What the human says | Your chat message |

---

<a id="setting-up-your-first-project"></a>
## 7. Setting Up Your First Project

### Step 1: Create the Project on start.spring.io

Go to [https://start.spring.io](https://start.spring.io) and configure:

```
Project:     Maven
Language:    Java
Spring Boot: 3.4.x (or latest stable)
Group:       com.example
Artifact:    spring-ai-demo
Name:        spring-ai-demo
Packaging:   Jar
Java:        21
```

**Dependencies to add:**
- ✅ `Spring Web` (for REST APIs)
- ✅ `OpenAI` (under AI category) — OR `Ollama` if you want local/free

Click **Generate** → Download and unzip.

---

### Step 2: Open in IntelliJ IDEA

1. Open IntelliJ → `File > Open` → Select the unzipped folder
2. Wait for Maven to download dependencies (first time takes 2–3 minutes)
3. Verify: You should see `pom.xml` with Spring AI dependencies

---

### Step 3: Configure API Key

Open `src/main/resources/application.yml` (create it if only `.properties` exists):

```yaml
# application.yml

spring:
  application:
    name: spring-ai-demo

  ai:
    openai:
      api-key: ${OPENAI_API_KEY}        # Read from environment variable
      chat:
        options:
          model: gpt-4o-mini            # Cheaper model, great for learning
          temperature: 0.7              # Balanced creativity

server:
  port: 8080
```

> ⚠️ **NEVER hardcode your API key** directly in `application.yml`. Always use environment variables or a `.env` file.

**Setting the environment variable:**

```powershell
# Windows PowerShell — set for current session
$env:OPENAI_API_KEY = "sk-proj-your-actual-key-here"

# To make it permanent (Windows)
[System.Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "sk-proj-...", "User")
```

**For Ollama (Free, No API Key needed):**
```yaml
spring:
  ai:
    ollama:
      base-url: http://localhost:11434
      chat:
        options:
          model: llama3.2
```

---

### Step 4: Getting an OpenAI API Key (if using OpenAI)

1. Go to [https://platform.openai.com](https://platform.openai.com)
2. Sign up / Login
3. Navigate to `API Keys` section
4. Click `Create new secret key`
5. Copy the key (you can only see it ONCE)

> 💡 **For Learning**: Use `gpt-4o-mini` — it's 15x cheaper than `gpt-4o` and works great for all Phase 1 exercises.

---

<a id="project-structure-explained"></a>
## 8. Project Structure Explained

```
spring-ai-demo/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/springaidemo/
│   │   │       ├── SpringAiDemoApplication.java   ← Main class
│   │   │       └── controller/
│   │   │           └── ChatController.java         ← Your REST endpoint
│   │   └── resources/
│   │       └── application.yml                    ← All config here
│   └── test/
│       └── java/
│           └── com/example/springaidemo/
│               └── SpringAiDemoApplicationTests.java
├── pom.xml                                        ← Dependencies
└── .gitignore
```

---

<a id="understanding-the-bom-bill-of-materials"></a>
## 9. Understanding the BOM (Bill of Materials)

### What is a BOM?
A **BOM (Bill of Materials)** is a special POM file that defines **compatible versions** of all related dependencies. Instead of specifying versions for each Spring AI artifact, you declare the BOM once and it manages everything.

### Your `pom.xml` explained:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>spring-ai-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-ai-demo</name>

    <!-- 1. Inherits from Spring Boot parent — handles Spring framework versions -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.4.1</version>
    </parent>

    <!-- 2. Java version -->
    <properties>
        <java.version>21</java.version>
        <spring-ai.version>1.0.0</spring-ai.version>
    </properties>

    <!-- 3. Spring AI BOM — manages ALL Spring AI dependency versions -->
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

    <dependencies>
        <!-- 4. Spring Web — for building REST APIs -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- 5. Spring AI OpenAI Starter — NO version needed! BOM handles it -->
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-starter-model-openai</artifactId>
            <!-- ✅ No <version> tag needed — BOM manages this -->
        </dependency>

        <!-- 6. Spring Boot Test — for unit/integration tests -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### Why use BOM?
| Without BOM | With BOM |
|---|---|
| Specify version for every Spring AI artifact | Specify version ONCE |
| Risk of version conflicts | Guaranteed compatible versions |
| Manual version management | Automatic |

---

<a id="hello-ai--first-working-code"></a>
## 10. Hello AI — First Working Code

### File 1: Main Application Class (already generated)

```java
// src/main/java/com/example/springaidemo/SpringAiDemoApplication.java

package com.example.springaidemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringAiDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringAiDemoApplication.class, args);
    }
}
```

---

### File 2: The Chat Controller

```java
// src/main/java/com/example/springaidemo/controller/ChatController.java

package com.example.springaidemo.controller;

import org.springframework.ai.chat.client.ChatClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class ChatController {

    // Spring AI auto-creates and injects ChatClient.Builder
    private final ChatClient chatClient;

    // Constructor injection — recommended pattern
    public ChatController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    /**
     * Basic chat endpoint
     * GET /api/chat?message=Hello
     */
    @GetMapping("/chat")
    public String chat(@RequestParam String message) {
        return chatClient
            .prompt()            // Start building a prompt
            .user(message)       // Set the user's message
            .call()              // Send to the AI (blocking call)
            .content();          // Extract just the text response
    }
}
```

### How it works step by step:

```
User hits: GET /api/chat?message=What is Spring AI?
                │
                ▼
        ChatController.chat("What is Spring AI?")
                │
                ▼
        chatClient.prompt()       ← Creates a new prompt builder
            .user(message)        ← Adds user message
            .call()               ← Sends HTTP request to OpenAI
            .content()            ← Extracts response text
                │
                ▼
        Returns: "Spring AI is a framework that..."
```

---

### File 3: A More Advanced Controller (with System Prompt)

```java
// Add this to ChatController.java or create a new file

/**
 * Chat with a system prompt — defines AI's role/behavior
 * GET /api/expert-chat?message=What is an embedding?
 */
@GetMapping("/expert-chat")
public String expertChat(@RequestParam String message) {
    return chatClient
        .prompt()
        .system("You are a Spring AI expert who explains concepts clearly " +
                "with practical Java code examples. Keep answers concise.")
        .user(message)
        .call()
        .content();
}

/**
 * Chat with custom model options per request
 * GET /api/creative-chat?message=Write a poem about Java
 */
@GetMapping("/creative-chat")
public String creativeChat(@RequestParam String message) {
    return chatClient
        .prompt()
        .user(message)
        .options(ChatOptionsBuilder.builder()
            .temperature(0.9)     // More creative
            .maxTokens(200)       // Limit response length
            .build())
        .call()
        .content();
}
```

---

### File 4: Streaming Response

```java
import reactor.core.publisher.Flux;
import org.springframework.web.bind.annotation.GetMapping;

/**
 * Stream the response word by word — better UX for long responses
 * GET /api/stream?message=Tell me about Spring Boot
 */
@GetMapping(value = "/stream", produces = "text/event-stream")
public Flux<String> streamChat(@RequestParam String message) {
    return chatClient
        .prompt()
        .user(message)
        .stream()       // ← Returns Flux<String> instead of blocking
        .content();
}
```

> 💡 **Streaming** is important for user experience — instead of waiting 5 seconds for a full response, the user sees words appearing one by one (like ChatGPT does).

---

<a id="running--testing"></a>
## 11. Running & Testing

### Run the Application

```powershell
# Navigate to your project root
cd "d:\All Files\Spring AI\spring-ai-demo"

# Set API key first
$env:OPENAI_API_KEY = "sk-proj-your-key-here"

# Run the app
mvn spring-boot:run
```

You should see:
```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
...
Started SpringAiDemoApplication in 2.345 seconds
```

---

### Test in Browser or Terminal

```powershell
# Test basic chat
Invoke-WebRequest -Uri "http://localhost:8080/api/chat?message=Hello, what is Spring AI?" | Select-Object -ExpandProperty Content

# Or use curl
curl "http://localhost:8080/api/chat?message=What+is+Spring+AI?"
```

**Expected Response:**
```
Spring AI is an application framework for Java developers that simplifies the 
integration of AI capabilities into Spring Boot applications...
```

---

### Test with Postman (Recommended for Beginners)

1. Open Postman
2. Create a new `GET` request
3. URL: `http://localhost:8080/api/chat`
4. Params: Key=`message`, Value=`What is Spring AI?`
5. Click **Send**

---

### Understanding the Auto-Configuration

Spring AI uses **Spring Boot Auto-Configuration** — you don't need to manually create beans!

When you add `spring-ai-starter-model-openai` to `pom.xml` and configure `spring.ai.openai.api-key`, Spring AI automatically creates:

| Bean | Type | Created automatically |
|---|---|---|
| `OpenAiChatModel` | `ChatModel` | ✅ Auto-configured |
| `ChatClient.Builder` | Builder | ✅ Auto-configured |
| `OpenAiEmbeddingModel` | `EmbeddingModel` | ✅ Auto-configured |

You just inject `ChatClient.Builder` — Spring handles the rest!

---

<a id="common-beginner-mistakes"></a>
## 12. Common Beginner Mistakes

### ❌ Mistake 1: Hardcoding API Key

```yaml
# ❌ WRONG — Never do this!
spring:
  ai:
    openai:
      api-key: sk-proj-abc123xyz...  # Exposed in version control!
```

```yaml
# ✅ CORRECT — Use environment variable
spring:
  ai:
    openai:
      api-key: ${OPENAI_API_KEY}
```

---

### ❌ Mistake 2: Injecting ChatModel instead of ChatClient

```java
// ❌ Avoid this for most cases — low-level API
@Autowired
private ChatModel chatModel;  // Too verbose to use directly

// ✅ Prefer this — high-level, fluent API
public ChatController(ChatClient.Builder builder) {
    this.chatClient = builder.build();
}
```

---

### ❌ Mistake 3: Wrong Dependency Name

Spring AI 1.0 changed the artifact names. Use the CORRECT ones:

```xml
<!-- ❌ OLD (pre-1.0 milestone versions) — will NOT work -->
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-openai-spring-boot-starter</artifactId>
</dependency>

<!-- ✅ NEW (1.0 GA) — correct name -->
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
```

---

### ❌ Mistake 4: Forgetting the BOM

```xml
<!-- ❌ Without BOM — you MUST specify version manually, risk of conflicts -->
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
    <version>1.0.0</version>   <!-- Must manage this manually -->
</dependency>

<!-- ✅ With BOM — version managed automatically -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>1.0.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

---

### ❌ Mistake 5: Using Wrong Spring Boot Version

Spring AI 1.0 requires **Spring Boot 3.2+** (which requires **Java 17+**).

```xml
<!-- ✅ Minimum required -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>  <!-- Minimum: 3.2.0 -->
</parent>
```

---

<a id="phase-1-summary--checklist"></a>
## 13. Phase 1 Summary & Checklist

### What You Learned in Phase 1

```
✅ What Spring AI is and the problems it solves
✅ Why Spring AI > raw API calls (portability, DI, structure)
✅ Spring AI vs LangChain — Java vs Python AI frameworks
✅ Spring AI architecture (ChatClient → ChatModel → Provider)
✅ Key terminology (LLM, token, prompt, temperature, embedding)
✅ Project setup with start.spring.io
✅ pom.xml structure with Spring AI BOM
✅ application.yml configuration with API keys
✅ Building a REST endpoint with ChatClient
✅ System prompts, streaming, and custom options
✅ Running and testing with Postman / curl
✅ Common beginner mistakes to avoid
```

---

### 🏁 Phase 1 Milestone Project

Build a simple **"Ask Me Anything" REST API**:

```
Endpoints to implement:
┌─────────────────────────────────────────────────────────┐
│  GET /api/chat?message={your question}                  │
│  → Basic chat response                                  │
│                                                         │
│  GET /api/expert?topic={topic}                         │
│  → Chat with system prompt: "You are a {topic} expert" │
│                                                         │
│  GET /api/stream?message={message}                     │
│  → Streaming response (Server-Sent Events)             │
└─────────────────────────────────────────────────────────┘
```

**Acceptance Criteria:**
- [ ] App starts without errors
- [ ] `/api/chat?message=Hello` returns an AI response
- [ ] System prompt changes the AI's behavior noticeably
- [ ] Streaming endpoint sends response progressively
- [ ] API key is stored in environment variable (not hardcoded)

---

### 🔗 Quick Reference

```yaml
# application.yml — OpenAI setup
spring:
  ai:
    openai:
      api-key: ${OPENAI_API_KEY}
      chat:
        options:
          model: gpt-4o-mini
          temperature: 0.7
```

```xml
<!-- pom.xml — Key dependency -->
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
```

```java
// Minimal working controller
@RestController
public class ChatController {
    private final ChatClient chatClient;

    ChatController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    @GetMapping("/chat")
    String chat(@RequestParam String message) {
        return chatClient.prompt().user(message).call().content();
    }
}
```

---

### ➡️ What's Next — Phase 2: Core Concepts

In Phase 2, you'll go deeper:
- `ChatClient` fluent API in detail
- All message types: `SystemMessage`, `UserMessage`, `AssistantMessage`
- `ChatOptions` — controlling AI behavior precisely
- **Structured Outputs** — map AI responses directly to Java records
- Multi-turn conversations

---

> 📁 **Part of**: [Spring AI Complete Roadmap](./Spring_AI_Roadmap.md)
> ⏭️ **Next**: Phase 2 — Core Concepts
