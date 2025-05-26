# Microservices, Java 8, Spring, and Advanced Java - PART 6

> **Interviewer Insight**: These questions target specialized knowledge expected from senior developers and architects. They cover microservices patterns, Java 8 functional programming, Spring ecosystem deep-dives, and advanced Java concepts. Prepare detailed, example-driven answers to stand out.

## Table of Contents
- [Microservices & Service Discovery](#microservices--service-discovery)
- [Message Queuing with RabbitMQ](#message-queuing-with-rabbitmq)
- [Exception Handling & Persistence](#exception-handling--persistence)
- [Authentication & Testing](#authentication--testing)
- [Deployment & ORM](#deployment--orm)
- [Java Fundamentals](#java-fundamentals)
- [Java 8 Features](#java-8-features)
- [Collections & Data Structures](#collections--data-structures)
- [Design Patterns](#design-patterns)

## Microservices & Service Discovery

### 1. What are the advantages of using server-side service discovery instead of client-side discovery?

> **Why this is asked**: This tests your understanding of microservices architecture patterns and their tradeoffs.

**Server-Side Service Discovery:**
- Client makes request to a router/load balancer, which handles service instance location
- Client doesn't need to know about service registry
- Service discovery logic is centralized

**Advantages:**
- **Simpler Client Code**: Clients don't need service discovery logic, reducing client complexity
- **Platform Independence**: Clients written in any language can access services
- **Security**: Registry can remain in a protected network, not exposed to clients
- **Centralized Load Balancing**: Can implement sophisticated load balancing strategies
- **Consistent Policy Enforcement**: Security, rate limiting, and metrics can be applied consistently

**Example Architecture:**
```
Client → API Gateway/Load Balancer → Service Instances
                      ↑
                      |
               Service Registry
```

**Client-Side Disadvantages:**
- Each client needs service discovery libraries and configuration
- Increased client complexity
- Registry must be accessible to all clients
- Harder to implement complex load balancing

### 2. How does Eureka Service Discovery work in a microservices architecture?

> **Why this is asked**: Eureka is a popular service discovery tool; understanding its internals shows your practical experience.

**Eureka Architecture:**
- **Eureka Server**: Central registry that holds service instance information
- **Eureka Client**: Library integrated into microservices for registration and discovery

**How it works:**
1. **Service Registration**: 
   - When a service starts, it registers with Eureka server (name, host, port, etc.)
   - Service sends heartbeats to indicate it's still alive

2. **Service Discovery**:
   - Clients query Eureka server for available instances
   - Clients cache registry information locally for resilience
   - Client can use a load balancing algorithm to choose among instances

3. **Self-Preservation Mode**:
   - If Eureka detects network issues causing widespread heartbeat failures
   - Retains all registrations to prevent mass deregistration during network partitions

**Example Configuration:**

Eureka Server:
```java
@SpringBootApplication
@EnableEurekaServer
public class ServiceRegistryApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceRegistryApplication.class, args);
    }
}
```

Eureka Client:
```java
@SpringBootApplication
@EnableEurekaClient
public class ServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceApplication.class, args);
    }
}
```

### 3. How does load balancing work in service discovery, and how do you configure and manage it?

> **Why this is asked**: Load balancing is critical for scaling microservices; this assesses your operations experience.

**Load Balancing with Service Discovery:**
- Distributes incoming requests across multiple service instances
- Improves availability, scalability, and performance

**Implementation Approaches:**
1. **Client-Side Load Balancing** (e.g., Ribbon with Eureka):
   - Client retrieves all service instances from registry
   - Client decides which instance to use based on load balancing algorithm
   - Example: Netflix Ribbon integrated with Eureka

2. **Server-Side Load Balancing** (e.g., API Gateway):
   - Central component routes requests to appropriate instances
   - Examples: Spring Cloud Gateway, Netflix Zuul, AWS ALB

**Load Balancing Algorithms:**
- **Round Robin**: Sequentially distributes requests (default in Ribbon)
- **Weighted Round Robin**: Assigns weights to instances based on capacity
- **Least Connections**: Routes to instance handling fewest requests
- **Zone Aware**: Prefers instances in same availability zone
- **Response Time**: Routes to fastest-responding instances

**Ribbon Configuration Example:**
```yaml
user-service:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.WeightedResponseTimeRule
    ServerListRefreshInterval: 15000
```

**Spring Cloud LoadBalancer Example:**
```java
@Configuration
public class LoadBalancerConfig {
    @Bean
    public ServiceInstanceListSupplier discoveryClientServiceInstanceListSupplier(
            DiscoveryClient discoveryClient) {
        return new DiscoveryClientServiceInstanceListSupplier(discoveryClient);
    }
}
```

### 4. How does registration and deregistration happen in a service discovery system?

> **Why this is asked**: This assesses your understanding of service discovery lifecycle management.

**Registration Process:**
1. **Service Startup**:
   - Service instance starts and initializes
   - Retrieves its metadata (host, port, service ID)
   - Eureka client in the service contacts Eureka server

2. **Registration Request**:
   - Service sends HTTP POST with instance details
   - Registry stores instance information
   - Registry acknowledges successful registration

3. **Heartbeat Mechanism**:
   - Service periodically sends heartbeat requests (default: 30 seconds)
   - Keeps registration active in the registry
   - If heartbeats stop, instance marked as unhealthy after timeout

**Deregistration Process:**
1. **Graceful Shutdown**:
   - Service initiates shutdown process
   - Sends explicit deregistration request to registry
   - Registry removes instance immediately

2. **Failure Detection**:
   - If service crashes without deregistration
   - Registry notes missing heartbeats
   - After timeout (default: 90 seconds), marks instance as DOWN
   - After additional time, removes instance completely

**Configuration Example (application.yml):**
```yaml
eureka:
  client:
    registerWithEureka: true
    fetchRegistry: true
    serviceUrl:
      defaultZone: http://eureka-server:8761/eureka/
  instance:
    leaseRenewalIntervalInSeconds: 30
    leaseExpirationDurationInSeconds: 90
```

**Handling in Spring Boot:**
```java
@PreDestroy
public void deregister() {
    // Custom deregistration logic if needed
    eurekaClient.shutdown();
}
```

## Message Queuing with RabbitMQ

### 5. How does RabbitMQ work, and what is its role in message queuing?

> **Why this is asked**: Message queues are fundamental to async communication in microservices; this tests your distributed systems knowledge.

**RabbitMQ Basics:**
- Message broker implementing Advanced Message Queuing Protocol (AMQP)
- Implements publish/subscribe pattern for asynchronous communication
- Enables decoupling of producers and consumers

**Core Components:**
- **Producer**: Application that sends messages
- **Exchange**: Receives messages from producers and routes to queues
- **Queue**: Buffer that stores messages
- **Binding**: Rule that tells exchange which queue to route to
- **Consumer**: Application that receives messages

**Exchange Types:**
- **Direct**: Routes based on exact routing key match
- **Fanout**: Broadcasts to all bound queues (no routing key)
- **Topic**: Routes based on pattern matching of routing keys
- **Headers**: Routes based on message header attributes

**Key Benefits:**
- **Reliability**: Persists messages until processed
- **Scalability**: Handles high throughput with multiple consumers
- **Flexibility**: Various exchange types for different routing patterns
- **Load Balancing**: Distributes messages across multiple consumers
- **Guaranteed Delivery**: Acknowledgment mechanisms ensure processing

**Java Integration Example:**
```java
@Configuration
public class RabbitMQConfig {
    @Bean
    Queue ordersQueue() {
        return new Queue("orders-queue", true);
    }
    
    @Bean
    DirectExchange exchange() {
        return new DirectExchange("orders-exchange");
    }
    
    @Bean
    Binding binding(Queue ordersQueue, DirectExchange exchange) {
        return BindingBuilder.bind(ordersQueue)
                .to(exchange)
                .with("orders");
    }
}

// Producer
@Service
public class OrderService {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    public void placeOrder(Order order) {
        // Business logic
        rabbitTemplate.convertAndSend("orders-exchange", "orders", order);
    }
}

// Consumer
@Component
public class OrderProcessor {
    @RabbitListener(queues = "orders-queue")
    public void processOrder(Order order) {
        // Process the order
    }
}
```

### 6. What are some common RabbitMQ interview questions?

> **Why this is asked**: This meta-question tests your depth of knowledge about messaging systems.

**1. How do you handle message persistence in RabbitMQ?**
- Set queue to durable when creating
- Mark messages as persistent (delivery mode 2)
- Enable publisher confirms
- Example: `new Queue("myQueue", true)` (second parameter makes it durable)

**2. What happens when a consumer fails to process a message?**
- Message acknowledgment is not sent
- RabbitMQ keeps message in queue
- Message can be redelivered to same or different consumer
- Dead letter exchanges can handle repeatedly failed messages

**3. How do you scale RabbitMQ for high throughput?**
- Multiple consumers per queue (parallel processing)
- Cluster RabbitMQ nodes for high availability
- Implement consumer prefetch to optimize delivery
- Batch message publishing and consuming

**4. How do you monitor RabbitMQ health?**
- RabbitMQ Management UI
- HTTP API for metrics
- Prometheus and Grafana integration
- Monitor queue depths, message rates, and consumer utilization

**5. How do you implement request-reply pattern with RabbitMQ?**
```java
// Sender
public String sendAndReceive(String request) {
    return rabbitTemplate.convertSendAndReceive(
        "request-exchange", 
        "requests", 
        request
    ).toString();
}

// Receiver
@RabbitListener(queues = "request-queue")
public String processRequest(String request) {
    return "Processed: " + request;
}
```

## Exception Handling & Persistence

### 7. How do you handle exception management in your project?

> **Why this is asked**: This evaluates your approach to resilient application design and error handling practices.

**Exception Handling Best Practices:**

1. **Global Exception Handling:**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            "RESOURCE_NOT_FOUND", 
            ex.getMessage(), 
            HttpStatus.NOT_FOUND.value()
        );
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }
    
    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ErrorResponse> handleValidation(ValidationException ex) {
        ErrorResponse error = new ErrorResponse(
            "VALIDATION_ERROR", 
            ex.getMessage(), 
            HttpStatus.BAD_REQUEST.value()
        );
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        ErrorResponse error = new ErrorResponse(
            "INTERNAL_ERROR", 
            "An unexpected error occurred", 
            HttpStatus.INTERNAL_SERVER_ERROR.value()
        );
        // Log the full error internally
        log.error("Unexpected error", ex);
        return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

2. **Custom Exception Hierarchy:**
```java
// Base exception
public abstract class BaseApplicationException extends RuntimeException {
    private final String errorCode;
    
    public BaseApplicationException(String message, String errorCode) {
        super(message);
        this.errorCode = errorCode;
    }
    
    public String getErrorCode() {
        return errorCode;
    }
}

// Specific exceptions
public class ResourceNotFoundException extends BaseApplicationException {
    public ResourceNotFoundException(String resource, String id) {
        super(String.format("%s with id %s not found", resource, id), "RESOURCE_NOT_FOUND");
    }
}
```

3. **Service Layer Exception Handling:**
```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public User getUserById(String id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User", id));
    }
    
    public User createUser(User user) {
        try {
            return userRepository.save(user);
        } catch (DataIntegrityViolationException e) {
            throw new ValidationException("User with this email already exists");
        }
    }
}
```

4. **Circuit Breaker Pattern** (for microservices):
```java
@Service
public class ProductService {
    
    @CircuitBreaker(
        name = "inventoryService",
        fallbackMethod = "getDefaultInventory"
    )
    public InventoryStatus getInventoryStatus(String productId) {
        // Call to external inventory service
        return inventoryClient.checkInventory(productId);
    }
    
    public InventoryStatus getDefaultInventory(String productId, Exception ex) {
        log.warn("Using fallback for inventory check: {}", ex.getMessage());
        return new InventoryStatus(productId, 0, false);
    }
}
```

5. **Exception Documentation:**
```java
/**
 * Retrieves a user by their unique identifier
 * 
 * @param id The user's unique identifier
 * @return The user object
 * @throws ResourceNotFoundException if no user with the given ID exists
 */
public User getUserById(String id) {
    // Implementation
}
```

### 8. What is the difference between eager fetch and lazy fetch in Hibernate, and how do they work?

> **Why this is asked**: This tests your understanding of ORM performance optimization and database access patterns.

**Eager Fetching vs. Lazy Fetching:**

| Aspect | Eager Fetching | Lazy Fetching |
|--------|---------------|---------------|
| Definition | Loads associated entities immediately when parent is loaded | Loads associated entities only when explicitly accessed |
| Default for | `@ManyToOne`, `@OneToOne` | `@OneToMany`, `@ManyToMany` |
| Performance | Better for small, frequently accessed relationships | Better for large collections or rarely accessed data |
| N+1 Problem | Avoids N+1 queries | Can cause N+1 query problem if not managed properly |
| Session Requirements | No session needed when accessing relationships | Requires open session when accessing lazy relationships |

**Eager Fetching Example:**
```java
@Entity
public class Employee {
    @Id
    private Long id;
    
    private String name;
    
    @ManyToOne(fetch = FetchType.EAGER)
    private Department department; // Loaded immediately with Employee
}
```

**Lazy Fetching Example:**
```java
@Entity
public class Department {
    @Id
    private Long id;
    
    private String name;
    
    @OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
    private List<Employee> employees; // Loaded only when accessed
}
```

**Handling Lazy Loading Issues:**

1. **Open Session In View Pattern:**
```java
// In Spring Boot application.properties
spring.jpa.open-in-view=true  // Keeps session open for entire request
```

2. **Using Join Fetch in JPQL:**
```java
@Repository
public interface DepartmentRepository extends JpaRepository<Department, Long> {
    
    @Query("SELECT d FROM Department d LEFT JOIN FETCH d.employees WHERE d.id = :id")
    Optional<Department> findByIdWithEmployees(@Param("id") Long id);
}
```

3. **Using Entity Graphs:**
```java
@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    
    @EntityGraph(attributePaths = {"department", "projects"})
    Optional<Employee> findById(Long id);
}
```

4. **DTO Projection to Avoid Lazy Loading:**
```java
@Repository
public interface DepartmentRepository extends JpaRepository<Department, Long> {
    
    @Query("SELECT new com.example.dto.DepartmentSummary(d.id, d.name, COUNT(e)) " +
           "FROM Department d LEFT JOIN d.employees e WHERE d.id = :id GROUP BY d.id")
    DepartmentSummary getDepartmentSummary(@Param("id") Long id);
}
