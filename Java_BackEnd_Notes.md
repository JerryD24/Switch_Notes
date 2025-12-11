# 📚 Complete Java & Backend Development Guide for Beginners

> A comprehensive guide covering Core Java, Databases, Spring Boot, Microservices, and CI/CD Deployment

---

# Table of Contents

1. [Core Java](#part-1-core-java)
2. [Databases](#part-2-databases)
3. [Spring Boot](#part-3-spring-boot)
4. [Microservices](#part-4-microservices)
5. [Deployment CI/CD](#part-5-deployment-cicd)

---

# 🔷 PART 1: CORE JAVA

---

## 1. Java 8 Features

Java 8 (released March 2014) was a **revolutionary release** that brought functional programming to Java.

### Key Features Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      JAVA 8 FEATURES                            │
├─────────────────────────────────────────────────────────────────┤
│  • Lambda Expressions      • Stream API                         │
│  • Functional Interfaces   • Optional Class                     │
│  • Default Methods         • New Date/Time API (java.time)      │
│  • Method References       • CompletableFuture                  │
└─────────────────────────────────────────────────────────────────┘
```

### Lambda Expressions

```java
// Before Java 8
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello World!");
    }
};

// After Java 8 - Lambda
Runnable r2 = () -> System.out.println("Hello World!");
```

### Optional Class

```java
Optional<String> optional = Optional.ofNullable(getName());
String name = optional.orElse("Default Name");
String name2 = optional.orElseThrow(() -> new RuntimeException("Name not found"));
```

### Default Methods in Interfaces

```java
interface Vehicle {
    void start();
    
    default void honk() {
        System.out.println("Beep beep!");
    }
}
```

### New Date/Time API

```java
LocalDate date = LocalDate.now();
LocalTime time = LocalTime.now();
LocalDateTime dateTime = LocalDateTime.now();
ZonedDateTime zonedDateTime = ZonedDateTime.now();

// Immutable and thread-safe!
LocalDate tomorrow = date.plusDays(1);
```

---

## 2. Stream API

The **Stream API** provides a functional approach to processing collections.

### Stream Pipeline

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         STREAM PIPELINE                                  │
│                                                                          │
│   ┌──────────┐    ┌─────────────────────┐    ┌─────────────────────┐   │
│   │  SOURCE  │───▶│ INTERMEDIATE OPS    │───▶│   TERMINAL OP       │   │
│   │          │    │ (Lazy Evaluation)   │    │ (Triggers Pipeline) │   │
│   └──────────┘    └─────────────────────┘    └─────────────────────┘   │
│                                                                          │
│   Collection      filter(), map(),            collect(), forEach(),     │
│   Array           sorted(), distinct(),       reduce(), count(),        │
│   I/O Channel     limit(), skip(),           findFirst(), anyMatch()    │
│   Generator       flatMap(), peek()                                      │
└─────────────────────────────────────────────────────────────────────────┘
```

### Common Stream Operations

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Filter - Select elements matching condition
List<Integer> evenNumbers = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList()); // [2, 4, 6, 8, 10]

// Map - Transform elements
List<Integer> squared = numbers.stream()
    .map(n -> n * n)
    .collect(Collectors.toList()); // [1, 4, 9, 16, 25, ...]

// Reduce - Combine elements
int sum = numbers.stream()
    .reduce(0, (a, b) -> a + b); // 55

// FlatMap - Flatten nested structures
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4)
);
List<Integer> flat = nested.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList()); // [1, 2, 3, 4]
```

### Collectors Examples

```java
// Group by
Map<String, List<Person>> byCity = persons.stream()
    .collect(Collectors.groupingBy(Person::getCity));

// Partition by (true/false groups)
Map<Boolean, List<Person>> adults = persons.stream()
    .collect(Collectors.partitioningBy(p -> p.getAge() >= 18));

// Joining strings
String names = persons.stream()
    .map(Person::getName)
    .collect(Collectors.joining(", ")); // "John, Jane, Bob"
```

---

## 3. Lambda Expressions and Functional Interfaces

### Lambda Syntax

```
┌───────────────────────────────────────────────────────────────────┐
│                    LAMBDA EXPRESSION SYNTAX                        │
│                                                                    │
│     (parameters) -> expression                                     │
│            OR                                                      │
│     (parameters) -> { statements; }                                │
│                                                                    │
│  Examples:                                                         │
│  () -> 42                        // No parameter, returns 42       │
│  x -> x * x                      // Single parameter               │
│  (x, y) -> x + y                 // Multiple parameters            │
│  (String s) -> s.length()        // Explicit type                  │
└───────────────────────────────────────────────────────────────────┘
```

### Built-in Functional Interfaces

| Interface | Method | Description |
|-----------|--------|-------------|
| `Predicate<T>` | `test(T t)` | Takes T, returns boolean |
| `Function<T,R>` | `apply(T t)` | Takes T, returns R |
| `Consumer<T>` | `accept(T t)` | Takes T, returns nothing |
| `Supplier<T>` | `get()` | Takes nothing, returns T |
| `BiFunction<T,U,R>` | `apply(T t, U u)` | Takes T and U, returns R |
| `UnaryOperator<T>` | `apply(T t)` | Takes T, returns T |
| `BinaryOperator<T>` | `apply(T t1, T t2)` | Takes 2 T, returns T |

### Examples

```java
// Predicate
Predicate<String> isEmpty = s -> s.isEmpty();
System.out.println(isEmpty.test("")); // true

