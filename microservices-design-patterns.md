## Saga pattern

### **Problem Context**:
In modern distributed systems, transactions often span across multiple microservices. For instance, in an e-commerce platform, placing an order involves multiple microservices such as:
- **Order service**: Places the order.
- **Payment service**: Charges the user.
- **Inventory service**: Reserves or deducts the stock.
- **Shipping service**: Schedules shipping.

In traditional monolithic systems, these operations could be part of a single ACID (Atomicity, Consistency, Isolation, Durability) transaction, ensuring that either all steps succeed or all fail, leaving the system in a consistent state. However, in a distributed microservices architecture, maintaining a distributed ACID transaction is difficult due to the lack of a global transaction manager and the asynchronous nature of services.

In this context, **partial failures** can happen. For example:
- The payment service succeeds, but the inventory service fails, causing an inconsistent state (e.g., the customer is charged, but the product is not reserved).

### **Solution: Saga Pattern**:
The **Saga Pattern** is a pattern used to manage distributed transactions across microservices by breaking a transaction into smaller, isolated steps, with compensation mechanisms for rollback. Each step is a transaction on its own, and if a step fails, the system rolls back by triggering compensating actions for the already completed steps.

#### Key Concepts of the Saga Pattern:
1. **Distributed Transaction**: Instead of a single monolithic transaction, each service performs its operation independently.
2. **Compensation**: If one of the operations fails, previous successful operations are undone by issuing compensating transactions.
3. **Choreography or Orchestration**: Sagas can be implemented either using a **choreography** (each service is responsible for invoking the next one) or **orchestration** (a central coordinator handles the transaction flow).

### **How the Saga Pattern Works**:
1. **Break Down the Transaction**: Divide the distributed transaction into a series of smaller, independent steps, each handled by a different service. Each step is a local transaction that updates the state of the service.
2. **Forward Processing**: Each service completes its part of the transaction, and upon successful completion, either invokes the next service (choreography) or informs the coordinator to move to the next step (orchestration).
3. **Rollback (Compensation)**: If any step fails, compensating actions are invoked in reverse order to undo the already completed steps (e.g., refund the payment, restock the inventory).
   
#### Types of Sagas:
1. **Choreography-Based Saga**: Each service knows what to do next, triggering the next service directly. No central coordinator exists.
   
2. **Orchestration-Based Saga**: A central orchestrator (a Saga coordinator) is responsible for invoking each step in the correct sequence and handling failures by calling compensating transactions.

### **Example Scenario**:
Consider an online travel booking system, where booking a trip involves multiple steps:
1. **Book Flight**.
2. **Reserve Hotel**.
3. **Rent Car**.

Each of these actions is handled by a separate microservice.

#### Problem:
If the hotel reservation fails after the flight has been booked, the system needs to roll back the flight booking to avoid leaving the customer with only a flight and no hotel.

### **Solution Using the Saga Pattern**:
1. **Forward Flow**:
   - **Step 1**: The user requests to book a trip.
   - **Step 2**: The **Flight service** books the flight.
   - **Step 3**: The **Hotel service** reserves a room.
   - **Step 4**: The **Car rental service** books a car.

2. **Compensation Flow (Rollback)**:
   - If the **Hotel service** fails, the **Flight service** is called to cancel the booked flight, and the **Car rental service** cancels the car reservation.

This way, even though the entire transaction (trip booking) fails, the system maintains consistency by undoing the parts that were already completed.

### **Implementation Approaches**:

#### 1. **Choreography-Based Saga**:
In this approach, each service is responsible for triggering the next step in the transaction and handling failures.

**Steps**:
- **Flight Service**: Books the flight and, on success, triggers the **Hotel Service**.
- **Hotel Service**: Books the hotel, and on success, triggers the **Car Rental Service**.
- If the **Hotel Service** fails, it triggers a compensating action to cancel the flight.

**Pros**:
- Simple to implement.
- Services remain loosely coupled.

**Cons**:
- Hard to maintain as the flow logic is spread across multiple services.
- If the number of services grows, it can become complex to manage failures and compensations.

#### 2. **Orchestration-Based Saga**:
In this approach, a central **Saga Orchestrator** manages the workflow of the entire transaction, invoking the individual services in sequence and handling compensation if a step fails.

**Steps**:
- The **Orchestrator** invokes the **Flight Service** to book the flight.
- Once the flight is successfully booked, the **Orchestrator** invokes the **Hotel Service**.
- If any service fails, the **Orchestrator** triggers compensating actions for the previous services (e.g., cancel the flight and rental).

**Pros**:
- Easier to manage complex workflows since all logic is centralized.
- Centralized error handling.

**Cons**:
- Introduces a single point of failure (the Orchestrator).
- Tight coupling between the orchestrator and services.

### **Example: Orchestration-Based Saga in Spring Boot**:

Here’s how you can implement the Saga pattern using orchestration in Spring Boot:

#### **1. Define Services**:

- **Flight Service**: Books or cancels a flight.
- **Hotel Service**: Reserves or cancels a hotel booking.
- **Car Rental Service**: Reserves or cancels a car rental.

```java
@RestController
public class FlightService {
    @PostMapping("/bookFlight")
    public String bookFlight() {
        // Book flight logic
        return "Flight booked successfully";
    }

    @PostMapping("/cancelFlight")
    public String cancelFlight() {
        // Cancel flight logic
        return "Flight booking canceled";
    }
}
```

#### **2. Saga Orchestrator**:

Create a **Saga Orchestrator** that handles the steps of the transaction and triggers compensating actions on failure.

```java
@Service
public class TravelBookingSaga {

    @Autowired
    private FlightService flightService;

    @Autowired
    private HotelService hotelService;

    @Autowired
    private CarRentalService carRentalService;

    public void bookTrip() {
        try {
            flightService.bookFlight();
            hotelService.reserveHotel();
            carRentalService.bookCar();
        } catch (Exception e) {
            // If any service fails, trigger compensation
            compensate();
        }
    }

    public void compensate() {
        flightService.cancelFlight();
        hotelService.cancelHotel();
        carRentalService.cancelCar();
    }
}
```

#### **3. Saga Execution**:

Finally, the Saga is executed by invoking the **bookTrip** method. If any service fails, compensating transactions are triggered.

```java
@RestController
public class BookingController {

    @Autowired
    private TravelBookingSaga travelBookingSaga;

    @PostMapping("/bookTrip")
    public String bookTrip() {
        travelBookingSaga.bookTrip();
        return "Trip booking completed or compensated!";
    }
}
```

### **Benefits of the Saga Pattern**:
1. **Failure Handling**: Provides a mechanism to handle partial failures in distributed transactions by undoing completed operations.
2. **Decentralized Transactions**: Each service handles its own transaction, making the system more resilient and scalable.
3. **Flexibility**: The pattern can handle complex workflows where steps are interdependent.

