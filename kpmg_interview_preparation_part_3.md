# KPMG Technical Interview Preparation - Part 3

## Advanced Java and System Design Questions

### 10. Functional Interface in Java 8 with Example
**Answer:**

A functional interface in Java is an interface that contains exactly one abstract method. They are the foundation for lambda expressions in Java 8. Java 8 introduced several built-in functional interfaces in the `java.util.function` package.

**Key Characteristics:**
- Contains exactly one abstract method
- Can have any number of default or static methods
- Can be annotated with `@FunctionalInterface` (optional but recommended)

**Built-in Functional Interfaces:**
1. `Function<T, R>`: Takes an input of type T and returns a result of type R
2. `Predicate<T>`: Takes an input of type T and returns a boolean
3. `Consumer<T>`: Takes an input of type T and returns no result (void)
4. `Supplier<T>`: Takes no input and returns a result of type T

**Example: Creating and Using a Custom Functional Interface**
```java
import java.util.Arrays;
import java.util.List;

// Custom functional interface
@FunctionalInterface
interface StringProcessor {
    String process(String input);
    
    // Can have default methods
    default String processWithPrefix(String input, String prefix) {
        return prefix + process(input);
    }
    
    // Can have static methods
    static StringProcessor getReversedProcessor() {
        return str -> new StringBuilder(str).reverse().toString();
    }
}

public class FunctionalInterfaceExample {
    public static void main(String[] args) {
        // Implementing functional interface using lambda expression
        StringProcessor toUpperCase = str -> str.toUpperCase();
        
        // Using the functional interface
        System.out.println(toUpperCase.process("hello")); // Output: HELLO
        
        // Using default method
        System.out.println(toUpperCase.processWithPrefix("world", "Processed: ")); // Output: Processed: WORLD
        
        // Using static method
        StringProcessor reverser = StringProcessor.getReversedProcessor();
        System.out.println(reverser.process("Java")); // Output: avaJ
        
        // Example with built-in functional interfaces
        List<String> names = Arrays.asList("John", "Alex", "Richard", "Mary", "Anna");
        
        // Using Predicate
        System.out.println("Names longer than 4 characters:");
        names.stream()
            .filter(name -> name.length() > 4) // Predicate
            .forEach(System.out::println);
        
        // Using Function
        System.out.println("\nLength of each name:");
        names.stream()
            .map(name -> name + ": " + name.length()) // Function
            .forEach(System.out::println);
    }
}
```

### 11. Reduce Method with a Small Example
**Answer:**

The `reduce` method in Java 8 Stream API is a terminal operation that applies a binary operator to each element in the stream to reduce the stream to a single value. It's particularly useful for operations like summing, finding maximum/minimum, or concatenating.

**Syntax:**
```java
Optional<T> reduce(BinaryOperator<T> accumulator)
T reduce(T identity, BinaryOperator<T> accumulator)
```

**Key Concepts:**
- **Identity**: The initial value or starting point for the reduction operation
- **Accumulator**: A function that takes two parameters - partial result and next element

**Example: Using Reduce for Various Operations**
```java
import java.util.Arrays;
import java.util.List;
import java.util.Optional;

public class ReduceMethodExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // Example 1: Sum of numbers using reduce
        int sum = numbers.stream()
                .reduce(0, (a, b) -> a + b);
        // Alternatively: .reduce(0, Integer::sum);
        System.out.println("Sum: " + sum); // Output: 15
        
        // Example 2: Multiplication of numbers using reduce
        int product = numbers.stream()
                .reduce(1, (a, b) -> a * b);
        System.out.println("Product: " + product); // Output: 120
        
        // Example 3: Finding maximum using reduce
        Optional<Integer> max = numbers.stream()
                .reduce(Integer::max);
        max.ifPresent(value -> System.out.println("Max: " + value)); // Output: 5
        
        // Example 4: String concatenation using reduce
        List<String> words = Arrays.asList("Java", "is", "awesome");
        String sentence = words.stream()
                .reduce("", (result, word) -> result + " " + word);
        System.out.println("Concatenated:" + sentence); // Output: Concatenated: Java is awesome
        
        // Example 5: Custom object reduction
        List<Employee> employees = Arrays.asList(
                new Employee("John", 5000),
                new Employee("Alice", 6000),
                new Employee("Bob", 4500)
        );
        
        double totalSalary = employees.stream()
                .map(Employee::getSalary)
                .reduce(0.0, Double::sum);
        
        System.out.println("Total salary: $" + totalSalary); // Output: Total salary: $15500.0
    }
    
    static class Employee {
        private String name;
        private double salary;
        
        public Employee(String name, double salary) {
            this.name = name;
            this.salary = salary;
        }
        
        public double getSalary() {
            return salary;
        }
        
        public String getName() {
            return name;
        }
    }
}
```

**Step-by-Step Execution of a Reduce Operation:**
For `numbers.stream().reduce(0, (a, b) -> a + b)` with numbers [1, 2, 3, 4, 5]:
1. Start with identity value a=0
2. First element b=1, compute 0+1=1, a becomes 1
3. Second element b=2, compute 1+2=3, a becomes 3
4. Third element b=3, compute 3+3=6, a becomes 6
5. Fourth element b=4, compute 6+4=10, a becomes 10
6. Fifth element b=5, compute 10+5=15, a becomes 15
7. Return final value 15

### 12. Synchronized Map vs ConcurrentHashMap (Null Keys Allowed?)
**Answer:**

**Synchronized Map:**
- Created using `Collections.synchronizedMap(new HashMap<>())`
- Uses intrinsic locking (synchronized blocks) on the entire map
- One thread can access the map at a time (coarse-grained locking)
- Allows null keys and values (depends on the underlying map implementation)
- Lower concurrency with higher contention
- Performance degrades with multiple threads
- Better for scenarios with more reads than writes
- Iteration requires external synchronization to avoid ConcurrentModificationException

**ConcurrentHashMap:**
- Specifically designed for concurrent access
- Uses segment-level locking (pre-Java 8) or node-level locking (Java 8+)
- Multiple threads can access different segments simultaneously (fine-grained locking)
- **Does not allow null keys or values**
- Higher concurrency with lower contention
- Better performance with multiple threads
- Designed for scenarios with many concurrent reads and writes
- Iteration is safe without external synchronization (snapshot view)

**Null Key/Value Support:**
- **HashMap**: Allows null keys and values
- **SynchronizedMap (on HashMap)**: Allows null keys and values
- **ConcurrentHashMap**: Does NOT allow null keys or values
- **Hashtable**: Does NOT allow null keys or values

**Why ConcurrentHashMap Doesn't Allow Null:**
The designers of ConcurrentHashMap made this decision because in a concurrent environment, a null return value could mean either:
1. The key is not present in the map, OR
2. The key is explicitly mapped to null

This ambiguity makes it difficult to implement methods like `containsKey()` or `get()` efficiently in a concurrent context.

**Code Example:**
```java
import java.util.*;
import java.util.concurrent.*;

public class MapConcurrencyExample {
    public static void main(String[] args) {
        // Synchronized Map example
        Map<String, String> synchronizedMap = Collections.synchronizedMap(new HashMap<>());
        
        // This works fine
        synchronizedMap.put(null, "Value for null key");
        synchronizedMap.put("Key for null value", null);
        
        System.out.println("Synchronized Map with null key: " + synchronizedMap.get(null));
        System.out.println("Synchronized Map with null value: " + synchronizedMap.get("Key for null value"));
        
        // ConcurrentHashMap example
        Map<String, String> concurrentMap = new ConcurrentHashMap<>();
        
        // These would throw NullPointerException
        try {
            concurrentMap.put(null, "Value");
            System.out.println("This line won't execute");
        } catch (NullPointerException e) {
            System.out.println("ConcurrentHashMap doesn't allow null keys: " + e.getMessage());
        }
        
        try {
            concurrentMap.put("Key", null);
            System.out.println("This line won't execute");
        } catch (NullPointerException e) {
            System.out.println("ConcurrentHashMap doesn't allow null values: " + e.getMessage());
        }
        
        // Performance demonstration (simplified)
        System.out.println("\nPerformance comparison:");
        
        Map<Integer, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());
        Map<Integer, Integer> concMap = new ConcurrentHashMap<>();
        
        // Fill maps with some data
        for (int i = 0; i < 10000; i++) {
            syncMap.put(i, i);
            concMap.put(i, i);
        }
        
        // Compare performance with multiple threads
        Runnable syncMapTask = () -> {
            for (int i = 0; i < 10000; i++) {
                syncMap.get(i % 1000);
            }
        };
        
        Runnable concMapTask = () -> {
            for (int i = 0; i < 10000; i++) {
                concMap.get(i % 1000);
            }
        };
        
        // Test synchronized map
        long syncStart = System.currentTimeMillis();
        Thread[] syncThreads = new Thread[10];
        for (int i = 0; i < 10; i++) {
            syncThreads[i] = new Thread(syncMapTask);
            syncThreads[i].start();
        }
        
        for (Thread t : syncThreads) {
            try { t.join(); } catch (InterruptedException e) { }
        }
        long syncTime = System.currentTimeMillis() - syncStart;
        
        // Test concurrent map
        long concStart = System.currentTimeMillis();
        Thread[] concThreads = new Thread[10];
        for (int i = 0; i < 10; i++) {
            concThreads[i] = new Thread(concMapTask);
            concThreads[i].start();
        }
        
        for (Thread t : concThreads) {
            try { t.join(); } catch (InterruptedException e) { }
        }
        long concTime = System.currentTimeMillis() - concStart;
        
        System.out.println("SynchronizedMap time: " + syncTime + "ms");
        System.out.println("ConcurrentHashMap time: " + concTime + "ms");
    }
}
```

### 13. Difference Between ListIterator and Iterator
**Answer:**

Iterator and ListIterator are both interfaces in Java for iterating through collections, but ListIterator provides more functionality specifically for List implementations.

**Iterator:**
- Can traverse elements in forward direction only
- Available for all Collection classes (List, Set, Queue, etc.)
- Methods: `hasNext()`, `next()`, `remove()`
- Cannot add elements during iteration
- Cannot replace elements during iteration
- No index information available

**ListIterator:**
- Can traverse elements in both forward and backward directions
- Available only for List implementations
- All Iterator methods plus: `hasPrevious()`, `previous()`, `nextIndex()`, `previousIndex()`, `add()`, `set()`
- Can add new elements during iteration
- Can replace elements during iteration
- Provides index information about elements

**Code Example:**
```java
import java.util.*;

public class IteratorExample {
    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Orange", "Mango", "Grapes"));
        
        System.out.println("Original list: " + fruits);
        
        // Using Iterator
        System.out.println("\n--- Using Iterator ---");
        Iterator<String> iterator = fruits.iterator();
        System.out.println("Forward traversal:");
        while (iterator.hasNext()) {
            String fruit = iterator.next();
            System.out.println(fruit);
            
            // Remove elements that start with 'A'
            if (fruit.startsWith("A")) {
                iterator.remove();
            }
            
            // This would throw UnsupportedOperationException
            // iterator.add("New Fruit");
        }
        
        // Cannot go backwards with iterator
        // while (iterator.hasPrevious()) {} // No such method
        
        System.out.println("List after Iterator operations: " + fruits);
        
        // Using ListIterator
        System.out.println("\n--- Using ListIterator ---");
        ListIterator<String> listIterator = fruits.listIterator();
        
        System.out.println("Forward traversal with index:");
        while (listIterator.hasNext()) {
            int index = listIterator.nextIndex();
            String fruit = listIterator.next();
            System.out.println("Index: " + index + ", Fruit: " + fruit);
            
            // Replace elements that contain 'an'
            if (fruit.contains("an")) {
                listIterator.set(fruit.toUpperCase());
            }
            
            // Add new element after 'Orange'
            if (fruit.equals("Orange")) {
                listIterator.add("Kiwi");
            }
        }
        
        System.out.println("\nBackward traversal with index:");
        while (listIterator.hasPrevious()) {
            int index = listIterator.previousIndex();
            String fruit = listIterator.previous();
            System.out.println("Index: " + index + ", Fruit: " + fruit);
        }
        
        System.out.println("List after ListIterator operations: " + fruits);
    }
}
```

### 14. Internal Working of HashMap (Technical Explanation)
**Answer:**

HashMap in Java is a data structure that implements the Map interface and stores data in key-value pairs. Here's a simplified but technical explanation of how HashMap works internally:

**1. Core Components:**
- **Buckets**: An array that stores the entries
- **Entry/Node**: Objects that store key-value pairs
- **Hash Function**: Converts keys to integer hash codes
- **Load Factor**: Determines when to resize (default is 0.75)
- **Initial Capacity**: Initial size of the bucket array (default is 16)

**2. Storage Mechanism:**
- HashMap maintains an array of "buckets" (internally called table)
- Each bucket can store multiple entries using a linked list (pre-Java 8) or a balanced tree (Java 8+)
- The index in the array is determined by the hash code of the key

**3. Put Operation (Storing a Key-Value Pair):**
```
Step 1: Calculate hash code of the key
Step 2: Convert hash code to an index in the array:
        index = hash & (n-1) where n is the size of the bucket array
Step 3: Check if bucket at index is empty:
        - If empty, create new entry and place it there
        - If not empty (collision), either:
          a) Traverse the linked list, replace if key exists
          b) Add to the end of the list if key doesn't exist
Step 4: Check if load factor threshold is exceeded:
        - If yes, resize the bucket array (usually doubles in size)
        - Rehash all existing entries to place them in new positions
```

**4. Get Operation (Retrieving a Value by Key):**
```
Step 1: Calculate hash code of the key
Step 2: Calculate index using hash & (n-1)
Step 3: Go to the bucket at that index
Step 4: If bucket has entries:
        - Traverse the linked list/tree
        - Compare keys using equals() method
        - Return value if key matches
Step 5: Return null if key not found
```

**5. Collision Handling:**
- **Chaining**: Multiple entries with the same index are stored in a linked list
- **Tree Conversion**: In Java 8+, if a bucket has more than 8 entries (TREEIFY_THRESHOLD), the linked list is converted to a balanced tree (Red-Black Tree) for O(log n) lookup instead of O(n)
- **Tree Conversion Threshold**: Only happens if the bucket contains at least 8 entries AND the total capacity is at least 64

**6. Resizing:**
- When the number of entries exceeds load factor * current capacity
- Creates a new array of double the size
- Rehashes all existing entries to new positions
- Expensive operation - O(n) time complexity

**7. Time Complexity:**
- Average case: O(1) for put, get, remove operations
- Worst case (many collisions): O(log n) with trees, O(n) with lists

**8. Java 8 Enhancements:**
- Replaced linked lists with balanced trees for buckets with many collisions
- Improved performance for high collision scenarios

**Code Walkthrough:**
```java
import java.util.HashMap;
import java.util.Map;

public class HashMapInternalDemo {
    public static void main(String[] args) {
        // Create HashMap with initial capacity 16, load factor 0.75
        Map<String, Integer> map = new HashMap<>();
        
        // Put operation
        map.put("One", 1);   // hash("One") & (16-1) = bucket index
        
        // What happens internally during put:
        // 1. Compute hash for "One"
        // 2. Calculate index in the bucket array
        // 3. Check if bucket is empty
        // 4. Create Entry object with key="One", value=1, hash code
        // 5. Place Entry in the bucket
        
        // Another put with collision (simplified example)
        map.put("OneMore", 11);  // Assume this has the same bucket index
        
        // What happens on collision:
        // 1. New Entry created (key="OneMore", value=11)
        // 2. Entry added to the linked list in the same bucket
        
        // Get operation
        int value = map.get("One");  // Returns 1
        
        // What happens during get:
        // 1. Compute hash for "One"
        // 2. Find bucket index
        // 3. Traverse linked list/tree in that bucket
        // 4. Compare key using equals()
        // 5. Return value if found
        
        // Demonstrating capacity growth
        for (int i = 0; i < 13; i++) {  // Add more elements
            map.put("Key" + i, i);
        }
        
        // After 12 elements, size becomes 13
        // 13 > 16 * 0.75 (12), so HashMap will resize to 32 buckets
        // All existing entries will be rehashed and redistributed
    }
}
```

### 15. What if We Insert a Null Key in HashMap? (Cross-questions)
**Answer:**

