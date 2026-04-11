# Antino Labs Interview Questions - Complete Answers Guide

## 🔹 1. Core Java & OOP

### What is OOP in Java? Explain its principles.

**Object-Oriented Programming (OOP)** is a programming paradigm that organizes code around "objects" rather than functions and logic. An object contains data (fields) and code (methods) that operates on that data.

**Four Main Principles of OOP:**

**1. Encapsulation:** Bundling data and methods together while hiding internal details. It's like a capsule where you don't need to know what's inside to use it.

**2. Inheritance:** Creating new classes based on existing classes, reusing code and establishing parent-child relationships.

**3. Polymorphism:** The ability of an object to take many forms. One interface, multiple implementations.

**4. Abstraction:** Hiding complex implementation details and showing only essential features.

### Explain Encapsulation with example

**Encapsulation** means wrapping data (variables) and code (methods) together as a single unit, and restricting direct access to some components.

**Example:**
```java
public class BankAccount {
    // Private fields - hidden from outside
    private double balance;
    private String accountNumber;
    
    // Constructor
    public BankAccount(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber;
        if (initialBalance > 0) {
            this.balance = initialBalance;
        }
    }
    
    // Public getter - controlled access
    public double getBalance() {
        return balance;
    }
    
    // Public setter with validation
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited: " + amount);
        } else {
            System.out.println("Invalid deposit amount");
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println("Withdrawn: " + amount);
        } else {
            System.out.println("Insufficient balance or invalid amount");
        }
    }
}

// Usage
BankAccount account = new BankAccount("ACC123", 1000);
account.deposit(500);      // Works
account.withdraw(200);     // Works
// account.balance = 5000; // ERROR! Cannot access private field directly
```

**Benefits:** Data hiding, validation control, flexibility to change internal implementation.

### What is Abstraction and how is it achieved?

**Abstraction** is the concept of hiding implementation details and showing only essential features to the user. It focuses on WHAT an object does rather than HOW it does it.

**Real-world analogy:** When you drive a car, you only see steering wheel, pedals, gear. You don't need to know how the engine works internally.

**How Abstraction is achieved in Java:**

**1. Using Abstract Classes (0-100% abstraction):**
```java
abstract class Vehicle {
    abstract void start();  // No implementation
    
    void stop() {           // Can have concrete method
        System.out.println("Vehicle stopped");
    }
}

class Car extends Vehicle {
    void start() {
        System.out.println("Car starts with key");
    }
}
```

**2. Using Interfaces (100% abstraction - Java 8+ has default methods):**
```java
interface Drawable {
    void draw();  // Abstract by default
    
    default void print() {  // Default method (Java 8+)
        System.out.println("Printing...");
    }
}

class Circle implements Drawable {
    public void draw() {
        System.out.println("Drawing circle");
    }
}
```

### Difference between Abstract Class and Interface

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| **Keyword** | `abstract class` | `interface` |
| **Multiple Inheritance** | No (can extend only one) | Yes (can implement multiple) |
| **Instance Variables** | Can have | Only static final (constants) |
| **Constructors** | Yes | No |
| **Access Modifiers** | All (private, protected, public) | Only public (default before Java 9) |
| **Method Implementation** | Can have both abstract & concrete | Before Java 8: only abstract; Java 8+: default & static; Java 9+: private |
| **When to use** | When classes share common code | When defining a capability/contract |

### What are Classes and Objects?

**Class:** A blueprint or template from which objects are created. It defines properties (fields) and behaviors (methods).

**Object:** An instance of a class - a concrete entity that occupies memory.

```java
// Class definition (Blueprint)
class Student {
    String name;
    int rollNo;
    
    void study() {
        System.out.println(name + " is studying");
    }
}

// Creating objects (Instances)
public class Main {
    public static void main(String[] args) {
        Student student1 = new Student();  // Object 1
        student1.name = "John";
        student1.rollNo = 101;
        student1.study();
        
        Student student2 = new Student();  // Object 2 - separate memory
        student2.name = "Emma";
        student2.rollNo = 102;
        student2.study();
    }
}
```

**Analogy:** Class = Cookie Cutter, Objects = Actual Cookies made from that cutter.

### What are Getters and Setters?

**Getters and Setters** are methods used to access and modify private fields of a class, following the encapsulation principle.

```java
public class Employee {
    private String name;
    private int age;
    private double salary;
    
    // Getter - retrieves the value
    public String getName() {
        return name;
    }
    
    // Setter - sets/modifies the value with validation
    public void setName(String name) {
        if (name != null && !name.trim().isEmpty()) {
            this.name = name;
        } else {
            throw new IllegalArgumentException("Name cannot be empty");
        }
    }
    
    public int getAge() {
        return age;
    }
    
    public void setAge(int age) {
        if (age >= 18 && age <= 65) {
            this.age = age;
        } else {
            throw new IllegalArgumentException("Age must be between 18-65");
        }
    }
    
    // Read-only property (only getter, no setter)
    public double getSalary() {
        return salary;
    }
    
    // Write-only or controlled setter
    public void setSalary(double salary) {
        if (salary >= 0) {
            this.salary = salary;
        }
    }
}
```

**Benefits:** Validation control, read-only/write-only properties, encapsulation, flexibility to change internal representation.

### What is a Singleton Class? Implement it

**Singleton Class** ensures that only ONE instance of the class is created throughout the application lifecycle. It provides a global access point to that instance.

**Implementation approaches:**

**1. Eager Initialization (Thread-safe, but instance created even if never used):**
```java
public class SingletonEager {
    // Create instance at class loading time
    private static final SingletonEager INSTANCE = new SingletonEager();
    
    // Private constructor prevents external instantiation
    private SingletonEager() {}
    
    // Global access point
    public static SingletonEager getInstance() {
        return INSTANCE;
    }
    
    public void showMessage() {
        System.out.println("Singleton instance: " + this.hashCode());
    }
}
```

**2. Lazy Initialization with Double-Checked Locking (Thread-safe, efficient):**
```java
public class SingletonLazy {
    // Volatile ensures visibility across threads
    private static volatile SingletonLazy instance;
    
    private SingletonLazy() {}
    
    public static SingletonLazy getInstance() {
        if (instance == null) {  // First check (no locking)
            synchronized (SingletonLazy.class) {
                if (instance == null) {  // Second check (with locking)
                    instance = new SingletonLazy();
                }
            }
        }
        return instance;
    }
}
```

**3. Bill Pugh Singleton (Most recommended - uses static inner class):**
```java
public class SingletonBillPugh {
    private SingletonBillPugh() {}
    
    // Static inner class - loaded only when getInstance() is called
    private static class SingletonHelper {
        private static final SingletonBillPugh INSTANCE = new SingletonBillPugh();
    }
    
    public static SingletonBillPugh getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

**Use cases:** Database connection pools, logging, configuration managers, cache managers.

### What is a Sealed Class (Java 17)?

**Sealed Class** is a feature introduced in Java 17 that allows you to control which classes or interfaces can extend or implement a given class/interface.

```java
// Sealed class with permits clause - only these classes can extend Vehicle
sealed class Vehicle permits Car, Bike, Truck {
    protected String brand;
    
    void move() {
        System.out.println("Vehicle is moving");
    }
}

// Must be either final, sealed, or non-sealed
final class Car extends Vehicle {
    @Override
    void move() {
        System.out.println("Car is driving on roads");
    }
}

non-sealed class Bike extends Vehicle {
    @Override
    void move() {
        System.out.println("Bike is zooming");
    }
}

sealed class Truck extends Vehicle permits HeavyTruck {
    // Truck specific code
}

final class HeavyTruck extends Truck {
    // Allowed because HeavyTruck is permitted by Truck
}

// This would cause compilation error:
// class Bus extends Vehicle {} // ERROR! Bus not permitted

// Pattern matching with sealed classes (Java 17+)
public void testVehicle(Vehicle v) {
    if (v instanceof Car car) {
        System.out.println("It's a " + car.brand);
    }
}
```

**Benefits:** Domain modeling, exhaustive pattern matching, better API design control.

### What is a Prototype design pattern?

**Prototype Pattern** creates new objects by cloning an existing object (prototype) rather than calling constructors. It's useful when object creation is expensive.

```java
// Step 1: Implement Cloneable interface
abstract class Shape implements Cloneable {
    protected String type;
    protected int x, y;
    
    abstract void draw();
    
    @Override
    public Shape clone() {
        try {
            return (Shape) super.clone();
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
}

class Circle extends Shape {
    private int radius;
    
    public Circle() {
        type = "Circle";
    }
    
    @Override
    void draw() {
        System.out.println("Drawing circle with radius: " + radius);
    }
    
    public void setRadius(int radius) {
        this.radius = radius;
    }
}

// Prototype Registry
class ShapeCache {
    private static Map<String, Shape> shapeMap = new HashMap<>();
    
    public static void loadCache() {
        Circle circle = new Circle();
        circle.setRadius(10);
        shapeMap.put("circle", circle);
        // Add more shapes
    }
    
    public static Shape getShape(String type) {
        Shape cachedShape = shapeMap.get(type);
        return cachedShape.clone();  // Return clone, not original
    }
}

// Usage
public class Client {
    public static void main(String[] args) {
        ShapeCache.loadCache();
        
        Shape clonedCircle1 = ShapeCache.getShape("circle");
        Shape clonedCircle2 = ShapeCache.getShape("circle");
        
        // clonedCircle1 and clonedCircle2 are different objects
        System.out.println(clonedCircle1 == clonedCircle2);  // false
    }
}
```

**Use cases:** When object creation is expensive (database operations, network calls), when you want to avoid subclasses for object creation.

### What are SOLID principles?

**SOLID** are five design principles for writing maintainable, scalable, and robust object-oriented code.

**S - Single Responsibility Principle (SRP)**
A class should have only ONE reason to change (only one job).

```java
// BAD - Multiple responsibilities
class UserManager {
    void saveUser(User user) { /* DB save */ }
    void sendEmail(User user, String message) { /* Email logic */ }
    void generateReport(User user) { /* Report logic */ }
}

// GOOD - Each class has single responsibility
class UserRepository {
    void saveUser(User user) { /* DB save */ }
}
class EmailService {
    void sendEmail(User user, String message) { /* Email logic */ }
}
class ReportGenerator {
    void generateReport(User user) { /* Report logic */ }
}
```

**O - Open/Closed Principle**
Classes should be open for extension but closed for modification.

```java
// BAD - Modifying existing code for new features
class DiscountCalculator {
    double calculate(double amount, String type) {
        if (type.equals("REGULAR")) return amount * 0.9;
        if (type.equals("PREMIUM")) return amount * 0.8;
        // Need to modify this method for new type
        return amount;
    }
}

// GOOD - Extend without modifying
interface DiscountStrategy {
    double calculate(double amount);
}
class RegularDiscount implements DiscountStrategy {
    public double calculate(double amount) { return amount * 0.9; }
}
class PremiumDiscount implements DiscountStrategy {
    public double calculate(double amount) { return amount * 0.8; }
}
// Add new type - just create new class, no modification needed
```

**L - Liskov Substitution Principle**
Subtypes must be substitutable for their base types.

```java
// BAD - Violates LSP
class Bird {
    void fly() { System.out.println("Flying"); }
}
class Penguin extends Bird {
    @Override
    void fly() { throw new RuntimeException("Can't fly"); } // Breaks substitution
}

// GOOD - Redesign hierarchy
interface Bird { }
interface FlyingBird extends Bird {
    void fly();
}
class Sparrow implements FlyingBird {
    public void fly() { System.out.println("Flying"); }
}
class Penguin implements Bird {
    // No fly method - correct
}
```

**I - Interface Segregation Principle**
Clients shouldn't depend on interfaces they don't use (fat interfaces are bad).

```java
// BAD - Fat interface
interface Worker {
    void work();
    void eat();
    void sleep();
}

// GOOD - Segregated interfaces
interface Workable {
    void work();
}
interface Eatable {
    void eat();
}
interface Sleepable {
    void sleep();
}
class Robot implements Workable {
    public void work() { /* work only */ }
}
class Human implements Workable, Eatable, Sleepable {
    public void work() { }
    public void eat() { }
    public void sleep() { }
}
```

**D - Dependency Inversion Principle**
High-level modules shouldn't depend on low-level modules. Both should depend on abstractions.

```java
// BAD - High-level depends on low-level
class EmailSender {
    void send(String message) { }
}
class NotificationService {
    private EmailSender emailSender = new EmailSender(); // Direct dependency
    void notify(String msg) { emailSender.send(msg); }
}

// GOOD - Depends on abstraction
interface MessageSender {
    void send(String message);
}
class EmailSender implements MessageSender {
    public void send(String message) { }
}
class SMSSender implements MessageSender {
    public void send(String message) { }
}
class NotificationService {
    private MessageSender sender;  // Depends on abstraction
    NotificationService(MessageSender sender) {
        this.sender = sender;
    }
    void notify(String msg) { sender.send(msg); }
}
```

### What is Method Overriding?

**Method Overriding** is when a subclass provides a specific implementation of a method already defined in its parent class.

**Rules:**
- Same method name, same parameters, same return type (or covariant return type)
- Cannot have weaker access modifier (can be same or more accessible)
- Cannot throw broader checked exceptions
- Use `@Override` annotation (optional but recommended)

```java
class Animal {
    protected String sound = "Generic sound";
    
    public void makeSound() {
        System.out.println("Animal makes sound: " + sound);
    }
    
    protected Animal getParent() {
        return new Animal();
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {  // Access modifier can be public (more accessible)
        System.out.println("Dog barks: Woof Woof!");
    }
    
    // Covariant return type - can return subclass
    @Override
    protected Dog getParent() {  // Returns Dog instead of Animal
        return new Dog();
    }
}

class Cat extends Animal {
    @Override
    public void makeSound() {
        super.makeSound();  // Can call parent method
        System.out.println("Cat meows: Meow!");
    }
}

// Runtime Polymorphism
public class Test {
    public static void main(String[] args) {
        Animal myAnimal = new Dog();
        myAnimal.makeSound();  // Output: Dog barks: Woof Woof! (Runtime binding)
        
        myAnimal = new Cat();
        myAnimal.makeSound();  // Output: Cat meows: Meow!
    }
}
```

**Overriding vs Overloading:**
- Overriding: Same method signature, different class, runtime polymorphism
- Overloading: Different parameters, same class, compile-time polymorphism

### Difference between local variables and instance variables

| Feature | Local Variables | Instance Variables |
|---------|----------------|-------------------|
| **Declaration** | Inside method, constructor, or block | Inside class but outside any method |
| **Default Value** | No default (must initialize before use) | Has default value (0, null, false) |
| **Access Modifiers** | Cannot use (private, public, etc.) | Can use all access modifiers |
| **Scope** | Within the method/block only | Throughout the class |
| **Memory** | Stack memory | Heap memory (part of object) |
| **Lifecycle** | Created when method called, destroyed when method exits | Created when object created, destroyed when garbage collected |
| **Static allowed?** | No | Yes (static variables) |

```java
public class VariableDemo {
    // Instance variables
    private String name;        // Default: null
    private int age;            // Default: 0
    private boolean active;     // Default: false
    
    // Static variable (class variable)
    private static int counter = 0;
    
    public void myMethod() {
        // Local variables - MUST initialize before use
        int localVar = 10;      // Explicit initialization
        String message;         // No default value
        
        // System.out.println(message); // ERROR! Not initialized
        
        message = "Hello";
        System.out.println(localVar);  // Works
        System.out.println(age);       // Works - instance variable has default 0
    }
}
```

### Where are variables stored (Heap vs Stack memory)?

**Stack Memory:**
- Stores: Local variables, method calls, primitive values, object references
- LIFO (Last In First Out) structure
- Each thread has its own stack
- Fast access but limited size
- Variables automatically destroyed when method completes

**Heap Memory:**
- Stores: All objects (instances of classes), instance variables, arrays
- Shared across all threads
- Larger but slower access
- Managed by Garbage Collector

```java
public class MemoryDemo {
    int instanceVar = 20;  // Stored in HEAP (part of object)
    static int staticVar = 30;  // Stored in METASPACE (special heap area)
    
    public void myMethod() {
        int localVar = 10;  // Stored in STACK
        String name = "John";  // Reference 'name' in STACK, String object in HEAP
        
        Person p = new Person();  // Reference 'p' in STACK, Person object in HEAP
    }
    
    public static void main(String[] args) {
        MemoryDemo obj = new MemoryDemo();  // 'obj' reference in STACK, object in HEAP
        obj.myMethod();
    }
}
```

**Visual representation:**
```
STACK                          HEAP
+--------------+              +-------------------+
| main()       |              | MemoryDemo object |
| args         | -----------> | instanceVar = 20  |
| obj (ref)----|              +-------------------+
+--------------+              
| myMethod()   |              +-------------------+
| localVar=10  |              | Person object     |
| name (ref)---|------------> | name = "John"     |
| p (ref)------|------------> | age = 25          |
+--------------+              +-------------------+
```

### Explain JVM vs JRE vs JDK

**JVM (Java Virtual Machine):**
- Executes Java bytecode
- Provides platform independence ("Write Once, Run Anywhere")
- Manages memory, garbage collection, thread synchronization
- Each OS has different JVM implementation
- NOT platform independent (JVM is platform dependent)

**JRE (Java Runtime Environment):**
- JVM + Core libraries + Supporting files
- Required to RUN Java applications
- Does NOT include compilers or development tools
- About 50-60 MB

**JDK (Java Development Kit):**
- JRE + Development tools (javac, jar, javadoc, debugger)
- Required to DEVELOP Java applications
- Includes compiler to convert .java to .class
- About 150-200 MB

```
JDK (Development Kit)
├── JRE (Runtime Environment)
│   ├── JVM (Virtual Machine)
│   ├── Core Libraries (rt.jar, etc.)
│   └── Supporting files
├── javac (Compiler)
├── jar (Archiver)
├── javadoc (Documentation)
└── Other tools (jdb, jconsole, etc.)
```

**When to use what:**
- **JDK:** Developers writing Java code
- **JRE:** End-users running Java applications
- **JVM:** Part of JRE, not installed separately

### Explain Memory Management in Java

**Java Memory Management** is automatic through Garbage Collection (GC). Developer doesn't manually free memory.

**Memory Areas:**

**1. Heap Memory (Garbage Collected):**
- Young Generation: New objects created here
  - Eden Space: Most objects start here
  - Survivor Space 0 (S0)
  - Survivor Space 1 (S1)
- Old Generation (Tenured): Objects that survive many GC cycles

**2. Metaspace (Java 8+):**
- Stores class metadata, static variables, method information
- Replaced PermGen (Java 7 and below)
- Automatically grows (no fixed size limit)

**3. Stack Memory (Not Garbage Collected):**
- Method calls, local variables, references
- Automatically freed when method completes

**Garbage Collection Process:**

```java
public class GCDemo {
    public void createObjects() {
        // These objects become eligible for GC after method ends
        Person p1 = new Person();  // No reference after method
        Person p2 = new Person();
        
        p1 = null;  // Eligible for GC immediately
        // p2 eligible when method ends
    }
    
    public static void main(String[] args) {
        GCDemo demo = new GCDemo();
        demo.createObjects();
        
        // Request GC (not guaranteed to run immediately)
        System.gc();
        
        // Better approach - let JVM decide
        Runtime.getRuntime().gc();
    }
}
```

**GC Algorithms:**

**1. Serial GC:** Single thread, good for small applications
**2. Parallel GC (Throughput GC):** Multiple threads, default for Java 8
**3. CMS (Concurrent Mark Sweep):** Low pause time (deprecated in Java 14)
**4. G1 GC (Garbage First):** Default for Java 9+, balances throughput and latency
**5. ZGC:** Very low latency (sub-millisecond pauses), Java 11+

**Best Practices:**
```java
// GOOD - Let GC handle cleanup
public void process() {
    List<String> list = new ArrayList<>();
    // Use list
    // No need to set to null - method ends, list goes out of scope
}

// GOOD - Close resources explicitly
try (Connection conn = DriverManager.getConnection(url);
     PreparedStatement ps = conn.prepareStatement(sql)) {
    // Auto-closed after try block
}

// BAD - Creating unnecessary objects
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // Creates new String object each iteration
}
// GOOD - Use StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
```

**Memory Leak Scenarios:**
- Unclosed resources (connections, streams)
- Adding objects to static collections without removal
- Listener registration without unregistration
- Inner classes holding reference to outer class

---

## 🔹 2. Exception Handling

### What is Exception Handling?

**Exception Handling** is a mechanism to handle runtime errors (exceptions) so that the normal flow of application can be maintained.

**Why needed?**
- Prevents program from crashing abruptly
- Separates error-handling code from regular code
- Provides meaningful error messages to users

```java
// WITHOUT Exception Handling - Program crashes
public class WithoutException {
    public static void main(String[] args) {
        int result = 10 / 0;  // ArithmeticException - program crashes
        System.out.println("This line never executes");
    }
}

// WITH Exception Handling - Graceful handling
public class WithException {
    public static void main(String[] args) {
        try {
            int result = 10 / 0;
            System.out.println(result);
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero! " + e.getMessage());
        }
        System.out.println("Program continues normally");
    }
}
```

**Keywords:**
- **try:** Block that contains code that might throw exception
- **catch:** Block that handles specific exception
- **finally:** Block that ALWAYS executes (cleanup code)
- **throw:** Manually throw an exception
- **throws:** Declares exceptions that method might throw

```java
public class CompleteExample {
    public static void main(String[] args) {
        try {
            validateAge(15);
        } catch (IllegalArgumentException e) {
            System.out.println("Caught: " + e.getMessage());
        } finally {
            System.out.println("This always executes");
        }
    }
    
    static void validateAge(int age) {
        if (age < 18) {
            throw new IllegalArgumentException("Age must be 18 or above");
        }
    }
}
```

### Types of Exceptions in Java

**Hierarchy:**
```
Throwable (parent of all errors and exceptions)
├── Error (irrecoverable - not handled by applications)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
└── Exception (recoverable)
    ├── Checked Exceptions (compile-time)
    │   ├── IOException
    │   ├── SQLException
    │   ├── ClassNotFoundException
    │   └── FileNotFoundException
    └── Unchecked Exceptions (runtime)
        ├── NullPointerException
        ├── ArrayIndexOutOfBoundsException
        ├── ArithmeticException
        ├── IllegalArgumentException
        └── NumberFormatException
```

### Difference between Checked and Unchecked Exceptions

| Feature | Checked Exceptions | Unchecked Exceptions |
|---------|-------------------|---------------------|
| **Checked at** | Compile-time | Runtime |
| **Handling required?** | Yes (try-catch or throws) | No (optional) |
| **Parent class** | Exception (but not RuntimeException) | RuntimeException |
| **Examples** | IOException, SQLException | NullPointerException, ArithmeticException |
| **Recovery possibility** | Usually recoverable | Usually programming errors |
| **When to use** | External factors (file not found, network down) | Programming bugs (null reference, array bounds) |

```java
// Checked Exception - MUST handle
public void readFile() throws IOException {  // 'throws' declares it
    FileReader file = new FileReader("test.txt");
    // Or use try-catch
}

// Unchecked Exception - Optional to handle
public void divide(int a, int b) {
    int result = a / b;  // May throw ArithmeticException
    // Can handle or not - compiler doesn't force
}
```

### What is ArithmeticException?

**ArithmeticException** is an unchecked exception thrown when an exceptional arithmetic condition occurs.

**Common causes:**
```java
public class ArithmeticExceptionDemo {
    public static void main(String[] args) {
        // 1. Division by zero
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero");
        }
        
        // 2. Integer overflow (not always thrown - wraps around)
        int maxInt = Integer.MAX_VALUE;
        System.out.println(maxInt + 1);  // No exception, wraps to -2147483648
        
        // 3. Modulo by zero
        try {
            int mod = 10 % 0;
        } catch (ArithmeticException e) {
            System.out.println("Cannot perform modulo by zero");
        }
        
        // 4. BigInteger division by zero (throws ArithmeticException)
        java.math.BigInteger big = java.math.BigInteger.TEN;
        try {
            big.divide(java.math.BigInteger.ZERO);
        } catch (ArithmeticException e) {
            System.out.println("BigInteger division by zero");
        }
    }
}
```

### How does Exception Handling work in Spring Boot?

**Spring Boot provides several ways to handle exceptions globally:**

**1. @ExceptionHandler (Controller-level):**
```java
@RestController
public class UserController {
    
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
    }
    
    @ExceptionHandler(UserNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponse handleUserNotFound(UserNotFoundException e) {
        return new ErrorResponse("USER_NOT_FOUND", e.getMessage());
    }
}
```

**2. @ControllerAdvice (Global exception handling - RECOMMENDED):**
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    
    // Handle specific exception
    @ExceptionHandler(UserNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException e) {
        ErrorResponse error = new ErrorResponse(
            LocalDateTime.now(),
            HttpStatus.NOT_FOUND.value(),
            "USER_NOT_FOUND",
            e.getMessage()
        );
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }
    
    // Handle validation errors
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public Map<String, String> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach((error) -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        return errors;
    }
    
    // Handle all other exceptions
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ErrorResponse handleAllExceptions(Exception e) {
        return new ErrorResponse("INTERNAL_ERROR", "An unexpected error occurred");
    }
}
```

**3. ResponseEntityExceptionHandler (For REST APIs):**
```java
@ControllerAdvice
public class CustomResponseEntityExceptionHandler extends ResponseEntityExceptionHandler {
    
    @Override
    protected ResponseEntity<Object> handleHttpMessageNotReadable(
            HttpMessageNotReadableException ex, 
            HttpHeaders headers, 
            HttpStatus status, 
            WebRequest request) {
        
        ErrorResponse error = new ErrorResponse("MALFORMED_JSON", "Invalid request body");
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }
}
```

**4. Using @ResponseStatus on custom exceptions:**
```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}

// When thrown, Spring returns 404 automatically
```

---

## 🔹 3. Collections Framework

### What is the Collection Framework?

**Collection Framework** is a unified architecture for storing, retrieving, and manipulating groups of objects. It provides interfaces, implementations, and algorithms.

**Core Interfaces Hierarchy:**
```
Iterable (root interface)
│
├── Collection (group of objects)
│   ├── List (ordered, allows duplicates)
│   │   ├── ArrayList (resizable array)
│   │   ├── LinkedList (doubly-linked list)
│   │   └── Vector (thread-safe, legacy)
│   │       └── Stack
│   │
│   ├── Set (unique elements, no order)
│   │   ├── HashSet (hash table, O(1))
│   │   ├── LinkedHashSet (maintains insertion order)
│   │   └── TreeSet (sorted, Red-Black tree)
│   │
│   └── Queue (FIFO order)
│       ├── PriorityQueue (priority-based)
│       ├── ArrayDeque (double-ended)
│       └── LinkedList (also implements Queue)
│
├── Map (key-value pairs)
│   ├── HashMap (unsorted, O(1))
│   ├── LinkedHashMap (insertion order)
│   ├── TreeMap (sorted by keys)
│   └── Hashtable (thread-safe, legacy)
│
└── Comparator & Comparable (for sorting)
```

**Benefits:**
- Reduces programming effort (ready-to-use data structures)
- Increases performance (high-quality implementations)
- Provides interoperability between APIs

### Difference between List and ArrayList

This question often confuses people. **List is an interface, ArrayList is a class that implements List.**

```java
// Correct comparison: List vs Set vs Map, or ArrayList vs LinkedList

// List (interface) - contract that all list implementations follow
List<String> list = new ArrayList<>();  // Programming to interface

// ArrayList (implementation) - specific class
ArrayList<String> arrayList = new ArrayList<>();
```

**Key differences between List (as interface) and ArrayList (as class):**

| Aspect | List (Interface) | ArrayList (Class) |
|--------|-----------------|-------------------|
| **Nature** | Contract/Blueprint | Concrete implementation |
| **Instantiation** | Cannot instantiate directly | Can instantiate |
| **Methods** | Declares methods | Implements all List methods |
| **Flexibility** | Can change implementation easily | Locked to ArrayList |
| **Best practice** | Use List type for variable declaration | Use ArrayList type only if ArrayList-specific methods needed |

```java
// GOOD - Programming to interface (flexible)
List<String> list = new ArrayList<>();  // Can change to LinkedList easily

// BAD - Programming to implementation (rigid)
ArrayList<String> list2 = new ArrayList<>();  // Hard to change later

// Switching implementation is easy with interface
list = new LinkedList<>();  // Works! Still a List
```

### Difference between ArrayList vs LinkedList

| Feature | ArrayList | LinkedList |
|---------|-----------|------------|
| **Internal structure** | Dynamic array | Doubly-linked list |
| **Memory overhead** | Less (stores only elements) | More (stores prev/next pointers) |
| **Get by index** | O(1) - direct array access | O(n) - traverse from start/end |
| **Add at end** | O(1) amortized | O(1) |
| **Add at middle** | O(n) - shift elements | O(n) - find position, then O(1) insert |
| **Remove from middle** | O(n) - shift elements | O(n) - find position, then O(1) remove |
| **Add/Remove at beginning** | O(n) - shift all elements | O(1) - just update pointers |
| **Memory locality** | Good (cache-friendly) | Poor (nodes scattered) |
| **When to use** | Frequent reads, fewer modifications | Frequent insertions/deletions |

```java
// Performance comparison
public class CompareList {
    public static void main(String[] args) {
        // ArrayList - Fast for random access
        List<Integer> arrayList = new ArrayList<>();
        for (int i = 0; i < 100000; i++) arrayList.add(i);
        
        long start = System.nanoTime();
        int value = arrayList.get(50000);  // Very fast - O(1)
        long end = System.nanoTime();
        System.out.println("ArrayList get: " + (end - start) + " ns");
        
        // LinkedList - Fast for insertions/deletions at ends
        List<Integer> linkedList = new LinkedList<>();
        for (int i = 0; i < 100000; i++) linkedList.add(i);
        
        start = System.nanoTime();
        linkedList.add(0, -1);  // Insert at beginning - O(1)
        end = System.nanoTime();
        System.out.println("LinkedList insert at beginning: " + (end - start) + " ns");
        
        // But LinkedList get by index is slow
        start = System.nanoTime();
        value = linkedList.get(50000);  // Slow - O(n)
        end = System.nanoTime();
        System.out.println("LinkedList get: " + (end - start) + " ns");
    }
}
```

### What is HashMap?

**HashMap** is a collection that stores key-value pairs. It uses hashing for O(1) average time complexity for put, get, and remove operations.

**Key characteristics:**
- Allows one null key and multiple null values
- Not thread-safe (use ConcurrentHashMap for concurrency)
- Order is not guaranteed
- Initial capacity = 16, load factor = 0.75

```java
public class HashMapDemo {
    public static void main(String[] args) {
        // Creating HashMap
        HashMap<String, Integer> studentScores = new HashMap<>();
        
        // Adding elements
        studentScores.put("John", 85);
        studentScores.put("Emma", 92);
        studentScores.put("Mike", 78);
        studentScores.put("John", 90);  // Overwrites previous value
        
        // Getting elements
        Integer johnScore = studentScores.get("John");  // 90
        Integer notFound = studentScores.get("Sarah");  // null
        
        // Checking existence
        boolean hasEmma = studentScores.containsKey("Emma");  // true
        boolean hasValue92 = studentScores.containsValue(92);  // true
        
        // Iterating
        for (Map.Entry<String, Integer> entry : studentScores.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
        
        // Using forEach (Java 8)
        studentScores.forEach((key, value) -> System.out.println(key + "=" + value));
        
        // Default values
        Integer sarahScore = studentScores.getOrDefault("Sarah", 0);  // 0
        
        // Compute if absent
        studentScores.computeIfAbsent("Sarah", k -> 75);  // Adds Sarah=75
        
        // Remove
        studentScores.remove("Mike");
        
        // Size
        System.out.println("Size: " + studentScores.size());  // 3
    }
}
```

### Internal working of HashMap

**HashMap internally uses an array of Node objects (buckets).**

**Structure:**
```java
// Simplified internal representation
class HashMap<K,V> {
    Node<K,V>[] table;  // Array of buckets
    
    static class Node<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;  // For collision handling (linked list)
    }
}
```

**Put Operation (how put() works):**

```java
// Step-by-step of map.put("John", 85)

1. Calculate hash: hash("John") = 2398472
2. Calculate index: index = hash & (n-1) where n=16 (table size)
   // index = 2398472 & 15 = 8
3. Check bucket at index 8:
   - If null, create new node and place there
   - If not null (collision), traverse linked list/Tree
4. If key already exists, replace value
5. If load factor exceeded, resize (double capacity)
```

**Visual representation:**
```
HashMap (size=16, load factor=0.75)
Index:   0    1    2    3    4    5    6    7    8    9    10   11   12   13   14   15
Bucket: [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [●]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]
                                                   │
                                                   ▼
                                              Node("John",85)
                                                    │
                                                    ▼
                                              Node("Jane",92)  ← Collision (same bucket)
                                                    │
                                                    ▼
                                              Node("Jack",78)  ← Linked list
```

**Get Operation (how get() works):**
```java
// map.get("John")
1. Calculate hash of "John" → same as above
2. Calculate index → 8
3. Go to bucket 8
4. Traverse linked list comparing keys using equals()
5. Return value if found, else null
```

**Java 8+ Optimization:**
- When linked list length > 8, convert to Red-Black Tree (TreeNodes)
- Improves worst-case from O(n) to O(log n)
- When tree size < 6, convert back to linked list

**Load Factor and Rehashing:**
- Load factor = 0.75 (default) - balance between time and space
- Threshold = capacity × load factor = 16 × 0.75 = 12
- When size exceeds threshold, HashMap resizes (doubles capacity)
- All entries are rehashed (recalculated indexes) - expensive operation

```java
// Customizing HashMap
HashMap<String, Integer> map = new HashMap<>(32, 0.8f);  // capacity=32, load factor=0.8
```

### What is HashSet? Internal working

**HashSet** is a collection that contains unique elements (no duplicates). It's backed by a HashMap internally.

**Key characteristics:**
- No duplicates (uses equals() and hashCode())
- Allows one null element
- Not thread-safe
- Order not guaranteed

**Internal structure:**
```java
public class HashSet<E> {
    private transient HashMap<E, Object> map;
    private static final Object PRESENT = new Object();  // Dummy value
    
    public HashSet() {
        map = new HashMap<>();
    }
    
    public boolean add(E e) {
        return map.put(e, PRESENT) == null;  // Key in HashMap, value is dummy
    }
    
    public boolean remove(Object o) {
        return map.remove(o) == PRESENT;
    }
    
    public boolean contains(Object o) {
        return map.containsKey(o);
    }
}
```

**Important:** HashSet uses HashMap internally, where elements are stored as KEYS in the HashMap, and all keys map to the same dummy object PRESENT.

```java
public class HashSetDemo {
    public static void main(String[] args) {
        HashSet<String> fruits = new HashSet<>();
        
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Orange");
        fruits.add("Apple");  // Duplicate - ignored
        
        System.out.println(fruits);  // [Apple, Banana, Orange] - order not guaranteed
        
        // Check existence
        System.out.println(fruits.contains("Banana"));  // true
        
        // Remove
        fruits.remove("Orange");
        
        // Size
        System.out.println(fruits.size());  // 2
        
        // Iteration
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
        
        // Java 8
        fruits.forEach(System.out::println);
    }
}
```

**Time complexity:** 
- add(): O(1) average
- remove(): O(1) average
- contains(): O(1) average

### What is ConcurrentHashMap?

**ConcurrentHashMap** is a thread-safe implementation of Map that allows concurrent access by multiple threads without external synchronization.

**Key features:**
- High concurrency: multiple threads can read/write simultaneously
- No locking for read operations
- Segment-based locking (Java 7) or CAS + synchronized (Java 8+)
- Does NOT allow null keys or null values

```java
public class ConcurrentHashMapDemo {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        
        // Thread-safe operations
        map.put("A", 1);
        map.putIfAbsent("B", 2);  // Atomic operation
        map.putIfAbsent("B", 3);  // Won't update, B already has value 2
        
        // compute methods are atomic
        map.compute("A", (key, val) -> val == null ? 1 : val + 1);
        
        // merge is atomic
        map.merge("C", 1, Integer::sum);
        
        // Iteration - weakly consistent (doesn't throw ConcurrentModificationException)
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
            // Can modify map while iterating
            map.put("D", 4);  // Won't cause exception
        }
    }
}
```

### Difference between HashMap vs ConcurrentHashMap

| Feature | HashMap | ConcurrentHashMap |
|---------|---------|-------------------|
| **Thread-safety** | Not thread-safe | Thread-safe |
| **Null keys/values** | Allows one null key, multiple null values | Does NOT allow null keys or values |
| **Performance (single thread)** | Faster | Slower (due to synchronization overhead) |
| **Performance (multi-thread)** | Requires external sync (slow) | High concurrency |
| **Iterator** | Fail-fast (throws ConcurrentModificationException) | Weakly consistent (no exception) |
| **Locking mechanism** | No locking | CAS + synchronized on buckets (Java 8+) |
| **When to use** | Single-threaded environments | Multi-threaded environments |

**Example - Concurrent access problem with HashMap:**
```java
// BAD - HashMap with multiple threads
Map<String, Integer> hashMap = new HashMap<>();
// Multiple threads accessing - corrupts data, may cause infinite loop

// GOOD - ConcurrentHashMap
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
// Safe for concurrent access

// Also possible - synchronized HashMap (but slower)
Map<String, Integer> synchronizedMap = Collections.synchronizedMap(new HashMap<>());
```

### What is hashCode() and equals() contract?

**The Contract:**
1. If two objects are equal according to equals(), they MUST have the same hashCode()
2. If two objects have the same hashCode(), they MAY be equal (not guaranteed)
3. hashCode() must return consistent value (if object not modified)

**Why important?**
HashMap, HashSet, and other hash-based collections use hashCode() to find the bucket, and equals() to find the exact element within that bucket.

```java
// BAD - Violates contract
class BadStudent {
    private int id;
    private String name;
    
    BadStudent(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        BadStudent student = (BadStudent) obj;
        return id == student.id;
    }
    
    // Missing hashCode() - VIOLATES CONTRACT!
    // Two equal objects may have different hashCodes
}

// GOOD - Follows contract
class GoodStudent {
    private int id;
    private String name;
    
    GoodStudent(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        GoodStudent student = (GoodStudent) obj;
        return id == student.id;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id);  // Or simply return id
    }
}

// Demonstration
public class HashCodeDemo {
    public static void main(String[] args) {
        // BAD - HashMap won't work correctly
        Map<BadStudent, String> badMap = new HashMap<>();
        BadStudent s1 = new BadStudent(1, "John");
        BadStudent s2 = new BadStudent(1, "John");
        
        badMap.put(s1, "Value");
        System.out.println(badMap.get(s2));  // null! (should find s1)
        
        // GOOD - HashMap works correctly
        Map<GoodStudent, String> goodMap = new HashMap<>();
        GoodStudent g1 = new GoodStudent(1, "John");
        GoodStudent g2 = new GoodStudent(1, "John");
        
        goodMap.put(g1, "Value");
        System.out.println(goodMap.get(g2));  // "Value" - works!
    }
}
```

**Best practices for hashCode():**
- Use same fields as equals()
- Use `Objects.hash(field1, field2, ...)`
- For performance, use prime numbers (31 is common)
- Avoid using mutable fields in hashCode()

```java
// Good hashCode implementation examples
@Override
public int hashCode() {
    // Using Objects.hash() - simple and safe
    return Objects.hash(id, name, email);
}

// Manual implementation for performance
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + id;
    result = 31 * result + (name != null ? name.hashCode() : 0);
    result = 31 * result + (email != null ? email.hashCode() : 0);
    return result;
}
```

---

## 🔹 4. Java 8 / 11 / 17 Features

### What are Java 8 features?

**Major Java 8 features (March 2014):**

**1. Lambda Expressions:**
```java
// Before Java 8
Runnable oldRunnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello");
    }
};

// Java 8 Lambda
Runnable newRunnable = () -> System.out.println("Hello");
```

**2. Stream API:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> evenSquares = numbers.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .collect(Collectors.toList());  // [4, 16]
```

**3. Functional Interfaces:**
```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
Calculator add = (a, b) -> a + b;
```

**4. Default Methods in Interfaces:**
```java
interface Vehicle {
    void start();
    
