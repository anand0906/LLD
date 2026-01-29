# Single Responsibility Principle (SRP) - Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Definition](#definition)
3. [Real-Life Analogy](#real-life-analogy)
4. [Simple Example](#simple-example)
5. [Real-Time Examples](#real-time-examples)
6. [Advantages of SRP](#advantages-of-srp)
7. [Common Mistakes](#common-mistakes)
8. [Interview Questions & Answers](#interview-questions--answers)
9. [Best Practices](#best-practices)

---

## Introduction

The **Single Responsibility Principle (SRP)** states that every class or module should have only one reason to change, meaning it should only perform one specific task or responsibility.

**Core Idea:** *A class should have a single responsibility.*

![SRP Concept](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*P3oONz9Da3Tc1w97fMV73Q.png)

If a class has many responsibilities, it increases the possibility of bugs because making changes to one of its responsibilities could affect the other ones without you knowing.

---

## Definition

**A class should have only one reason to change.**

In other words:
- One job
- One responsibility  
- One purpose

**Why?** If a class takes more than one responsibility, it becomes **coupled**. This means that if one responsibility changes, the other responsibilities may also be affected, leading to a ripple effect of changes throughout the codebase.

---

## Real-Life Analogy

### The Restaurant Example

Imagine a chef who is responsible for:
- 🍳 Cooking
- 🧹 Cleaning
- 🍽️ Serving food
- 📦 Ordering groceries

**Problem:** If the chef is busy cleaning, they can't focus on cooking, and the quality of the food may suffer.

**Solution:** Different people should handle each task:
- **Chef** → Cooks
- **Cleaner** → Cleans
- **Waiter** → Serves
- **Manager** → Orders groceries

This way, each person can focus on their specific responsibility, leading to better results overall.

---

## Simple Example

### ❌ Without SRP (Violating the Principle)

```java
public class Invoice {
    private double subtotal;
    private double tax;

    public Invoice(double subtotal, double tax) {
        this.subtotal = subtotal;
        this.tax = tax;
    }

    // Responsibility 1: Calculate total
    public double calculateTotal() {
        return subtotal + tax;
    }

    // Responsibility 2: Print invoice
    public void printInvoice() {
        System.out.println("Invoice Details:");
        System.out.println("Subtotal: " + subtotal);
        System.out.println("Tax: " + tax);
        System.out.println("Total: " + calculateTotal());
    }

    // Responsibility 3: Save to database
    public void saveToDatabase() {
        System.out.println("Saving invoice to the database...");
    }
}
```

**Problems:**
- The `Invoice` class has **3 responsibilities**: calculation, printing, and saving
- Any change to printing logic requires modifying the Invoice class
- Hard to test individual responsibilities
- Violates SRP

---

### ✅ With SRP (Following the Principle)

#### 1. Invoice Class (Data Model)

```java
public class Invoice {
    private double subtotal;
    private double tax;

    public Invoice(double subtotal, double tax) {
        this.subtotal = subtotal;
        this.tax = tax;
    }

    public double getSubtotal() { return subtotal; }
    public double getTax() { return tax; }
}
```

#### 2. InvoiceCalculator Class (Calculation Logic)

```java
public class InvoiceCalculator {
    public double calculateTotal(Invoice invoice) {
        return invoice.getSubtotal() + invoice.getTax();
    }
}
```

#### 3. InvoicePrinter Class (Printing Logic)

```java
public class InvoicePrinter {
    public void printInvoice(Invoice invoice, InvoiceCalculator calculator) {
        System.out.println("Invoice Details:");
        System.out.println("Subtotal: " + invoice.getSubtotal());
        System.out.println("Tax: " + invoice.getTax());
        System.out.println("Total: " + calculator.calculateTotal(invoice));
    }
}
```

#### 4. InvoiceSaver Class (Database Logic)

```java
public class InvoiceSaver {
    public void saveToDatabase(Invoice invoice) {
        System.out.println("Saving invoice to the database...");
        // Database saving logic here
    }
}
```

#### Using the SRP Classes Together

```java
public class Main {
    public static void main(String[] args) {
        Invoice invoice = new Invoice(100.0, 15.0);
        InvoiceCalculator calculator = new InvoiceCalculator();
        InvoicePrinter printer = new InvoicePrinter();
        InvoiceSaver saver = new InvoiceSaver();

        // Calculate and print the invoice
        printer.printInvoice(invoice, calculator);

        // Save the invoice to the database
        saver.saveToDatabase(invoice);
    }
}
```

**Benefits:**
- Each class has **one clear responsibility**
- Easy to modify printing logic without touching calculation or database code
- Better testing - test each class independently
- Follows SRP perfectly

---

## Real-Time Examples

### Example 1: User Management System

#### ❌ Without SRP

```java
public class User {
    private String username;
    private String email;
    
    // User data
    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
    
    // Responsibility 1: Validation
    public boolean isValidEmail() {
        return email.contains("@");
    }
    
    // Responsibility 2: Database operations
    public void saveToDatabase() {
        System.out.println("Saving user to database...");
    }
    
    // Responsibility 3: Sending notifications
    public void sendWelcomeEmail() {
        System.out.println("Sending welcome email to " + email);
    }
}
```

#### ✅ With SRP

```java
// 1. User class - Only holds user data
public class User {
    private String username;
    private String email;
    
    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
    
    public String getUsername() { return username; }
    public String getEmail() { return email; }
}

// 2. UserValidator - Handles validation
public class UserValidator {
    public boolean isValidEmail(String email) {
        return email != null && email.contains("@");
    }
}

// 3. UserRepository - Handles database operations
public class UserRepository {
    public void save(User user) {
        System.out.println("Saving user to database...");
    }
}

// 4. EmailService - Handles email notifications
public class EmailService {
    public void sendWelcomeEmail(User user) {
        System.out.println("Sending welcome email to " + user.getEmail());
    }
}

// Usage
public class UserService {
    private UserValidator validator;
    private UserRepository repository;
    private EmailService emailService;
    
    public void registerUser(User user) {
        if (validator.isValidEmail(user.getEmail())) {
            repository.save(user);
            emailService.sendWelcomeEmail(user);
        }
    }
}
```

---

### Example 2: TUF+ Compiler System

**Scenario:** A compiler that needs to:
1. Add driver code
2. Perform syntax check
3. Run code with test cases
4. Store output in database
5. Return output to user

#### ❌ Without SRP (God Class)

```java
public class TUFplusCompiler {
    public void compile(String code) {
        addDriverCode(code);
        performSyntaxCheck(code);
        runTestCases(code);
        storeInDatabase(code);
        returnOutputToUser(code);
    }
    
    private void addDriverCode(String code) { /* ... */ }
    private void performSyntaxCheck(String code) { /* ... */ }
    private void runTestCases(String code) { /* ... */ }
    private void storeInDatabase(String code) { /* ... */ }
    private void returnOutputToUser(String code) { /* ... */ }
}
```

**Problem:** One class doing everything!

#### ✅ With SRP (Modular Design)

```java
// 1. DriverCodeGenerator - Adds driver code
public class DriverCodeGenerator {
    public String addDriverCode(String code) {
        System.out.println("Adding driver code...");
        return code + "\n// Driver code added";
    }
}

// 2. SyntaxChecker - Performs syntax checks
public class SyntaxChecker {
    public boolean checkSyntax(String code) {
        System.out.println("Checking syntax...");
        return true; // Simplified
    }
}

// 3. TestRunner - Runs test cases
public class TestRunner {
    public String runTests(String code) {
        System.out.println("Running test cases...");
        return "All tests passed";
    }
}

// 4. DatabaseManager - Stores output
public class DatabaseManager {
    public void storeOutput(String output) {
        System.out.println("Storing output in database...");
    }
}

// 5. UserOutputHandler - Returns output to user
public class UserOutputHandler {
    public void sendOutput(String output) {
        System.out.println("Sending output to user: " + output);
    }
}

// 6. Coordinator - Orchestrates the process
public class CompilerCoordinator {
    private DriverCodeGenerator driverCodeGen;
    private SyntaxChecker syntaxChecker;
    private TestRunner testRunner;
    private DatabaseManager dbManager;
    private UserOutputHandler outputHandler;
    
    public void compile(String code) {
        String codeWithDriver = driverCodeGen.addDriverCode(code);
        
        if (syntaxChecker.checkSyntax(codeWithDriver)) {
            String output = testRunner.runTests(codeWithDriver);
            dbManager.storeOutput(output);
            outputHandler.sendOutput(output);
        }
    }
}
```

**Benefits:**
- Each class has **one clear job**
- Easy to modify any component independently
- Better testing - test each component separately
- Easy to add new features (e.g., new test types)

---

## Advantages of SRP

### 1. **Easier Maintenance**
When a class does only one job, updating or fixing it becomes much easier. You know exactly where to look if there's a problem.

### 2. **Improved Readability**
Code becomes easier to read and understand when each class has a clear, single responsibility.

### 3. **Reduced Risk of Bugs**
If each class does just one thing, changes to one class are less likely to affect others.

### 4. **Easier Testing**
Testing is simpler when a class has one responsibility, as you're only verifying one thing.

### 5. **Supports Code Reuse**
Classes that focus on one task can be reused in different projects or parts of the program.

### 6. **Lower Risk in Changes**
Since each class handles only one concern, changes made to it are less likely to cause unintended side effects.

### 7. **Better Collaboration**
Multiple developers can work on different classes without conflicts.

---

## Common Mistakes

### 1. **Mixing Database Logic with Business Logic**

❌ **Wrong:**
```java
public class OrderService {
    public void processOrder(Order order) {
        // Business logic
        order.calculateTotal();
        
        // Database logic mixed in
        Connection conn = DriverManager.getConnection("...");
        PreparedStatement stmt = conn.prepareStatement("INSERT INTO orders...");
        // SQL code...
    }
}
```

✅ **Correct:**
```java
// Business logic
public class OrderService {
    private OrderRepository repository;
    
    public void processOrder(Order order) {
        order.calculateTotal();
        repository.save(order);
    }
}

// Database logic
public class OrderRepository {
    public void save(Order order) {
        // SQL code here
    }
}
```

---

### 2. **Coupling UI Code with Business Logic**

❌ **Wrong:**
```java
public class LoginForm {
    public void login() {
        String username = usernameField.getText();
        String password = passwordField.getText();
        
        // Business logic embedded in UI
        if (username.equals("admin") && password.equals("pass")) {
            System.out.println("Login successful");
        }
    }
}
```

✅ **Correct:**
```java
// UI Layer
public class LoginForm {
    private AuthService authService;
    
    public void login() {
        String username = usernameField.getText();
        String password = passwordField.getText();
        
        if (authService.authenticate(username, password)) {
            System.out.println("Login successful");
        }
    }
}

// Business Logic Layer
public class AuthService {
    public boolean authenticate(String username, String password) {
        return username.equals("admin") && password.equals("pass");
    }
}
```

---

### 3. **God Classes (Doing Everything)**

❌ **Wrong:**
```java
public class Application {
    public void start() {
        initializeDatabase();
        loadConfiguration();
        startServer();
        setupLogging();
        registerRoutes();
        // ... doing everything
    }
}
```

✅ **Correct:**
```java
public class Application {
    private DatabaseInitializer dbInit;
    private ConfigLoader configLoader;
    private ServerStarter server;
    private LoggerSetup logger;
    private RouteRegistry routes;
    
    public void start() {
        dbInit.initialize();
        configLoader.load();
        server.start();
        logger.setup();
        routes.register();
    }
}
```

---

## Interview Questions & Answers

### Q1: What is the Single Responsibility Principle?

**Answer:**

The Single Responsibility Principle (SRP) states that **a class should have only one reason to change**, meaning it should have only one job or responsibility.

**Example:**
```java
// Bad: Multiple responsibilities
class Employee {
    void calculateSalary() { }
    void saveToDatabase() { }
    void generateReport() { }
}

// Good: Single responsibility
class Employee {
    // Just employee data
}

class SalaryCalculator {
    void calculateSalary(Employee emp) { }
}

class EmployeeRepository {
    void save(Employee emp) { }
}

class ReportGenerator {
    void generate(Employee emp) { }
}
```

---

### Q2: Why is SRP important?

**Answer:**

SRP is important because it:

1. **Reduces coupling** - Changes in one area don't affect others
2. **Improves maintainability** - Easy to find and fix bugs
3. **Enhances testability** - Easier to write unit tests
4. **Increases reusability** - Single-purpose classes can be reused
5. **Simplifies understanding** - Clear, focused classes are easier to read

**Real Example:**
If you change how invoices are printed, you shouldn't have to modify the invoice calculation logic.

---

### Q3: How do you identify if a class violates SRP?

**Answer:**

Ask these questions:

**1. The "Reason to Change" Test**
- How many reasons does this class have to change?
- If more than one → Violates SRP

**2. The "And" Test**
- Can you describe the class using "and"?
- Example: "This class calculates totals **AND** prints reports **AND** saves to database"
- If yes → Violates SRP

**3. The "Multiple Stakeholders" Test**
- Do different people/teams care about different parts of this class?
- If yes → Violates SRP

**Example:**
```java
class UserManager {
    void validateUser() { }      // QA team cares
    void saveUser() { }          // DBA team cares
    void sendEmail() { }         // Marketing team cares
    void generateReport() { }    // Analytics team cares
}
// Multiple stakeholders → Violates SRP
```

---

### Q4: Can you give a real-world example of SRP violation and its fix?

**Answer:**

**Scenario:** E-commerce Product Management

❌ **Violating SRP:**
```java
public class Product {
    private String name;
    private double price;
    
    // Responsibility 1: Product data
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    // Responsibility 2: Validation
    public boolean isValidPrice() {
        return price > 0;
    }
    
    // Responsibility 3: Discount calculation
    public double getDiscountedPrice(double discountPercent) {
        return price * (1 - discountPercent / 100);
    }
    
    // Responsibility 4: Database operations
    public void saveToDatabase() {
        System.out.println("Saving product to DB...");
    }
    
    // Responsibility 5: Formatting for display
    public String formatForDisplay() {
        return "Product: " + name + " - $" + price;
    }
}
```

✅ **Following SRP:**
```java
// 1. Product - Data only
public class Product {
    private String name;
    private double price;
    
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    public String getName() { return name; }
    public double getPrice() { return price; }
}

// 2. ProductValidator - Validation logic
public class ProductValidator {
    public boolean isValidPrice(double price) {
        return price > 0;
    }
}

// 3. DiscountCalculator - Pricing logic
public class DiscountCalculator {
    public double calculateDiscount(Product product, double discountPercent) {
        return product.getPrice() * (1 - discountPercent / 100);
    }
}

// 4. ProductRepository - Database operations
public class ProductRepository {
    public void save(Product product) {
        System.out.println("Saving product to DB...");
    }
}

// 5. ProductFormatter - Display formatting
public class ProductFormatter {
    public String format(Product product) {
        return "Product: " + product.getName() + " - $" + product.getPrice();
    }
}
```

**Benefits:**
- Each class has one clear job
- Easy to change discount logic without touching database code
- Can test each component independently

---

### Q5: Does SRP apply only to classes?

**Answer:**

**No!** SRP can be applied at multiple levels:

**1. Methods**
```java
// Bad: Method doing multiple things
void processUser(User user) {
    validateUser(user);
    saveUser(user);
    sendEmail(user);
    logActivity(user);
}

// Good: Methods with single responsibility
void processUser(User user) {
    if (isValid(user)) {
        save(user);
        notify(user);
        log(user);
    }
}

private boolean isValid(User user) { /* ... */ }
private void save(User user) { /* ... */ }
private void notify(User user) { /* ... */ }
private void log(User user) { /* ... */ }
```

**2. Modules/Packages**
```
com.app.users        // User-related functionality
com.app.products     // Product-related functionality
com.app.orders       // Order-related functionality
```

**3. Microservices**
```
UserService          // Handles users
PaymentService       // Handles payments
NotificationService  // Handles notifications
```

**4. Entire Systems**
- Authentication System
- Billing System
- Reporting System

**Key Point:** SRP is a **mindset** you can apply from the smallest method to the largest system design.

---

### Q7: What are the trade-offs of strictly following SRP?

**Answer:**

**Potential Drawbacks:**

**1. More Classes**
- Following SRP can lead to many small classes
- Can feel overwhelming initially

**2. Indirection**
- More layers between components
- Harder to trace execution flow

**3. Over-engineering for Simple Cases**
```java
// Overkill for a simple script
class NumberPrinter {
    void print(int number) {
        System.out.println(number);
    }
}

// Just do:
System.out.println(number);
```

**Balance:**

✅ **Good:**
```java
// For a production application
class OrderService {
    private OrderValidator validator;
    private OrderRepository repository;
    private EmailService emailService;
}
```

❌ **Over-engineering:**
```java
// For a simple calculator
class AdditionOperation { }
class SubtractionOperation { }
class MultiplicationOperation { }
class DivisionOperation { }
// ... too much for a simple case
```

**Rule of Thumb:**
- Apply SRP when responsibilities are **truly distinct**
- Don't split just for the sake of splitting
- Consider the **context and complexity** of your application

---

### Q9: Give an example of SRP in a real production system.

**Answer:**

**Real Example: Authentication System**

```java
// 1. User Model - Data only
public class User {
    private String username;
    private String passwordHash;
    private String email;
    
    // Getters and setters only
}

// 2. Password Encoder - Encoding logic
public class PasswordEncoder {
    public String encode(String plainPassword) {
        // BCrypt hashing logic
        return BCrypt.hashpw(plainPassword, BCrypt.gensalt());
    }
    
    public boolean matches(String plainPassword, String hashedPassword) {
        return BCrypt.checkpw(plainPassword, hashedPassword);
    }
}

// 3. User Repository - Database operations
public class UserRepository {
    public User findByUsername(String username) {
        // Database query
        return database.query("SELECT * FROM users WHERE username = ?", username);
    }
    
    public void save(User user) {
        // Insert or update logic
    }
}

// 4. Token Generator - JWT token creation
public class TokenGenerator {
    public String generateToken(User user) {
        // Create JWT token
        return JWT.create()
            .withSubject(user.getUsername())
            .withExpiresAt(Date.from(Instant.now().plus(24, ChronoUnit.HOURS)))
            .sign(Algorithm.HMAC256(SECRET));
    }
}

// 5. Authentication Service - Orchestration
public class AuthenticationService {
    private UserRepository userRepository;
    private PasswordEncoder passwordEncoder;
    private TokenGenerator tokenGenerator;
    
    public AuthResult authenticate(String username, String password) {
        User user = userRepository.findByUsername(username);
        
        if (user == null) {
            return AuthResult.failure("User not found");
        }
        
        if (!passwordEncoder.matches(password, user.getPasswordHash())) {
            return AuthResult.failure("Invalid password");
        }
        
        String token = tokenGenerator.generateToken(user);
        return AuthResult.success(token);
    }
}
```

**Why This Works:**
- **PasswordEncoder** only handles encoding
- **UserRepository** only handles database
- **TokenGenerator** only creates tokens
- **AuthenticationService** orchestrates but delegates work
- Easy to swap BCrypt for Argon2
- Easy to change from JWT to session tokens
- Each component can be tested independently

---

### Q10: What's the difference between SRP and Separation of Concerns?

**Answer:**

While related, they're different concepts:

**Single Responsibility Principle (SRP):**
- **Scope:** Class/Module level
- **Focus:** One reason to change
- **Question:** "Does this class have one job?"

**Separation of Concerns (SoC):**
- **Scope:** System/Architecture level
- **Focus:** Dividing system by concerns
- **Question:** "Are different concerns separated?"

**Example:**

```java
// SRP - Each CLASS has one responsibility
class UserValidator {
    boolean validate(User user) { /* ... */ }
}

class UserRepository {
    void save(User user) { /* ... */ }
}

// SoC - Different CONCERNS separated into layers
// Presentation Layer
class UserController {
    // Handles HTTP requests
}

// Business Logic Layer
class UserService {
    // Business rules
}

// Data Access Layer
class UserRepository {
    // Database operations
}
```

**Relationship:**
- **SoC** is the broader architectural principle
- **SRP** is a specific implementation at the class level
- **SRP helps achieve SoC**

---

## Best Practices

### ✅ Do's

1. **Ask "What does this class do?"**
   - If you use "and" in the answer → Consider splitting

2. **One Reason to Change**
   - If multiple teams care about the same class → Split it

3. **Keep Classes Small**
   - 100-200 lines is a good guideline

4. **Use Descriptive Names**
   - `UserValidator` instead of `UserManager`

5. **Test Individual Components**
   - Each class should be testable in isolation

### ❌ Don'ts

1. **Don't Create God Classes**
   - Avoid `Manager`, `Handler`, `Utility` classes that do everything

2. **Don't Over-Split**
   - Don't create a class for every single method

3. **Don't Ignore Context**
   - A simple script doesn't need perfect SRP

4. **Don't Mix Concerns**
   - Keep UI separate from business logic
   - Keep database separate from business logic

---

## Conclusion

**Single Responsibility Principle** is about creating focused, maintainable code where each class does one thing well.

**Remember:**
- ✅ One class = One responsibility
- ✅ One reason to change
- ✅ Easier to test, maintain, and reuse

**The Golden Rule:**
> "A class should have only one reason to change."

---

**Happy Coding! 🚀**

*Last Updated: January 2026*