// Function
Function<String, Integer> length = s -> s.length();
System.out.println(length.apply("Hello")); // 5

// Consumer
Consumer<String> printer = s -> System.out.println(s);
printer.accept("Hello World!");

// Supplier
Supplier<Double> random = () -> Math.random();
```

### Method References

| Type | Syntax | Lambda Equivalent |
|------|--------|------------------|
| Static method | `Class::staticMethod` | `x -> Class.method(x)` |
| Instance method (obj) | `object::instanceMethod` | `x -> obj.method(x)` |
| Instance method (class) | `Class::instanceMethod` | `(x,y) -> x.method(y)` |
| Constructor | `Class::new` | `x -> new Class(x)` |

---

## 4. Multithreading and Lock Strategies

### Thread Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        THREAD LIFECYCLE                                  │
│                                                                          │
│                          ┌─────────┐                                    │
│                          │   NEW   │                                    │
│                          └────┬────┘                                    │
│                               │ start()                                  │
│                               ▼                                          │
│                          ┌─────────┐                                    │
│                   ┌──────│ RUNNABLE│◄────────┐                          │
│                   │      └────┬────┘         │                          │
│          sleep()/ │           │              │ notify()/                 │
│          wait()   │           │ run()        │ I/O completes             │
│                   │           │ completes    │                           │
│                   │           ▼              │                           │
│                   │      ┌─────────┐    ┌────┴─────┐                    │
│                   └─────▶│ BLOCKED │    │ WAITING  │                    │
│                          └─────────┘    └──────────┘                    │
│                               │                                          │
│                               ▼                                          │
│                          ┌─────────┐                                    │
│                          │TERMINATED│                                   │
│                          └─────────┘                                    │
└─────────────────────────────────────────────────────────────────────────┘
```

### Creating Threads

```java
// Method 1: Extending Thread class
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + getName());
    }
}

// Method 2: Implementing Runnable
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable running");
    }
}

// Method 3: Lambda (Java 8+)
Thread t3 = new Thread(() -> System.out.println("Lambda thread"));

// Method 4: Callable (returns value)
Callable<Integer> callable = () -> 42;
```

### Lock Strategies Comparison

| Feature | synchronized | ReentrantLock |
|---------|-------------|---------------|
| Lock acquisition | Implicit | Explicit (lock/unlock) |
| Fairness | Not guaranteed | Can be fair or unfair |
| Try lock | Not possible | `tryLock()` available |
| Timeout | Not possible | `tryLock(timeout)` available |
| Interruptible | No | `lockInterruptibly()` |
| Multiple conditions | Single (wait/notify) | Multiple Condition objects |

```java
// synchronized keyword
public synchronized void syncMethod() {
    // Only one thread can execute this at a time
}

// ReentrantLock
ReentrantLock lock = new ReentrantLock();
public void lockMethod() {
    lock.lock();
    try {
        // Critical section
    } finally {
        lock.unlock();
    }
}

// ReadWriteLock - Multiple readers, single writer
ReadWriteLock rwLock = new ReentrantReadWriteLock();
public void readData() {
    rwLock.readLock().lock();
    try {
        // Multiple threads can read simultaneously
    } finally {
        rwLock.readLock().unlock();
    }
}
```

---

## 5. String Class

### String Characteristics

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         STRING IN JAVA                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  Key Properties:                                                         │
│  • IMMUTABLE - Once created, cannot be modified                         │
│  • Stored in String Pool (for memory optimization)                      │
│  • Thread-safe (due to immutability)                                    │
│  • Implements Serializable, Comparable, CharSequence                    │
└─────────────────────────────────────────────────────────────────────────┘
```

### String Pool Memory Model

```
┌─────────────────────────────────────────────────────────────────┐
│                        HEAP MEMORY                               │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              STRING POOL (Special area)                    │  │
│  │    ┌─────────┐  ┌─────────┐  ┌─────────┐                 │  │
│  │    │ "Hello" │  │ "World" │  │ "Java"  │                 │  │
│  │    └─────────┘  └─────────┘  └─────────┘                 │  │
│  │         ▲                                                  │  │
│  │    s1 ──┘                                                  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│    s3 ──▶ ┌─────────────┐  (Outside pool - new String())       │
│           │   "Hello"   │                                       │
│           └─────────────┘                                       │
└─────────────────────────────────────────────────────────────────┘
```

### String vs StringBuilder vs StringBuffer

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| Mutability | Immutable | Mutable | Mutable |
| Thread Safety | Yes | No | Yes (synchronized) |
| Performance | Slow | Fast | Slower than StringBuilder |
| Storage | String Pool | Heap | Heap |
| When to use | Few changes | Single thread, many changes | Multi-threaded, many changes |

### Important String Methods

```java
String str = "Hello World";