    default void honk() {
        System.out.println("Beep beep!");
    }
}
```

**5. Optional Class:**
```java
Optional<String> optional = Optional.ofNullable(getName());
String name = optional.orElse("Default Name");
```

**6. New Date/Time API (java.time):**
```java
LocalDate date = LocalDate.now();
LocalDateTime dateTime = LocalDateTime.now();
Instant timestamp = Instant.now();
```

**7. Method References:**
```java
List<String> names = Arrays.asList("John", "Jane");
names.forEach(System.out::println);  // Method reference
```

**8. CompletableFuture:**
```java
CompletableFuture.supplyAsync(() -> fetchData())
    .thenApply(data -> process(data))
    .thenAccept(result -> System.out.println(result));
```

### What are Java 11 vs Java 17 features?

**Java 11 (LTS - September 2018):**

**1. HTTP Client API (standard):**
```java
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com"))
    .build();
HttpResponse<String> response = client.send(request, 
    HttpResponse.BodyHandlers.ofString());
```

**2. Local-Variable Syntax for Lambda:**
```java
// Java 10+ - 'var' for local variables
var list = new ArrayList<String>();

// Java 11 - var in lambda parameters
(var x, var y) -> x + y;
```

**3. String Methods:**
```java
String str = "  Hello  ";
str.isBlank();           // false
str.strip();             // "Hello" (Unicode-aware trim)
str.stripLeading();      // "Hello  "
str.stripTrailing();     // "  Hello"
"Hello\nWorld".lines().forEach(System.out::println);  // Hello World
"Hello".repeat(3);       // "HelloHelloHello"
```

**4. Files.readString() / writeString():**
```java
Path path = Path.of("file.txt");
String content = Files.readString(path);
Files.writeString(path, "New content");
```

**Java 17 (LTS - September 2021):**

**1. Sealed Classes:**
```java
sealed class Shape permits Circle, Rectangle { }
final class Circle extends Shape { }
final class Rectangle extends Shape { }
```

**2. Pattern Matching for Switch (Preview in 17, final in 21):**
```java
Object obj = "Hello";
String result = switch(obj) {
    case Integer i -> "Integer: " + i;
    case String s -> "String: " + s;
    case null -> "It's null";
    default -> "Unknown";
};
```

**3. Records:**
```java
// Immutable data carrier (less boilerplate)
record Person(String name, int age) { }
Person p = new Person("John", 25);
System.out.println(p.name());  // Accessor
```

**4. Text Blocks:**
```java
String json = """
    {
        "name": "John",
        "age": 25
    }
    """;
```

**5. Enhanced Pseudo-Random Number Generators:**
```java
RandomGenerator random = RandomGenerator.of("L128X256MixRandom");
int value = random.nextInt();
```

### What is a Lambda Expression?

**Lambda Expression** is an anonymous function (a function without a name) that can be passed as an argument to methods or stored in variables.

**Syntax:** `(parameters) -> { body }`

```java
// Lambda examples
// 1. No parameters
() -> System.out.println("Hello")

// 2. One parameter (parentheses optional)
x -> x * 2

// 3. Multiple parameters
(x, y) -> x + y

// 4. Multiple statements with braces
(x, y) -> {
    int result = x + y;
    System.out.println("Sum: " + result);
    return result;
}
```

**Complete examples:**
```java
public class LambdaDemo {
    public static void main(String[] args) {
        // Example 1: Sorting with lambda
        List<String> names = Arrays.asList("Charlie", "Alice", "Bob");
        names.sort((a, b) -> a.compareTo(b));
        // OR
        names.sort(String::compareTo);
        
        // Example 2: Thread with lambda
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread: " + i);
            }
        }).start();
        
        // Example 3: Custom functional interface
        MathOperation addition = (a, b) -> a + b;
        MathOperation subtraction = (a, b) -> a - b;
        System.out.println(addition.operate(10, 5));  // 15
        
        // Example 4: Predicate (built-in functional interface)
        Predicate<Integer> isEven = n -> n % 2 == 0;
        System.out.println(isEven.test(4));  // true
    }
    
    @FunctionalInterface
    interface MathOperation {
        int operate(int a, int b);
    }
}
```

**Variable capture - effectively final:**
```java
int factor = 2;
Calculator multiply = (a, b) -> a * b * factor;  // factor is effectively final
// factor = 3;  // ERROR! Cannot modify captured variable
```

### What is a Functional Interface?

**Functional Interface** is an interface with exactly ONE abstract method. Can have multiple default or static methods.

**Key annotations:**
- `@FunctionalInterface` - optional but recommended (compiler checks)

```java
// Built-in functional interfaces (java.util.function package)

// 1. Predicate<T> - takes T, returns boolean
Predicate<String> isEmpty = s -> s.isEmpty();
Predicate<Integer> isEven = n -> n % 2 == 0;

// 2. Function<T,R> - takes T, returns R
Function<String, Integer> length = s -> s.length();
Function<Integer, String> toString = Object::toString;

// 3. Consumer<T> - takes T, no return
Consumer<String> printer = s -> System.out.println(s);
Consumer<Integer> logger = i -> System.out.println("Value: " + i);

// 4. Supplier<T> - no input, returns T
Supplier<Double> random = () -> Math.random();
Supplier<LocalDate> today = LocalDate::now;

// 5. BiPredicate<T,U> - takes two, returns boolean
BiPredicate<String, String> equal = (s1, s2) -> s1.equals(s2);

// 6. BiFunction<T,U,R> - takes two, returns R
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

// 7. UnaryOperator<T> - extends Function<T,T>
UnaryOperator<Integer> square = n -> n * n;

// 8. BinaryOperator<T> - extends BiFunction<T,T,T>
BinaryOperator<Integer> multiply = (a, b) -> a * b;

// Custom functional interface
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
    
    // Can have default methods
    default void log(String message) {
        System.out.println(message);
    }
    
    // Can have static methods
    static <F, T> Converter<F, T> identity() {
        return f -> (T) f;
    }
}

// Usage
Converter<String, Integer> stringToInt = Integer::parseInt;
int result = stringToInt.convert("123");  // 123
```

### What is Stream API?

**Stream API** is a sequence of elements supporting sequential and parallel aggregate operations. It allows functional-style operations on collections.

**Characteristics:**
- Not a data structure (doesn't store data)
- Doesn't modify source (produces new results)
- Lazy evaluation (intermediate operations)
- Can be sequential or parallel

**Stream Operations:**
```java
public class StreamDemo {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("John", 25, "IT"),
            new Person("Jane", 30, "HR"),
            new Person("Bob", 22, "IT"),
            new Person("Alice", 28, "Finance")
        );
        
        // Intermediate operations (lazy)
        // - filter(), map(), flatMap(), distinct(), sorted(), limit(), skip(), peek()
        
        // Terminal operations (eager)
        // - forEach(), collect(), reduce(), count(), anyMatch(), allMatch(), findFirst()
        
        // Example 1: Filtering and mapping
        List<String> itPeopleNames = people.stream()
            .filter(p -> "IT".equals(p.getDepartment()))
            .map(Person::getName)
            .collect(Collectors.toList());  // [John, Bob]
        
        // Example 2: Sorting
        List<Person> sortedByAge = people.stream()
            .sorted(Comparator.comparing(Person::getAge))
            .collect(Collectors.toList());
        
        // Example 3: Grouping
        Map<String, List<Person>> byDept = people.stream()
            .collect(Collectors.groupingBy(Person::getDepartment));
        
        // Example 4: Aggregation
        double averageAge = people.stream()
            .mapToInt(Person::getAge)
            .average()
            .orElse(0);
        
        // Example 5: Find and match
        boolean anyAbove30 = people.stream()
            .anyMatch(p -> p.getAge() > 30);
        
        Optional<Person> firstIT = people.stream()
            .filter(p -> "IT".equals(p.getDepartment()))
            .findFirst();
        
        // Example 6: Reduce
        int totalAge = people.stream()
            .map(Person::getAge)
            .reduce(0, Integer::sum);
        
        // Example 7: Parallel stream (uses ForkJoinPool)
        long count = people.parallelStream()
            .filter(p -> p.getAge() > 25)
            .count();
        
        // Example 8: Primitive streams
        IntStream.range(1, 10).forEach(System.out::print);  // 123456789
        LongStream.rangeClosed(1, 5).sum();  // 15
    }
}
```

### Difference between map() and flatMap()

| Feature | map() | flatMap() |
|---------|-------|-----------|
| **Input** | Stream<T> | Stream<T> |
| **Output** | Stream<R> (one-to-one) | Stream<R> (one-to-many) |
| **Function returns** | R (single element) | Stream<R> (stream of elements) |
| **Result structure** | Same number of elements | Can be different number |
| **Use case** | Transforming each element | Flattening nested structures |

```java
public class MapVsFlatMap {
    public static void main(String[] args) {
        // map() - one-to-one transformation
        List<String> words = Arrays.asList("hello", "world");
        List<Integer> lengths = words.stream()
            .map(String::length)  // "hello" -> 5, "world" -> 5
            .collect(Collectors.toList());  // [5, 5]
        
        // flatMap() - one-to-many, flattening
        List<String> words2 = Arrays.asList("hello", "world");
        List<String> letters = words2.stream()
            .flatMap(word -> Arrays.stream(word.split("")))  // "hello" -> ["h","e","l","l","o"]
            .distinct()
            .collect(Collectors.toList());  // [h, e, l, o, w, r, d]
        
        // Real-world example: List of lists
        List<List<Integer>> listOfLists = Arrays.asList(
            Arrays.asList(1, 2, 3),
            Arrays.asList(4, 5),
            Arrays.asList(6, 7, 8, 9)
        );
        
        // Using map() - keeps nested structure
        List<Integer> sizes = listOfLists.stream()
            .map(List::size)  // [3, 2, 4]
            .collect(Collectors.toList());
        
        // Using flatMap() - flattens
        List<Integer> flatList = listOfLists.stream()
            .flatMap(List::stream)  // [1,2,3,4,5,6,7,8,9]
            .collect(Collectors.toList());
        
        // Example with Optional
        List<Optional<String>> optionals = Arrays.asList(
            Optional.of("a"), Optional.empty(), Optional.of("b")
        );
        
        List<String> values = optionals.stream()
            .flatMap(Optional::stream)  // Java 9+ - filters empty optionals
            .collect(Collectors.toList());  // [a, b]
    }
}
```

### Difference between filter() and map()

| Feature | filter() | map() |
|---------|----------|-------|
| **Purpose** | Select elements based on condition | Transform each element |
| **Output count** | Same or fewer than input | Same as input |
| **Function returns** | boolean (predicate) | new value (transformation) |
| **Use case** | Removing unwanted elements | Converting elements |

```java
public class FilterVsMap {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
        
        // filter() - selects elements
        List<Integer> evenNumbers = numbers.stream()
            .filter(n -> n % 2 == 0)  // Keeps 2,4,6
            .collect(Collectors.toList());  // [2,4,6]
        
        // map() - transforms elements
        List<Integer> doubled = numbers.stream()
            .map(n -> n * 2)  // Transforms all: 1→2, 2→4, 3→6...
            .collect(Collectors.toList());  // [2,4,6,8,10,12]
        
        // Chaining filter and map
        List<Integer> evenDoubled = numbers.stream()
            .filter(n -> n % 2 == 0)  // First filter
            .map(n -> n * 2)          // Then transform
            .collect(Collectors.toList());  // [4,8,12]
        
        // Real-world: Filtering and extracting
        List<Person> people = getPeople();
        
        // Get names of people over 18
        List<String> adultNames = people.stream()
            .filter(p -> p.getAge() >= 18)  // Filter condition
            .map(Person::getName)            // Extract name
            .collect(Collectors.toList());
    }
}
```

### What is Optional class?

**Optional** is a container object that may or may not contain a non-null value. It helps avoid NullPointerException.

```java
public class OptionalDemo {
    public static void main(String[] args) {
        // Creating Optional
        Optional<String> nonEmpty = Optional.of("Hello");  // Can't be null
        Optional<String> empty = Optional.empty();         // Empty optional
        Optional<String> nullable = Optional.ofNullable(getName());  // Can be null
        
        // Checking existence
        if (nonEmpty.isPresent()) {
            System.out.println(nonEmpty.get());  // Hello
        }
        
        // Modern approach - use ifPresent
        nonEmpty.ifPresent(System.out::println);  // Hello
        
        // Default values
        String result = nullable.orElse("Default");
        String result2 = nullable.orElseGet(() -> "Default from supplier");
        
        // Throw exception if empty
        String value = nullable.orElseThrow(() -> new RuntimeException("No value"));
        
        // Transformation
        Optional<Integer> length = nonEmpty.map(String::length);  // Optional[5]
        
        // FlatMap for nested Optionals
        Optional<String> opt = Optional.of("Hello");
        Optional<Integer> flatMapped = opt.flatMap(s -> Optional.of(s.length()));
        
        // Filtering
        Optional<String> filtered = nonEmpty.filter(s -> s.length() > 3);  // Optional[Hello]
        Optional<String> filtered2 = nonEmpty.filter(s -> s.length() > 10);  // Optional.empty
        
        // Combining optionals
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        Optional<String> combined = opt1.or(() -> opt2);  // Optional[Hello]
        
        // Stream operations
        nonEmpty.stream()  // Converts to Stream with one element (or empty stream)
            .map(String::toUpperCase)
            .forEach(System.out::println);
    }
    
    static String getName() {
        return null;  // or "John"
    }
    
    // BAD - returning null
    public Person findPersonBad(String id) {
        // might return null
        return null;
    }
    
    // GOOD - returning Optional
    public Optional<Person> findPersonGood(String id) {
        // return Optional.of(person) or Optional.empty()
        return Optional.empty();
    }
    
    // Using Optional in method chaining
    public void processUser(String userId) {
        Optional.ofNullable(userId)
            .map(this::findUser)
            .filter(user -> user.isActive())
            .ifPresent(this::sendEmail);
    }
}
```

**Best Practices:**
- DON'T use Optional for fields (not serializable)
- DON'T use Optional as method parameters
- DON'T call get() without checking isPresent()
- DO use Optional for return types
- DO use orElse/orElseGet for defaults

---

## 🔹 5. Multithreading & Concurrency

### What is Multithreading?

**Multithreading** is the ability of a CPU to execute multiple threads concurrently. Each thread runs independently but shares the same memory space.

**Benefits:**
- Better resource utilization
- Improved performance (on multi-core systems)
- Responsive applications (UI doesn't freeze)
- Better user experience

```java
public class MultithreadingBasics {
    public static void main(String[] args) {
        // Get current thread info
        Thread currentThread = Thread.currentThread();
        System.out.println("Current thread: " + currentThread.getName());
        System.out.println("Priority: " + currentThread.getPriority());
        
        // Thread states
        // NEW -> RUNNABLE -> RUNNING -> BLOCKED/WAITING/TIMED_WAITING -> TERMINATED
        
        // Create threads
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + ": " + i);
                try {
                    Thread.sleep(100);  // Pause for 100ms
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + ": " + i);
                Thread.yield();  // Hint to scheduler
            }
        });
        
        t1.start();  // Start thread
        t2.start();
        
        // Wait for threads to finish
        try {
            t1.join();  // Main thread waits for t1
            t2.join();  // Main thread waits for t2
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        System.out.println("Both threads completed");
    }
}
```

### How to create threads in Java?

**Three ways to create threads:**

**1. Extending Thread class:**
```java
class MyThread extends Thread {
    private String name;
    
    MyThread(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + ": " + i);
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                System.out.println(name + " interrupted");
            }
        }
    }
}

// Usage
MyThread t1 = new MyThread("Thread-1");
MyThread t2 = new MyThread("Thread-2");
t1.start();
t2.start();
```

**2. Implementing Runnable interface (Preferred):**
```java
class MyRunnable implements Runnable {
    private String name;
    
    MyRunnable(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + ": " + i);
        }
    }
}

// Usage - Runnable is functional interface
Thread t1 = new Thread(new MyRunnable("Thread-1"));
Thread t2 = new Thread(() -> {  // Lambda
    System.out.println("Lambda thread running");
});
t1.start();
t2.start();
```

**3. Implementing Callable interface (Returns result, can throw exception):**
```java
class MyCallable implements Callable<Integer> {
    private int number;
    
    MyCallable(int number) {
        this.number = number;
    }
    
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= number; i++) {
            sum += i;
            Thread.sleep(10);
        }
        return sum;
    }
}

// Usage
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(new MyCallable(100));

