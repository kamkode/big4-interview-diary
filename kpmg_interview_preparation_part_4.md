# Java Interview Questions and Detailed Answers - PART 4

> **Interview Tip**: For Java interviews, focus on demonstrating both theoretical understanding and practical application. When answering, start with a concise definition, then provide a real-world example or code snippet to show your hands-on experience.

## Core Java Concepts 💡

### 1. What is Object-Oriented Programming (OOP) in Java, and how is it applied?
OOP is a way to design and structure a program using real-world objects. Java applies OOP through:
- **Encapsulation**: Bundling data and methods inside a class. For example, making variables private and accessing them with getters/setters.
- **Inheritance**: Creating a new class (child) from an existing class (parent) to reuse code. Uses the "extends" keyword.
- **Polymorphism**: Performing the same action in different ways. Includes method overloading and overriding.
- **Abstraction**: Hiding implementation details and exposing only necessary features using abstract classes and interfaces.

### 2. What are abstract methods and interfaces in Java?
An abstract method has no body and is defined in an abstract class. Subclasses must implement it. 
An interface is like a contract. It can contain abstract methods (till Java 7) and static/default methods (Java 8+). A class can implement multiple interfaces.

### 3. How do method overriding and method overloading differ in Java?
**Overriding**: Happens in inheritance. Child class redefines parent method with the same name and parameters.
**Overloading**: Same method name, different parameter list in the same class.

Example:
```java
class MathOps {
    int add(int a, int b) { return a + b; } // overload
    int add(int a, int b, int c) { return a + b + c; } // overload
}
class MyMath extends MathOps {
    @Override
    int add(int a, int b) { return a + b + 10; } // override
}
```

### 4. What are the differences between JOIN and UNION in SQL?
**JOIN**: Combines rows from two or more tables based on related columns.
**UNION**: Combines results of two SELECT statements. Removes duplicates by default.

### 5. What is exception handling in Java, and what types of exceptions exist?
Exception handling allows you to manage runtime errors gracefully using try-catch-finally blocks.
- **Checked exceptions**: Must be caught or declared using "throws". Example: IOException.
- **Unchecked exceptions**: Occur at runtime. Example: NullPointerException.

### 6. Which Java exceptions require explicit handling?
Checked exceptions like IOException, FileNotFoundException, SQLException require explicit handling.

### 7. How do runtime and compile-time exceptions differ in Java?
- **Compile-time (checked)**: Detected at compile time. Must be handled.
- **Runtime (unchecked)**: Detected at runtime. Optional to handle.

### 8. What are throw, throws, and thrown in Java?
- **throw**: Used to explicitly throw an exception.
- **throws**: Declares exceptions in a method signature.
- **thrown**: A term used to describe when an exception is raised.

## Java Collections Framework 📊

> **Interview Tip**: When discussing collections, be prepared to compare and contrast different implementations. Interviewers often ask follow-up questions about performance characteristics and when to use one collection over another.

### 9. What is the Java Collection Framework?
It is a unified architecture to store and manipulate groups of objects. It includes interfaces (List, Set, Map), implementations (ArrayList, HashSet, HashMap), and algorithms (sort, search).

### 10. How do ArrayList and HashMap work internally?

**ArrayList**:
- Uses a dynamic array implementation
- Initial capacity is 10 (default)
- Grows by 50% when capacity is reached via `Arrays.copyOf()`
- Time complexity: O(1) for access, O(n) for insertion/deletion at arbitrary position
- Memory overhead when resizing

**HashMap**:
- Uses array of buckets (16 buckets by default)
- Each bucket can hold multiple entries as a linked list or tree
- Uses hashing to determine bucket location: `index = hash(key) & (n-1)`
- Converts to balanced tree when bucket size exceeds 8 (improves worst-case performance)
- Load factor determines when to resize (default 0.75)
- Time complexity: O(1) average case, O(log n) worst case

### 11. What are the characteristics of HashMap?
- Allows one null key and multiple null values.
- Not synchronized.
- Fast lookup using keys.

### 12. Difference between HashMap and Hashtable?
- **HashMap**: Not thread-safe, better performance, allows null key.
- **Hashtable**: Thread-safe, slower, doesn't allow null key or value.

### 13. How to create custom HashMap class?
Extend HashMap or implement Map interface. Override put(), get() if needed.

### 14. What is inheritance in Java?
Inheritance allows one class to acquire fields and methods of another class using the "extends" keyword.

### 15. What is abstraction?
Abstraction hides complexity by showing only essential features and hiding internal details.

### 16. What is the purpose of hashCode?
hashCode() helps collections like HashMap to quickly find objects based on hash values.

### 17. Comparable vs Comparator?
- **Comparable**: Natural ordering inside the class using compareTo().
- **Comparator**: External ordering using compare().

### 18. What is static keyword?
Used to declare class-level variables or methods. Shared by all instances.

### 19. Why are Strings immutable in Java?
- **Security**: Can't be modified after creation.
- **Caching**: Uses String pool for memory efficiency.
- **Thread-safe**: No synchronization needed.

### 20. What is String Pool?
A special memory area in Java Heap where identical string literals are stored to save memory.

### 21. Difference between HashMap, Hashtable, TreeMap?
- **HashMap**: Fast, no order.
- **Hashtable**: Thread-safe, slower.
- **TreeMap**: Sorted keys using natural order or comparator.