### **Challenges**:
1. **Complexity of Compensations**: Writing compensating logic can be complex, especially when different services interact in non-trivial ways.
2. **Idempotency**: Services involved in the Saga must be idempotent, meaning repeated executions of the same operation should not have unintended side effects.
3. **Choreography Complexity**: In choreography-based Sagas, managing the workflow across multiple services can lead to complexity as services must communicate and trigger the next steps.

### **Conclusion**:
The **Saga Pattern** is an effective solution for managing distributed transactions in microservices. It helps ensure that even when parts of the system fail, the overall system remains consistent by compensating for completed steps. By either orchestrating or choreographing the steps, the pattern balances simplicity and flexibility depending on the complexity of the system.


--------
## Priority Queue Pattern

### **Problem Context**:
In a system where multiple types of requests are handled, some requests are more critical than others. For instance:
- In an e-commerce platform, requests for processing payments or completing transactions are more critical than viewing product details.
- In cloud infrastructure, tasks such as scaling or provisioning resources are more critical than gathering metrics or health checks.

In such systems, when the system is under heavy load, there is a risk that low-priority requests can overwhelm the system, resulting in high-priority or time-sensitive requests being delayed. If all requests are treated equally, important tasks might face latency or even fail due to resource exhaustion.

To ensure the system responds appropriately, requests should be prioritized based on their importance, and high-priority requests should be processed faster or before low-priority ones.

### **Solution: Rate Priority Queue Pattern**:
The **Rate Priority Queue Pattern** helps manage and process requests based on their priority. By placing requests in a queue and assigning priority levels, the system can ensure that critical requests are processed first, while less important tasks are handled later or throttled under heavy load.

#### Key Concepts:
1. **Queue**: Requests are placed in a queue that prioritizes them based on their importance.
2. **Rate Limiting**: To prevent overloading, each type of request (based on priority) can be rate-limited, meaning you can control the rate at which requests of different priorities are processed.
3. **Priority Levels**: Requests are classified into different priority levels (e.g., high, medium, low) so that higher-priority requests are processed before lower-priority ones.

### **How the Pattern Works**:
1. **Prioritize Requests**: As requests come in, they are classified into different priority levels (e.g., high, medium, low).
   
2. **Enqueue Based on Priority**: Requests are added to a priority queue, where higher-priority requests are placed ahead of lower-priority ones.
   
3. **Process Requests Based on Rate**: The system processes requests in the queue based on their priority. You can apply different rate limits to each priority level to ensure fairness. For example, high-priority requests are processed immediately, while lower-priority requests are throttled.
   
4. **Dynamic Reordering**: The system may dynamically adjust priority levels based on certain conditions (e.g., if a low-priority task becomes time-sensitive, it may be moved to a higher priority).

### **Example Scenario**:
Consider a cloud infrastructure service where users can:
- **Deploy new virtual machines (VMs)**: High priority (time-sensitive).
- **Query system metrics**: Medium priority.
- **Generate reports**: Low priority.

During peak usage, if users are generating numerous reports, this can slow down or block requests for deploying new VMs, which is unacceptable since deploying VMs is time-sensitive.

Using the Rate Priority Queue pattern, you can:
- Assign **high priority** to requests for deploying VMs.
- Assign **medium priority** to system metric queries.
- Assign **low priority** to report generation.

When a surge of requests occurs, the system ensures that VM deployments are handled first, followed by metric queries, and finally, report generation is processed as resources become available.

### **Steps for Implementing the Rate Priority Queue Pattern**:

1. **Classify Requests by Priority**:
   - Define priority levels for different types of requests. For example:
     - **High**: Time-sensitive actions (e.g., payments, resource provisioning).
     - **Medium**: Regular user actions (e.g., metric gathering, data fetch).
     - **Low**: Background tasks (e.g., reporting, data aggregation).
   
2. **Implement a Priority Queue**:
   - Use a data structure like a **priority queue** to hold requests, ensuring that higher-priority tasks are dequeued and processed first.
   - In Java, you can use a `PriorityBlockingQueue` to manage concurrency and priority-based processing.
   
3. **Enforce Rate Limits for Each Priority**:
   - Set different rate limits for each priority level to avoid overloading the system.
   - High-priority requests may have a higher rate limit (or no rate limit at all), while low-priority requests are processed at a lower rate.
   - For example, you can use **Bucket4j** or a similar library to implement rate limiting at different levels.

4. **Process Requests Based on Priority and Rate**:
   - A dispatcher service reads from the priority queue and processes requests based on their priority and the available rate limit.
   - If a low-priority task exceeds its rate limit, it is temporarily delayed to give preference to higher-priority tasks.

5. **Handle Backpressure**:
   - If the system is overwhelmed, lower-priority tasks may be throttled or rejected (e.g., by returning an HTTP `429 Too Many Requests` status), while high-priority tasks continue to be processed without delays.

### **Example Implementation in Spring Boot**:

Using Spring Boot, you can implement the Rate Priority Queue pattern with rate-limited priority queues. Here’s a simplified approach using Java’s `PriorityBlockingQueue` and rate limiting with `Bucket4j`.

#### **1. Define Request Priority Levels**:

```java
public enum Priority {
    HIGH, MEDIUM, LOW
}
```

#### **2. Implement Priority Queue Logic**:

```java
import java.util.concurrent.PriorityBlockingQueue;

public class PriorityRequestQueue {

    // Priority queue that stores requests and orders them by priority
    private final PriorityBlockingQueue<PriorityRequest> queue = new PriorityBlockingQueue<>();

    public void addRequest(PriorityRequest request) {
        queue.put(request);
    }

    public PriorityRequest getNextRequest() throws InterruptedException {
        return queue.take(); // Automatically handles priority
    }
}
```

#### **3. Define PriorityRequest Class**:

```java
public class PriorityRequest implements Comparable<PriorityRequest> {
    private final Priority priority;
    private final String data;

    public PriorityRequest(Priority priority, String data) {
        this.priority = priority;
        this.data = data;
    }

    public Priority getPriority() {
        return priority;
    }

    @Override
    public int compareTo(PriorityRequest other) {
        return this.priority.compareTo(other.getPriority()); // Higher priority processed first
    }

    public String getData() {
        return data;
    }
}
```

#### **4. Implement Rate Limiting (Bucket4j)**:

```java
import io.github.bucket4j.Bucket;
import io.github.bucket4j.Bucket4j;
import io.github.bucket4j.Refill;
import io.github.bucket4j.Bandwidth;

import java.time.Duration;

public class RateLimiter {

    private final Bucket highPriorityBucket;
    private final Bucket mediumPriorityBucket;
    private final Bucket lowPriorityBucket;

    public RateLimiter() {
        highPriorityBucket = createBucket(100);  // Higher rate for high priority
        mediumPriorityBucket = createBucket(50);  // Medium rate
        lowPriorityBucket = createBucket(20);    // Lower rate for low priority
    }

    private Bucket createBucket(int capacity) {
        Bandwidth limit = Bandwidth.classic(capacity, Refill.greedy(capacity, Duration.ofMinutes(1)));
        return Bucket4j.builder().addLimit(limit).build();
    }

    public boolean tryConsume(Priority priority) {
        switch (priority) {
            case HIGH:
                return highPriorityBucket.tryConsume(1);
            case MEDIUM:
                return mediumPriorityBucket.tryConsume(1);
            case LOW:
                return lowPriorityBucket.tryConsume(1);
            default:
                return false;
        }
    }
}
```

#### **5. Process Requests Based on Priority**:

```java
public class RequestProcessor {

    private final PriorityRequestQueue queue;
    private final RateLimiter rateLimiter;

    public RequestProcessor(PriorityRequestQueue queue, RateLimiter rateLimiter) {
        this.queue = queue;
        this.rateLimiter = rateLimiter;
    }

    public void process() throws InterruptedException {
        while (true) {
            PriorityRequest request = queue.getNextRequest();

            // Rate limit based on priority
            if (rateLimiter.tryConsume(request.getPriority())) {
                // Process the request
                System.out.println("Processing: " + request.getData());
            } else {
                System.out.println("Rate limit exceeded for: " + request.getPriority());
            }
        }
    }
}
```

### **6. Main Class to Simulate the System**:

```java
public class Main {

    public static void main(String[] args) throws InterruptedException {
        PriorityRequestQueue queue = new PriorityRequestQueue();
        RateLimiter rateLimiter = new RateLimiter();
        RequestProcessor processor = new RequestProcessor(queue, rateLimiter);

        // Add some requests
        queue.addRequest(new PriorityRequest(Priority.HIGH, "High priority request"));
        queue.addRequest(new PriorityRequest(Priority.LOW, "Low priority request"));
        queue.addRequest(new PriorityRequest(Priority.MEDIUM, "Medium priority request"));

        // Process the requests
        processor.process();
    }
}
```

### **Benefits of the Rate Priority Queue Pattern**:
1. **Fairness and Responsiveness**: Critical, high-priority tasks are processed quickly, ensuring timely responses.
2. **Controlled Load**: Rate limiting ensures that the system is not overwhelmed by low-priority or non-essential tasks.
3. **Improved Resource Utilization**: Important tasks are prioritized, preventing non-essential tasks from consuming resources.
4. **Flexibility**: Allows dynamic adjustment of priority levels based on changing business needs.

### **Challenges**:
1. **Managing Complexity**: Designing and maintaining a system with multiple queues and rate limits can become complex.
2. **Rate Limit Tuning**: Finding the right rate limits for different priority levels requires careful monitoring and tuning

--------

## Strangler Pattern

### **Problem Context**:
In large, monolithic systems, as applications evolve and grow, they become increasingly difficult to maintain. Some of the common issues that arise include:
- **Complexity**: Over time, monolithic codebases become large and complex, making it hard to add new features or make changes without introducing bugs.
- **Scaling Issues**: Monolithic applications typically scale as a whole, which can be inefficient. For instance, scaling a feature that is heavily used requires scaling the entire application, even if other features don’t need it.
- **Technological Debt**: Older systems may be built on outdated technology stacks, making them difficult to maintain and impossible to take advantage of modern development practices.
- **High Risk of Changes**: Modifying one part of a monolithic application can inadvertently break other parts, leading to extensive regression testing and deployment difficulties.
  
To address these challenges, organizations often consider migrating to a microservices architecture. However, fully replacing a monolith with microservices in one go is risky, time-consuming, and resource-intensive. This is where the **Strangler Pattern** (sometimes called the **Strangler Fig Pattern**) comes in.

### **Solution: Strangler Pattern**:
The **Strangler Pattern** is a gradual migration strategy that allows you to incrementally refactor and replace parts of a monolithic application with new services or components. The pattern gets its name from the strangler fig tree, which grows around an existing tree and eventually replaces it.

#### Key Idea:
- Rather than re-writing or replacing the entire monolithic system in one go, you gradually replace functionality by creating new microservices or components that handle specific parts of the application.
- As you add new services, the old monolithic code responsible for the same functionality is deprecated or "strangled," and over time the monolith becomes smaller and smaller until it is fully replaced by the new system.

#### How it Works:
1. **Identify a Functionality to Extract**: Start by identifying a piece of functionality (e.g., a feature or module) in the monolithic application that can be migrated independently.
   
2. **Build a New Service**: Develop a new microservice or component that handles the identified functionality. This new service should be designed and deployed independently from the monolith.

3. **Redirect Traffic**: Modify the routing logic to redirect requests related to the identified functionality from the monolith to the new service. This can be done via an API gateway, a proxy, or other routing mechanisms.

4. **Deprecate Old Functionality**: Once the new service is fully functional and reliable, the corresponding code in the monolith can be deprecated and eventually removed.

5. **Repeat**: The process is repeated iteratively, with more and more functionalities extracted from the monolith into new services until the entire system is modernized.

### Example Scenario:
Imagine you have an old e-commerce platform that is implemented as a monolithic application. The platform has various functionalities like user management, inventory management, payment processing, and order management.

If you want to move to a microservices architecture without disrupting the whole system, you can use the Strangler pattern to:
1. **Start with a Function**: Identify a small, isolated functionality to migrate first, like the **Inventory Management** module.
2. **Create a New Service**: Build a new microservice that handles inventory management, separate from the monolith.
3. **Route Requests**: Route all requests related to inventory (via an API gateway) to the new microservice instead of the monolithic inventory module.
4. **Deprecate Old Code**: Once the inventory microservice is stable, remove the corresponding inventory logic from the monolith.
5. **Move to Other Modules**: Repeat the process for other functionalities, like user management or payment processing, until the entire application has been refactored into microservices.

### **Steps for Implementing the Strangler Pattern**:

1. **Analysis and Decomposition**:
   - Identify components or modules in the monolith that can be isolated and migrated.
   - Ensure the component has well-defined boundaries (e.g., APIs, business logic) and minimal dependencies on other parts of the monolith.

2. **Implement an API Gateway or Reverse Proxy**:
   - Introduce a gateway that intercepts incoming requests to the monolith.
   - The gateway can be configured to route requests either to the monolith or to new microservices as they are developed.
   - For example, incoming requests for inventory management would be directed to the new microservice, while other requests still go to the monolith.

3. **Build and Test New Service**:
   - Develop the new microservice with the desired functionality.
   - Perform thorough testing to ensure it works correctly with the rest of the system.
   - Integrate the service with the API Gateway.