str.length();                    // 11
str.charAt(0);                   // 'H'
str.substring(0, 5);             // "Hello"
str.indexOf("World");            // 6
str.contains("World");           // true
str.toUpperCase();               // "HELLO WORLD"
str.toLowerCase();               // "hello world"
str.trim();                      // Remove whitespace
str.replace("World", "Java");    // "Hello Java"
str.split(" ");                  // ["Hello", "World"]
String.join("-", "a", "b", "c"); // "a-b-c"
```

---

## 6. Static Keyword

### Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         STATIC KEYWORD                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  "static" means belonging to the CLASS, not to any instance             │
│                                                                          │
│  Can be applied to:                                                      │
│    1. Variables (Class Variables)                                        │
│    2. Methods (Class Methods)                                            │
│    3. Blocks (Static Initializers)                                       │
│    4. Nested Classes (Static Nested Classes)                             │
│    5. Imports (Static Import)                                            │
└─────────────────────────────────────────────────────────────────────────┘
```

### Initialization Order

```
When class is loaded:
  1. Static variables initialized (in order of appearance)
  2. Static blocks executed (in order of appearance)

When object is created:
  3. Instance variables initialized
  4. Instance blocks executed
  5. Constructor executed
```

### Static Block Example

```java
public class DatabaseConfig {
    private static Connection connection;
    
    // Static block - Executes once when class is loaded
    static {
        System.out.println("Static block executing...");
        try {
            connection = DriverManager.getConnection(url);
        } catch (SQLException e) {
            throw new RuntimeException("Failed to connect", e);
        }
    }
}
```

---

## 7. Global and Instance Variables

### Variable Types

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       VARIABLE TYPES IN JAVA                             │
├─────────────────────────────────────────────────────────────────────────┤
│  CLASS/STATIC VARIABLES                                                  │
│  • Declared with 'static' keyword                                        │
│  • Shared across all instances                                           │
│  • Stored in Method Area (Metaspace)                                    │
│                                                                          │
│  INSTANCE VARIABLES                                                      │
│  • Declared without 'static'                                             │
│  • Each object has its own copy                                         │
│  • Stored in Heap (with object)                                         │
│                                                                          │
│  LOCAL VARIABLES                                                         │
│  • Declared inside methods, constructors, or blocks                     │
│  • Stored in Stack                                                       │
│  • Must be initialized before use                                        │
└─────────────────────────────────────────────────────────────────────────┘
```

### Default Values

| Variable Type | Default Value |
|--------------|---------------|
| int, short, byte | 0 |
| long | 0L |
| float | 0.0f |
| double | 0.0d |
| char | '\u0000' |
| boolean | false |
| Object references | null |
| Local variables | NO DEFAULT (must init) |

---

## 8. Java Memory Model

### JVM Memory Structure

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        JVM MEMORY STRUCTURE                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  METHOD AREA (Metaspace)                                                │
│  • Class metadata, static variables, method bytecode                    │
│  • Shared among all threads                                             │
│                                                                          │
│  HEAP                                                                    │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  YOUNG GENERATION           │      OLD GENERATION               │   │
│  │  ┌─────┐ ┌────────┐        │      (Tenured)                     │   │
│  │  │Eden │ │Survivor│        │      Long-lived objects            │   │
│  │  │     │ │ S0  S1 │        │                                     │   │
│  │  └─────┘ └────────┘        │                                     │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│  • Stores all objects                                                   │
│  • Shared among all threads                                             │
│                                                                          │
│  STACK (One per thread)                                                 │
│  • Method frames, local variables, operand stack                        │
│  • Thread-specific                                                       │
│                                                                          │
│  PC REGISTERS (One per thread)                                          │
│  • Points to current instruction                                        │
│                                                                          │
│  NATIVE METHOD STACK (One per thread)                                   │
│  • For native method calls                                              │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### Garbage Collection

```
YOUNG GENERATION GC (Minor GC - Fast, Frequent)
═══════════════════════════════════════════════
1. New objects created in EDEN
2. When Eden fills up → Minor GC triggers
3. Live objects move to Survivor space (S0 or S1)
4. Objects surviving multiple Minor GCs → move to Old Generation

OLD GENERATION GC (Major GC / Full GC - Slow, Infrequent)
════════════════════════════════════════════════════════
When Old Generation fills up → Major GC triggers
This is "Stop-the-World" event (all threads paused)
```

### GC Algorithms

| Algorithm | Description | Use Case |
|-----------|-------------|----------|
| Serial GC | Single threaded | Small applications |
| Parallel GC | Multiple threads | Default for Java 8 |
| G1 GC | Divides heap into regions | Default for Java 9+ |
| ZGC | Very low latency (<10ms) | Java 11+, large heaps |

---

## 9. Collections Framework

### Collections Hierarchy

```
                          Iterable
                             │
                             ▼
                        Collection
                    ┌───────┼───────┐
                    ▼       ▼       ▼
                  List    Set    Queue
                    │       │       │
        ┌──────┬───┴───┐   │   ┌───┴───┬─────────┐
        ▼      ▼       ▼   │   ▼       ▼         ▼
   ArrayList LinkedList    │ PriorityQueue    Deque
     Vector                │              ArrayDeque
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
               HashSet          SortedSet
             LinkedHashSet          │
                                TreeSet

                           Map (separate)
                  ┌───────────┼───────────┐
                  ▼           ▼           ▼
              HashMap    SortedMap   Hashtable
           LinkedHashMap     │
        ConcurrentHashMap  TreeMap