**HashMap with Null Key:**
- HashMap allows exactly one null key
- The null key is stored in bucket 0 (since hash code of null is 0)
- Multiple calls to put(null, value) will overwrite the previous value
- get(null) will return the value associated with the null key

**Code Example:**
```java
import java.util.HashMap;
import java.util.Map;

public class NullKeyInHashMap {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        
        // Insert null key
        map.put(null, "Value for null key");
        System.out.println("Value for null key: " + map.get(null));
        
        // Overwrite null key value
        map.put(null, "New value for null key");
        System.out.println("Updated value for null key: " + map.get(null));
        
        // Check containsKey for null
        System.out.println("Contains null key? " + map.containsKey(null));
        
        // Remove null key
        String removed = map.remove(null);
        System.out.println("Removed value: " + removed);
        System.out.println("After removal, contains null key? " + map.containsKey(null));
        
        // Normal operations still work
        map.put("Regular key", "Regular value");
        System.out.println("Regular value: " + map.get("Regular key"));
    }
}
```

### 19. What is Microservices Architecture and its Features
**Answer:**

Microservices is an architectural style that structures an application as a collection of small, loosely coupled, and independently deployable services. Each service is focused on a specific business capability and communicates with other services through well-defined APIs.

#### Key Features of Microservices

1. **Service Independence**
   - Each service can be developed, deployed, operated, and scaled independently
   - Services can be written in different programming languages and use different data storage technologies

2. **Decentralized Data Management**
   - Each service manages its own database
   - Different services can use different database types (SQL, NoSQL, etc.) based on their requirements
   - Avoids single points of failure

3. **Bounded Contexts**
   - Services are organized around business capabilities, not technical layers
   - Clear boundaries between services following Domain-Driven Design principles

4. **Smart Endpoints, Dumb Pipes**
   - Services communicate through simple, technology-agnostic protocols (REST, message queues)
   - Complex logic resides within the services, not in the communication layer

5. **Resilience and Fault Isolation**
   - Failure of one service doesn't affect the entire system
   - Enables implementation of circuit breakers, retries, and other fault tolerance patterns

6. **Infrastructure Automation**
   - CI/CD pipelines for automated testing and deployment
   - Infrastructure as Code for consistent environments
   - Containerization and orchestration tools like Docker and Kubernetes

7. **Evolutionary Design**
   - Services can evolve independently at different speeds
   - Facilitates continuous delivery and deployment

#### Advantages of Microservices

1. **Scalability**: Individual services can be scaled independently based on demand
2. **Technology Flexibility**: Different services can use different technology stacks
3. **Resilience**: Failure in one service doesn't bring down the entire application
4. **Development Velocity**: Smaller teams can work independently on different services
5. **Targeted Deployments**: Updates can be made to specific services without redeploying everything
6. **Better Fault Isolation**: Easier to identify and isolate issues

#### Disadvantages of Microservices

1. **Distributed System Complexity**: Debugging, testing, and monitoring become more complex
2. **Network Latency**: Communication between services adds overhead
3. **Data Consistency Challenges**: Maintaining consistency across services is difficult
4. **Operational Overhead**: Managing many services requires sophisticated DevOps practices
5. **Service Coordination**: Orchestrating transactions across services is complex
6. **Learning Curve**: Requires expertise in distributed systems and DevOps

### 20. Why Choose Microservices (or Not)
**Answer:**

#### Reasons to Choose Microservices

1. **Large, Complex Applications**
   - When the application is too complex to understand and maintain as a monolith
   - When different parts of the application have vastly different resource requirements

2. **Team Structure**
   - When you have multiple teams that need to work independently
   - When teams are geographically distributed

3. **Scaling Requirements**
   - When different components need to scale independently
   - When you need extreme scalability for specific functions

4. **Technology Diversity**
   - When different parts of the application would benefit from different technologies
   - When you need to gradually modernize a legacy application

5. **Frequent Deployments**
   - When you need to release updates to parts of the application frequently
   - When you want to reduce the risk associated with each deployment

#### Reasons to Avoid Microservices

1. **Small, Simple Applications**
   - Microservices add unnecessary complexity for small applications
   - The overhead may not be justified by the benefits

2. **Limited DevOps Capabilities**
   - Without mature DevOps practices, microservices can be difficult to manage
   - Requires expertise in deployment automation, monitoring, and container orchestration

3. **Team Size and Structure**
   - Small teams may struggle to maintain multiple services
   - Teams without clear boundaries might create poorly designed service boundaries

4. **Strong Data Consistency Requirements**
   - Applications requiring strong transactional consistency across components
   - When atomic operations across multiple data stores are essential

5. **Early Stage Startups**
   - When speed to market and pivoting are more important than scalability
   - When the domain is not well understood yet

### 21. Implementing Microservices Architecture
**Answer:**

Implementing a microservices architecture involves several key steps and considerations:

#### 1. Service Decomposition

**Approaches:**
- **Domain-Driven Design (DDD)**: Identify bounded contexts and aggregate roots
- **Business Capability**: Organize around business functions
- **Subdomain**: Decompose by subdomain
- **Decompose by Transactions**: Group by related transactions
- **Strangler Pattern**: Gradually migrate from monolith to microservices

**Example:**
```
E-commerce Application Decomposition:
- Product Catalog Service
- Inventory Service
- Order Management Service
- Customer Service
- Payment Service
- Shipping Service
- Notification Service
```

#### 2. Inter-Service Communication

**Synchronous Communication:**
- REST APIs (HTTP/HTTPS)
- gRPC (Protocol Buffers)
- GraphQL

**Asynchronous Communication:**
- Message Queues (RabbitMQ, Apache Kafka)
- Event Sourcing
- Publish-Subscribe Pattern

**Example REST API:**
```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    private final OrderService orderService;
    private final RestTemplate restTemplate;
    
    @Autowired
    public OrderController(OrderService orderService, RestTemplate restTemplate) {
        this.orderService = orderService;
        this.restTemplate = restTemplate;
    }
    
    @PostMapping
    public ResponseEntity<Order> createOrder(@RequestBody OrderRequest request) {
        // Check inventory availability through Inventory Service
        InventoryResponse response = restTemplate.getForObject(
            "http://inventory-service/api/inventory/check?productId=" + request.getProductId(),
            InventoryResponse.class
        );
        
        if (response.isAvailable()) {
            Order order = orderService.createOrder(request);
            return ResponseEntity.ok(order);
        } else {
            return ResponseEntity.badRequest().build();
        }
    }
}
```

#### 3. Data Management

**Strategies:**
- Database per Service
- Shared Database (avoid if possible)
- Event Sourcing + CQRS
- Saga Pattern for distributed transactions

**Example:**
```java
// Order Service Data Model
@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String customerEmail;
    private BigDecimal totalAmount;
    private String status;
    
    @OneToMany(cascade = CascadeType.ALL)
    private List<OrderItem> items;
    
    // Getters and setters
}

// Inventory Service Data Model (separate database)
@Entity
@Table(name = "inventory")
public class InventoryItem {
    @Id
    private String productId;
    
    private int quantity;
    private String warehouseLocation;
    
    // Getters and setters
}
```

#### 4. API Gateway

**Functions:**
- Routing
- Load Balancing
- Authentication/Authorization
- Rate Limiting
- Request Transformation
- Analytics

**Example with Spring Cloud Gateway:**
```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: product-service
          uri: lb://product-service
          predicates:
            - Path=/api/products/**
          filters:
            - StripPrefix=1
            - name: CircuitBreaker
              args:
                name: productServiceCircuitBreaker
                fallbackUri: forward:/fallback/products
        
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20
```

#### 5. Service Discovery

**Options:**
- Client-side Discovery (Eureka, Consul)
- Server-side Discovery (Kubernetes Service, AWS ALB)
- DNS-based Discovery

**Example with Spring Eureka:**
```java
// Service Registration
@SpringBootApplication
@EnableEurekaClient
public class ProductServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProductServiceApplication.class, args);
    }
}

// Service Discovery Configuration
@Configuration
public class RestTemplateConfig {
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

// Using discovered service
@Service
public class OrderServiceImpl implements OrderService {
    private final RestTemplate restTemplate;
    
    @Autowired
    public OrderServiceImpl(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }
    
    public void processOrder(Order order) {
        // RestTemplate automatically resolves 'product-service' using Eureka
        ProductInfo product = restTemplate.getForObject(
            "http://product-service/api/products/" + order.getProductId(),
            ProductInfo.class
        );
        
        // Process order with product info
    }
}
```

### 22. API Gateways and Their Working
**Answer:**

An API Gateway is a server that acts as an entry point into a microservices architecture. It serves as a reverse proxy, routing requests to appropriate services, and can provide cross-cutting functionality like authentication, monitoring, and load balancing.

#### Core Functions of API Gateways

1. **Request Routing**
   - Routes client requests to appropriate backend services
   - Can use path-based, header-based, or content-based routing
   - Handles service versioning

2. **API Composition**
   - Aggregates responses from multiple services
   - Reduces client-side complexity and network overhead
   - Implements the Backend for Frontend (BFF) pattern

3. **Protocol Translation**
   - Transforms between different protocols (e.g., HTTP, gRPC, WebSockets)
   - Enables clients to use one protocol while services use another

4. **Authentication and Authorization**
   - Centralizes security concerns
   - Validates tokens and credentials
   - Implements OAuth, JWT, or other authentication mechanisms

5. **Rate Limiting and Throttling**
   - Protects backend services from overload
   - Implements various rate limiting strategies
   - Can prioritize certain clients or operations

6. **Monitoring and Analytics**
   - Collects metrics on API usage
   - Enables tracing of requests across services
   - Provides insights for capacity planning

7. **Caching**
   - Caches responses to reduce load on backend services
   - Improves response times for frequently requested data

8. **Circuit Breaking**
   - Prevents cascading failures
   - Redirects traffic away from failing services

#### How API Gateways Work

**Request Flow:**
1. Client sends a request to the API Gateway
2. Gateway authenticates and authorizes the request
3. Gateway applies rate limiting and other policies
4. Gateway transforms/routes the request to appropriate service(s)
5. Service(s) process the request and return response(s)
6. Gateway aggregates responses if needed
7. Gateway transforms the response for the client
8. Gateway returns the final response to the client

**Implementation Example (Spring Cloud Gateway):**

```java
@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            // Route to Product Service
            .route("product-service", r -> r.path("/api/products/**")
                .filters(f -> f
                    .rewritePath("/api/products/(?<segment>.*)", "/products/${segment}")
                    .addRequestHeader("X-Gateway-Source", "api-gateway")
                    .circuitBreaker(c -> c.setName("productCB")
                        .setFallbackUri("forward:/fallback/products")))
                .uri("lb://product-service"))
            
            // Route to Order Service with rate limiting
            .route("order-service", r -> r.path("/api/orders/**")
                .filters(f -> f
                    .rewritePath("/api/orders/(?<segment>.*)", "/orders/${segment}")
                    .requestRateLimiter(c -> c
                        .setRateLimiter(redisRateLimiter())
                        .setKeyResolver(userKeyResolver())))
                .uri("lb://order-service"))
            
            // Aggregated view across services
            .route("user-dashboard", r -> r.path("/api/user-dashboard/**")
                .filters(f -> f
                    .setPath("/dashboard")
                    .addRequestHeader("Aggregate", "true"))
                .uri("lb://dashboard-service"))
            .build();
    }
    
    @Bean
    public RedisRateLimiter redisRateLimiter() {
        return new RedisRateLimiter(10, 20); // 10 requests/second, burst of 20
    }
    
    @Bean
    KeyResolver userKeyResolver() {
        return exchange -> Mono.just(
            Optional.ofNullable(exchange.getRequest().getHeaders().getFirst("X-User-ID"))
                .orElse("anonymous"));
    }
    
    @Bean
    public Customizer<ReactiveResilience4JCircuitBreakerFactory> defaultCircuitBreakerCustomizer() {
        return factory -> factory.configureDefault(id -> new Resilience4JConfigBuilder(id)
            .circuitBreakerConfig(CircuitBreakerConfig.custom()
                .slidingWindowSize(10)
                .failureRateThreshold(50)
                .waitDurationInOpenState(Duration.ofSeconds(10))
                .build())
            .build());
    }
}
```

#### Popular API Gateway Solutions

1. **Spring Cloud Gateway**
   - Non-blocking, reactive API gateway
   - Built on Spring WebFlux
   - Integrated with Spring Cloud ecosystem

2. **Netflix Zuul**
   - JVM-based gateway
   - Used extensively in Netflix architecture
   - Synchronous (Zuul 1.x) and asynchronous (Zuul 2.x) options

3. **Kong**
   - Lua-based gateway running on NGINX
   - Plugin architecture for extensibility
   - Available as open-source and enterprise versions

4. **Amazon API Gateway**
   - Fully managed service in AWS
   - Integrates with Lambda, EC2, and other AWS services
   - Features like caching, throttling, and API key management

5. **Azure API Management**
   - Microsoft's API gateway solution
   - Features API versioning, documentation, and analytics
   - Integrates with Azure Functions and other Azure services

6. **Apigee**
   - Google Cloud's API management platform
   - Comprehensive API lifecycle management
   - Advanced analytics and monetization features

### 23. How to Ensure Data Consistency Between Microservices
**Answer:**

Data consistency across microservices is one of the most challenging aspects of a distributed architecture. In a microservices environment, the CAP theorem states that we can only achieve two out of three properties: Consistency, Availability, and Partition tolerance. In distributed systems, partition tolerance is typically non-negotiable, so we must make trade-offs between consistency and availability.

#### Approaches to Ensure Data Consistency

1. **Saga Pattern**
   - Sequence of local transactions where each transaction updates data within a single service
   - Each local transaction publishes an event that triggers the next local transaction
   - If a step fails, compensating transactions are executed to undo previous changes

   **Example (Choreography-based Saga):**
   ```java
   // Order Service
   @Service
   public class OrderService {
       private final KafkaTemplate<String, Event> kafkaTemplate;
       
       @Transactional
       public void createOrder(Order order) {
           // Save order with PENDING status
           orderRepository.save(order);
           
           // Publish order created event
           kafkaTemplate.send("order-events", new OrderCreatedEvent(order.getId(), order.getItems()));
       }
       
       @KafkaListener(topics = "payment-events")
       public void handlePaymentEvent(PaymentEvent event) {
           if (event.getType() == PaymentEvent.Type.PAYMENT_COMPLETED) {
               // Update order status to PAID
               Order order = orderRepository.findById(event.getOrderId()).orElseThrow();
               order.setStatus(OrderStatus.PAID);
               orderRepository.save(order);
               
           } else if (event.getType() == PaymentEvent.Type.PAYMENT_FAILED) {
               // Update order status to PAYMENT_FAILED
               Order order = orderRepository.findById(event.getOrderId()).orElseThrow();
               order.setStatus(OrderStatus.PAYMENT_FAILED);
               orderRepository.save(order);
           }
       }
   }
   
   // Payment Service
   @Service
   public class PaymentService {
       private final KafkaTemplate<String, Event> kafkaTemplate;
       
       @KafkaListener(topics = "order-events")
       public void handleOrderEvent(OrderCreatedEvent event) {
           try {
               // Process payment
               Payment payment = new Payment(event.getOrderId(), event.getAmount());
               paymentRepository.save(payment);
               
               // Publish payment completed event
               kafkaTemplate.send("payment-events", 
                   new PaymentEvent(PaymentEvent.Type.PAYMENT_COMPLETED, event.getOrderId()));
               
           } catch (Exception e) {
               // Publish payment failed event
               kafkaTemplate.send("payment-events", 
                   new PaymentEvent(PaymentEvent.Type.PAYMENT_FAILED, event.getOrderId()));
           }
       }
   }
   ```