4. **Incremental Migration**:
   - Migrate the functionality from the monolith to the microservice gradually. As each new microservice is built, requests related to its functionality are routed to the new service.
   - The monolithic code handling that functionality is gradually deprecated.

5. **Monitor and Optimize**:
   - Monitor the system to ensure that the migration is smooth, and there are no performance or integration issues.
   - Optimize new services as needed, including scaling independently.

6. **Complete Migration**:
   - After all functionalities have been moved to microservices, the monolith can either be discarded or continue running with minimal responsibilities.

### **Benefits of the Strangler Pattern**:

1. **Reduced Risk**: Since the migration is done incrementally, there is less risk of system failure compared to a complete re-write.
2. **No Disruption**: The monolithic application continues to function while migration happens in the background, minimizing disruptions to users.
3. **Faster Time-to-Market**: Instead of spending months or years re-writing the entire application, individual services can be developed and deployed incrementally.
4. **Modernization**: It allows you to adopt modern technologies and architectural practices (e.g., microservices, cloud-native) without rewriting the entire codebase at once.
5. **Scalability**: Each microservice can be scaled independently, improving system performance and reducing resource wastage.

### **Challenges**:

1. **Routing Complexity**: Managing traffic between the monolith and new services via an API gateway can introduce complexity. You need to ensure correct routing and backward compatibility.
2. **Data Synchronization**: In cases where both the monolith and new services share a database, maintaining consistency can be challenging.
3. **Refactoring Costs**: Although less risky than a complete re-write, the process still requires significant time and resources.
4. **Partial Dependency**: If some features remain in the monolith, microservices may still depend on the monolithic database or shared components, creating hybrid architectures that are harder to manage.

### **When to Use the Strangler Pattern**:
- When you need to modernize a legacy system without halting operations.
- When you want to incrementally migrate to a microservices architecture.
- When the monolith is too complex or risky to replace in one go.
- When the system is large, and a complete re-write would take too much time or disrupt business operations.

### Example Implementation:

Let's assume you want to migrate the **Order Management** functionality in your monolithic e-commerce application to a microservice.

1. **API Gateway Setup**:
   You set up an API Gateway that will handle routing for the application.
   
2. **Develop Order Microservice**:
   You develop a new microservice that handles order creation, updates, and cancellations.

3. **Modify Routing**:
   Configure the API Gateway so that all requests for order management are routed to the new microservice instead of the monolith.

4. **Deprecate Old Code**:
   Once the microservice is stable and fully functional, remove the order management logic from the monolith.

By repeating this process for other components (e.g., user management, payment), you can eventually "strangle" the entire monolith, leaving behind a modernized, service-oriented architecture.

The **Strangler Pattern** provides a safe, incremental approach to modernizing and refactoring legacy systems. It allows businesses to adopt newer technologies without the risk of full rewrites, ensuring continued service availability throughout the migration process.




--------

## Rate Limiting Pattern

### **Problem Context**:
In distributed systems, APIs or services are often shared by multiple clients, which can lead to several challenges:
- **Resource Exhaustion**: If clients make an excessive number of requests, it can overwhelm the system, consuming critical resources like CPU, memory, or network bandwidth.
- **Service Downtime**: Overloaded services may crash or become unresponsive, causing downtime and impacting all users.
- **Fair Usage**: In multi-tenant systems, one client or user could monopolize resources, leading to an unfair experience for others.
- **Security and Abuse Prevention**: Attackers or malicious users might intentionally flood an API with requests (e.g., Denial of Service attacks), causing service disruptions.

In such cases, the system needs to control how many requests it accepts within a given time frame. This can prevent abuse, ensure fair usage, and protect the system from being overwhelmed. This is where the **Rate Limiting Pattern** comes into play.

### **Solution: Rate Limiting Pattern**:
**Rate limiting** is a pattern used to control the rate at which requests are processed by a service. It ensures that a system or API only allows a certain number of requests within a specified time window, thus preventing overloading and ensuring equitable access to resources.

#### How Rate Limiting Works:
Rate limiting enforces limits based on specific criteria, such as:
- **Requests per second/minute/hour**: Limiting the number of requests a client can make within a specific time window.
- **Quota per user or client**: Restricting the total number of requests allowed for each client or user.
- **IP-based throttling**: Restricting the number of requests based on the client's IP address.
- **Token or Key-based limits**: Using an API key or token to apply limits specific to a user or application.

Once the limit is reached, the service can either:
- **Reject further requests** (returning a `429 Too Many Requests` HTTP status code).
- **Queue the requests** for later processing.
- **Delay the requests** using a backoff mechanism.

### **Common Rate Limiting Algorithms**:
Several algorithms are commonly used to implement rate limiting:
1. **Token Bucket Algorithm**: A token bucket is filled at a constant rate. Each request consumes a token. If no tokens are available, the request is denied or delayed.
2. **Leaky Bucket Algorithm**: Requests are processed at a fixed rate, similar to water leaking from a bucket. Excessive requests are dropped.
3. **Fixed Window Counter**: A fixed time window is defined, and requests are counted within that window. Once the limit is reached, no more requests are allowed until the window resets.
4. **Sliding Window Log**: A more granular approach where a moving window is used to track requests, preventing spikes just before the window resets.
5. **Sliding Window Counter**: Combines features of fixed window and sliding window log, distributing limits more evenly across a time period.

### Example Scenario:
Consider a public-facing API that provides weather data. Without any controls, some clients may continuously bombard the service with requests, overwhelming the system. This can degrade the quality of service for other clients or even cause an outage.

To avoid this:
- Implement rate limiting to allow, for example, only **100 requests per minute** per client.
- If a client exceeds the limit, return a `429 Too Many Requests` response and ask them to retry after some time.

### **Rate Limiting Implementation in Spring Boot**
Using Spring Boot and libraries like **Bucket4j**, you can easily implement rate limiting in a service.

### **1. Add Dependencies**
Add the `Bucket4j` dependency to your `pom.xml` (if using Maven):

```xml
<dependency>
    <groupId>com.github.vladimir-bukhtoyarov</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>8.0.0</version> <!-- or latest version -->
</dependency>
```

### **2. Implement Rate Limiting**
Let's create a simple rate-limited API.

```java
import io.github.bucket4j.Bucket;
import io.github.bucket4j.Bucket4j;
import io.github.bucket4j.Refill;
import io.github.bucket4j.Bandwidth;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.Duration;

@RestController
@RequestMapping("/api")
public class RateLimitedController {

    // Create a Bucket (token bucket algorithm) with 100 requests per minute
    private final Bucket bucket;

    public RateLimitedController() {
        Bandwidth limit = Bandwidth.classic(100, Refill.greedy(100, Duration.ofMinutes(1)));
        this.bucket = Bucket4j.builder().addLimit(limit).build();
    }

    @GetMapping("/data")
    public ResponseEntity<String> getData() {
        // Check if a token is available
        if (bucket.tryConsume(1)) {
            return ResponseEntity.ok("Data response");
        } else {
            // If no tokens are available, return 429 Too Many Requests
            return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS)
                    .body("Rate limit exceeded. Please try again later.");
        }
    }
}
```

