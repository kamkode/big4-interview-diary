# KPMG Technical Interview Preparation

## Java and Spring Boot Questions

### 1. Which Java version and Spring version are you using in your project?
**Answer:** I am using Java 17 and Spring Boot 3.3.0 in my current project.

### 2. Which Java 8 features are you working with?
**Answer:** I'm working with several Java 8 features in my project:
- **Lambda Expressions** - For functional programming and cleaner code
- **Stream API** - For processing collections of objects
- **Optional** - For better null handling
- **Method References** - To make code more readable
- **Default and Static Methods in Interfaces** - For backward compatibility
- **New Date and Time API** - For better date/time handling
- **CompletableFuture** - For asynchronous programming
- **Functional Interfaces** - Used with lambdas for functional programming paradigms

### 3. Spring Boot Starter Parent - What is its importance (beyond just downloading dependencies)?
**Answer:** Spring Boot Starter Parent's importance goes beyond just downloading dependencies:

- **Dependency Management** - It provides a curated set of compatible dependencies with tested versions
- **Maven Defaults** - It sets sensible defaults for Maven plugins and configurations
- **Version Management** - It manages versions of all dependencies to ensure compatibility
- **Inherited Build Configuration** - Provides common build configurations that can be inherited by child projects
- **Consistent Build Environment** - Ensures consistency across multiple modules of the same project
- **Pluggable Features** - Makes it easy to add features by just adding a starter dependency
- **Simplified Upgrades** - Simplifies upgrading Spring Boot version across the entire project

### 4. Authentication vs. Authorization (How it works in your project)
**Answer:**

**Authentication:**
- Verifies the identity of a user
- In my project, we use Spring Security with JWT for authentication
- Users provide credentials (username/password)
- These credentials are validated against stored user information
- Upon successful validation, a JWT token is generated and returned to the client
- For subsequent requests, the token is included in the Authorization header

**Authorization:**
- Determines what resources an authenticated user can access
- In my project, we use role-based access control (RBAC)
- JWT tokens contain user roles
- Spring Security's @PreAuthorize annotations on methods/controllers enforce permissions
- Custom SecurityConfig defines access rules for different endpoints
- Method-level security ensures sensitive operations are restricted to authorized users

### 5. Anonymous Array with Example
**Answer:**

Anonymous arrays in Java are arrays created without an explicit reference variable. They're typically used for one-time use scenarios.

Example:
```java
// Creating and using an anonymous array
public void processNames() {
    // Anonymous string array as a method argument
    printNames(new String[]{"John", "Mary", "Tom"});
    
    // Anonymous integer array in a for-each loop
    for (int number : new int[]{1, 2, 3, 4, 5}) {
        System.out.println(number);
    }
}

public void printNames(String[] names) {
    for (String name : names) {
        System.out.println(name);
    }
}
```

### 6. SOLID Principles
**Answer:**

**S - Single Responsibility Principle**
- A class should have only one reason to change
- Example: Separating data access, business logic, and presentation into different classes

**O - Open/Closed Principle**
- Software entities should be open for extension but closed for modification
- Example: Using interfaces and inheritance to extend functionality without modifying existing code

**L - Liskov Substitution Principle**
- Subtypes must be substitutable for their base types without altering program correctness
- Example: Ensuring that all implementations of an interface behave as expected

**I - Interface Segregation Principle**
- Clients should not be forced to depend on methods they do not use
- Example: Creating specific interfaces rather than one large interface

**D - Dependency Inversion Principle**
- High-level modules should not depend on low-level modules; both should depend on abstractions
- Example: Using dependency injection to decouple components

### 7. Have you worked on Cloud?
**Answer:** Yes, I have worked with cloud technologies:

