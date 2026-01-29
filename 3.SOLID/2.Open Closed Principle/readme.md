# Open-Closed Principle (OCP) - Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Definition](#definition)
3. [Real-Life Analogy](#real-life-analogy)
4. [Simple Example](#simple-example)
5. [Real-Time Examples](#real-time-examples)
6. [Advantages of OCP](#advantages-of-ocp)
7. [When to Apply OCP](#when-to-apply-ocp)
8. [Common Misconceptions](#common-misconceptions)
9. [Interview Questions & Answers](#interview-questions--answers)
10. [Best Practices](#best-practices)

---

## Introduction

**Open-Closed Principle (OCP)** states that software entities like classes, modules, and functions should be **open for extension but closed for modification**.

**Core Idea:**
- **Open for Extension:** You can add new features or behaviors
- **Closed for Modification:** Existing code should remain untouched

![OCP Concept](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*0MtFBmm6L2WVM04qCJOZPQ.png)

The main idea is to **avoid modifying existing code** to prevent bugs and keep the system stable. Instead, add new features by creating new classes or methods.

---

## Definition

**Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.**

This means:
- The **behavior of a module can be extended** without modifying its source code
- The goal is to **reduce the risk of breaking existing functionality** when requirements change

**Key Principle:**
> Changing the current behaviour of a Class will affect all the systems using that Class. If you want the Class to perform more functions, the ideal approach is to **add** to the functions that already exist, **NOT change** them.

---

## Real-Life Analogy

### Power Adapter Example

Imagine you travel from **India to the UK**. Your Indian charger doesn't fit into UK power sockets.

**Solution:** Use a travel adapter

✅ **The adapter extends** your existing charger's usability (now works in UK)  
✅ **You did not modify** the charger itself

**Code Parallel:**
- **Charger** = Existing stable code
- **Adapter** = Extension/New feature
- **No modification** = Original code stays unchanged

Similarly, in code, OCP encourages **adding new functionality via extension**, rather than altering existing, stable code.

---

## Simple Example

### Shape Area Calculator

#### ❌ Without OCP (Violating the Principle)

```java
class Circle {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    public double getRadius() {
        return radius;
    }
}

class AreaCalculator {
    public double calculateCircleArea(Circle circle) {
        return Math.PI * circle.getRadius() * circle.getRadius();
    }
    
    // Adding Rectangle requires MODIFYING this class
    public double calculateRectangleArea(Rectangle rectangle) {
        return rectangle.getWidth() * rectangle.getHeight();
    }
    
    // Adding Triangle requires MODIFYING this class again
    public double calculateTriangleArea(Triangle triangle) {
        return 0.5 * triangle.getBase() * triangle.getHeight();
    }
}
```

**Problems:**
- Every new shape requires **modifying** AreaCalculator
- Risk of breaking existing functionality
- Violates OCP

---

#### ✅ With OCP (Following the Principle)

**1. Define the Shape Interface**

```java
interface Shape {
    double calculateArea();
}
```

**2. Create Shape Implementations**

```java
class Circle implements Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

class Rectangle implements Shape {
    private double width;
    private double height;
    
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double calculateArea() {
        return width * height;
    }
}

class Triangle implements Shape {
    private double base;
    private double height;
    
    public Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }
    
    @Override
    public double calculateArea() {
        return 0.5 * base * height;
    }
}
```

**3. Update AreaCalculator (No Future Modifications Needed)**

```java
class AreaCalculator {
    public double calculateArea(Shape shape) {
        return shape.calculateArea();
    }
}
```

**4. Usage**

```java
public class Main {
    public static void main(String[] args) {
        AreaCalculator calculator = new AreaCalculator();
        
        Shape circle = new Circle(5);
        Shape rectangle = new Rectangle(4, 6);
        Shape triangle = new Triangle(3, 4);
        
        System.out.println("Circle Area: " + calculator.calculateArea(circle));
        System.out.println("Rectangle Area: " + calculator.calculateArea(rectangle));
        System.out.println("Triangle Area: " + calculator.calculateArea(triangle));
    }
}
```

**Benefits:**
- ✅ **Open for Extension:** Add new shapes by creating new classes
- ✅ **Closed for Modification:** AreaCalculator never needs to change
- ✅ No risk of breaking existing code

---

## Real-Time Examples

### Example 1: Tax Calculation System

**Scenario:** An invoicing system that handles tax rules for different regions:
- India: GST 18%
- US: Sales Tax 8%
- UK: VAT 12%

#### ❌ Without OCP (Bad Design)

```java
public class InvoiceProcessor {
    public double calculateTotal(String region, double amount) {
        if (region.equals("India")) {
            return amount + amount * 0.18;
        } else if (region.equals("US")) {
            return amount + amount * 0.08;
        } else if (region.equals("UK")) {
            return amount + amount * 0.12;
        } else {
            return amount; // No tax for unknown region
        }
    }
}
```

**Problems:**
- Adding Germany requires **modifying** this method
- Risk of breaking existing logic
- Hard to test and maintain
- Violates OCP

---

#### ✅ With OCP (Good Design)

**1. Define Tax Strategy Interface**

```java
interface TaxCalculator {
    double calculateTax(double amount);
}
```

**2. Implement Region-Specific Tax Calculators**

```java
class IndiaTaxCalculator implements TaxCalculator {
    @Override
    public double calculateTax(double amount) {
        return amount * 0.18; // GST
    }
}

class USTaxCalculator implements TaxCalculator {
    @Override
    public double calculateTax(double amount) {
        return amount * 0.08; // Sales Tax
    }
}

class UKTaxCalculator implements TaxCalculator {
    @Override
    public double calculateTax(double amount) {
        return amount * 0.12; // VAT
    }
}
```

**3. Invoice Class (Using Dependency Injection)**

```java
public class Invoice {
    private double amount;
    private TaxCalculator taxCalculator;
    
    public Invoice(double amount, TaxCalculator taxCalculator) {
        this.amount = amount;
        this.taxCalculator = taxCalculator;
    }
    
    public double getTotalAmount() {
        return amount + taxCalculator.calculateTax(amount);
    }
}
```

**4. Usage**

```java
public class Main {
    public static void main(String[] args) {
        double amount = 1000.0;
        
        Invoice indiaInvoice = new Invoice(amount, new IndiaTaxCalculator());
        System.out.println("Total (India): ₹" + indiaInvoice.getTotalAmount());
        
        Invoice usInvoice = new Invoice(amount, new USTaxCalculator());
        System.out.println("Total (US): $" + usInvoice.getTotalAmount());
        
        Invoice ukInvoice = new Invoice(amount, new UKTaxCalculator());
        System.out.println("Total (UK): £" + ukInvoice.getTotalAmount());
    }
}
```

**Adding Germany (No Modification Required):**

```java
class GermanyTaxCalculator implements TaxCalculator {
    @Override
    public double calculateTax(double amount) {
        return amount * 0.15; // VAT for Germany
    }
}

// Usage
Invoice germanyInvoice = new Invoice(1000.0, new GermanyTaxCalculator());
```

**Benefits:**
- ✅ No changes to Invoice or Main classes
- ✅ Easy to add new regions
- ✅ Each tax calculator is independent and testable

---

### Example 2: Payment Processing System

#### ❌ Without OCP

```java
public class PaymentProcessor {
    public void processPayment(String paymentType, double amount) {
        if (paymentType.equals("CreditCard")) {
            System.out.println("Processing credit card payment: $" + amount);
            // Credit card logic
        } else if (paymentType.equals("PayPal")) {
            System.out.println("Processing PayPal payment: $" + amount);
            // PayPal logic
        } else if (paymentType.equals("Bitcoin")) {
            System.out.println("Processing Bitcoin payment: $" + amount);
            // Bitcoin logic
        }
    }
}
```

#### ✅ With OCP

```java
// 1. Payment Strategy Interface
interface PaymentMethod {
    void processPayment(double amount);
}

// 2. Concrete Implementations
class CreditCardPayment implements PaymentMethod {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing credit card payment: $" + amount);
    }
}

class PayPalPayment implements PaymentMethod {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing PayPal payment: $" + amount);
    }
}

class BitcoinPayment implements PaymentMethod {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing Bitcoin payment: $" + amount);
    }
}

// 3. Payment Processor (Closed for Modification)
public class PaymentProcessor {
    public void processPayment(PaymentMethod paymentMethod, double amount) {
        paymentMethod.processPayment(amount);
    }
}

// 4. Usage
PaymentProcessor processor = new PaymentProcessor();
processor.processPayment(new CreditCardPayment(), 100.0);
processor.processPayment(new PayPalPayment(), 200.0);
```

**Adding Apple Pay:**

```java
class ApplePayPayment implements PaymentMethod {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing Apple Pay payment: $" + amount);
    }
}

// No modification to PaymentProcessor needed!
processor.processPayment(new ApplePayPayment(), 300.0);
```

---

### Example 3: Notification System

```java
// 1. Notification Interface
interface Notification {
    void send(String message);
}

// 2. Implementations
class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}

class SMSNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

class PushNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending Push Notification: " + message);
    }
}

// 3. Notification Service (Open for Extension, Closed for Modification)
public class NotificationService {
    public void notify(Notification notification, String message) {
        notification.send(message);
    }
}

// 4. Usage
NotificationService service = new NotificationService();
service.notify(new EmailNotification(), "Welcome!");
service.notify(new SMSNotification(), "Your code is 1234");

// Adding Slack notifications - no changes to NotificationService
class SlackNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending Slack message: " + message);
    }
}

service.notify(new SlackNotification(), "Meeting at 3 PM");
```

---

## Advantages of OCP

### 1. **Reduces Bugs**
By not modifying existing code, you avoid introducing new bugs into tested and stable code.

### 2. **Easier Maintenance**
Each change or addition is isolated, making the system easier to maintain and understand.

### 3. **Better Team Collaboration**
Multiple developers can add features without worrying about breaking or conflicting with each other's code.

### 4. **Scalability**
Systems designed with OCP are more adaptable to future changes, making them scalable and flexible.

### 5. **Improved Testability**
New extensions can be tested independently without retesting existing code.

### 6. **Reduced Regression Risk**
Stable modules remain untouched, minimizing the risk of regression bugs.

---

## When to Apply OCP

Apply OCP in these scenarios:

### ✅ **When to Use:**

1. **Expected Changes:** Module is expected to evolve due to shifting business or technical requirements

2. **Extension Needs:** Need to extend functionality without modifying existing, tested code

3. **Extensible Systems:** Developing frameworks, plugins, or extensible systems like:
   - Billing engines
   - Tax calculators
   - UI components
   - Payment gateways

4. **Production-Ready Code:** Safeguarding stable, production-ready modules from regression

5. **God Class Detected:** A class is handling too many responsibilities or branching logic

### ❌ **When NOT to Use:**

- **Small, Simple Projects:** Over-engineering simple code
- **No Clear Extension Pattern:** Applying preemptively without clear needs
- **Prototype/POC:** Quick prototypes where flexibility isn't needed yet

**Golden Rule:** Apply OCP in response to **observed patterns of change** or **well-understood need for scalability**.

---

## Common Misconceptions

### Misconception 1: "Code should never be changed again"

❌ **Wrong:** This overlooks the intent of OCP

✅ **Correct:** OCP emphasizes avoiding changes to **core logic** while allowing behavior to be extended safely

---

### Misconception 2: "OCP leads to too many classes, so it's overkill"

❌ **Wrong:** Too many classes is bad

✅ **Correct:** The trade-off typically improves modularity, testability, and maintainability in systems expected to evolve

---

### Misconception 3: "OCP makes code harder to read"

❌ **Wrong:** Abstractions always complicate code

✅ **Correct:** In systems with complex behavior or frequent changes, well-structured extensibility improves clarity by separating concerns and reducing conditional logic

---

### Misconception 4: "OCP should always be applied upfront"

❌ **Wrong:** Always use interfaces from day one

✅ **Correct:** Applying OCP preemptively can result in unnecessary abstraction. It's often more effective when used in response to emerging patterns of change

---

### Misconception 5: "Refactoring contradicts OCP"

❌ **Wrong:** Refactoring means modifying code, which violates OCP

✅ **Correct:** Refactoring is often a step **toward** making code compliant with OCP by improving structure and extensibility

---

### Misconception 6: "OCP makes retesting legacy code unnecessary"

❌ **Wrong:** No need to test if we didn't modify the class

✅ **Correct:** While OCP reduces modification needs, new extensions still require thorough testing to ensure correctness and integration

---

## Interview Questions & Answers

### Q1: What is the Open-Closed Principle?

**Answer:**

The Open-Closed Principle (OCP) states that **software entities should be open for extension but closed for modification**.

**Meaning:**
- **Open for Extension:** You can add new functionality
- **Closed for Modification:** You shouldn't change existing code

**Simple Example:**
```java
// Bad: Modifying class for each new shape
class AreaCalculator {
    double calculate(Object shape) {
        if (shape instanceof Circle) { /* ... */ }
        else if (shape instanceof Rectangle) { /* ... */ }
        // Adding Triangle means MODIFYING this method
    }
}

// Good: Extending without modification
interface Shape {
    double calculateArea();
}

class AreaCalculator {
    double calculate(Shape shape) {
        return shape.calculateArea(); // No modification needed for new shapes
    }
}
```

---

### Q2: Why is OCP important?

**Answer:**

OCP is important because it:

1. **Prevents Breaking Changes:** Existing, tested code remains stable
2. **Reduces Bugs:** No risk of introducing bugs in working code
3. **Improves Scalability:** Easy to add new features
4. **Enhances Maintainability:** Changes are isolated
5. **Supports Team Collaboration:** Multiple developers can extend without conflicts

**Real-World Impact:**
Imagine a payment system handling CreditCard and PayPal. If you need to add Bitcoin, OCP ensures you can add it without touching (and potentially breaking) existing payment logic.

---

### Q3: How do you implement OCP in practice?

**Answer:**

**Main Techniques:**

**1. Use Interfaces/Abstract Classes**
```java
interface PaymentMethod {
    void pay(double amount);
}

class CreditCard implements PaymentMethod { }
class PayPal implements PaymentMethod { }
```

**2. Strategy Pattern**
```java
class PaymentProcessor {
    void process(PaymentMethod method, double amount) {
        method.pay(amount);
    }
}
```

**3. Polymorphism**
```java
// Works with any PaymentMethod implementation
PaymentMethod payment = new CreditCard();
processor.process(payment, 100.0);
```

**4. Dependency Injection**
```java
class OrderService {
    private PaymentMethod paymentMethod;
    
    // Inject the payment method
    public OrderService(PaymentMethod paymentMethod) {
        this.paymentMethod = paymentMethod;
    }
}
```

---

### Q4: Give a real-world example of OCP violation and its fix.

**Answer:**

**Scenario:** Discount Calculation System

❌ **Violating OCP:**

```java
public class DiscountCalculator {
    public double calculateDiscount(String customerType, double amount) {
        if (customerType.equals("Regular")) {
            return amount * 0.0; // No discount
        } else if (customerType.equals("Premium")) {
            return amount * 0.1; // 10% discount
        } else if (customerType.equals("VIP")) {
            return amount * 0.2; // 20% discount
        }
        return 0;
    }
}
```

**Problems:**
- Adding "Gold" customer requires modifying the method
- If-else chain grows with each new customer type
- Hard to test each discount type separately

---

✅ **Following OCP:**

```java
// 1. Strategy Interface
interface DiscountStrategy {
    double calculateDiscount(double amount);
}

// 2. Concrete Strategies
class RegularCustomerDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double amount) {
        return 0; // No discount
    }
}

class PremiumCustomerDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double amount) {
        return amount * 0.1; // 10%
    }
}

class VIPCustomerDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double amount) {
        return amount * 0.2; // 20%
    }
}

// 3. Calculator (Closed for Modification)
public class DiscountCalculator {
    public double calculateDiscount(DiscountStrategy strategy, double amount) {
        return strategy.calculateDiscount(amount);
    }
}

// 4. Adding Gold customer (Extension, not Modification)
class GoldCustomerDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double amount) {
        return amount * 0.15; // 15%
    }
}
```

---

### Q5: What's the difference between OCP and adding new methods to a class?

**Answer:**

**Key Difference:**

**Adding Methods (Violates OCP):**
```java
class ReportGenerator {
    void generatePDFReport() { /* ... */ }
    
    // Later: Need Excel support - MODIFY class
    void generateExcelReport() { /* ... */ }
    
    // Later: Need CSV support - MODIFY class again
    void generateCSVReport() { /* ... */ }
}
```
❌ **Problem:** Class keeps being modified

---

**OCP Approach (Extension):**
```java
interface ReportGenerator {
    void generate();
}

class PDFReportGenerator implements ReportGenerator {
    void generate() { /* PDF logic */ }
}

class ExcelReportGenerator implements ReportGenerator {
    void generate() { /* Excel logic */ }
}

class CSVReportGenerator implements ReportGenerator {
    void generate() { /* CSV logic */ }
}

// Report Service (Never Modified)
class ReportService {
    void createReport(ReportGenerator generator) {
        generator.generate();
    }
}
```
✅ **Benefit:** ReportService never changes, only extended with new generators

**The Difference:**
- **Adding methods** = Modification (changes existing class)
- **OCP** = Extension (adds new classes without changing existing ones)

---

### Q6: When should you NOT apply OCP?

**Answer:**

**Scenarios to Avoid OCP:**

**1. Simple, Stable Code**
```java
// Don't over-engineer this
class Calculator {
    int add(int a, int b) {
        return a + b;
    }
}

// No need for:
interface Operation { }
class Addition implements Operation { }
```

**2. Requirements Unlikely to Change**
```java
// If your app will ALWAYS only send emails
class EmailService {
    void send(String message) { /* ... */ }
}
// Don't create NotificationInterface "just in case"
```

**3. Prototypes/POCs**
- Speed matters more than extensibility
- Requirements are uncertain

**4. Over-Abstraction Warning Signs**
- More interfaces than implementations
- Abstractions with only one concrete class
- Code harder to understand than without OCP

**Balance Rule:**
> Apply OCP when you **see a pattern of change**, not just because you imagine future possibilities.

---

### Q7: How does OCP relate to the Strategy Pattern?

**Answer:**

**Strategy Pattern is a PRIMARY way to implement OCP.**

**Strategy Pattern Structure:**
```java
// 1. Strategy Interface
interface SortStrategy {
    void sort(int[] array);
}

// 2. Concrete Strategies (Extensions)
class QuickSort implements SortStrategy {
    void sort(int[] array) { /* Quick sort */ }
}

class MergeSort implements SortStrategy {
    void sort(int[] array) { /* Merge sort */ }
}

class BubbleSort implements SortStrategy {
    void sort(int[] array) { /* Bubble sort */ }
}

// 3. Context (Closed for Modification)
class Sorter {
    private SortStrategy strategy;
    
    public Sorter(SortStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void performSort(int[] array) {
        strategy.sort(array);
    }
}
```

**OCP Compliance:**
- ✅ **Open for Extension:** Add new sorting algorithms
- ✅ **Closed for Modification:** Sorter class never changes
- ✅ Each strategy is independent and testable

**Other OCP Patterns:**
- **Template Method Pattern**
- **Observer Pattern**
- **Decorator Pattern**
- **Factory Pattern**

---

### Q8: Can you show OCP in action with a logging system?

**Answer:**

**Scenario:** Application needs flexible logging to different destinations

```java
// 1. Logger Interface
interface Logger {
    void log(String message);
}

// 2. Concrete Loggers (Extensions)
class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("[CONSOLE] " + message);
    }
}

class FileLogger implements Logger {
    private String filename;
    
    public FileLogger(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void log(String message) {
        // Write to file
        System.out.println("[FILE:" + filename + "] " + message);
    }
}

class DatabaseLogger implements Logger {
    @Override
    public void log(String message) {
        // Insert into database
        System.out.println("[DATABASE] " + message);
    }
}

// 3. Application Service (Closed for Modification)
class UserService {
    private Logger logger;
    
    public UserService(Logger logger) {
        this.logger = logger;
    }
    
    public void createUser(String username) {
        // Business logic
        logger.log("User created: " + username);
    }
}

// 4. Usage
UserService service1 = new UserService(new ConsoleLogger());
service1.createUser("Alice");

UserService service2 = new UserService(new FileLogger("app.log"));
service2.createUser("Bob");

// 5. Adding CloudLogger (Extension, not Modification)
class CloudLogger implements Logger {
    @Override
    public void log(String message) {
        // Send to cloud service
        System.out.println("[CLOUD] " + message);
    }
}

UserService service3 = new UserService(new CloudLogger());
service3.createUser("Charlie");
```

**OCP Benefits:**
- UserService never changes when adding new log destinations
- Each logger is independently testable
- Easy to switch logging strategies

---

### Q9: How do you refactor existing code to follow OCP?

**Answer:**

**Step-by-Step Refactoring Process:**

**Original Code:**
```java
class OrderProcessor {
    void processOrder(Order order) {
        if (order.getType().equals("Online")) {
            // Online order logic
            validateOnlinePayment(order);
            checkDigitalInventory(order);
            sendEmailConfirmation(order);
        } else if (order.getType().equals("Store")) {
            // Store order logic
            checkPhysicalInventory(order);
            printReceipt(order);
        }
    }
}
```

**Step 1: Identify Variation Points**
- Different order types have different processing logic

**Step 2: Extract Interface**
```java
interface OrderProcessingStrategy {
    void process(Order order);
}
```

**Step 3: Create Concrete Strategies**
```java
class OnlineOrderProcessor implements OrderProcessingStrategy {
    @Override
    public void process(Order order) {
        validateOnlinePayment(order);
        checkDigitalInventory(order);
        sendEmailConfirmation(order);
    }
}

class StoreOrderProcessor implements OrderProcessingStrategy {
    @Override
    public void process(Order order) {
        checkPhysicalInventory(order);
        printReceipt(order);
    }
}
```

**Step 4: Update Main Class**
```java
class OrderProcessor {
    private OrderProcessingStrategy strategy;
    
    public OrderProcessor(OrderProcessingStrategy strategy) {
        this.strategy = strategy;
    }
    
    void processOrder(Order order) {
        strategy.process(order);
    }
}
```

**Step 5: Use Dependency Injection**
```java
// Usage
OrderProcessor onlineProcessor = new OrderProcessor(new OnlineOrderProcessor());
onlineProcessor.processOrder(onlineOrder);

OrderProcessor storeProcessor = new OrderProcessor(new StoreOrderProcessor());
storeProcessor.processOrder(storeOrder);
```

**Benefits:**
- Adding "PhoneOrder" type doesn't require modifying OrderProcessor
- Each strategy is independently testable
- Follows OCP perfectly

---

### Q10: What are common code smells that indicate OCP violation?

**Answer:**

**Red Flags:**

**1. Long If-Else or Switch Statements**
```java
// Code smell
if (type.equals("A")) { }
else if (type.equals("B")) { }
else if (type.equals("C")) { }
// Adding type D requires modification
```

**2. Type Checking with instanceof**
```java
// Code smell
if (payment instanceof CreditCard) { }
else if (payment instanceof PayPal) { }
else if (payment instanceof Bitcoin) { }
```

**3. Hard-coded Type Strings**
```java
// Code smell
if (userType.equals("Premium")) { }
else if (userType.equals("Regular")) { }
```

**4. Growing Method with Each New Feature**
```java
// Code smell
void generateReport() {
    // v1.0: PDF support
    // v1.1: Added Excel support (modified method)
    // v1.2: Added CSV support (modified method again)
    // v1.3: Added HTML support (modified method again)
}
```

**5. Comments Like "TODO: Add support for..."**
```java
// Code smell
void process(String type) {
    if (type.equals("email")) { }
    // TODO: Add SMS support
    // TODO: Add push notification support
}
```

**Solutions:**
- Replace conditionals with polymorphism
- Use Strategy/Template pattern
- Extract variations into separate classes
- Depend on abstractions (interfaces)

---

## Best Practices

### ✅ Do's

1. **Use Abstractions**
   - Depend on interfaces/abstract classes, not concrete implementations

2. **Apply Strategy Pattern**
   - When you have multiple algorithms or behaviors

3. **Identify Variation Points**
   - Look for code that changes frequently

4. **Use Dependency Injection**
   - Pass dependencies from outside rather than creating them internally

5. **Keep Interfaces Focused**
   - Single-method interfaces are often ideal (Interface Segregation)

### ❌ Don'ts

1. **Don't Apply Prematurely**
   - Wait for patterns of change to emerge

2. **Don't Over-Abstract**
   - Not every class needs an interface

3. **Don't Create Interfaces with One Implementation**
   - Unless you're certain more will follow

4. **Don't Sacrifice Readability**
   - If abstraction makes code harder to understand, reconsider

5. **Don't Forget Testing**
   - New extensions still need thorough testing

---

## Conclusion

The **Open-Closed Principle** helps create flexible, maintainable systems by allowing extension without modification.

**Key Takeaways:**
- ✅ **Open for Extension:** Add new features easily
- ✅ **Closed for Modification:** Don't change existing code
- ✅ Use interfaces and polymorphism
- ✅ Apply when you see patterns of change
- ✅ Balance flexibility with simplicity

**Remember:**
> "Software entities should be open for extension, but closed for modification."

---

**Happy Coding! 🚀**

*Last Updated: January 2026*