2. **Event Sourcing with CQRS**
   - Store changes to application state as a sequence of events
   - Eventual consistency is achieved by replaying events
   - CQRS (Command Query Responsibility Segregation) separates read and write operations

   **Example:**
   ```java
   // Command side (write model)
   @Service
   public class OrderCommandService {
       private final EventStore eventStore;
       
       public void createOrder(CreateOrderCommand command) {
           // Create and store the event
           OrderCreatedEvent event = new OrderCreatedEvent(command.getOrderId(), 
               command.getCustomerId(), command.getItems());
           eventStore.save(event);
       }
       
       public void confirmPayment(ConfirmPaymentCommand command) {
           // Create and store the event
           PaymentConfirmedEvent event = new PaymentConfirmedEvent(
               command.getOrderId(), command.getPaymentId());
           eventStore.save(event);
       }
   }
   
   // Query side (read model)
   @Service
   public class OrderQueryService {
       private final OrderReadRepository repository;
       
       @EventListener
       public void on(OrderCreatedEvent event) {
           OrderReadModel order = new OrderReadModel();
           order.setId(event.getOrderId());
           order.setCustomerId(event.getCustomerId());
           order.setItems(event.getItems());
           order.setStatus("CREATED");
           repository.save(order);
       }
       
       @EventListener
       public void on(PaymentConfirmedEvent event) {
           OrderReadModel order = repository.findById(event.getOrderId()).orElseThrow();
           order.setStatus("PAID");
           order.setPaymentId(event.getPaymentId());
           repository.save(order);
       }
   }
   ```

3. **Two-Phase Commit (2PC)**
   - Ensure all services either commit or roll back a transaction
   - Consists of a prepare phase and a commit phase
   - Not recommended for microservices due to tight coupling and availability concerns

4. **Distributed Transaction Managers**
   - Tools like Atomikos, Narayana, or Bitronix that implement JTA
   - Coordinate transactions across multiple resources
   - Generally considered anti-pattern in microservices due to tight coupling

5. **Eventual Consistency with Polling**
   - Services periodically poll for updates from other services
   - Simple to implement but can be inefficient

   **Example:**
   ```java
   @Service
   public class InventoryReconciliationService {
       private final RestTemplate restTemplate;
       private final InventoryRepository inventoryRepository;
       
       @Scheduled(fixedRate = 60000) // Run every minute
       public void reconcileInventory() {
           // Get all orders with PAID status that haven't been processed
           ResponseEntity<List<Order>> response = restTemplate.exchange(
               "http://order-service/api/orders?status=PAID&processed=false",
               HttpMethod.GET, null, new ParameterizedTypeReference<List<Order>>() {});
           
           List<Order> orders = response.getBody();
           
           for (Order order : orders) {
               // Update inventory
               for (OrderItem item : order.getItems()) {
                   InventoryItem inventoryItem = inventoryRepository
                       .findById(item.getProductId())
                       .orElseThrow();
                   
                   inventoryItem.setQuantity(inventoryItem.getQuantity() - item.getQuantity());
                   inventoryRepository.save(inventoryItem);
               }
               
               // Mark order as processed
               restTemplate.put(
                   "http://order-service/api/orders/" + order.getId() + "/processed",
                   null);
           }
       }
   }
   ```

6. **Outbox Pattern**
   - Local database transaction that includes writing to an "outbox" table
   - Separate process reads from outbox and publishes events
   - Ensures at-least-once delivery of events

   **Example:**
   ```java
   @Service
   public class OrderService {
       private final OrderRepository orderRepository;
       private final OutboxRepository outboxRepository;
       
       @Transactional
       public void createOrder(Order order) {
           // Save the order
           orderRepository.save(order);
           
           // Create outbox message
           OutboxMessage message = new OutboxMessage();
           message.setAggregateId(order.getId().toString());
           message.setAggregateType("Order");
           message.setType("OrderCreated");
           message.setPayload(objectMapper.writeValueAsString(order));
           outboxRepository.save(message);
       }
   }
   
   @Component
   public class OutboxPublisher {
       private final OutboxRepository outboxRepository;
       private final KafkaTemplate<String, String> kafkaTemplate;
       
       @Scheduled(fixedRate = 5000) // Run every 5 seconds
       @Transactional
       public void publishOutboxMessages() {
           List<OutboxMessage> messages = outboxRepository.findByPublished(false);
           
           for (OutboxMessage message : messages) {
               kafkaTemplate.send(message.getAggregateType(), message.getPayload());
               message.setPublished(true);
               outboxRepository.save(message);
           }
       }
   }
   ```

#### Best Practices for Data Consistency

1. **Define Consistency Boundaries**
   - Identify which operations require strong consistency
   - Accept eventual consistency where appropriate

2. **Design for Idempotency**
   - Ensure operations can be safely retried multiple times
   - Use idempotency keys for APIs and message processors

3. **Implement Effective Error Handling**
   - Retry mechanisms with exponential backoff
   - Dead letter queues for failed messages
   - Alerting and monitoring for inconsistency

4. **Use Versioning**
   - Optimistic locking with version fields
   - Event versioning to handle schema evolution

5. **Consider Domain-Specific Solutions**
   - Some domains may have industry-specific patterns
   - Example: Ledger-based accounting systems with reconciliation

### 24. Circuit Breaker Pattern in Microservices
**Answer:**

The Circuit Breaker pattern is a design pattern used in microservices architecture to prevent cascading failures and provide fallback mechanisms when services fail. It works similar to an electrical circuit breaker - when a problem is detected, it "trips" to stop the flow and prevent further damage.

#### How Circuit Breakers Work

1. **Three States of a Circuit Breaker**
   - **Closed State**: Normal operation. Requests pass through to the service. Failures are counted.

   - **Open State**: When failure threshold is exceeded, the circuit opens. All requests fail fast without calling the service.
   - **Half-Open State**: After a timeout period, allows a limited number of requests to test if the service has recovered.

2. **Key Parameters**
   - **Failure Threshold**: Number or percentage of failures that trigger the circuit to open (e.g., 50% of 20 requests)
   - **Timeout Duration**: How long the circuit stays open before moving to half-open state
   - **Reset Timeout**: Time window for counting failures (e.g., last 10 seconds)
   - **Volume Threshold**: Minimum number of requests needed before tripping (avoid premature opening)

#### Implementation Using Resilience4j

Resilience4j is a lightweight fault tolerance library inspired by Netflix Hystrix but designed for Java 8 and functional programming.

**Example:**

```java
// Circuit Breaker Configuration
@Configuration
public class CircuitBreakerConfig {
    @Bean
    public CircuitBreakerRegistry circuitBreakerRegistry() {
        
        // Define custom configuration
        io.github.resilience4j.circuitbreaker.CircuitBreakerConfig config = io.github.resilience4j.circuitbreaker.CircuitBreakerConfig.custom()
            .failureRateThreshold(50)               // 50% failure rate to open circuit
            .waitDurationInOpenState(Duration.ofMillis(1000))  // 1 second wait before testing
            .slidingWindowType(SlidingWindowType.COUNT_BASED)
            .slidingWindowSize(10)                  // Consider last 10 calls
            .minimumNumberOfCalls(5)                // Minimum calls before calculating error rate
            .permittedNumberOfCallsInHalfOpenState(3) // Allow 3 calls in half-open state
            .recordExceptions(IOException.class, TimeoutException.class)
            .build();
        
        // Create registry with custom configuration
        return CircuitBreakerRegistry.of(config);
    }
}

// Service with Circuit Breaker
@Service
public class ProductService {
    private final RestTemplate restTemplate;
    private final CircuitBreaker circuitBreaker;
    
    public ProductService(RestTemplate restTemplate, CircuitBreakerRegistry registry) {
        this.restTemplate = restTemplate;
        this.circuitBreaker = registry.circuitBreaker("productService");
    }
    
    public Product getProductById(Long id) {
        // Decorate the function call with circuit breaker
        return Try.ofSupplier(CircuitBreaker.decorateSupplier(circuitBreaker, 
                () -> callProductService(id)))
            .recover(throwable -> getDefaultProduct(id)) // Fallback function
            .get();
    }
    
    private Product callProductService(Long id) {
        return restTemplate.getForObject(
            "http://product-service/api/products/" + id, 
            Product.class);
    }
    
    private Product getDefaultProduct(Long id) {
        // Return a default/cached product as fallback
        Product defaultProduct = new Product();
        defaultProduct.setId(id);
        defaultProduct.setName("Default Product");
        defaultProduct.setAvailable(false);
        return defaultProduct;
    }
}
```

#### Implementation Using Spring Cloud Circuit Breaker

Spring Cloud Circuit Breaker provides an abstraction across different circuit breaker implementations (Resilience4j, Hystrix, etc.).

**Example:**

```java
@Service
public class OrderService {
    private final RestTemplate restTemplate;
    private final CircuitBreakerFactory circuitBreakerFactory;
    
    public OrderService(RestTemplate restTemplate, CircuitBreakerFactory circuitBreakerFactory) {
        this.restTemplate = restTemplate;
        this.circuitBreakerFactory = circuitBreakerFactory;
    }
    
    public Order createOrder(OrderRequest request) {
        // Create circuit breaker
        CircuitBreaker circuitBreaker = circuitBreakerFactory.create("inventory");
        
        // Execute with circuit breaker
        return circuitBreaker.run(
            () -> {
                // Check inventory
                InventoryResponse response = restTemplate.getForObject(
                    "http://inventory-service/api/inventory/check?productId=" + request.getProductId(),
                    InventoryResponse.class);
                
                if (response.isAvailable()) {
                    // Create order if product is available
                    Order order = new Order();
                    order.setProductId(request.getProductId());
                    order.setQuantity(request.getQuantity());
                    order.setStatus("CREATED");
                    // Save order and return
                    return order;
                } else {
                    throw new ProductNotAvailableException("Product not available");
                }
            },
            throwable -> fallbackCreateOrder(request) // Fallback method
        );
    }
    
    private Order fallbackCreateOrder(OrderRequest request) {
        // Create a backorder or return an appropriate fallback
        Order backOrder = new Order();
        backOrder.setProductId(request.getProductId());
        backOrder.setQuantity(request.getQuantity());
        backOrder.setStatus("BACKORDERED"); // Indicate this is a backorder
        return backOrder;
    }
}
```

#### Circuit Breaker in API Gateway

Circuit breakers are often implemented at the API Gateway level to protect downstream services.

