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
