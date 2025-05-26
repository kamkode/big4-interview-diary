# Improved Java Interview Questions - PART 7

> **Interviewer Insight**: These questions cover both core Java concepts and advanced Spring Framework topics that frequently appear in senior developer interviews. Having practical examples ready will significantly strengthen your answers.

## Table of Contents
- [Java 8 Features](#java-8-features)
- [Java Core Concepts](#java-core-concepts)
- [Multithreading and Concurrency](#multithreading-and-concurrency)
- [Design Patterns](#design-patterns)
- [Spring Framework](#spring-framework)
- [Testing and DevOps](#testing-and-devops)
- [Data Structures and Collections](#data-structures-and-collections)
- [Security](#security)

## Java 8 Features

### 1. What are the new features introduced in Java 8?

> **Why this is asked**: This tests your awareness of modern Java features and your ability to leverage them in applications.

**Key Java 8 Features:**

1. **Lambda Expressions**: Anonymous functions that enable functional programming
   ```java
   // Before Java 8
   Collections.sort(names, new Comparator<String>() {
       @Override
       public int compare(String a, String b) {
           return a.compareTo(b);
       }
   });
   
   // With Lambda in Java 8
   Collections.sort(names, (a, b) -> a.compareTo(b));
   ```

2. **Stream API**: Enables functional-style operations on collections
   ```java
   // Finding sum of numbers > 10
   int sum = numbers.stream()
                   .filter(n -> n > 10)
                   .mapToInt(Integer::intValue)
                   .sum();
   ```

3. **Method References**: Shorthand for lambdas calling a specific method
   ```java
   // Instead of: list.forEach(s -> System.out.println(s));
   list.forEach(System.out::println);
   ```

4. **Default Methods**: Interface methods with implementation
   ```java
   public interface Vehicle {
       default void print() {
           System.out.println("I am a vehicle!");
       }
   }
   ```

5. **Optional Class**: Container to handle null values better
   ```java
   Optional<String> optional = Optional.ofNullable(getName());
   String name = optional.orElse("Default");
   ```

6. **New Date/Time API**: Improved date and time handling
   ```java
   LocalDate today = LocalDate.now();
   LocalDateTime timestamp = LocalDateTime.now();
   ZonedDateTime zonedDateTime = ZonedDateTime.now(ZoneId.of("Europe/Paris"));
   ```

7. **CompletableFuture**: Enhanced asynchronous programming
   ```java
   CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
       // Long running task
       return "Result";
   });
   ```

8. **Nashorn JavaScript Engine**: New JavaScript runtime 

9. **Parallel Array Sorting**: Utilizes multi-core processors
   ```java
   Arrays.parallelSort(numbers);
   ```

10. **Type Annotations**: Annotations that can be applied to any type use
    ```java
    List<@NotNull String> list;
    ```

## Java Core Concepts

### 2. What are Comparable and Comparator in Java, and in which scenarios are they used?

> **Why this is asked**: This evaluates your understanding of sorting mechanisms in Java and when to use each approach.

**Comparable vs Comparator:**

| Feature | Comparable | Comparator |
|---------|------------|------------|
| Interface | `java.lang.Comparable` | `java.util.Comparator` |
| Method | `compareTo(Object o)` | `compare(Object o1, Object o2)` |
| Implementation | Inside the class to be compared | In a separate class |
| Usage | For natural ordering | For custom/multiple orderings |
| Control | Class must be modified | No modification to original class |

**Comparable Example:**
```java
public class Employee implements Comparable<Employee> {
    private String name;
    private int age;
    
    // Constructor, getters, setters
    
    @Override
    public int compareTo(Employee other) {
        // Natural ordering by age
        return this.age - other.age;
    }
}

// Usage
List<Employee> employees = getEmployees();
Collections.sort(employees); // Sorts by age (natural order)
```

**Comparator Example:**
```java
public class EmployeeNameComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return e1.getName().compareTo(e2.getName());
    }
}

// Usage
List<Employee> employees = getEmployees();
Collections.sort(employees, new EmployeeNameComparator()); // Sorts by name

// Or with Java 8 lambda
Collections.sort(employees, (e1, e2) -> e1.getName().compareTo(e2.getName()));
```

### 3. If you want to define custom comparison logic, which interface should you use?

> **Why this is asked**: This follow-up tests if you understand when Comparator is the appropriate choice.

For custom comparison logic, especially when:
1. You cannot modify the class (e.g., third-party library)
2. You need multiple different sorting orders
3. You want to separate comparison logic from the business object

**Use the Comparator interface:**

```java
// Sort employees by department, then by salary
Comparator<Employee> departmentThenSalaryComparator = Comparator
    .comparing(Employee::getDepartment)
    .thenComparingDouble(Employee::getSalary);

Collections.sort(employees, departmentThenSalaryComparator);

// With Java 8 you can create multiple comparators
Comparator<Employee> byName = Comparator.comparing(Employee::getName);
Comparator<Employee> bySalary = Comparator.comparingDouble(Employee::getSalary);
Comparator<Employee> byNameReversed = byName.reversed();

// Use them flexibly
employees.stream()
    .sorted(byName)
    .collect(Collectors.toList());
```

## Multithreading and Concurrency

### 4. What is a deadlock in Java, and how does it occur?

> **Why this is asked**: This tests your understanding of concurrent programming issues and ability to write thread-safe code.

**Deadlock Definition:**
A deadlock occurs when two or more threads are blocked forever, each waiting for resources held by the other threads.

**For a deadlock to occur, four conditions must be met:**
1. **Mutual Exclusion**: Resources cannot be shared simultaneously
2. **Hold and Wait**: A thread holds one resource while waiting for another
3. **No Preemption**: Resources cannot be forcibly taken from threads
4. **Circular Wait**: A circular chain of threads, each waiting for a resource held by the next

**Example of a Deadlock:**
```java
public class DeadlockExample {
    private static final Object RESOURCE_A = new Object();
    private static final Object RESOURCE_B = new Object();
    
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            synchronized (RESOURCE_A) {
                System.out.println("Thread 1: Holding Resource A...");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                System.out.println("Thread 1: Waiting for Resource B...");
                
                synchronized (RESOURCE_B) {
                    System.out.println("Thread 1: Holding both resources");
                }
            }
        });
        
        Thread thread2 = new Thread(() -> {
            synchronized (RESOURCE_B) {
                System.out.println("Thread 2: Holding Resource B...");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                System.out.println("Thread 2: Waiting for Resource A...");
                
                synchronized (RESOURCE_A) {
                    System.out.println("Thread 2: Holding both resources");
                }
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

In this example:
- Thread 1 acquires Resource A and then tries to acquire Resource B
- Thread 2 acquires Resource B and then tries to acquire Resource A
- Both threads wait indefinitely for the other's resource, causing a deadlock

### 5. What is the use of JConsole in Java development?

> **Why this is asked**: This evaluates your experience with Java monitoring and troubleshooting tools.

**JConsole:**
JConsole is a monitoring and management tool included in the Java Development Kit (JDK) that provides information about performance and resource consumption of Java applications.

**Key Features:**
1. **Memory Usage Monitoring**: Heap and non-heap memory consumption
2. **Thread Monitoring**: Active threads, deadlocks, thread states
3. **Class Loading**: Number of loaded/unloaded classes
4. **JVM Summary**: Runtime information about the Java virtual machine
5. **MBean Operations**: Interact with JMX-enabled applications

**Use Cases:**
- Identifying memory leaks
- Detecting thread deadlocks
- Monitoring CPU usage
- Performance tuning
- Runtime management via MBeans

**How to Launch:**
```
jconsole [options] [connection]
```

**Example Use for Deadlock Detection:**
1. Launch JConsole and connect to your application
2. Go to the "Threads" tab
3. Click "Detect Deadlock" button
4. JConsole will show the deadlocked threads and lock information if found

### 7. How can you prevent deadlocks in Java applications?

> **Why this is asked**: This tests your problem-solving skills for concurrency issues and preventative design thinking.

**Deadlock Prevention Strategies:**

1. **Lock Ordering**: Always acquire locks in a fixed, predefined order
   ```java
   // Consistent lock ordering
   public void transferMoney(Account from, Account to, double amount) {
       // Ensure locks are always acquired in the same order
       Account firstLock = from.getId() < to.getId() ? from : to;
       Account secondLock = from.getId() < to.getId() ? to : from;
       
       synchronized (firstLock) {
           synchronized (secondLock) {
               // Transfer logic
           }
       }
   }
   ```

2. **Lock Timeouts**: Use tryLock with timeout instead of indefinite waiting
   ```java
   Lock lock1 = new ReentrantLock();
   Lock lock2 = new ReentrantLock();
   
   public void process() {
       try {
           if (lock1.tryLock(10, TimeUnit.SECONDS)) {
               try {
                   if (lock2.tryLock(10, TimeUnit.SECONDS)) {
                       try {
                           // Process when both locks acquired
                       } finally {
                           lock2.unlock();
                       }
                   }
               } finally {
                   lock1.unlock();
               }
           }
       } catch (InterruptedException e) {
           Thread.currentThread().interrupt();
       }
   }
   ```

3. **Deadlock Detection**: Implement a detection system using ThreadMXBean
   ```java
   public static boolean detectDeadlock() {
       ThreadMXBean threadBean = ManagementFactory.getThreadMXBean();
       long[] threadIds = threadBean.findDeadlockedThreads();
       return threadIds != null && threadIds.length > 0;
   }
   ```

4. **Avoid Nested Locks**: Minimize using multiple locks in a single method

5. **Use Higher-Level Concurrency Utilities**:
   - java.util.concurrent collections
   - Executors and thread pools
   - Atomic variables
   - Concurrent data structures

6. **Resource Allocation Graph**: In complex systems, model resource allocation to detect cycles

7. **Lock-Free Algorithms**: Use non-blocking algorithms where possible
   ```java
   AtomicInteger counter = new AtomicInteger(0);
   counter.incrementAndGet(); // Thread-safe increment without locks
   ```

## Java Core Concepts (Continued)

### 6. What are generics in Java and why are they useful?

> **Why this is asked**: This evaluates your understanding of type safety and code reusability principles.

**Generics Overview:**
Generics enable classes, interfaces, and methods to operate on objects of various types while providing compile-time type safety.

**Benefits of Generics:**
1. **Type Safety**: Catch type errors at compile time rather than runtime
2. **Elimination of Type Casting**: No need for explicit casting
3. **Code Reusability**: Write algorithms once that work on different types
4. **Improved API Design**: Create more flexible and descriptive interfaces

**Examples:**

1. **Generic Class**:
```java
public class Box<T> {
    private T content;
    
    public void set(T content) {
        this.content = content;
    }
    
    public T get() {
        return content;
    }
}

// Usage
Box<String> stringBox = new Box<>();
stringBox.set("Hello World");
String str = stringBox.get(); // No casting needed

Box<Integer> intBox = new Box<>();
intBox.set(123);
Integer num = intBox.get();
```

2. **Generic Method**:
```java
public static <T> T findMax(List<T> list, Comparator<T> comp) {
    if (list.isEmpty()) {
        return null;
    }
    
    T max = list.get(0);
    for (T item : list) {
        if (comp.compare(item, max) > 0) {
            max = item;
        }
    }
    return max;
}

// Usage
List<Employee> employees = getEmployeeList();
Employee highestPaid = findMax(employees, 
    Comparator.comparingDouble(Employee::getSalary));
```

3. **Bounded Type Parameters**:
```java
// T must be a subtype of Number
public class MathBox<T extends Number> {
    private T value;
    
    public MathBox(T value) {
        this.value = value;
    }
    
    public double sqrt() {
        return Math.sqrt(value.doubleValue());
    }
}

// Valid usages
MathBox<Integer> intBox = new MathBox<>(16);
MathBox<Double> doubleBox = new MathBox<>(16.0);

// Won't compile - String is not a Number
// MathBox<String> stringBox = new MathBox<>("16");
```

4. **Wildcard Types**:
```java
// Can read from a list of any Number subtype
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number n : list) {
        sum += n.doubleValue();
    }
    return sum;
}

// Can add Integers or its supertypes to the list
public static void addNumbers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    // list.add("string"); // Won't compile
}
```

## Testing and DevOps

### 8. What is Continuous Integration (CI) and Continuous Deployment (CD), and how are they implemented?

> **Why this is asked**: This tests your familiarity with modern software development practices and automation.

**Continuous Integration (CI):**
The practice of automatically integrating code changes from multiple contributors into a shared repository, followed by automated builds and tests to detect integration problems early.

**Continuous Deployment (CD):**
The practice of automatically deploying all code changes to production after passing through the CI pipeline and additional testing environments.

**CI/CD Process Flow:**
1. **Code Commit**: Developer pushes code to repository
2. **Build**: Automated build process compiles the code
3. **Test**: Automated tests run (unit, integration, etc.)
4. **Analysis**: Code quality checks, security scans
5. **Staging**: Deploy to testing/staging environment
6. **Production**: Automatic or manual approval for production deployment

**Common CI/CD Tools:**
- **Jenkins**: Open-source automation server
- **GitHub Actions**: Integrated with GitHub repositories
- **GitLab CI/CD**: Integrated with GitLab
- **CircleCI**: Cloud-based CI/CD service
- **Travis CI**: CI service for GitHub projects
- **Azure DevOps**: Microsoft's DevOps service
- **AWS CodePipeline**: AWS CI/CD service

**Example GitHub Actions Workflow:**
```yaml
name: Java CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
        
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      
    - name: Run Tests
      run: mvn test
      
    - name: SonarQube Analysis
      run: mvn sonar:sonar
      
    - name: Deploy to Staging
      if: github.ref == 'refs/heads/main'
      run: |
        echo "Deploying to staging environment"
        # Deployment script
        
    - name: Deploy to Production
      if: github.ref == 'refs/heads/main'
      run: |
        echo "Deploying to production"
        # Production deployment script
```

### 20. How do you test a Java application? What are the common tools and strategies?

> **Why this is asked**: This assesses your approach to ensuring software quality and familiarity with testing frameworks.

**Testing Levels:**

1. **Unit Testing**: Test individual components in isolation
   - Tools: JUnit, TestNG
   - Example:
   ```java
   @Test
   public void testAddition() {
       Calculator calc = new Calculator();
       assertEquals(5, calc.add(2, 3));
   }
   ```

2. **Integration Testing**: Test component interactions
   - Tools: Spring Test, Testcontainers
   - Example:
   ```java
   @SpringBootTest
   class OrderServiceIntegrationTest {
       @Autowired
       private OrderService orderService;
       
       @Autowired
       private OrderRepository orderRepository;
       
       @Test
       void createOrderTest() {
           Order order = new Order("product123", 2);
           Order savedOrder = orderService.createOrder(order);
           
           assertNotNull(savedOrder.getId());
           assertEquals("product123", savedOrder.getProductId());
       }
   }
   ```

3. **Functional Testing**: Test complete functionality
   - Tools: Selenium, Cucumber
   - Example:
   ```java
   @RunWith(Cucumber.class)
   @CucumberOptions(features = "src/test/resources/features")
   public class FunctionalTests {}
   
   // Feature file
   // Feature: User places an order
   //   Scenario: Successful order placement
   //     Given the user is logged in
   //     When they add "Product A" to cart
   //     And proceed to checkout
   //     Then the order should be created
   ```

4. **Performance Testing**: Test system performance under load
   - Tools: JMeter, Gatling
   - Example:
   ```java
   // Gatling simulation
   public class ApiLoadTest extends Simulation {
       HttpProtocolBuilder httpProtocol = http
           .baseUrl("http://myapp.com")
           .acceptHeader("application/json");
           
       ScenarioBuilder scn = scenario("Load Test")
           .exec(http("Get Products")
               .get("/api/products")
               .check(status().is(200)));
               
       setUp(
           scn.injectOpen(rampUsers(100).during(60))
       ).protocols(httpProtocol);
   }
   ```

**Testing Strategies:**

1. **Test-Driven Development (TDD)**:
   - Write tests before implementation
   - Red-Green-Refactor cycle

2. **Behavior-Driven Development (BDD)**:
   - Focus on business behavior
   - Use natural language specifications

3. **Mock Objects**:
   - Tools: Mockito, EasyMock
   - Example:
   ```java
   @Test
   public void testOrderService() {
       // Mock dependencies
       ProductRepository mockRepo = mock(ProductRepository.class);
       EmailService mockEmail = mock(EmailService.class);
       
       // Set up behavior
       when(mockRepo.findById("product1")).thenReturn(Optional.of(new Product("product1", 9.99)));
       
       // Test service with mocks
       OrderService service = new OrderService(mockRepo, mockEmail);
       Order order = service.placeOrder("product1", 2);
       
       // Verify interactions
       assertEquals(19.98, order.getTotalPrice(), 0.001);
       verify(mockEmail).sendConfirmation(any(Order.class));
   }
   ```

4. **Code Coverage**:
   - Tools: JaCoCo, Cobertura
   - Aim for high test coverage (typically 70-80%+)

5. **Continuous Testing**:
   - Integrate testing into CI/CD pipeline
   - Automate regression testing

6. **Contract Testing**:
   - Tools: Pact, Spring Cloud Contract
   - Test service interactions based on contracts

## Spring Framework

### 9. If you have test properties in Spring, how can you use a different configuration for local development?

> **Why this is asked**: This evaluates your understanding of Spring's configuration flexibility for different environments.

**Spring Environment-Specific Configuration Options:**

1. **Profile-Specific Properties Files**:
   ```
   src/main/resources/
     application.properties      # Common properties
     application-dev.properties  # Development properties
     application-test.properties # Test properties
     application-prod.properties # Production properties
   ```

2. **Activate Profiles**:
   ```java
   // Command line
   java -jar myapp.jar --spring.profiles.active=dev
   
   // In application.properties
   spring.profiles.active=dev
   
   // Environment variable
   export SPRING_PROFILES_ACTIVE=dev
   ```

3. **YAML Configuration with Profile Sections**:
   ```yaml
   # Common properties
   app:
     name: My Application
   
   ---
   spring:
     config:
       activate:
         on-profile: dev
   # Development properties
   app:
     url: http://localhost:8080
   logging:
     level:
       root: DEBUG
   
   ---
   spring:
     config:
       activate:
         on-profile: prod
   # Production properties
   app:
     url: https://production.example.com
   logging:
     level:
       root: WARN
   ```

4. **@Profile Annotation**:
   ```java
   @Configuration
   @Profile("dev")
   public class DevConfig {
       @Bean
       public DataSource dataSource() {
           return new EmbeddedDatabaseBuilder()
               .setType(EmbeddedDatabaseType.H2)
               .build();
       }
   }
   
   @Configuration
   @Profile("prod")
   public class ProdConfig {
       @Bean
       public DataSource dataSource() {
           DriverManagerDataSource ds = new DriverManagerDataSource();
           ds.setUrl("jdbc:mysql://production-db:3306/myapp");
           // Set other properties
           return ds;
       }
   }
   ```

5. **Profile-Specific Beans**:
   ```java
   @Component
   public class AppConfig {
       
       @Bean
       @Profile("dev")
       public EmailService devEmailService() {
           return new MockEmailService();
       }
       
       @Bean
       @Profile("prod")
       public EmailService prodEmailService() {
           return new SmtpEmailService();
       }
   }
   ```

6. **@ActiveProfiles in Tests**:
   ```java
   @SpringBootTest
   @ActiveProfiles("test")
   public class OrderServiceTest {
       // Tests run with test profile
   }
   ```

7. **Configuration Properties Class**:
   ```java
   @Component
   @ConfigurationProperties(prefix = "app")
   @Validated
   public class AppProperties {
       private String url;
       private String name;
       
       // Getters and setters
   }
   
   // Usage
   @Service
   public class MyService {
       private final AppProperties properties;
       
       public MyService(AppProperties properties) {
           this.properties = properties;
       }
   }
   ```

### 14. What is AOP (Aspect-Oriented Programming) in Spring, and how does it work?

> **Why this is asked**: This tests your understanding of Spring's powerful cross-cutting concerns mechanism.

**AOP Overview:**
Aspect-Oriented Programming is a programming paradigm that allows separation of cross-cutting concerns (like logging, security, transactions) from the main business logic.

**Key AOP Concepts:**

1. **Aspect**: A modularization of a concern that cuts across multiple classes
2. **Join Point**: A point during program execution (method execution, exception handling)
3. **Advice**: Action taken at a particular join point (before, after, around)
4. **Pointcut**: Expression that matches join points
5. **Weaving**: Process of linking aspects with application objects

**Spring AOP Implementation:**
Spring uses proxy-based AOP implementation: it creates proxy objects to implement the aspects.

**Example - Logging Aspect:**
```java
@Aspect
@Component
public class LoggingAspect {
    
    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);
    
    // Pointcut that matches all repository methods
    @Pointcut("execution(* com.example.repository.*.*(..))")
    public void repositoryMethods() {}
    
    // Advice that logs before method execution
    @Before("repositoryMethods()")
    public void logBefore(JoinPoint joinPoint) {
        logger.info("Executing: {} with arguments: {}", 
            joinPoint.getSignature().getName(), 
            Arrays.toString(joinPoint.getArgs()));
    }
    
    // Advice that logs after method execution
    @AfterReturning(pointcut = "repositoryMethods()", returning = "result")
    public void logAfterReturning(JoinPoint joinPoint, Object result) {
        logger.info("Method {} returned: {}", 
            joinPoint.getSignature().getName(), 
            result);
    }
    
    // Advice that logs exceptions
    @AfterThrowing(pointcut = "repositoryMethods()", throwing = "error")
    public void logAfterThrowing(JoinPoint joinPoint, Throwable error) {
        logger.error("Exception in {}.{}() with cause: {}", 
            joinPoint.getSignature().getDeclaringTypeName(),
            joinPoint.getSignature().getName(), 
            error.getCause() != null ? error.getCause() : "NULL");
    }
    
    // Around advice - provides complete control
    @Around("execution(* com.example.service.*.*(..))")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        
        try {
            Object result = joinPoint.proceed();
            long executionTime = System.currentTimeMillis() - start;
            
            logger.info("{} executed in {}ms", 
                joinPoint.getSignature(), executionTime);
                
            return result;
        } catch (Exception e) {
            logger.error("Exception during method execution", e);
            throw e;
        }
    }
}
```

**Common Use Cases for AOP:**
1. **Logging**: Capture method entry/exit, parameters, and execution time
2. **Security**: Check permissions before method execution
3. **Transactions**: Begin, commit, or rollback transactions around method execution
4. **Caching**: Cache method results
5. **Error Handling**: Provide consistent exception handling

**Spring AOP Annotations:**
- **@Aspect**: Declares the class as an aspect
- **@Pointcut**: Defines a pointcut expression
- **@Before**: Executes advice before the join point
- **@After**: Executes advice after the join point (regardless of outcome)
- **@AfterReturning**: Executes after successful completion
- **@AfterThrowing**: Executes if method throws an exception
- **@Around**: Most powerful advice, surrounds the join point execution

**AOP Performance Considerations:**
- Spring AOP uses runtime proxies, which can impact performance
- Limit the number of pointcuts to what's necessary
- Consider using AspectJ for more complex scenarios or better performance

### 24. What are the main features of Spring Framework, and how is AOP used for logging with annotations?

> **Why this is asked**: This assesses your overall understanding of Spring and practical implementation of AOP.

**Main Spring Framework Features:**

1. **Dependency Injection (DI)**: Core feature for managing component dependencies
2. **Aspect-Oriented Programming (AOP)**: Handle cross-cutting concerns
3. **Spring MVC**: Web application framework
4. **Spring Data**: Simplified data access layer
5. **Spring Security**: Authentication and authorization
6. **Spring Boot**: Simplified configuration and deployment
7. **Transaction Management**: Declarative transaction handling
8. **Testing Support**: Framework for unit and integration testing
9. **Spring Cloud**: Tools for distributed systems and microservices
10. **Event Handling**: Application event system

**Using AOP for Logging with Annotations:**

1. **Create a Custom Annotation**:
```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {}
```

2. **Implement the Aspect**:
```java
@Aspect
@Component
public class LoggingAspect {
    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);
    
    @Around("@annotation(com.example.annotation.LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        String methodName = joinPoint.getSignature().getName();
        String className = joinPoint.getSignature().getDeclaringTypeName();
        
        logger.info("Starting execution of {}.{}", className, methodName);
        
        Object result = joinPoint.proceed();
        
        long executionTime = System.currentTimeMillis() - startTime;
        logger.info("{}.{} executed in {}ms", className, methodName, executionTime);
        
        return result;
    }
}
```

3. **Enable AOP in Configuration**:
```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
    // Configuration beans
}
```

4. **Apply the Annotation to Methods**:
```java
@Service
public class UserService {
    
    @LogExecutionTime
    public User findUserById(Long id) {
        // Implementation
    }
    
    @LogExecutionTime
    public List<User> findAllUsers() {
        // Implementation
    }
}
```

**Common Patterns for Logging with AOP:**

1. **Method Entry/Exit Logging**:
```java
@Before("execution(* com.example.service.*.*(..))")
public void logMethodEntry(JoinPoint joinPoint) {
    logger.info("Entering: {}", joinPoint.getSignature());
}

@After("execution(* com.example.service.*.*(..))")
public void logMethodExit(JoinPoint joinPoint) {
    logger.info("Exiting: {}", joinPoint.getSignature());
}
```

2. **Exception Logging**:
```java
@AfterThrowing(pointcut = "execution(* com.example.service.*.*(..))", throwing = "ex")
public void logException(JoinPoint joinPoint, Exception ex) {
    logger.error("Exception in {}: {}", joinPoint.getSignature(), ex.getMessage());
}
```

3. **Parameter Logging**:
```java
@Before("execution(* com.example.service.*.*(..))")
public void logParameters(JoinPoint joinPoint) {
    Object[] args = joinPoint.getArgs();
    logger.info("Method: {} called with arguments: {}", 
        joinPoint.getSignature(), 
        Arrays.toString(args));
}
```