### Explanation:
- **Bucket**: We use the **Token Bucket** algorithm here, where the bucket can hold 100 tokens (requests), and it refills at a rate of 100 tokens per minute.
- **`tryConsume(1)`**: Each request consumes a token from the bucket. If tokens are available, the request is processed; otherwise, a `429 Too Many Requests` response is returned.
- This ensures that no more than 100 requests are processed per minute for this endpoint.

### **3. Customize Rate Limits per User (Optional)**
You can apply different rate limits for each user or client. This can be done by associating each client with a unique bucket.

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

@RestController
@RequestMapping("/api")
public class UserRateLimitedController {

    private final Map<String, Bucket> cache = new ConcurrentHashMap<>();

    @GetMapping("/user-data")
    public ResponseEntity<String> getUserData(String userId) {
        Bucket bucket = getBucketForUser(userId);

        if (bucket.tryConsume(1)) {
            return ResponseEntity.ok("User-specific data response");
        } else {
            return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS)
                    .body("Rate limit exceeded for user " + userId);
        }
    }

    private Bucket getBucketForUser(String userId) {
        return cache.computeIfAbsent(userId, k -> {
            Bandwidth limit = Bandwidth.classic(50, Refill.greedy(50, Duration.ofMinutes(1)));
            return Bucket4j.builder().addLimit(limit).build();
        });
    }
}
```

Here, each user gets a unique rate-limiting bucket, allowing different limits for different users.

### **Benefits of Rate Limiting**:
1. **Prevent Resource Exhaustion**: By controlling the number of requests, rate limiting prevents the service from being overwhelmed.
2. **Fair Usage**: Ensures fair resource allocation among users, preventing a single client from monopolizing the system.
3. **Improved System Stability**: Helps maintain system stability during high traffic by spreading out load.
4. **Defend Against Attacks**: Protects against malicious attacks such as DoS by limiting the number of requests from a single source.
5. **Customizable Limits**: Rate limits can be tailored per user, per IP, or per API key, allowing for flexibility in handling different client needs.

### **Challenges**:
1. **Delay in Service**: Users may experience delays or rejections when limits are exceeded.
2. **Complexity in Multi-Node Systems**: In distributed systems, rate limits need to be enforced across all nodes consistently, which can add complexity.
3. **Balancing Fairness and Performance**: Setting appropriate limits that balance system performance and user experience can be challenging.

The **Rate Limiting Pattern** is crucial for controlling access to resources and ensuring that services remain reliable and responsive under high load conditions, preventing overloads and promoting fair usage.


-------

## CQRS Pattern

### **Problem Context**:
In traditional architectures, applications use a single model for both reading and writing data (CRUD operations). This works well for simple systems, but as the system grows in complexity, several issues arise:
- **Performance Bottlenecks**: Read and write operations often have different performance and scalability requirements. For instance, reads might outnumber writes significantly, leading to performance degradation when both are handled by the same model.
- **Conflicting Optimization**: Systems that need to handle both reads and writes efficiently often have conflicting requirements. Data models optimized for fast reads (e.g., denormalized) may not be ideal for writes (which might require normalization), and vice versa.
- **Complexity in Business Logic**: Handling complex validation, auditing, or transactional integrity becomes challenging when the same model is used for both operations.

To address these issues, the **CQRS** (Command Query Responsibility Segregation) pattern can be applied. It is especially useful in large, complex systems where read and write workloads have different requirements.

### **Solution: CQRS Pattern**:
The **Command Query Responsibility Segregation (CQRS)** pattern is an architectural pattern that separates the models for reading (queries) and writing (commands) data. The core idea is to have distinct models: one for handling commands (which modify the data) and another for handling queries (which retrieve the data).

#### Key Aspects of the CQRS Pattern:
1. **Command Model**: Responsible for handling write operations (create, update, delete). It focuses on the consistency and integrity of data, usually following stricter validation rules. Commands typically modify the state of the system.
   
2. **Query Model**: Responsible for handling read operations (retrieving data). This model is optimized for fast, efficient reads and may use denormalized or cached data. Queries only fetch data and do not modify it.

3. **Eventual Consistency**: Since the read and write models are separated, the data may not be immediately consistent across both models. This can lead to eventual consistency, where the data becomes consistent over time.

4. **Event Sourcing (optional)**: CQRS is often used in conjunction with **Event Sourcing**, where state changes are stored as a series of events. These events can be replayed to reconstruct the state of the system.

#### Example in a Microservices Architecture:
Consider an e-commerce system where users place orders and the system needs to manage inventory levels. Using CQRS:
- The **Command Model** would handle write operations like placing an order, updating inventory, or cancelling an order. It ensures that the business rules are followed, such as checking that the stock is available before confirming an order.
- The **Query Model** would handle read operations like retrieving product information, order history, or available inventory. This model could be optimized for fast retrieval, potentially using a NoSQL database or in-memory cache for faster reads.

When a user places an order:
- The **Command Model** updates the inventory and order status. 
- An event is triggered to update the **Query Model** so that the next time a query is made, the latest order status or inventory level is reflected.

#### Example Workflow:
1. A user places an order, triggering a `PlaceOrderCommand` in the **Command Model**.
2. The **Command Model** processes the request, validates the business logic (e.g., checking stock), and updates the write database.
3. After the order is placed, an event is generated (e.g., `OrderPlacedEvent`), which updates the **Query Model**.
4. A subsequent query for the order status is made to the **Query Model**, which returns the updated data.

### **Benefits**:
1. **Scalability**: Since read and write workloads are segregated, each model can be scaled independently based on its own performance requirements.
2. **Optimized Data Models**: The command and query sides can have different data schemas, each optimized for its specific operation type (e.g., normalized schema for writes, denormalized schema for reads).
3. **Improved Performance**: By using denormalized or specialized read models, the query performance can be significantly improved, especially for complex queries.
4. **Simplified Complexity**: The separation of concerns makes it easier to manage complex business rules and validation in the command model, while keeping the query model simple and optimized for retrieval.
5. **Eventual Consistency**: In distributed systems, CQRS works well with eventual consistency, where updates happen asynchronously, and systems eventually converge to a consistent state.

### **Challenges**:
1. **Eventual Consistency**: The system may not always be consistent immediately. This can be problematic for scenarios requiring strong consistency.
2. **Increased Complexity**: Managing two separate models, event propagation, and eventual consistency can add complexity to the system’s design and development.
3. **Complex Data Synchronization**: Keeping the read model in sync with the write model can be challenging, especially in the presence of failures or network issues.

### **When to Use CQRS**:
- When read and write operations have significantly different performance, scaling, or security requirements.
- In systems where read operations dominate and need to be optimized for speed.
- When dealing with complex business logic that requires strict validation during write operations.
- In distributed systems where eventual consistency can be tolerated.
- In systems that require auditing or historical data reconstruction, as CQRS can be combined with Event Sourcing.

The CQRS pattern is powerful for improving system scalability and maintainability by decoupling reads and writes. However, it comes with trade-offs, particularly in terms of consistency and added complexity. It’s most beneficial in systems with distinct read and write workloads and where performance optimization is crucial.

-----
## **Bulkhead Pattern**

### **Problem Context**:
In distributed systems, services or applications often rely on other services or components for their functioning. If one of these services or components starts to fail (due to high load, slow response times, or complete unavailability), it can lead to cascading failures throughout the system. This can eventually cause the entire application to become unresponsive or crash, even if only a small part of the system is under strain.

This is a significant concern in microservices architectures, where services are often tightly coupled and dependent on each other. Without proper isolation, a failure in one part of the system can easily propagate to other services, resulting in a system-wide outage.

### **Solution: Bulkhead Pattern**:
The **Bulkhead pattern** is inspired by bulkheads in a ship, which separate the vessel into multiple compartments. In the event of a breach in one compartment, the damage is contained and doesn't spread to other compartments. Similarly, the Bulkhead pattern isolates critical resources, services, or parts of a system, ensuring that failures in one area don’t affect the entire system.

#### Key Aspects of the Bulkhead Pattern:
1. **Isolation of Resources**: Divide the system into isolated partitions (bulkheads), so that failure in one component doesn't bring down the whole system.
2. **Limited Access to Shared Resources**: Limit the amount of resources (threads, connections, etc.) that each component can use. If a component exhausts its allotted resources, the failure is contained within that component.
3. **Graceful Degradation**: Instead of total failure, the system can continue to operate in a reduced capacity, as only a small part of the system is affected.

#### Example in a Microservices Architecture:
- A service can be designed to handle requests to multiple downstream services.
- Using the Bulkhead pattern, you can allocate separate thread pools or connection pools for each downstream service.
- If one of the downstream services becomes slow or unresponsive, only the requests to that particular service are delayed or dropped. The other services continue to operate normally without being affected by the problematic service.

### **Benefits**:
1. **Improved Resilience**: The failure of one component does not affect the rest of the system.
2. **Resource Isolation**: Prevents resource exhaustion by a single failing component.
3. **Graceful Degradation**: Ensures partial availability instead of a complete system crash.
4. **Service Continuity**: Critical services can continue to function, improving overall system reliability.

The Bulkhead pattern is often used alongside other resilience patterns such as Circuit Breakers, Timeouts, and Retries for greater fault tolerance in distributed systems.




----
## Choreography pattern

### **Problem Context**:
In a distributed system or microservices architecture, multiple services often need to collaborate to fulfill a business process. Traditionally, orchestration (a central controller) is used to manage interactions between services. However, as systems grow in complexity, this centralized approach can lead to tight coupling, making the system harder to maintain, scale, and evolve. The central orchestrator can become a bottleneck or single point of failure, impacting the performance and reliability of the system.

The **Choreography pattern** addresses this issue by decentralizing the coordination of services, allowing services to interact directly with one another, creating more loosely coupled interactions.

### **Solution: Choreography Pattern**:
The **Choreography pattern** is an event-driven approach where each service is responsible for reacting to events in the system. Instead of a central orchestrator telling services what to do, services publish events when something significant happens, and other services listen for these events and act accordingly.

In this approach, services coordinate indirectly by emitting and listening to events, allowing for greater flexibility and scalability. Each service knows how to react to events without a centralized decision-maker.

#### Key Aspects of the Choreography Pattern:
1. **Event-Driven Communication**: Services communicate via events, reducing direct dependencies.
2. **Loose Coupling**: Services are only aware of the events they need to respond to, without needing to know about other services.
3. **Autonomy**: Each service can operate independently and evolve without impacting others, improving scalability and maintainability.
4. **Decentralized Control**: There’s no single orchestrator; services decide their actions based on the events they consume.

#### Example in a Microservices Architecture:
Imagine an e-commerce system with separate services for order processing, payment, shipping, and inventory. When an order is placed:
- The **Order Service** publishes an `OrderCreated` event.
- The **Payment Service** listens for the `OrderCreated` event and processes the payment, then publishes a `PaymentProcessed` event.
- The **Shipping Service** listens for the `PaymentProcessed` event and ships the order, then publishes a `OrderShipped` event.
- The **Inventory Service** listens for the `OrderShipped` event and updates stock levels accordingly.

In this choreography setup, each service responds to events and continues the flow of the business process without a central orchestrator telling them what to do.

### **Benefits**:
1. **Scalability**: Services can scale independently, as there's no central orchestrator that could become a bottleneck.
2. **Resilience**: Since control is decentralized, the system can continue functioning even if some services are unavailable.
3. **Flexibility**: New services can be added by simply subscribing to relevant events, allowing the system to evolve over time.
4. **Loose Coupling**: Services are unaware of each other's existence, reducing dependencies and making the system more modular.

### **Challenges**:
- **Complexity in Debugging**: Since there's no central place managing the workflow, tracing and debugging distributed events can be more challenging.
- **Eventual Consistency**: Systems using the Choreography pattern typically exhibit eventual consistency, which may require special handling depending on business requirements.

By using the Choreography pattern, distributed systems can maintain flexibility and resilience without relying on a central orchestrator, allowing services to evolve and scale independently.

------

## Orchestration pattern

### **Problem Context**:
In a microservices architecture, various services often need to collaborate to fulfill a business requirement. These services are usually independent, with their own databases, APIs, and responsibilities. When a business process spans across multiple microservices, it becomes challenging to coordinate these interactions, manage data consistency, and handle failures. 

For example, consider an e-commerce platform where an order process might involve:
- **Inventory Service**: To check and reserve items.
- **Payment Service**: To process payments.
- **Shipping Service**: To schedule delivery.
  
If one of these services fails, handling compensation or retrying the operation becomes complicated. Without proper coordination, service interactions can become chaotic, leading to inconsistency, duplicated logic, and hard-to-manage error handling.

### **Solution: Orchestration Pattern**:
The **Orchestration Pattern** solves this problem by introducing a central orchestrator (or coordinator) that controls and manages the interactions between various services. The orchestrator is responsible for invoking the services in the correct sequence, handling success and failure responses, and applying business logic. This central authority ensures that all the services involved in the process work together in a coordinated manner.

#### **Key Characteristics**:
1. **Centralized Control**: The orchestrator knows the sequence of actions required to complete a business process and invokes each service accordingly.
2. **Failure Management**: The orchestrator handles service failures by retrying, invoking compensating transactions, or executing alternative workflows.
3. **Business Logic**: The orchestrator contains the business rules for how services should interact and in what order.

### **How Orchestration Pattern Works**:
1. **Orchestrator**: A central service that coordinates the workflow of a business process. It invokes services based on predefined business rules and handles responses.
2. **Services**: Each microservice performs its part of the process when invoked by the orchestrator.
3. **Success and Failure Management**: The orchestrator determines what to do next based on the success or failure of each service.

### **Example Scenario**:
Consider a **travel booking system** where booking a trip involves:
1. **Booking a flight**.
2. **Reserving a hotel**.
3. **Renting a car**.

Each of these steps is handled by a separate microservice, but they need to be coordinated in a specific order.

#### **Problem**:
If each service interacts directly with other services without coordination, the system can become hard to manage. For example:
- What happens if the flight booking succeeds but the hotel reservation fails?
- How should the system roll back or compensate for these failures?

### **Solution Using the Orchestration Pattern**:
A central orchestrator can manage the interactions between the services and handle the workflow in an orderly fashion. This central orchestrator will:
1. Call the **Flight Booking Service**.
2. On success, call the **Hotel Reservation Service**.
3. On success, call the **Car Rental Service**.
4. If any step fails, it triggers compensating actions to roll back the previous steps (e.g., cancel the flight or refund payments).

### **Steps in Orchestration**:

1. **Define the Workflow**: The orchestrator defines the business process as a sequence of service calls. It knows which services to call and in what order.
   
2. **Service Invocation**: The orchestrator calls each service, waits for the response, and based on the result, either moves forward or triggers compensating actions.
   
3. **Handle Failures**: If any service fails, the orchestrator can either retry the operation or invoke a compensating transaction to undo the effects of previous steps.

### **Implementation**:

#### **1. Services**:
Let's assume we have three microservices: `FlightService`, `HotelService`, and `CarRentalService`. Each service performs its own task independently.

```java
@RestController
public class FlightService {
    @PostMapping("/bookFlight")
    public String bookFlight() {
        // Logic to book a flight
        return "Flight booked";
    }

