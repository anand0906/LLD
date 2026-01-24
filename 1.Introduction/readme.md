# Low-Level Design (LLD) Introduction 

## Table of Contents
1. [Introduction](#introduction)
2. [Understanding LLD with Real-Life Examples](#understanding-lld-with-real-life-examples)
3. [Definition](#definition)
4. [Coding Example](#coding-example)
5. [Key Characteristics of LLD](#key-characteristics-of-lld)
   - [Granular and Code-Level](#1-granular-and-code-level)
   - [Implementation-Focused](#2-implementation-focused)
   - [Applies OOP Principles](#3-applies-oop-principles)
6. [Stakeholders in LLD](#stakeholders-in-lld)
7. [Difference between HLD and LLD](#difference-between-hld-and-lld)
8. [Importance of Low-Level Design](#importance-of-low-level-design)
9. [Interview Questions & Answers](#interview-questions--answers)

---

## Introduction

Low-Level Design (LLD) is a critical phase in the software development lifecycle that bridges the gap between high-level system architecture and actual code implementation. It focuses on the detailed design of individual components, modules, algorithms, and data structures.

---

## Understanding LLD with Real-Life Examples

### The House Building Analogy

Think of building a house:

- **High-Level Design (HLD)** is like the architect's blueprint
  - Defines where the rooms will be
  - Specifies room sizes
  - Shows how rooms are connected
  
- **Low-Level Design (LLD)** is like the detailed construction plan
  - Decides where the switches go
  - Plans how the plumbing is laid out
  - Specifies what materials to use
  - Details the wiring diagrams

**Key Takeaway:** LLD is all about the small, detailed planning you do before actually building the house (or writing code).

---

## Definition

> **Low-Level Design (LLD)** is where your code starts to take shape. It's a crucial phase in the software development lifecycle that focuses on the detailed design of individual components or modules of a system.

LLD involves:
- Specifying the internal structure
- Defining algorithms
- Choosing appropriate data structures
- Implementing system functionality
- Acting as a bridge between high-level design and actual coding

---

## Coding Example

### Simple Login System

A basic example of LLD for a Login System would involve designing the following components:

```
Component: Authentication Module

Classes:
├── User
│   ├── Properties: userId, username, email, passwordHash
│   └── Methods: validateCredentials(), updateProfile()
│
├── AuthenticationService
│   ├── Methods:
│   │   ├── login(username, password)
│   │   ├── signUp(userDetails)
│   │   ├── forgotPassword(email)
│   │   └── resetPassword(token, newPassword)
│   │
│   └── Dependencies: DatabaseService, EmailService

Data Flow:
1. User enters credentials
2. login() validates input
3. Check against database
4. Return success/failure response
```

---

## Key Characteristics of LLD

### 1. Granular and Code-Level

LLD dives deep into the fine details of how each component will function.

**What it defines:**
- Classes and their relationships
- Functions and methods
- Variables and their scope
- Data structures (arrays, hashmaps, trees, etc.)

**Example:**

Instead of saying "we need user authentication", LLD shows:
- What classes handle authentication (e.g., `AuthManager`, `TokenValidator`)
- What methods validate login (e.g., `validateCredentials()`, `checkPassword()`)
- What happens on failure (e.g., error logging, retry mechanism, account lockout)

```java
class AuthManager {
    private UserRepository userRepo;
    private PasswordEncoder encoder;
    
    public AuthResult login(String username, String password) {
        User user = userRepo.findByUsername(username);
        if (user == null) {
            return AuthResult.failure("User not found");
        }
        
        if (!encoder.matches(password, user.getPasswordHash())) {
            logFailedAttempt(username);
            return AuthResult.failure("Invalid password");
        }
        
        return AuthResult.success(generateToken(user));
    }
}
```

### 2. Implementation-Focused

LLD is directly linked to how the actual code will be written.

**Key Aspects:**
- Acts as a blueprint for developers
- Guides the logic, flow, and structure of modules
- Includes pseudocode and diagrams
- Shows real-time data flow between functions

**Example: Order Processing Flow**

```
Pseudocode for processOrder():

function processOrder(orderId):
    order = fetchOrderFromDB(orderId)
    
    if order.status != "PENDING":
        return error("Order already processed")
    
    if !validateInventory(order.items):
        return error("Insufficient inventory")
    
    payment = processPayment(order.totalAmount, order.paymentMethod)
    
    if payment.success:
        updateInventory(order.items)
        updateOrderStatus(orderId, "CONFIRMED")
        sendConfirmationEmail(order.userEmail)
        return success("Order processed")
    else:
        return error("Payment failed")
```

### 3. Applies OOP Principles

LLD makes heavy use of Object-Oriented Programming (OOP) concepts.

**OOP Principles Applied:**
- **Classes:** Blueprint for objects
- **Inheritance:** Code reusability through parent-child relationships
- **Abstraction:** Hiding complex implementation details
- **Encapsulation:** Data hiding and protection
- **Polymorphism:** Same interface, different implementations

**Example: Notification System**

```java
// Base class (Abstraction)
abstract class Notification {
    protected String recipient;
    protected String message;
    
    public abstract void send();
}

// Inheritance & Polymorphism
class EmailNotification extends Notification {
    private String subject;
    
    @Override
    public void send() {
        // Email-specific implementation
        EmailService.sendEmail(recipient, subject, message);
    }
}

class SMSNotification extends Notification {
    @Override
    public void send() {
        // SMS-specific implementation
        SMSGateway.sendSMS(recipient, message);
    }
}

class PushNotification extends Notification {
    private String deviceToken;
    
    @Override
    public void send() {
        // Push notification implementation
        PushService.sendPush(deviceToken, message);
    }
}

// Usage (Polymorphism in action)
Notification notification = new EmailNotification();
notification.send(); // Calls EmailNotification's send()
```

---

## Stakeholders in LLD

In Low-Level Design (LLD), the stakeholders are primarily people directly involved in the actual implementation of the system:

- **Senior Software Developers:** Design and review LLD documents
- **Technical Leads:** Ensure design aligns with technical standards
- **Engineering Managers:** Oversee timelines and resource allocation
- **Software Architects:** Validate that LLD aligns with HLD
- **QA Engineers:** Use LLD for test case creation
- **Junior Developers:** Reference LLD during implementation

---

## Difference between HLD and LLD

| Aspect | High-Level Design (HLD) | Low-Level Design (LLD) |
|--------|-------------------------|------------------------|
| **Purpose** | System overview & modules | Detailed implementation & logic |
| **Level of Detail** | Abstract | Highly Detailed |
| **Focus** | Architecture, modules, interfaces | Class diagrams, methods, details |
| **Outcome** | Modules, System diagrams | Detailed class/method diagrams |
| **Audience** | Stakeholders, Architects, Management | Developers, Tech Leads |
| **Scope** | Entire system | Individual components/modules |
| **Artifacts** | System architecture diagrams, component diagrams | Class diagrams, sequence diagrams, pseudocode |
| **Timeframe** | Early phase of design | After HLD is approved |

---

## Importance of Low-Level Design

Beyond its role in the software development lifecycle and high demand for senior roles, LLD is crucial for several reasons:

### 1. **Avoids Rework**
- Clearly defined logic and structure help catch design issues early
- Reduces costly changes during development
- Minimizes debugging time
- Prevents architectural debt

### 2. **Improves Collaboration**
- Acts as a shared reference for teams
- Ensures everyone understands component behavior
- Clarifies integration points between modules
- Facilitates code reviews and knowledge transfer

### 3. **Promotes Scalability**
- Well-designed, modular components can handle growth
- Supports addition of new features without major redesign
- Enables horizontal and vertical scaling
- Reduces technical debt

### 4. **Encourages Best Practices**
- Enforces use of clean code principles
- Promotes design patterns (Singleton, Factory, Observer, etc.)
- Applies OOP principles consistently
- Leads to maintainable and robust systems

---'
---

## Interview Questions & Answers

### Q1: What is Low-Level Design (LLD) and why is it important?

**Answer:**

Low-Level Design (LLD) is a detailed design phase in software development that focuses on the implementation specifics of individual components and modules. It defines classes, methods, data structures, algorithms, and the internal logic needed to build a system.

**Importance:**
- Provides a clear blueprint for developers
- Catches design flaws early before coding begins
- Improves code quality and maintainability
- Facilitates team collaboration through shared understanding
- Reduces development time and rework
- Ensures scalability and extensibility

---

### Q2: How does LLD differ from HLD?

**Answer:**

| Aspect | HLD | LLD |
|--------|-----|-----|
| **Abstraction Level** | High-level, abstract view | Detailed, concrete view |
| **Focus** | System architecture, modules, and their interactions | Internal structure of each module, classes, and methods |
| **Audience** | Stakeholders, architects, managers | Developers, technical leads |
| **Output** | Architecture diagrams, component diagrams | Class diagrams, sequence diagrams, pseudocode |
| **Question Answered** | "What needs to be built?" | "How will it be built?" |

**Example:**
- HLD: "The system needs a payment module that integrates with third-party gateways"
- LLD: Detailed class diagram showing `PaymentService`, `PaymentGatewayAdapter`, `PaymentValidator` with specific methods like `processPayment()`, `validateCard()`, `handleResponse()`

---

### Q3: What are the key components of a good LLD document?

**Answer:**

A comprehensive LLD document should include:

1. **Class Diagrams**
   - Shows classes, attributes, methods, and relationships
   - Includes inheritance, composition, and association

2. **Sequence Diagrams**
   - Illustrates interaction between objects over time
   - Shows method calls and message flow

3. **Data Structures**
   - Specifies which data structures will be used (HashMap, List, Tree, etc.)
   - Justifies the choice based on time/space complexity

4. **Algorithms**
   - Pseudocode or flowcharts for complex logic
   - Big O notation for performance analysis

5. **Database Schema**
   - Table structures, relationships, indexes
   - ER diagrams for data modeling

6. **API Specifications**
   - Method signatures, parameters, return types
   - Error handling mechanisms

7. **Design Patterns**
   - Which patterns are used and where (Singleton, Factory, Observer, etc.)


---

### Q4: What design patterns would you use in LLD and why?

**Answer:**

Common design patterns in LLD:

**1. Singleton Pattern**
- **Use Case:** Database connection, configuration manager, logger
- **Why:** Ensures only one instance exists globally
```java
class DatabaseConnection {
    private static DatabaseConnection instance;
    
    private DatabaseConnection() {}
    
    public static synchronized DatabaseConnection getInstance() {
        if (instance == null) {
            instance = new DatabaseConnection();
        }
        return instance;
    }
}
```

**2. Factory Pattern**
- **Use Case:** Creating objects without specifying exact class
- **Why:** Encapsulates object creation logic
```java
class NotificationFactory {
    public static Notification createNotification(String type) {
        switch(type) {
            case "EMAIL": return new EmailNotification();
            case "SMS": return new SMSNotification();
            case "PUSH": return new PushNotification();
            default: throw new IllegalArgumentException("Unknown type");
        }
    }
}
```

**3. Strategy Pattern**
- **Use Case:** Different payment methods, sorting algorithms
- **Why:** Allows selecting algorithm at runtime
```java
interface PaymentStrategy {
    void pay(double amount);
}

class CreditCardPayment implements PaymentStrategy {
    public void pay(double amount) { /* Implementation */ }
}

class PayPalPayment implements PaymentStrategy {
    public void pay(double amount) { /* Implementation */ }
}
```

**4. Observer Pattern**
- **Use Case:** Event handling, notification systems
- **Why:** Decouples publishers from subscribers
```java
interface Observer {
    void update(String event);
}

class Subject {
    private List<Observer> observers = new ArrayList<>();
    
    public void attach(Observer obs) { observers.add(obs); }
    public void notifyAllObservers(String event) {
        for (Observer obs : observers) {
            obs.update(event);
        }
    }
}
```

**5. Decorator Pattern**
- **Use Case:** Adding features to objects dynamically
- **Why:** Extends functionality without modifying original class

---

### Q5: How do you handle scalability in LLD?

**Answer:**

Scalability considerations in LLD:

**1. Database Design**
- Use indexing on frequently queried columns
- Implement database sharding for horizontal scaling
- Design for read replicas
```sql
-- Example: Indexing
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_order_date ON orders(created_at);
```

**2. Caching Strategy**
- Use Redis/Memcached for frequently accessed data
- Implement cache-aside pattern
```java
class ProductService {
    private Cache cache;
    
    public Product getProduct(String id) {
        Product product = cache.get(id);
        if (product == null) {
            product = database.fetchProduct(id);
            cache.put(id, product, TTL);
        }
        return product;
    }
}
```

**3. Asynchronous Processing**
- Use message queues (RabbitMQ, Kafka) for heavy operations
- Implement event-driven architecture
```java
class OrderService {
    private MessageQueue queue;
    
    public void createOrder(Order order) {
        // Save order synchronously
        orderRepository.save(order);
        
        // Process notifications asynchronously
        queue.publish("order.created", order);
    }
}
```

**4. Load Balancing**
- Design stateless services
- Use consistent hashing for distributed caching

**5. Microservices Architecture**
- Break monoliths into independent services
- Each service can scale independently

---

### Q6: Design a URL shortener like bit.ly at the LLD level.

**Answer:**

**Requirements:**
- Convert long URL to short URL
- Redirect short URL to original URL
- Track analytics (click count)

**LLD Design:**

```java
class URLShortenerService {
    private Map<String, String> shortToLong; // shortCode -> originalURL
    private Map<String, String> longToShort; // originalURL -> shortCode
    private Map<String, Integer> analytics; // shortCode -> clickCount
    private static final String BASE_URL = "https://short.ly/";
    private static final String CHARS = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    
    public String shortenURL(String longURL) {
        // Check if already shortened
        if (longToShort.containsKey(longURL)) {
            return BASE_URL + longToShort.get(longURL);
        }
        
        // Generate unique short code
        String shortCode = generateShortCode();
        
        // Store mappings
        shortToLong.put(shortCode, longURL);
        longToShort.put(longURL, shortCode);
        analytics.put(shortCode, 0);
        
        return BASE_URL + shortCode;
    }
    
    public String expandURL(String shortCode) {
        if (!shortToLong.containsKey(shortCode)) {
            throw new NotFoundException("Short URL not found");
        }
        
        // Increment analytics
        analytics.put(shortCode, analytics.get(shortCode) + 1);
        
        return shortToLong.get(shortCode);
    }
    
    private String generateShortCode() {
        // Method 1: Random generation (simple but can have collisions)
        StringBuilder sb = new StringBuilder();
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            sb.append(CHARS.charAt(random.nextInt(CHARS.length())));
        }
        
        String shortCode = sb.toString();
        
        // Handle collision
        while (shortToLong.containsKey(shortCode)) {
            shortCode = generateShortCode();
        }
        
        return shortCode;
    }
    
    // Alternative: Base62 encoding of auto-increment ID
    private String encodeBase62(long id) {
        StringBuilder sb = new StringBuilder();
        while (id > 0) {
            sb.append(CHARS.charAt((int)(id % 62)));
            id /= 62;
        }
        return sb.reverse().toString();
    }
    
    public int getClickCount(String shortCode) {
        return analytics.getOrDefault(shortCode, 0);
    }
}

// Database Schema Design
/*
Table: url_mappings
- id (BIGINT, PRIMARY KEY, AUTO_INCREMENT)
- short_code (VARCHAR(10), UNIQUE, INDEXED)
- original_url (TEXT)
- created_at (TIMESTAMP)
- expires_at (TIMESTAMP, NULLABLE)

Table: analytics
- id (BIGINT, PRIMARY KEY, AUTO_INCREMENT)
- short_code (VARCHAR(10), FOREIGN KEY)
- click_count (INT)
- last_accessed (TIMESTAMP)
*/
```

**Scalability Considerations:**
- Use distributed cache (Redis) for frequently accessed URLs
- Database sharding based on short_code hash
- Use CDN for global availability
- Implement rate limiting to prevent abuse

---

### Q7: What is the role of SOLID principles in LLD?

**Answer:**

SOLID principles are fundamental to creating maintainable, scalable LLD:

**S - Single Responsibility Principle**
- Each class should have only one reason to change
```java
// Bad: UserService doing too much
class UserService {
    void createUser() {}
    void sendEmail() {}
    void generateReport() {}
}

// Good: Separated responsibilities
class UserService {
    void createUser() {}
}
class EmailService {
    void sendEmail() {}
}
class ReportService {
    void generateReport() {}
}
```

**O - Open/Closed Principle**
- Open for extension, closed for modification
```java
// Using inheritance and polymorphism
abstract class Shape {
    abstract double calculateArea();
}

class Circle extends Shape {
    double calculateArea() { return Math.PI * radius * radius; }
}

class Rectangle extends Shape {
    double calculateArea() { return width * height; }
}
```

**L - Liskov Substitution Principle**
- Subtypes must be substitutable for their base types
```java
class Bird {
    void eat() {}
}

class FlyingBird extends Bird {
    void fly() {}
}

class Penguin extends Bird {
    // Doesn't inherit fly() - correct!
}
```

**I - Interface Segregation Principle**
- Clients shouldn't depend on interfaces they don't use
```java
// Bad: Fat interface
interface Worker {
    void work();
    void eat();
    void sleep();
}

// Good: Segregated interfaces
interface Workable {
    void work();
}
interface Eatable {
    void eat();
}
```

**D - Dependency Inversion Principle**
- Depend on abstractions, not concretions
```java
// Good: Depends on abstraction
class PaymentProcessor {
    private PaymentGateway gateway; // Interface
    
    public PaymentProcessor(PaymentGateway gateway) {
        this.gateway = gateway;
    }
}
```

---

### Q8: How would you design a rate limiter in LLD?

**Answer:**

**Approach: Token Bucket Algorithm**

```java
class RateLimiter {
    private int maxTokens;
    private int tokensPerSecond;
    private int currentTokens;
    private long lastRefillTimestamp;
    
    public RateLimiter(int maxTokens, int tokensPerSecond) {
        this.maxTokens = maxTokens;
        this.tokensPerSecond = tokensPerSecond;
        this.currentTokens = maxTokens;
        this.lastRefillTimestamp = System.currentTimeMillis();
    }
    
    public synchronized boolean allowRequest() {
        refillTokens();
        
        if (currentTokens > 0) {
            currentTokens--;
            return true;
        }
        
        return false;
    }
    
    private void refillTokens() {
        long now = System.currentTimeMillis();
        long timePassed = now - lastRefillTimestamp;
        
        int tokensToAdd = (int) (timePassed / 1000 * tokensPerSecond);
        
        if (tokensToAdd > 0) {
            currentTokens = Math.min(currentTokens + tokensToAdd, maxTokens);
            lastRefillTimestamp = now;
        }
    }
}

// Usage in API
class APIController {
    private Map<String, RateLimiter> userLimiters = new ConcurrentHashMap<>();
    
    public Response handleRequest(String userId, Request request) {
        RateLimiter limiter = userLimiters.computeIfAbsent(
            userId, 
            k -> new RateLimiter(100, 10) // 100 requests, refill 10/sec
        );
        
        if (!limiter.allowRequest()) {
            return Response.error(429, "Rate limit exceeded");
        }
        
        return processRequest(request);
    }
}
```

**Alternative: Sliding Window Log**
```java
class SlidingWindowRateLimiter {
    private Queue<Long> requestTimestamps = new LinkedList<>();
    private int maxRequests;
    private long windowSizeMs;
    
    public SlidingWindowRateLimiter(int maxRequests, long windowSizeMs) {
        this.maxRequests = maxRequests;
        this.windowSizeMs = windowSizeMs;
    }
    
    public synchronized boolean allowRequest() {
        long now = System.currentTimeMillis();
        long windowStart = now - windowSizeMs;
        
        // Remove old timestamps outside window
        while (!requestTimestamps.isEmpty() && 
               requestTimestamps.peek() <= windowStart) {
            requestTimestamps.poll();
        }
        
        if (requestTimestamps.size() < maxRequests) {
            requestTimestamps.offer(now);
            return true;
        }
        
        return false;
    }
}
```

---


---

## Conclusion

Low-Level Design is a critical skill for software engineers, especially for senior roles. It bridges the gap between architecture and implementation, ensuring that systems are built efficiently, maintainably, and scalably.

**Key Takeaways:**
- LLD focuses on detailed component design
- Applies OOP principles and design patterns
- Requires understanding of data structures and algorithms
- Essential for clean, maintainable code
- Critical for system scalability

---
