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
```