```

### List Implementations Comparison

| Operation | ArrayList | LinkedList | Vector |
|-----------|-----------|------------|--------|
| Get by index | O(1) | O(n) | O(1) |
| Add at end | O(1)* | O(1) | O(1)* |
| Add at beginning | O(n) | O(1) | O(n) |
| Remove by index | O(n) | O(n) | O(n) |
| Thread Safe | No | No | Yes |

### HashMap Internal Structure

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      HASHMAP INTERNAL STRUCTURE                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  HashMap stores data in an array of "buckets"                           │
│                                                                          │
│  Index │  Bucket (Node/Entry)                                           │
│  ──────┼──────────────────────────────────────────────────────────      │
│    0   │  null                                                          │
│    1   │  [Key1:Val1] → [Key5:Val5] → null (LinkedList/Tree)           │
│    2   │  [Key2:Val2] → null                                            │
│    4   │  [Key3:Val3] → [Key6:Val6] → [Key9:Val9] → null               │
│                                                                          │
│  How index is calculated:                                                │
│  index = hashCode(key) & (n-1)  // n = array length                     │
│                                                                          │
│  Java 8 Optimization:                                                    │
│  When bucket has > 8 entries → LinkedList converts to Red-Black Tree    │
│  When bucket has < 6 entries → Tree converts back to LinkedList         │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### HashMap Types Comparison

| Feature | HashMap | LinkedHashMap | ConcurrentHashMap |
|---------|---------|---------------|-------------------|
| Order | No guarantee | Insertion order | No guarantee |
| Null keys | 1 allowed | 1 allowed | Not allowed |
| Null values | Allowed | Allowed | Not allowed |
| Thread-safe | No | No | Yes |

### Using Custom Objects as Keys

```java
// IMPORTANT: Must override both equals() and hashCode()

public class Employee {
    private int id;
    private String name;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return id == employee.id && Objects.equals(name, employee.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
}

// Contract:
// 1. If equals() returns true, hashCode() MUST return same value
// 2. If hashCode() is same, equals() may or may not be true
// 3. Key objects should be IMMUTABLE to avoid issues
```

---

## 10. Design Patterns

### Categories

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      DESIGN PATTERNS CATEGORIES                          │
├─────────────────────────────────────────────────────────────────────────┤
│  CREATIONAL PATTERNS (Object creation)                                  │
│  • Singleton, Factory, Builder, Prototype, Abstract Factory             │
│                                                                          │
│  STRUCTURAL PATTERNS (Class/Object composition)                         │
│  • Adapter, Decorator, Facade, Proxy, Composite                         │
│                                                                          │
│  BEHAVIORAL PATTERNS (Object communication)                             │
│  • Observer, Strategy, Template, Command, State                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Singleton Pattern

```java
// 1. Eager Initialization
public class EagerSingleton {
    private static final EagerSingleton INSTANCE = new EagerSingleton();
    private EagerSingleton() {}
    public static EagerSingleton getInstance() { return INSTANCE; }
}

// 2. Double-Check Locking
public class LazySingleton {
    private static volatile LazySingleton instance;
    private LazySingleton() {}
    public static LazySingleton getInstance() {
        if (instance == null) {
            synchronized (LazySingleton.class) {
                if (instance == null) {
                    instance = new LazySingleton();
                }
            }
        }
        return instance;
    }
}

// 3. Bill Pugh Singleton (Recommended)
public class BillPughSingleton {
    private BillPughSingleton() {}
    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }
    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}

// 4. Enum Singleton (Best)
public enum EnumSingleton {
    INSTANCE;
    public void doSomething() { }
}
```

### Factory Pattern

```java
interface Vehicle { void drive(); }

class Car implements Vehicle {
    public void drive() { System.out.println("Driving car"); }
}

class Bike implements Vehicle {
    public void drive() { System.out.println("Riding bike"); }
}

class VehicleFactory {
    public static Vehicle createVehicle(String type) {
        return switch (type.toLowerCase()) {
            case "car" -> new Car();
            case "bike" -> new Bike();
            default -> throw new IllegalArgumentException("Unknown: " + type);
        };
    }
}
```

### Observer Pattern

```java
interface Observer {
    void update(String message);
}

class NewsAgency {
    private List<Observer> observers = new ArrayList<>();
    private String news;
    
    public void addObserver(Observer observer) { observers.add(observer); }
    public void removeObserver(Observer observer) { observers.remove(observer); }
    
    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }
    
    private void notifyObservers() {
        observers.forEach(o -> o.update(news));
    }
}
```

---

## 11. SOLID Principles

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         SOLID PRINCIPLES                                 │
├─────────────────────────────────────────────────────────────────────────┤
│  S - Single Responsibility: A class should have only ONE reason to     │
│      change                                                              │
│                                                                          │
│  O - Open/Closed: Open for extension, closed for modification          │
│                                                                          │
│  L - Liskov Substitution: Subtypes must be substitutable for base      │
│      types                                                               │
│                                                                          │
│  I - Interface Segregation: Many specific interfaces > one general     │
│      interface                                                           │
│                                                                          │
│  D - Dependency Inversion: Depend on abstractions, not concretions     │
└─────────────────────────────────────────────────────────────────────────┘
```

### Single Responsibility Principle