try {
    Integer result = future.get();  // Blocks until result available
    System.out.println("Sum: " + result);
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
executor.shutdown();
```

**Comparison:**
| Feature | Thread | Runnable | Callable |
|---------|--------|----------|----------|
| **Return value** | No | No | Yes (via Future) |
| **Throw checked exception** | No | No | Yes |
| **Requires inheritance** | Yes (extends) | No (implements) | No (implements) |
| **Preferred** | Not recommended | Recommended | For results |

### Difference between Runnable and Callable

| Feature | Runnable | Callable |
|---------|----------|----------|
| **Package** | java.lang | java.util.concurrent |
| **Method** | `void run()` | `V call() throws Exception` |
| **Return value** | None | Can return value |
| **Exception handling** | Cannot throw checked exceptions | Can throw checked exceptions |
| **Usage with ExecutorService** | `execute()` or `submit()` | `submit()` only |
| **Result retrieval** | Not applicable | Via `Future<T>` |

```java
public class RunnableVsCallable {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // Runnable - no return value
        Runnable runnableTask = () -> {
            System.out.println("Runnable task executing");
            // Can't return value, can't throw checked exception
        };
        executor.execute(runnableTask);
        
        // Callable - returns value and can throw exception
        Callable<Integer> callableTask = () -> {
            System.out.println("Callable task executing");
            // Simulate work
            Thread.sleep(1000);
            return 42;
        };
        
        Future<Integer> future = executor.submit(callableTask);
        try {
            Integer result = future.get();  // Blocks until result ready
            System.out.println("Callable result: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        
        // Multiple callables
        List<Callable<Integer>> tasks = Arrays.asList(
            () -> { Thread.sleep(1000); return 1; },
            () -> { Thread.sleep(500); return 2; },
            () -> { Thread.sleep(2000); return 3; }
        );
        
        try {
            // Invoke all - returns when all complete
            List<Future<Integer>> futures = executor.invokeAll(tasks);
            
            // Invoke any - returns when first completes
            Integer firstResult = executor.invokeAny(tasks);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        
        executor.shutdown();
    }
}
```

### What is Thread Pooling?

**Thread Pool** is a collection of pre-initialized worker threads that stand ready to execute tasks. Instead of creating a new thread for each task, threads are reused.

**Benefits:**
- Reduces thread creation overhead
- Limits resource consumption
- Improves response time
- Better system stability

```java
public class ThreadPoolDemo {
    public static void main(String[] args) {
        // 1. Fixed Thread Pool (fixed number of threads)
        ExecutorService fixedPool = Executors.newFixedThreadPool(5);
        // Good for: Known number of threads, CPU-intensive tasks
        
        // 2. Cached Thread Pool (creates threads as needed, reuses idle)
        ExecutorService cachedPool = Executors.newCachedThreadPool();
        // Good for: Many short-lived tasks, I/O-bound tasks
        
        // 3. Single Thread Executor (one thread, sequential execution)
        ExecutorService singlePool = Executors.newSingleThreadExecutor();
        // Good for: Tasks that need sequential processing
        
        // 4. Scheduled Thread Pool (for delayed/recurring tasks)
        ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(3);
        // Good for: Periodic tasks, delayed execution
        
        // Using Fixed Thread Pool
        for (int i = 0; i < 20; i++) {
            final int taskId = i;
            fixedPool.submit(() -> {
                System.out.println("Task " + taskId + " executed by " + 
                    Thread.currentThread().getName());
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
        }
        
        // Scheduled tasks
        scheduledPool.schedule(() -> {
            System.out.println("Delayed task");
        }, 5, TimeUnit.SECONDS);  // Run after 5 seconds
        
        scheduledPool.scheduleAtFixedRate(() -> {
            System.out.println("Periodic task");
        }, 0, 2, TimeUnit.SECONDS);  // Run every 2 seconds
        
        // Shutdown properly
        fixedPool.shutdown();  // No new tasks, complete existing
        // fixedPool.shutdownNow();  // Attempt to stop all
        
        try {
            if (!fixedPool.awaitTermination(60, TimeUnit.SECONDS)) {
                fixedPool.shutdownNow();
            }
        } catch (InterruptedException e) {
            fixedPool.shutdownNow();
        }
    }
}
```

**Custom Thread Pool:**
```java
// Creating custom thread pool with specific parameters
ThreadPoolExecutor customPool = new ThreadPoolExecutor(
    2,                              // corePoolSize (min threads)
    5,                              // maximumPoolSize (max threads)
    60,                             // keepAliveTime (idle time)
    TimeUnit.SECONDS,               // time unit
    new LinkedBlockingQueue<>(10),  // work queue (capacity 10)
    Executors.defaultThreadFactory(), // thread factory
    new ThreadPoolExecutor.CallerRunsPolicy()  // rejection policy
);

// Rejection Policies:
// - AbortPolicy: Throws RejectedExecutionException (default)
// - CallerRunsPolicy: Runs task in caller's thread
// - DiscardPolicy: Silently discards task
// - DiscardOldestPolicy: Discards oldest and retries
```

### What are Daemon Threads?

**Daemon threads** are low-priority threads that run in the background to provide services to user threads. JVM exits when only daemon threads remain.

```java
public class DaemonThreadDemo {
    public static void main(String[] args) {
        // User thread (non-daemon) - prevents JVM exit
        Thread userThread = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("User thread: " + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        // Daemon thread - JVM doesn't wait for it
        Thread daemonThread = new Thread(() -> {
            while (true) {
                System.out.println("Daemon thread running in background");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    break;
                }
            }
        });
        
        daemonThread.setDaemon(true);  // Must be set before start()
        
        userThread.start();
        daemonThread.start();
        
        System.out.println("Main thread ending");
        // JVM will exit when userThread completes (doesn't wait for daemon)
    }
}
```

**Common daemon thread examples:**
- Garbage Collector
- Finalizer thread
- Signal Dispatcher
- Reference Handler

**Characteristics:**
- Automatically die when all user threads finish
- Cannot have finally blocks (may not execute)
- Used for background supporting tasks

### What is the Volatile keyword?

**Volatile** keyword ensures that changes to a variable are visible to all threads immediately. It prevents thread-local caching and reordering.

```java
public class VolatileDemo {
    // Without volatile - may cause infinite loop
    private static boolean running = true;
    
    // With volatile - ensures visibility
    private static volatile boolean running2 = true;
    
    public static void main(String[] args) {
        // Problem: Thread may cache variable
        new Thread(() -> {
            while (running) {
                // busy wait - may never see running = false
            }
            System.out.println("Thread stopped");
        }).start();
        
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {}
        
        running = false;  // May not be visible to other thread!
        
        // Solution with volatile
        new Thread(() -> {
            while (running2) {
                // Now will see changes
            }
            System.out.println("Thread stopped with volatile");
        }).start();
        
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {}
        
        running2 = false;  // Immediately visible to other thread
    }
}
```

**Double-checked locking with volatile:**
```java
public class SingletonVolatile {
    private static volatile SingletonVolatile instance;
    
    private SingletonVolatile() {}
    
    public static SingletonVolatile getInstance() {
        if (instance == null) {
            synchronized (SingletonVolatile.class) {
                if (instance == null) {
                    instance = new SingletonVolatile();  // volatile prevents partial construction
                }
            }
        }
        return instance;
    }
}
```

**When to use volatile:**
- Flag variables (boolean status)
- When only one thread writes, others read
- For simple operations (read/write, not compound)

**When NOT to use volatile:**
- For compound operations (i++, check-then-act)
- When multiple threads write to same variable
- When you need atomicity

### What is Atomicity?

**Atomicity** means an operation executes as a single, indivisible unit. Either it completes fully or not at all. No other thread can see intermediate state.

```java
public class AtomicityDemo {
    // Problem: i++ is NOT atomic (read, modify, write)
    private static int counter = 0;
    
    // Solution 1: Synchronized
    private static synchronized void incrementSync() {
        counter++;
    }
    
    // Solution 2: Atomic classes
    private static AtomicInteger atomicCounter = new AtomicInteger(0);
    
    public static void main(String[] args) throws InterruptedException {
        // Without atomicity - race condition
        Thread[] threads = new Thread[1000];
        for (int i = 0; i < 1000; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter++;  // NOT ATOMIC - loses updates
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        System.out.println("Expected: 1,000,000, Actual: " + counter);  // Less!
        
        // With atomic classes
        counter = 0;
        for (int i = 0; i < 1000; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    atomicCounter.incrementAndGet();  // ATOMIC
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        System.out.println("Atomic result: " + atomicCounter.get());  // 1,000,000
    }
}
```

**Atomic classes in Java:**
```java
// AtomicInteger
AtomicInteger ai = new AtomicInteger(0);
ai.incrementAndGet();  // ++i
ai.getAndIncrement();  // i++
ai.addAndGet(5);       // i += 5
ai.compareAndSet(10, 20);  // If value is 10, set to 20

// AtomicLong, AtomicBoolean, AtomicReference
AtomicReference<String> ref = new AtomicReference<>("initial");
ref.compareAndSet("initial", "updated");

// AtomicIntegerArray, AtomicLongArray
AtomicIntegerArray array = new AtomicIntegerArray(10);
array.incrementAndGet(0);

// LongAdder (better for high contention)
LongAdder adder = new LongAdder();
adder.increment();
adder.add(10);
long sum = adder.sum();  // Eventually consistent
```

### Difference between Atomic vs Volatile

| Feature | Atomic | Volatile |
|---------|--------|----------|
| **Visibility** | Yes | Yes |
| **Atomicity** | Yes (for compound operations) | No (only single read/write) |
| **Compound operations** | Supported (incrementAndGet, etc.) | Not supported |
| **Performance** | Slightly slower (CAS overhead) | Faster |
| **Memory barrier** | Full barrier | Partial barrier |
| **Use case** | Counters, accumulators, state updates | Simple flags, status variables |

```java
public class AtomicVsVolatile {
    private volatile int volatileCounter = 0;
    private AtomicInteger atomicCounter = new AtomicInteger(0);
    
    public void increment() {
        // Volatile - NOT atomic, can lose updates
        volatileCounter++;  // Actually 3 operations: read, add, write
        
        // Atomic - atomic operation
        atomicCounter.incrementAndGet();  // Single atomic operation
    }
    
    // To make volatile atomic, need synchronization
    public synchronized void incrementVolatileSafe() {
        volatileCounter++;
    }
    
    // Check-then-act pattern
    public void checkThenAct() {
        // With volatile - NOT safe
        if (volatileCounter == 10) {  // Read
            volatileCounter = 20;      // Write - but value may have changed!
        }
        
        // With atomic - safe
        atomicCounter.compareAndSet(10, 20);  // Atomic check-and-set
    }
}
```

### What happens when multiple threads access shared variables?

**Race Condition:** Multiple threads accessing shared data concurrently, leading to unpredictable results.

```java
public class RaceConditionDemo {
    private static int sharedCounter = 0;
    
    // Problem: Two threads incrementing same variable
    public static void main(String[] args) throws InterruptedException {
        // Without synchronization - RACE CONDITION
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                sharedCounter++;  // NOT atomic
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                sharedCounter++;  // NOT atomic
            }
        });
        
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        
        // Result is NOT 20000 - due to lost updates
        System.out.println("Final counter: " + sharedCounter);
        
        // Solutions to prevent race conditions:
        
        // 1. Synchronized block
        Object lock = new Object();
        synchronized(lock) {
            sharedCounter++;
        }
        
        // 2. Synchronized method
        // public synchronized void increment() { counter++; }
        
        // 3. ReentrantLock
        Lock lock2 = new ReentrantLock();
        lock2.lock();
        try {
            sharedCounter++;
        } finally {
            lock2.unlock();
        }
        
        // 4. Atomic classes
        AtomicInteger atomicCounter = new AtomicInteger();
        atomicCounter.incrementAndGet();
        
        // 5. Volatile + CAS (Compare-And-Swap)
        // Implemented by atomic classes
    }
}
```

**Memory Visibility Problems:**
```java
public class VisibilityProblem {
    private static boolean flag = true;  // Without volatile
    
    public static void main(String[] args) throws InterruptedException {
        Thread worker = new Thread(() -> {
            while (flag) {
                // Busy wait - may never see flag = false
            }
            System.out.println("Worker stopped");
        });
        
        worker.start();
        Thread.sleep(1000);
        flag = false;  // May not be visible to worker thread
        System.out.println("Main set flag to false");
    }
    // Solution: Make flag volatile
}
```

### How did you handle Concurrency in your project?

**Sample answer with examples:**

"In my previous project, I handled concurrency in several ways:

**1. Used Concurrent Collections instead of synchronized wrappers:**
```java
// Instead of
Map<String, User> map = Collections.synchronizedMap(new HashMap<>());

// Used
ConcurrentHashMap<String, User> concurrentMap = new ConcurrentHashMap<>();
```

**2. Implemented thread-safe counters using AtomicInteger:**
```java
private AtomicInteger requestCounter = new AtomicInteger(0);

public void processRequest() {
    int requestId = requestCounter.incrementAndGet();
    // Process with unique ID
}
```

**3. Used ExecutorService for managing thread pools:**
```java
ExecutorService executor = Executors.newFixedThreadPool(10);
List<CompletableFuture<Void>> futures = tasks.stream()
    .map(task -> CompletableFuture.runAsync(() -> processTask(task), executor))
    .collect(Collectors.toList());
CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();
```

**4. Implemented proper locking for critical sections:**
```java
private final ReadWriteLock lock = new ReentrantReadWriteLock();

public void updateCache() {
    lock.writeLock().lock();
    try {
        // Update cache
    } finally {
        lock.writeLock().unlock();
    }
}
```

**5. Used volatile for status flags:**
```java
private volatile boolean shutdown = false;

public void shutdown() {
    shutdown = true;
}
```

**6. Implemented rate limiting using Semaphore:**
```java
private final Semaphore rateLimiter = new Semaphore(100);

public void handleRequest() {
    if (rateLimiter.tryAcquire()) {
        try {
            // Process request
        } finally {
            rateLimiter.release();
        }
    } else {
        throw new RateLimitExceededException();
    }
}
```

**7. Avoided deadlocks by:**
- Consistent lock ordering
- Using tryLock() with timeout
- Minimizing synchronized blocks

**8. Used CompletableFuture for async processing:**
```java
CompletableFuture.supplyAsync(() -> fetchData())
    .thenApply(data -> process(data))
    .thenAccept(result -> save(result))
    .exceptionally(ex -> {
        log.error("Error: ", ex);
        return null;
    });
```"

---

## 🔹 6. Spring & Spring Boot

### What is Spring Boot?

**Spring Boot** is a framework that simplifies Spring application development by providing auto-configuration, starter dependencies, and embedded servers.

**Key features:**
- Auto-configuration (configures beans based on classpath)
- Starter dependencies (curated dependency sets)
- Embedded servers (Tomcat, Jetty, Undertow)
- Production-ready features (actuator, metrics, health checks)
- No XML configuration (annotation-based)

```java
// Minimal Spring Boot Application
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// @SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan
```

**Spring Boot vs Spring:**
| Feature | Spring | Spring Boot |
|---------|--------|-------------|
| **Configuration** | Manual XML/Java config | Auto-configuration |
| **Dependencies** | Need to manage versions | Starter POMs |
| **Deployment** | Requires external server | Embedded server |
| **Setup time** | Longer | Rapid |
| **When to use** | Complex, custom requirements | Standard microservices, REST APIs |

### Explain Dependency Injection (DI)

**Dependency Injection** is a design pattern where objects receive their dependencies from an external source rather than creating them internally. It promotes loose coupling and testability.

**Without DI (Tight coupling):**
```java
class EmailService {
    public void sendEmail(String to, String message) {
        // Email sending logic
    }
}

class UserService {
    private EmailService emailService = new EmailService();  // Tight coupling
    
    public void registerUser(User user) {
        // Registration logic
        emailService.sendEmail(user.getEmail(), "Welcome!");
    }
}
// Hard to test, can't easily change EmailService
```

**With DI (Loose coupling):**
```java
// Define interface
interface MessageService {
    void sendMessage(String to, String message);
}

// Implementation
class EmailService implements MessageService {
    @Override
    public void sendMessage(String to, String message) {
        System.out.println("Sending email to " + to + ": " + message);
    }
}

class SMSService implements MessageService {
    @Override
    public void sendMessage(String to, String message) {
        System.out.println("Sending SMS to " + to + ": " + message);
    }
}

// Client with DI
class UserService {
    private final MessageService messageService;
    
    // Constructor Injection (preferred)
    public UserService(MessageService messageService) {
        this.messageService = messageService;
    }
    
    public void registerUser(User user) {
        // Registration logic
        messageService.sendMessage(user.getContact(), "Welcome!");
    }
}

// Usage
MessageService emailService = new EmailService();
UserService userService = new UserService(emailService);  // Dependency injected
```

### What is IOC (Inversion of Control)?

**IOC (Inversion of Control)** is a principle where the control of object creation and lifecycle is inverted from the application to a container (Spring Container).

**Traditional control (Application creates objects):**
```java
class Application {
    public static void main(String[] args) {
        // Application controls object creation
        ServiceA serviceA = new ServiceA();
        ServiceB serviceB = new ServiceB(serviceA);
        Controller controller = new Controller(serviceB);
        controller.execute();
    }
}
```

**IOC (Container controls objects):**
```java
@Configuration
class AppConfig {
    @Bean
    public ServiceA serviceA() {
        return new ServiceA();
    }
    
    @Bean
    public ServiceB serviceB() {
        return new ServiceB(serviceA());  // Container manages dependencies
    }
    
    @Bean
    public Controller controller() {
        return new Controller(serviceB());
    }
}

// Application just requests the bean
public class Application {
    public static void main(String[] args) {
        ApplicationContext context = 
            new AnnotationConfigApplicationContext(AppConfig.class);
        
        // Container gives ready-to-use object
        Controller controller = context.getBean(Controller.class);
        controller.execute();
    }
}
```

**IOC Container responsibilities:**
- Create objects (beans)
- Wire dependencies
- Manage lifecycle (init, destroy)
- Provide configuration

### Types of Dependency Injection

**1. Constructor Injection (Recommended - Spring favors this):**
```java
@Component
class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    @Autowired  // Optional from Spring 4.3+
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}
// Benefits: Immutable, required dependencies, testable, prevents circular dependencies
```

**2. Setter Injection:**
```java
@Component
class UserService {
    private UserRepository userRepository;
    
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
// Benefits: Optional dependencies, allows reconfiguration
```

**3. Field Injection (NOT recommended):**
```java
@Component
class UserService {
    @Autowired
    private UserRepository userRepository;  // Hard to test, violates SRP
}
// Problems: Can't make final, hard to unit test, hides dependencies
```

**Comparison:**
| Type | Pros | Cons | When to use |
|------|------|------|-------------|
| Constructor | Immutable, required deps, testable | Verbose for many deps | ALWAYS preferred |
| Setter | Optional deps, reconfigurable | Mutable, can be null | For optional dependencies |
| Field | Concise | Hard to test, hidden deps | NEVER (except quick prototypes) |

### What is @Qualifier?

**@Qualifier** is used to resolve ambiguity when multiple beans of the same type exist.

```java
// Multiple implementations of same interface
@Component
class EmailService implements MessageService {
    @Override
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

@Component
class SMSService implements MessageService {
    @Override
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}

// Without @Qualifier - Ambiguous! Spring doesn't know which to inject
@Component
class NotificationService {
    @Autowired
    private MessageService messageService;  // ERROR: 2 beans found!
}

// Solution 1: Using @Qualifier
@Component
class NotificationService {
    @Autowired
    @Qualifier("emailService")  // Bean name (default is class name with lowercase first letter)
    private MessageService messageService;
}

// Solution 2: Custom qualifier annotations
@Qualifier
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@interface Email {}

@Component
@Email
class EmailService implements MessageService { }

@Component
class NotificationService {
    @Autowired
    @Email  // Custom qualifier
    private MessageService messageService;
}

// Solution 3: @Primary (see next question)
```

### What is @Primary?

**@Primary** indicates that a bean should be given preference when multiple candidates are qualified for autowiring.

```java
@Component
@Primary  // This bean will be chosen when ambiguity exists
class EmailService implements MessageService {
    @Override
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

@Component
class SMSService implements MessageService {
    @Override
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}

@Component
class NotificationService {
    @Autowired
    private MessageService messageService;  // Gets EmailService (due to @Primary)
}

// @Primary vs @Qualifier
// @Primary: Default bean when no specific choice
// @Qualifier: Explicitly specify which bean
```

### Difference between @Qualifier and @Primary

| Feature | @Qualifier | @Primary |
|---------|------------|----------|
| **Purpose** | Specify exact bean by name | Set default bean |
| **Specificity** | More specific | Less specific |
| **Overriding** | Overrides @Primary | Can be overridden by @Qualifier |
| **Multiple beans** | Can use multiple | Only one per type |
| **When to use** | Need to choose specific implementation | One implementation is default |

```java
// Example combining both
@Component
@Primary
class DefaultEmailService implements MessageService { }

@Component
class SpecialEmailService implements MessageService { }

@Component
class NotificationService {
    // Gets DefaultEmailService (due to @Primary)
    @Autowired
    private MessageService messageService1;
    
    // Gets SpecialEmailService (overrides @Primary)
    @Autowired
    @Qualifier("specialEmailService")
    private MessageService messageService2;
}
```

### What is Bean Scope?

**Bean Scope** defines the lifecycle and visibility of a bean in the Spring container.

**Scopes in Spring:**

**1. Singleton (Default):** Single instance per Spring container
```java
@Component
@Scope("singleton")  // Default, can omit
class UserService { }
// Same instance returned for all requests
```

**2. Prototype:** New instance each time requested
```java
@Component
@Scope("prototype")
class ShoppingCart { }
// New instance every time injected or requested
```

**3. Request (Web-aware):** Single instance per HTTP request
```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
class RequestData { }
// New instance for each HTTP request
```

**4. Session (Web-aware):** Single instance per HTTP session
```java
@Component
@Scope(value = "session", proxyMode = ScopedProxyMode.TARGET_CLASS)
class UserSession { }
// Same instance for all requests in same session
```

**5. Application (Web-aware):** Single instance per ServletContext
```java
@Component
@Scope("application")
class AppData { }
// Same instance across all sessions and requests
```

**6. Websocket:** Single instance per WebSocket session
```java
@Component
@Scope("websocket")
class WebSocketData { }
```

**Examples:**
```java
@Configuration
public class ScopeConfig {
    
    @Bean
    @Scope("singleton")
    public Counter singletonCounter() {
        return new Counter();  // One instance
    }
    
    @Bean
    @Scope("prototype")
    public Counter prototypeCounter() {
        return new Counter();  // New instance each time
    }
}

@Component
public class ScopeDemo {
    @Autowired
    private Counter singletonCounter1;
    
    @Autowired
    private Counter singletonCounter2;  // Same as singletonCounter1
    
    @Autowired
    private Counter prototypeCounter1;
    
    @Autowired
    private Counter prototypeCounter2;  // Different from prototypeCounter1
    
    public void demo() {
        System.out.println(singletonCounter1 == singletonCounter2);  // true
        System.out.println(prototypeCounter1 == prototypeCounter2);  // false
    }
}
```

### What is an Entity in Spring Boot?

**Entity** is a JPA annotation that maps a Java class to a database table.

```java
@Entity
@Table(name = "employees")
public class Employee {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "full_name", nullable = false, length = 100)
    private String name;
    
    @Column(unique = true, nullable = false)
    private String email;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @Enumerated(EnumType.STRING)
    private Department department;
    
    @Transient  // Not persisted
    private String temporaryData;
    
    @OneToMany(mappedBy = "employee", cascade = CascadeType.ALL)
    private List<Address> addresses;
    
    // Constructors, getters, setters
    public Employee() {}  // Required for JPA
    
    public Employee(String name, String email) {
        this.name = name;
        this.email = email;
        this.createdAt = LocalDateTime.now();
    }
    
    // Getters and setters...
}
```

**Entity annotations:**
- `@Entity`: Marks class as JPA entity
- `@Table`: Specifies table details (name, schema, indexes)
- `@Id`: Primary key
- `@GeneratedValue`: Auto-generation strategy
- `@Column`: Column mapping (name, nullable, unique, length)
- `@Transient`: Field not persisted
- `@Enumerated`: Enum mapping
- `@Temporal`: Date/time mapping
- `@Lob`: Large object (BLOB/CLOB)

### Important Spring Boot Annotations

**Core Annotations:**
```java
@SpringBootApplication  // Main class annotation (combines 3 below)
@Configuration          // Bean definition source
@EnableAutoConfiguration // Auto-configuration based on classpath
@ComponentScan         // Scan for components in package
```

**Stereotype Annotations:**
```java
@Component    // Generic component
@Service      // Service layer (business logic)
@Repository   // DAO layer (database operations)
@Controller   // Web controller (returns view)
@RestController // REST controller (@Controller + @ResponseBody)
```

**Dependency Injection:**
```java
@Autowired      // Inject dependency
@Qualifier      // Specify which bean
@Primary        // Default bean
@Value          // Inject property values
@Lazy           // Lazy initialization
```

**Request Mapping:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping("/{id}")           // HTTP GET
    public User getUser(@PathVariable Long id) { }
    
    @PostMapping                   // HTTP POST
    public User createUser(@RequestBody User user) { }
    
    @PutMapping("/{id}")           // HTTP PUT
    public User updateUser(@PathVariable Long id, @RequestBody User user) { }
    
    @DeleteMapping("/{id}")        // HTTP DELETE
    public void deleteUser(@PathVariable Long id) { }
    
    @PatchMapping("/{id}")         // HTTP PATCH
    public User partialUpdate(@PathVariable Long id, @RequestBody Map<String, Object> updates) { }
}
```

**Request Parameters:**
```java
@GetMapping("/search")
public List<User> searchUsers(
    @RequestParam(required = false, defaultValue = "1") int page,
    @RequestParam int size,
    @RequestHeader("User-Agent") String userAgent,
    @CookieValue("sessionId") String sessionId
) { }
```

**Validation:**
```java
@PostMapping
public User createUser(@Valid @RequestBody User user) { }

// In entity
public class User {
    @NotNull @NotBlank @Size(min=2, max=50)
    private String name;
    
    @NotNull @Email
    private String email;
    
    @Min(18) @Max(120)
    private int age;
    
    @Pattern(regexp = "^\\d{10}$")
    private String phone;
}
```

**Transaction Management:**
```java
@Service
public class UserService {
    @Transactional
    public void createUser(User user) {
        // Database operations - all succeed or all fail
    }
    
    @Transactional(readOnly = true)
    public User findUser(Long id) { }
    
    @Transactional(rollbackFor = Exception.class)
    public void riskyOperation() { }
}
```

**Configuration Properties:**
```java
@ConfigurationProperties(prefix = "app")
@Component
public class AppConfig {
    private String name;
    private int maxConnections;
    private List<String> allowedOrigins;
    private Map<String, String> endpoints;
    // getters/setters
}

// application.yml
app:
  name: MyApp
  max-connections: 100
  allowed-origins:
    - http://localhost:3000
    - https://example.com
  endpoints:
    health: /health
    metrics: /metrics
```

### Explain Spring Security

**Spring Security** is a powerful authentication and access control framework for Spring applications.

**Core Concepts:**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    // 1. Configure Authentication (who are you?)
    @Bean
    public UserDetailsService userDetailsService() {
        UserDetails user = User.builder()
            .username("user")
            .password(passwordEncoder().encode("password"))
            .roles("USER")
            .build();
            
        UserDetails admin = User.builder()
            .username("admin")
            .password(passwordEncoder().encode("admin123"))
            .roles("ADMIN", "USER")
            .build();
            
        return new InMemoryUserDetailsManager(user, admin);
    }
    
    // 2. Password Encoding
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();  // Strong hashing
    }
    
    // 3. Configure Authorization (what can you do?)
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()  // For REST APIs
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**", "/auth/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)  // JWT
            )
            .httpBasic(Customizer.withDefaults());  // Basic Auth
            
        return http.build();
    }
}
```

### What is @PreAuthorize?

**@PreAuthorize** is a method-level security annotation that checks authorization before method execution.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // Role-based access
    @PreAuthorize("hasRole('ADMIN')")
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    // Permission-based
    @PreAuthorize("hasAuthority('USER_READ')")
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    // Method parameter evaluation
    @PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
    @PutMapping("/{userId}")
    public User updateUser(@PathVariable Long userId, @RequestBody User user) {
        return userService.update(userId, user);
    }
    
    // Custom logic
    @PreAuthorize("@securityService.isOwner(#resourceId, authentication.name)")
    @DeleteMapping("/resources/{resourceId}")
    public void deleteResource(@PathVariable Long resourceId) {
        resourceService.delete(resourceId);
    }
    
    // SpEL expressions
    @PreAuthorize("hasRole('ADMIN') and #user.department == 'IT'")
    public void processUser(User user) { }
}

// Custom security service
@Component("securityService")
public class SecurityService {
    public boolean isOwner(Long resourceId, String username) {
        Resource resource = resourceService.findById(resourceId);
        return resource.getOwner().equals(username);
    }
}
```

**Other security annotations:**
- `@PostAuthorize`: Check after method execution
- `@PreFilter`: Filter collection arguments
- `@PostFilter`: Filter return values
- `@Secured`: Simple role-based (older, less flexible)

### What is Filter Chain in Spring Security?

**Filter Chain** is a sequence of filters that process each HTTP request. Spring Security adds its own filters to this chain.

```java
// Security filter chain order (simplified)
// 1. DisableUrlSessionFilter
// 2. WebAsyncManagerIntegrationFilter
// 3. SecurityContextPersistenceFilter (loads SecurityContext)
// 4. HeaderWriterFilter (adds security headers)
// 5. CsrfFilter (CSRF protection)
// 6. LogoutFilter (handles logout)
// 7. UsernamePasswordAuthenticationFilter (handles login)
// 8. RequestCacheAwareFilter
// 9. SecurityContextHolderAwareRequestFilter
// 10. AnonymousAuthenticationFilter
// 11. SessionManagementFilter
// 12. ExceptionTranslationFilter (handles AccessDeniedException)
// 13. FilterSecurityInterceptor (authorization)

@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .addFilterBefore(new CustomFilter(), UsernamePasswordAuthenticationFilter.class)
            .addFilterAfter(new AnotherFilter(), CsrfFilter.class)
            .addFilterAt(new PositionFilter(), BasicAuthenticationFilter.class);
        
        return http.build();
    }
}

// Custom filter example
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                    HttpServletResponse response, 
                                    FilterChain filterChain) throws IOException, ServletException {
        
        String token = extractToken(request);
        
        if (token != null && validateToken(token)) {
            Authentication auth = getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(auth);
        }
        
        filterChain.doFilter(request, response);  // Continue to next filter
    }
}
```

### How do you secure APIs?

**Complete API Security Example:**

```java
// 1. Dependencies
// build.gradle
implementation 'org.springframework.boot:spring-boot-starter-security'
implementation 'io.jsonwebtoken:jjwt-api:0.11.5'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.5'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.11.5'

// 2. JWT Utility Class
@Component
public class JwtUtil {
    @Value("${jwt.secret}")
    private String secret;
    
    @Value("${jwt.expiration}")
    private Long expiration;
    
    public String generateToken(String username) {
        return Jwts.builder()
            .setSubject(username)
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + expiration))
            .signWith(SignatureAlgorithm.HS256, secret)
            .compact();
    }
    
    public String extractUsername(String token) {
        return Jwts.parser().setSigningKey(secret)
            .parseClaimsJws(token).getBody().getSubject();
    }
    
    public Boolean validateToken(String token) {
        try {
            Jwts.parser().setSigningKey(secret).parseClaimsJws(token);
            return true;
        } catch (Exception e) {
            return false;
        }
    }
}

// 3. Authentication Filter
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Autowired
    private JwtUtil jwtUtil;
    
    @Autowired
    private UserDetailsService userDetailsService;
    
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain chain) throws IOException, ServletException {
        
        final String authHeader = request.getHeader("Authorization");
        
        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            String token = authHeader.substring(7);
            String username = jwtUtil.extractUsername(token);
            
            if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
                UserDetails userDetails = userDetailsService.loadUserByUsername(username);
                
                if (jwtUtil.validateToken(token)) {
                    UsernamePasswordAuthenticationToken auth = 
                        new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
                    auth.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                    SecurityContextHolder.getContext().setAuthentication(auth);
                }
            }
        }
        
        chain.doFilter(request, response);
    }
}

// 4. Authentication Controller
@RestController
@RequestMapping("/auth")
public class AuthController {
    
    @Autowired
    private AuthenticationManager authManager;
    
    @Autowired
    private JwtUtil jwtUtil;
    
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody AuthRequest request) {
        try {
            Authentication authentication = authManager.authenticate(
                new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
            );
            
            String token = jwtUtil.generateToken(request.getUsername());
            return ResponseEntity.ok(new AuthResponse(token));
        } catch (BadCredentialsException e) {
            return ResponseEntity.status(401).body("Invalid credentials");
        }
    }
}

// 5. Security Configuration
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig {
    
    @Autowired
    private JwtAuthenticationFilter jwtFilter;
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .cors().and()
            .authorizeRequests()
                .antMatchers("/auth/**", "/public/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS);
        
        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }
    
    @Bean
    public AuthenticationManager authManager(AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}

// 6. Protected Controller
@RestController
@RequestMapping("/api")
public class UserController {
    
    @PreAuthorize("hasRole('USER')")
    @GetMapping("/user-data")
    public UserData getUserData() {
        return userService.getCurrentUser();
    }
    
    @PreAuthorize("hasRole('ADMIN')")
    @GetMapping("/admin-data")
    public AdminData getAdminData() {
        return adminService.getData();
    }
}
```

---
# Antino Labs Interview Questions - Complete Answers Guide (Continued)

## 🔹 7. REST APIs & Microservices

### What are REST APIs?

**REST (Representational State Transfer) API** is an architectural style for designing networked applications. It uses HTTP protocols and standard methods to perform CRUD operations.

**Key principles of REST:**

1. **Stateless:** Each request contains all necessary information (no client context stored on server)
2. **Client-Server:** Separation of concerns
3. **Cacheable:** Responses must define themselves as cacheable or not
4. **Uniform Interface:** Standard HTTP methods and status codes
5. **Layered System:** Client can't tell if connected directly to end server

```java
// REST API Example - Employee Management
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {
    
    // GET - Retrieve resource
    @GetMapping
    public List<Employee> getAllEmployees() {
        return employeeService.findAll();
    }
    
    // GET with ID - Retrieve specific resource
    @GetMapping("/{id}")
    public Employee getEmployeeById(@PathVariable Long id) {
        return employeeService.findById(id);
    }
    
    // POST - Create new resource
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Employee createEmployee(@Valid @RequestBody Employee employee) {
        return employeeService.save(employee);
    }
    
    // PUT - Complete update
    @PutMapping("/{id}")
    public Employee updateEmployee(@PathVariable Long id, @Valid @RequestBody Employee employee) {
        return employeeService.update(id, employee);
    }
    
    // PATCH - Partial update
    @PatchMapping("/{id}")
    public Employee partialUpdate(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
        return employeeService.partialUpdate(id, updates);
    }
    
    // DELETE - Remove resource
    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteEmployee(@PathVariable Long id) {
        employeeService.delete(id);
    }
}
```

**HTTP Methods mapping to CRUD:**
| HTTP Method | CRUD Operation | Idempotent? | Safe? |
|-------------|---------------|-------------|-------|
| GET | Read | Yes | Yes |
| POST | Create | No | No |
| PUT | Update (full) | Yes | No |
| PATCH | Update (partial) | No | No |
| DELETE | Delete | Yes | No |

### Explain API Development Flow in Spring Boot

**Complete API development flow from requirements to deployment:**

```java
// Step 1: Define Entity (Model)
@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @NotBlank
    private String name;
    
    @NotNull
    @Min(0)
    private BigDecimal price;
    
    private String description;
    
    // Constructors, getters, setters
}

// Step 2: Create Repository (Data Access Layer)
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByPriceBetween(BigDecimal min, BigDecimal max);
    Optional<Product> findByNameIgnoreCase(String name);
    
    @Query("SELECT p FROM Product p WHERE p.price > :minPrice")
    List<Product> findExpensiveProducts(@Param("minPrice") BigDecimal minPrice);
}

// Step 3: Create Service (Business Logic Layer)
@Service
@Transactional
public class ProductService {
    
    @Autowired
    private ProductRepository productRepository;
    
    public Product createProduct(Product product) {
        // Business validation
        if (product.getPrice().compareTo(BigDecimal.ZERO) <= 0) {
            throw new InvalidPriceException("Price must be positive");
        }
        
        // Check for duplicate
        if (productRepository.findByNameIgnoreCase(product.getName()).isPresent()) {
            throw new DuplicateProductException("Product name already exists");
        }
        
        return productRepository.save(product);
    }
    
    @Transactional(readOnly = true)
    public Product getProduct(Long id) {
        return productRepository.findById(id)
            .orElseThrow(() -> new ProductNotFoundException("Product not found: " + id));
    }
    
    public Product updateProduct(Long id, Product product) {
        Product existing = getProduct(id);
        existing.setName(product.getName());
        existing.setPrice(product.getPrice());
        existing.setDescription(product.getDescription());
        return productRepository.save(existing);
    }
}

// Step 4: Create DTO (Data Transfer Object)
public class ProductDTO {
    private Long id;
    private String name;
    private BigDecimal price;
    
    // Constructor, getters, setters
    
    public static ProductDTO fromEntity(Product product) {
        ProductDTO dto = new ProductDTO();
        dto.setId(product.getId());
        dto.setName(product.getName());
        dto.setPrice(product.getPrice());
        return dto;
    }
    
    public Product toEntity() {
        Product product = new Product();
        product.setId(this.id);
        product.setName(this.name);
        product.setPrice(this.price);
        return product;
    }
}

// Step 5: Create Controller (API Layer)
@RestController
@RequestMapping("/api/products")
public class ProductController {
    
    @Autowired
    private ProductService productService;
    
    @GetMapping
    public ResponseEntity<List<ProductDTO>> getAllProducts() {
        List<ProductDTO> products = productService.findAll().stream()
            .map(ProductDTO::fromEntity)
            .collect(Collectors.toList());
        return ResponseEntity.ok(products);
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<ProductDTO> getProduct(@PathVariable Long id) {
        Product product = productService.getProduct(id);
        return ResponseEntity.ok(ProductDTO.fromEntity(product));
    }
    
    @PostMapping
    public ResponseEntity<ProductDTO> createProduct(@Valid @RequestBody ProductDTO productDTO) {
        Product product = productService.createProduct(productDTO.toEntity());
        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(product.getId())
            .toUri();
        return ResponseEntity.created(location).body(ProductDTO.fromEntity(product));
    }
}

// Step 6: Exception Handling
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ProductNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponse handleNotFound(ProductNotFoundException e) {
        return new ErrorResponse("NOT_FOUND", e.getMessage());
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public Map<String, String> handleValidation(MethodArgumentNotValidException e) {
        return e.getBindingResult().getFieldErrors().stream()
            .collect(Collectors.toMap(
                FieldError::getField,
                FieldError::getDefaultMessage
            ));
    }
}

// Step 7: API Documentation (OpenAPI/Swagger)
// Add dependency: springdoc-openapi-ui
@Configuration
public class OpenAPIConfig {
    
    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("Product API")
                .version("1.0")
                .description("Product Management API")
                .contact(new Contact()
                    .name("Developer Team")
                    .email("dev@company.com")))
            .servers(List.of(
                new Server().url("http://localhost:8080").description("Development"),
                new Server().url("https://api.company.com").description("Production")
            ));
    }
}

// Step 8: Application Properties
// application.yml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/productdb
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

server:
  port: 8080
  error:
    include-message: always

logging:
  level:
    com.example: DEBUG
```