    @PostMapping("/cancelFlight")
    public String cancelFlight() {
        // Logic to cancel a flight
        return "Flight booking canceled";
    }
}
```

Similarly, the `HotelService` and `CarRentalService` would have their respective booking and cancellation APIs.

#### **2. Orchestrator**:
The orchestrator is responsible for coordinating the overall workflow. It calls each service in order and manages the result.

```java
@Service
public class TravelBookingOrchestrator {

    @Autowired
    private FlightService flightService;

    @Autowired
    private HotelService hotelService;

    @Autowired
    private CarRentalService carRentalService;

    public String bookTrip() {
        String flightResponse;
        String hotelResponse;
        String carResponse;

        try {
            // Step 1: Book Flight
            flightResponse = flightService.bookFlight();

            // Step 2: Reserve Hotel
            hotelResponse = hotelService.reserveHotel();

            // Step 3: Rent Car
            carResponse = carRentalService.bookCar();
        } catch (Exception e) {
            // If any service fails, trigger compensating actions
            compensate();
            return "Failed to book trip, compensating actions triggered.";
        }

        return "Trip booked successfully!";
    }

    private void compensate() {
        flightService.cancelFlight();
        hotelService.cancelHotel();
        carRentalService.cancelCar();
    }
}
```

#### **3. Controller**:
The user interacts with a controller to initiate the trip booking process.

```java
@RestController
public class TravelBookingController {