```java
// BAD - Multiple responsibilities
class Employee {
    void calculateSalary() { }
    void saveToDatabase() { }
    void generateReport() { }
}

// GOOD - Single responsibility each
class Employee { /* Only employee data */ }
class SalaryCalculator { double calculate(Employee emp) { } }
class EmployeeRepository { void save(Employee emp) { } }
class ReportGenerator { void generate(Employee emp) { } }
```

### Open/Closed Principle

```java
// GOOD - Open for extension, closed for modification
interface Shape { double calculateArea(); }

class Rectangle implements Shape {
    double width, height;
    public double calculateArea() { return width * height; }
}

class Circle implements Shape {
    double radius;
    public double calculateArea() { return Math.PI * radius * radius; }
}
// New shapes can be added without modifying existing code
```

### Dependency Inversion Principle

```java
// BAD
class OrderService {
    private MySQLDatabase database = new MySQLDatabase();
}

// GOOD
interface Database { void save(Object obj); }

class OrderService {
    private Database database;
    public OrderService(Database database) { this.database = database; }
}
```

---

## 12. Java Version Differences

### Key Features by Version

| Version | Year | Key Features |
|---------|------|-------------|
| Java 8 | 2014 | Lambda, Streams, Optional, Date/Time API ★ LTS |
| Java 9 | 2017 | Modules (Jigsaw), JShell |
| Java 10 | 2018 | Local variable type inference (var) |
| Java 11 | 2018 | HTTP Client, String methods ★ LTS |
| Java 14 | 2020 | Records (preview), Pattern matching instanceof |
| Java 16 | 2021 | Records (final), Pattern matching (final) |
| Java 17 | 2021 | Sealed classes, Pattern matching switch ★ LTS |
| Java 21 | 2023 | Virtual threads, Pattern matching ★ LTS |

### Feature Comparison

```java
// var (Java 10+)
var map = new HashMap<String, List<String>>();

// Switch expressions (Java 14+)
String result = switch (day) {
    case "MON", "TUE" -> "Weekday";
    case "SAT", "SUN" -> "Weekend";
    default -> "Unknown";
};

// Text blocks (Java 15+)
String json = """
              {
                "name": "John",
                "age": 30
              }
              """;

// Records (Java 16+)
public record Person(String name, int age) {}

// Pattern matching instanceof (Java 16+)
if (obj instanceof String s) {
    System.out.println(s.length());
}

// Virtual threads (Java 21+)
Thread vThread = Thread.ofVirtual().start(() -> System.out.println("Hello"));
```

---

## 13. Abstract Class vs Functional Interfaces

| Feature | Abstract Class | Interface | Functional Interface |
|---------|---------------|-----------|---------------------|
| Abstract methods | 0 or more | 0 or more | Exactly 1 |
| Concrete methods | Yes | default only | default only |
| Constructors | Yes | No | No |
| Instance variables | Yes | No (constants) | No |
| Multiple inheritance | No | Yes | Yes |
| Lambda compatible | No | No | Yes ✓ |

### When to Use

- **Abstract Class**: IS-A relationship with shared code
- **Interface**: CAN-DO capability
- **Functional Interface**: Single behavior, lambda target

---

## 14. Try-Catch-Finally

### Exception Hierarchy

```
                           Throwable
                    ┌──────────┴──────────┐
                    ▼                     ▼
                 Error                Exception
                   │               ┌──────┴──────┐
                   │               ▼             ▼
          OutOfMemoryError   RuntimeException  Checked Exceptions
          StackOverflowError      │                │
                            NullPointer      IOException
                            ArrayIndexOOB    SQLException
```

### Try-Catch-Finally Examples

```java
// Basic try-catch-finally
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero!");
} finally {
    System.out.println("Always runs");
}

// Multi-catch (Java 7+)
try {
    // code
} catch (IOException | SQLException e) {
    System.out.println("I/O or SQL error: " + e.getMessage());
}

// Try-with-resources (Java 7+)
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line = reader.readLine();
} catch (IOException e) {
    e.printStackTrace();
}
// reader is automatically closed!
```

### Tricky Return Behavior

```java
public int trickyReturn() {
    try {
        return 1;
    } finally {
        return 2;  // WARNING: Overrides try's return!
    }
}
// Returns 2, NOT 1!
```

---

## 15. Main Function

### Valid Signatures

```java
// All valid
public static void main(String[] args) { }
public static void main(String... args) { }
public static void main(String args[]) { }
public final static void main(String[] args) { }
static public void main(String[] args) { }

// Invalid
static void main(String[] args) { }        // Missing public
public void main(String[] args) { }        // Missing static
public static int main(String[] args) { }  // Wrong return type
```

### Interview Questions

```java
// Can we overload main? YES
public static void main(String[] args) { main(10); }
public static void main(int num) { }

// Can we override main? NO (static methods can't be overridden)

// Can main throw exception? YES
public static void main(String[] args) throws Exception { }
```

---

## 16. Operator Precedence

| Precedence | Operator | Description |
|------------|----------|-------------|
| 1 | () [] . | Postfix |
| 2 | ++ -- (postfix) | Unary postfix |
| 3 | ++ -- + - ! ~ | Unary prefix |
| 4 | * / % | Multiplicative |
| 5 | + - | Additive |
| 6 | << >> >>> | Shift |
| 7 | < <= > >= instanceof | Relational |
| 8 | == != | Equality |
| 9-11 | & ^ \| | Bitwise |
| 12-13 | && \|\| | Logical |
| 14 | ?: | Ternary |
| 15 | = += -= etc. | Assignment |