### How to create a GET API for Employee by ID (with annotations)

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {
    
    @Autowired
    private EmployeeService employeeService;
    
    // Complete GET API with all options
    @GetMapping("/{id}")
    public ResponseEntity<EmployeeResponseDTO> getEmployeeById(
            @PathVariable(name = "id") Long employeeId,
            @RequestParam(defaultValue = "false") boolean includeDetails,
            @RequestHeader(value = "Accept-Language", defaultValue = "en") String language,
            @RequestAttribute("requestId") String requestId) {
        
        Employee employee = employeeService.findById(employeeId);
        
        if (includeDetails) {
            EmployeeResponseDTO response = EmployeeResponseDTO.withDetails(employee, language);
            return ResponseEntity.ok(response);
        } else {
            EmployeeResponseDTO response = EmployeeResponseDTO.basic(employee);
            return ResponseEntity.ok(response);
        }
    }
    
    // Alternative: Using Optional return type
    @GetMapping("/v2/{id}")
    public ResponseEntity<Employee> getEmployeeByIdV2(@PathVariable Long id) {
        return employeeService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    // With custom headers
    @GetMapping("/{id}/details")
    public ResponseEntity<Employee> getEmployeeWithCache(@PathVariable Long id) {
        Employee employee = employeeService.findById(id);
        return ResponseEntity.ok()
            .header("Cache-Control", "max-age=3600")
            .header("ETag", generateETag(employee))
            .body(employee);
    }
}

// DTO for response
public class EmployeeResponseDTO {
    private Long id;
    private String fullName;
    private String email;
    private String department;
    private LocalDate joiningDate;
    
    public static EmployeeResponseDTO basic(Employee employee) {
        EmployeeResponseDTO dto = new EmployeeResponseDTO();
        dto.setId(employee.getId());
        dto.setFullName(employee.getFirstName() + " " + employee.getLastName());
        dto.setEmail(employee.getEmail());
        return dto;
    }
    
    public static EmployeeResponseDTO withDetails(Employee employee, String language) {
        EmployeeResponseDTO dto = basic(employee);
        dto.setDepartment(employee.getDepartment());
        dto.setJoiningDate(employee.getJoiningDate());
        // Language-specific formatting
        return dto;
    }
}
```

### What is 401 vs 201 HTTP response?

**HTTP Status Codes - Detailed:**

```java
// 2xx Success
@PostMapping("/employees")
@ResponseStatus(HttpStatus.CREATED)  // 201 - Resource created
public Employee createEmployee(@RequestBody Employee employee) {
    return employeeService.save(employee);
}

@GetMapping("/employees/{id}")
@ResponseStatus(HttpStatus.OK)  // 200 - Success
public Employee getEmployee(@PathVariable Long id) {
    return employeeService.findById(id);
}

@DeleteMapping("/employees/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)  // 204 - Success, no content to return
public void deleteEmployee(@PathVariable Long id) {
    employeeService.delete(id);
}

// 4xx Client Errors
@ResponseStatus(HttpStatus.BAD_REQUEST)  // 400 - Bad request
public class InvalidInputException extends RuntimeException { }

@ResponseStatus(HttpStatus.UNAUTHORIZED)  // 401 - Authentication required
public class UnauthorizedException extends RuntimeException { }

@ResponseStatus(HttpStatus.FORBIDDEN)  // 403 - Authenticated but not authorized
public class ForbiddenException extends RuntimeException { }

@ResponseStatus(HttpStatus.NOT_FOUND)  // 404 - Resource not found
public class ResourceNotFoundException extends RuntimeException { }

@ResponseStatus(HttpStatus.CONFLICT)  // 409 - Conflict (duplicate resource)
public class DuplicateResourceException extends RuntimeException { }

@ResponseStatus(HttpStatus.UNPROCESSABLE_ENTITY)  // 422 - Validation failed
public class ValidationException extends RuntimeException { }

// 5xx Server Errors
@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)  // 500 - Generic server error
public class DatabaseException extends RuntimeException { }

@ResponseStatus(HttpStatus.SERVICE_UNAVAILABLE)  // 503 - Service temporarily unavailable
public class ServiceUnavailableException extends RuntimeException { }
```

**Detailed Comparison: 201 vs 401**

| Aspect | 201 Created | 401 Unauthorized |
|--------|-------------|------------------|
| **Category** | 2xx Success | 4xx Client Error |
| **Meaning** | Resource successfully created | Missing or invalid authentication |
| **When to use** | After POST/PUT that creates resource | When user not logged in or invalid credentials |
| **Response body** | Usually contains created resource or location | Often contains error message |
| **Retry after fix** | N/A | Yes, after providing credentials |
| **Common headers** | Location header with resource URI | WWW-Authenticate header |

```java
// 201 Created - Example
@PostMapping("/employees")
public ResponseEntity<Employee> createEmployee(@RequestBody Employee employee) {
    Employee saved = employeeService.save(employee);
    return ResponseEntity
        .created(URI.create("/api/employees/" + saved.getId()))
        .header("X-Custom-Header", "EmployeeCreated")
        .body(saved);
}

// Response:
// HTTP/1.1 201 Created
// Location: /api/employees/123
// X-Custom-Header: EmployeeCreated
// {
//   "id": 123,
//   "name": "John Doe",
//   "email": "john@example.com"
// }

// 401 Unauthorized - Example
@RestControllerAdvice
public class SecurityExceptionHandler {
    
    @ExceptionHandler(AuthenticationException.class)
    public ResponseEntity<ErrorResponse> handleUnauthorized(AuthenticationException e) {
        return ResponseEntity
            .status(HttpStatus.UNAUTHORIZED)
            .header("WWW-Authenticate", "Bearer realm=\"api\"")
            .body(new ErrorResponse("UNAUTHORIZED", "Authentication required"));
    }
}

// Response:
// HTTP/1.1 401 Unauthorized
// WWW-Authenticate: Bearer realm="api"
// {
//   "error": "UNAUTHORIZED",
//   "message": "Authentication required"
// }
```

### Monolithic vs Microservices Architecture

**Monolithic Architecture:** Single, unified application where all components are tightly coupled.

**Microservices Architecture:** Application composed of small, independent services, each with its own responsibility.

```java
// MONOLITHIC ARCHITECTURE
// Single application handling everything
@SpringBootApplication
public class MonolithicApp {
    // Contains all modules in one deployment
    // - User management
    // - Order processing
    // - Inventory management
    // - Payment processing
    // - Shipping
}

// Single database for everything
// One deployment artifact (one JAR/WAR)
// Scaling: Must scale entire application
```

```java
// MICROSERVICES ARCHITECTURE
// Service 1: User Service
@SpringBootApplication
@EnableEurekaClient
public class UserService {
    // Handles user CRUD, authentication
    // Own database: user_db
    // Port: 8081
}

// Service 2: Order Service
@SpringBootApplication
@EnableEurekaClient
public class OrderService {
    // Handles orders
    // Own database: order_db
    // Port: 8082
}

// Service 3: Inventory Service
@SpringBootApplication
@EnableEurekaClient
public class InventoryService {
    // Handles stock
    // Own database: inventory_db
    // Port: 8083
}
```

**Comparison Table:**

| Aspect | Monolithic | Microservices |
|--------|------------|---------------|
| **Deployment** | Single unit | Independent services |
| **Scalability** | Scale everything | Scale only needed services |
| **Development** | Simple initially | Complex (distributed systems) |
| **Team structure** | Single team | Multiple small teams |
| **Technology** | Single stack | Polyglot (different per service) |
| **Database** | Single database | Database per service |
| **Communication** | In-memory calls | HTTP/RPC/Messaging |
| **Testing** | End-to-end easier | Integration testing complex |
| **Fault isolation** | One bug affects all | Isolated to service |
| **Data consistency** | ACID transactions | Eventual consistency |
| **Time to deploy** | Longer (full rebuild) | Faster (only changed service) |

### How do Microservices communicate?

**Communication patterns in microservices:**

```java
// 1. SYNCHRONOUS COMMUNICATION (HTTP/REST)
// Order Service calling User Service
@Service
public class OrderService {
    
    @Autowired
    private RestTemplate restTemplate;  // Or WebClient
    
    public Order createOrder(OrderRequest request) {
        // Synchronous call to User Service
        User user = restTemplate.getForObject(
            "http://user-service/api/users/" + request.getUserId(),
            User.class
        );
        
        // Check inventory
        InventoryResponse inventory = restTemplate.getForObject(
            "http://inventory-service/api/inventory/" + request.getProductId(),
            InventoryResponse.class
        );
        
        if (inventory.getQuantity() < request.getQuantity()) {
            throw new InsufficientStockException();
        }
        
        return saveOrder(request, user);
    }
}

// Using WebClient (non-blocking, reactive)
@Service
public class ReactiveOrderService {
    
    @Autowired
    private WebClient.Builder webClientBuilder;
    
    public Mono<Order> createOrder(OrderRequest request) {
        return webClientBuilder.build()
            .get()
            .uri("http://user-service/api/users/{id}", request.getUserId())
            .retrieve()
            .bodyToMono(User.class)
            .flatMap(user -> {
                return webClientBuilder.build()
                    .get()
                    .uri("http://inventory-service/api/inventory/{productId}", request.getProductId())
                    .retrieve()
                    .bodyToMono(InventoryResponse.class)
                    .flatMap(inventory -> {
                        if (inventory.getQuantity() >= request.getQuantity()) {
                            return Mono.just(saveOrder(request, user));
                        }
                        return Mono.error(new InsufficientStockException());
                    });
            });
    }
}

// 2. ASYNCHRONOUS COMMUNICATION (Message Queue - Kafka/RabbitMQ)
@Service
public class AsyncOrderService {
    
    @Autowired
    private KafkaTemplate<String, OrderEvent> kafkaTemplate;
    
    public void createOrder(OrderRequest request) {
        // Save order locally
        Order order = saveOrderLocally(request);
        
        // Publish event (non-blocking)
        OrderEvent event = new OrderEvent(order.getId(), order.getProductId(), order.getQuantity());
        kafkaTemplate.send("order-created", event);
        
        // Don't wait for other services
        return order;
    }
}

// Inventory Service listening to events
@Component
public class OrderEventListener {
    
    @KafkaListener(topics = "order-created")
    public void handleOrderCreated(OrderEvent event) {
        // Update inventory asynchronously
        inventoryService.reserveStock(event.getProductId(), event.getQuantity());
        
        // Publish inventory updated event
        kafkaTemplate.send("inventory-updated", event);
    }
}

// 3. SERVICE DISCOVERY (Eureka)
@SpringBootApplication
@EnableEurekaClient
public class UserService {
    // Service registers with Eureka
}

// Client using discovery
@Service
public class DiscoveryClientService {
    
    @Autowired
    private DiscoveryClient discoveryClient;
    
    public void callService() {
        List<ServiceInstance> instances = discoveryClient.getInstances("user-service");
        ServiceInstance instance = instances.get(0);
        
        String url = "http://" + instance.getHost() + ":" + instance.getPort() + "/api/users";
        // Make REST call
    }
}

// 4. API GATEWAY (Spring Cloud Gateway)
@SpringBootApplication
public class ApiGateway {
    
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("user-service", r -> r
                .path("/api/users/**")
                .uri("lb://user-service"))  // Load balanced
            
            .route("order-service", r -> r
                .path("/api/orders/**")
                .uri("lb://order-service"))
            
            .route("inventory-service", r -> r
                .path("/api/inventory/**")
                .uri("lb://inventory-service"))
            .build();
    }
}
```

### How to handle Transactions in Microservices?

**Challenges:** Traditional ACID transactions don't work across services. Use distributed transaction patterns.

```java
// 1. TWO-PHASE COMMIT (2PC) - Not recommended for microservices
// Problem: Coordinator becomes bottleneck, services lock resources

// 2. SAGA PATTERN (Most common)
// Distributed transaction as sequence of local transactions with compensating actions

// Orchestration-based Saga
@Service
public class OrderSagaOrchestrator {
    
    @Autowired
    private OrderService orderService;
    
    @Autowired
    private PaymentServiceClient paymentClient;
    
    @Autowired
    private InventoryServiceClient inventoryClient;
    
    @Autowired
    private ShippingServiceClient shippingClient;
    
    @Transactional
    public void processOrder(OrderRequest request) {
        try {
            // Step 1: Create order
            Order order = orderService.createOrder(request);
            
            // Step 2: Reserve payment
            PaymentResponse payment = paymentClient.reservePayment(order);
            
            // Step 3: Reserve inventory
            InventoryResponse inventory = inventoryClient.reserveStock(order);
            
            // Step 4: Arrange shipping
            ShippingResponse shipping = shippingClient.arrangeShipping(order);
            
            // Step 5: Confirm all
            orderService.confirmOrder(order.getId());
            
        } catch (Exception e) {
            // Compensating transactions (rollback)
            compensateOrder(request);
        }
    }
    
    private void compensateOrder(OrderRequest request) {
        // Rollback in reverse order
        try { shippingClient.cancelShipping(request.getOrderId()); } catch(Exception ex) {}
        try { inventoryClient.releaseStock(request.getProductId(), request.getQuantity()); } catch(Exception ex) {}
        try { paymentClient.refundPayment(request.getOrderId()); } catch(Exception ex) {}
        try { orderService.cancelOrder(request.getOrderId()); } catch(Exception ex) {}
    }
}

// Choreography-based Saga (using events)
@Component
public class OrderEventSaga {
    
    // Order Service publishes ORDER_CREATED event
    @EventListener
    public void handleOrderCreated(OrderCreatedEvent event) {
        // Payment service listens and processes payment
        paymentService.processPayment(event);
        // Publishes PAYMENT_PROCESSED
    }
    
    // Inventory Service listens to PAYMENT_PROCESSED
    @EventListener
    public void handlePaymentProcessed(PaymentProcessedEvent event) {
        inventoryService.reserveStock(event);
        // Publishes INVENTORY_RESERVED
    }
    
    // Compensating transactions (rollback)
    @EventListener
    public void handlePaymentFailed(PaymentFailedEvent event) {
        orderService.cancelOrder(event.getOrderId());
        inventoryService.releaseStock(event);
    }
}

// 3. EVENTUAL CONSISTENCY with Outbox Pattern
@Entity
public class OutboxMessage {
    @Id
    @GeneratedValue
    private Long id;
    private String aggregateType;
    private String aggregateId;
    private String eventType;
    private String payload;
    private LocalDateTime createdAt;
    private boolean published;
}

@Service
@Transactional
public class OrderServiceWithOutbox {
    
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private OutboxRepository outboxRepository;
    
    public Order createOrder(OrderRequest request) {
        // Save order
        Order order = orderRepository.save(new Order(request));
        
        // Store event in outbox (same transaction as order)
        OutboxMessage message = new OutboxMessage();
        message.setEventType("ORDER_CREATED");
        message.setPayload(toJson(order));
        message.setCreatedAt(LocalDateTime.now());
        message.setPublished(false);
        outboxRepository.save(message);
        
        return order;  // Transaction commits, order and event saved together
    }
}

// Poller publishes outbox messages
@Component
public class OutboxPublisher {
    
    @Scheduled(fixedDelay = 5000)
    @Transactional
    public void publishMessages() {
        List<OutboxMessage> messages = outboxRepository.findByPublishedFalse();
        
        for (OutboxMessage message : messages) {
            try {
                kafkaTemplate.send("order-events", message.getPayload());
                message.setPublished(true);
                outboxRepository.save(message);
            } catch (Exception e) {
                // Log error, retry later
            }
        }
    }
}
```

### What is Saga Design Pattern?

**Saga Pattern** manages data consistency across microservices without distributed transactions. It's a sequence of local transactions where each transaction publishes events triggering the next transaction.

```java
// SAGA Pattern Implementation

// 1. Define Saga Steps
public class TravelBookingSaga {
    
    // Travel booking saga: Book Flight → Book Hotel → Book Car → Process Payment
    public void bookTravelPackage(TravelRequest request) {
        SagaBuilder saga = SagaBuilder.newSaga("travel-booking")
            .activity("book-flight", () -> bookFlight(request))
              .compensation(() -> cancelFlight(request))
            .activity("book-hotel", () -> bookHotel(request))
              .compensation(() -> cancelHotel(request))
            .activity("book-car", () -> bookCar(request))
              .compensation(() -> cancelCar(request))
            .activity("process-payment", () -> processPayment(request))
              .compensation(() -> refundPayment(request))
            .build();
        
        saga.execute();
    }
}

// 2. Orchestrator-based Saga (Central coordinator)
@Service
public class TravelSagaOrchestrator {
    
    @Autowired
    private SagaStateRepository sagaRepository;
    
    @Autowired
    private FlightServiceClient flightClient;
    
    @Autowired
    private HotelServiceClient hotelClient;
    
    @Autowired
    private CarServiceClient carClient;
    
    @Autowired
    private PaymentServiceClient paymentClient;
    
    public SagaState startSaga(TravelRequest request) {
        SagaState saga = new SagaState();
        saga.setSagaId(UUID.randomUUID().toString());
        saga.setCurrentStep("START");
        saga.setStatus(SagaStatus.STARTED);
        saga.setRequest(request);
        sagaRepository.save(saga);
        
        // Async execution
        executeNextStep(saga);
        return saga;
    }
    
    @Async
    public void executeNextStep(SagaState saga) {
        try {
            switch (saga.getCurrentStep()) {
                case "START":
                    bookFlight(saga);
                    break;
                case "FLIGHT_BOOKED":
                    bookHotel(saga);
                    break;
                case "HOTEL_BOOKED":
                    bookCar(saga);
                    break;
                case "CAR_BOOKED":
                    processPayment(saga);
                    break;
                case "PAYMENT_PROCESSED":
                    completeSaga(saga);
                    break;
            }
        } catch (Exception e) {
            compensateSaga(saga);
        }
    }
    
    private void bookFlight(SagaState saga) {
        FlightBookingResponse response = flightClient.bookFlight(saga.getRequest());
        saga.setFlightBookingId(response.getBookingId());
        saga.setCurrentStep("FLIGHT_BOOKED");
        sagaRepository.save(saga);
        executeNextStep(saga);
    }
    
    private void compensateSaga(SagaState saga) {
        // Execute compensations in reverse order
        switch (saga.getCurrentStep()) {
            case "PAYMENT_PROCESSED":
                paymentClient.refund(saga.getPaymentId());
            case "CAR_BOOKED":
                carClient.cancelBooking(saga.getCarBookingId());
            case "HOTEL_BOOKED":
                hotelClient.cancelBooking(saga.getHotelBookingId());
            case "FLIGHT_BOOKED":
                flightClient.cancelBooking(saga.getFlightBookingId());
        }
        saga.setStatus(SagaStatus.COMPENSATED);
        sagaRepository.save(saga);
    }
}

// 3. Choreography-based Saga (Event-driven)
@Configuration
public class TravelSagaChoreography {
    
    // Flight Service
    @Component
    public class FlightService {
        @EventListener
        @Async
        public void handleTravelRequest(TravelRequestEvent event) {
            try {
                Booking booking = bookFlight(event.getRequest());
                event.setFlightBookingId(booking.getId());
                event.setStep("FLIGHT_BOOKED");
                applicationEventPublisher.publishEvent(event);
            } catch (Exception e) {
                event.setFailed(true);
                applicationEventPublisher.publishEvent(new TravelFailedEvent(event));
            }
        }
        
        @EventListener
        public void handleTravelFailure(TravelFailedEvent event) {
            if (event.getTravelRequest().getFlightBookingId() != null) {
                cancelFlight(event.getTravelRequest().getFlightBookingId());
            }
        }
    }
    
    // Hotel Service
    @Component
    public class HotelService {
        @EventListener
        @Async
        public void handleFlightBooked(TravelRequestEvent event) {
            if (event.getStep().equals("FLIGHT_BOOKED") && !event.isFailed()) {
                try {
                    Booking booking = bookHotel(event.getRequest());
                    event.setHotelBookingId(booking.getId());
                    event.setStep("HOTEL_BOOKED");
                    applicationEventPublisher.publishEvent(event);
                } catch (Exception e) {
                    event.setFailed(true);
                    applicationEventPublisher.publishEvent(new TravelFailedEvent(event));
                }
            }
        }
        
        @EventListener
        public void handleTravelFailure(TravelFailedEvent event) {
            if (event.getTravelRequest().getHotelBookingId() != null) {
                cancelHotel(event.getTravelRequest().getHotelBookingId());
            }
        }
    }
}

// Saga State Management
@Entity
public class SagaState {
    @Id
    private String sagaId;
    private String currentStep;
    @Enumerated(EnumType.STRING)
    private SagaStatus status;
    private String flightBookingId;
    private String hotelBookingId;
    private String carBookingId;
    private String paymentId;
    @Embedded
    private TravelRequest request;
    
    // getters/setters
}

enum SagaStatus {
    STARTED, FLIGHT_BOOKED, HOTEL_BOOKED, CAR_BOOKED, 
    PAYMENT_PROCESSED, COMPLETED, COMPENSATED, FAILED
}
```

### Scenario-based: Logging & communication between services

```java
// Distributed Tracing with Sleuth + Zipkin

// 1. Dependencies
// implementation 'org.springframework.cloud:spring-cloud-starter-sleuth'
// implementation 'org.springframework.cloud:spring-cloud-sleuth-zipkin'

// 2. Configuration
spring:
  application:
    name: order-service
  zipkin:
    base-url: http://zipkin:9411
  sleuth:
    sampler:
      probability: 1.0  # Sample 100% of requests

// 3. Automatic tracing - all requests get traceId, spanId
// Logs automatically include trace information
// 2024-01-15 10:30:45.123 [order-service, traceId=abc123, spanId=def456] INFO - Processing order

// 4. Manual trace injection
@Service
public class OrderProcessor {
    
    @Autowired
    private Tracer tracer;
    
    public void processOrder(Order order) {
        Span newSpan = tracer.nextSpan().name("process-order").start();
        try (Tracer.SpanInScope ws = tracer.withSpanInScope(newSpan)) {
            // Business logic
            newSpan.tag("order.id", order.getId().toString());
            newSpan.tag("order.amount", order.getAmount().toString());
            
            // Add custom event
            newSpan.annotate("processing_started");
            
            orderService.process(order);
            
            newSpan.annotate("processing_completed");
        } finally {
            newSpan.end();
        }
    }
}

// 5. Propagating trace across HTTP calls
@Service
public class ServiceClient {
    
    @Autowired
    private RestTemplate restTemplate;
    
    @Autowired
    private Tracer tracer;
    
    public String callOtherService() {
        // Sleuth automatically adds trace headers to RestTemplate calls
        // Headers: X-B3-TraceId, X-B3-SpanId, X-B3-Sampled
        
        return restTemplate.getForObject("http://other-service/api/data", String.class);
    }
}

// 6. Logging configuration for distributed systems
@Configuration
public class LoggingConfig {
    
    @Bean
    public Filter loggingFilter() {
        return (request, response, chain) -> {
            HttpServletRequest httpRequest = (HttpServletRequest) request;
            String traceId = httpRequest.getHeader("X-B3-TraceId");
            String spanId = httpRequest.getHeader("X-B3-SpanId");
            
            MDC.put("traceId", traceId != null ? traceId : "no-trace");
            MDC.put("spanId", spanId != null ? spanId : "no-span");
            MDC.put("requestId", UUID.randomUUID().toString());
            MDC.put("userId", getCurrentUserId());
            
            long startTime = System.currentTimeMillis();
            
            try {
                log.info("Request: {} {} - Headers: {}", 
                    httpRequest.getMethod(), 
                    httpRequest.getRequestURI(),
                    getHeaders(httpRequest));
                
                chain.doFilter(request, response);
                
                long duration = System.currentTimeMillis() - startTime;
                log.info("Response: {} - Duration: {}ms", 
                    ((HttpServletResponse) response).getStatus(), 
                    duration);
            } finally {
                MDC.clear();
            }
        };
    }
}

// 7. Correlation ID across services
@Component
public class CorrelationIdInterceptor implements ClientHttpRequestInterceptor {
    
    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, 
                                        ClientHttpRequestExecution execution) throws IOException {
        String correlationId = MDC.get("correlationId");
        if (correlationId == null) {
            correlationId = UUID.randomUUID().toString();
        }
        request.getHeaders().add("X-Correlation-Id", correlationId);
        return execution.execute(request, body);
    }
}

// 8. Centralized logging with ELK Stack
// logback-spring.xml
<configuration>
    <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <destination>logstash:5000</destination>
        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <includeMdc>true</includeMdc>
        </encoder>
    </appender>
    
    <root level="INFO">
        <appender-ref ref="LOGSTASH"/>
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```

### How to migrate DB across cloud with minimal downtime?

**Strategy for zero-downtime database migration:**

```java
// 1. BLUE-GREEN DEPLOYMENT with Dual Writes
@Configuration
public class DatabaseMigrationStrategy {
    
    // Step 1: Set up replication from source to target
    // Step 2: Dual-write pattern
    @Component
    public class DualWriteService {
        
        @Autowired
        @Qualifier("sourceDataSource")
        private DataSource sourceDataSource;
        
        @Autowired
        @Qualifier("targetDataSource")
        private DataSource targetDataSource;
        
        @Transactional
        public void saveUser(User user) {
            // Write to both databases
            saveToSource(user);
            saveToTargetAsync(user);  // Async to avoid slowing main transaction
        }
        
        @Async
        public void saveToTargetAsync(User user) {
            try {
                targetRepository.save(user);
            } catch (Exception e) {
                // Log failure, queue for retry
                failedWritesQueue.add(user);
            }
        }
    }
    
    // Step 3: Backfill existing data
    @Component
    public class DataBackfillService {
        
        @Scheduled(fixedDelay = 60000)
        public void backfillData() {
            // Copy data in batches
            int batchSize = 1000;
            Long lastId = 0L;
            
            while (true) {
                List<User> users = sourceRepository.findByIdGreaterThan(lastId, PageRequest.of(0, batchSize));
                if (users.isEmpty()) break;
                
                targetRepository.saveAll(users);
                lastId = users.get(users.size() - 1).getId();
                
                log.info("Backfilled up to ID: {}", lastId);
            }
        }
    }
    
    // Step 4: Validation and comparison
    @Component
    public class DataValidator {
        
        public void validateData() {
            long sourceCount = sourceRepository.count();
            long targetCount = targetRepository.count();
            
            if (sourceCount != targetCount) {
                log.warn("Count mismatch: source={}, target={}", sourceCount, targetCount);
                // Trigger reconciliation
            }
            
            // Sample check
            List<User> sourceSample = sourceRepository.findAll(PageRequest.of(0, 1000)).getContent();
            List<User> targetSample = targetRepository.findAll(PageRequest.of(0, 1000)).getContent();
            
            // Compare checksums
            String sourceChecksum = calculateChecksum(sourceSample);
            String targetChecksum = calculateChecksum(targetSample);
            
            if (!sourceChecksum.equals(targetChecksum)) {
                log.warn("Data mismatch detected");
            }
        }
    }
    
    // Step 5: Switch over with feature flags
    @Component
    public class ReadRouter {
        
        @Value("${database.use.target:false}")
        private boolean useTargetDb;
        
        public User findUser(Long id) {
            if (useTargetDb) {
                return targetRepository.findById(id).orElse(null);
            } else {
                return sourceRepository.findById(id).orElse(null);
            }
        }
    }
}

// Complete migration workflow:
/*
Phase 1: Preparation
- Set up target database
- Configure replication
- Implement dual writes

Phase 2: Backfill
- Copy historical data
- Verify data consistency
- Handle conflicts

Phase 3: Catch up
- Ensure replication catches up
- Pause writes if needed
- Final validation

Phase 4: Switch over (minimal downtime - seconds)
1. Enable feature flag for reads from target
2. Switch writes to target
3. Disable dual writes
4. Decommission source

Phase 5: Cleanup
- Remove migration code
- Archive source database
- Update connection strings
*/
```

---

## 🔹 8. Security (JWT, Auth)

### What is Authentication vs Authorization?

| Aspect | Authentication | Authorization |
|--------|---------------|---------------|
| **Purpose** | Verify identity | Verify permissions |
| **Question** | "Who are you?" | "What can you do?" |
| **Timing** | First | After authentication |
| **Example** | Login with username/password | Check if user has ADMIN role |
| **HTTP Status** | 401 Unauthorized | 403 Forbidden |
| **Common methods** | Password, Biometric, OTP | Roles, Permissions, ACLs |

```java
@RestController
public class SecurityDemo {
    
    @PostMapping("/login")
    public ResponseEntity<?> authenticate(@RequestBody LoginRequest request) {
        // AUTHENTICATION - Verify identity
        User user = userService.findByUsername(request.getUsername());
        if (user == null || !passwordEncoder.matches(request.getPassword(), user.getPassword())) {
            return ResponseEntity.status(401).body("Invalid credentials");
        }
        
        String token = jwtUtil.generateToken(user);
        return ResponseEntity.ok(new AuthResponse(token));
    }
    
    @GetMapping("/admin/users")
    @PreAuthorize("hasRole('ADMIN')")  // AUTHORIZATION - Check permission
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    @GetMapping("/user/profile")
    @PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
    public UserProfile getProfile(@PathVariable Long userId) {
        return userService.getProfile(userId);
    }
}
```

### What is JWT?

**JWT (JSON Web Token)** is a compact, URL-safe token format for securely transmitting information between parties. It's digitally signed, so it can be verified and trusted.

**JWT Structure:** `xxxxx.yyyyy.zzzzz` (Header.Payload.Signature)

```java
// Complete JWT implementation

// 1. JWT Structure
public class JWTStructure {
    // Header: Algorithm and token type
    // {
    //   "alg": "HS256",
    //   "typ": "JWT"
    // }
    
    // Payload: Claims (data)
    // {
    //   "sub": "1234567890",
    //   "name": "John Doe",
    //   "iat": 1516239022,
    //   "exp": 1516242622,
    //   "roles": ["USER", "ADMIN"]
    // }
    
    // Signature: HMACSHA256(
    //   base64UrlEncode(header) + "." + base64UrlEncode(payload),
    //   secret
    // )
}

// 2. JWT Utility Class
@Component
public class JwtTokenUtil {
    
    @Value("${jwt.secret}")
    private String secret;
    
    @Value("${jwt.expiration}")
    private Long expiration;
    
    @Value("${jwt.refresh-expiration}")
    private Long refreshExpiration;
    
    // Generate access token
    public String generateAccessToken(UserDetails userDetails) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("authorities", userDetails.getAuthorities());
        claims.put("type", "access");
        
        return Jwts.builder()
            .setClaims(claims)
            .setSubject(userDetails.getUsername())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + expiration))
            .signWith(SignatureAlgorithm.HS256, secret)
            .compact();
    }
    
    // Generate refresh token (longer expiration)
    public String generateRefreshToken(String username) {
        return Jwts.builder()
            .setSubject(username)
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + refreshExpiration))
            .signWith(SignatureAlgorithm.HS256, secret)
            .compact();
    }
    
    // Extract username from token
    public String extractUsername(String token) {
        return extractAllClaims(token).getSubject();
    }
    
    // Extract expiration date
    public Date extractExpiration(String token) {
        return extractAllClaims(token).getExpiration();
    }
    
    // Extract specific claim
    public List<GrantedAuthority> extractAuthorities(String token) {
        Claims claims = extractAllClaims(token);
        List<String> authorities = claims.get("authorities", List.class);
        return authorities.stream()
            .map(SimpleGrantedAuthority::new)
            .collect(Collectors.toList());
    }
    
    // Validate token
    public Boolean validateToken(String token, UserDetails userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails.getUsername()) && !isTokenExpired(token));
    }
    
    // Check if token expired
    private Boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }
    
    // Extract all claims
    private Claims extractAllClaims(String token) {
        return Jwts.parser()
            .setSigningKey(secret)
            .parseClaimsJws(token)
            .getBody();
    }
}
```

### How does JWT work (flow)?

**Complete JWT Authentication Flow:**

```java
// Step 1: User logs in with credentials
@RestController
@RequestMapping("/auth")
public class AuthController {
    
    @Autowired
    private AuthenticationManager authenticationManager;
    
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    
    @Autowired
    private RefreshTokenService refreshTokenService;
    
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request) {
        // 1. Authenticate user
        Authentication authentication = authenticationManager.authenticate(
            new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
        );
        
        // 2. Get user details
        UserDetails userDetails = (UserDetails) authentication.getPrincipal();
        
        // 3. Generate tokens
        String accessToken = jwtTokenUtil.generateAccessToken(userDetails);
        String refreshToken = jwtTokenUtil.generateRefreshToken(userDetails.getUsername());
        
        // 4. Store refresh token (for revocation)
        refreshTokenService.saveRefreshToken(userDetails.getUsername(), refreshToken);
        
        // 5. Return tokens to client
        return ResponseEntity.ok(new JwtResponse(accessToken, refreshToken));
    }
    
    // Step 2: Refresh token endpoint
    @PostMapping("/refresh")
    public ResponseEntity<?> refreshToken(@RequestBody RefreshTokenRequest request) {
        String refreshToken = request.getRefreshToken();
        
        // Validate refresh token
        if (!jwtTokenUtil.validateRefreshToken(refreshToken)) {
            return ResponseEntity.status(401).body("Invalid refresh token");
        }
        
        String username = jwtTokenUtil.extractUsername(refreshToken);
        
        // Check if refresh token exists in store (not revoked)
        if (!refreshTokenService.isValidRefreshToken(username, refreshToken)) {
            return ResponseEntity.status(401).body("Refresh token revoked");
        }
        
        // Generate new access token
        UserDetails userDetails = userDetailsService.loadUserByUsername(username);
        String newAccessToken = jwtTokenUtil.generateAccessToken(userDetails);
        
        return ResponseEntity.ok(new JwtResponse(newAccessToken, refreshToken));
    }
    
    // Step 3: Logout (revoke tokens)
    @PostMapping("/logout")
    public ResponseEntity<?> logout(@RequestHeader("Authorization") String token) {
        String jwt = token.substring(7);
        String username = jwtTokenUtil.extractUsername(jwt);
        
        // Blacklist the token
        jwtTokenUtil.blacklistToken(jwt);
        
        // Remove refresh token
        refreshTokenService.revokeRefreshToken(username);
        
        return ResponseEntity.ok("Logged out successfully");
    }
}

// Step 4: JWT Authentication Filter
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    
    @Autowired
    private UserDetailsService userDetailsService;
    
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain chain) throws IOException, ServletException {
        
        final String requestTokenHeader = request.getHeader("Authorization");
        
        String username = null;
        String jwtToken = null;
        
        // Extract token from Bearer header
        if (requestTokenHeader != null && requestTokenHeader.startsWith("Bearer ")) {
            jwtToken = requestTokenHeader.substring(7);
            try {
                username = jwtTokenUtil.extractUsername(jwtToken);
            } catch (IllegalArgumentException e) {
                logger.error("Unable to get JWT Token");
            } catch (ExpiredJwtException e) {
                logger.error("JWT Token has expired");
                response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
                return;
            } catch (MalformedJwtException e) {
                logger.error("Invalid JWT Token");
                response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
                return;
            }
        }
        
        // Validate token and set authentication
        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);
            
            if (jwtTokenUtil.validateToken(jwtToken, userDetails)) {
                UsernamePasswordAuthenticationToken authentication = 
                    new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
                authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        }
        
        chain.doFilter(request, response);
    }
}

// JWT Flow Diagram:
/*
Client                    Server
  |                         |
  |--- POST /login -------->|
  |    (username/password)  |
  |                         |-- Verify credentials
  |                         |-- Generate JWT
  |<-- JWT Token -----------|
  |                         |
  |--- GET /api/data ------>|
  |    (Bearer: JWT)        |
  |                         |-- Validate JWT
  |                         |-- Check signature
  |                         |-- Check expiration
  |                         |-- Extract user
  |<-- Response ------------|
  |                         |
  |--- POST /refresh ------>|
  |    (refresh token)      |
  |                         |-- Validate refresh token
  |                         |-- Generate new access token
  |<-- New JWT -------------|
  |                         |
  |--- POST /logout ------->|
  |    (Bearer: JWT)        |
  |                         |-- Blacklist token
  |<-- Logout success ------|
*/
```

### How do you secure APIs using JWT?

**Complete API Security Implementation:**

```java
// 1. Security Configuration
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfiguration {
    
    @Autowired
    private JwtAuthenticationFilter jwtFilter;
    
    @Autowired
    private JwtAuthenticationEntryPoint unauthorizedHandler;
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .cors().and()
            .csrf().disable()
            .exceptionHandling()
                .authenticationEntryPoint(unauthorizedHandler)
                .and()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
            .authorizeRequests()
                // Public endpoints
                .antMatchers("/auth/**", "/public/**", "/actuator/health").permitAll()
                // Swagger UI
                .antMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()
                // Protected endpoints
                .antMatchers("/api/admin/**").hasRole("ADMIN")
                .antMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated();
        
        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }
    
    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}

// 2. Custom UserDetails Service
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
            .orElseThrow(() -> new UsernameNotFoundException("User not found: " + username));
        
        return org.springframework.security.core.userdetails.User
            .withUsername(user.getUsername())
            .password(user.getPassword())
            .authorities(user.getRoles().stream()
                .map(role -> new SimpleGrantedAuthority("ROLE_" + role.getName()))
                .collect(Collectors.toList()))
            .accountExpired(!user.isActive())
            .accountLocked(user.isLocked())
            .credentialsExpired(false)
            .disabled(!user.isEnabled())
            .build();
    }
}

// 3. Authentication Entry Point
@Component
public class JwtAuthenticationEntryPoint implements AuthenticationEntryPoint {
    
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response,
                         AuthenticationException authException) throws IOException {
        response.setContentType("application/json");
        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
        
        Map<String, Object> error = new HashMap<>();
        error.put("timestamp", LocalDateTime.now().toString());
        error.put("status", 401);
        error.put("error", "Unauthorized");
        error.put("message", authException.getMessage());
        error.put("path", request.getRequestURI());
        
        response.getWriter().write(new ObjectMapper().writeValueAsString(error));
    }
}

// 4. Protected Controller with method-level security
@RestController
@RequestMapping("/api")
public class SecureController {
    
    @GetMapping("/user/profile")
    @PreAuthorize("isAuthenticated()")
    public UserProfile getProfile(@AuthenticationPrincipal UserDetails userDetails) {
        return userService.getProfile(userDetails.getUsername());
    }
    