- **AWS Services** - Utilized EC2 for hosting applications, S3 for storage, RDS for database management
- **Containerization** - Used Docker to containerize applications for consistent deployment
- **CI/CD** - Implemented continuous integration/deployment pipelines with Jenkins and AWS CodePipeline
- **Microservices** - Designed and deployed microservices architecture on the cloud
- **Serverless** - Worked with AWS Lambda for event-driven processing
- **Auto-scaling** - Configured auto-scaling groups to handle varying loads efficiently
- **Monitoring** - Used CloudWatch for monitoring application performance and health

### 8. How are you securing your REST APIs (JWT/OAuth)?
**Answer:** In my project, we secure REST APIs using:

**JWT (JSON Web Tokens):**
- Stateless authentication mechanism
- Users authenticate once and receive a signed token
- Token contains encoded user information and claims
- All subsequent requests include this token in the Authorization header
- Server validates the token signature and extracts user information

**Implementation Details:**
- Using Spring Security with custom JWT filter
- Tokens have a configurable expiration time
- Refresh token mechanism for seamless re-authentication
- Role-based access control embedded in token claims
- HTTPS for all API communications to prevent token interception
- Token blacklisting for logout functionality

### 9. Signature Importance in JWT
**Answer:** The signature in JWT is critically important for several reasons:

- **Integrity Verification** - Ensures the token hasn't been tampered with or modified
- **Authentication** - Verifies the token was issued by a trusted authority
- **Non-repudiation** - Provides proof that the token was created by the claimed issuer
- **Security** - Prevents unauthorized parties from forging valid tokens
- **Trust** - Establishes trust between the token issuer and the recipient

The signature is created by encoding the header and payload using a secret key or private key (in case of asymmetric encryption). When a service receives a JWT, it recalculates the signature using the same algorithm and secret/public key and compares it with the received signature. If they match, the token is valid; otherwise, it is rejected.

### 10. Output of the given code:
```java
class A {
    String i, j;

    A(String i, String j) {
        this.i = i;
        this.j = j;
    }
}

public class SampleOutput {
    public static void main(String[] args) {
        final A a = new A(null, null);
        System.out.println(a.i + " " + a.j);
    }
}
```

**Answer:** The output will be:
```
null null
```

Explanation:
- Object 'a' is created with both i and j initialized to null
- When System.out.println is called, Java converts null to the string "null"
- The + operator concatenates these strings with a space in between
- Note that the 'final' keyword on variable 'a' means the reference cannot be changed, but the object's properties can still be modified

### 11. Decoding String Implementation:
```java
public class DecodeString {
    public static void main(String[] args) {
        String str = "w4a3d1e1x6";
        
        StringBuilder result = new StringBuilder();
        
        for (int i = 0; i < str.length(); i += 2) {
            char character = str.charAt(i);
            int count = Character.getNumericValue(str.charAt(i + 1));
            
            for (int j = 0; j < count; j++) {
                result.append(character);
            }
        }
        
        System.out.println(result.toString());
        // Output: "wwwwaaaddeexxxxxx"
    }
}
```

### 12. Shifting Zeroes Solution with O(1) Space Complexity:
```java
import java.util.Arrays;

public class ShiftZeroes {
    public static void main(String[] args) {
        int[] arr = {1, 0, 9, 0, 7, 0, 5, 8};
        
        // Shift all zeroes to the RIGHT
        shiftZeroesToRight(arr.clone());
        
        // Shift all zeroes to the LEFT
        shiftZeroesToLeft(arr.clone());
    }
    
    // Solution to shift zeroes to the right with O(1) space complexity
    public static void shiftZeroesToRight(int[] arr) {
        int nonZeroIndex = 0;
        
        // Move all non-zero elements to the front
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] != 0) {
                arr[nonZeroIndex++] = arr[i];
            }
        }
        
        // Fill the rest with zeroes
        while (nonZeroIndex < arr.length) {
            arr[nonZeroIndex++] = 0;
        }
        
        System.out.println("Zeroes to right: " + Arrays.toString(arr));
        // Output: [1, 9, 7, 5, 8, 0, 0, 0]
    }
    
    // Solution to shift zeroes to the left with O(1) space complexity
    public static void shiftZeroesToLeft(int[] arr) {
        int nonZeroIndex = arr.length - 1;
        
        // Move all non-zero elements to the back
        for (int i = arr.length - 1; i >= 0; i--) {
            if (arr[i] != 0) {
                arr[nonZeroIndex--] = arr[i];
            }
        }
        
        // Fill the rest with zeroes
        while (nonZeroIndex >= 0) {
            arr[nonZeroIndex--] = 0;
        }
        
        System.out.println("Zeroes to left: " + Arrays.toString(arr));
        // Output: [0, 0, 0, 1, 9, 7, 5, 8]
    }
}
```