### Tricky Examples

```java
int i = 0;
int j = i++ + i++ + ++i;  // j = 0 + 1 + 3 = 4

System.out.println(1 + 2 + "3");   // "33"
System.out.println("1" + 2 + 3);   // "123"
```

---

## 17. Constructor Chaining

```java
public class Employee {
    private String name;
    private int age;
    private String department;
    
    // Full constructor
    public Employee(String name, int age, String department) {
        this.name = name;
        this.age = age;
        this.department = department;
    }
    
    // Chain with default department
    public Employee(String name, int age) {
        this(name, age, "General");
    }
    
    // Chain with default age
    public Employee(String name) {
        this(name, 25);
    }
    
    // Default constructor
    public Employee() {
        this("Unknown");
    }
}

// Parent-child chaining with super()
class Animal {
    public Animal(String name) { }
}

class Dog extends Animal {
    public Dog(String name) {
        super(name);  // Must be first statement
    }
}
```

---

# 🔷 PART 2: DATABASES

---

## 1. Basic Concepts

### Key Concepts

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         KEY CONCEPTS                                     │
├─────────────────────────────────────────────────────────────────────────┤
│  KEYS:                                                                   │
│  • Primary Key (PK) - Unique identifier for each row                    │
│  • Foreign Key (FK) - Reference to PK in another table                  │
│  • Composite Key - PK made of multiple columns                          │
│  • Unique Key - Unique but can have NULL                                │
│                                                                          │
│  ACID PROPERTIES (Transactions):                                        │
│  • Atomicity - All or nothing                                           │
│  • Consistency - Valid state before and after                           │
│  • Isolation - Concurrent transactions don't interfere                  │
│  • Durability - Committed data survives failures                        │
└─────────────────────────────────────────────────────────────────────────┘
```

### SQL Command Types

| Type | Commands | Purpose |
|------|----------|---------|
| DDL | CREATE, ALTER, DROP, TRUNCATE | Structure |
| DML | SELECT, INSERT, UPDATE, DELETE | Data |
| DCL | GRANT, REVOKE | Permissions |
| TCL | COMMIT, ROLLBACK, SAVEPOINT | Transactions |

### Joins

```
INNER JOIN  - Only matching rows
LEFT JOIN   - All from left + matching from right
RIGHT JOIN  - All from right + matching from left
FULL OUTER JOIN - All from both
CROSS JOIN  - Cartesian product
```

---

## 2. Normalization

### Normal Forms

```
1NF (First Normal Form):
• Each column contains atomic values
• No repeating groups

2NF (Second Normal Form):
• Must be in 1NF
• No partial dependencies (all non-key columns depend on FULL PK)

3NF (Third Normal Form):
• Must be in 2NF
• No transitive dependencies (non-key → non-key)

BCNF (Boyce-Codd Normal Form):
• For every dependency X → Y, X must be a superkey
```

---

## 3. Query Problems

### Aggregation Functions

```sql
SELECT 
    department,
    COUNT(*) as emp_count,
    SUM(salary) as total_salary,
    AVG(salary) as avg_salary,
    MIN(salary) as min_salary,
    MAX(salary) as max_salary
FROM employees
GROUP BY department
HAVING COUNT(*) >= 5
ORDER BY avg_salary DESC;
```

### Ranking Functions

```sql
-- ROW_NUMBER - Unique sequential number
SELECT name, salary, ROW_NUMBER() OVER (ORDER BY salary DESC) as row_num
FROM employees;

-- RANK - Same rank for ties, gaps after
SELECT name, salary, RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- DENSE_RANK - Same rank for ties, no gaps
SELECT name, salary, DENSE_RANK() OVER (ORDER BY salary DESC) as dense_rank
FROM employees;
```

### Common Query Problems

```sql
-- Find second highest salary
SELECT MAX(salary) FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Find duplicates
SELECT email, COUNT(*) FROM employees 
GROUP BY email HAVING COUNT(*) > 1;

-- Find employees with above-average salary
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

## 4. Connection Pool

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      CONNECTION POOLING                                  │
├─────────────────────────────────────────────────────────────────────────┤
│  WITHOUT Pool: Create → Use → Close (expensive, 100-500ms each)        │
│                                                                          │
│  WITH Pool: Borrow → Use → Return (connections reused)                  │
│                                                                          │
│  Popular Libraries:                                                      │
│  • HikariCP (Fastest, Spring Boot default)                              │
│  • Apache DBCP2                                                          │
│  • C3P0                                                                  │
└─────────────────────────────────────────────────────────────────────────┘
```

### HikariCP Configuration

```yaml
spring:
  datasource:
    hikari:
      minimum-idle: 5
      maximum-pool-size: 20
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
```

---

## 5. Database Optimization

### Indexing

```
When to Index:
✓ Columns in WHERE clauses
✓ Columns in JOIN conditions
✓ Columns in ORDER BY
✓ Foreign key columns

When NOT to Index:
✗ Small tables
✗ Columns with low selectivity
✗ Frequently updated columns
```

### Query Optimization

```sql
-- Select only needed columns
SELECT id, name, email FROM employees WHERE id = 1;