### 22. Can a Java class be static?
Only nested (inner) classes can be static. Top-level classes cannot.

### 23. What is a Singleton class?
A class that allows only one object to be created. Useful in logging, config management, etc.

### 24. How to implement Singleton?
Make constructor private. Provide static method to return the only instance.

**Implementation approaches:**

1. **Eager initialization** (thread-safe):
```java
class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

2. **Lazy initialization with double-checked locking** (thread-safe):
```java
class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

3. **Enum implementation** (simplest and best practice):
```java
public enum Singleton {
    INSTANCE;
    
    // Add methods/fields here
    public void doSomething() {
        // implementation
    }
}

// Usage: Singleton.INSTANCE.doSomething();
```

### 25. How to make a class immutable?
- Make class final.
- All fields private and final.
- No setters, only getters.
- Return copies for mutable fields.

### 26. Difference between StringBuilder and StringBuffer?
- **StringBuilder**: Not thread-safe, faster.
- **StringBuffer**: Thread-safe, slower.

## Spring Framework and Microservices 🔄

> **Interview Tip**: For Spring questions, demonstrate knowledge of both core concepts and recent features. Being familiar with Spring Boot's auto-configuration and common annotations will set you apart from other candidates.

### 27. Core modules of Spring?
- **Spring Core**: DI and IoC container.
- **Spring MVC**: Web framework.
- **Spring Boot**: Simplifies setup and config.
- **Spring Security**: Secures apps.
- **Spring Data**: Works with databases.

### 28. What are microservices?
Independent services that perform a single function. Communicate via REST or messaging.

### 29. What is Eureka Server and Eureka Client?

**Eureka Server**:
- Service registry that maintains a directory of available microservices
- Provides REST API for service registration and discovery
- Part of Netflix OSS, integrated with Spring Cloud
- Supports self-preservation mode to handle network partitions

**Eureka Client**:
- Registers itself with Eureka Server on startup
- Sends heartbeats to maintain registration
- Can query the registry to locate other services
- Caches registry information locally for resilience

**Example configuration (Server):**
```java
@SpringBootApplication
@EnableEurekaServer
public class ServiceRegistryApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceRegistryApplication.class, args);
    }
}
```

**Example configuration (Client):**
```java
@SpringBootApplication
@EnableEurekaClient
public class ServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceApplication.class, args);
    }
}
```

### 30. What is API Gateway?
Entry point for all client requests. Manages routing, security, load balancing, etc.

### 31. What is a Spring Bean? What does @Component do?
**Bean**: Object managed by Spring IoC container.
**@Component**: Marks a class as a Spring Bean.

### 32. DI vs IoC?
**IoC**: Spring framework controls object creation.
**DI**: Spring injects objects where needed.

### 33. What is autowiring? What does @Autowired do?
Automatically injects required dependencies by Spring using @Autowired.

### 34. Methods of autowiring?
- **ByType**: Injects by data type.
- **ByName**: Injects by property name.
- **Constructor**: Uses constructor injection.
- **Annotation**: Uses @Autowired.

### 35. Ways to implement DI in Spring?
- Constructor injection (recommended)
- Setter injection
- Field injection (not test-friendly)

## Visual References 📊

> **Interview Tip**: Having a mental model of system architectures demonstrates your ability to see the big picture. Be ready to sketch out these diagrams during interviews to show your understanding of system interactions.

### Microservices Architecture
```
[Client App]
   |
[API Gateway]
   |
+------------------------+
|      Eureka Server     |
+------------------------+
   |            |
[Service A]   [Service B]
   |
[Spring Boot + Bean + DI]
```

### Spring DI Flow
```
Class A needs Class B:
- Declare B
- Annotate with @Autowired
- Spring injects B object into A
```

## Comparative Tables 📋

> **Interview Tip**: These comparison tables are highly valuable for interview preparation. When faced with questions asking you to compare concepts, structure your answer as "X is ___ while Y is ___. The key differences are..." followed by 2-3 main differences.

### Method Overloading vs Overriding

| Overloading | Overriding |
|-------------|------------|
| Same method name, different parameters | Same method name and parameters |
| Within same class | Parent-child relationship |
| Compile-time polymorphism | Runtime polymorphism |

### Exception Types

| Checked Exceptions | Unchecked Exceptions |
|-------------------|---------------------|
| Detected at compile time | Detected at runtime |
| Must be handled or declared | Optional to handle |
| Examples: IOException, SQLException | Examples: NullPointerException, ArrayIndexOutOfBoundsException |

### Java Collection Classes

| Collection | Implementation | Thread-Safe | Null Keys/Values | Order |
|------------|---------------|------------|-----------------|-------|
| HashMap | Hash table | No | One null key, multiple null values | No order |
| Hashtable | Hash table | Yes | No nulls allowed | No order |
| TreeMap | Red-black tree | No | No null keys, null values allowed | Sorted by keys |

### StringBuilder vs StringBuffer

| Feature | StringBuilder | StringBuffer |
|---------|--------------|--------------|
| Thread Safety | Not thread-safe | Thread-safe |
| Performance | Faster | Slower |
| Use Case | Single-threaded environments | Multi-threaded environments |