### 13. SQL Query to Fetch Department-wise Count of Employees Sorted by Department in Ascending Order
**Answer:**
```sql
SELECT 
    d.department_name, 
    COUNT(e.employee_id) AS employee_count
FROM 
    departments d
LEFT JOIN 
    employees e ON d.department_id = e.department_id
GROUP BY 
    d.department_id, d.department_name
ORDER BY 
    d.department_name ASC;
```

## L2 Questions

### 14. Design Patterns Used in Your Project
**Answer:**

**Commonly Used Patterns:**
1. **Builder Pattern**
   - Used for creating complex objects step by step
   - Example: Building complex request/response objects with many optional parameters
   
2. **Factory Pattern**
   - Used to create objects without specifying the exact class
   - Example: Creating different types of service implementations based on configuration
   
3. **Singleton Pattern**
   - Ensures a class has only one instance and provides global access
   - Example: Configuration managers, connection pools

**Additional Design Patterns I'm Familiar With:**

4. **Strategy Pattern** (Behavioral)
   - Defines a family of algorithms, encapsulates each one, and makes them interchangeable
   - Example: Different payment processing strategies (credit card, PayPal, etc.)

5. **Observer Pattern** (Behavioral)
   - Defines a one-to-many dependency so that when one object changes state, all dependents are notified
   - Example: Event handling systems, pub/sub implementations

6. **Decorator Pattern** (Structural)
   - Attaches additional responsibilities to objects dynamically
   - Example: Adding caching or logging capabilities to existing components

7. **Adapter Pattern** (Structural)
   - Allows incompatible interfaces to work together
   - Example: Integrating with third-party APIs that have different interfaces

8. **Repository Pattern** (Architectural)
   - Abstracts the data access layer
   - Example: Providing a clean API for database operations

**Difference Between Behavioral Design Patterns:**
Behavioral design patterns focus on communication between objects, defining how objects interact and distribute responsibility. Unlike creational patterns (which deal with object creation) or structural patterns (which deal with object composition), behavioral patterns are concerned with algorithms and the assignment of responsibilities between objects.

Key behavioral patterns include Strategy, Observer, Command, Template Method, and Chain of Responsibility. They help manage complex control flows, reduce coupling, and increase flexibility in how objects communicate.

### 15. JVM Internal Architecture
**Answer:**

The JVM internal architecture consists of several components:

1. **Class Loader Subsystem**
   - Loads, links, and initializes Java class files
   - Three types: Bootstrap, Extension, and Application class loaders
   - Performs verification to ensure code integrity

2. **Runtime Data Areas**
   - **Method Area** - Stores class structures, method data, and constant runtime pool
   - **Heap** - Memory area where objects are allocated
   - **Java Stacks** - Contains frames for method execution, one stack per thread
   - **PC Registers** - Stores the address of the current executing instruction
   - **Native Method Stacks** - Used for native method execution

3. **Execution Engine**
   - **Interpreter** - Executes bytecode instructions
   - **JIT Compiler** - Compiles frequently executed bytecode to native code
   - **Garbage Collector** - Manages automatic memory deallocation

4. **Native Method Interface (JNI)**
   - Provides interface for Java code to call and be called by native applications