-- Use appropriate WHERE conditions
-- BAD (function on column)
SELECT * FROM employees WHERE YEAR(hire_date) = 2023;
-- GOOD
SELECT * FROM employees 
WHERE hire_date >= '2023-01-01' AND hire_date < '2024-01-01';

-- Use EXISTS instead of IN for large subqueries
SELECT * FROM orders o WHERE EXISTS 
    (SELECT 1 FROM customers c WHERE c.id = o.customer_id AND c.status = 'active');
```

---

# 🔷 PART 3: SPRING BOOT

---

## 1. Basic Concepts

### Dependency Injection Types

```java
// 1. Constructor Injection (Recommended)
@Service
public class OrderService {
    private final PaymentService paymentService;
    
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

// 2. Setter Injection
@Service
public class OrderService {
    private PaymentService paymentService;
    
    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

// 3. Field Injection (Not Recommended)
@Service
public class OrderService {
    @Autowired
    private PaymentService paymentService;
}
```

---

## 2. Stereotype Annotations

```
                        @Component
                   (Generic Spring bean)
                            │
         ┌──────────────────┼──────────────────┐
         │                  │                  │
         ▼                  ▼                  ▼
   @Controller         @Service          @Repository
  (Web layer)      (Service layer)     (Data layer)

@RestController = @Controller + @ResponseBody
```

---

## 3. Bean Life Cycle and Scopes

### Lifecycle

```
1. Instantiation → 2. Populate Properties → 3. BeanNameAware →
4. BeanFactoryAware → 5. ApplicationContextAware → 
6. PreInitialization → 7. @PostConstruct → 8. PostInitialization →
9. Bean Ready → 10. @PreDestroy (on shutdown)
```

### Scopes

| Scope | Description |
|-------|-------------|
| singleton (default) | One instance per Spring container |
| prototype | New instance each time requested |
| request | One per HTTP request (web only) |
| session | One per HTTP session (web only) |
| application | One per ServletContext (web only) |

---

## 4. Configuration

```yaml
# application.yml
server:
  port: 8080

spring:
  profiles:
    active: dev
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
```

```java
// Reading properties
@Value("${app.name}")
private String appName;

// Type-safe configuration
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private int timeout;
    // getters/setters
}
```

---

## 5. REST API Concepts

### HTTP Methods

| Method | Annotation | Purpose | Idempotent |
|--------|------------|---------|------------|
| GET | @GetMapping | Retrieve | Yes |
| POST | @PostMapping | Create | No |
| PUT | @PutMapping | Replace | Yes |
| PATCH | @PatchMapping | Partial update | No |
| DELETE | @DeleteMapping | Delete | Yes |

### Controller Example

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {
    
    @GetMapping
    public List<User> getAllUsers() { }
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) { }
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody @Valid CreateUserRequest request) {
        User created = userService.create(request);
        return ResponseEntity.created(URI.create("/api/v1/users/" + created.getId()))
                           .body(created);
    }
    
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody UpdateUserRequest request) { }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

### PUT vs POST

```
POST (Create):
• Create new resource
• Server assigns ID
• Not idempotent
• POST /api/users → Creates user, returns ID

PUT (Replace):
• Replace entire resource
• Client specifies ID
• Idempotent
• PUT /api/users/123 → Replaces user 123 completely
```

---

## 6. Exception Handling

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            LocalDateTime.now()
        );
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        // Handle validation errors
    }
}
```

---

## 7. JUnit/Mockito

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    
    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void shouldReturnUserWhenFound() {
        // Arrange
        when(userRepository.findById(1L)).thenReturn(Optional.of(mockUser));
        
        // Act
        User result = userService.findById(1L);
        
        // Assert
        assertNotNull(result);
        verify(userRepository, times(1)).findById(1L);
    }
}
```

---

## 8. Spring Security (JWT)

```
JWT AUTHENTICATION FLOW:
═══════════════════════

1. Client: POST /api/auth/login { username, password }
2. Server: Validate → Generate JWT → Return { accessToken }
3. Client: GET /api/protected, Authorization: Bearer <token>
4. Server: Validate JWT → Return data
```

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }
}
```

---

## 9-12. Repository, Maven/Gradle, Database, Kafka

### Repository

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    List<User> findByStatus(String status);
    
    @Query("SELECT u FROM User u WHERE u.department = :dept")
    List<User> findByDepartment(@Param("dept") String department);
}
```

### Kafka

```java
// Producer
@Service
public class OrderEventProducer {
    @Autowired
    private KafkaTemplate<String, Object> kafkaTemplate;
    
    public void sendOrderEvent(OrderEvent event) {
        kafkaTemplate.send("order-events", event.getOrderId(), event);
    }
}

// Consumer
@Service
public class OrderEventConsumer {
    @KafkaListener(topics = "order-events", groupId = "order-service")
    public void consume(OrderEvent event) {
        // Process event
    }
}
```

---

# 🔷 PART 4: MICROSERVICES

---

## 1. Design Patterns

```
┌─────────────────────────────────────────────────────────────────────────┐
│                  MICROSERVICES DESIGN PATTERNS                           │
├─────────────────────────────────────────────────────────────────────────┤
│  1. API Gateway - Single entry point for all clients                    │
│  2. Service Discovery - Dynamic service registration                    │
│  3. Circuit Breaker - Prevents cascading failures                       │
│  4. Saga Pattern - Distributed transactions                             │
│  5. CQRS - Separate read and write operations                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Inter-Service Communication