    @GetMapping("/admin/users")
    @PreAuthorize("hasRole('ADMIN')")
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    @GetMapping("/user/{userId}/orders")
    @PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
    public List<Order> getUserOrders(@PathVariable Long userId) {
        return orderService.findByUserId(userId);
    }
    
    @PostMapping("/manager/approve/{orderId}")
    @PreAuthorize("hasAnyRole('MANAGER', 'ADMIN') and @orderSecurity.isOrderInDepartment(#orderId, authentication.name)")
    public void approveOrder(@PathVariable Long orderId) {
        orderService.approve(orderId);
    }
}

// 5. Custom Security Expression
@Component("orderSecurity")
public class OrderSecurityService {
    
    @Autowired
    private OrderRepository orderRepository;
    
    public boolean isOrderInDepartment(Long orderId, String username) {
        Order order = orderRepository.findById(orderId).orElse(null);
        if (order == null) return false;
        
        User user = userRepository.findByUsername(username).orElse(null);
        if (user == null) return false;
        
        return order.getDepartment().equals(user.getDepartment());
    }
}

// 6. Token Blacklist (for logout)
@Component
public class TokenBlacklistService {
    
    private Set<String> blacklistedTokens = ConcurrentHashMap.newKeySet();
    
    @Value("${jwt.expiration}")
    private Long expiration;
    
    public void blacklistToken(String token) {
        blacklistedTokens.add(token);
        
        // Schedule removal after token expiration
        ScheduledExecutorService scheduler = Executors.newSingleThreadScheduledExecutor();
        scheduler.schedule(() -> {
            blacklistedTokens.remove(token);
        }, expiration, TimeUnit.MILLISECONDS);
    }
    
    public boolean isBlacklisted(String token) {
        return blacklistedTokens.contains(token);
    }
}

// 7. Enhanced JWT Filter with blacklist check
@Component
public class EnhancedJwtFilter extends OncePerRequestFilter {
    
    @Autowired
    private TokenBlacklistService blacklistService;
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                    HttpServletResponse response, 
                                    FilterChain chain) throws IOException, ServletException {
        
        String token = extractToken(request);
        
        if (token != null && blacklistService.isBlacklisted(token)) {
            response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            response.getWriter().write("Token has been revoked");
            return;
        }
        
        // Continue with normal JWT validation...
        chain.doFilter(request, response);
    }
}
```

---

## 🔹 9. Database, SQL & JPA

### What is SQL vs NoSQL?

| Feature | SQL (Relational) | NoSQL (Non-relational) |
|---------|-----------------|----------------------|
| **Data Model** | Tables with rows and columns | Document, Key-Value, Graph, Column |
| **Schema** | Fixed, predefined schema | Dynamic, schema-less |
| **ACID Compliance** | Full ACID support | Usually BASE (Basically Available, Soft state, Eventual consistency) |
| **Scaling** | Vertical scaling (scale up) | Horizontal scaling (scale out) |
| **Joins** | Complex joins supported | Limited or no joins |
| **Examples** | MySQL, PostgreSQL, Oracle | MongoDB, Cassandra, Redis, Neo4j |
| **Best for** | Complex queries, transactions, reporting | High volume, rapid iteration, distributed systems |

```java
// SQL Example - Structured data
@Entity
@Table(name = "orders")
public class Order {
    @Id
    private Long id;
    private Long userId;
    private LocalDateTime orderDate;
    private BigDecimal totalAmount;
    
    @OneToMany(mappedBy = "order")
    private List<OrderItem> items;
}

// SQL Query
SELECT o.id, u.name, SUM(oi.quantity * oi.price) as total
FROM orders o
JOIN users u ON o.user_id = u.id
JOIN order_items oi ON o.id = oi.order_id
WHERE o.order_date BETWEEN '2024-01-01' AND '2024-12-31'
GROUP BY o.id, u.name
HAVING total > 1000;

// NoSQL (MongoDB) Example - Embedded document
@Document(collection = "orders")
public class OrderDocument {
    @Id
    private String id;
    private Long userId;
    private LocalDateTime orderDate;
    
    // Embedded documents instead of joins
    private UserInfo user;
    private List<OrderItem> items;
    private Address shippingAddress;
    
    @Data
    public static class UserInfo {
        private String name;
        private String email;
    }
}

// MongoDB Query
db.orders.aggregate([
    { $match: { orderDate: { $gte: ISODate("2024-01-01") } } },
    { $unwind: "$items" },
    { $group: { 
        _id: "$userId", 
        total: { $sum: { $multiply: ["$items.quantity", "$items.price"] } }
    }},
    { $match: { total: { $gt: 1000 } } }
])
```

### What is JPA?

**JPA (Java Persistence API)** is a specification for object-relational mapping (ORM) in Java. It provides a standard way to map Java objects to database tables.

```java
// JPA Basics

// 1. Entity Mapping
@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "product_name", nullable = false, length = 100)
    private String name;
    
    @Column(unique = true)
    private String sku;
    
    private BigDecimal price;
    
    @Enumerated(EnumType.STRING)
    private ProductStatus status;
    
    @Lob
    private String description;
    
    @Temporal(TemporalType.TIMESTAMP)
    private Date createdAt;
    
    // Relationships
    @ManyToOne
    @JoinColumn(name = "category_id")
    private Category category;
    
    @OneToMany(mappedBy = "product", cascade = CascadeType.ALL)
    private List<Review> reviews;
}

// 2. Repository Pattern
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    
    // Query methods (derived from method name)
    List<Product> findByPriceBetween(BigDecimal min, BigDecimal max);
    Optional<Product> findBySku(String sku);
    List<Product> findByNameContainingIgnoreCase(String keyword);
    
    // JPQL Query
    @Query("SELECT p FROM Product p WHERE p.price > :minPrice AND p.status = :status")
    List<Product> findExpensiveProducts(@Param("minPrice") BigDecimal minPrice, 
                                        @Param("status") ProductStatus status);
    
    // Native SQL Query
    @Query(value = "SELECT * FROM products WHERE price > ?1", nativeQuery = true)
    List<Product> findProductsByPriceNative(BigDecimal price);
    
    // Modifying query
    @Modifying
    @Transactional
    @Query("UPDATE Product p SET p.price = p.price * :multiplier WHERE p.category.id = :categoryId")
    int updatePricesByCategory(@Param("categoryId") Long categoryId, 
                               @Param("multiplier") BigDecimal multiplier);
}

// 3. Using EntityManager directly
@Repository
public class CustomProductRepository {
    
    @PersistenceContext
    private EntityManager entityManager;
    
    public List<Product> findProductsWithDynamicFilters(Map<String, Object> filters) {
        CriteriaBuilder cb = entityManager.getCriteriaBuilder();
        CriteriaQuery<Product> query = cb.createQuery(Product.class);
        Root<Product> root = query.from(Product.class);
        
        List<Predicate> predicates = new ArrayList<>();
        
        if (filters.containsKey("minPrice")) {
            predicates.add(cb.greaterThanOrEqualTo(root.get("price"), 
                (BigDecimal) filters.get("minPrice")));
        }
        
        if (filters.containsKey("categoryId")) {
            predicates.add(cb.equal(root.get("category").get("id"), 
                filters.get("categoryId")));
        }
        
        query.where(predicates.toArray(new Predicate[0]));
        
        return entityManager.createQuery(query).getResultList();
    }
}
```

### Difference between JPA and Hibernate

| Feature | JPA | Hibernate |
|---------|-----|-----------|
| **Type** | Specification (interface) | Implementation |
| **Provider** | Multiple (Hibernate, EclipseLink, OpenJPA) | Single (Hibernate ORM) |
| **Portability** | High (can switch implementations) | Low (vendor-specific features) |
| **Features** | Standard features only | Extended features (caching, custom types) |
| **Caching** | Basic (Level 1) | Level 1 and Level 2 caching |
| **Mapping flexibility** | Standard annotations | Hibernate-specific annotations |
| **Performance** | Standard performance | Optimized with features like batch fetching |

```java
// JPA Standard (Portable)
@Entity
public class User {
    @Id
    private Long id;
    
    @Column(name = "full_name")
    private String name;
    
    @OneToMany(cascade = CascadeType.ALL)
    private List<Order> orders;
}

// Hibernate-specific features (Non-portable)
@Entity
@org.hibernate.annotations.Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
@org.hibernate.annotations.BatchSize(size = 10)
public class Product {
    @Id
    private Long id;
    
    @Column(columnDefinition = "TEXT")
    private String description;
    
    @org.hibernate.annotations.Formula("(SELECT AVG(r.rating) FROM reviews r WHERE r.product_id = id)")
    private Double averageRating;
    
    @org.hibernate.annotations.Type(type = "json")
    private Map<String, Object> metadata;
}
```

### Important Hibernate Annotations

```java
@Entity
@Table(name = "employees", 
       indexes = {@Index(name = "idx_email", columnList = "email")},
       uniqueConstraints = {@UniqueConstraint(columnNames = {"email", "department"})})
@org.hibernate.annotations.Cache(usage = CacheConcurrencyStrategy.READ_WRITE, region = "employeeCache")
@org.hibernate.annotations.DynamicInsert  // Only insert non-null columns
@org.hibernate.annotations.DynamicUpdate  // Only update changed columns
@org.hibernate.annotations.SelectBeforeUpdate  // Check if update needed
public class Employee {
    
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "emp_seq")
    @SequenceGenerator(name = "emp_seq", sequenceName = "employee_seq", allocationSize = 1)
    private Long id;
    
    @NaturalId  // Business key
    @Column(nullable = false, unique = true)
    private String employeeNumber;
    
    @Column(nullable = false)
    private String firstName;
    
    private String lastName;
    
    @Formula("concat(first_name, ' ', last_name)")
    private String fullName;
    
    @CreationTimestamp
    private LocalDateTime createdAt;
    
    @UpdateTimestamp
    private LocalDateTime updatedAt;
    
    @Version  // Optimistic locking
    private Long version;
    
    @Lob
    private byte[] photo;
    
    @Type(type = "json")
    @Column(columnDefinition = "jsonb")
    private Map<String, Object> attributes;
    
    // Relationships
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id")
    @Fetch(FetchMode.JOIN)  // Override fetch strategy
    @NotFound(action = NotFoundAction.IGNORE)
    private Department department;
    
    @OneToMany(mappedBy = "employee", cascade = CascadeType.ALL, orphanRemoval = true)
    @BatchSize(size = 20)
    @OrderBy("startDate DESC")
    private List<ProjectAssignment> projects;
    
    @ElementCollection
    @CollectionTable(name = "employee_phones", joinColumns = @JoinColumn(name = "employee_id"))
    @Column(name = "phone_number")
    private Set<String> phoneNumbers;
    
    @Enumerated(EnumType.STRING)
    private EmployeeStatus status;
    
    @Where(clause = "active = true")
    @OneToMany(mappedBy = "employee")
    private List<Address> activeAddresses;
    
    @Filter(name = "dateFilter", condition = "created_date >= :startDate")
    private List<AuditLog> auditLogs;
}
```

### What is a Database Index?

**Database Index** is a data structure that improves the speed of data retrieval operations on a database table.

```sql
-- Types of Indexes

-- 1. Single-column Index
CREATE INDEX idx_employee_email ON employees(email);

-- 2. Composite Index (multiple columns)
CREATE INDEX idx_employee_dept_salary ON employees(department_id, salary);

-- 3. Unique Index
CREATE UNIQUE INDEX idx_unique_employee_number ON employees(employee_number);

-- 4. Full-text Index
CREATE FULLTEXT INDEX idx_product_description ON products(description);

-- 5. Partial Index (only for specific rows)
CREATE INDEX idx_active_employees ON employees(last_name) WHERE status = 'ACTIVE';

-- 6. Expression Index (function-based)
CREATE INDEX idx_lower_email ON employees(LOWER(email));

-- JPA Index Definition
@Entity
@Table(indexes = {
    @Index(name = "idx_email", columnList = "email"),
    @Index(name = "idx_dept_salary", columnList = "department_id, salary")
})
public class Employee {
    // fields
}

-- When to use indexes:
-- - Columns used in WHERE clauses frequently
-- - Foreign key columns
-- - Columns used for JOINs
-- - Columns used for ORDER BY or GROUP BY
-- - Columns with high selectivity (many unique values)

-- When NOT to use indexes:
-- - Small tables
-- - Columns frequently updated (INSERT, UPDATE, DELETE)
-- - Columns with low selectivity (e.g., boolean columns)
-- - Tables with frequent bulk inserts

-- Check index usage (PostgreSQL)
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read, idx_tup_fetch
FROM pg_stat_user_indexes
WHERE tablename = 'employees';

-- Analyze query performance
EXPLAIN ANALYZE
SELECT * FROM employees WHERE email = 'john@example.com';
```

### Types of SQL Joins

```sql
-- Sample tables
-- employees: id, name, department_id
-- departments: id, name

-- 1. INNER JOIN (returns matching records from both tables)
SELECT e.name, d.name as department
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;
-- Result: Only employees with valid department_id

-- 2. LEFT JOIN (all records from left, matching from right)
SELECT e.name, d.name as department
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;
-- Result: All employees, department name NULL if no match

-- 3. RIGHT JOIN (all records from right, matching from left)
SELECT e.name, d.name as department
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;
-- Result: All departments, employee NULL if no employees

-- 4. FULL OUTER JOIN (all records from both tables)
SELECT e.name, d.name as department
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.id;
-- Result: All employees AND all departments

-- 5. CROSS JOIN (Cartesian product)
SELECT e.name, d.name
FROM employees e
CROSS JOIN departments d;
-- Result: Every employee paired with every department

-- 6. SELF JOIN (join table to itself)
SELECT e1.name as employee, e2.name as manager
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.id;

-- JPA Join Examples
@Entity
public class Employee {
    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
    
    @ManyToOne
    @JoinColumn(name = "manager_id")
    private Employee manager;
}

// JPQL Joins
@Query("SELECT e.name, d.name FROM Employee e JOIN e.department d")
List<Object[]> findEmployeeWithDepartment();

@Query("SELECT e.name, d.name FROM Employee e LEFT JOIN e.department d")
List<Object[]> findAllEmployeesWithDepartment();

@Query("SELECT e1.name, e2.name FROM Employee e1 LEFT JOIN e1.manager e2")
List<Object[]> findEmployeeWithManager();
```

### Which join to use when one department has multiple employees?

```sql
-- Scenario: One department has multiple employees
-- Departments table (one side)
-- Employees table (many side)

-- To get department with all its employees (1-to-many relationship)
-- Use LEFT JOIN to include departments with no employees

SELECT d.id, d.name as department_name, 
       e.id as employee_id, e.name as employee_name
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id
ORDER BY d.id, e.id;

-- Result:
-- dept_id | department_name | employee_id | employee_name
-- 1       | IT              | 101         | John
-- 1       | IT              | 102         | Jane
-- 1       | IT              | 103         | Bob
-- 2       | HR              | 104         | Alice
-- 3       | Finance         | NULL        | NULL

-- To aggregate employees per department
SELECT d.id, d.name, 
       COUNT(e.id) as employee_count,
       STRING_AGG(e.name, ', ') as employee_names
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id
GROUP BY d.id, d.name;

-- JPA equivalent
@Entity
public class Department {
    @Id
    private Long id;
    private String name;
    
    @OneToMany(mappedBy = "department")
    private List<Employee> employees;
}

// Query all departments with employees
List<Department> departments = departmentRepository.findAll();
for (Department dept : departments) {
    System.out.println(dept.getName() + ": " + dept.getEmployees().size());
}
```

### Write SQL for:

**83.1 3rd highest salary**

```sql
-- Method 1: Using LIMIT and OFFSET
SELECT DISTINCT salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 1 OFFSET 2;

-- Method 2: Using subquery
SELECT MAX(salary) 
FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees 
                WHERE salary < (SELECT MAX(salary) FROM employees));

-- Method 3: Using DENSE_RANK() (handles ties properly)
WITH ranked_salaries AS (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rank
    FROM employees
)
SELECT DISTINCT salary 
FROM ranked_salaries 
WHERE rank = 3;

-- Method 4: Using LIMIT with subquery (MySQL)
SELECT salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 2, 1;

-- Method 5: Using ROW_NUMBER() (doesn't handle ties)
WITH numbered AS (
    SELECT salary, ROW_NUMBER() OVER (ORDER BY salary DESC) as rn
    FROM employees
)
SELECT salary FROM numbered WHERE rn = 3;
```

**83.2 4th highest salary**

```sql
-- Same approaches, just change offset/rank

-- Using LIMIT OFFSET
SELECT DISTINCT salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 1 OFFSET 3;

-- Using DENSE_RANK
WITH ranked_salaries AS (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rank
    FROM employees
)
SELECT DISTINCT salary 
FROM ranked_salaries 
WHERE rank = 4;

-- Using subquery for NTH highest (generic)
SELECT salary FROM employees e1
WHERE N-1 = (SELECT COUNT(DISTINCT salary) FROM employees e2 
             WHERE e2.salary > e1.salary);
-- For 4th highest, N=4
SELECT salary FROM employees e1
WHERE 3 = (SELECT COUNT(DISTINCT salary) FROM employees e2 
           WHERE e2.salary > e1.salary);
```

**83.3 Employees in IT department (JOIN)**

```sql
-- Method 1: Using JOIN
SELECT e.id, e.name, e.salary, d.name as department
FROM employees e
INNER JOIN departments d ON e.department_id = d.id
WHERE d.name = 'IT';

-- Method 2: Using subquery
SELECT * FROM employees
WHERE department_id = (SELECT id FROM departments WHERE name = 'IT');

-- Method 3: Using EXISTS
SELECT * FROM employees e
WHERE EXISTS (SELECT 1 FROM departments d 
              WHERE d.id = e.department_id AND d.name = 'IT');

-- Method 4: With additional filters
SELECT e.id, e.name, e.salary, e.hire_date
FROM employees e
JOIN departments d ON e.department_id = d.id
WHERE d.name = 'IT' 
  AND e.salary > 50000
  AND e.hire_date >= '2023-01-01'
ORDER BY e.salary DESC;

-- JPA equivalent
@Query("SELECT e FROM Employee e WHERE e.department.name = 'IT'")
List<Employee> findITEmployees();

// Or using derived query
List<Employee> findByDepartmentName(String departmentName);
```

**83.4 Outer Join example**

```sql
-- Setup
CREATE TABLE customers (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

-- Sample data
INSERT INTO customers VALUES (1, 'John'), (2, 'Jane'), (3, 'Bob');
INSERT INTO orders VALUES (1, 1, 100), (2, 1, 200), (3, 2, 150);

-- LEFT OUTER JOIN: All customers, orders if exist
SELECT c.name, COALESCE(SUM(o.amount), 0) as total_orders
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name;

-- Result:
-- John | 300
-- Jane | 150
-- Bob  | 0

-- RIGHT OUTER JOIN: All orders, customer info
SELECT o.id, o.amount, c.name
FROM orders o
RIGHT JOIN customers c ON o.customer_id = c.id;

-- FULL OUTER JOIN: All customers and all orders
SELECT c.name, o.id, o.amount
FROM customers c
FULL OUTER JOIN orders o ON c.id = o.customer_id;

-- Practical use case: Find customers with no orders
SELECT c.name
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.id IS NULL;

-- JPA equivalent for OUTER JOIN
@Query("SELECT c.name, COALESCE(SUM(o.amount), 0) FROM Customer c LEFT JOIN c.orders o GROUP BY c.id")
List<Object[]> getCustomerOrderTotals();
```

### How to map different JSON fields to entity fields? (→ @JsonProperty)

```java
// Scenario: JSON from API has different field names than entity

// JSON input:
// {
//   "user_id": 123,
//   "full_name": "John Doe",
//   "emp_email": "john@example.com",
//   "address": {
//     "street": "123 Main St",
//     "city": "New York"
//   }
// }

// Entity with different field names
@Entity
@Table(name = "employees")
public class Employee {
    
    @Id
    @JsonProperty("user_id")  // Maps JSON "user_id" to this field
    private Long id;
    
    @JsonProperty("full_name")  // Maps JSON "full_name" to name
    @Column(name = "employee_name")
    private String name;
    
    @JsonProperty("emp_email")
    @Column(name = "email_address")
    private String email;
    
    @JsonProperty("address")
    @Embedded
    private Address address;
    
    // If JSON has nested objects
    @JsonIgnore  // Ignore during serialization/deserialization
    private String internalData;
    
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createdAt;
    
    // Custom deserialization
    @JsonDeserialize(using = CustomDateDeserializer.class)
    private LocalDate birthDate;
    
    // Getter with custom JSON property name
    @JsonProperty("full_info")
    public String getFullInfo() {
        return name + " (" + email + ")";
    }
}

@Embeddable
public class Address {
    private String street;
    private String city;
}

// Custom deserializer
public class CustomDateDeserializer extends JsonDeserializer<LocalDate> {
    @Override
    public LocalDate deserialize(JsonParser parser, DeserializationContext context) 
            throws IOException {
        String date = parser.getText();
        // Handle multiple formats
        try {
            return LocalDate.parse(date, DateTimeFormatter.ISO_LOCAL_DATE);
        } catch (DateTimeParseException e) {
            return LocalDate.parse(date, DateTimeFormatter.ofPattern("dd/MM/yyyy"));
        }
    }
}

// Controller using @JsonProperty
@RestController
public class EmployeeController {
    
    @PostMapping("/employees")
    public Employee createEmployee(@RequestBody Employee employee) {
        // JSON automatically mapped despite different field names
        return employeeService.save(employee);
    }
    
    @GetMapping("/employees/{id}")
    public Employee getEmployee(@PathVariable Long id) {
        Employee emp = employeeService.findById(id);
        // When returned as JSON, uses @JsonProperty annotations
        return emp;
    }
}

// Using DTO with @JsonProperty for API contracts
public class EmployeeDTO {
    
    @JsonProperty("user_id")
    private Long id;
    
    @JsonProperty("full_name")
    private String fullName;
    
    @JsonProperty("email_address")
    private String email;
    
    @JsonIgnore
    private String sensitiveData;
    
    @JsonInclude(JsonInclude.Include.NON_NULL)
    private String optionalField;
    
    // Constructor mapping from entity
    public static EmployeeDTO fromEntity(Employee employee) {
        EmployeeDTO dto = new EmployeeDTO();
        dto.setId(employee.getId());
        dto.setFullName(employee.getName());
        dto.setEmail(employee.getEmail());
        return dto;
    }
}
```

---

## 🔹 10. Kafka & Distributed Systems

### What is Kafka?

**Apache Kafka** is a distributed event streaming platform for high-throughput, fault-tolerant, real-time data pipelines.

**Key Concepts:**

```java
// Core Kafka Components
/*
1. Producer: Publishes messages to topics
2. Consumer: Subscribes to topics and processes messages
3. Topic: Category/feed name to which records are published
4. Partition: Topic is divided into partitions for parallelism
5. Broker: Kafka server that stores data
6. Cluster: Group of brokers
7. Zookeeper/KRaft: Manages cluster metadata
*/

// Spring Boot Kafka Configuration
@Configuration
public class KafkaConfig {
    
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;
    
    // Producer configuration
    @Bean
    public ProducerFactory<String, Object> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        config.put(ProducerConfig.ACKS_CONFIG, "all");  // Strongest durability
        config.put(ProducerConfig.RETRIES_CONFIG, 3);
        config.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, true);
        return new DefaultKafkaProducerFactory<>(config);
    }
    
    @Bean
    public KafkaTemplate<String, Object> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
    
    // Consumer configuration
    @Bean
    public ConsumerFactory<String, Object> consumerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        config.put(ConsumerConfig.GROUP_ID_CONFIG, "my-group");
        config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
        config.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        config.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
        config.put(JsonDeserializer.TRUSTED_PACKAGES, "*");
        return new DefaultKafkaConsumerFactory<>(config);
    }
    
    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, Object> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, Object> factory = 
            new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        factory.setConcurrency(3);  // Number of consumer threads
        return factory;
    }
}
```

### What is a Topic in Kafka?

**Topic** is a logical category or feed name to which records are published. It's similar to a table in a database.

```java
// Working with Topics

// 1. Creating a topic
@Component
public class TopicCreator {
    
    @Autowired
    private KafkaAdmin kafkaAdmin;
    
    public void createTopic(String topicName, int partitions, short replicationFactor) {
        NewTopic topic = new NewTopic(topicName, partitions, replicationFactor);
        kafkaAdmin.createOrModifyTopic(topic);
    }
    
    // Topic configuration
    public NewTopic createConfiguredTopic() {
        return TopicBuilder.name("order-events")
            .partitions(10)
            .replicas(3)
            .config(TopicConfig.RETENTION_MS_CONFIG, "604800000")  // 7 days
            .config(TopicConfig.COMPRESSION_TYPE_CONFIG, "snappy")
            .config(TopicConfig.MAX_MESSAGE_BYTES_CONFIG, "10485760")  // 10 MB
            .build();
    }
}

// 2. Producer sending to topic
@Service
public class OrderEventProducer {
    
    @Autowired
    private KafkaTemplate<String, OrderEvent> kafkaTemplate;
    
    public void sendOrderEvent(OrderEvent event) {
        // Send with key for partitioning
        CompletableFuture<SendResult<String, OrderEvent>> future = 
            kafkaTemplate.send("order-events", event.getOrderId(), event);
        
        future.whenComplete((result, ex) -> {
            if (ex == null) {
                System.out.println("Sent to partition: " + result.getRecordMetadata().partition());
                System.out.println("Offset: " + result.getRecordMetadata().offset());
            } else {
                System.err.println("Failed to send: " + ex.getMessage());
            }
        });
    }
    
    // Synchronous send
    public void sendSync(OrderEvent event) throws Exception {
        SendResult<String, OrderEvent> result = 
            kafkaTemplate.send("order-events", event).get();
        System.out.println("Sent successfully");
    }
    
    // Send with headers
    public void sendWithHeaders(OrderEvent event) {
        ProducerRecord<String, OrderEvent> record = new ProducerRecord<>(
            "order-events", 
            event.getOrderId(), 
            event
        );
        record.headers().add("source", "order-service".getBytes());
        record.headers().add("timestamp", String.valueOf(System.currentTimeMillis()).getBytes());
        
        kafkaTemplate.send(record);
    }
}

// 3. Consumer listening to topic
@Component
public class OrderEventConsumer {
    
    @KafkaListener(topics = "order-events", groupId = "order-processor")
    public void consume(OrderEvent event, 
                        @Header(KafkaHeaders.RECEIVED_PARTITION) int partition,
                        @Header(KafkaHeaders.OFFSET) long offset) {
        System.out.printf("Consumed from partition %d, offset %d: %s%n", 
                         partition, offset, event);
        
        // Process event
        processOrder(event);
    }
    
    // Batch consumption
    @KafkaListener(topics = "order-events", groupId = "batch-processor")
    public void consumeBatch(List<OrderEvent> events) {
        System.out.println("Received batch of " + events.size() + " messages");
        events.forEach(this::processOrder);
    }
    
    // Manual offset commit
    @KafkaListener(topics = "order-events", groupId = "manual-commit")
    public void consumeWithManualCommit(OrderEvent event, Acknowledgment ack) {
        try {
            processOrder(event);
            ack.acknowledge();  // Commit offset after successful processing
        } catch (Exception e) {
            // Don't commit, message will be replayed
            System.err.println("Processing failed: " + e.getMessage());
        }
    }
}
```

### What is a Partition in Kafka?

**Partition** is a unit of parallelism in Kafka. Each topic is divided into partitions, and messages within a partition are ordered.

```java
// Partition concepts explained

/*
Key Partitioning Concepts:
1. Messages with same key go to same partition (guarantees order)
2. Each partition is an ordered, immutable sequence of records
3. Partitions are distributed across brokers for scalability
4. Consumer groups allow parallel processing across partitions
*/

// 1. Custom Partitioner
public class OrderPartitioner implements Partitioner {
    
    @Override
    public int partition(String topic, Object key, byte[] keyBytes, 
                        Object value, byte[] valueBytes, Cluster cluster) {
        // Custom logic to decide partition
        OrderEvent event = (OrderEvent) value;
        
        // Route based on order type
        if ("PREMIUM".equals(event.getOrderType())) {
            return 0;  // Premium orders to partition 0 (higher priority)
        } else if ("BULK".equals(event.getOrderType())) {
            return 1;  // Bulk orders to partition 1
        } else {
            // Default: hash based on order ID
            return Math.abs(event.getOrderId().hashCode()) % cluster.partitionCountForTopic(topic);
        }
    }
    
    @Override
    public void close() {}
    
    @Override
    public void configure(Map<String, ?> configs) {}
}

// 2. Using custom partitioner
@Bean
public ProducerFactory<String, OrderEvent> producerFactory() {
    Map<String, Object> config = new HashMap<>();
    config.put(ProducerConfig.PARTITIONER_CLASS_CONFIG, OrderPartitioner.class);
    // ... other configs
    return new DefaultKafkaProducerFactory<>(config);
}

// 3. Consumer group with specific partitions
@Component
public class PartitionSpecificConsumer {
    
    // Listen to specific partitions
    @KafkaListener(topicPartitions = {
        @TopicPartition(topic = "order-events", partitions = {"0", "1"}),
        @TopicPartition(topic = "payment-events", partitions = {"0", "2"})
    })
    public void consumeFromSpecificPartitions(ConsumerRecord<String, OrderEvent> record) {
        System.out.println("Partition: " + record.partition());
        System.out.println("Offset: " + record.offset());
        System.out.println("Value: " + record.value());
    }
    
    // Assign all partitions from specific consumer group
    @KafkaListener(topics = "order-events", groupId = "processor-group")
    public void consume(OrderEvent event) {
        // Kafka automatically assigns partitions across consumers in same group
        // With 3 consumers and 6 partitions: each gets 2 partitions
        processEvent(event);
    }
}

// 4. Understanding partition rebalancing
@Component
public class RebalanceListener {
    
    @KafkaListener(topics = "order-events", groupId = "balanced-group")
    public void consume(ConsumerRecord<String, OrderEvent> record, 
                        Consumer<String, OrderEvent> consumer) {
        
        // Add rebalance listener
        consumer.subscribe(Collections.singletonList("order-events"), 
            new ConsumerRebalanceListener() {
                @Override
                public void onPartitionsRevoked(Collection<TopicPartition> partitions) {
                    System.out.println("Partitions revoked: " + partitions);
                    // Commit offsets before rebalance
                    consumer.commitSync();
                }
                
                @Override
                public void onPartitionsAssigned(Collection<TopicPartition> partitions) {
                    System.out.println("Partitions assigned: " + partitions);
                    // Seek to last committed offset
                    partitions.forEach(partition -> 
                        consumer.seek(partition, consumer.position(partition)));
                }
            });
        
        processRecord(record);
    }
}

// Partition performance considerations
/*
- More partitions = more parallelism, but:
  - More open file handles
  - Longer leader failover time
  - Increased latency for small messages
  
- Recommended:
  - 1 partition per consumer in group
  - Calculate: partitions = max(consumers, expected throughput / broker capacity)
  - For 10 consumers, use 10+ partitions
  - Avoid over-partitioning (>1000 partitions per cluster)
*/
```

---

## 🔹 11. Coding Questions (Very Important)

### Arrays & Strings

**1. Reverse a String**

```java
public class ReverseString {
    
    // Method 1: Using StringBuilder (simplest)
    public static String reverseWithBuilder(String str) {
        return new StringBuilder(str).reverse().toString();
    }
    
    // Method 2: Using char array (manual)
    public static String reverseManual(String str) {
        char[] chars = str.toCharArray();
        int left = 0;
        int right = chars.length - 1;
        
        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left++;
            right--;
        }
        
        return new String(chars);
    }
    
    // Method 3: Using recursion
    public static String reverseRecursive(String str) {
        if (str.isEmpty()) {
            return str;
        }
        return reverseRecursive(str.substring(1)) + str.charAt(0);
    }
    
    // Method 4: Using Java 8 streams
    public static String reverseWithStreams(String str) {
        return str.chars()
            .mapToObj(c -> (char) c)
            .reduce("", (s, c) -> c + s, (s1, s2) -> s2 + s1);
    }
    
    // Method 5: Reverse words in a sentence
    public static String reverseWords(String sentence) {
        String[] words = sentence.split(" ");
        StringBuilder result = new StringBuilder();
        
        for (int i = words.length - 1; i >= 0; i--) {
            result.append(words[i]);
            if (i > 0) result.append(" ");
        }
        
        return result.toString();
    }
    
    public static void main(String[] args) {
        String str = "Hello World";
        System.out.println(reverseWithBuilder(str));  // dlroW olleH
        System.out.println(reverseManual(str));      // dlroW olleH
        System.out.println(reverseRecursive(str));   // dlroW olleH
        System.out.println(reverseWords(str));       // World Hello
    }
}
```

**2. Check Palindrome String**

```java
public class PalindromeCheck {
    