**Example with Spring Cloud Gateway:**

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: product-service
          uri: lb://product-service
          predicates:
            - Path=/api/products/**
          filters:
            - name: CircuitBreaker
              args:
                name: productServiceCircuitBreaker
                fallbackUri: forward:/fallback/products
```

```java
@RestController
public class FallbackController {
    @GetMapping("/fallback/products")
    public ResponseEntity<Map<String, String>> productServiceFallback() {
        Map<String, String> response = new HashMap<>();
        response.put("message", "Product service is currently unavailable");
        response.put("timestamp", new Date().toString());
        return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE).body(response);
    }
}
```

#### Benefits of Circuit Breakers

1. **Prevent Cascading Failures**: Stop failure in one service from affecting others
2. **Fast Failure**: Fail quickly without waiting for timeouts
3. **Graceful Degradation**: Provide fallback responses when services are down
4. **Self-Healing**: Automatically test for recovery
5. **Monitoring**: Provide metrics about service health

#### Considerations When Using Circuit Breakers

1. **Granularity**: Apply circuit breakers to specific operations, not entire services
2. **Threshold Tuning**: Balance between sensitivity and false positives
3. **Fallback Strategy**: Design meaningful fallbacks for critical operations
4. **Timeout Configuration**: Set appropriate timeouts for service calls
5. **Monitoring**: Track circuit breaker states for operational visibility

### 25. Security in Microservices
**Answer:**

Security in a microservices architecture is more complex than in monolithic applications due to the distributed nature and increased number of network interactions. A comprehensive security strategy for microservices encompasses multiple layers and approaches.

#### Key Security Challenges in Microservices

1. **Larger Attack Surface**
   - More network endpoints and communication channels
   - Multiple services that could be individually compromised

2. **Service-to-Service Communication**
   - Need for secure authentication between services
   - Risk of unauthorized services accessing internal APIs

3. **Distributed Data**
   - Sensitive data spread across multiple services
   - Consistent enforcement of data access policies

4. **Multiple Technology Stacks**
   - Varied security implementations across services
   - Potential for inconsistent security practices

5. **Dynamic Deployment**
   - Containers and instances that frequently change
   - Challenge in maintaining security boundaries

#### Comprehensive Microservices Security Strategy

1. **API Gateway Security**

   - **Authentication and Authorization**
     - Centralized authentication at the gateway
     - OAuth 2.0 and OpenID Connect implementation
     - JWT token validation and propagation

   - **Rate Limiting and Throttling**
     - Protection against DDoS attacks
     - Per-client or per-IP rate limits

   - **Request Validation**
     - Input validation and sanitization
     - Protection against injection attacks

   **Example with Spring Cloud Gateway:**
   ```java
   @Configuration
   public class SecurityConfig {
       @Bean
       SecurityWebFilterChain springSecurityFilterChain(ServerHttpSecurity http) {
           return http
               .authorizeExchange()
                   .pathMatchers("/public/**").permitAll()
                   .pathMatchers("/api/**").authenticated()
                   .anyExchange().authenticated()
               .and()
               .oauth2ResourceServer()
                   .jwt()
               .and()
               .csrf().disable()
               .build();
       }
       
       @Bean
       public SecurityGatewayFilterFactory securityGatewayFilterFactory(
               ReactiveAuthenticationManager authenticationManager) {
           return new SecurityGatewayFilterFactory(authenticationManager);
       }
   }
   ```

2. **Service-to-Service Authentication**

   - **Mutual TLS (mTLS)**
     - Services authenticate each other using certificates
     - Prevents unauthorized service communication

   - **Service Mesh (e.g., Istio, Linkerd)**
     - Transparently manage mTLS between services
     - Traffic encryption and authentication

   - **Client Certificates**
     - Each service has its own identity certificate
     - Certificate rotation and management

   **Example with Spring Boot and mTLS:**
   ```java
   @Configuration
   public class RestTemplateConfig {
       @Bean
       public RestTemplate restTemplate() throws Exception {
           // Load client certificate keystore
           KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
           keyStore.load(
               new FileInputStream("client-keystore.jks"), 
               "keystore-password".toCharArray());

           // Create SSL context with client certificate
           SSLContext sslContext = SSLContexts.custom()
               .loadKeyMaterial(keyStore, "key-password".toCharArray())
               .loadTrustMaterial(trustStore, new TrustSelfSignedStrategy())
               .build();

           // Create SSL connection factory
           SSLConnectionSocketFactory socketFactory = 
               new SSLConnectionSocketFactory(sslContext);

           // Create HTTP client with SSL support
           CloseableHttpClient httpClient = HttpClients.custom()
               .setSSLSocketFactory(socketFactory)
               .build();

           // Create RestTemplate with the custom HTTP client
           HttpComponentsClientHttpRequestFactory factory = 
               new HttpComponentsClientHttpRequestFactory(httpClient);

           return new RestTemplate(factory);
       }
   }
   ```

3. **Token-Based Authentication**

   - **JWT (JSON Web Tokens)**
     - Stateless authentication with signed tokens
     - Claims for authorization information

   - **Token Propagation**
     - Pass user context through service calls
     - Maintain end-user identity across services

   - **Token Validation**
     - Verify signatures and claims in each service
     - Check for token expiration and revocation

   **Example with Spring Security:**
   ```java
   @Configuration
   @EnableWebSecurity
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
               .authorizeRequests()
                   .antMatchers("/public/**").permitAll()
                   .anyRequest().authenticated()
               .and()
               .oauth2ResourceServer()
                   .jwt()
                       .jwtAuthenticationConverter(jwtAuthenticationConverter());
       }
       
       private JwtAuthenticationConverter jwtAuthenticationConverter() {
           JwtGrantedAuthoritiesConverter converter = new JwtGrantedAuthoritiesConverter();
           converter.setAuthoritiesClaimName("roles");
           converter.setAuthorityPrefix("ROLE_");
           
           JwtAuthenticationConverter jwtConverter = new JwtAuthenticationConverter();
           jwtConverter.setJwtGrantedAuthoritiesConverter(converter);
           return jwtConverter;
       }
   }
   
   // Service to propagate tokens
   @Service
   public class UserServiceClient {
       private final RestTemplate restTemplate;
       private final TokenRelayRequestInterceptor interceptor;
       
       public UserServiceClient(RestTemplate restTemplate, 
                               TokenRelayRequestInterceptor interceptor) {
           this.restTemplate = restTemplate;
           this.interceptor = interceptor;
           this.restTemplate.getInterceptors().add(interceptor);
       }
       
       public UserDetails getUserDetails(String userId) {
           return restTemplate.getForObject(
               "http://user-service/users/" + userId, UserDetails.class);
       }
   }
   
   @Component
   public class TokenRelayRequestInterceptor implements ClientHttpRequestInterceptor {
       @Override
       public ClientHttpResponse intercept(HttpRequest request, byte[] body, 
                                         ClientHttpRequestExecution execution) throws IOException {
           SecurityContext context = SecurityContextHolder.getContext();
           Authentication authentication = context.getAuthentication();
           
           if (authentication != null && authentication.getCredentials() instanceof Jwt) {
               Jwt jwt = (Jwt) authentication.getCredentials();
               request.getHeaders().add("Authorization", "Bearer " + jwt.getTokenValue());
           }
           
           return execution.execute(request, body);
       }
   }
   ```

4. **Secure Configuration Management**

   - **Externalized Configuration**
     - Keep secrets out of code and container images
     - Use configuration servers or secret managers

   - **Encryption**
     - Encrypt sensitive configuration values
     - Secure storage of encryption keys

   - **Runtime Injection**
     - Inject secrets at runtime, not build time
     - Use Kubernetes Secrets, Vault, or AWS Secrets Manager

   **Example with Spring Cloud Config and Vault:**
   ```yaml
   # bootstrap.yml
   spring:
     application:
       name: payment-service
     cloud:
       config:
         uri: http://config-server:8888
         fail-fast: true
       vault:
         host: vault
         port: 8200
         scheme: http
         authentication: TOKEN
         token: ${VAULT_TOKEN}
         kv:
           enabled: true
   ```

   ```java
   @RestController
   public class PaymentController {
       @Value("${payment.gateway.api-key}")
       private String apiKey;
       
       // The API key is injected from Vault at runtime
   }
   ```

5. **Network Security**

   - **Network Segmentation**
     - Create network zones for different security levels
     - Limit communication paths between services

   - **Ingress/Egress Filtering**
     - Control traffic in and out of the service mesh
     - Block unnecessary external connections

   - **Encryption in Transit**
     - TLS for all service communications
     - Certificate management and rotation

   **Example with Kubernetes Network Policies:**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: payment-service-policy
     namespace: production
   spec:
     podSelector:
       matchLabels:
         app: payment-service
     policyTypes:
     - Ingress
     - Egress
     ingress:
     - from:
       - podSelector:
           matchLabels:
             app: order-service
       ports:
       - protocol: TCP
         port: 8080
     egress:
     - to:
       - podSelector:
           matchLabels:
             app: database
       ports:
       - protocol: TCP
         port: 5432
     - to:
       - namespaceSelector:
           matchLabels:
             name: kube-system
         podSelector:
           matchLabels:
             k8s-app: kube-dns
       ports:
       - protocol: UDP
         port: 53
   ```

6. **Container Security**

   - **Image Scanning**
     - Check for vulnerabilities in container images
     - Implement policies to block insecure images

   - **Minimal Base Images**
     - Use distroless or minimal images
     - Reduce attack surface in containers

   - **Non-root Execution**
     - Run containers with non-privileged users
     - Apply principle of least privilege

   **Example with Dockerfile:**
   ```dockerfile
   # Start with minimal base image
   FROM openjdk:11-jre-slim
   
   # Create a non-root user
   RUN groupadd -r appuser && useradd -r -g appuser appuser
   
   # Set working directory
   WORKDIR /app
   
   # Copy application JAR
   COPY --chown=appuser:appuser target/service.jar /app/service.jar
   
   # Switch to non-root user
   USER appuser
   
   # Run with security options
   CMD ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app/service.jar"]
   ```

7. **Monitoring and Threat Detection**

   - **Centralized Logging**
     - Collect and analyze logs from all services
     - Look for suspicious patterns

   - **Real-time Alerting**
     - Set up alerts for security anomalies
     - Respond quickly to potential breaches

   - **Audit Trails**
     - Record all security-relevant events
     - Maintain compliance with regulations

   **Example with Spring Boot Actuator and Micrometer:**
   ```java
   @Configuration
   public class SecurityAuditConfig {
       private final MeterRegistry meterRegistry;
       
       public SecurityAuditConfig(MeterRegistry meterRegistry) {
           this.meterRegistry = meterRegistry;
       }
       
       @Bean
       public AuditEventRepository auditEventRepository() {
           return new InMemoryAuditEventRepository();
       }
       
       @EventListener
       public void auditEventListener(AuditApplicationEvent event) {
           AuditEvent auditEvent = event.getAuditEvent();
           
           // Log security event
           log.info("Security event: {} by {} at {}", 
               auditEvent.getType(), 
               auditEvent.getPrincipal(), 
               auditEvent.getTimestamp());
           
           // Record metrics for monitoring
           meterRegistry.counter("security.events", 
               "type", auditEvent.getType(),
               "principal", auditEvent.getPrincipal())
               .increment();
       }
   }
   ```

8. **API Security**

   - **Input Validation**
     - Validate all input at service boundaries
     - Protect against injection attacks

   - **Output Encoding**
     - Properly encode data before returning to clients
     - Prevent XSS and other injection vulnerabilities

   - **API Schemas and Contracts**
     - Define and enforce API contracts
     - Reject malformed requests

   **Example with Spring Validation:**
   ```java
   @RestController
   @RequestMapping("/api/orders")
   public class OrderController {
       
       @PostMapping
       public ResponseEntity<Order> createOrder(
               @Valid @RequestBody OrderRequest request, 
               BindingResult bindingResult) {
           
           if (bindingResult.hasErrors()) {
               throw new ValidationException("Invalid order request");
           }
           
           // Process valid order
           Order order = orderService.createOrder(request);
           return ResponseEntity.ok(order);
       }
   }
   
   public class OrderRequest {
       @NotNull(message = "Customer ID is required")
       private String customerId;
       
       @NotEmpty(message = "Order must have at least one item")
       private List<OrderItemRequest> items;
       
       @Pattern(regexp = "^[A-Z]{3}$", message = "Currency must be a 3-letter code")
       private String currency;
       
       // Getters and setters
   }
   ```

#### Security Implementation Best Practices

1. **Defense in Depth**
   - Implement security at multiple layers
   - Don't rely on a single security control

2. **Principle of Least Privilege**
   - Services should have only the permissions they need
   - Minimize the scope of damage if compromised

3. **Secure by Default**
   - Security should be the default configuration
   - Explicit action required to reduce security

4. **Regular Security Testing**
   - Penetration testing of services
   - Automated security scanning in CI/CD

5. **Security as Code**
   - Define security policies as code
   - Version and review security configurations

### 26. JWT Authentication in Microservices
**Answer:**

JSON Web Tokens (JWT) provide a compact, self-contained mechanism for securely transmitting information between parties. They're particularly valuable in microservices architectures for authentication and authorization across distributed services.

#### JWT Structure

A JWT consists of three parts separated by dots (`.`):
1. **Header**: Identifies the algorithm used to generate the signature
2. **Payload**: Contains claims (assertions about an entity, typically the user)
3. **Signature**: Ensures the token hasn't been altered

**Example JWT:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

#### JWT Authentication Flow in Microservices

1. **Authentication Service**
   - User provides credentials (username/password)
   - Auth service validates credentials and generates a JWT
   - JWT is returned to the client

2. **Service Requests**
   - Client includes JWT in Authorization header
   - Services validate the JWT signature and claims
   - Services extract user information and permissions

3. **Token Propagation**
   - Gateway or services forward the JWT to downstream services
   - User context is maintained across the service chain

#### Implementation with Spring Security

**1. Auth Service (JWT Generation):**

```java
@Service
public class AuthenticationService {
    @Value("${jwt.secret}")
    private String jwtSecret;
    
    @Value("${jwt.expiration}")
    private long jwtExpiration;
    
    public String generateToken(Authentication authentication) {
        UserDetails userDetails = (UserDetails) authentication.getPrincipal();
        
        Map<String, Object> claims = new HashMap<>();
        // Add custom claims
        claims.put("roles", userDetails.getAuthorities().stream()
            .map(GrantedAuthority::getAuthority)
            .collect(Collectors.toList()));
        
        return Jwts.builder()
            .setClaims(claims)
            .setSubject(userDetails.getUsername())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + jwtExpiration))
            .signWith(SignatureAlgorithm.HS512, jwtSecret)
            .compact();
    }
    
    @PostMapping("/login")
    public ResponseEntity<?> authenticate(@RequestBody LoginRequest loginRequest) {
        // Authenticate user with username/password
        Authentication authentication = authenticationManager.authenticate(
            new UsernamePasswordAuthenticationToken(
                loginRequest.getUsername(), 
                loginRequest.getPassword()
            )
        );
        
        SecurityContextHolder.getContext().setAuthentication(authentication);
        
        // Generate JWT token
        String jwt = generateToken(authentication);
        
        // Return token to client
        return ResponseEntity.ok(new JwtResponse(jwt));
    }
}
```

**2. Resource Service (JWT Validation):**

```java
@Component
public class JwtTokenFilter extends OncePerRequestFilter {
    @Value("${jwt.secret}")
    private String jwtSecret;
    
    private final UserDetailsService userDetailsService;
    
    @Override
    protected void doFilterInternal(
            HttpServletRequest request, 
            HttpServletResponse response, 
            FilterChain filterChain) throws ServletException, IOException {
        
        try {
            String jwt = getJwtFromRequest(request);
            
            if (StringUtils.hasText(jwt) && validateToken(jwt)) {
                String username = getUsernameFromToken(jwt);
                UserDetails userDetails = userDetailsService.loadUserByUsername(username);
                
                // Create authentication object
                UsernamePasswordAuthenticationToken authentication = 
                    new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                    
                authentication.setDetails(
                    new WebAuthenticationDetailsSource().buildDetails(request));
                    
                // Set authentication in security context
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        } catch (Exception ex) {
            logger.error("Could not set user authentication in security context", ex);
        }
        
        filterChain.doFilter(request, response);
    }
    
    private String getJwtFromRequest(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        
        return null;
    }
    
    public boolean validateToken(String token) {
        try {
            Jwts.parser().setSigningKey(jwtSecret).parseClaimsJws(token);
            return true;
        } catch (SignatureException ex) {
            logger.error("Invalid JWT signature");
        } catch (MalformedJwtException ex) {
            logger.error("Invalid JWT token");
        } catch (ExpiredJwtException ex) {
            logger.error("Expired JWT token");
        } catch (UnsupportedJwtException ex) {
            logger.error("Unsupported JWT token");
        } catch (IllegalArgumentException ex) {
            logger.error("JWT claims string is empty");
        }
        
        return false;
    }
    
    public String getUsernameFromToken(String token) {
        Claims claims = Jwts.parser()
            .setSigningKey(jwtSecret)
            .parseClaimsJws(token)
            .getBody();
            
        return claims.getSubject();
    }
}
```

**3. Security Configuration:**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    private final JwtTokenFilter jwtTokenFilter;
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .cors().and().csrf().disable()
            .exceptionHandling().authenticationEntryPoint(unauthorizedHandler).and()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS).and()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .antMatchers("/api/public/**").permitAll()
                .anyRequest().authenticated();
                
        // Add JWT filter
        http.addFilterBefore(jwtTokenFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

**4. Token Propagation Between Services:**

```java
@Component
public class JwtTokenRelayFilter implements WebFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain) {
        // Extract JWT from incoming request
        String token = extractToken(exchange.getRequest());
        
        // Forward JWT to downstream services in outgoing requests
        return chain.filter(exchange)
            .contextWrite(context -> context.put("jwt-token", token));
    }
    
    private String extractToken(ServerHttpRequest request) {
        List<String> authHeaders = request.getHeaders().get("Authorization");
        if (authHeaders != null && !authHeaders.isEmpty()) {
            return authHeaders.get(0);
        }
        return null;
    }
}

@Component
public class JwtTokenRelayInterceptor implements ClientHttpRequestInterceptor {
    private final ReactiveContext reactiveContext;
    
    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, 
                                      ClientHttpRequestExecution execution) throws IOException {
        // Get token from context
        String token = reactiveContext.getContext()
            .getOrEmpty("jwt-token")
            .map(Object::toString)
            .orElse(null);
            
        if (token != null) {
            // Add token to outgoing request
            request.getHeaders().add("Authorization", token);
        }
        
        return execution.execute(request, body);
    }
}
```

#### JWT Best Practices in Microservices

1. **Token Scope and Audience**
   - Include an `aud` (audience) claim to specify which services can use the token
   - Restrict token scope to only necessary services

2. **Short-lived Tokens**
   - Use short expiration times (5-15 minutes)
   - Implement token refresh mechanisms for longer sessions

3. **Secure Token Storage**
   - Store tokens securely on the client (HttpOnly cookies for web apps)
   - Protect against XSS and CSRF attacks

4. **Claims Validation**
   - Validate all claims, not just signature
   - Check issuer, audience, expiration, and not-before times

5. **Key Management**
   - Rotate signing keys regularly
   - Use asymmetric keys (RS256) for better security

6. **Centralized JWT Configuration**
   - Keep JWT settings consistent across services
   - Use a central configuration service

7. **Token Blacklisting (Optional)**
   - Implement token revocation for critical systems
   - Consider using a distributed cache like Redis

#### JWT Claims for Microservices

```json
{
  "sub": "1234567890",         // Subject (user ID)
  "name": "John Doe",         // User's name
  "iat": 1516239022,          // Issued At timestamp
  "exp": 1516242622,          // Expiration timestamp
  "iss": "auth-service",       // Issuer (auth service)
  "aud": ["order-service",     // Audience (intended recipients)
          "payment-service"],
  "roles": ["ROLE_USER"],      // User roles for authorization
  "permissions": ["read",      // Granular permissions
                 "create:order"],
  "tenant_id": "tenant-123"    // Multi-tenancy information
}
```

#### JWT vs Session-Based Authentication for Microservices

| Aspect | JWT | Session-Based |
|--------|-----|---------------|
| **Statelessness** | Stateless - all info in token | Stateful - requires session storage |
| **Scalability** | Excellent - no shared session store | Requires sticky sessions or distributed session store |
| **Revocation** | Difficult - tokens valid until expiry | Easy - just invalidate the session |
| **Size** | Larger - contains all claims | Smaller - just a session ID |
| **Processing Overhead** | Higher - cryptographic validation | Lower - simple lookup |
| **Cross-domain** | Easy - can be used across domains | Difficult - cookies are domain-bound |

### 27. Login/Signup Pages: Are They Necessary?
**Answer:**

The necessity of dedicated login/signup pages depends on the specific requirements and context of the application. Let's explore when they're needed and when alternatives might be appropriate.

#### When Login/Signup Pages ARE Necessary

1. **User-Specific Content and Personalization**
   - Applications that store user preferences, history, or personalized content
   - Services that tailor experiences based on user identity

2. **Data Security and Privacy**
   - Applications handling sensitive personal information
   - Systems requiring user consent for data processing
   - Services with regulatory compliance requirements (GDPR, HIPAA, etc.)

3. **Access Control**
   - Services with premium or tiered features
   - Applications with role-based permissions
   - Content with varying visibility levels

4. **User Engagement and Retention**
   - Services benefiting from building ongoing relationships with users
   - Applications that track user progress or history
   - Platforms with social or community features

5. **Accountability and Audit Trails**
   - Systems where user actions need attribution
   - Applications requiring non-repudiation of actions
   - Services with compliance requirements for action tracking

#### When Login/Signup Pages Might NOT Be Necessary

1. **Informational Websites**
   - Static content sites where information is the same for all users
   - Marketing or brochure websites without interactive features

2. **Single-Use or Simple Tools**
   - Calculators, converters, or utilities that don't save state
   - Tools where persistence isn't valuable (e.g., random generators)

3. **Public Content Platforms**
   - News sites or blogs with no comment features
   - Reference materials with public access

4. **Reducing Friction for Initial Engagement**
   - Services prioritizing immediate use over user tracking
   - Applications where user acquisition is prioritized over data collection

#### Alternative Authentication Approaches

1. **Social Login**
   - Leverage existing accounts (Google, Facebook, Apple, etc.)
   - Reduces friction while still identifying users
   - Example: "Login with Google" buttons

2. **Progressive Authentication**
   - Allow basic functionality without login
   - Request authentication only when needed for advanced features
   - Example: Shopping carts that allow browsing and selection before requiring login at checkout

3. **Magic Links / Passwordless**
   - Email a login link rather than requiring password entry
   - Simplifies user experience while maintaining security
   - Example: "Login with email" where a time-limited link is sent

4. **Guest Access with Conversion Path**
   - Allow temporary use with an option to save progress by creating an account
   - Balance immediate access with long-term engagement
   - Example: "Continue as guest" with "Save your progress" prompts

5. **Device or Session Authentication**
   - Use device fingerprinting or cookies for lightweight identification
   - Provide personalization without formal account creation
   - Example: Preferences stored in local storage with an option to formalize later

#### Technical and UX Considerations

1. **Security**
   - Authentication methods must be appropriate for the sensitivity of data
   - Security requirements may dictate authentication approach

2. **User Experience**
   - Login/signup friction can reduce conversion rates (up to 30% according to studies)
   - Each form field adds potential for abandonment

3. **Business Goals**
   - Balance between data collection needs and user acquisition goals
   - Consider the lifetime value of authenticated users vs. volume of anonymous users

4. **Regulatory Compliance**
   - Data protection laws may require explicit user consent and verification
   - Industry-specific regulations may mandate certain authentication methods

5. **Implementation Complexity**
   - Authentication systems add development and maintenance overhead
   - Security requirements for authentication are continually evolving

#### Best Practices When Implementing Login/Signup

1. **Minimize Friction**
   - Collect only essential information initially
   - Offer social login options when appropriate
   - Consider progressive profiling (collect more data over time)

2. **Clear Value Proposition**
   - Clearly communicate benefits of account creation
   - Show tangible advantages of logging in

3. **Security Balance**
   - Implement appropriate security without excessive friction
   - Consider risk-based authentication (more security for sensitive operations)

4. **Seamless Experience**
   - Design for smooth transitions between anonymous and authenticated states
   - Ensure mobile-friendly authentication flows

5. **Account Recovery**
   - Provide easy but secure account recovery options
   - Balance security with usability in recovery flows

### 28. The Twelve-Factor App Methodology in Microservices
**Answer:**

The Twelve-Factor App methodology is a set of best practices for building modern, scalable, maintainable software-as-a-service applications. It was formulated by developers at Heroku and has become a foundational framework for microservices architecture. Each factor addresses a specific aspect of application development and deployment.

#### The Twelve Factors

1. **Codebase**
   - One codebase tracked in version control, many deploys
   - Each microservice has its own dedicated repository
   - Avoid monorepos for true microservices isolation

   **Example:**
   ```bash
   # Repository structure for microservices architecture
   /company-github
     /order-service
     /payment-service
     /user-service
     /notification-service
   ```

2. **Dependencies**
   - Explicitly declare and isolate dependencies
   - Use dependency management tools
   - Never rely on implicit system-wide packages

   **Example (Java/Maven):**
   ```xml
   <dependencies>
     <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
       <version>2.6.7</version>
     </dependency>
     <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
       <version>2.6.7</version>
     </dependency>
   </dependencies>
   ```

3. **Configuration**
   - Store configuration in the environment
   - Strict separation of config from code
   - Use environment variables or config servers

   **Example (Spring Boot):**
   ```java
   @Configuration
   public class DatabaseConfig {
       @Value("${database.url}")
       private String databaseUrl;
       
       @Value("${database.username}")
       private String username;
       
       @Value("${database.password}")
       private String password;
       
       // Bean definitions using injected configuration
   }
   ```

4. **Backing Services**
   - Treat backing services as attached resources
   - Access all services via URLs or connection strings in config
   - Should be able to swap a local database with a cloud one without code changes

   **Example (Spring Boot):**
   ```yaml
   # application.yml
   spring:
     datasource:
       url: ${DATABASE_URL}
       username: ${DATABASE_USER}
       password: ${DATABASE_PASSWORD}
     rabbitmq:
       host: ${RABBITMQ_HOST}
       port: ${RABBITMQ_PORT}
       username: ${RABBITMQ_USER}
       password: ${RABBITMQ_PASSWORD}
   ```

5. **Build, Release, Run**
   - Strictly separate build, release, and run stages
   - Builds create immutable artifacts
   - Releases combine artifacts with configuration
   - Runs execute the application in the target environment

   **Example (CI/CD Pipeline):**
   ```yaml
   # GitLab CI configuration
   stages:
     - build
     - test
     - release
     - deploy

   build_job:
     stage: build
     script:
       - ./mvnw package -DskipTests
     artifacts:
       paths:
         - target/*.jar

   test_job:
     stage: test
     script:
       - ./mvnw test

   release_job:
     stage: release
     script:
       - docker build -t myapp:$CI_COMMIT_SHA .
       - docker tag myapp:$CI_COMMIT_SHA myregistry/myapp:$CI_COMMIT_SHA
       - docker push myregistry/myapp:$CI_COMMIT_SHA

   deploy_job:
     stage: deploy
     script:
       - kubectl set image deployment/myapp myapp=myregistry/myapp:$CI_COMMIT_SHA
   ```

6. **Processes**
   - Execute apps as one or more stateless processes
   - Persist state in backing services, not in process memory
   - Enable horizontal scaling and resilience

   **Example (Stateless REST Controller):**
   ```java
   @RestController
   @RequestMapping("/api/orders")
   public class OrderController {
       private final OrderRepository orderRepository;
       
       @Autowired
       public OrderController(OrderRepository orderRepository) {
           this.orderRepository = orderRepository;
       }
       
       @PostMapping
       public ResponseEntity<Order> createOrder(@RequestBody OrderRequest request) {
           // Process and store in database, not in memory
           Order order = new Order();
           order.setCustomerId(request.getCustomerId());
           order.setItems(request.getItems());
           
           Order savedOrder = orderRepository.save(order);
           return ResponseEntity.ok(savedOrder);
       }
   }
   ```

7. **Port Binding**
   - Export services via port binding
   - One service per port
   - Service should be self-contained and not rely on runtime injection of a webserver

   **Example (Spring Boot):**
   ```java
   @SpringBootApplication
   public class PaymentServiceApplication {
       public static void main(String[] args) {
           SpringApplication.run(PaymentServiceApplication.class, args);
       }
   }
   ```

   ```yaml
   # application.yml
   server:
     port: 8081
   ```

8. **Concurrency**
   - Scale out via the process model
   - Horizontal scaling of stateless processes rather than vertical scaling
   - Processes should handle their own multiplexing

   **Example (Kubernetes Deployment):**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: order-service
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: order-service
     template:
       metadata:
         labels:
           app: order-service
       spec:
         containers:
         - name: order-service
           image: myregistry/order-service:latest
           ports:
           - containerPort: 8080
   ```

9. **Disposability**
   - Maximize robustness with fast startup and graceful shutdown
   - Processes should be disposable and ready to be replaced
   - Should handle unexpected termination gracefully

   **Example (Spring Boot Graceful Shutdown):**
   ```java
   @Bean
   public GracefulShutdown gracefulShutdown() {
       return new GracefulShutdown();
   }
   
   @Bean
   public ServletWebServerFactory servletContainer(GracefulShutdown gracefulShutdown) {
       TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
       factory.addConnectorCustomizers(gracefulShutdown);
       return factory;
   }
   
   // Graceful shutdown implementation
   private static class GracefulShutdown implements TomcatConnectorCustomizer, ApplicationListener<ContextClosedEvent> {
       private volatile Connector connector;
       
       @Override
       public void customize(Connector connector) {
           this.connector = connector;
       }
       
       @Override
       public void onApplicationEvent(ContextClosedEvent event) {
           this.connector.pause();
           // Allow active requests to complete (with a timeout)
           Executor executor = this.connector.getProtocolHandler().getExecutor();
           if (executor instanceof ThreadPoolExecutor) {
               ThreadPoolExecutor threadPoolExecutor = (ThreadPoolExecutor) executor;
               threadPoolExecutor.shutdown();
               try {
                   if (!threadPoolExecutor.awaitTermination(30, TimeUnit.SECONDS)) {
                       threadPoolExecutor.shutdownNow();
                   }
               } catch (InterruptedException e) {
                   Thread.currentThread().interrupt();
                   threadPoolExecutor.shutdownNow();
               }
           }
       }
   }
   ```

10. **Dev/Prod Parity**
    - Keep development, staging, and production as similar as possible
    - Use the same backing services across environments
    - Containerization helps maintain environment consistency

    **Example (Docker Compose for Local Development):**
    ```yaml
    # docker-compose.yml for local development matching production services
    version: '3'
    services:
      order-service:
        build: ./order-service
        ports:
          - "8080:8080"
        environment:
          - SPRING_PROFILES_ACTIVE=dev
          - DATABASE_URL=jdbc:postgresql://postgres:5432/orders
          - RABBITMQ_HOST=rabbitmq
      
      postgres:
        image: postgres:13
        environment:
          - POSTGRES_DB=orders
          - POSTGRES_USER=admin
          - POSTGRES_PASSWORD=password
        volumes:
          - postgres-data:/var/lib/postgresql/data
      
      rabbitmq:
        image: rabbitmq:3-management
        ports:
          - "5672:5672"
          - "15672:15672"
    
    volumes:
      postgres-data:
    ```

11. **Logs**
    - Treat logs as event streams
    - Don't concern the application with storage or routing of logs
    - Write logs to stdout/stderr and let the execution environment handle them

    **Example (Spring Boot Logging):**
    ```java
    @Service
    public class OrderService {
        private static final Logger logger = LoggerFactory.getLogger(OrderService.class);
        
        public Order createOrder(OrderRequest request) {
            logger.info("Creating order for customer: {}", request.getCustomerId());
            
            try {
                // Order creation logic
                Order order = new Order();
                // [...]
                
                logger.info("Order created successfully with ID: {}", order.getId());
                return order;
            } catch (Exception e) {
                logger.error("Failed to create order: {}", e.getMessage(), e);
                throw e;
            }
        }
    }
    ```

12. **Admin Processes**
    - Run admin/management tasks as one-off processes
    - Use the same codebase and configuration
    - Ship admin code with application code

    **Example (Spring Boot Command Line Runner):**
    ```java
    @Component
    @Profile("migration")
    public class DatabaseMigration implements CommandLineRunner {
        private final MigrationService migrationService;
        
        @Autowired
        public DatabaseMigration(MigrationService migrationService) {
            this.migrationService = migrationService;
        }
        
        @Override
        public void run(String... args) {
            migrationService.migrateData();
            // Exit after migration
            System.exit(0);
        }
    }
    ```

#### Benefits of Twelve-Factor Methodology in Microservices

1. **Portability**
   - Applications can run in various environments without modification
   - Easy migration between cloud providers

2. **Continuous Deployment**
   - Clear separation of build, release, and run stages
   - Automated deployment pipelines

3. **Scalability**
   - Stateless processes enable horizontal scaling
   - Clear concurrency model

4. **Maintainability**
   - Explicit dependencies and configuration
   - Consistent development practices

5. **Resilience**
   - Disposable processes improve system stability
   - Fast startup and graceful shutdown for better recovery

#### Challenges and Adaptations

1. **Stateful Services**
   - Some services inherently require state
   - Use external state stores or design for proper state management

2. **Legacy Integration**
   - May need to adapt principles when integrating with legacy systems
   - Use anti-corruption layers and bounded contexts

3. **Organizational Alignment**
   - Requires alignment of development and operations teams
   - DevOps practices and culture are essential

4. **Monitoring and Observability**
   - Additional factor often considered for modern applications
   - Implement distributed tracing, metrics, and centralized logging

5. **Security**
   - Another factor frequently added to the original twelve
   - Consider security at every stage: secrets management, vulnerability scanning, etc.

### 29. Handling Properties Overriding in Spring Boot
**Answer:**

Spring Boot provides a flexible property management system that allows configuration settings to be defined in multiple places and formats. Understanding the property resolution order is crucial for managing configurations across different environments.

#### Property Sources and Order of Precedence

Spring Boot loads properties from multiple sources in a specific order. Sources with higher precedence override those with lower precedence. The order (from highest to lowest precedence) is:

1. **Command-line arguments**
   ```bash
   java -jar app.jar --server.port=9000
   ```

2. **SPRING_APPLICATION_JSON properties**
   ```bash
   export SPRING_APPLICATION_JSON='{"server":{"port":9000}}'
   ```

3. **Servlet parameters and ServletContext parameters**

4. **JNDI attributes from java:comp/env**

5. **Java System properties (System.getProperties())**
   ```bash
   java -Dserver.port=9000 -jar app.jar
   ```

6. **OS environment variables**
   ```bash
   export SERVER_PORT=9000
   ```

7. **Profile-specific application properties outside of the packaged jar**
   - `application-{profile}.yml` or `application-{profile}.properties`

8. **Profile-specific application properties packaged inside the jar**
   - `application-{profile}.yml` or `application-{profile}.properties`

9. **Application properties outside of the packaged jar**
   - `application.yml` or `application.properties`

10. **Application properties packaged inside the jar**
    - `application.yml` or `application.properties`

11. **@PropertySource annotations on your @Configuration classes**
    ```java
    @Configuration
    @PropertySource("classpath:custom.properties")
    public class AppConfig {
        // ...
    }
    ```

12. **Default properties (SpringApplication.setDefaultProperties)**
    ```java
    SpringApplication app = new SpringApplication(MyApp.class);
    Properties defaultProperties = new Properties();
    defaultProperties.setProperty("server.port", "8080");
    app.setDefaultProperties(defaultProperties);
    app.run(args);
    ```

#### Managing Properties for Multiple Environments

1. **Profile-Specific Properties**

   Spring Boot loads profile-specific properties when a profile is active. For example, if the "dev" profile is active, Spring Boot will load `application-dev.properties` or `application-dev.yml`.

   **Example:**
   ```properties
   # application.properties (base properties)
   app.name=My Application
   app.description=Default configuration
   
   # application-dev.properties
   app.description=Development configuration
   logging.level.root=DEBUG
   
   # application-prod.properties
   app.description=Production configuration
   logging.level.root=INFO
   server.port=443
   ```

   Activating profiles:
   ```bash
   # Via command line
   java -jar app.jar --spring.profiles.active=dev
   
   # Via environment variable
   export SPRING_PROFILES_ACTIVE=dev
   ```

   ```java
   // In code
   SpringApplication app = new SpringApplication(MyApp.class);
   app.setAdditionalProfiles("dev");
   app.run(args);
   ```

2. **External Configuration**

   Properties can be loaded from specific locations outside the application:

   ```bash
   # Run with custom config location
   java -jar app.jar --spring.config.location=file:./config/application.yml
   
   # Add additional config locations
   java -jar app.jar --spring.config.additional-location=file:./config/
   ```

3. **Environment-Specific Configuration Servers**

   Spring Cloud Config provides a centralized configuration server:

   ```java
   @SpringBootApplication
   @EnableConfigServer
   public class ConfigServerApplication {
       public static void main(String[] args) {
           SpringApplication.run(ConfigServerApplication.class, args);
       }
   }
   ```

   Client configuration:
   ```yaml
   # bootstrap.yml
   spring:
     application:
       name: my-service
     cloud:
       config:
         uri: http://config-server:8888
         fail-fast: true
         label: master
   ```

#### Property Overriding Strategies

1. **Using PropertySource Order**

   Leverage the natural order of PropertySources to override properties. For example, define defaults in `application.properties` and override in environment-specific files.

2. **Property Placeholders with Defaults**

   Use property placeholders with default values:

   ```properties
   # Will use the value of app.timeout if set, otherwise 5000
   connection.timeout=${app.timeout:5000}
   ```

3. **Profile-Specific Bean Configuration**

   Create profile-specific bean configurations:

   ```java
   @Configuration
   @Profile("dev")
   public class DevDatabaseConfig {
       @Bean
       public DataSource dataSource() {
           return new EmbeddedDatabaseBuilder()
               .setType(EmbeddedDatabaseType.H2)
               .build();
       }
   }
   
   @Configuration
   @Profile("prod")
   public class ProdDatabaseConfig {
       @Bean
       public DataSource dataSource(@Value("${db.url}") String url,
                                   @Value("${db.username}") String username,
                                   @Value("${db.password}") String password) {
           HikariConfig config = new HikariConfig();
           config.setJdbcUrl(url);
           config.setUsername(username);
           config.setPassword(password);
           return new HikariDataSource(config);
       }
   }
   ```

4. **ConfigurationProperties with Validation**

   Group related properties and provide validation:

   ```java
   @Configuration
   @ConfigurationProperties(prefix = "app.mail")
   @Validated
   public class MailProperties {
       @NotNull
       private String host;
       
       @Min(1)
       @Max(65535)
       private int port = 25;
       
       private String username;
       private String password;
       private boolean ssl = false;
       
       // Getters and setters
   }
   ```

#### Advanced Techniques

1. **Dynamic Property Refresh**

   Use Spring Cloud's @RefreshScope to dynamically update properties:

   ```java
   @RestController
   @RefreshScope
   public class MyController {
       @Value("${app.message}")
       private String message;
       
       @GetMapping("/message")
       public String getMessage() {
           return message;
       }
   }
   ```

   ```yaml
   # application.yml
   management:
     endpoints:
       web:
         exposure:
           include: refresh
   ```

   When properties change, call the refresh endpoint:
   ```bash
   curl -X POST http://localhost:8080/actuator/refresh
   ```

2. **Custom Property Sources**

   Create custom property sources for specialized configuration needs:

   ```java
   @Configuration
   public class CustomPropertySourceConfig {
       @Bean
       public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
           PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
           YamlPropertiesFactoryBean yaml = new YamlPropertiesFactoryBean();
           yaml.setResources(new ClassPathResource("custom-config.yml"));
           configurer.setProperties(yaml.getObject());
           return configurer;
       }
   }
   ```

3. **Environment Post-Processors**

   Create a custom EnvironmentPostProcessor to modify the environment before the application context is created:

   ```java
   public class CustomEnvironmentPostProcessor implements EnvironmentPostProcessor {
       @Override
       public void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application) {
           Map<String, Object> map = new HashMap<>();
           map.put("custom.property", "dynamically-set-value");
           PropertySource<?> propertySource = new MapPropertySource("customProperties", map);
           environment.getPropertySources().addFirst(propertySource);
       }
   }
   ```

   Register it in `META-INF/spring.factories`:
   ```properties
   org.springframework.boot.env.EnvironmentPostProcessor=com.example.CustomEnvironmentPostProcessor
   ```

#### Best Practices

1. **Sensitive Configuration**

   - Never store secrets in version control
   - Use environment variables or secure vaults for sensitive data
   - Consider encryption for sensitive properties

   ```yaml
   # application.yml
   spring:
     datasource:
       url: jdbc:mysql://localhost:3306/mydb
       username: ${DB_USERNAME}
       password: ${DB_PASSWORD}
   ```

2. **Explicit Property Sources**

   - Be explicit about where properties come from
   - Document the property sources used by your application
   - Use a consistent approach across services

3. **Documentation**

   - Document available configuration properties
   - Consider using Spring Boot's `@ConfigurationProperties` metadata

   ```java
   @ConfigurationProperties(prefix = "app.service")
   @ConfigurationPropertiesScan
   public class ServiceProperties {
       /**
        * The endpoint URL for the remote service.
        */
       private String url;
       
       /**
        * Connection timeout in milliseconds.
        */
       private int timeout = 1000;
       
       // Getters and setters
   }
   ```

4. **Validation**

   - Validate configurations at startup
   - Fail fast if critical configuration is missing

   ```java
   @PostConstruct
   public void validateConfiguration() {
       Assert.notNull(dataSource, "DataSource must not be null");
       Assert.hasText(apiKey, "API key must not be empty");
   }
   ```

5. **Testing**

   - Test different property configurations
   - Use @TestPropertySource for test-specific properties

   ```java
   @SpringBootTest
   @TestPropertySource(properties = {
       "app.service.url=http://test-server",
       "app.service.timeout=500"
   })
   public class ServiceConfigurationTest {
       @Autowired
       private ServiceProperties properties;
       
       @Test
       public void testPropertiesLoaded() {
           assertEquals("http://test-server", properties.getUrl());
           assertEquals(500, properties.getTimeout());
       }
   }
   ```

### 30. Asynchronous Processing in Spring Boot
**Answer:**

Asynchronous processing in Spring Boot allows applications to execute operations concurrently without blocking the main thread, improving responsiveness and scalability. Spring provides several approaches for implementing asynchronous operations.

#### 1. @Async Annotation

The simplest way to make a method execute asynchronously is by using the `@Async` annotation.

**Configuration:**
```java
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer {
    
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(25);
        executor.setThreadNamePrefix("MyAsync-");
        executor.initialize();
        return executor;
    }
    
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new SimpleAsyncUncaughtExceptionHandler();
    }
}
```

**Usage:**
```java
@Service
public class EmailService {
    