    @Autowired
    private TravelBookingOrchestrator orchestrator;

    @PostMapping("/bookTrip")
    public String bookTrip() {
        return orchestrator.bookTrip();
    }
}
```

### **Handling Failure Scenarios**:
In case of a failure in any service (e.g., hotel booking fails), the orchestrator:
1. Stops further execution.
2. Calls the compensating transactions for the already completed steps (e.g., cancels the flight and car rental).
3. Notifies the user that the process could not be completed, and compensating actions were taken.

### **Benefits of Orchestration Pattern**:
1. **Centralized Control**: All logic for coordinating service interactions and managing failures is centralized in the orchestrator, making it easier to manage complex workflows.
2. **Business Logic Isolation**: The orchestrator contains the business rules, so individual services remain focused on their core functionality.
3. **Simplified Failure Handling**: The orchestrator is responsible for handling failures and compensating transactions, reducing complexity in individual services.

### **Drawbacks**:
1. **Single Point of Failure**: The orchestrator itself becomes a critical component. If it fails, the entire workflow fails.
2. **Tight Coupling**: The orchestrator must be aware of the APIs and behavior of all services it coordinates, leading to tighter coupling between services and the orchestrator.
3. **Complexity in Orchestrator**: As workflows grow more complex, the orchestrator logic can become difficult to maintain, especially with many services involved.

### **When to Use the Orchestration Pattern**:
1. **Complex Workflows**: When business processes span across multiple services, requiring coordination and specific sequencing of operations.
2. **Compensating Transactions**: When failure handling and compensating actions are needed to maintain consistency across services.
3. **Centralized Logic**: When you want a single place to manage the workflow and business logic for interacting services.

### **Conclusion**:
The **Orchestration Pattern** is an effective solution for managing complex business processes that involve multiple microservices. By centralizing the workflow in an orchestrator, you can manage service interactions, handle failures, and ensure that compensating actions are triggered when necessary. This pattern provides better control over service coordination compared to the more decentralized choreography approach, making it suitable for complex, failure-prone systems.


------
## Circuit Breaker

### **Problem Context**:
In distributed systems, services often need to communicate with external systems or other microservices. These external services may occasionally fail, become unresponsive, or suffer from performance degradation. When a system continues to make calls to a failing service, it can lead to:
- **Increased latency**: As requests wait for timeouts, the overall response times increase.
- **Resource exhaustion**: The system consumes resources (e.g., threads, memory) waiting for unresponsive services, potentially leading to failures in other parts of the system.
- **Cascading failures**: A failure in one service can ripple through the entire system, causing multiple services to fail.

Without proper handling of such failures, the system could become unresponsive or experience a complete outage.

### **Solution: Circuit Breaker Pattern**:
The **Circuit Breaker pattern** is designed to prevent cascading failures by acting as a safety valve in distributed systems. It wraps a service call and monitors for failures. If the failures reach a certain threshold, the circuit breaker "trips," preventing further calls to the failing service for a specified period. This gives the failing service time to recover, preventing resource waste on repeated failed attempts.

#### How the Circuit Breaker Works:
1. **Closed State**: In this state, the circuit breaker allows all requests to flow through to the external service. The circuit breaker keeps track of the success and failure rates.
   - If the number of failures is below the threshold, the circuit remains closed.
   - If the number of failures exceeds the threshold within a specified time window, the circuit trips and moves to the **Open State**.

2. **Open State**: In this state, the circuit breaker prevents all requests from going to the external service.
   - Instead, it returns an error immediately, preventing further load on the failing service.
   - After a fixed amount of time, the circuit breaker moves to the **Half-Open State** to test if the external service has recovered.

3. **Half-Open State**: In this state, the circuit breaker allows a limited number of requests to go through to the external service.
   - If the requests succeed, the circuit moves back to the **Closed State**.
   - If the requests fail again, the circuit returns to the **Open State**.

#### Example in a Microservices Architecture:
Imagine a microservice-based e-commerce system where the **Order Service** depends on the **Payment Service**. If the **Payment Service** becomes slow or fails, repeated calls from the **Order Service** can overload both services, affecting the entire system.

To prevent this:
- The **Order Service** uses a Circuit Breaker to monitor the status of the **Payment Service**.
- If the **Payment Service** fails repeatedly (e.g., 5 consecutive failures), the Circuit Breaker trips, and further calls to the **Payment Service** are blocked.
- After a predefined period (e.g., 30 seconds), the Circuit Breaker moves to the **Half-Open State** and tests the **Payment Service** with a small number of requests.
- If the **Payment Service** has recovered, the Circuit Breaker closes, and normal operations resume. Otherwise, it remains open.

### **Benefits**:
1. **Improved Resilience**: Prevents cascading failures by stopping calls to failing services.
2. **Resource Optimization**: Reduces resource exhaustion by avoiding repeated attempts to a non-responsive service.
3. **Graceful Recovery**: Gives failing services time to recover without overwhelming them with new requests.
4. **Enhanced System Stability**: Helps maintain overall system stability even when dependent services are temporarily down.

### **Challenges**:
- **Choosing the Right Threshold**: Setting appropriate failure thresholds and timeouts requires careful tuning to avoid tripping the circuit too early or too late.
- **Fallback Strategies**: When the circuit is open, fallback mechanisms (such as default responses or degraded functionality) need to be implemented to maintain some level of service availability.

The Circuit Breaker pattern is a vital tool in improving the resilience of distributed systems by preventing failures from cascading and protecting the overall system from being overwhelmed by repeated failures.

To implement the **Circuit Breaker** pattern in a Spring Boot application, you can use the **Resilience4j** library. Resilience4j provides a powerful and flexible way to add resilience to your services, including circuit breakers, retries, rate limiters, and more.

Here's a step-by-step guide to implementing the **Circuit Breaker** using **Resilience4j** in a Spring Boot application.

### **1. Add Dependencies**
First, add the necessary dependencies to your `pom.xml` file if you're using Maven. You'll need to include `resilience4j-spring-boot2` and `resilience4j-circuitbreaker`.

```xml
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot2</artifactId>
    <version>1.7.1</version> <!-- Use the latest version -->
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