    // Method 1: Two pointers (most efficient)
    public static boolean isPalindrome(String str) {
        if (str == null || str.isEmpty()) return true;
        
        int left = 0;
        int right = str.length() - 1;
        
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
    
    // Method 2: Using StringBuilder
    public static boolean isPalindromeBuilder(String str) {
        String reversed = new StringBuilder(str).reverse().toString();
        return str.equals(reversed);
    }
    
    // Method 3: Palindrome ignoring non-alphanumeric (case insensitive)
    public static boolean isPalindromeIgnoreCase(String str) {
        if (str == null) return false;
        
        str = str.toLowerCase().replaceAll("[^a-z0-9]", "");
        return isPalindrome(str);
    }
    
    // Method 4: Check if string can be rearranged as palindrome
    public static boolean canFormPalindrome(String str) {
        Set<Character> set = new HashSet<>();
        
        for (char c : str.toCharArray()) {
            if (set.contains(c)) {
                set.remove(c);
            } else {
                set.add(c);
            }
        }
        
        return set.size() <= 1;
    }
    
    // Method 5: Check if substring is palindrome (DP approach)
    public static boolean isPalindromeSubstring(String str, int start, int end) {
        while (start < end) {
            if (str.charAt(start) != str.charAt(end)) {
                return false;
            }
            start++;
            end--;
        }
        return true;
    }
    
    // Method 6: Longest palindromic substring
    public static String longestPalindrome(String s) {
        if (s == null || s.length() < 2) return s;
        
        int start = 0;
        int maxLength = 1;
        
        for (int i = 0; i < s.length(); i++) {
            // Odd length palindrome
            int len1 = expandAroundCenter(s, i, i);
            // Even length palindrome
            int len2 = expandAroundCenter(s, i, i + 1);
            
            int len = Math.max(len1, len2);
            
            if (len > maxLength) {
                maxLength = len;
                start = i - (len - 1) / 2;
            }
        }
        
        return s.substring(start, start + maxLength);
    }
    
    private static int expandAroundCenter(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }
        return right - left - 1;
    }
    
    public static void main(String[] args) {
        System.out.println(isPalindrome("racecar"));     // true
        System.out.println(isPalindrome("hello"));       // false
        System.out.println(isPalindromeIgnoreCase("A man, a plan, a canal: Panama")); // true
        System.out.println(canFormPalindrome("civic"));  // true
        System.out.println(canFormPalindrome("hello"));  // false
        System.out.println(longestPalindrome("babad"));  // "bab" or "aba"
    }
}
```

**3. Find Duplicate elements in array/list**

```java
public class FindDuplicates {
    
    // Method 1: Using HashSet (O(n) time, O(n) space)
    public static <T> List<T> findDuplicatesWithSet(List<T> list) {
        Set<T> seen = new HashSet<>();
        Set<T> duplicates = new HashSet<>();
        
        for (T element : list) {
            if (!seen.add(element)) {
                duplicates.add(element);
            }
        }
        
        return new ArrayList<>(duplicates);
    }
    
    // Method 2: Using HashMap with counts
    public static <T> Map<T, Long> countOccurrences(List<T> list) {
        return list.stream()
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
    
    public static <T> List<T> findDuplicatesWithMap(List<T> list) {
        return countOccurrences(list).entrySet().stream()
            .filter(entry -> entry.getValue() > 1)
            .map(Map.Entry::getKey)
            .collect(Collectors.toList());
    }
    
    // Method 3: Using Java 8 streams (one-liner)
    public static <T> List<T> findDuplicatesWithStream(List<T> list) {
        Set<T> seen = new HashSet<>();
        return list.stream()
            .filter(e -> !seen.add(e))
            .distinct()
            .collect(Collectors.toList());
    }
    
    // Method 4: For integer array with values in range [0, n-1]
    public static List<Integer> findDuplicatesInPlace(int[] nums) {
        List<Integer> duplicates = new ArrayList<>();
        
        for (int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]);
            if (nums[index] < 0) {
                duplicates.add(index);
            } else {
                nums[index] = -nums[index];
            }
        }
        
        return duplicates;
    }
    
    // Method 5: Find first duplicate
    public static <T> T findFirstDuplicate(List<T> list) {
        Set<T> seen = new HashSet<>();
        
        for (T element : list) {
            if (seen.contains(element)) {
                return element;
            }
            seen.add(element);
        }
        
        return null;
    }
    
    // Method 6: Remove duplicates and keep order
    public static <T> List<T> removeDuplicatesKeepOrder(List<T> list) {
        Set<T> seen = new LinkedHashSet<>(list);
        return new ArrayList<>(seen);
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 3, 6);
        
        System.out.println(findDuplicatesWithSet(numbers));     // [2, 3]
        System.out.println(findDuplicatesWithMap(numbers));     // [2, 3]
        System.out.println(findDuplicatesWithStream(numbers));  // [2, 3]
        System.out.println(findFirstDuplicate(numbers));        // 2
        
        int[] arr = {1, 2, 3, 2, 4, 3, 5};
        System.out.println(findDuplicatesInPlace(arr));         // [2, 3]
    }
}
```

**4. Find Unique elements**

```java
public class FindUniqueElements {
    
    // Method 1: Using Set (all unique)
    public static <T> Set<T> getUniqueElements(List<T> list) {
        return new HashSet<>(list);
    }
    
    // Method 2: Elements that appear exactly once
    public static <T> List<T> getElementsWithFrequencyOne(List<T> list) {
        Map<T, Long> frequency = list.stream()
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
        
        return frequency.entrySet().stream()
            .filter(entry -> entry.getValue() == 1)
            .map(Map.Entry::getKey)
            .collect(Collectors.toList());
    }
    
    // Method 3: Find unique in array where all others appear twice (XOR trick)
    public static int findUniqueInPairs(int[] nums) {
        int result = 0;
        for (int num : nums) {
            result ^= num;  // XOR cancels duplicates
        }
        return result;
    }
    
    // Method 4: Find unique where others appear exactly N times
    public static int findUniqueWhereOthersAppearNTimes(int[] nums, int n) {
        int result = 0;
        
        for (int i = 0; i < 32; i++) {
            int sum = 0;
            int bit = 1 << i;
            
            for (int num : nums) {
                if ((num & bit) != 0) {
                    sum++;
                }
            }
            
            if (sum % n != 0) {
                result |= bit;
            }
        }
        
        return result;
    }
    
    // Method 5: First non-repeating character in string
    public static Character firstNonRepeatingChar(String str) {
        Map<Character, Long> frequency = str.chars()
            .mapToObj(c -> (char) c)
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
        
        return str.chars()
            .mapToObj(c -> (char) c)
            .filter(c -> frequency.get(c) == 1)
            .findFirst()
            .orElse(null);
    }
    
    // Method 6: Unique elements in two arrays (union without duplicates)
    public static <T> Set<T> unionUnique(T[] arr1, T[] arr2) {
        Set<T> result = new HashSet<>();
        result.addAll(Arrays.asList(arr1));
        result.addAll(Arrays.asList(arr2));
        return result;
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 3, 5, 1);
        
        System.out.println(getUniqueElements(numbers));              // [1, 2, 3, 4, 5]
        System.out.println(getElementsWithFrequencyOne(numbers));    // [4, 5]
        
        int[] pairs = {2, 3, 5, 4, 5, 3, 4};
        System.out.println(findUniqueInPairs(pairs));               // 2
        
        String str = "swiss";
        System.out.println(firstNonRepeatingChar(str));              // 'w'
    }
}
```

**5. Find Second/Highest value**

```java
public class FindHighestValues {
    
    // Method 1: Find maximum
    public static <T extends Comparable<T>> T findMax(List<T> list) {
        if (list == null || list.isEmpty()) return null;
        
        T max = list.get(0);
        for (T element : list) {
            if (element.compareTo(max) > 0) {
                max = element;
            }
        }
        return max;
    }
    
    // Method 2: Find second maximum (with duplicates)
    public static <T extends Comparable<T>> T findSecondMax(List<T> list) {
        if (list == null || list.size() < 2) return null;
        
        T max = null;
        T secondMax = null;
        
        for (T element : list) {
            if (max == null || element.compareTo(max) > 0) {
                secondMax = max;
                max = element;
            } else if (secondMax == null || 
                      (element.compareTo(secondMax) > 0 && element.compareTo(max) < 0)) {
                secondMax = element;
            }
        }
        
        return secondMax;
    }
    
    // Method 3: Find second maximum (distinct values)
    public static <T extends Comparable<T>> T findSecondMaxDistinct(List<T> list) {
        Set<T> distinct = new TreeSet<>(list);
        List<T> sorted = new ArrayList<>(distinct);
        
        if (sorted.size() < 2) return null;
        return sorted.get(sorted.size() - 2);
    }
    
    // Method 4: Using Java 8 streams
    public static <T extends Comparable<T>> Optional<T> findSecondMaxWithStream(List<T> list) {
        return list.stream()
            .distinct()
            .sorted(Comparator.reverseOrder())
            .skip(1)
            .findFirst();
    }
    
    // Method 5: Find Kth largest element
    public static <T extends Comparable<T>> T findKthLargest(List<T> list, int k) {
        if (list == null || list.size() < k) return null;
        
        // Using min-heap of size k
        PriorityQueue<T> minHeap = new PriorityQueue<>();
        
        for (T element : list) {
            minHeap.offer(element);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        return minHeap.peek();
    }
    
    // Method 6: Find Kth largest using Quick Select (O(n) average)
    public static int findKthLargestQuickSelect(int[] nums, int k) {
        return quickSelect(nums, 0, nums.length - 1, nums.length - k);
    }
    
    private static int quickSelect(int[] nums, int left, int right, int k) {
        if (left == right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        
        if (pivotIndex == k) {
            return nums[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(nums, pivotIndex + 1, right, k);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k);
        }
    }
    
    private static int partition(int[] nums, int left, int right) {
        int pivot = nums[right];
        int i = left;
        
        for (int j = left; j < right; j++) {
            if (nums[j] <= pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        
        swap(nums, i, right);
        return i;
    }
    
    private static void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(10, 5, 20, 8, 20, 15);
        
        System.out.println(findMax(numbers));                    // 20
        System.out.println(findSecondMax(numbers));              // 15
        System.out.println(findSecondMaxDistinct(numbers));      // 15
        System.out.println(findSecondMaxWithStream(numbers));    // Optional[15]
        System.out.println(findKthLargest(numbers, 3));          // 15
        
        int[] arr = {10, 5, 20, 8, 15};
        System.out.println(findKthLargestQuickSelect(arr, 2));   // 15
    }
}
```

**6. Find Next Greater Element**

```java
public class NextGreaterElement {
    
    // Method 1: Next Greater Element to the right (NGE)
    public static int[] nextGreaterElement(int[] nums) {
        int[] result = new int[nums.length];
        Stack<Integer> stack = new Stack<>();
        
        // Traverse from right to left
        for (int i = nums.length - 1; i >= 0; i--) {
            // Pop elements smaller than current
            while (!stack.isEmpty() && stack.peek() <= nums[i]) {
                stack.pop();
            }
            
            result[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(nums[i]);
        }
        
        return result;
    }
    
    // Method 2: Next Greater Element using HashMap (for circular array)
    public static int[] nextGreaterElementCircular(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);
        Stack<Integer> stack = new Stack<>();
        
        // Traverse twice (circular)
        for (int i = 0; i < 2 * n; i++) {
            int index = i % n;
            
            while (!stack.isEmpty() && nums[stack.peek()] < nums[index]) {
                result[stack.pop()] = nums[index];
            }
            
            if (i < n) {
                stack.push(index);
            }
        }
        
        return result;
    }
    
    // Method 3: Next Greater Element for each element in nums1 from nums2
    public static int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> nextGreater = new HashMap<>();
        Stack<Integer> stack = new Stack<>();
        
        for (int num : nums2) {
            while (!stack.isEmpty() && stack.peek() < num) {
                nextGreater.put(stack.pop(), num);
            }
            stack.push(num);
        }
        
        int[] result = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            result[i] = nextGreater.getOrDefault(nums1[i], -1);
        }
        
        return result;
    }
    
    // Method 4: Previous Greater Element
    public static int[] previousGreaterElement(int[] nums) {
        int[] result = new int[nums.length];
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < nums.length; i++) {
            while (!stack.isEmpty() && stack.peek() <= nums[i]) {
                stack.pop();
            }
            
            result[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(nums[i]);
        }
        
        return result;
    }
    
    // Method 5: Next Smaller Element
    public static int[] nextSmallerElement(int[] nums) {
        int[] result = new int[nums.length];
        Stack<Integer> stack = new Stack<>();
        
        for (int i = nums.length - 1; i >= 0; i--) {
            while (!stack.isEmpty() && stack.peek() >= nums[i]) {
                stack.pop();
            }
            
            result[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(nums[i]);
        }
        
        return result;
    }
    
    public static void main(String[] args) {
        int[] nums = {4, 5, 2, 10, 8};
        
        System.out.println("Original: " + Arrays.toString(nums));
        System.out.println("Next Greater: " + Arrays.toString(nextGreaterElement(nums))); 
        // [5, 10, 10, -1, -1]
        
        System.out.println("Previous Greater: " + Arrays.toString(previousGreaterElement(nums))); 
        // [-1, -1, 5, -1, 10]
        
        System.out.println("Next Smaller: " + Arrays.toString(nextSmallerElement(nums))); 
        // [2, 2, -1, 8, -1]
        
        int[] circular = {1, 2, 1};
        System.out.println("Circular NGE: " + Arrays.toString(nextGreaterElementCircular(circular))); 
        // [2, -1, 2]
    }
}
```

**7. Frequency of characters in a string**

```java
public class CharacterFrequency {
    
    // Method 1: Using HashMap (most common)
    public static Map<Character, Integer> getFrequency(String str) {
        Map<Character, Integer> frequency = new HashMap<>();
        
        for (char c : str.toCharArray()) {
            frequency.put(c, frequency.getOrDefault(c, 0) + 1);
        }
        
        return frequency;
    }
    
    // Method 2: Using Java 8 streams
    public static Map<Character, Long> getFrequencyWithStream(String str) {
        return str.chars()
            .mapToObj(c -> (char) c)
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
    
    // Method 3: Using int array for ASCII characters
    public static int[] getFrequencyAscii(String str) {
        int[] freq = new int[256];  // Extended ASCII
        
        for (char c : str.toCharArray()) {
            freq[c]++;
        }
        
        return freq;
    }
    
    // Method 4: Frequency sorted by value (descending)
    public static List<Map.Entry<Character, Long>> getFrequencySorted(String str) {
        return getFrequencyWithStream(str).entrySet().stream()
            .sorted(Map.Entry.<Character, Long>comparingByValue().reversed())
            .collect(Collectors.toList());
    }
    
    // Method 5: Most frequent character
    public static Character mostFrequentChar(String str) {
        if (str == null || str.isEmpty()) return null;
        
        return getFrequencyWithStream(str).entrySet().stream()
            .max(Map.Entry.comparingByValue())
            .map(Map.Entry::getKey)
            .orElse(null);
    }
    
    // Method 6: Least frequent character
    public static Character leastFrequentChar(String str) {
        if (str == null || str.isEmpty()) return null;
        
        return getFrequencyWithStream(str).entrySet().stream()
            .min(Map.Entry.comparingByValue())
            .map(Map.Entry::getKey)
            .orElse(null);
    }
    
    // Method 7: Check if all characters have same frequency
    public static boolean allSameFrequency(String str) {
        Map<Character, Long> freq = getFrequencyWithStream(str);
        if (freq.isEmpty()) return true;
        
        long firstFreq = freq.values().iterator().next();
        return freq.values().stream().allMatch(f -> f == firstFreq);
    }
    
    // Method 8: Print frequency in formatted way
    public static void printFrequency(String str) {
        Map<Character, Integer> freq = getFrequency(str);
        
        for (Map.Entry<Character, Integer> entry : freq.entrySet()) {
            System.out.printf("'%c' : %d %s%n", 
                entry.getKey(), 
                entry.getValue(),
                "*".repeat(entry.getValue()));
        }
    }
    
    public static void main(String[] args) {
        String str = "hello world";
        
        System.out.println(getFrequency(str));        // { =1, r=1, d=1, e=1, w=1, h=1, l=3, o=2}
        System.out.println(getFrequencyWithStream(str));
        
        System.out.println("Most frequent: " + mostFrequentChar(str));  // l
        System.out.println("Least frequent: " + leastFrequentChar(str)); // (space)
        
        getFrequencySorted(str).forEach(entry -> 
            System.out.println(entry.getKey() + ": " + entry.getValue()));
        
        printFrequency(str);
        // 'l' : 3 ***
        // 'o' : 2 **
        // ' ' : 1 *
        // etc.
    }
}
```

**8. Find unique character in string**

```java
public class UniqueCharacter {
    
    // Method 1: First non-repeating character
    public static Character firstNonRepeating(String str) {
        Map<Character, Integer> frequency = new LinkedHashMap<>();
        
        for (char c : str.toCharArray()) {
            frequency.put(c, frequency.getOrDefault(c, 0) + 1);
        }
        
        for (Map.Entry<Character, Integer> entry : frequency.entrySet()) {
            if (entry.getValue() == 1) {
                return entry.getKey();
            }
        }
        
        return null;
    }
    
    // Method 2: Using Java 8 streams
    public static Optional<Character> firstNonRepeatingStream(String str) {
        Map<Character, Long> freq = str.chars()
            .mapToObj(c -> (char) c)
            .collect(Collectors.groupingBy(Function.identity(), 
                     LinkedHashMap::new, Collectors.counting()));
        
        return freq.entrySet().stream()
            .filter(entry -> entry.getValue() == 1)
            .map(Map.Entry::getKey)
            .findFirst();
    }
    
    // Method 3: First repeating character
    public static Character firstRepeating(String str) {
        Set<Character> seen = new HashSet<>();
        
        for (char c : str.toCharArray()) {
            if (seen.contains(c)) {
                return c;
            }
            seen.add(c);
        }
        
        return null;
    }
    
    // Method 4: Using indexOf and lastIndexOf (simple)
    public static Character firstUniqueSimple(String str) {
        for (int i = 0; i < str.length(); i++) {
            char c = str.charAt(i);
            if (str.indexOf(c) == str.lastIndexOf(c)) {
                return c;
            }
        }
        return null;
    }
    
    // Method 5: All unique characters (check if string has all unique chars)
    public static boolean hasAllUniqueChars(String str) {
        if (str == null || str.length() > 128) return false;
        
        boolean[] chars = new boolean[128];
        
        for (char c : str.toCharArray()) {
            if (chars[c]) {
                return false;
            }
            chars[c] = true;
        }
        
        return true;
    }
    
    // Method 6: Using Bit Vector (space efficient)
    public static boolean hasAllUniqueCharsBitVector(String str) {
        int checker = 0;
        
        for (char c : str.toCharArray()) {
            int bitIndex = c - 'a';
            if ((checker & (1 << bitIndex)) > 0) {
                return false;
            }
            checker |= (1 << bitIndex);
        }
        
        return true;
    }
    
    // Method 7: Last non-repeating character
    public static Character lastNonRepeating(String str) {
        Map<Character, Integer> frequency = getFrequency(str);
        
        for (int i = str.length() - 1; i >= 0; i--) {
            char c = str.charAt(i);
            if (frequency.get(c) == 1) {
                return c;
            }
        }
        
        return null;
    }
    
    private static Map<Character, Integer> getFrequency(String str) {
        Map<Character, Integer> freq = new HashMap<>();
        for (char c : str.toCharArray()) {
            freq.put(c, freq.getOrDefault(c, 0) + 1);
        }
        return freq;
    }
    
    public static void main(String[] args) {
        String str = "swiss";
        
        System.out.println("First non-repeating: " + firstNonRepeating(str));     // w
        System.out.println("First repeating: " + firstRepeating(str));            // s
        System.out.println("Last non-repeating: " + lastNonRepeating(str));       // w
        System.out.println("All unique: " + hasAllUniqueChars("abcde"));          // true
        System.out.println("All unique: " + hasAllUniqueChars("abca"));           // false
        
        firstNonRepeatingStream(str).ifPresent(c -> System.out.println("Stream: " + c));
    }
}
```

### Java 8 Stream Coding

**1. Remove duplicates from list: list1={1,2,3,2,4,5} list2={6,7,8,7} Output={1,2,3,4,5,6,7,8}**

```java
public class MergeAndRemoveDuplicates {
    
    // Method 1: Using Stream.concat() and distinct()
    public static List<Integer> mergeAndRemoveDuplicates(List<Integer> list1, List<Integer> list2) {
        return Stream.concat(list1.stream(), list2.stream())
            .distinct()
            .sorted()
            .collect(Collectors.toList());
    }
    
    // Method 2: Using Set then sorting
    public static List<Integer> mergeWithSet(List<Integer> list1, List<Integer> list2) {
        Set<Integer> set = new HashSet<>();
        set.addAll(list1);
        set.addAll(list2);
        
        return set.stream()
            .sorted()
            .collect(Collectors.toList());
    }
    
    // Method 3: Union operation (mathematical)
    public static List<Integer> union(List<Integer> list1, List<Integer> list2) {
        List<Integer> result = new ArrayList<>(list1);
        result.addAll(list2);
        
        return result.stream()
            .distinct()
            .sorted()
            .collect(Collectors.toList());
    }
    
    // Method 4: Using TreeSet (automatically sorted)
    public static List<Integer> mergeWithTreeSet(List<Integer> list1, List<Integer> list2) {
        Set<Integer> treeSet = new TreeSet<>();
        treeSet.addAll(list1);
        treeSet.addAll(list2);
        
        return new ArrayList<>(treeSet);
    }
    
    public static void main(String[] args) {
        List<Integer> list1 = Arrays.asList(1, 2, 3, 2, 4, 5);
        List<Integer> list2 = Arrays.asList(6, 7, 8, 7);
        
        System.out.println(mergeAndRemoveDuplicates(list1, list2));  // [1, 2, 3, 4, 5, 6, 7, 8]
        System.out.println(mergeWithSet(list1, list2));              // [1, 2, 3, 4, 5, 6, 7, 8]
        System.out.println(union(list1, list2));                     // [1, 2, 3, 4, 5, 6, 7, 8]
        System.out.println(mergeWithTreeSet(list1, list2));          // [1, 2, 3, 4, 5, 6, 7, 8]
    }
}
```

**2. Find duplicates using Streams**

```java
public class FindDuplicatesWithStream {
    
    // Method 1: Using frequency map
    public static <T> List<T> findDuplicates(List<T> list) {
        Map<T, Long> frequency = list.stream()
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
        
        return frequency.entrySet().stream()
            .filter(entry -> entry.getValue() > 1)
            .map(Map.Entry::getKey)
            .collect(Collectors.toList());
    }
    
    // Method 2: Using filter with Set (more efficient)
    public static <T> List<T> findDuplicatesEfficient(List<T> list) {
        Set<T> seen = new HashSet<>();
        
        return list.stream()
            .filter(element -> !seen.add(element))
            .distinct()
            .collect(Collectors.toList());
    }
    
    // Method 3: Find duplicates with their count
    public static <T> Map<T, Long> findDuplicatesWithCount(List<T> list) {
        return list.stream()
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
            .entrySet().stream()
            .filter(entry -> entry.getValue() > 1)
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
    }
    
    // Method 4: Find duplicates using Collections.frequency
    public static <T> List<T> findDuplicatesWithFrequency(List<T> list) {
        return list.stream()
            .distinct()
            .filter(element -> Collections.frequency(list, element) > 1)
            .collect(Collectors.toList());
    }
    
    // Method 5: Find first duplicate
    public static <T> Optional<T> findFirstDuplicate(List<T> list) {
        Set<T> seen = new HashSet<>();
        
        return list.stream()
            .filter(element -> !seen.add(element))
            .findFirst();
    }
    
    // Method 6: Find all duplicates with their indices
    public static <T> Map<T, List<Integer>> findDuplicatesWithIndices(List<T> list) {
        Map<T, List<Integer>> indexMap = new HashMap<>();
        
        for (int i = 0; i < list.size(); i++) {
            T element = list.get(i);
            indexMap.computeIfAbsent(element, k -> new ArrayList<>()).add(i);
        }
        
        return indexMap.entrySet().stream()
            .filter(entry -> entry.getValue().size() > 1)
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 3, 6, 2);
        
        System.out.println(findDuplicates(numbers));           // [2, 3]
        System.out.println(findDuplicatesEfficient(numbers));  // [2, 3]
        System.out.println(findDuplicatesWithCount(numbers));  // {2=3, 3=2}
        System.out.println(findFirstDuplicate(numbers));       // Optional[2]
        System.out.println(findDuplicatesWithIndices(numbers)); // {2=[1, 3, 8], 3=[2, 6]}
    }
}
```

**3. Reverse list using Streams**

```java
public class ReverseListWithStream {
    
    // Method 1: Using sorted with custom comparator
    public static <T> List<T> reverseWithSorted(List<T> list) {
        return list.stream()
            .sorted(Collections.reverseOrder())
            .collect(Collectors.toList());
    }
    
    // Method 2: Using Collections.reverse()
    public static <T> List<T> reverseWithCollector(List<T> list) {
        return list.stream()
            .collect(Collectors.collectingAndThen(
                Collectors.toList(),
                l -> {
                    Collections.reverse(l);
                    return l;
                }
            ));
    }
    
    // Method 3: Using reduce (not efficient)
    public static <T> List<T> reverseWithReduce(List<T> list) {
        return list.stream()
            .reduce(new ArrayList<>(),
                    (acc, element) -> {
                        acc.add(0, element);
                        return acc;
                    },
                    (list1, list2) -> {
                        list1.addAll(list2);
                        return list1;
                    });
    }
    
    // Method 4: Using IntStream for indices
    public static <T> List<T> reverseWithIndices(List<T> list) {
        return IntStream.range(0, list.size())
            .mapToObj(i -> list.get(list.size() - 1 - i))
            .collect(Collectors.toList());
    }
    
    // Method 5: Using Collections.reverse() directly (simplest)
    public static <T> List<T> reverseDirect(List<T> list) {
        List<T> result = new ArrayList<>(list);
        Collections.reverse(result);
        return result;
    }
    
    // Method 6: Custom Collector for reversal
    public static <T> Collector<T, ?, List<T>> reverseCollector() {
        return Collector.of(
            ArrayList::new,
            (list, item) -> list.add(0, item),
            (list1, list2) -> {
                list1.addAll(list2);
                return list1;
            }
        );
    }
    
    public static <T> List<T> reverseWithCustomCollector(List<T> list) {
        return list.stream()
            .collect(reverseCollector());
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        System.out.println(reverseWithSorted(numbers));           // [5, 4, 3, 2, 1]
        System.out.println(reverseWithCollector(numbers));        // [5, 4, 3, 2, 1]
        System.out.println(reverseWithIndices(numbers));          // [5, 4, 3, 2, 1]
        System.out.println(reverseDirect(numbers));               // [5, 4, 3, 2, 1]
        System.out.println(reverseWithCustomCollector(numbers));  // [5, 4, 3, 2, 1]
    }
}
```

**4. Find 3rd highest using Streams**

```java
public class FindKthHighest {
    
    // Method 1: Skip and limit
    public static <T extends Comparable<T>> Optional<T> findKthHighest(List<T> list, int k) {
        return list.stream()
            .distinct()
            .sorted(Comparator.reverseOrder())
            .skip(k - 1)
            .findFirst();
    }
    
    // Method 2: Using limit and skip
    public static <T extends Comparable<T>> T findKthHighestWithLimit(List<T> list, int k) {
        return list.stream()
            .distinct()
            .sorted(Comparator.reverseOrder())
            .limit(k)
            .skip(k - 1)
            .findFirst()
            .orElse(null);
    }
    
    // Method 3: Using min-heap (for very large lists)
    public static <T extends Comparable<T>> T findKthHighestWithHeap(List<T> list, int k) {
        PriorityQueue<T> minHeap = new PriorityQueue<>();
        
        for (T element : list) {
            minHeap.offer(element);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        return minHeap.isEmpty() ? null : minHeap.peek();
    }
    
    // Method 4: Using collectingAndThen
    public static <T extends Comparable<T>> Optional<T> findKthHighestCollecting(List<T> list, int k) {
        return list.stream()
            .collect(Collectors.collectingAndThen(
                Collectors.toCollection(() -> new TreeSet<>(Comparator.reverseOrder())),
                set -> set.stream().skip(k - 1).findFirst()
            ));
    }
    
    // Method 5: With custom comparator
    public static <T> Optional<T> findKthHighest(List<T> list, int k, Comparator<T> comparator) {
        return list.stream()
            .distinct()
            .sorted(comparator.reversed())
            .skip(k - 1)
            .findFirst();
    }
    
    // Method 6: Find 3rd distinct highest
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(10, 5, 20, 8, 20, 15, 25, 25, 30);
        
        System.out.println(findKthHighest(numbers, 1));  // Optional[30] (highest)
        System.out.println(findKthHighest(numbers, 2));  // Optional[25] (2nd highest)
        System.out.println(findKthHighest(numbers, 3));  // Optional[20] (3rd highest)
        System.out.println(findKthHighest(numbers, 4));  // Optional[15]
        
        System.out.println(findKthHighestWithLimit(numbers, 3));  // 20
        System.out.println(findKthHighestWithHeap(numbers, 3));    // 20
        
        // With custom objects
        List<Employee> employees = Arrays.asList(
            new Employee("John", 50000),
            new Employee("Jane", 75000),
            new Employee("Bob", 60000),
            new Employee("Alice", 80000),
            new Employee("Tom", 65000)
        );
        
        Optional<Employee> thirdHighestPaid = findKthHighest(
            employees, 3, Comparator.comparing(Employee::getSalary)
        );
        thirdHighestPaid.ifPresent(e -> 
            System.out.println(e.getName() + ": " + e.getSalary()));  // Bob: 60000
    }
}
```

**5. Count frequency using Streams**

```java
public class CountFrequencyWithStream {
    
    // Method 1: Using groupingBy with counting
    public static <T> Map<T, Long> getFrequency(List<T> list) {
        return list.stream()
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
    
    // Method 2: Using toMap with merge function
    public static <T> Map<T, Integer> getFrequencyWithMerge(List<T> list) {
        return list.stream()
            .collect(Collectors.toMap(
                Function.identity(),
                element -> 1,
                Integer::sum
            ));
    }
    
    // Method 3: Frequency with custom Map implementation
    public static <T> Map<T, Long> getFrequencyWithCustomMap(List<T> list) {
        return list.stream()
            .collect(Collectors.groupingBy(
                Function.identity(),
                LinkedHashMap::new,  // Preserve order
                Collectors.counting()
            ));
    }
    
    // Method 4: Character frequency in string
    public static Map<Character, Long> getCharFrequency(String str) {
        return str.chars()
            .mapToObj(c -> (char) c)
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
    
    // Method 5: Word frequency in sentence
    public static Map<String, Long> getWordFrequency(String sentence) {
        return Arrays.stream(sentence.toLowerCase().split("\\W+"))
            .filter(word -> !word.isEmpty())
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
    
    // Method 6: Frequency with percentage
    public static <T> Map<T, Double> getFrequencyWithPercentage(List<T> list) {
        int total = list.size();
        
        return list.stream()
            .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.collectingAndThen(
                    Collectors.counting(),
                    count -> (count * 100.0) / total
                )
            ));
    }
    
    // Method 7: Most frequent element
    public static <T> Optional<Map.Entry<T, Long>> getMostFrequent(List<T> list) {
        return getFrequency(list).entrySet().stream()
            .max(Map.Entry.comparingByValue());
    }
    
    // Method 8: Elements with frequency greater than threshold
    public static <T> List<T> getElementsWithFrequencyAbove(List<T> list, long threshold) {
        return getFrequency(list).entrySet().stream()
            .filter(entry -> entry.getValue() > threshold)
            .map(Map.Entry::getKey)
            .collect(Collectors.toList());
    }
    
    public static void main(String[] args) {
        List<String> fruits = Arrays.asList("apple", "banana", "apple", "orange", "banana", "apple");
        
        System.out.println(getFrequency(fruits));
        // {orange=1, banana=2, apple=3}
        
        System.out.println(getFrequencyWithPercentage(fruits));
        // {orange=16.66, banana=33.33, apple=50.0}
        
        System.out.println(getMostFrequent(fruits));  // Optional[apple=3]
        
        System.out.println(getElementsWithFrequencyAbove(fruits, 1));  // [banana, apple]
        
        String text = "The quick brown fox jumps over the lazy dog";
        System.out.println(getWordFrequency(text));
        // {the=2, quick=1, brown=1, fox=1, jumps=1, over=1, lazy=1, dog=1}
    }
}
```

**6. Grouping using groupingBy**

```java
public class GroupingByExamples {
    
    static class Person {
        String name;
        int age;
        String city;
        String department;
        double salary;
        
        // constructor, getters, toString
    }
    
    // 1. Simple grouping by single field
    public static Map<String, List<Person>> groupByCity(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(Person::getCity));
    }
    
    // 2. Grouping with counting
    public static Map<String, Long> countByDepartment(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(Person::getDepartment, Collectors.counting()));
    }
    
    // 3. Grouping with summing
    public static Map<String, Double> sumSalaryByDepartment(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getDepartment, 
                Collectors.summingDouble(Person::getSalary)
            ));
    }
    
    // 4. Grouping with averaging
    public static Map<String, Double> averageAgeByCity(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getCity,
                Collectors.averagingInt(Person::getAge)
            ));
    }
    
    // 5. Grouping with custom mapping
    public static Map<String, List<String>> getNamesByDepartment(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getDepartment,
                Collectors.mapping(Person::getName, Collectors.toList())
            ));
    }
    
    // 6. Multi-level grouping
    public static Map<String, Map<Integer, List<Person>>> groupByCityAndAge(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getCity,
                Collectors.groupingBy(Person::getAge)
            ));
    }
    
    // 7. Grouping with reducing
    public static Map<String, Optional<Person>> getHighestSalaryByDepartment(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getDepartment,
                Collectors.maxBy(Comparator.comparing(Person::getSalary))
            ));
    }
    
    // 8. Partitioning (special case of grouping with boolean)
    public static Map<Boolean, List<Person>> partitionByAge(List<Person> people, int threshold) {
        return people.stream()
            .collect(Collectors.partitioningBy(p -> p.getAge() >= threshold));
    }
    
    // 9. Grouping with complex key (multiple fields)
    public static Map<String, List<Person>> groupByCityAndDepartment(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(p -> p.getCity() + "_" + p.getDepartment()));
    }
    
    // 10. Grouping with downstream collector - Collectors.toSet()
    public static Map<String, Set<String>> getUniqueNamesByCity(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getCity,
                Collectors.mapping(Person::getName, Collectors.toSet())
            ));
    }
    
    // 11. Grouping with summarizing
    public static Map<String, IntSummaryStatistics> getAgeStatsByCity(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getCity,
                Collectors.summarizingInt(Person::getAge)
            ));
    }
    
    // 12. Grouping and collecting to specific Map implementation
    public static Map<String, List<Person>> groupByCityWithTreeMap(List<Person> people) {
        return people.stream()
            .collect(Collectors.groupingBy(
                Person::getCity,
                TreeMap::new,
                Collectors.toList()
            ));
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("John", 25, "NYC", "IT", 70000),
            new Person("Jane", 30, "LA", "HR", 65000),
            new Person("Bob", 25, "NYC", "IT", 75000),
            new Person("Alice", 35, "LA", "IT", 80000),
            new Person("Tom", 28, "NYC", "HR", 60000)
        );
        
        System.out.println("Group by city: " + groupByCity(people));
        System.out.println("Count by department: " + countByDepartment(people));
        System.out.println("Sum salary by department: " + sumSalaryByDepartment(people));
        System.out.println("Average age by city: " + averageAgeByCity(people));
        System.out.println("Names by department: " + getNamesByDepartment(people));
        System.out.println("Highest salary by department: " + getHighestSalaryByDepartment(people));
        System.out.println("Partition by age 30: " + partitionByAge(people, 30));
    }
}
```

### Other - Fibonacci Series

```java
public class FibonacciSeries {
    