5. **Native Method Libraries**
   - Collection of native libraries required by the execution engine

### 16. Returning User-defined Exceptions from Controller Class
**Answer:**

To return user-defined exceptions from a controller class in Spring:

```java
// 1. Create a custom exception class
@ResponseStatus(HttpStatus.BAD_REQUEST)
public class CustomBusinessException extends RuntimeException {
    private String errorCode;
    
    public CustomBusinessException(String message, String errorCode) {
        super(message);
        this.errorCode = errorCode;
    }
    
    public String getErrorCode() {
        return errorCode;
    }
}

// 2. Create a global exception handler
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(CustomBusinessException.class)
    public ResponseEntity<ErrorResponse> handleCustomException(CustomBusinessException ex) {
        ErrorResponse error = new ErrorResponse(
            ex.getErrorCode(),
            ex.getMessage(),
            LocalDateTime.now()
        );
        
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }
    
    // Error response model
    @Data
    @AllArgsConstructor
    public static class ErrorResponse {
        private String errorCode;
        private String message;
        private LocalDateTime timestamp;
    }
}

// 3. Throw the exception from the controller
@RestController
@RequestMapping("/api")
public class UserController {
    
    @GetMapping("/users/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        
        if (user == null) {
            throw new CustomBusinessException(
                "User not found with id: " + id,
                "USER_NOT_FOUND"
            );
        }
        
        return ResponseEntity.ok(user);
    }
}
```

### 17. @AutoConfiguration vs @SpringBootApplication - Impact if Reversed
**Answer:**

**@SpringBootApplication** is a convenience annotation that combines:
- @Configuration
- @EnableAutoConfiguration
- @ComponentScan

**@EnableAutoConfiguration** (which is part of @SpringBootApplication) is what triggers Spring Boot's auto-configuration mechanism.

If you reverse the order (i.e., use @AutoConfiguration instead of @SpringBootApplication at the application level):

1. **Component Scanning Issues**: 
   - @ComponentScan would be missing, so Spring wouldn't automatically detect your @Component, @Service, @Repository, and @Controller classes
   - You would need to explicitly define all beans or add @ComponentScan separately

2. **Configuration Issues**:
   - @Configuration would be missing, so the class wouldn't be recognized as a source of bean definitions
   - You would need to add @Configuration explicitly

3. **Initialization Problems**:
   - The application context might not be properly configured
   - Spring Boot's opinionated defaults wouldn't be applied correctly

4. **Startup Failure**:
   - The application would likely fail to start or function properly
   - You would lose most of the "auto" part of Spring Boot

In summary, reversing these annotations would break the Spring Boot application's core functionality and require significant manual configuration to make it work again.

### 18. What is a Bean?
**Answer:**

In Spring, a Bean is:

- An object that is instantiated, assembled, and managed by the Spring IoC (Inversion of Control) container
- The backbone of a Spring application
- A reusable software component that conforms to certain conventions

**Key Characteristics:**
1. **Lifecycle Management** - Spring manages the complete lifecycle of beans (creation, initialization, destruction)
2. **Dependency Injection** - Beans can have dependencies injected by the container
3. **Scope** - Beans can have different scopes (singleton, prototype, request, session, etc.)
4. **Lazy Initialization** - Beans can be lazily initialized to improve startup performance
5. **Configuration** - Beans can be configured via XML, annotations, or Java configuration

**Ways to Define Beans:**
- Using @Component, @Service, @Repository, @Controller annotations
- Using @Bean methods in @Configuration classes
- Using XML configuration
- Using component scanning

**Example:**
```java
@Configuration
public class AppConfig {
    
    @Bean
    public UserService userService() {
        return new UserServiceImpl();
    }
    
    @Bean
    public EmailService emailService() {
        return new EmailServiceImpl();
    }
}
```

In this example, `userService()` and `emailService()` methods create and return beans that will be managed by the Spring container.