If you're using **Gradle**, add the following dependencies:

```groovy
implementation 'io.github.resilience4j:resilience4j-spring-boot2'
implementation 'org.springframework.boot:spring-boot-starter-aop'
```

### **2. Enable Circuit Breaker in Spring Boot**
In the `application.properties` or `application.yml`, configure the circuit breaker. You can define failure rate thresholds, wait durations, and other parameters.

For example, in **`application.yml`**:

```yaml
resilience4j.circuitbreaker:
  instances:
    externalService:
      registerHealthIndicator: true
      slidingWindowSize: 10
      failureRateThreshold: 50
      waitDurationInOpenState: 10s
      permittedNumberOfCallsInHalfOpenState: 3
      slidingWindowType: COUNT_BASED
```

### **3. Annotate Methods with `@CircuitBreaker`**
You can apply the `@CircuitBreaker` annotation to any method where you want to apply the circuit breaker. This is usually applied to methods making remote calls to external services, like REST APIs or databases.

Example of a service method that makes a remote call:

```java
import io.github.resilience4j.circuitbreaker.annotation.CircuitBreaker;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class ExternalService {

    private final RestTemplate restTemplate;

    public ExternalService(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    @CircuitBreaker(name = "externalService", fallbackMethod = "fallback")
    public String callExternalApi() {
        String url = "https://api.example.com/resource";
        return restTemplate.getForObject(url, String.class);
    }

    // Fallback method
    public String fallback(Throwable t) {
        return "Fallback response: External service is unavailable.";
    }
}
```

Here’s what’s happening:
- `@CircuitBreaker(name = "externalService", fallbackMethod = "fallback")`: The `name` corresponds to the configuration defined in `application.yml`. The `fallbackMethod` is called if the circuit is open or the external service call fails.
- The `fallback` method handles the scenario when the external service is down or unreachable.

### **4. Configure RestTemplate**
Make sure to configure `RestTemplate` in your application if you're using it to make external API calls:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

### **5. Customize Circuit Breaker Config (Optional)**
You can fine-tune the Circuit Breaker behavior programmatically if you need more control over the configuration:

```java
import io.github.resilience4j.circuitbreaker.CircuitBreakerConfig;
import io.github.resilience4j.circuitbreaker.CircuitBreakerRegistry;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.time.Duration;

@Configuration
public class Resilience4jConfig {

    @Bean
    public CircuitBreakerRegistry circuitBreakerRegistry() {
        CircuitBreakerConfig config = CircuitBreakerConfig.custom()
            .failureRateThreshold(50)  // Set the failure rate threshold
            .waitDurationInOpenState(Duration.ofSeconds(10)) // Time to wait before trying the half-open state
            .slidingWindowSize(10) // Number of calls in the sliding window
            .build();

        return CircuitBreakerRegistry.of(config);
    }
}
```

### **6. Monitor Circuit Breaker Status**
Spring Boot Actuator can help monitor the Circuit Breaker. Add the `spring-boot-starter-actuator` dependency to your project:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Enable the Circuit Breaker health indicator in the `application.yml`:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health, circuitbreakers
```

Now, you can monitor the status of the Circuit Breaker by accessing the `/actuator/health` endpoint.

### **7. Run and Test the Circuit Breaker**
When you run the application and make requests to the method annotated with `@CircuitBreaker`, the Circuit Breaker will monitor the success and failure rates.

- If the external service fails repeatedly, the Circuit Breaker will "open," and requests will be short-circuited to the fallback method.
- After the wait duration, it will move to the "half-open" state to test if the external service has recovered.
- If the service recovers, the Circuit Breaker will close, allowing normal traffic.

### **Summary of Key Steps**:
1. Add **Resilience4j** dependencies to your project.
2. Configure the Circuit Breaker settings in `application.yml` or `application.properties`.
3. Annotate service methods with `@CircuitBreaker` and provide a fallback method.
4. Optionally customize Circuit Breaker behavior using programmatic configuration.
5. Monitor Circuit Breaker health with Spring Boot Actuator.

This setup will help protect your application from cascading failures when an external service becomes unresponsive or fails.