    // Method 1: Iterative (most efficient)
    public static void printFibonacciIterative(int n) {
        int a = 0, b = 1;
        
        System.out.print("Fibonacci series: ");
        for (int i = 0; i < n; i++) {
            System.out.print(a + " ");
            int next = a + b;
            a = b;
            b = next;
        }
        System.out.println();
    }
    
    // Method 2: Return as list
    public static List<Integer> getFibonacciList(int n) {
        List<Integer> fib = new ArrayList<>();
        int a = 0, b = 1;
        
        for (int i = 0; i < n; i++) {
            fib.add(a);
            int next = a + b;
            a = b;
            b = next;
        }
        
        return fib;
    }
    
    // Method 3: Recursive (inefficient for large n)
    public static int fibonacciRecursive(int n) {
        if (n <= 1) return n;
        return fibonacciRecursive(n - 1) + fibonacciRecursive(n - 2);
    }
    
    // Method 4: Recursive with memoization (efficient)
    public static int fibonacciMemoization(int n, Map<Integer, Integer> memo) {
        if (n <= 1) return n;
        
        if (memo.containsKey(n)) {
            return memo.get(n);
        }
        
        int result = fibonacciMemoization(n - 1, memo) + fibonacciMemoization(n - 2, memo);
        memo.put(n, result);
        return result;
    }
    
    // Method 5: Using Stream.iterate (Java 8)
    public static List<Integer> fibonacciWithStream(int n) {
        return Stream.iterate(new int[]{0, 1}, f -> new int[]{f[1], f[0] + f[1]})
            .limit(n)
            .map(f -> f[0])
            .collect(Collectors.toList());
    }
    
    // Method 6: Nth Fibonacci number using matrix exponentiation (O(log n))
    public static int fibonacciMatrix(int n) {
        if (n <= 1) return n;
        
        int[][] F = {{1, 1}, {1, 0}};
        power(F, n - 1);
        
        return F[0][0];
    }
    
    private static void multiply(int[][] F, int[][] M) {
        int x = F[0][0] * M[0][0] + F[0][1] * M[1][0];
        int y = F[0][0] * M[0][1] + F[0][1] * M[1][1];
        int z = F[1][0] * M[0][0] + F[1][1] * M[1][0];
        int w = F[1][0] * M[0][1] + F[1][1] * M[1][1];
        
        F[0][0] = x;
        F[0][1] = y;
        F[1][0] = z;
        F[1][1] = w;
    }
    
    private static void power(int[][] F, int n) {
        if (n <= 1) return;
        
        int[][] M = {{1, 1}, {1, 0}};
        power(F, n / 2);
        multiply(F, F);
        
        if (n % 2 != 0) {
            multiply(F, M);
        }
    }
    
    // Method 7: Using formula (Binet's formula)
    public static int fibonacciFormula(int n) {
        double phi = (1 + Math.sqrt(5)) / 2;
        return (int) Math.round(Math.pow(phi, n) / Math.sqrt(5));
    }
    
    // Method 8: Check if a number is Fibonacci
    public static boolean isFibonacci(int num) {
        int a = 0, b = 1;
        
        while (a < num) {
            int next = a + b;
            a = b;
            b = next;
        }
        
        return a == num;
    }
    
    // Method 9: Sum of first N Fibonacci numbers
    public static int sumOfFirstNFibonacci(int n) {
        // Sum(F0..Fn) = F(n+2) - 1
        return fibonacciMemoization(n + 2, new HashMap<>()) - 1;
    }
    
    public static void main(String[] args) {
        int n = 10;
        
        System.out.println("Iterative:");
        printFibonacciIterative(n);
        
        System.out.println("As list: " + getFibonacciList(n));
        
        System.out.println("Recursive (n=10): " + fibonacciRecursive(10));
        
        System.out.println("Memoization (n=40): " + fibonacciMemoization(40, new HashMap<>()));
        
        System.out.println("Stream: " + fibonacciWithStream(n));
        
        System.out.println("Matrix exponentiation: " + fibonacciMatrix(n));
        
        System.out.println("Formula: " + fibonacciFormula(n));
        
        System.out.println("Is 34 Fibonacci? " + isFibonacci(34));  // true
        System.out.println("Is 35 Fibonacci? " + isFibonacci(35));  // false
        
        System.out.println("Sum of first 10 Fibonacci: " + sumOfFirstNFibonacci(n));
    }
}
```

---

## 🔹 12. Design Patterns

### Singleton Pattern (implementation)

```java
// 1. Eager Initialization
public class SingletonEager {
    private static final SingletonEager INSTANCE = new SingletonEager();
    
    private SingletonEager() {}
    
    public static SingletonEager getInstance() {
        return INSTANCE;
    }
}

// 2. Lazy Initialization (not thread-safe)
public class SingletonLazyNotSafe {
    private static SingletonLazyNotSafe instance;
    
    private SingletonLazyNotSafe() {}
    
    public static SingletonLazyNotSafe getInstance() {
        if (instance == null) {
            instance = new SingletonLazyNotSafe();
        }
        return instance;
    }
}

// 3. Thread-safe with synchronized method (slow)
public class SingletonSynchronized {
    private static SingletonSynchronized instance;
    
    private SingletonSynchronized() {}
    
    public static synchronized SingletonSynchronized getInstance() {
        if (instance == null) {
            instance = new SingletonSynchronized();
        }
        return instance;
    }
}

// 4. Double-checked locking (most common)
public class SingletonDoubleChecked {
    private static volatile SingletonDoubleChecked instance;
    
    private SingletonDoubleChecked() {}
    
    public static SingletonDoubleChecked getInstance() {
        if (instance == null) {
            synchronized (SingletonDoubleChecked.class) {
                if (instance == null) {
                    instance = new SingletonDoubleChecked();
                }
            }
        }
        return instance;
    }
}

// 5. Bill Pugh Inner Static Class (best for most cases)
public class SingletonBillPugh {
    private SingletonBillPugh() {}
    
    private static class SingletonHelper {
        private static final SingletonBillPugh INSTANCE = new SingletonBillPugh();
    }
    
    public static SingletonBillPugh getInstance() {
        return SingletonHelper.INSTANCE;
    }
}

// 6. Enum Singleton (prevents reflection and serialization issues)
public enum SingletonEnum {
    INSTANCE;
    
    public void doSomething() {
        System.out.println("Singleton operation");
    }
}

// 7. Using Spring (managed by container)
@Component
@Scope("singleton")  // Default scope
public class SpringSingleton {
    // Spring manages single instance
}

// Testing all implementations
public class SingletonTest {
    public static void main(String[] args) {
        // Test Bill Pugh
        SingletonBillPugh s1 = SingletonBillPugh.getInstance();
        SingletonBillPugh s2 = SingletonBillPugh.getInstance();
        System.out.println(s1 == s2);  // true
        
        // Test Enum
        SingletonEnum.INSTANCE.doSomething();
        
        // Reflection attack prevention
        try {
            Constructor<?>[] constructors = SingletonBillPugh.class.getDeclaredConstructors();
            for (Constructor<?> constructor : constructors) {
                constructor.setAccessible(true);
                SingletonBillPugh s3 = (SingletonBillPugh) constructor.newInstance();
                System.out.println(s3 == s1);  // false (reflection breaks)
            }
        } catch (Exception e) {
            // Bill Pugh can be broken by reflection
        }
        
        // Enum prevents reflection
        // SingletonEnum.INSTANCE.getClass().getDeclaredConstructors() - returns empty
    }
}
```

### Factory Design Pattern

```java
// Product interface
interface Vehicle {
    void drive();
    String getType();
}

// Concrete products
class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a car...");
    }
    
    @Override
    public String getType() {
        return "CAR";
    }
}

class Bike implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Riding a bike...");
    }
    
    @Override
    public String getType() {
        return "BIKE";
    }
}

class Truck implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a truck...");
    }
    
    @Override
    public String getType() {
        return "TRUCK";
    }
}

// Simple Factory
class VehicleFactory {
    public static Vehicle createVehicle(String type) {
        if (type == null) {
            return null;
        }
        
        switch (type.toUpperCase()) {
            case "CAR":
                return new Car();
            case "BIKE":
                return new Bike();
            case "TRUCK":
                return new Truck();
            default:
                throw new IllegalArgumentException("Unknown vehicle type: " + type);
        }
    }
}

// Factory Method Pattern
abstract class VehicleCreator {
    // Factory method
    public abstract Vehicle createVehicle();
    
    // Template method
    public void testVehicle() {
        Vehicle vehicle = createVehicle();
        System.out.println("Created: " + vehicle.getType());
        vehicle.drive();
    }
}

class CarCreator extends VehicleCreator {
    @Override
    public Vehicle createVehicle() {
        return new Car();
    }
}

class BikeCreator extends VehicleCreator {
    @Override
    public Vehicle createVehicle() {
        return new Bike();
    }
}

// Abstract Factory Pattern
interface VehicleAbstractFactory {
    Vehicle createEconomyVehicle();
    Vehicle createLuxuryVehicle();
    Vehicle createSportsVehicle();
}

class CarFactory implements VehicleAbstractFactory {
    @Override
    public Vehicle createEconomyVehicle() {
        return new Car();  // Economy car
    }
    
    @Override
    public Vehicle createLuxuryVehicle() {
        return new Car();  // Luxury car with features
    }
    
    @Override
    public Vehicle createSportsVehicle() {
        return new Car();  // Sports car
    }
}

// Using with Spring (Bean Factory)
@Configuration
public class AppConfig {
    
    @Bean
    public VehicleFactory vehicleFactory() {
        return new VehicleFactory();
    }
    
    @Bean
    public Vehicle car(@Autowired VehicleFactory factory) {
        return factory.createVehicle("CAR");
    }
}

// Usage
public class FactoryDemo {
    public static void main(String[] args) {
        // Simple Factory
        Vehicle car = VehicleFactory.createVehicle("CAR");
        car.drive();  // Driving a car...
        
        Vehicle bike = VehicleFactory.createVehicle("BIKE");
        bike.drive();  // Riding a bike...
        
        // Factory Method
        VehicleCreator creator = new CarCreator();
        Vehicle v1 = creator.createVehicle();
        v1.drive();
        
        creator = new BikeCreator();
        Vehicle v2 = creator.createVehicle();
        v2.drive();
        
        // Using with configuration
        Map<String, Vehicle> vehicles = Map.of(
            "CAR", new Car(),
            "BIKE", new Bike()
        );
        
        String userChoice = "CAR";
        Vehicle selected = vehicles.get(userChoice);
        selected.drive();
    }
}
```

### Saga Pattern (microservices)

```java
// Saga Pattern Implementation for Distributed Transactions

// 1. Saga Step
@Getter
@Setter
public class SagaStep {
    private String name;
    private Runnable action;
    private Runnable compensation;
    
    public SagaStep(String name, Runnable action, Runnable compensation) {
        this.name = name;
        this.action = action;
        this.compensation = compensation;
    }
}

// 2. Saga Orchestrator
public class SagaOrchestrator {
    private List<SagaStep> steps = new ArrayList<>();
    private Stack<SagaStep> executedSteps = new Stack<>();
    
    public SagaOrchestrator addStep(String name, Runnable action, Runnable compensation) {
        steps.add(new SagaStep(name, action, compensation));
        return this;
    }
    
    public void execute() {
        for (SagaStep step : steps) {
            try {
                System.out.println("Executing step: " + step.getName());
                step.getAction().run();
                executedSteps.push(step);
            } catch (Exception e) {
                System.err.println("Step failed: " + step.getName() + " - " + e.getMessage());
                compensate();
                throw new SagaExecutionException("Saga failed at step: " + step.getName(), e);
            }
        }
        System.out.println("Saga completed successfully!");
    }
    
    private void compensate() {
        System.out.println("Starting compensation...");
        while (!executedSteps.isEmpty()) {
            SagaStep step = executedSteps.pop();
            try {
                System.out.println("Compensating step: " + step.getName());
                step.getCompensation().run();
            } catch (Exception e) {
                System.err.println("Compensation failed for step: " + step.getName());
                // Log for manual intervention
            }
        }
    }
}

// 3. Service Clients for Saga
@Service
public class OrderSagaOrchestrator {
    
    @Autowired
    private OrderService orderService;
    
    @Autowired
    private PaymentServiceClient paymentClient;
    
    @Autowired
    private InventoryServiceClient inventoryClient;
    
    @Autowired
    private ShippingServiceClient shippingClient;
    
    @Autowired
    private KafkaTemplate<String, SagaEvent> kafkaTemplate;
    
    public void processOrder(OrderRequest request) {
        SagaOrchestrator saga = new SagaOrchestrator();
        
        String orderId = UUID.randomUUID().toString();
        
        saga.addStep(
            "CREATE_ORDER",
            () -> orderService.createOrder(orderId, request),
            () -> orderService.cancelOrder(orderId)
        );
        
        saga.addStep(
            "RESERVE_PAYMENT",
            () -> paymentClient.reservePayment(orderId, request.getAmount()),
            () -> paymentClient.refundPayment(orderId)
        );
        
        saga.addStep(
            "RESERVE_INVENTORY",
            () -> inventoryClient.reserveStock(orderId, request.getProductId(), request.getQuantity()),
            () -> inventoryClient.releaseStock(orderId, request.getProductId(), request.getQuantity())
        );
        
        saga.addStep(
            "CREATE_SHIPMENT",
            () -> shippingClient.createShipment(orderId, request.getAddress()),
            () -> shippingClient.cancelShipment(orderId)
        );
        
        saga.execute();
        
        // Publish success event
        kafkaTemplate.send("order-completed", new SagaEvent(orderId, "COMPLETED"));
    }
}

// 4. Choreography-based Saga
@Component
public class OrderSagaChoreography {
    
    @Autowired
    private ApplicationEventPublisher eventPublisher;
    
    // Order Service
    @EventListener
    @Async
    public void handleCreateOrder(CreateOrderCommand command) {
        try {
            Order order = orderRepository.save(new Order(command));
            eventPublisher.publishEvent(new OrderCreatedEvent(order));
        } catch (Exception e) {
            eventPublisher.publishEvent(new OrderFailedEvent(command.getOrderId(), e.getMessage()));
        }
    }
    
    // Payment Service
    @EventListener
    @Async
    public void handleOrderCreated(OrderCreatedEvent event) {
        try {
            Payment payment = paymentService.processPayment(event.getOrder());
            eventPublisher.publishEvent(new PaymentProcessedEvent(event.getOrder(), payment));
        } catch (Exception e) {
            eventPublisher.publishEvent(new OrderFailedEvent(event.getOrder().getId(), e.getMessage()));
        }
    }
    
    // Inventory Service
    @EventListener
    @Async
    public void handlePaymentProcessed(PaymentProcessedEvent event) {
        try {
            inventoryService.reserveStock(event.getOrder());
            eventPublisher.publishEvent(new InventoryReservedEvent(event.getOrder()));
        } catch (Exception e) {
            eventPublisher.publishEvent(new OrderFailedEvent(event.getOrder().getId(), e.getMessage()));
        }
    }
    
    // Compensating transactions
    @EventListener
    public void handleOrderFailed(OrderFailedEvent event) {
        System.out.println("Order failed: " + event.getOrderId() + " - " + event.getReason());
        // Execute compensating actions
        orderService.cancelOrder(event.getOrderId());
    }
}

// 5. Saga State Management with Database
@Entity
@Table(name = "saga_executions")
public class SagaExecution {
    @Id
    private String sagaId;
    private String sagaType;
    private String currentStep;
    @Enumerated(EnumType.STRING)
    private SagaStatus status;
    private String payload;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    
    @ElementCollection
    private List<String> completedSteps;
    @ElementCollection
    private List<String> compensatedSteps;
    
    // getters/setters
}

// 6. Saga Repository and Service
@Repository
public interface SagaExecutionRepository extends JpaRepository<SagaExecution, String> {}

@Service
public class SagaPersistenceService {
    
    @Autowired
    private SagaExecutionRepository sagaRepository;
    
    public void saveSagaState(SagaExecution saga) {
        saga.setUpdatedAt(LocalDateTime.now());
        sagaRepository.save(saga);
    }
    
    public Optional<SagaExecution> resumeSaga(String sagaId) {
        return sagaRepository.findById(sagaId);
    }
    
    public List<SagaExecution> getFailedSagas() {
        return sagaRepository.findByStatus(SagaStatus.FAILED);
    }
}

// 7. Using Resilience4j for retry and circuit breaker
@Service
public class ResilientSagaOrchestrator {
    
    @Retry(name = "sagaStep", fallbackMethod = "fallback")
    @CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
    public void executePaymentStep(String orderId) {
        paymentClient.processPayment(orderId);
    }
    
    public void fallback(String orderId, Exception e) {
        System.out.println("Fallback triggered for order: " + orderId);
        // Initiate compensation
        compensateOrder(orderId);
    }
}
```

---

## 🔹 13. Miscellaneous / Frontend (Flutter)

### Lifecycle of Stateful Widget

```dart
// Flutter Stateful Widget Lifecycle

class MyStatefulWidget extends StatefulWidget {
  @override
  _MyStatefulWidgetState createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  
  // 1. createState() - Called when widget is created
  // Creates the State object
  
  // 2. mounted - true after createState, before dispose
  // Check if state is still in tree
  
  // 3. initState() - Called once when widget inserted into tree
  @override
  void initState() {
    super.initState();
    print('initState - Called once');
    // Initialize data, subscribe to streams, start animations
    // Cannot call BuildContext here
  }
  
  // 4. didChangeDependencies() - Called after initState and when dependencies change
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    print('didChangeDependencies - Called when InheritedWidget changes');
    // Safe to use BuildContext here
    // Called after initState and whenever dependencies change
  }
  
  // 5. build() - Called frequently, returns widget tree
  @override
  Widget build(BuildContext context) {
    print('build - Building widget tree');
    return Container(
      child: Text('Hello'),
    );
  }
  
  // 6. setState() - Triggers rebuild
  void updateData() {
    setState(() {
      // Update state variables
      // This triggers build() again
    });
  }
  
  // 7. didUpdateWidget() - Called when parent widget rebuilds with new config
  @override
  void didUpdateWidget(MyStatefulWidget oldWidget) {
    super.didUpdateWidget(oldWidget);
    print('didUpdateWidget - Widget configuration changed');
    // React to changes in widget properties
  }
  
  // 8. deactivate() - Called when widget removed from tree temporarily
  @override
  void deactivate() {
    super.deactivate();
    print('deactivate - Widget being removed from tree');
    // Rarely used
  }
  
  // 9. dispose() - Called when widget removed permanently
  @override
  void dispose() {
    print('dispose - Cleaning up resources');
    // Cancel subscriptions, stop animations, dispose controllers
    super.dispose();
  }
}

// Lifecycle Flow Diagram:
/*
Widget created
    |
    v
createState()
    |
    v
mounted = true
    |
    v
initState()
    |
    v
didChangeDependencies()
    |
    v
build()
    |
    v (if setState called)
    |
    +-----> build() again
    |
    v (if parent rebuilds)
    |
didUpdateWidget()
    |
    v (if removed)
    |
deactivate()
    |
    v
dispose()
    |
    v
mounted = false
*/

// Practical Example with real use cases
class LifecycleDemo extends StatefulWidget {
  final String title;
  
  LifecycleDemo({required this.title});
  
  @override
  _LifecycleDemoState createState() => _LifecycleDemoState();
}

class _LifecycleDemoState extends State<LifecycleDemo> with WidgetsBindingObserver {
  
  late StreamSubscription _subscription;
  
  @override
  void initState() {
    super.initState();
    
    // Initialize data
    loadData();
    
    // Add lifecycle observer
    WidgetsBinding.instance.addObserver(this);
    
    // Subscribe to stream
    _subscription = someStream.listen((data) {
      setState(() {
        // Update UI with new data
      });
    });
    
    // Start animation
    _controller = AnimationController(vsync: this);
  }
  
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    super.didChangeAppLifecycleState(state);
    
    switch (state) {
      case AppLifecycleState.resumed:
        print('App resumed - visible and responding');
        break;
      case AppLifecycleState.inactive:
        print('App inactive - not receiving events');
        break;
      case AppLifecycleState.paused:
        print('App paused - not visible');
        break;
      case AppLifecycleState.detached:
        print('App detached - about to be destroyed');
        break;
    }
  }
  
  @override
  void didUpdateWidget(LifecycleDemo oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (oldWidget.title != widget.title) {
      // Title changed, reload data
      loadData();
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(widget.title)),
      body: FutureBuilder(
        future: loadData(),
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return Text(snapshot.data.toString());
          }
          return CircularProgressIndicator();
        },
      ),
    );
  }
  
  @override
  void dispose() {
    // Cancel subscription
    _subscription.cancel();
    
    // Remove observer
    WidgetsBinding.instance.removeObserver(this);
    
    // Dispose animation controller
    _controller.dispose();
    
    super.dispose();
  }
  
  Future<String> loadData() async {
    // Simulate API call
    await Future.delayed(Duration(seconds: 1));
    return 'Data loaded';
  }
}
```

### Navigation and its effect on Widget Tree

```dart
// Navigation Effects on Widget Tree

// 1. Basic Navigation (push/pop)
class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // Push new screen - adds to navigation stack
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => DetailScreen()),
            );
            // Previous widget remains in tree (in background)
          },
          child: Text('Go to Detail'),
        ),
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Detail'),
        leading: IconButton(
          icon: Icon(Icons.arrow_back),
          onPressed: () {
            // Pop - removes current screen from stack
            Navigator.pop(context);
            // DetailScreen removed, HomeScreen becomes active again
          },
        ),
      ),
    );
  }
}

// 2. Named Routes (with routes defined)
class Routes {
  static const String home = '/';
  static const String detail = '/detail';
  static const String settings = '/settings';
}

// In MaterialApp
MaterialApp(
  initialRoute: Routes.home,
  routes: {
    Routes.home: (context) => HomeScreen(),
    Routes.detail: (context) => DetailScreen(),
    Routes.settings: (context) => SettingsScreen(),
  },
  onGenerateRoute: (settings) {
    // Dynamic routes
    if (settings.name == '/user') {
      final args = settings.arguments as User;
      return MaterialPageRoute(
        builder: (context) => UserProfileScreen(user: args),
      );
    }
    return null;
  },
)

// Navigation with arguments
class UserProfileScreen extends StatelessWidget {
  final User user;
  
  UserProfileScreen({required this.user});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Text('User: ${user.name}'),
    );
  }
}

// Pushing with arguments
Navigator.pushNamed(
  context,
  '/user',
  arguments: User(name: 'John', id: 1),
);

// 3. pushReplacement - Replace current route
Navigator.pushReplacement(
  context,
  MaterialPageRoute(builder: (context) => NewScreen()),
);
// Previous screen removed from stack, cannot go back to it

// 4. pushAndRemoveUntil - Remove all routes below
Navigator.pushAndRemoveUntil(
  context,
  MaterialPageRoute(builder: (context) => HomeScreen()),
  (route) => false, // Remove all routes
);

// 5. popUntil - Pop until specific route
Navigator.popUntil(context, ModalRoute.withName(Routes.home));

// 6. Navigation and State Management Effect
class NavigationEffectDemo extends StatefulWidget {
  @override
  _NavigationEffectDemoState createState() => _NavigationEffectDemoState();
}

class _NavigationEffectDemoState extends State<NavigationEffectDemo> {
  int _counter = 0;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Text('Counter: $_counter'),
          ElevatedButton(
            onPressed: () {
              setState(() {
                _counter++;  // This state is preserved during navigation
              });
            },
            child: Text('Increment'),
          ),
          ElevatedButton(
            onPressed: () async {
              // Navigate and wait for result
              final result = await Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => EditScreen()),
              );
              
              if (result != null) {
                setState(() {
                  // Update state based on result
                  _counter = result;
                });
              }
            },
            child: Text('Edit'),
          ),
        ],
      ),
    );
  }
}

// 7. Returning data from screen
class EditScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // Return data to previous screen
            Navigator.pop(context, 42);
          },
          child: Text('Return 42'),
        ),
      ),
    );
  }
}

// 8. Navigation with Provider/Riverpod (state management)
class CounterProvider extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  
  void increment() {
    _count++;
    notifyListeners();
  }
}

class HomeWithProvider extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider(
      create: (_) => CounterProvider(),
      child: Scaffold(
        body: Consumer<CounterProvider>(
          builder: (context, provider, child) {
            return Column(
              children: [
                Text('Count: ${provider.count}'),
                ElevatedButton(
                  onPressed: () {
                    // Provider state persists across navigation
                    provider.increment();
                  },
                  child: Text('Increment'),
                ),
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(builder: (context) => AnotherScreen()),
                    );
                    // Provider still available in new screen
                  },
                  child: Text('Next'),
                ),
              ],
            );
          },
        ),
      ),
    );
  }
}

// 9. Effect on Widget Tree - Understanding
/*
Navigation Effect Summary:

1. Push: New route added to stack
   - Previous widget stays in tree (mounted = true, but invisible)
   - New widget created and inserted

2. Pop: Current route removed from stack
   - Widget disposed (dispose() called)
   - Previous widget becomes active again

3. PushReplacement: Current replaced with new
   - Current widget disposed
   - New widget created

4. Memory Considerations:
   - Too many pushes can cause memory issues
   - Use pushReplacement when back navigation not needed
   - Consider using IndexedStack for tab navigation

5. State Preservation:
   - Stateful widgets preserve state when in background
   - Use AutomaticKeepAliveClientMixin to prevent disposal
*/
```

---

## 🔹 14. HR / Project-Based Questions

### Tell me about your experience

**Sample Answer Structure:**

"Thank you for asking. Let me walk you through my professional journey.

**Current Role (Last 2 years):**
I'm currently working as a Java Developer at [Company Name], where I'm part of a 6-member team building a microservices-based e-commerce platform. My primary responsibilities include:

- Designing and implementing RESTful APIs using Spring Boot
- Managing database operations with PostgreSQL and JPA/Hibernate
- Implementing security using JWT and Spring Security
- Setting up message queues with Kafka for asynchronous processing
- Writing unit and integration tests using JUnit and Mockito

**Key Achievement:** I recently led the migration of our monolithic application to microservices, which reduced deployment time by 60% and improved scalability.

**Previous Role (2 years before that):**
I worked as a Java Developer at [Previous Company], where I focused on:

- Developing core banking application features
- Working with legacy code and modernizing components
- Implementing batch processing for daily transactions
- Collaborating with cross-functional teams

**Key Achievement:** I optimized a slow-running database query that processed millions of records, reducing execution time from 45 minutes to under 2 minutes.

**Technical Skills:**
- Languages: Java (8, 11, 17), SQL
- Frameworks: Spring Boot, Spring Cloud, Hibernate
- Databases: PostgreSQL, MySQL, MongoDB
- Tools: Git, Docker, Jenkins, Kubernetes (basics)
- Testing: JUnit, Mockito, Integration Testing

**What I'm looking for:**
I'm excited about this opportunity at Antino Labs because I see a strong alignment with my skills in microservices and cloud-native development. I'm particularly interested in working with modern tech stacks and being part of innovative projects."

### Explain your project architecture

**Sample Answer - E-commerce Microservices Project:**

"Let me explain the architecture of my recent e-commerce platform project.

**High-Level Architecture:**
```
                    [API Gateway]
                         |
        ---------------------------------
        |               |               |
    [Auth Service]  [Order Service]  [Product Service]
        |               |               |
    [User DB]       [Order DB]      [Product DB]
        
                    [Kafka]
                       |
              [Inventory Service]
              [Payment Service]
              [Notification Service]