    private static final Logger logger = LoggerFactory.getLogger(EmailService.class);
    
    @Async
    public CompletableFuture<Boolean> sendEmail(String to, String subject, String content) {
        logger.info("Sending email to {} on thread {}", to, Thread.currentThread().getName());
        
        try {
            // Simulate email sending
            Thread.sleep(3000);
            logger.info("Email sent to {}", to);
            return CompletableFuture.completedFuture(true);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return CompletableFuture.completedFuture(false);
        }
    }
    
    @Async
    public void sendNotification(String userId, String message) {
        logger.info("Sending notification to {} on thread {}", userId, Thread.currentThread().getName());
        
        try {
            // Simulate notification sending
            Thread.sleep(1000);
            logger.info("Notification sent to {}", userId);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

**Calling Async Methods:**
```java
@RestController
@RequestMapping("/api")
public class NotificationController {
    
    private final EmailService emailService;
    
    @Autowired
    public NotificationController(EmailService emailService) {
        this.emailService = emailService;
    }
    
    @PostMapping("/notify")
    public ResponseEntity<String> notifyUser(@RequestBody NotificationRequest request) {
        // This will execute asynchronously
        emailService.sendNotification(request.getUserId(), request.getMessage());
        
        return ResponseEntity.accepted().body("Notification process started");
    }
    
    @PostMapping("/email")
    public ResponseEntity<String> sendEmail(@RequestBody EmailRequest request) {
        // This will execute asynchronously and return a future
        CompletableFuture<Boolean> future = emailService.sendEmail(
            request.getTo(), request.getSubject(), request.getContent());
        
        return ResponseEntity.accepted().body("Email sending process started");
    }
    
    @GetMapping("/process-batch")
    public ResponseEntity<String> processBatch() throws InterruptedException, ExecutionException {
        // Send multiple emails asynchronously
        CompletableFuture<Boolean> future1 = emailService.sendEmail("user1@example.com", "Subject 1", "Content 1");
        CompletableFuture<Boolean> future2 = emailService.sendEmail("user2@example.com", "Subject 2", "Content 2");
        CompletableFuture<Boolean> future3 = emailService.sendEmail("user3@example.com", "Subject 3", "Content 3");
        
        // Wait for all futures to complete
        CompletableFuture.allOf(future1, future2, future3).join();
        
        // Check results
        boolean allSuccessful = future1.get() && future2.get() && future3.get();
        
        return ResponseEntity.ok("Batch processing completed, success: " + allSuccessful);
    }
}
```

#### 2. WebClient for Reactive HTTP Requests

Spring WebFlux provides WebClient for non-blocking, reactive HTTP requests.

**Configuration:**
```java
@Configuration
public class WebClientConfig {
    
    @Bean
    public WebClient webClient() {
        return WebClient.builder()
            .baseUrl("https://api.example.com")
            .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
            .build();
    }
}
```

**Usage:**
```java
@Service
public class ProductService {
    
    private final WebClient webClient;
    
    @Autowired
    public ProductService(WebClient webClient) {
        this.webClient = webClient;
    }
    
    public Mono<Product> getProduct(String id) {
        return webClient.get()
            .uri("/products/{id}", id)
            .retrieve()
            .bodyToMono(Product.class);
    }
    
    public Flux<Product> getAllProducts() {
        return webClient.get()
            .uri("/products")
            .retrieve()
            .bodyToFlux(Product.class);
    }
    
    public Mono<Product> createProduct(Product product) {
        return webClient.post()
            .uri("/products")
            .bodyValue(product)
            .retrieve()
            .bodyToMono(Product.class);
    }
}
```

**Controller Using Reactive Service:**
```java
@RestController
@RequestMapping("/api/products")
public class ProductController {
    
    private final ProductService productService;
    
    @Autowired
    public ProductController(ProductService productService) {
        this.productService = productService;
    }
    
    @GetMapping("/{id}")
    public Mono<ResponseEntity<Product>> getProduct(@PathVariable String id) {
        return productService.getProduct(id)
            .map(product -> ResponseEntity.ok(product))
            .defaultIfEmpty(ResponseEntity.notFound().build());
    }
    
    @GetMapping
    public Flux<Product> getAllProducts() {
        return productService.getAllProducts();
    }
    
    @PostMapping
    public Mono<ResponseEntity<Product>> createProduct(@RequestBody Product product) {
        return productService.createProduct(product)
            .map(createdProduct -> ResponseEntity.status(HttpStatus.CREATED).body(createdProduct));
    }
    
    @GetMapping(value = "/stream", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<Product> streamProducts() {
        return productService.getAllProducts()
            .delayElements(Duration.ofSeconds(1)); // Add delay to simulate streaming
    }
}
```

#### 3. Spring Integration for Message-Driven Architecture

Spring Integration provides a way to implement asynchronous, message-driven architectures.

**Configuration:**
```java
@Configuration
@EnableIntegration
public class IntegrationConfig {
    
    @Bean
    public MessageChannel orderChannel() {
        return new DirectChannel();
    }
    
    @Bean
    public MessageChannel processedOrderChannel() {
        return new DirectChannel();
    }
    
    @Bean
    public IntegrationFlow orderProcessingFlow() {
        return IntegrationFlows.from(orderChannel())
            .handle(orderProcessor())
            .channel(processedOrderChannel())
            .get();
    }
    
    @Bean
    public OrderProcessor orderProcessor() {
        return new OrderProcessor();
    }
}
```

**Service and Message Handlers:**
```java
@Service
public class OrderService {
    
    private final MessageChannel orderChannel;
    private final PollableChannel processedOrderChannel;
    
    @Autowired
    public OrderService(
            @Qualifier("orderChannel") MessageChannel orderChannel,
            @Qualifier("processedOrderChannel") PollableChannel processedOrderChannel) {
        this.orderChannel = orderChannel;
        this.processedOrderChannel = processedOrderChannel;
    }
    
    public void submitOrder(Order order) {
        orderChannel.send(MessageBuilder.withPayload(order).build());
    }
    
    public Order receiveProcessedOrder(long timeout) {
        Message<?> received = processedOrderChannel.receive(timeout);
        return (received != null) ? (Order) received.getPayload() : null;
    }
}

@Component
public class OrderProcessor {
    
    private static final Logger logger = LoggerFactory.getLogger(OrderProcessor.class);
    
    @Transformer(inputChannel = "orderChannel", outputChannel = "processedOrderChannel")
    public Order processOrder(Order order) {
        logger.info("Processing order: {}", order.getId());
        
        try {
            // Simulate processing
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        order.setStatus("PROCESSED");
        order.setProcessedDate(new Date());
        
        logger.info("Order processed: {}", order.getId());
        return order;
    }
}
```

#### 4. Spring Batch for Bulk Processing

Spring Batch provides a framework for batch processing - reading, processing, and writing bulk data.

**Configuration:**
```java
@Configuration
@EnableBatchProcessing
public class BatchConfig {
    
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    
    @Bean
    public Job importUserJob(Step step1) {
        return jobBuilderFactory.get("importUserJob")
            .incrementer(new RunIdIncrementer())
            .listener(new JobCompletionNotificationListener())
            .flow(step1)
            .end()
            .build();
    }
    
    @Bean
    public Step step1(ItemReader<Person> reader, ItemProcessor<Person, Person> processor, ItemWriter<Person> writer) {
        return stepBuilderFactory.get("step1")
            .<Person, Person>chunk(10)
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .build();
    }
    
    @Bean
    public FlatFileItemReader<Person> reader() {
        return new FlatFileItemReaderBuilder<Person>()
            .name("personItemReader")
            .resource(new ClassPathResource("sample-data.csv"))
            .delimited()
            .names("firstName", "lastName")
            .fieldSetMapper(new BeanWrapperFieldSetMapper<Person>() {{
                setTargetType(Person.class);
            }})
            .build();
    }
    
    @Bean
    public PersonItemProcessor processor() {
        return new PersonItemProcessor();
    }
    
    @Bean
    public JdbcBatchItemWriter<Person> writer(DataSource dataSource) {
        return new JdbcBatchItemWriterBuilder<Person>()
            .itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>())
            .sql("INSERT INTO people (first_name, last_name) VALUES (:firstName, :lastName)")
            .dataSource(dataSource)
            .build();
    }
}
```

**Processor and Listener:**
```java
public class PersonItemProcessor implements ItemProcessor<Person, Person> {
    
    private static final Logger logger = LoggerFactory.getLogger(PersonItemProcessor.class);
    
    @Override
    public Person process(final Person person) {
        final String firstName = person.getFirstName().toUpperCase();
        final String lastName = person.getLastName().toUpperCase();
        
        final Person transformedPerson = new Person(firstName, lastName);
        
        logger.info("Converting ({}) into ({})", person, transformedPerson);
        
        return transformedPerson;
    }
}

public class JobCompletionNotificationListener extends JobExecutionListenerSupport {
    
    private static final Logger logger = LoggerFactory.getLogger(JobCompletionNotificationListener.class);
    
    private final JdbcTemplate jdbcTemplate;
    
    @Autowired
    public JobCompletionNotificationListener(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    
    @Override
    public void afterJob(JobExecution jobExecution) {
        if(jobExecution.getStatus() == BatchStatus.COMPLETED) {
            logger.info("!!! JOB FINISHED! Time to verify the results");
            
            jdbcTemplate.query("SELECT first_name, last_name FROM people",
                (rs, row) -> new Person(rs.getString(1), rs.getString(2)))
                .forEach(person -> logger.info("Found <{}> in the database.", person));
        }
    }
}
```

**Triggering a Batch Job:**
```java
@RestController
@RequestMapping("/api/batch")
public class BatchController {
    
    private final JobLauncher jobLauncher;
    private final Job importUserJob;
    
    @Autowired
    public BatchController(JobLauncher jobLauncher, Job importUserJob) {
        this.jobLauncher = jobLauncher;
        this.importUserJob = importUserJob;
    }
    
    @PostMapping("/import-users")
    public ResponseEntity<String> importUsers() {
        try {
            JobParameters jobParameters = new JobParametersBuilder()
                .addLong("time", System.currentTimeMillis())
                .toJobParameters();
                
            jobLauncher.run(importUserJob, jobParameters);
            
            return ResponseEntity.ok("Batch job has been submitted");
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Error: " + e.getMessage());
        }
    }
}
```

#### 5. Spring Scheduler for Periodic Tasks

Spring's scheduling capabilities allow running tasks asynchronously at specified intervals.

**Configuration:**
```java
@Configuration
@EnableScheduling
public class SchedulingConfig {
    
    @Bean
    public TaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
        scheduler.setPoolSize(5);
        scheduler.setThreadNamePrefix("scheduled-task-");
        scheduler.setErrorHandler(t -> System.err.println("Error in scheduled task: " + t.getMessage()));
        return scheduler;
    }
}
```

**Scheduled Tasks:**
```java
@Component
public class ScheduledTasks {
    
    private static final Logger logger = LoggerFactory.getLogger(ScheduledTasks.class);
    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");
    
    // Execute every 5 seconds
    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        logger.info("The time is now {}", dateFormat.format(new Date()));
    }
    
    // Execute 5 seconds after previous execution completes
    @Scheduled(fixedDelay = 5000)
    public void processQueues() {
        logger.info("Processing message queues at {}", dateFormat.format(new Date()));
        
        try {
            // Simulate processing
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        logger.info("Finished processing queues at {}", dateFormat.format(new Date()));
    }
    
    // Execute at 2:15 AM every day
    @Scheduled(cron = "0 15 2 * * ?")
    public void performDailyCleanup() {
        logger.info("Performing daily cleanup at {}", dateFormat.format(new Date()));
    }
}
```

**Dynamic Scheduling:**
```java
@Service
public class DynamicSchedulerService {
    
    private static final Logger logger = LoggerFactory.getLogger(DynamicSchedulerService.class);
    
    private final TaskScheduler taskScheduler;
    private final Map<String, ScheduledFuture<?>> scheduledTasks = new ConcurrentHashMap<>();
    
    @Autowired
    public DynamicSchedulerService(TaskScheduler taskScheduler) {
        this.taskScheduler = taskScheduler;
    }
    
    public void scheduleTask(String taskId, Runnable task, String cronExpression) {
        // Cancel existing task if present
        cancelTask(taskId);
        
        CronTrigger trigger = new CronTrigger(cronExpression);
        ScheduledFuture<?> future = taskScheduler.schedule(task, trigger);
        scheduledTasks.put(taskId, future);
        
        logger.info("Scheduled task '{}' with cron expression: {}", taskId, cronExpression);
    }
    
    public void cancelTask(String taskId) {
        ScheduledFuture<?> future = scheduledTasks.get(taskId);
        if (future != null) {
            future.cancel(false);
            scheduledTasks.remove(taskId);
            logger.info("Cancelled task: {}", taskId);
        }
    }
    
    public Set<String> getScheduledTaskIds() {
        return Collections.unmodifiableSet(scheduledTasks.keySet());
    }
}
```

#### 6. Event-Driven Programming with Spring Events

Spring's event system provides a way to decouple components using a publish-subscribe model.

**Event Classes:**
```java
public class OrderCreatedEvent extends ApplicationEvent {
    
    private final Order order;
    
    public OrderCreatedEvent(Object source, Order order) {
        super(source);
        this.order = order;
    }
    
    public Order getOrder() {
        return order;
    }
}

public class OrderShippedEvent extends ApplicationEvent {
    
    private final Order order;
    
    public OrderShippedEvent(Object source, Order order) {
        super(source);
        this.order = order;
    }
    
    public Order getOrder() {
        return order;
    }
}
```

**Event Publishers:**
```java
@Service
public class OrderService {
    
    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);
    
    private final ApplicationEventPublisher eventPublisher;
    private final OrderRepository orderRepository;
    
    @Autowired
    public OrderService(ApplicationEventPublisher eventPublisher, OrderRepository orderRepository) {
        this.eventPublisher = eventPublisher;
        this.orderRepository = orderRepository;
    }
    
    public Order createOrder(Order order) {
        logger.info("Creating order");
        Order savedOrder = orderRepository.save(order);
        
        // Publish event asynchronously
        eventPublisher.publishEvent(new OrderCreatedEvent(this, savedOrder));
        
        return savedOrder;
    }
    
    public Order shipOrder(Long orderId) {
        logger.info("Shipping order: {}", orderId);
        Order order = orderRepository.findById(orderId)
            .orElseThrow(() -> new NotFoundException("Order not found"));
            
        order.setStatus("SHIPPED");
        order.setShippedDate(new Date());
        Order savedOrder = orderRepository.save(order);
        
        // Publish event asynchronously
        eventPublisher.publishEvent(new OrderShippedEvent(this, savedOrder));
        
        return savedOrder;
    }
}
```

**Event Listeners:**
```java
@Component
public class OrderEventListener {
    
    private static final Logger logger = LoggerFactory.getLogger(OrderEventListener.class);
    
    private final EmailService emailService;
    
    @Autowired
    public OrderEventListener(EmailService emailService) {
        this.emailService = emailService;
    }
    
    @EventListener
    @Async
    public void handleOrderCreatedEvent(OrderCreatedEvent event) {
        Order order = event.getOrder();
        logger.info("Received OrderCreatedEvent for order: {} on thread {}", 
            order.getId(), Thread.currentThread().getName());
            
        // Send confirmation email
        emailService.sendOrderConfirmation(order);
    }
    
    @EventListener
    @Async
    public void handleOrderShippedEvent(OrderShippedEvent event) {
        Order order = event.getOrder();
        logger.info("Received OrderShippedEvent for order: {} on thread {}", 
            order.getId(), Thread.currentThread().getName());
            
        // Send shipping notification
        emailService.sendShippingNotification(order);
    }
}
```

#### Best Practices for Asynchronous Processing

1. **Use Dedicated Thread Pools**
   - Configure separate thread pools for different types of tasks
   - Set appropriate pool sizes based on task characteristics (I/O-bound vs CPU-bound)

2. **Handle Exceptions Properly**
   - Implement AsyncUncaughtExceptionHandler for @Async methods
   - Use .exceptionally() or .onError() with CompletableFuture and reactive streams

3. **Consider Task Priority**
   - Use priority queues for tasks with different importance levels
   - Implement custom TaskExecutor that respects priorities

4. **Monitor Thread Pools**
   - Expose metrics about thread pool utilization
   - Set up alerts for thread pool saturation

5. **Timeout Handling**
   - Set appropriate timeouts for asynchronous operations
   - Implement circuit breakers for external service calls

6. **Testing Async Code**
   - Use CountDownLatch or CompletableFuture.get() to wait for async operations in tests
   - Consider using Awaitility library for cleaner async testing

```java
@SpringBootTest
public class AsyncServiceTest {
    
    @Autowired
    private EmailService emailService;
    
    @Test
    public void testAsyncEmailSending() throws Exception {
        // Given
        CountDownLatch latch = new CountDownLatch(1);
        AtomicBoolean success = new AtomicBoolean(false);
        
        // When
        CompletableFuture<Boolean> future = emailService.sendEmail("test@example.com", "Test", "Content");
        future.whenComplete((result, ex) -> {
            if (ex == null) {
                success.set(result);
            }
            latch.countDown();
        });
        
        // Then
        latch.await(5, TimeUnit.SECONDS);
        assertTrue(success.get());
    }
}
```

### 31. Spring Boot Actuator Security
**Answer:**

Spring Boot Actuator is a powerful set of tools for monitoring and managing your Spring Boot application. It provides several production-ready features like health checks, metrics, environment information, etc. However, these endpoints can expose sensitive information, making security a critical concern.

#### Securing Actuator Endpoints

1. **Enabling and Exposing Actuator Endpoints**

   First, add the actuator dependency to your project:

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   ```

   By default, only the `/actuator/health` and `/actuator/info` endpoints are exposed over HTTP. To expose more endpoints, configure your `application.properties` or `application.yml`:

   ```properties
   # Expose all endpoints
   management.endpoints.web.exposure.include=*
   
   # Or expose specific endpoints
   management.endpoints.web.exposure.include=health,info,metrics,prometheus
   
   # Exclude specific endpoints
   management.endpoints.web.exposure.exclude=env,beans
   ```

2. **Adding Spring Security**

   Add Spring Security to secure your actuator endpoints:

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

3. **Basic Configuration**

   Configure Spring Security to secure actuator endpoints:

   ```java
   @Configuration
   public class ActuatorSecurityConfig extends WebSecurityConfigurerAdapter {
       
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
               .authorizeRequests()
                   // Allow health and info endpoints for all
                   .antMatchers("/actuator/health", "/actuator/info").permitAll()
                   // Require ADMIN role for all other actuator endpoints
                   .antMatchers("/actuator/**").hasRole("ADMIN")
                   // Your application endpoints security
                   .antMatchers("/api/**").authenticated()
               .and()
               .httpBasic(); // Use basic auth for simplicity
       }
       
       @Override
       protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           auth.inMemoryAuthentication()
               .withUser("user")
                   .password("{noop}password") // {noop} means no encoding
                   .roles("USER")
               .and()
               .withUser("admin")
                   .password("{noop}admin")
                   .roles("USER", "ADMIN");
       }
   }
   ```

4. **Using Spring Security 5.4+ with Lambda DSL**

   For Spring Security 5.4 and above, you can use the Lambda DSL:

   ```java
   @Configuration
   public class ActuatorSecurityConfig {
       
