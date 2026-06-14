# 🧠 Phase 2: Core Concepts — Study Notes

> **Phase**: 2 of 8 | **Estimated Time**: 1–2 Weeks
> **Prerequisites**: Phase 0 (Java 17+, Spring Boot) + Phase 1 (First Spring AI app running)
> **Goal**: Master the core Spring AI APIs — `ChatClient`, Messages, `ChatOptions`, and Structured Outputs.

---

## 📋 Table of Contents

1. [ChatClient vs ChatModel](#chatclient-vs-chatmodel)
2. [ChatClient Builder — Fluent API](#chatclient-builder--fluent-api)
3. [Synchronous Calls](#synchronous-calls)
4. [Streaming Responses](#streaming-responses)
5. [Messages & Roles](#messages--roles)
6. [SystemMessage — Defining AI Behavior](#systemmessage--defining-ai-behavior)
7. [UserMessage — The Human's Input](#usermessage--the-humans-input)
8. [AssistantMessage — The AI's Response](#assistantmessage--the-ais-response)
9. [ToolResponseMessage — Tool Results](#toolresponsemessage--tool-results)
10. [Multi-Turn Conversations](#multi-turn-conversations)
11. [ChatOptions — Controlling AI Behavior](#chatoptions--controlling-ai-behavior)
12. [Options at Builder vs Per-Call Level](#options-at-builder-vs-per-call-level)
13. [Structured Outputs — Entity Mapping](#structured-outputs--entity-mapping)
14. [BeanOutputConverter — Under the Hood](#beanoutputconverter--under-the-hood)
15. [Mapping Lists of Objects](#mapping-lists-of-objects)
16. [Phase 2 Summary & Checklist](#phase-2-summary--checklist)

---

<a id="chatclient-vs-chatmodel"></a>
## 1. ChatClient vs ChatModel

This is the first question every Spring AI beginner asks. Understand this clearly.

### The Relationship

```
ChatClient (High-level API — use this 90% of the time)
    ↓ uses internally
ChatModel (Low-level API — rarely use directly)
    ↓ calls
AI Provider (OpenAI, Ollama, Gemini...)
```

### ChatModel — Low-Level Interface

`ChatModel` is the raw interface that directly communicates with the AI provider.

```java
// ChatModel — verbose, low-level
@Service
public class LowLevelService {

    private final ChatModel chatModel;  // Injected by Spring (OpenAiChatModel, OllamaChatModel, etc.)

    public LowLevelService(ChatModel chatModel) {
        this.chatModel = chatModel;
    }

    public String chat(String userInput) {
        // You must manually build the Prompt object
        Prompt prompt = new Prompt(
            List.of(new UserMessage(userInput)),
            OpenAiChatOptions.builder()
                .model("gpt-4o-mini")
                .temperature(0.7)
                .build()
        );

        // Call returns a ChatResponse object
        ChatResponse response = chatModel.call(prompt);

        // Extract text from the deeply nested response
        return response.getResult().getOutput().getContent();
    }
}
```

### ChatClient — High-Level Fluent API

`ChatClient` is a fluent builder API built on top of `ChatModel`. It handles all the boilerplate.

```java
// ChatClient — clean, fluent, expressive
@Service
public class HighLevelService {

    private final ChatClient chatClient;

    public HighLevelService(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    public String chat(String userInput) {
        return chatClient
            .prompt()
            .user(userInput)
            .call()
            .content();  // ← Directly returns the String!
    }
}
```

### Side-by-Side Comparison

| Feature | `ChatModel` | `ChatClient` |
|---|---|---|
| **Abstraction Level** | Low-level | High-level |
| **Verbosity** | Verbose | Concise, fluent |
| **Default options** | Must set every time | Set once at builder time |
| **Advisors support** | ❌ No | ✅ Yes |
| **Memory/RAG built-in** | ❌ No | ✅ Yes (via Advisors) |
| **Recommended usage** | Library authors | Application developers |
| **When to use** | Building frameworks, custom logic | Building applications |

> 💡 **Rule**: Always prefer `ChatClient` in your Spring AI applications. Use `ChatModel` only if you're building a library or need very low-level control.

---

<a id="chatclient-builder--fluent-api"></a>
## 2. ChatClient Builder — Fluent API

`ChatClient.Builder` is how you configure and create a `ChatClient` instance. Spring auto-provides the builder.

### Basic Builder Pattern

```java
@Configuration
public class AiConfig {

    // Define a ChatClient bean with defaults
    @Bean
    public ChatClient chatClient(ChatClient.Builder builder) {
        return builder
            // Default system prompt — applied to ALL requests from this client
            .defaultSystem("You are a helpful Spring AI assistant.")
            // Default options — applied to ALL requests
            .defaultOptions(OpenAiChatOptions.builder()
                .model("gpt-4o-mini")
                .temperature(0.7)
                .maxTokens(1000)
                .build())
            .build();
    }
}
```

### Injecting and Using the ChatClient

```java
@RestController
@RequestMapping("/api")
public class ChatController {

    private final ChatClient chatClient;

    // Inject the pre-configured ChatClient bean
    public ChatController(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    @GetMapping("/chat")
    public String chat(@RequestParam String message) {
        return chatClient
            .prompt()       // Step 1: Start building the prompt
            .user(message)  // Step 2: Add user message
            .call()         // Step 3: Send to AI (blocking)
            .content();     // Step 4: Extract string response
    }
}
```

### Multiple ChatClient Instances for Different Use Cases

```java
@Configuration
public class AiConfig {

    // ChatClient for general questions
    @Bean("generalChatClient")
    public ChatClient generalChatClient(ChatClient.Builder builder) {
        return builder
            .defaultSystem("You are a helpful general assistant.")
            .build();
    }

    // ChatClient specialized for code help
    @Bean("codeChatClient")
    public ChatClient codeChatClient(ChatClient.Builder builder) {
        return builder
            .defaultSystem("""
                You are an expert Java developer.
                Always provide working code examples.
                Use modern Java 21 features.
                Format code in markdown code blocks.
                """)
            .defaultOptions(OpenAiChatOptions.builder()
                .temperature(0.1)   // Low temperature for deterministic code
                .build())
            .build();
    }

    // ChatClient for creative writing
    @Bean("creativeChatClient")
    public ChatClient creativeChatClient(ChatClient.Builder builder) {
        return builder
            .defaultSystem("You are a creative writer and storyteller.")
            .defaultOptions(OpenAiChatOptions.builder()
                .temperature(1.0)   // High temperature for creativity
                .build())
            .build();
    }
}

// Inject specific clients using @Qualifier
@Service
public class ChatService {

    private final ChatClient generalClient;
    private final ChatClient codeClient;

    public ChatService(
            @Qualifier("generalChatClient") ChatClient generalClient,
            @Qualifier("codeChatClient") ChatClient codeClient) {
        this.generalClient = generalClient;
        this.codeClient = codeClient;
    }
}
```

---

<a id="synchronous-calls"></a>
## 3. Synchronous Calls

Synchronous (blocking) calls wait for the full AI response before returning. The thread is blocked during the call.

### The Full Synchronous Call Chain

```java
chatClient
    .prompt()                    // Returns: PromptSpec
    .system("Be concise")        // Returns: PromptSpec
    .user("What is Java?")       // Returns: PromptSpec
    .call()                      // Returns: CallResponseSpec (sends HTTP request HERE)
    .content()                   // Returns: String
```

### All Ways to Extract the Response

```java
var callSpec = chatClient.prompt().user("Tell me about Spring AI").call();

// Option 1: Get just the text content (most common)
String text = callSpec.content();

// Option 2: Get the full ChatResponse object (all metadata)
ChatResponse fullResponse = callSpec.chatResponse();

// Access metadata from full response
String content = fullResponse.getResult().getOutput().getContent();
String model = fullResponse.getMetadata().getModel();
Long promptTokens = fullResponse.getMetadata().getUsage().getPromptTokens();
Long completionTokens = fullResponse.getMetadata().getUsage().getGenerationTokens();
Long totalTokens = fullResponse.getMetadata().getUsage().getTotalTokens();

// Option 3: Map to a Java entity/record directly
record JavaInfo(String description, String version, List<String> features) {}
JavaInfo info = callSpec.entity(JavaInfo.class);

// Option 4: Map to a list of entities
List<JavaInfo> infos = callSpec.entity(new ParameterizedTypeReference<List<JavaInfo>>() {});
```

### Real-World Example: Checking Token Usage

```java
@GetMapping("/chat-with-usage")
public Map<String, Object> chatWithUsage(@RequestParam String message) {
    ChatResponse response = chatClient
        .prompt()
        .user(message)
        .call()
        .chatResponse();

    return Map.of(
        "answer", response.getResult().getOutput().getContent(),
        "model", response.getMetadata().getModel(),
        "totalTokens", response.getMetadata().getUsage().getTotalTokens()
    );
}
```

---

<a id="streaming-responses"></a>
## 4. Streaming Responses

Streaming returns tokens one by one as they're generated — like watching ChatGPT type. This dramatically improves perceived response time for the user.

### Streaming vs Blocking — Visual Comparison

```
BLOCKING (call()):
  User sends request
  └─── Wait 5 seconds ─────────────────────────┐
                                                ↓
                                         Full response arrives
                                         All at once

STREAMING (stream()):
  User sends request
  └─── "Spring" ── "AI" ── "is" ── "a" ── "framework" ── "that..."
       Word by word, user sees output immediately!
```

### Basic Streaming Example

```java
import reactor.core.publisher.Flux;
import org.springframework.http.MediaType;

@GetMapping(value = "/stream", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public Flux<String> streamChat(@RequestParam String message) {
    return chatClient
        .prompt()
        .user(message)
        .stream()        // Returns Flux<String> instead of blocking
        .content();      // Each item in the Flux is a token/chunk
}
```

> ⚠️ **Important**: The endpoint must declare `produces = MediaType.TEXT_EVENT_STREAM_VALUE` and return `Flux<String>` for streaming to work.

### Testing Streaming in PowerShell

```powershell
# You'll see tokens appearing one by one
Invoke-WebRequest -Uri "http://localhost:8080/api/stream?message=Tell+me+about+Spring+AI" `
    -Headers @{ "Accept" = "text/event-stream" }
```

### Collecting the Full Stream (when needed)

```java
// Collect all streamed tokens into one String
String fullResponse = chatClient
    .prompt()
    .user("Write a poem")
    .stream()
    .content()
    .collectList()               // Collect Flux<String> → Mono<List<String>>
    .map(chunks -> String.join("", chunks))  // Join all chunks
    .block();                    // Block to get the final String (only if needed)
```

### Streaming the Full ChatResponse

```java
// Stream complete ChatResponse objects (includes metadata per chunk)
Flux<ChatResponse> responseFlux = chatClient
    .prompt()
    .user("Explain microservices")
    .stream()
    .chatResponse();

responseFlux.subscribe(chunk -> {
    String token = chunk.getResult().getOutput().getContent();
    System.out.print(token);  // Print each token as it arrives
});
```

---

<a id="messages--roles"></a>
## 5. Messages & Roles

Every interaction with an LLM is structured as a list of **messages**, each with a **role**. Understanding this is fundamental to Spring AI.

### The Message Concept

When you call an LLM, you don't just send text — you send a **conversation history** where each message has a role:

```
[
  { role: "system",    content: "You are a Java expert." },
  { role: "user",      content: "What is a Record?" },
  { role: "assistant", content: "A Record is a concise..." },
  { role: "user",      content: "Can you show an example?" }
]
```

The LLM sees ALL of these messages and generates the next response in context.

### Spring AI Message Types

```java
// Spring AI's sealed interface hierarchy
sealed interface Message permits
    SystemMessage,
    UserMessage,
    AssistantMessage,
    ToolResponseMessage
```

| Message Type | Role | Created By | Purpose |
|---|---|---|---|
| `SystemMessage` | `system` | Developer | Set AI's personality/rules |
| `UserMessage` | `user` | End user | The human's input |
| `AssistantMessage` | `assistant` | AI | AI's previous responses |
| `ToolResponseMessage` | `tool` | System | Results from tool calls |

---

<a id="systemmessage--defining-ai-behavior"></a>
## 6. SystemMessage — Defining AI Behavior

The **system message** is the "job description" you give the AI. It shapes how the AI behaves throughout the entire conversation.

### Key Properties
- Set ONCE at the start (or per request)
- User cannot override it (ideally)
- Not visible to the end user (developer-controlled)
- Accepts dynamic content via `PromptTemplate`

### Ways to Set the System Message

```java
// Method 1: Inline string (simple)
chatClient
    .prompt()
    .system("You are a helpful Java developer assistant.")
    .user(userQuestion)
    .call()
    .content();

// Method 2: Text block (multiline — recommended)
chatClient
    .prompt()
    .system("""
        You are an expert Spring Boot developer with 10 years of experience.
        Rules:
        - Always provide working, compilable Java code
        - Use Java 21 features where appropriate
        - Explain WHY, not just WHAT
        - Keep responses under 500 words unless asked for more
        - Format all code in proper markdown code blocks
        """)
    .user(userQuestion)
    .call()
    .content();

// Method 3: Default system at builder level (applies to ALL requests)
ChatClient client = ChatClient.builder(chatModel)
    .defaultSystem("You are a concise assistant. Be brief.")
    .build();

// Method 4: Dynamic system with variables
chatClient
    .prompt()
    .system(s -> s.param("role", "Spring Security expert")
                  .param("language", "Java"))
    .user(userQuestion)
    .call()
    .content();
// (requires a template like "You are a {role} who codes in {language}.")
```

### System Prompt Strategies

```java
// Strategy 1: Role-based
String rolePrompt = "You are a senior Java architect specialized in microservices.";

// Strategy 2: Output format specification
String formatPrompt = """
    You are a helpful assistant.
    ALWAYS respond in this JSON format:
    {
      "answer": "your answer here",
      "confidence": "high/medium/low",
      "sources": ["source1", "source2"]
    }
    """;

// Strategy 3: Few-shot examples (teach AI with examples)
String fewShotPrompt = """
    You classify Java exceptions as CHECKED or UNCHECKED.

    Examples:
    Exception: IOException → CHECKED
    Exception: NullPointerException → UNCHECKED
    Exception: SQLException → CHECKED
    Exception: ArrayIndexOutOfBoundsException → UNCHECKED

    Respond with only "CHECKED" or "UNCHECKED".
    """;

// Strategy 4: Chain-of-thought (better reasoning)
String chainOfThoughtPrompt = """
    You are a debugging assistant.
    When analyzing a bug, ALWAYS:
    1. Identify what the code DOES
    2. Identify what the code SHOULD do
    3. Find the discrepancy
    4. Suggest a fix with explanation
    Think step by step before answering.
    """;
```

---

<a id="usermessage--the-humans-input"></a>
## 7. UserMessage — The Human's Input

The `UserMessage` represents what the human/end-user is saying.

### Creating User Messages

```java
// Simplest way — string directly in .user()
chatClient.prompt()
    .user("What is Spring AI?")
    .call()
    .content();

// Dynamic user message with variables
chatClient.prompt()
    .user(u -> u.text("Explain {concept} with a {language} code example")
               .param("concept", "dependency injection")
               .param("language", "Java"))
    .call()
    .content();

// Multi-modal user message (text + image) — for vision models like GPT-4o
ClassPathResource image = new ClassPathResource("diagram.png");
chatClient.prompt()
    .user(u -> u.text("What does this architecture diagram show?")
               .media(MimeTypeUtils.IMAGE_PNG, image))
    .call()
    .content();
```

### UserMessage with Content Parts

```java
// Manually creating a UserMessage with multiple content parts
UserMessage userMessage = new UserMessage(
    "Analyze this image and code:",
    List.of(
        new MediaContent(MimeTypeUtils.IMAGE_PNG, new ClassPathResource("screenshot.png")),
        new TextContent("Here is the related Java code: " + codeSnippet)
    )
);
```

---

<a id="assistantmessage--the-ais-response"></a>
## 8. AssistantMessage — The AI's Response

`AssistantMessage` represents what the AI said. You'll encounter it when building multi-turn conversations.

### Reading AssistantMessage

```java
ChatResponse response = chatClient.prompt()
    .user("What is a Java Record?")
    .call()
    .chatResponse();

AssistantMessage aiMessage = response.getResult().getOutput();

// Get the text content
String content = aiMessage.getContent();

// Get tool calls if the AI decided to use tools
List<AssistantMessage.ToolCall> toolCalls = aiMessage.getToolCalls();

// Check message type
MessageType type = aiMessage.getMessageType(); // MessageType.ASSISTANT
```

### Adding AssistantMessage to Conversation History

```java
// Building a multi-turn conversation manually
List<Message> conversationHistory = new ArrayList<>();

// Turn 1
conversationHistory.add(new UserMessage("What is Spring AI?"));
ChatResponse response1 = chatModel.call(new Prompt(conversationHistory));
AssistantMessage aiReply1 = response1.getResult().getOutput();
conversationHistory.add(aiReply1);  // Add AI's reply to history

// Turn 2 — AI remembers the previous context!
conversationHistory.add(new UserMessage("Can you give me a code example?"));
ChatResponse response2 = chatModel.call(new Prompt(conversationHistory));
String answer = response2.getResult().getOutput().getContent();
```

---

<a id="toolresponsemessage--tool-results"></a>
## 9. ToolResponseMessage — Tool Results

`ToolResponseMessage` carries the result of a tool/function call back to the AI. You'll use this in Phase 6 (AI Agents).

### Conceptual Flow

```
User: "What's the weather in Mumbai?"
        ↓
AI decides: "I need to call the weather tool"
        ↓ ToolCall created
Your code: Calls actual weather API
        ↓
ToolResponseMessage: { "city": "Mumbai", "temp": "32°C", "condition": "Sunny" }
        ↓
AI reads tool result and responds: "The weather in Mumbai is sunny and 32°C."
```

```java
// ToolResponseMessage is created internally by Spring AI when using @Tool
// Preview of Phase 6 — you'll learn this in detail there
ToolResponseMessage toolResult = new ToolResponseMessage(
    List.of(new ToolResponseMessage.ToolResponse(
        toolCallId,
        "getWeather",
        "{\"temp\": \"32°C\", \"condition\": \"Sunny\"}"
    ))
);
```

---

<a id="multi-turn-conversations"></a>
## 10. Multi-Turn Conversations

Multi-turn conversations maintain context across multiple messages — like a real chat session.

### Why Multi-Turn Matters

```
WITHOUT multi-turn (stateless):
  User: "My name is Alice"
  AI:   "Nice to meet you, Alice!"

  User: "What's my name?"        ← New request, no history
  AI:   "I don't know your name" ❌

WITH multi-turn (stateful):
  User: "My name is Alice"
  AI:   "Nice to meet you, Alice!"

  User: "What's my name?"        ← History included
  AI:   "Your name is Alice!" ✅
```

### Method 1: Manual Message List (Full Control)

```java
@Service
public class ConversationService {

    private final ChatModel chatModel;

    // In-memory session storage (use a database for production!)
    private final Map<String, List<Message>> sessions = new ConcurrentHashMap<>();

    public ConversationService(ChatModel chatModel) {
        this.chatModel = chatModel;
    }

    public String chat(String sessionId, String userInput) {
        // Get or create conversation history for this session
        List<Message> history = sessions.computeIfAbsent(sessionId, k -> new ArrayList<>());

        // Add the new user message to history
        history.add(new UserMessage(userInput));

        // Call the AI with the FULL conversation history
        ChatResponse response = chatModel.call(new Prompt(history));
        AssistantMessage aiMessage = response.getResult().getOutput();

        // Add AI's response to history for next turn
        history.add(aiMessage);

        return aiMessage.getContent();
    }

    public void clearSession(String sessionId) {
        sessions.remove(sessionId);
    }
}
```

### Method 2: MessageChatMemoryAdvisor (Spring AI Built-in — Recommended)

Spring AI provides built-in memory management via **Advisors** (covered in Phase 4).

```java
@Configuration
public class AiConfig {

    @Bean
    public ChatClient chatClientWithMemory(ChatClient.Builder builder) {
        // InMemoryChatMemory — stores history in RAM
        // (Use JdbcChatMemory or CassandraChatMemory for production)
        ChatMemory memory = new InMemoryChatMemory();

        return builder
            .defaultAdvisors(
                new MessageChatMemoryAdvisor(memory)  // Auto-manages conversation history!
            )
            .build();
    }
}

// Usage — Spring AI handles memory automatically!
@RestController
public class ChatController {

    private final ChatClient chatClient;

    public ChatController(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    @GetMapping("/chat")
    public String chat(@RequestParam String message,
                       @RequestParam String sessionId) {
        return chatClient.prompt()
            .user(message)
            .advisors(a -> a.param(CHAT_MEMORY_CONVERSATION_ID_KEY, sessionId))
            .call()
            .content();
    }
}
```

### Managing Conversation Window Size

LLMs have a context window limit (e.g., 128K tokens for GPT-4o). For long conversations, you need to manage history size.

```java
// Keep only the last N messages in memory
ChatMemory memory = new InMemoryChatMemory();

// MessageChatMemoryAdvisor with window size
MessageChatMemoryAdvisor advisor = new MessageChatMemoryAdvisor(
    memory,
    DEFAULT_CHAT_MEMORY_CONVERSATION_ID,
    10  // Keep only last 10 messages in context
);
```

---

<a id="chatoptions--controlling-ai-behavior"></a>
## 11. ChatOptions — Controlling AI Behavior

`ChatOptions` are the parameters that control HOW the AI generates its response.

### Common Options Explained

```java
OpenAiChatOptions options = OpenAiChatOptions.builder()
    // Which AI model to use
    .model("gpt-4o-mini")

    // Creativity: 0.0 (deterministic) → 2.0 (very random)
    // Default: 0.7
    .temperature(0.7)

    // Max tokens in the AI's response (cost & length control)
    // 1 token ≈ 4 characters
    .maxTokens(500)

    // Nucleus sampling: 0.1 (conservative) → 1.0 (all tokens)
    // Use EITHER temperature OR topP, not both
    .topP(0.9)

    // Reduce repetition: positive = less repetition
    // Range: -2.0 to 2.0, default: 0.0
    .frequencyPenalty(0.5)

    // Encourage new topics: positive = more topic variety
    // Range: -2.0 to 2.0, default: 0.0
    .presencePenalty(0.3)

    // Number of responses to generate (most APIs: max 1)
    .n(1)

    // Stop generation when these strings are found
    .stop(List.of("END", "---"))

    .build();
```

### Quick Reference: When to Use Which Temperature

| Use Case | Temperature | Notes |
|---|---|---|
| Code generation | `0.0–0.2` | Deterministic, fewer hallucinations |
| Factual Q&A | `0.1–0.3` | Accurate, minimal creativity |
| General chat | `0.5–0.8` | Balanced (DEFAULT) |
| Creative writing | `0.8–1.2` | More variety and imagination |
| Brainstorming | `1.0–1.5` | Diverse, out-of-the-box ideas |

### Provider-Specific Options

Each AI provider has unique options. Spring AI provides typed options for each:

```java
// OpenAI specific
OpenAiChatOptions openAiOptions = OpenAiChatOptions.builder()
    .model("gpt-4o")
    .temperature(0.7)
    .responseFormat(new ResponseFormat(ResponseFormat.Type.JSON_OBJECT))  // Force JSON output
    .seed(42)         // For reproducibility (same seed → same output)
    .build();

// Ollama specific
OllamaChatOptions ollamaOptions = OllamaChatOptions.builder()
    .model("llama3.2")
    .temperature(0.7)
    .numCtx(4096)    // Context window size
    .numPredict(500) // Max tokens to predict
    .build();

// Anthropic (Claude) specific
AnthropicChatOptions claudeOptions = AnthropicChatOptions.builder()
    .model("claude-3-5-sonnet-20241022")
    .temperature(0.7)
    .maxTokens(1024)
    .build();
```

---

<a id="options-at-builder-vs-per-call-level"></a>
## 12. Options at Builder vs Per-Call Level

You can set options at two levels. Understanding the **override hierarchy** is important.

### Override Priority (highest wins)

```
Per-call options
    ↑ overrides
Builder-level (default) options
    ↑ overrides
application.yml options
```

### Example: Three-Level Configuration

```yaml
# Level 1: application.yml (lowest priority — global defaults)
spring:
  ai:
    openai:
      chat:
        options:
          model: gpt-4o-mini
          temperature: 0.7
          max-tokens: 500
```

```java
// Level 2: Builder-level (overrides application.yml)
@Bean
public ChatClient chatClient(ChatClient.Builder builder) {
    return builder
        .defaultOptions(OpenAiChatOptions.builder()
            .temperature(0.5)    // Overrides application.yml's 0.7
            .maxTokens(1000)     // Overrides application.yml's 500
            .build())
        .build();
}
```

```java
// Level 3: Per-call (highest priority — overrides everything)
String response = chatClient.prompt()
    .user("Write a creative poem about Java")
    .options(OpenAiChatOptions.builder()
        .temperature(1.2)    // Overrides builder's 0.5 for THIS call only
        .maxTokens(200)      // Overrides builder's 1000 for THIS call only
        .build())
    .call()
    .content();
```

### Practical Pattern: Feature-Specific Options

```java
@Service
public class ChatService {

    private final ChatClient chatClient;

    // These are defined ONCE for reuse
    private static final OpenAiChatOptions CODE_OPTIONS = OpenAiChatOptions.builder()
        .temperature(0.1).maxTokens(2000).build();

    private static final OpenAiChatOptions CREATIVE_OPTIONS = OpenAiChatOptions.builder()
        .temperature(1.1).maxTokens(500).build();

    private static final OpenAiChatOptions FACTUAL_OPTIONS = OpenAiChatOptions.builder()
        .temperature(0.0).maxTokens(300).build();

    public ChatService(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    public String generateCode(String requirement) {
        return chatClient.prompt()
            .system("You are a Java code generator. Output only valid Java code.")
            .user(requirement)
            .options(CODE_OPTIONS)
            .call().content();
    }

    public String writeCreativeContent(String topic) {
        return chatClient.prompt()
            .system("You are a creative writer.")
            .user("Write about: " + topic)
            .options(CREATIVE_OPTIONS)
            .call().content();
    }

    public String answerFactually(String question) {
        return chatClient.prompt()
            .system("Answer factually and concisely.")
            .user(question)
            .options(FACTUAL_OPTIONS)
            .call().content();
    }
}
```

---

<a id="structured-outputs--entity-mapping"></a>
## 13. Structured Outputs — Entity Mapping

One of Spring AI's most powerful features: **mapping AI responses directly to Java objects**.

### The Problem Without Structured Output

```java
// Without structured output — fragile, manual parsing
String rawResponse = chatClient.prompt()
    .user("Give me info about Java 21 as JSON")
    .call()
    .content();

// Now what? You get a String like:
// "```json\n{ \"version\": \"21\", \"releaseYear\": 2023 }\n```"
// You have to strip markdown, parse JSON manually...
ObjectMapper mapper = new ObjectMapper();
JsonNode node = mapper.readTree(rawResponse.replaceAll("```json\\n|\\n```", ""));
String version = node.get("version").asText();  // Fragile! What if format changes?
```

### With Structured Output — Clean & Type-Safe

```java
// Define your target structure as a Java Record or class
record JavaVersionInfo(
    String version,
    int releaseYear,
    List<String> keyFeatures,
    boolean isLTS
) {}

// Spring AI handles everything automatically!
JavaVersionInfo info = chatClient.prompt()
    .user("Provide information about Java 21")
    .call()
    .entity(JavaVersionInfo.class);  // ← Magic happens here

// Now use it as a proper Java object
System.out.println(info.version());        // "21"
System.out.println(info.releaseYear());    // 2023
System.out.println(info.isLTS());          // true
info.keyFeatures().forEach(System.out::println);
```

### More Complex Nested Records

```java
// Nested records work too!
record Developer(String name, String role, int yearsOfExperience) {}

record ProjectRecommendation(
    String projectName,
    String description,
    String difficulty,           // "beginner", "intermediate", "advanced"
    List<String> technologiesNeeded,
    Developer idealDeveloper,    // Nested record!
    int estimatedWeeks
) {}

ProjectRecommendation project = chatClient.prompt()
    .user("Suggest a Spring AI project for a mid-level Java developer")
    .call()
    .entity(ProjectRecommendation.class);

System.out.println(project.projectName());
System.out.println(project.idealDeveloper().role());  // Accessing nested record
```

### Using Regular Classes (instead of Records)

```java
// POJO class also works — needs default constructor + getters/setters (or Lombok)
@Data  // Lombok annotation
@NoArgsConstructor
@AllArgsConstructor
public class MovieReview {
    private String title;
    private double rating;      // 1.0 to 10.0
    private String sentiment;   // "positive", "negative", "neutral"
    private String summary;
    private List<String> pros;
    private List<String> cons;
}

MovieReview review = chatClient.prompt()
    .user("Review the movie Inception")
    .call()
    .entity(MovieReview.class);
```

### How Spring AI Makes This Work

Internally, Spring AI:
1. Inspects your target class via reflection
2. Generates a JSON schema from the class structure
3. Adds format instructions to the prompt automatically:
   ```
   Your response must be in JSON format matching this schema:
   { "version": "string", "releaseYear": "integer", ... }
   ```
4. Parses the AI's JSON response and maps it to your class

---

<a id="beanoutputconverter--under-the-hood"></a>
## 14. BeanOutputConverter — Under the Hood

`BeanOutputConverter` is what Spring AI uses internally to convert AI text → Java objects.

### How It Works (The Details)

```java
// What Spring AI does internally when you call .entity(MyClass.class):

// Step 1: Create a BeanOutputConverter for your type
BeanOutputConverter<JavaVersionInfo> converter =
    new BeanOutputConverter<>(JavaVersionInfo.class);

// Step 2: Get the JSON schema + format instructions
String formatInstructions = converter.getFormat();
// Output: "Your response should be in JSON format.\nHere is the JSON Schema..."

// Step 3: Add format instructions to the prompt automatically
Prompt prompt = new Prompt(
    List.of(
        new UserMessage("Provide info about Java 21\n" + formatInstructions)
    )
);

// Step 4: Call the AI
ChatResponse response = chatModel.call(prompt);
String jsonText = response.getResult().getOutput().getContent();

// Step 5: Parse JSON → Java object
JavaVersionInfo result = converter.convert(jsonText);
```

### Using BeanOutputConverter Manually (advanced)

```java
// Manually use BeanOutputConverter when you need more control
@GetMapping("/custom-parse")
public JavaVersionInfo customParse() {
    var converter = new BeanOutputConverter<>(JavaVersionInfo.class);

    String jsonResponse = chatClient.prompt()
        .user("Info about Java 21. " + converter.getFormat())
        .call()
        .content();

    return converter.convert(jsonResponse);
}
```

### Available Output Converters in Spring AI

| Converter | Output Type | When to Use |
|---|---|---|
| `BeanOutputConverter<T>` | Single POJO/Record | Map to one object |
| `ListOutputConverter` | `List<String>` | Simple string lists |
| `MapOutputConverter` | `Map<String, Object>` | Key-value pairs |

---

<a id="mapping-lists-of-objects"></a>
## 15. Mapping Lists of Objects

When you need the AI to return multiple items as a typed list.

### Mapping to List of Records

```java
record BookRecommendation(String title, String author, String genre, String reason) {}

// Use ParameterizedTypeReference for lists
List<BookRecommendation> books = chatClient.prompt()
    .user("Recommend 5 books for a Java developer learning AI")
    .call()
    .entity(new ParameterizedTypeReference<List<BookRecommendation>>() {});

// Use the result
books.forEach(book ->
    System.out.printf("📚 %s by %s (%s)%n", book.title(), book.author(), book.genre())
);
```

### Mapping to Simple String List

```java
// Simple list of strings — use ListOutputConverter
List<String> topics = chatClient.prompt()
    .user("List 10 important Spring AI concepts. Return as a comma-separated list.")
    .call()
    .entity(new ParameterizedTypeReference<List<String>>() {});

topics.forEach(topic -> System.out.println("• " + topic));
```

### Mapping to Map

```java
// Map<String, String> — key-value pairs
Map<String, String> javaFeatures = chatClient.prompt()
    .user("List Java 21 features with brief descriptions as key-value pairs")
    .call()
    .entity(new ParameterizedTypeReference<Map<String, String>>() {});

javaFeatures.forEach((feature, description) ->
    System.out.printf("%-30s → %s%n", feature, description)
);
```

### Complete REST API with Structured Output

```java
@RestController
@RequestMapping("/api/recommendations")
public class RecommendationController {

    private final ChatClient chatClient;

    public RecommendationController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    // Records for structured output
    record CourseRecommendation(
        String courseName,
        String platform,          // "YouTube", "Udemy", "Coursera", "Official Docs"
        String difficulty,        // "beginner", "intermediate", "advanced"
        int estimatedHours,
        List<String> prerequisites,
        String url
    ) {}

    record LearningPlan(
        String topic,
        String totalDuration,
        List<CourseRecommendation> courses,
        List<String> practiceProjects
    ) {}

    @GetMapping("/learning-plan")
    public LearningPlan getLearningPlan(@RequestParam String topic,
                                        @RequestParam String level) {
        return chatClient.prompt()
            .system("""
                You are an expert learning advisor.
                Always recommend real, existing resources.
                Provide realistic time estimates.
                """)
            .user("Create a learning plan for '%s' at %s level".formatted(topic, level))
            .call()
            .entity(LearningPlan.class);
    }
}
```

**Test it:**
```
GET /api/recommendations/learning-plan?topic=Spring AI&level=beginner

Response (automatic JSON):
{
  "topic": "Spring AI",
  "totalDuration": "6 weeks",
  "courses": [
    {
      "courseName": "Spring AI Official Documentation",
      "platform": "Official Docs",
      "difficulty": "beginner",
      "estimatedHours": 10,
      "prerequisites": ["Spring Boot", "Java 17+"],
      "url": "https://docs.spring.io/spring-ai/reference/"
    },
    ...
  ],
  "practiceProjects": [
    "Build a PDF Q&A chatbot",
    "Create a multi-provider chat API",
    ...
  ]
}
```

---

<a id="phase-2-summary--checklist"></a>
## 16. Phase 2 Summary & Checklist

### What You Learned

```
✅ ChatClient vs ChatModel — when and why to use each
✅ ChatClient.Builder — configuring with defaults
✅ Multiple ChatClient beans with @Qualifier
✅ Synchronous calls — .call().content() and extracting metadata
✅ Streaming — .stream().content() with Flux<String>
✅ Four Message types — System, User, Assistant, Tool
✅ SystemMessage — defining AI behavior, strategies
✅ UserMessage — dynamic content, multi-modal
✅ AssistantMessage — reading AI responses
✅ Multi-turn conversations — manual and with ChatMemoryAdvisor
✅ ChatOptions — temperature, maxTokens, topP, penalties
✅ Override hierarchy — yml → builder → per-call
✅ Structured Outputs — .entity(MyClass.class)
✅ BeanOutputConverter — how Spring AI maps JSON to Java
✅ Mapping lists — ParameterizedTypeReference
```

---

### 🏁 Phase 2 Milestone Project: "Movie Recommender API"

Build a REST API that uses structured output to recommend movies.

```
Endpoints to implement:

1. GET /movies/recommend?genre=sci-fi&count=3
   → Returns List<MovieRecommendation> as JSON
   → Uses structured output

2. POST /movies/review
   Body: {"title": "Inception"}
   → Returns MovieReview with rating, sentiment, pros, cons
   → Uses structured output

3. GET /movies/chat?message={msg}&sessionId={id}
   → Conversational movie discussion
   → Uses ChatMemoryAdvisor for multi-turn context

4. GET /movies/stream?prompt={prompt}
   → Streams movie description word-by-word
   → Returns text/event-stream
```

**Records to define:**
```java
record MovieRecommendation(String title, String year, String director,
                           String genre, String synopsis, double imdbRating) {}

record MovieReview(String title, double rating, String sentiment,
                   String summary, List<String> pros, List<String> cons) {}
```

**Acceptance Criteria:**
- [ ] All 4 endpoints work
- [ ] JSON responses are properly typed (not raw strings)
- [ ] `/movies/chat` remembers conversation context across requests
- [ ] `/movies/stream` shows response word-by-word in browser
- [ ] Temperature is different for creative vs factual endpoints
- [ ] API key is in environment variable

---

### 🔗 Quick Reference Card

```java
// ── ChatClient Setup ──────────────────────────────────────────
@Bean
public ChatClient chatClient(ChatClient.Builder builder) {
    return builder
        .defaultSystem("You are a helpful assistant.")
        .defaultOptions(OpenAiChatOptions.builder().temperature(0.7).build())
        .build();
}

// ── Synchronous Call ──────────────────────────────────────────
String text = chatClient.prompt().user("Hello").call().content();

// ── Streaming Call ────────────────────────────────────────────
Flux<String> stream = chatClient.prompt().user("Hello").stream().content();

// ── Structured Output ─────────────────────────────────────────
record MyData(String field1, int field2) {}
MyData data = chatClient.prompt().user("...").call().entity(MyData.class);

// ── List Output ───────────────────────────────────────────────
List<MyData> list = chatClient.prompt().user("...").call()
    .entity(new ParameterizedTypeReference<List<MyData>>() {});

// ── Per-call Options ──────────────────────────────────────────
chatClient.prompt().user("...").options(
    OpenAiChatOptions.builder().temperature(0.0).maxTokens(200).build()
).call().content();

// ── Multi-turn with Memory ────────────────────────────────────
ChatMemory memory = new InMemoryChatMemory();
ChatClient memoryClient = ChatClient.builder(chatModel)
    .defaultAdvisors(new MessageChatMemoryAdvisor(memory))
    .build();
memoryClient.prompt().user("Hi, I'm Alice")
    .advisors(a -> a.param(CHAT_MEMORY_CONVERSATION_ID_KEY, "session-1"))
    .call().content();
```

---

### ➡️ What's Next — Phase 3: AI Models & Providers

In Phase 3, you'll explore:
- OpenAI, Gemini, Anthropic Claude, Mistral integration
- Running local models with **Ollama** (free, private)
- Multi-modal models (vision — sending images to the AI)
- Switching providers without code changes

---

> 📁 **Part of**: [Spring AI Complete Roadmap](./Spring_AI_Roadmap.md)
> ⬅️ **Previous**: [Phase 1 — Introduction to Spring AI](./Phase1_Introduction_to_Spring_AI.md)
> ⏭️ **Next**: Phase 3 — AI Models & Providers