```

**Detailed Components:**

**1. API Gateway (Spring Cloud Gateway)**
- Single entry point for all client requests
- Handles routing, load balancing, and authentication
- Rate limiting and request/response transformation

```yaml
# Gateway configuration
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/api/orders/**
          filters:
            - name: CircuitBreaker
              args:
                name: orderService
                fallbackUri: forward:/fallback/orders
```

**2. Service Discovery (Netflix Eureka)**
- All services register with Eureka
- Enables dynamic service discovery
- Provides load balancing out of the box

**3. Order Service (Core Service)**
- Handles order creation, updates, and tracking
- Uses Saga pattern for distributed transactions
- Communicates with other services via REST and Kafka

**4. Database Architecture**
- Database per service pattern
- Order Service: PostgreSQL (ACID compliance)
- Product Service: MongoDB (flexible schema)
- Caching: Redis for frequently accessed data

**5. Communication Patterns**
- Synchronous: REST with Feign Clients for real-time needs
- Asynchronous: Kafka for event-driven communication
- Example: Order creation event triggers inventory check

**6. Security Implementation**
- JWT-based authentication
- OAuth2 with Keycloak for identity management
- Role-based access control (RBAC)

**7. Data Flow Example - Order Placement:**
```
1. Client -> API Gateway (with JWT)
2. Gateway -> Auth Service (validate token)
3. Gateway -> Order Service (forward request)
4. Order Service -> Product Service (check availability - REST)
5. Order Service -> Payment Service (process payment - Kafka event)
6. Order Service -> Inventory Service (reserve stock - Kafka)
7. Order Service -> Notification Service (send confirmation)
8. Order Service -> Response to client
```

**8. Resilience Patterns:**
- Circuit Breaker (Resilience4j) for external calls
- Retry mechanism with exponential backoff
- Timeout configurations for all external calls
- Bulkhead pattern to limit concurrent requests

**9. Monitoring and Observability:**
- Distributed tracing with Jaeger
- Metrics with Micrometer + Prometheus
- Centralized logging with ELK stack
- Health checks and readiness probes

**10. Deployment:**
- Containerized with Docker
- Orchestrated with Kubernetes
- CI/CD pipeline using Jenkins
- Environment-specific configurations

**Challenges and Solutions:**
- **Challenge:** Handling distributed transactions
  **Solution:** Implemented Saga pattern with Kafka

- **Challenge:** Service discovery in dynamic environment
  **Solution:** Used Eureka with client-side load balancing

- **Challenge:** Debugging across services
  **Solution:** Implemented correlation IDs and distributed tracing"

### How did you handle: Concurrency

**Sample Answer:**

"In my e-commerce project, we faced several concurrency challenges. Here's how I handled them:

**1. Inventory Management (Race Conditions):**

Problem: Multiple users trying to purchase the last item simultaneously.

Solution: Optimistic locking with version field
```java
@Entity
public class Product {
    @Version
    private Long version;
    private int quantity;
    
    @Transactional
    public boolean reserveStock(int requestedQty) {
        Product p = repository.findById(id).get();
        if (p.quantity >= requestedQty) {
            p.quantity -= requestedQty;
            repository.save(p);  // Throws OptimisticLockException if version mismatch
            return true;
        }
        return false;
    }
}
```

**2. Order Processing (Thread Safety):**

Used ConcurrentHashMap for thread-safe caching:
```java
@Component
public class OrderCache {
    private final ConcurrentHashMap<String, Order> orderCache = new ConcurrentHashMap<>();
    
    public void putOrder(String id, Order order) {
        orderCache.put(id, order);  // Thread-safe
    }
    
    public Order getOrder(String id) {
        return orderCache.get(id);
    }
}
```

**3. Batch Processing with Parallel Streams:**
```java
public void processOrders(List<Order> orders) {
    orders.parallelStream()
        .forEach(order -> {
            // Each order processed in parallel
            processOrder(order);
        });
}
```

**4. Rate Limiting with Semaphore:**
```java
@Service
public class PaymentService {
    private final Semaphore rateLimiter = new Semaphore(10);  // Max 10 concurrent
    
    public void processPayment(Payment payment) {
        if (rateLimiter.tryAcquire(5, TimeUnit.SECONDS)) {
            try {
                // Process payment
            } finally {
                rateLimiter.release();
            }
        } else {
            throw new RateLimitException("Service busy");
        }
    }
}
```

**5. Async Processing with CompletableFuture:**
```java
public CompletableFuture<Order> processOrderAsync(Order order) {
    return CompletableFuture.supplyAsync(() -> validateOrder(order))
        .thenApplyAsync(this::calculatePrice)
        .thenApplyAsync(this::processPayment)
        .thenApplyAsync(this::confirmOrder)
        .exceptionally(ex -> {
            log.error("Order failed", ex);
            return handleFailure(order);
        });
}
```

**6. Preventing Deadlocks:**
- Consistent lock ordering across all services
- Using tryLock() with timeout instead of synchronized
- Minimizing lock scope

**Results:**
- Successfully handled 1000+ concurrent users
- Zero race condition incidents after implementation
- 99.9% order processing success rate"

### How did you handle: Logging

**Sample Answer:**

"I implemented a comprehensive logging strategy across our microservices:

**1. Structured Logging with MDC:**
```java
@Component
public class LoggingFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) {
        MDC.put("requestId", UUID.randomUUID().toString());
        MDC.put("userId", getCurrentUserId());
        MDC.put("correlationId", getCorrelationId(request));
        
        long startTime = System.currentTimeMillis();
        
        try {
            chain.doFilter(request, response);
        } finally {
            long duration = System.currentTimeMillis() - startTime;
            log.info("Request completed in {} ms", duration);
            MDC.clear();
        }
    }
}
```

**2. Different Log Levels for Different Environments:**
```yaml
# application.yml
logging:
  level:
    com.mycompany: DEBUG  # Development
    org.springframework: WARN
    com.netflix: ERROR
  
# Production overrides
---
spring:
  profiles: production
logging:
  level:
    com.mycompany: INFO
```

**3. Centralized Logging with ELK Stack:**
- Filebeat ships logs to Logstash
- Logstash processes and structures logs
- Elasticsearch indexes logs
- Kibana provides visualization

**4. Log Format:**
```json
{
  "timestamp": "2024-01-15T10:30:45.123Z",
  "level": "INFO",
  "service": "order-service",
  "traceId": "abc123",
  "spanId": "def456",
  "requestId": "xyz789",
  "userId": "user123",
  "message": "Order created successfully",
  "orderId": "ORD-12345",
  "duration_ms": 234,
  "status": "SUCCESS"
}
```

**5. Aspect-Oriented Logging:**
```java
@Aspect
@Component
public class LoggingAspect {
    
    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        Object result = joinPoint.proceed();
        long duration = System.currentTimeMillis() - start;
        
        log.info("{} executed in {} ms", joinPoint.getSignature(), duration);
        return result;
    }
    
    @AfterThrowing(pointcut = "within(com.mycompany..*)", throwing = "ex")
    public void logException(Exception ex) {
        log.error("Exception occurred: {}", ex.getMessage(), ex);
    }
}
```

**6. Sensitive Data Masking:**
```java
@Component
public class SensitiveDataMasker {
    
    public String maskSensitiveData(String input) {
        return input.replaceAll("\\b\\d{16}\\b", "****-****-****-****")  // Credit card
                    .replaceAll("\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b", "***@***.***");  // Email
    }
}
```

**7. Audit Logging:**
```java
@Entity
public class AuditLog {
    @Id
    private Long id;
    private String action;
    private String userId;
    private String entityType;
    private String entityId;
    private String oldValue;
    private String newValue;
    private LocalDateTime timestamp;
}

@EventListener
public void handleEntityChange(EntityChangeEvent event) {
    auditLogRepository.save(new AuditLog(event));
}
```

**Benefits Achieved:**
- Reduced debugging time by 70%
- Quick incident response with Kibana dashboards
- Compliance with data retention policies
- Proactive issue detection with log alerts"

### How did you handle: Transactions

**Sample Answer:**

"Handling transactions, especially in distributed systems, was crucial for our e-commerce platform. Here's my approach:

**1. Local Transactions (ACID):**
```java
@Service
@Transactional
public class OrderService {
    
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private PaymentRepository paymentRepository;
    
    public Order createOrder(OrderRequest request) {
        // All operations in single transaction
        Order order = orderRepository.save(new Order(request));
        Payment payment = paymentRepository.save(new Payment(order));
        // Both succeed or both fail
        return order;
    }
    
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void processInNewTransaction(Long orderId) {
        // Runs in separate transaction
    }
    
    @Transactional(timeout = 30)  // Timeout after 30 seconds
    public void processWithTimeout() { }
    
    @Transactional(rollbackFor = {CustomException.class})
    public void processWithCustomRollback() { }
}
```

**2. Declarative Transaction Management:**
```java
@Configuration
@EnableTransactionManagement
public class TransactionConfig {
    
    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(emf);
        transactionManager.setDefaultTimeout(30);
        return transactionManager;
    }
}
```

**3. Programmatic Transaction Management:**
```java
@Service
public class FlexibleTransactionService {
    
    @Autowired
    private TransactionTemplate transactionTemplate;
    
    public void processWithRetry() {
        transactionTemplate.execute(status -> {
            try {
                // Business logic
                return processData();
            } catch (OptimisticLockException e) {
                status.setRollbackOnly();
                // Retry logic
                return null;
            }
        });
    }
}
```

**4. Distributed Transactions - Saga Pattern:**
```java
@Component
public class OrderSagaOrchestrator {
    
    @Autowired
    private SagaOrchestrator sagaOrchestrator;
    
    public void processOrder(Order order) {
        sagaOrchestrator
            .addStep("CREATE_ORDER", 
                () -> createOrder(order),
                () -> cancelOrder(order))
            .addStep("PROCESS_PAYMENT",
                () -> processPayment(order),
                () -> refundPayment(order))
            .addStep("RESERVE_INVENTORY",
                () -> reserveInventory(order),
                () -> releaseInventory(order))
            .execute();
    }
}
```

**5. Transaction Propagation Levels:**
```java
// REQUIRED (default) - Join existing or create new
@Transactional(propagation = Propagation.REQUIRED)

// REQUIRES_NEW - Always create new, suspend existing
@Transactional(propagation = Propagation.REQUIRES_NEW)

// NESTED - Savepoint within existing transaction
@Transactional(propagation = Propagation.NESTED)

// MANDATORY - Must have existing transaction
@Transactional(propagation = Propagation.MANDATORY)

// NEVER - Must NOT have transaction
@Transactional(propagation = Propagation.NEVER)

// SUPPORTS - Execute with or without transaction
@Transactional(propagation = Propagation.SUPPORTS)
```

**6. Isolation Levels:**
```java
@Transactional(isolation = Isolation.READ_COMMITTED)  // Default PostgreSQL
@Transactional(isolation = Isolation.REPEATABLE_READ) // Default MySQL
@Transactional(isolation = Isolation.SERIALIZABLE)    // Highest isolation
@Transactional(isolation = Isolation.READ_UNCOMMITTED)// Lowest (dirty reads)
```

**7. Deadlock Handling:**
```java
@Service
public class DeadlockHandler {
    
    @Retryable(value = {DeadlockLoserDataAccessException.class}, maxAttempts = 3, backoff = @Backoff(delay = 100))
    public void updateWithRetry(Long id, String data) {
        // Operation that may cause deadlock
        repository.update(id, data);
    }
}
```

**8. Transaction Event Listeners:**
```java
@Component
public class TransactionEvents {
    
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void handleAfterCommit(CustomEvent event) {
        // Execute after transaction commits successfully
        sendNotification(event);
    }
    
    @TransactionalEventListener(phase = TransactionPhase.AFTER_ROLLBACK)
    public void handleAfterRollback(CustomEvent event) {
        // Execute after rollback
        logError(event);
    }
}
```

**Challenges and Solutions:**

| Challenge | Solution |
|-----------|----------|
| Long-running transactions | Split into smaller transactions, use @Async |
| Distributed transactions | Saga pattern with compensating transactions |
| Deadlocks | Consistent lock ordering, retry mechanism |
| Performance issues | Optimistic locking, read-only transactions |
| Cross-service consistency | Eventual consistency with message queue |

**Best Practices Implemented:**
- Keep transactions as short as possible
- Avoid @Transactional on controller methods
- Use readOnly=true for query methods
- Don't call @Transactional methods from within same class
- Monitor transaction metrics (duration, rollback rate)"

### Why should we hire you?

**Sample Answer:**

"I believe I'm an excellent fit for this position for several reasons:

**1. Technical Alignment:**
- My experience with Java 17, Spring Boot, and microservices directly matches your tech stack
- I've implemented JWT-based security and worked with Kafka for event-driven architecture
- I'm familiar with the exact technologies mentioned in your requirements

**2. Problem-Solving Track Record:**
- Successfully migrated a monolithic application to microservices, reducing deployment time by 60%
- Optimized database queries that improved response time from 2 seconds to 200ms
- Implemented caching strategy that reduced database load by 40%

**3. Recent Project Experience:**
In my current role, I built a distributed order processing system that:
- Handles 10,000+ concurrent users
- Maintains 99.9% uptime
- Processes orders within 500ms

**4. Learning Agility:**
- I continuously update my skills (completed Java 17 certification last month)
- I'm an active contributor to open-source projects
- I regularly attend tech conferences and write technical blogs

**5. Team Contribution:**
- Mentored 3 junior developers who are now productive team members
- Implemented code review guidelines that improved code quality by 30%
- Introduced pair programming that reduced bugs by 25%

**6. Cultural Fit:**
- I thrive in collaborative environments
- I'm passionate about clean code and best practices
- I believe in continuous improvement and knowledge sharing

**7. Immediate Contribution:**
- I can start contributing from day one with minimal ramp-up
- I bring best practices from my previous experience
- I can help modernize existing systems

**What sets me apart:**
- I don't just write code; I think about scalability, maintainability, and user experience
- I take ownership of features from requirement to deployment
- I'm proactive about identifying and solving problems before they become critical

I'm excited about the challenges at Antino Labs and confident I can add immediate value to your team. I'm not just looking for a job; I'm looking to build great products with great people."

---
# When API Does Not Respond - Complete Implementation Guide

## 📋 Table of Contents
1. [Understanding the Problem](#understanding)
2. [Immediate Actions](#immediate-actions)
3. [Implementation Strategies](#implementation-strategies)
4. [Code Examples](#code-examples)
5. [Best Practices](#best-practices)

---

## 1. Understanding the Problem {#understanding}

When an API doesn't respond, it could be due to:

| Issue Type | Description | Typical Causes |
|------------|-------------|----------------|
| **Timeout** | Request takes too long | Slow database, network latency, deadlock |
| **Network Issue** | Connection lost | DNS failure, firewall, proxy issues |
| **Service Down** | Server not available | Crash, deployment, scaling issues |
| **Resource Exhaustion** | Server overwhelmed | Thread pool full, connection pool exhausted |
| **Infinite Loop** | Code never returns | Bug in business logic |

---

## 2. Immediate Actions {#immediate-actions}

### What to do when you hit a non-responsive API:

```java
// DON'T just wait indefinitely - implement these strategies immediately:

public class ApiCallHandler {
    
    // 1. SET TIMEOUTS (Most Important!)
    public void makeApiCall() {
        // Without timeout - BAD (never do this)
        // String response = restTemplate.getForObject(url, String.class);
        
        // WITH timeout - GOOD
        RestTemplate restTemplate = new RestTemplate();
        restTemplate.setRequestFactory(
            new SimpleClientHttpRequestFactory() {{
                setConnectTimeout(5000);    // 5 seconds to connect
                setReadTimeout(10000);      // 10 seconds to read response
            }}
        );
    }
    
    // 2. USE FALLBACK (Return default/alternative)
    public String getDataWithFallback() {
        try {
            return callExternalApi();
        } catch (Exception e) {
            return getCachedData();  // Return cached data
        }
    }
    
    // 3. IMPLEMENT RETRY (With backoff)
    public String getDataWithRetry() {
        int maxRetries = 3;
        int retryDelay = 1000;  // Start with 1 second
        
        for (int i = 0; i < maxRetries; i++) {
            try {
                return callExternalApi();
            } catch (Exception e) {
                if (i == maxRetries - 1) throw e;
                Thread.sleep(retryDelay * (i + 1));  // Exponential backoff
            }
        }
        return null;
    }
}
```

---

## 3. Implementation Strategies {#implementation-strategies}

### Strategy 1: Timeout Configuration

```java
// ========== COMPLETE TIMEOUT IMPLEMENTATION ==========

@Configuration
public class TimeoutConfig {
    
    // 1. RestTemplate with Timeouts
    @Bean
    public RestTemplate restTemplate() {
        HttpComponentsClientHttpRequestFactory factory = 
            new HttpComponentsClientHttpRequestFactory();
        
        factory.setConnectTimeout(5000);      // Connection timeout
        factory.setReadTimeout(10000);        // Read timeout
        factory.setConnectionRequestTimeout(3000);  // Connection pool timeout
        
        return new RestTemplate(factory);
    }
    
    // 2. WebClient with Timeouts (Reactive)
    @Bean
    public WebClient webClient() {
        HttpClient httpClient = HttpClient.create()
            .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 5000)
            .responseTimeout(Duration.ofSeconds(10))
            .doOnConnected(conn -> 
                conn.addHandlerLast(new ReadTimeoutHandler(10))
                    .addHandlerLast(new WriteTimeoutHandler(10)));
        
        return WebClient.builder()
            .clientConnector(new ReactorClientHttpConnector(httpClient))
            .build();
    }
    
    // 3. Feign Client with Timeouts
    @Bean
    public Request.Options feignOptions() {
        return new Request.Options(
            5000,  // connect timeout
            10000  // read timeout
        );
    }
}

// Feign Client Interface
@FeignClient(name = "user-service", configuration = FeignConfig.class)
public interface UserClient {
    @GetMapping("/users/{id}")
    User getUser(@PathVariable("id") Long id);
}
```

### Strategy 2: Retry Mechanism

```java
// ========== COMPLETE RETRY IMPLEMENTATION ==========

// 1. Using Spring Retry
@Service
@Slf4j
public class RetryService {
    
    @Retryable(
        value = {TimeoutException.class, RestClientException.class},
        maxAttempts = 3,
        backoff = @Backoff(delay = 1000, multiplier = 2, maxDelay = 5000)
    )
    public String callUnreliableApi() {
        log.info("Attempting API call");
        return restTemplate.getForObject("http://unstable-api/data", String.class);
    }
    
    @Recover
    public String recover(TimeoutException e) {
        log.error("All retries failed", e);
        return "FALLBACK_RESPONSE";
    }
}

// 2. Custom Retry with Exponential Backoff
@Component
public class CustomRetryHandler {
    
    public <T> T executeWithRetry(Supplier<T> operation, String operationName) {
        int maxRetries = 3;
        int initialDelay = 1000;
        int maxDelay = 10000;
        
        for (int attempt = 1; attempt <= maxRetries; attempt++) {
            try {
                return operation.get();
            } catch (Exception e) {
                if (attempt == maxRetries) {
                    throw new RuntimeException("Failed after " + maxRetries + " attempts", e);
                }
                
                long delay = calculateDelay(attempt, initialDelay, maxDelay);
                log.warn("Attempt {} failed for {}, retrying in {} ms", 
                    attempt, operationName, delay, e);
                
                try {
                    Thread.sleep(delay);
                } catch (InterruptedException ie) {
                    Thread.currentThread().interrupt();
                    throw new RuntimeException("Retry interrupted", ie);
                }
            }
        }
        throw new RuntimeException("Unexpected error in retry logic");
    }
    
    private long calculateDelay(int attempt, int initialDelay, int maxDelay) {
        // Exponential backoff: 1000, 2000, 4000, 8000...
        long delay = initialDelay * (long) Math.pow(2, attempt - 1);
        return Math.min(delay, maxDelay);
    }
}

// 3. Retry with Circuit Breaker (Resilience4j)
@Service
public class ResilientService {
    
    @CircuitBreaker(name = "apiService", fallbackMethod = "fallbackResponse")
    @Retry(name = "apiService", fallbackMethod = "fallbackResponse")
    @TimeLimiter(name = "apiService")
    public CompletableFuture<String> callApiWithResilience() {
        return CompletableFuture.supplyAsync(() -> 
            restTemplate.getForObject("http://slow-api/data", String.class)
        );
    }
    
    private CompletableFuture<String> fallbackResponse(Exception e) {
        return CompletableFuture.completedFuture("FALLBACK_DATA");
    }
}
```

### Strategy 3: Circuit Breaker Pattern

```java
// ========== COMPLETE CIRCUIT BREAKER IMPLEMENTATION ==========

@Configuration
public class CircuitBreakerConfig {
    
    @Bean
    public CircuitBreakerRegistry circuitBreakerRegistry() {
        CircuitBreakerConfig config = CircuitBreakerConfig.custom()
            .failureRateThreshold(50)                    // 50% failures opens circuit
            .waitDurationInOpenState(Duration.ofSeconds(30))  // Check after 30 sec
            .permittedNumberOfCallsInHalfOpenState(5)    // 5 test calls in half-open
            .slidingWindowSize(10)                       // 10 calls in window
            .minimumNumberOfCalls(5)                     // Min calls before calculation
            .build();
        
        return CircuitBreakerRegistry.of(config);
    }
}

@Service
@Slf4j
public class CircuitBreakerService {
    
    @Autowired
    private CircuitBreakerRegistry circuitBreakerRegistry;
    
    // Programmatic Circuit Breaker
    public String callWithCircuitBreaker(String url) {
        CircuitBreaker circuitBreaker = circuitBreakerRegistry.circuitBreaker("api-breaker");
        
        Supplier<String> decoratedSupplier = Decorators.ofSupplier(() -> 
            restTemplate.getForObject(url, String.class)
        ).withCircuitBreaker(circuitBreaker)
         .withFallback(as -> "CIRCUIT_OPEN_FALLBACK")
         .decorate();
        
        return decoratedSupplier.get();
    }
    
    // Monitor circuit breaker state
    @Scheduled(fixedDelay = 5000)
    public void monitorCircuitBreaker() {
        CircuitBreaker circuitBreaker = circuitBreakerRegistry.circuitBreaker("api-breaker");
        CircuitBreaker.State state = circuitBreaker.getState();
        
        if (state == CircuitBreaker.State.OPEN) {
            log.warn("Circuit is OPEN - API calls are failing fast");
        } else if (state == CircuitBreaker.State.HALF_OPEN) {
            log.info("Circuit is HALF_OPEN - Testing if API recovered");
        } else {
            log.debug("Circuit is CLOSED - API calls flowing normally");
        }
        
        // Get metrics
        CircuitBreaker.Metrics metrics = circuitBreaker.getMetrics();
        log.debug("Failure rate: {}%, Total calls: {}", 
            metrics.getFailureRate(), metrics.getNumberOfCalls());
    }
}

// Annotation-based Circuit Breaker
@Service
@Slf4j
public class AnnotatedCircuitBreakerService {
    
    @CircuitBreaker(name = "userService", fallbackMethod = "getUserFallback")
    public User getUser(Long userId) {
        return userClient.getUser(userId);
    }
    
    private User getUserFallback(Long userId, Exception e) {
        log.warn("Circuit breaker triggered for user: {}", userId);
        return new User(userId, "Fallback User", "fallback@example.com");
    }
}
```

### Strategy 4: Timeout and Fallback Patterns

```java
// ========== COMPLETE FALLBACK IMPLEMENTATION ==========

@Service
@Slf4j
public class FallbackService {
    
    // 1. Cache Fallback
    @Cacheable(value = "userCache", unless = "#result == null")
    public User getUserWithCacheFallback(Long userId) {
        try {
            return userClient.getUser(userId);
        } catch (Exception e) {
            log.warn("API failed, checking cache for user: {}", userId);
            // Cache will be checked automatically due to @Cacheable
            return null;
        }
    }
    
    // 2. Multiple Fallback Sources
    public String getDataWithMultipleFallbacks(String key) {
        // Try primary API
        try {
            return callPrimaryApi(key);
        } catch (Exception e1) {
            log.warn("Primary API failed, trying secondary", e1);
        }
        
        // Try secondary API
        try {
            return callSecondaryApi(key);
        } catch (Exception e2) {
            log.warn("Secondary API failed, trying cache", e2);
        }
        
        // Try cache
        String cached = getFromCache(key);
        if (cached != null) {
            log.info("Returning cached data for: {}", key);
            return cached;
        }
        
        // Final fallback - default value
        log.error("All fallbacks exhausted for key: {}", key);
        return getDefaultValue(key);
    }
    
    // 3. Stale Cache Fallback (Return old data while refreshing)
    @Service
    public class StaleCacheService {
        
        private final Map<String, CacheEntry> cache = new ConcurrentHashMap<>();
        
        public String getWithStaleFallback(String key) {
            CacheEntry entry = cache.get(key);
            
            try {
                // Try to get fresh data
                String freshData = callApi(key);
                cache.put(key, new CacheEntry(freshData, System.currentTimeMillis()));
                return freshData;
            } catch (Exception e) {
                // Return stale data if available
                if (entry != null) {
                    log.warn("API failed, returning stale data for: {}", key);
                    return entry.getData();
                }
                throw new RuntimeException("No data available", e);
            }
        }
    }
    
    // 4. Graceful Degradation (Return partial/limited data)
    public DashboardData getDashboardWithDegradation() {
        DashboardData dashboard = new DashboardData();
        
        // Critical data - must succeed
        dashboard.setCriticalData(getCriticalData());
        
        // Non-critical data - can fail gracefully
        try {
            dashboard.setAnalytics(getAnalyticsData());
        } catch (Exception e) {
            log.warn("Analytics unavailable, showing limited view");
            dashboard.setAnalytics(new EmptyAnalytics());
        }
        
        try {
            dashboard.setRecommendations(getRecommendations());
        } catch (Exception e) {
            log.warn("Recommendations unavailable");
            dashboard.setRecommendations(Collections.emptyList());
        }
        
        return dashboard;
    }
}
```

### Strategy 5: Async Handling with Timeouts

```java
// ========== ASYNC IMPLEMENTATION ==========

@Service
@Slf4j
public class AsyncApiHandler {
    
    @Autowired
    private RestTemplate restTemplate;
    
    // 1. CompletableFuture with Timeout
    public CompletableFuture<String> callApiWithTimeout(String url) {
        return CompletableFuture
            .supplyAsync(() -> restTemplate.getForObject(url, String.class))
            .orTimeout(10, TimeUnit.SECONDS)
            .exceptionally(ex -> {
                log.error("API call failed or timed out", ex);
                return "TIMEOUT_FALLBACK";
            });
    }
    
    // 2. Bulkhead Pattern (Limit concurrent calls)
    @Service
    public class BulkheadService {
        
        private final Semaphore semaphore = new Semaphore(10); // Max 10 concurrent
        
        public String callWithBulkhead(String url) {
            if (!semaphore.tryAcquire()) {
                log.warn("Too many concurrent calls, rejecting request");
                throw new BulkheadRejectedException("Service busy");
            }
            
            try {
                return restTemplate.getForObject(url, String.class);
            } finally {
                semaphore.release();
            }
        }
    }
    
    // 3. Queue-based Processing (Don't wait for response)
    @Service
    public class QueueBasedService {
        
        @Autowired
        private KafkaTemplate<String, Request> kafkaTemplate;
        
        public void processAsync(Request request) {
            // Don't wait for response - publish to queue
            kafkaTemplate.send("request-topic", request);
            
            // Return immediately
            return;
        }
        
        @KafkaListener(topics = "request-topic")
        public void processRequest(Request request) {
            // Process in background
            try {
                String result = callExternalApi(request);
                // Store result for later retrieval
                storeResult(request.getId(), result);
            } catch (Exception e) {
                // Handle failure
                storeFailure(request.getId(), e);
            }
        }
    }
}
```

### Strategy 6: Monitoring and Alerting

```java
// ========== MONITORING IMPLEMENTATION ==========

@Component
@Slf4j
public class ApiMonitoringService {
    
    private final MeterRegistry meterRegistry;
    private final Map<String, AtomicInteger> failureCounts = new ConcurrentHashMap<>();
    
    public ApiMonitoringService(MeterRegistry meterRegistry) {
        this.meterRegistry = meterRegistry;
    }
    
    // 1. Track API calls with Micrometer
    public <T> T monitorApiCall(String apiName, Supplier<T> apiCall) {
        Timer.Sample sample = Timer.start(meterRegistry);
        
        try {
            T result = apiCall.get();
            sample.stop(Timer.builder("api.call.duration")
                .tag("api", apiName)
                .tag("status", "success")
                .register(meterRegistry));
            
            // Record success
            Counter.builder("api.call.total")
                .tag("api", apiName)
                .tag("status", "success")
                .register(meterRegistry)
                .increment();
            
            return result;
            
        } catch (Exception e) {
            sample.stop(Timer.builder("api.call.duration")
                .tag("api", apiName)
                .tag("status", "failure")
                .register(meterRegistry));
            
            // Record failure
            Counter.builder("api.call.total")
                .tag("api", apiName)
                .tag("status", "failure")
                .register(meterRegistry)
                .increment();
            
            // Track failure rate
            AtomicInteger failureCount = failureCounts.computeIfAbsent(apiName, 
                k -> new AtomicInteger(0));
            
            int failures = failureCount.incrementAndGet();
            if (failures >= 5) {
                sendAlert(apiName, failures);
                failureCount.set(0);
            }
            
            throw e;
        }
    }
    
    // 2. Health Check Endpoint
    @RestController
    public class HealthController {
        
        @GetMapping("/health/apis")
        public Map<String, HealthStatus> checkApiHealth() {
            Map<String, HealthStatus> statuses = new HashMap<>();
            
            // Check each API
            statuses.put("user-service", checkApi("http://user-service/health"));
            statuses.put("order-service", checkApi("http://order-service/health"));
            statuses.put("payment-service", checkApi("http://payment-service/health"));
            
            return statuses;
        }
        
        private HealthStatus checkApi(String url) {
            try {
                RestTemplate restTemplate = new RestTemplate();
                restTemplate.setRequestFactory(new SimpleClientHttpRequestFactory() {{
                    setConnectTimeout(3000);
                    setReadTimeout(3000);
                }});
                
                ResponseEntity<String> response = restTemplate.getForEntity(url, String.class);
                if (response.getStatusCode().is2xxSuccessful()) {
                    return new HealthStatus("UP", response.getBody());
                }
                return new HealthStatus("DOWN", "HTTP " + response.getStatusCode());
                
            } catch (Exception e) {
                return new HealthStatus("DOWN", e.getMessage());
            }
        }
    }
    
    // 3. Send alerts
    private void sendAlert(String apiName, int failureCount) {
        // Send to alerting system (Slack, PagerDuty, Email)
        String alertMessage = String.format(
            "⚠️ ALERT: API %s has failed %d times in current window",
            apiName, failureCount
        );
        
        log.error(alertMessage);
        
        // Send to Slack webhook
        sendSlackNotification(alertMessage);
        
        // Send to metrics system
        meterRegistry.counter("api.alert", "api", apiName).increment();
    }
}

// 4. Timeout Configuration for Different Scenarios
@Configuration
public class TimeoutStrategyConfig {
    
    // Different timeouts for different API types
    public enum ApiType {
        CRITICAL(30000, 30000),   // 30 seconds for critical
        NORMAL(10000, 15000),      // 10s connect, 15s read
        BACKGROUND(60000, 120000), // 60s connect, 2 min read
        EXTERNAL(5000, 5000);      // 5 seconds for external APIs
        
        private final int connectTimeout;
        private final int readTimeout;
        
        ApiType(int connectTimeout, int readTimeout) {
            this.connectTimeout = connectTimeout;
            this.readTimeout = readTimeout;
        }
        
        public RestTemplate createRestTemplate() {
            HttpComponentsClientHttpRequestFactory factory = 
                new HttpComponentsClientHttpRequestFactory();
            factory.setConnectTimeout(connectTimeout);
            factory.setReadTimeout(readTimeout);
            return new RestTemplate(factory);
        }
    }
    
    @Bean
    public RestTemplate criticalApiRestTemplate() {
        return ApiType.CRITICAL.createRestTemplate();
    }
    
    @Bean
    public RestTemplate externalApiRestTemplate() {
        return ApiType.EXTERNAL.createRestTemplate();
    }
}
```

---

## 4. Complete Working Example {#code-examples}

```java
// ========== COMPLETE PRODUCTION-READY IMPLEMENTATION ==========

@Service
@Slf4j
public class RobustApiClient {
    
    private final RestTemplate restTemplate;
    private final CircuitBreaker circuitBreaker;
    private final CacheManager cacheManager;
    private final MeterRegistry meterRegistry;
    
    public RobustApiClient(RestTemplate restTemplate, 
                           CircuitBreakerRegistry circuitBreakerRegistry,
                           CacheManager cacheManager,
                           MeterRegistry meterRegistry) {
        this.restTemplate = restTemplate;
        this.circuitBreaker = circuitBreakerRegistry.circuitBreaker("api-client");
        this.cacheManager = cacheManager;
        this.meterRegistry = meterRegistry;
    }
    
    public String callApi(String url, String cacheKey) {
        Timer.Sample sample = Timer.start(meterRegistry);
        
        // Try cache first
        Cache cache = cacheManager.getCache("api-responses");
        Cache.ValueWrapper cached = cache.get(cacheKey);
        if (cached != null) {
            log.debug("Returning cached response for: {}", cacheKey);
            return (String) cached.get();
        }
        
        // Circuit breaker pattern
        Supplier<String> decoratedSupplier = Decorators.ofSupplier(() -> {
            try {
                // Set timeout at call level
                return restTemplate.getForObject(url, String.class);
            } catch (ResourceAccessException e) {
                throw new TimeoutException("API timeout");
            }
        }).withCircuitBreaker(circuitBreaker)
          .withFallback(this::getFallbackResponse)
          .decorate();
        
        try {
            String response = decoratedSupplier.get();
            
            // Cache successful response
            cache.put(cacheKey, response);
            
            sample.stop(Timer.builder("api.call")
                .tag("status", "success")
                .register(meterRegistry));
            
            return response;
            
        } catch (Exception e) {
            sample.stop(Timer.builder("api.call")
                .tag("status", "failure")
                .register(meterRegistry));
            
            log.error("API call failed: {}", url, e);
            throw new ApiCallException("Failed to call API", e);
        }
    }
    
    private String getFallbackResponse() {
        log.warn("Circuit breaker open, returning fallback");
        return "{\"status\": \"FALLBACK\", \"message\": \"Service temporarily unavailable\"}";
    }
}

// Controller using the robust client
@RestController
@RequestMapping("/api/data")
public class DataController {
    
    @Autowired
    private RobustApiClient apiClient;
    
    @GetMapping("/{id}")
    public ResponseEntity<?> getData(@PathVariable String id) {
        try {
            String result = apiClient.callApi(
                "http://external-api/data/" + id,
                "data-" + id
            );
            return ResponseEntity.ok(result);
            
        } catch (ApiCallException e) {
            return ResponseEntity.status(HttpStatus.GATEWAY_TIMEOUT)
                .body(new ErrorResponse("API_TIMEOUT", "External API did not respond"));
        }
    }
}

// Configuration for production
@Configuration
@EnableCaching
@EnableRetry
@EnableCircuitBreaker
public class ApiClientConfig {
    
    @Bean
    public RestTemplate restTemplate() {
        RequestConfig config = RequestConfig.custom()
            .setConnectTimeout(5000)
            .setConnectionRequestTimeout(3000)
            .setSocketTimeout(10000)
            .build();
        
        CloseableHttpClient client = HttpClientBuilder.create()
            .setDefaultRequestConfig(config)
            .setMaxConnTotal(100)
            .setMaxConnPerRoute(20)
            .build();
        
        HttpComponentsClientHttpRequestFactory factory = 
            new HttpComponentsClientHttpRequestFactory(client);
        
        return new RestTemplate(factory);
    }
    
    @Bean
    public CacheManager cacheManager() {
        CaffeineCacheManager cacheManager = new CaffeineCacheManager("api-responses");
        cacheManager.setCaffeine(Caffeine.newBuilder()
            .expireAfterWrite(5, TimeUnit.MINUTES)
            .maximumSize(1000));
        return cacheManager;
    }
}
```

---

## 5. Best Practices Summary {#best-practices}

### DO's ✅

| Practice | Implementation |
|----------|----------------|
| **Always set timeouts** | Connect timeout: 5s, Read timeout: 10s |
| **Implement retry with backoff** | Exponential backoff: 1s, 2s, 4s |
| **Use circuit breakers** | Open after 50% failure rate |
| **Cache responses** | TTL: 5 minutes for dynamic data |
| **Fallback mechanisms** | Default values, stale cache, secondary APIs |
| **Monitor everything** | Metrics, logs, alerts |
| **Graceful degradation** | Return partial data if possible |
| **Async processing** | Use queues for non-critical operations |

### DON'Ts ❌

| Practice | Why Not |
|----------|---------|
| **Infinite waiting** | Blocks threads, kills performance |
| **Immediate retry** | Amplifies problem, no recovery time |
| **Ignoring timeouts** | User experience suffers |
| **No fallback** | Complete failure instead of partial |
| **Swallowing exceptions** | Hard to debug, no visibility |
| **Retrying non-idempotent operations** | May cause duplicate data |

### Decision Flow Chart

```
API Call
    │
    ▼
Set Timeout (5-10 seconds)
    │
    ▼
Try API ──────► Success ──► Return Response
    │
    ▼ Timeout/Failure
Retry? (max 3 times)
    │
    ├── Yes ──► Wait (exponential backoff) ──► Try API
    │
    ▼ No
Circuit Breaker Open?
    │
    ├── Yes ──► Return Fallback Immediately
    │
    ▼ No
Check Cache
    │
    ├── Has Cache ──► Return Stale Data
    │
    ▼ No Cache
Graceful Degradation
    │
    ├── Partial Data Available ──► Return Partial
    │
    ▼ No Data
Return Error with HTTP 504/503
```

### Quick Reference - Timeout Values

| Scenario | Connect Timeout | Read Timeout | Retry Count |
|----------|----------------|--------------|-------------|
| Internal API | 3s | 5s | 1-2 |
| External API | 5s | 10s | 2-3 |
| Database | 10s | 30s | 1 |
| File Upload | 30s | 5 min | 0 |
| Batch Processing | 60s | 10 min | 0 |

This comprehensive implementation ensures your application handles non-responsive APIs gracefully, maintaining user experience and system stability.