       @Bean
       public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
           http
               .authorizeHttpRequests(authorizeRequests -> 
                   authorizeRequests
                       .antMatchers("/actuator/health", "/actuator/info").permitAll()
                       .antMatchers("/actuator/**").hasRole("ADMIN")
                       .antMatchers("/api/**").authenticated()
               )
               .httpBasic();
               
           return http.build();
       }
       
       @Bean
       public InMemoryUserDetailsManager userDetailsService() {
           UserDetails user = User.withUsername("user")
               .password("{noop}password")
               .roles("USER")
               .build();
               
           UserDetails admin = User.withUsername("admin")
               .password("{noop}admin")
               .roles("USER", "ADMIN")
               .build();
               
           return new InMemoryUserDetailsManager(user, admin);
       }
   }
   ```

5. **Custom Endpoint Security**

   For more granular control, you can secure specific endpoints differently:

   ```java
   @Configuration
   public class ActuatorSecurityConfig {
       
       @Bean
       public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
           http
               .authorizeHttpRequests(authorizeRequests -> 
                   authorizeRequests
                       // Health and info endpoints are public
                       .antMatchers("/actuator/health/**", "/actuator/info").permitAll()
                       // Metrics requires MONITOR role
                       .antMatchers("/actuator/metrics/**", "/actuator/prometheus/**").hasRole("MONITOR")
                       // These endpoints require ADMIN role
                       .antMatchers("/actuator/env/**", "/actuator/configprops/**").hasRole("ADMIN")
                       // Default rule for other actuator endpoints
                       .antMatchers("/actuator/**").hasRole("ACTUATOR_USER")
                       // Regular application security
                       .anyRequest().authenticated()
               )
               .httpBasic()
               .and()
               .formLogin();
               
           return http.build();
       }
   }
   ```

6. **Using Request Matchers With Spring Security**

   For more complex conditions, you can use request matchers:

   ```java
   @Bean
   public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
       http
           .authorizeHttpRequests(authorizeRequests -> 
               authorizeRequests
                   .requestMatchers(EndpointRequest.to("health", "info")).permitAll()
                   .requestMatchers(EndpointRequest.to("metrics", "prometheus")).hasRole("MONITOR")
                   .requestMatchers(EndpointRequest.toAnyEndpoint()).hasRole("ADMIN")
                   .anyRequest().authenticated()
           )
           .httpBasic();
           
       return http.build();
   }
   ```

7. **Sensitive Information Protection**

   To further control the information displayed in actuator endpoints:

   ```properties
   # Show details only when authorized
   management.endpoint.health.show-details=when_authorized
   
   # Show components only when authorized
   management.endpoint.health.show-components=when_authorized
   
   # Disable specific endpoints completely
   management.endpoint.shutdown.enabled=false
   ```

8. **Cross-Origin Resource Sharing (CORS)**

   If your monitoring tools need to access actuator endpoints from a different origin:

   ```properties
   # Enable CORS for actuator endpoints
   management.endpoints.web.cors.allowed-origins=https://monitoring.example.com
   management.endpoints.web.cors.allowed-methods=GET,POST
   ```

9. **Using Path Base**

   You can also change the base path for actuator endpoints:

   ```properties
   # Change actuator base path
   management.endpoints.web.base-path=/management
   ```

   This will change the endpoints from `/actuator/*` to `/management/*`

10. **Production Setup with JWT**

    For a production environment, you might want to use JWT for authentication:

    ```java
    @Configuration
    public class ActuatorSecurityConfig {
        
        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            http
                .csrf().disable()
                .authorizeHttpRequests(authorizeRequests -> 
                    authorizeRequests
                        .requestMatchers(EndpointRequest.to("health", "info")).permitAll()
                        .requestMatchers(EndpointRequest.toAnyEndpoint()).hasAuthority("SCOPE_ACTUATOR_ADMIN")
                        .anyRequest().authenticated()
                )
                .oauth2ResourceServer(oauth2 -> oauth2.jwt());
                
            return http.build();
        }
    }
    ```

11. **Implementing Custom Actuator Endpoint Security**

    For very specific security requirements, you can implement a custom security filter:

    ```java
    @Component
    public class ActuatorEndpointFilter extends GenericFilterBean {
        
        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
                throws IOException, ServletException {
            HttpServletRequest httpRequest = (HttpServletRequest) request;
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            
            String path = httpRequest.getRequestURI();
            
            // Check if request is for an actuator endpoint
            if (path.startsWith("/actuator") && !path.startsWith("/actuator/health") && !path.startsWith("/actuator/info")) {
                // Check for API key
                String apiKey = httpRequest.getHeader("X-API-Key");
                if (apiKey == null || !apiKey.equals("your-secret-api-key")) {
                    httpResponse.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
                    return;
                }
            }
            
            chain.doFilter(request, response);
        }
    }
    ```

#### Best Practices for Actuator Security

1. **Minimum Exposure**
   - Only expose endpoints that are necessary for monitoring and operations
   - Use management.endpoints.web.exposure.include to specify exactly which endpoints to expose

2. **Network Security**
   - Consider exposing actuator endpoints on a different port
   - Use network-level security to restrict who can access the actuator endpoints

   ```properties
   # Set different port for actuator endpoints
   management.server.port=8081
   
   # Bind to management-only interface
   management.server.address=127.0.0.1
   ```

3. **Authentication and Authorization**
   - Always require authentication for sensitive actuator endpoints
   - Use role-based access control to limit who can access what
   - Consider using OAuth 2.0 or API keys for machine-to-machine communication

4. **TLS Encryption**
   - Always use HTTPS for actuator endpoints in production
   - Configure TLS to secure the communication

5. **Audit Logging**
   - Enable audit logging for actuator endpoint access
   - Monitor who is accessing what and when

   ```java
   @Component
   public class ActuatorAccessAuditLogger extends AbstractAuthenticationAuditListener {
       
       private static final Logger logger = LoggerFactory.getLogger(ActuatorAccessAuditLogger.class);
       
       @Override
       public void onApplicationEvent(AbstractAuthenticationEvent event) {
           Authentication authentication = event.getAuthentication();
           
           if (event instanceof AuthenticationSuccessEvent) {
               HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.currentRequestAttributes()).getRequest();
               String uri = request.getRequestURI();
               
               if (uri.startsWith("/actuator")) {
                   logger.info("Actuator endpoint '{}' accessed by user '{}' from IP {}", 
                       uri, authentication.getName(), request.getRemoteAddr());
               }
           }
       }
   }
   ```

6. **Monitoring and Alerting**
   - Set up monitoring and alerting for unusual actuator endpoint access patterns
   - Integrate with security information and event management (SIEM) systems

### 32. Differences Between @Controller, @Service, @Repository and @Component
**Answer:**

In Spring Framework, these annotations are used to classify and differentiate between different types of beans (components). While they all serve the purpose of component scanning and dependency injection, they have semantic differences that convey their specific roles in a typical Spring application.

#### 1. @Component

**Purpose**: The most generic annotation for any Spring-managed component.

**Characteristics**:
- Acts as a base/generic stereotype annotation for any Spring-managed component
- All other stereotype annotations (@Controller, @Service, @Repository) are specialized forms of @Component
- Used when the component doesn't fit into other more specific categories

**Example**:
```java
@Component
public class UtilityHelper {
    
    public String formatText(String text) {
        return text != null ? text.trim().toLowerCase() : "";
    }
    
    public boolean isValidInput(String input) {
        return input != null && !input.trim().isEmpty();
    }
}
```

#### 2. @Controller

**Purpose**: Indicates that a class serves as a Spring MVC controller.

**Characteristics**:
- Specialized version of @Component for web controllers
- Typically used to handle HTTP requests
- Often used with @RequestMapping to map web requests to specific methods
- Spring automatically detects these through component scanning and makes them available for handling web requests

**Example**:
```java
@Controller
@RequestMapping("/users")
public class UserController {
    
    private final UserService userService;
    
    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping
    public String listUsers(Model model) {
        model.addAttribute("users", userService.getAllUsers());
        return "user/list";
    }
    
    @GetMapping("/{id}")
    public String viewUser(@PathVariable Long id, Model model) {
        model.addAttribute("user", userService.getUserById(id));
        return "user/view";
    }
    
    @PostMapping
    public String createUser(@Valid UserForm userForm, BindingResult result) {
        if (result.hasErrors()) {
            return "user/form";
        }
        userService.createUser(userForm);
        return "redirect:/users";
    }
}
```

**Related Annotations**:
- **@RestController**: Combines @Controller and @ResponseBody, indicating that methods return data directly (typically JSON/XML) instead of view names

```java
@RestController
@RequestMapping("/api/users")
public class UserRestController {
    
    private final UserService userService;
    
    @Autowired
    public UserRestController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return userService.getUserById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody @Valid UserDTO userDTO) {
        User createdUser = userService.createUser(userDTO);
        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(createdUser.getId())
            .toUri();
        return ResponseEntity.created(location).body(createdUser);
    }
}
```

#### 3. @Service

**Purpose**: Indicates that a class contains business logic.

**Characteristics**:
- Specialized version of @Component for service layer
- Typically contains business logic, calculations, and calls to repositories
- Acts as a bridge between controllers and repositories
- No additional functionality compared to @Component but provides semantic meaning

**Example**:
```java
@Service
public class UserService {
    
    private final UserRepository userRepository;
    private final PasswordEncoder passwordEncoder;
    
    @Autowired
    public UserService(UserRepository userRepository, PasswordEncoder passwordEncoder) {
        this.userRepository = userRepository;
        this.passwordEncoder = passwordEncoder;
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public Optional<User> getUserById(Long id) {
        return userRepository.findById(id);
    }
    
    @Transactional
    public User createUser(UserDTO userDTO) {
        // Business logic
        if (userRepository.existsByEmail(userDTO.getEmail())) {
            throw new UserAlreadyExistsException("Email already in use");
        }
        
        User user = new User();
        user.setFirstName(userDTO.getFirstName());
        user.setLastName(userDTO.getLastName());
        user.setEmail(userDTO.getEmail());
        user.setPassword(passwordEncoder.encode(userDTO.getPassword()));
        user.setCreatedAt(LocalDateTime.now());
        
        return userRepository.save(user);
    }
    
    @Transactional
    public User updateUser(Long id, UserDTO userDTO) {
        return userRepository.findById(id)
            .map(user -> {
                user.setFirstName(userDTO.getFirstName());
                user.setLastName(userDTO.getLastName());
                if (!user.getEmail().equals(userDTO.getEmail())) {
                    // Email changed, verify it's not used
                    if (userRepository.existsByEmail(userDTO.getEmail())) {
                        throw new UserAlreadyExistsException("Email already in use");
                    }
                    user.setEmail(userDTO.getEmail());
                }
                // Update password only if provided
                if (StringUtils.hasText(userDTO.getPassword())) {
                    user.setPassword(passwordEncoder.encode(userDTO.getPassword()));
                }
                user.setUpdatedAt(LocalDateTime.now());
                return userRepository.save(user);
            })
            .orElseThrow(() -> new UserNotFoundException("User not found with id: " + id));
    }
    
    @Transactional
    public void deleteUser(Long id) {
        if (!userRepository.existsById(id)) {
            throw new UserNotFoundException("User not found with id: " + id);
        }
        userRepository.deleteById(id);
    }
}
```

#### 4. @Repository

**Purpose**: Indicates that a class is a Data Access Object (DAO).

**Characteristics**:
- Specialized version of @Component for data access layer
- Used for database operations and data access
- Provides additional benefits like automatic persistence exception translation
- Translates database-specific exceptions to Spring's DataAccessException hierarchy

**Example**:
```java
@Repository
public class JdbcUserRepository implements UserRepository {
    
    private final JdbcTemplate jdbcTemplate;
    
    @Autowired
    public JdbcUserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    
    @Override
    public List<User> findAll() {
        return jdbcTemplate.query(
            "SELECT id, first_name, last_name, email, created_at, updated_at FROM users",
            (rs, rowNum) -> mapRowToUser(rs)
        );
    }
    
    @Override
    public Optional<User> findById(Long id) {
        try {
            User user = jdbcTemplate.queryForObject(
                "SELECT id, first_name, last_name, email, created_at, updated_at FROM users WHERE id = ?",
                (rs, rowNum) -> mapRowToUser(rs),
                id
            );
            return Optional.ofNullable(user);
        } catch (EmptyResultDataAccessException e) {
            return Optional.empty();
        }
    }
    
    @Override
    public boolean existsByEmail(String email) {
        Integer count = jdbcTemplate.queryForObject(
            "SELECT COUNT(*) FROM users WHERE email = ?",
            Integer.class,
            email
        );
        return count != null && count > 0;
    }
    
    @Override
    public boolean existsById(Long id) {
        Integer count = jdbcTemplate.queryForObject(
            "SELECT COUNT(*) FROM users WHERE id = ?",
            Integer.class,
            id
        );
        return count != null && count > 0;
    }
    
    @Override
    public User save(User user) {
        if (user.getId() == null) {
            // Insert new user
            KeyHolder keyHolder = new GeneratedKeyHolder();
            jdbcTemplate.update(connection -> {
                PreparedStatement ps = connection.prepareStatement(
                    "INSERT INTO users (first_name, last_name, email, password, created_at) VALUES (?, ?, ?, ?, ?)",
                    Statement.RETURN_GENERATED_KEYS
                );
                ps.setString(1, user.getFirstName());
                ps.setString(2, user.getLastName());
                ps.setString(3, user.getEmail());
                ps.setString(4, user.getPassword());
                ps.setTimestamp(5, Timestamp.valueOf(user.getCreatedAt()));
                return ps;
            }, keyHolder);
            
            Number key = keyHolder.getKey();
            if (key != null) {
                user.setId(key.longValue());
            }
        } else {
            // Update existing user
            jdbcTemplate.update(
                "UPDATE users SET first_name = ?, last_name = ?, email = ?, password = ?, updated_at = ? WHERE id = ?",
                user.getFirstName(),
                user.getLastName(),
                user.getEmail(),
                user.getPassword(),
                Timestamp.valueOf(user.getUpdatedAt()),
                user.getId()
            );
        }
        return user;
    }
    
    @Override
    public void deleteById(Long id) {
        jdbcTemplate.update("DELETE FROM users WHERE id = ?", id);
    }
    
    private User mapRowToUser(ResultSet rs) throws SQLException {
        User user = new User();
        user.setId(rs.getLong("id"));
        user.setFirstName(rs.getString("first_name"));
        user.setLastName(rs.getString("last_name"));
        user.setEmail(rs.getString("email"));
        user.setCreatedAt(rs.getTimestamp("created_at").toLocalDateTime());
        Timestamp updatedAt = rs.getTimestamp("updated_at");
        if (updatedAt != null) {
            user.setUpdatedAt(updatedAt.toLocalDateTime());
        }
        return user;
    }
}
```

**Spring Data Repository**:

With Spring Data, you often extend repository interfaces rather than implementing them directly:

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    boolean existsByEmail(String email);
    Optional<User> findByEmail(String email);
    List<User> findByLastName(String lastName);
    
    @Query("SELECT u FROM User u WHERE u.createdAt > :date")
    List<User> findUsersCreatedAfter(@Param("date") LocalDateTime date);
}
```

#### Key Differences

1. **Purpose and Layer**:
   - **@Controller**: Web layer, handles HTTP requests
   - **@Service**: Service layer, contains business logic
   - **@Repository**: Data access layer, interacts with the database
   - **@Component**: Generic component, used when a bean doesn't fit other categories

2. **Additional Features**:
   - **@Controller**: Works with Spring MVC's request mapping
   - **@Repository**: Provides automatic exception translation from database-specific exceptions
   - **@Service** and **@Component**: No additional technical features beyond @Component

3. **Semantic Meaning**:
   - Each annotation provides semantic information about the role and purpose of the class
   - This helps developers understand the application architecture and component responsibilities
   - Aids in structuring applications according to the separation of concerns principle

#### Usage Guidelines

1. **When to use @Component**:
   - For utility classes that don't fit other stereotypes
   - For infrastructure beans that aren't part of business domain
   - For technical components like converters, validators, etc.

2. **When to use @Controller**:
   - For classes handling HTTP requests
   - For web endpoints that route user interactions
   - When returning views or REST responses

3. **When to use @Service**:
   - For classes containing business logic
   - For orchestrating multiple repositories to perform business operations
   - For implementing business rules and validations

4. **When to use @Repository**:
   - For classes directly interacting with databases
   - For data access objects (DAOs)
   - For implementing custom query methods

#### Common Misconceptions

1. **Technical Differences**:
   - Aside from @Repository's exception translation, there are no functional differences between these annotations
   - All are detected equally by component scanning

2. **Required Usage**:
   - Spring doesn't strictly require you to use the specific stereotype
   - You could theoretically use @Component for everything, but you'd lose semantic clarity

3. **Performance Impact**:
   - There's no performance difference between the annotations
   - They're primarily for organizational and semantic purposes

### 33. SQL Query for Employee Count by Department
**Answer:**

To get the count of employees by department in SQL, we can use the `GROUP BY` clause along with the `COUNT()` function. Here's a basic query that achieves this:

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
    employee_count DESC;
```

This query:
1. Selects the department name and counts the number of employees in each department
2. Uses a LEFT JOIN to include departments that might not have any employees
3. Groups the results by department
4. Orders the results with departments having the most employees first

#### Variations and Advanced Queries

**1. Including Only Departments with Employees:**

```sql
SELECT 
    d.department_name,
    COUNT(e.employee_id) AS employee_count
FROM 
    departments d
INNER JOIN 
    employees e ON d.department_id = e.department_id
GROUP BY 
    d.department_id, d.department_name
ORDER BY 
    employee_count DESC;
```

**2. Filtering for Departments with More Than N Employees:**

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
HAVING 
    COUNT(e.employee_id) > 5
ORDER BY 
    employee_count DESC;
```

**3. Including Additional Department Information:**

```sql
SELECT 
    d.department_id,
    d.department_name,
    d.location_id,
    l.city,
    COUNT(e.employee_id) AS employee_count
FROM 
    departments d
LEFT JOIN 
    employees e ON d.department_id = e.department_id
LEFT JOIN 
    locations l ON d.location_id = l.location_id
GROUP BY 
    d.department_id, d.department_name, d.location_id, l.city
ORDER BY 
    employee_count DESC;
```

**4. Including Employee Statistics by Department:**

```sql
SELECT 
    d.department_name,
    COUNT(e.employee_id) AS employee_count,
    AVG(e.salary) AS avg_salary,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary,
    SUM(e.salary) AS total_salary_expense
FROM 
    departments d
LEFT JOIN 
    employees e ON d.department_id = e.department_id
GROUP BY 
    d.department_id, d.department_name
ORDER BY 
    employee_count DESC;
```

**5. Count of Employees by Department and Job Title:**

```sql
SELECT 
    d.department_name,
    j.job_title,
    COUNT(e.employee_id) AS employee_count
FROM 
    departments d
LEFT JOIN 
    employees e ON d.department_id = e.department_id
LEFT JOIN 
    jobs j ON e.job_id = j.job_id
GROUP BY 
    d.department_id, d.department_name, j.job_title
ORDER BY 
    d.department_name, employee_count DESC;
```

**6. Percentage of Total Employees by Department:**

```sql
WITH department_counts AS (
    SELECT 
        d.department_id,
        d.department_name,
        COUNT(e.employee_id) AS dept_count
    FROM 
        departments d
    LEFT JOIN 
        employees e ON d.department_id = e.department_id
    GROUP BY 
        d.department_id, d.department_name
),
total_count AS (
    SELECT COUNT(*) AS total FROM employees
)
SELECT 
    dc.department_name,
    dc.dept_count AS employee_count,
    CAST(dc.dept_count AS FLOAT) / tc.total * 100 AS percentage
FROM 
    department_counts dc
CROSS JOIN 
    total_count tc
ORDER BY 
    dc.dept_count DESC;
```

**7. Employee Count by Department and Year of Hire:**

```sql
SELECT 
    d.department_name,
    EXTRACT(YEAR FROM e.hire_date) AS hire_year,
    COUNT(e.employee_id) AS employee_count
FROM 
    departments d
JOIN 
    employees e ON d.department_id = e.department_id
GROUP BY 
    d.department_id, d.department_name, EXTRACT(YEAR FROM e.hire_date)
ORDER BY 
    d.department_name, hire_year;
```

**8. Departments with More Female than Male Employees:**

```sql
SELECT 
    d.department_name,
    SUM(CASE WHEN e.gender = 'F' THEN 1 ELSE 0 END) AS female_count,
    SUM(CASE WHEN e.gender = 'M' THEN 1 ELSE 0 END) AS male_count
FROM 
    departments d
JOIN 
    employees e ON d.department_id = e.department_id
GROUP BY 
    d.department_id, d.department_name
HAVING 
    SUM(CASE WHEN e.gender = 'F' THEN 1 ELSE 0 END) > 
    SUM(CASE WHEN e.gender = 'M' THEN 1 ELSE 0 END)
ORDER BY 
    d.department_name;
```

**9. Using Common Table Expressions (CTE) for Readability:**

```sql
WITH employee_counts AS (
    SELECT 
        e.department_id,
        COUNT(e.employee_id) AS count
    FROM 
        employees e
    GROUP BY 
        e.department_id
)
SELECT 
    d.department_name,
    COALESCE(ec.count, 0) AS employee_count
FROM 
    departments d
LEFT JOIN 
    employee_counts ec ON d.department_id = ec.department_id
ORDER BY 
    employee_count DESC;
```

**10. Using Window Functions for Additional Analysis:**

```sql
SELECT 
    d.department_name,
    COUNT(e.employee_id) AS employee_count,
    RANK() OVER (ORDER BY COUNT(e.employee_id) DESC) AS rank_by_size,
    COUNT(e.employee_id) * 100.0 / SUM(COUNT(e.employee_id)) OVER () AS percentage
FROM 
    departments d
LEFT JOIN 
    employees e ON d.department_id = e.department_id
GROUP BY 
    d.department_id, d.department_name
ORDER BY 
    employee_count DESC;
```

#### Database-Specific Variations

**MySQL/MariaDB:**
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
    employee_count DESC;
```

**PostgreSQL:**
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
    employee_count DESC;
```

**Oracle:**
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
    COUNT(e.employee_id) DESC;
```

**SQL Server:**
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
    employee_count DESC;
```

```

**Potential Cross-Questions and Answers:

**Q1: What happens internally when you insert a null key in HashMap?**
A1: When a null key is inserted, the HashMap treats it as a special case. The hash code for null is considered 0, and it's placed in bucket 0. The HashMap implementation has a special check for null keys in both put() and get() methods to handle this case differently from regular keys.

**Q2: Can you have multiple entries with null keys in a HashMap?**
A2: No, you can have only one null key in a HashMap. Since keys must be unique, any subsequent put operation with a null key will overwrite the previous value associated with the null key.

**Q3: How does HashMap find the value for a null key during get() operation?**
A3: During a get(null) operation, HashMap first checks if the key is null. If it is, it directly goes to bucket 0 (or wherever null keys are stored based on the implementation) and returns the associated value without computing any hash code.

**Q4: Which other Map implementations allow null keys?**
A4: Besides HashMap, LinkedHashMap also allows one null key. TreeMap does NOT allow null keys (throws NullPointerException). ConcurrentHashMap and Hashtable also do NOT allow null keys.

**Q5: What problems might arise from using null keys in a HashMap?**
A5: 
- Code readability and maintainability issues
- Difficult to distinguish between "key not present" and "key is null"
- Can lead to NullPointerExceptions if the map is later converted to a type that doesn't support null keys
- Makes the code less robust when interacting with APIs that don't expect null keys

**Q6: What if you need to store multiple values that represent "no key"?**
A6: Since you can only have one null key, you might use sentinel objects or Optional as keys instead. For example:
```java
Map<Optional<String>, String> map = new HashMap<>();
map.put(Optional.empty(), "Value 1");
map.put(Optional.empty(), "Value 2"); // This will overwrite "Value 1"
```

**Q7: Why do some Map implementations prohibit null keys while HashMap allows it?**
A7: 
- **ConcurrentHashMap**: In concurrent contexts, a null return could mean "key not found" or "key mapped to null," causing ambiguity
- **TreeMap**: Uses compareTo() on keys for ordering, and calling compareTo() on null would cause NullPointerException
- **Hashtable**: Legacy class designed before null values were accommodated in collections

**Q8: Is there a performance impact when using null keys?**
A8: Minimal. HashMap has special case handling for null keys that bypasses the hash calculation, which might be slightly faster, but the difference is negligible in most applications.