```
SYNCHRONOUS:
• REST/HTTP - Simple, widely used
• gRPC - Binary, faster, strong typing

ASYNCHRONOUS:
• Message Queue (Kafka, RabbitMQ) - Loose coupling, resilient
```

---

## 3. Monolithic vs Microservices

| Aspect | Monolithic | Microservices |
|--------|-----------|---------------|
| Deployment | All or nothing | Independent per service |
| Scaling | Scale entire app | Scale specific services |
| Technology | Single stack | Polyglot |
| Data management | Single database | Database per service |
| Failure impact | Entire app down | Partial degradation |

---

## 4. Resiliency Patterns

```java
// Circuit Breaker
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallback")
public Payment processPayment(PaymentRequest request) {
    return paymentClient.process(request);
}

// Retry
@Retry(name = "paymentService", fallbackMethod = "fallback")
public Payment processPayment(PaymentRequest request) { }

// Rate Limiter
@RateLimiter(name = "apiLimit")
public Response callApi() { }
```

---

## 5-6. Deployment Strategies

```
1. ROLLING UPDATE - Gradual replacement
   v1 → v1 → v1 → v1 → v1
   v1 → v1 → v2 → v2 → v2

2. BLUE-GREEN - Instant switch between environments
   Blue (v1 ACTIVE) ←→ Green (v2 IDLE)

3. CANARY - Gradual traffic shift
   90% → v1, 10% → v2 (canary)
   Then increase: 25% → 50% → 100%
```

---

## 7-10. Service Discovery, Load Balancing, API Gateway

```
Client → API Gateway → Service Discovery → Load Balancer → Service Instance

API Gateway responsibilities:
• Authentication/Authorization
• Rate Limiting
• Request/Response transformation
• Logging/Monitoring
• SSL Termination
```

---

## 11. HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Unprocessable Entity |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

---

# 🔷 PART 5: DEPLOYMENT CI/CD

---

## 1. Docker and Kubernetes

### Docker

```dockerfile
FROM openjdk:17
COPY target/app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
docker build -t myapp:1.0 .
docker run -p 8080:8080 myapp:1.0
docker push myregistry/myapp:1.0
```

### Kubernetes

```
Container = Single running process
Pod = Smallest deployable unit (1+ containers)
Deployment = Manages ReplicaSets, rolling updates
Service = Stable network endpoint
ConfigMap = Configuration data
Secret = Sensitive data (base64 encoded)
Ingress = External HTTP routing
```

---

## 2. Pipeline Stages

```
SOURCE → BUILD → TEST → ANALYZE → PACKAGE → DEPLOY

1. Source: Git clone/checkout
2. Build: Compile, resolve dependencies
3. Test: Unit tests, integration tests
4. Analyze: SonarQube, Checkmarx, Prisma
5. Package: Build JAR, Docker image
6. Deploy: DEV → QA → UAT → PROD
```

---

## 3. Security Scanning

| Tool | Type | Purpose |
|------|------|---------|
| Checkmarx | SAST | Source code analysis |
| SonarQube | Quality | Code quality & security |
| Prisma/Trivy | Container | Docker image scanning |
| Snyk | SCA | Dependency vulnerabilities |
| OWASP ZAP | DAST | Runtime security testing |

---

## 4-5. Deployment & Scaling

### Graceful Shutdown

```yaml
# Spring Boot
server:
  shutdown: graceful
spring:
  lifecycle:
    timeout-per-shutdown-phase: 30s

# Kubernetes
spec:
  terminationGracePeriodSeconds: 60
  containers:
  - name: app
    lifecycle:
      preStop:
        exec:
          command: ["/bin/sh", "-c", "sleep 15"]
```

### Auto Scaling

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-service
  minReplicas: 3
  maxReplicas: 100
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

## 6. OpenShift

### ConfigMap vs Secret

| ConfigMap | Secret |
|-----------|--------|
| Non-sensitive config | Sensitive data |
| Plain text | Base64 encoded |
| App settings | Passwords, tokens, keys |

```yaml
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  LOG_LEVEL: "INFO"
  MAX_CONNECTIONS: "100"

# Secret
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
stringData:
  DB_PASSWORD: password123
```

### Using in Pods

```yaml
env:
- name: LOG_LEVEL
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: LOG_LEVEL
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: app-secrets
      key: DB_PASSWORD
```

---

# 📋 QUICK REFERENCE

## Interview Cheat Sheet

| Topic | Key Points |
|-------|------------|
| Java 8 | Lambda, Streams, Optional, Default methods, Date/Time API |
| Collections | HashMap (hashCode/equals), ConcurrentHashMap, ArrayList vs LinkedList |
| Threads | synchronized, ReentrantLock, ExecutorService, volatile |
| Spring Boot | DI, @Component/@Service/@Repository, Bean lifecycle |
| REST | GET/POST/PUT/DELETE, Status codes, @RestController |
| Microservices | Circuit Breaker, Service Discovery, API Gateway |
| Docker | Image, Container, Dockerfile, docker-compose |
| Kubernetes | Pod, Deployment, Service, ConfigMap, Secret |

---

*This guide is designed for interview preparation and quick reference. For in-depth understanding, practice coding and building projects.*

