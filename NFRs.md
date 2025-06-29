Improving Non-Functional Requirements (NFRs) in a distributed system requires a mix of architectural, technological, and operational strategies. Below are key techniques for enhancing each aspect:

### Creating a multi-tenant SaaS-based architecture
In the previous section, you learned about multitier architecture, also called a single tenancy, when built for a single organization. Multi-tenant architecture is becoming more popular as organizations welcome the digital revolution while keeping the overall application and operational cost low. The Software-as-a-Service (SaaS) model is constructed upon a multi-tenant architecture, where an instance of the software and the accompanying infrastructure caters to numerous customers. Within this framework, each customer utilizes the application and database in a shared manner. With each tenant being isolated by their unique configuration, identity, and data, they remain invisible to each other while sharing the same product.

As multi-tenant SaaS providers are responsible for everything from the hardware to the software, SaaS-based products offload an organization’s responsibilities to the application’s maintenance and updates, as the SaaS provider takes care of this.

Each organization that is a buyer of the SaaS product is considered a tenant. These tenants can customize their user interface using a configuration without code changes. As multiple customers share a common infrastructure, they benefit from scale, which further lowers the cost. Some of the most popular SaaS providers are Salesforce CRM, Jira Software, Slack, Google Workspace, and Amazon Connect.

As shown in the following architecture diagram, two organizations (tenants) use the same software and infrastructure. The SaaS vendor provides access to the application layer by allocating a unique tenant ID to each organization:


Figure 4.2: Multi-tenant SaaS architecture

The preceding architecture design shows that the presentation layer provides a user interface, and the application layer holds the business logic. At the data access layer, each tenant will have data-level isolation with one of the following methods:

#### Database-Level Isolation: 
In this model, each tenant has its database associated with its tenant ID. When each tenant queries data from the user interface, they are redirected to their database. This model is required if the customer doesn’t want a single shared database for compliance and security reasons.
#### Table-Level Isolation: 
This isolation level can be achieved by providing a separate table for each tenant. In this model, tables need to be uniquely assigned to each tenant, for example, with the tenant ID prefix. When each tenant queries data from the user interface, they are redirected to their tables as per their unique identifier.
#### Row-Level Isolation: 
All tenants share the same table in a database in this isolation level. There is an additional column in a table where a unique tenant ID is stored against each row. When an individual tenant wants to access their data from the user interface, the application’s data access layer formulates a query based on the tenant ID to the shared table. Each tenant gets a row that belongs to their users only.
For enterprise customers, a careful assessment should be carried out to understand whether a SaaS solution fits them based on their unique features’ requirements. This is because, often, a SaaS model has limited customization capabilities.

The selection of isolation approaches is based on considerations around the organization’s compliance, security, cost, and tenant contractual requirements.

It’s important to find the cost value proposition if many users need to subscribe. The cost comparison should be calculated based on the total cost of ownership when making a build versus buy decision. This is because building software is not the primary business of most organizations, so the SaaS model is becoming highly popular as organizations can focus on their business and let the experts handle the IT side of it.

Service-oriented architecture (SOA) is a popular approach for designing and building applications, especially when organizations have unique, customized requirements. Let’s learn more about it.


### 1. **Performance**
   Techniques to improve the system’s speed and response time:
   - **Caching**: Use distributed caches (e.g., Redis, Memcached) to store frequently accessed data.
   - **Load Balancing**: Distribute traffic across multiple instances or servers to reduce individual load.
   - **CDN (Content Delivery Network)**: Cache static content closer to users to reduce latency and improve loading times.
   - **Asynchronous Processing**: Use message queues (e.g., RabbitMQ, Kafka) or background jobs for long-running tasks.
   - **Database Optimization**: Use indexing, partitioning, sharding, and query optimization to improve database performance.
   - **Compression**: Compress data to reduce transmission time, especially over slow networks.
   - **Threading and Concurrency**: Leverage multi-threading and concurrency to handle parallel workloads.

   -  users expect consistency in performance, regardless of their geographical location.
   -  Organizations can also utilize a content distribution network (CDN) to store heavy image and video data near user locations to reduce network latency and improve performence.
   -  throughput
      -  Network throughput refers to the amount of data that is successfully transferred over a network in a given period of time.
      -   Throughput can be influenced by various factors, including the network’s capacity (bandwidth), the quality of the connection, and the protocols used for data transfer. Bandwidth determines the maximum amount of data that can be transferred over the network.
      -   Throughput and latency have a direct relationship. Low latency means high throughput because more data can be transferred in less time.
      -   When you look at latency, throughput, and bandwidth, there is another factor called concurrency, which applies to the various architecture components and helps improve application performance.
   -   Handling concurrency
       -   It allows an application to perform multiple tasks simultaneously, making better use of system resources and improving overall application performance. By implementing concurrency, developers can ensure that their applications can handle multiple operations at the same time without waiting for one task to complete before starting another.
       -   Implementing concurrency can lead to significant improvements in response times and throughput, enhancing the user experience and the application’s ability to scale.
       -   Parallelism is another crucial concept in software design, complementing concurrency by executing multiple operations simultaneously across different processors or cores. While concurrency involves dealing with many tasks at once (for example, multitasking on a single-core CPU, where tasks are switched rapidly to give the appearance of simultaneous execution), parallelism takes this a step further by truly performing multiple operations at the same time via multi-core processors or distributed systems. This approach significantly speeds up processing times for compute-intensive tasks by dividing the work into small chunks that can be processed in parallel.
    -   Applying caching
       -        

### 2. **Scalability**
   Techniques to allow the system to grow and handle increasing loads:
   - **Microservices Architecture**: Break the system into smaller, independently scalable services.
   - **Horizontal Scaling**: Add more servers (nodes) to the system instead of scaling vertically (increasing server capacity).
   - **Vertical Scaling**: Upgrade existing servers with more powerful hardware.
   - **Database Sharding**: Split data across multiple database servers (shards) to balance the load.
   - **Stateless Services**: Design services that don't maintain state, making them easier to scale and replicate.
   - **Auto-scaling**: Automatically add or remove resources (e.g., virtual machines, containers) based on demand using tools like AWS Auto Scaling or Kubernetes.
   - **Distributed Databases**: Use distributed databases (e.g., Cassandra, CockroachDB) to scale storage and query capabilities.

### 3. **Availability**
   Techniques to ensure the system is operational and accessible:
   - **Failover Systems**: Set up hot or cold standby systems that can take over in case of failure.
   - **Redundancy**: Implement redundant components (servers, networks, databases) to avoid single points of failure.
   - **Replication**: Use database replication (e.g., master-slave, master-master) for data redundancy and fast failover.
   - **Geo-distributed Deployment**: Distribute the system across multiple geographical regions to avoid localized outages.
   - **Self-healing Systems**: Use automation tools that can detect failures and automatically restart, replace, or fix failed components.
   - **Disaster Recovery Planning**: Develop a plan to restore the system in case of a major disaster.

### 4. **Latency**
   Techniques to reduce communication delays and improve real-time performance:
   - **Edge Computing**: Process data closer to the user or data source to reduce round-trip time.
   - **Local Caching**: Use client-side or edge-side caching to reduce server load and improve access speed.
   - **Data Compression**: Compress payloads (e.g., JSON, HTTP) to reduce transmission time.
   - **Network Optimization**: Use faster network protocols (e.g., HTTP/2, gRPC) and optimized routing to reduce latency.
   - **Content Delivery Network (CDN)**: Cache content at the edge to deliver resources quickly to users in different regions.
   - **Partitioning and Load Balancing**: Distribute workload efficiently across multiple servers to reduce processing time.
   - **Asynchronous Communication**: Minimize synchronous blocking calls by implementing non-blocking I/O, and use event-driven communication.

### 5. **Security**
   Techniques to protect the system from attacks and unauthorized access:
   - **Encryption**: Use strong encryption (e.g., AES-256) for data at rest and in transit (e.g., TLS/SSL).
   - **Authentication and Authorization**: Implement OAuth, JWT, or multi-factor authentication (MFA) for robust access control.
   - **Zero Trust Architecture**: Ensure that no component is trusted by default, enforcing strict identity verification and access control.
   - **API Security**: Secure APIs using authentication, rate limiting, IP whitelisting, and input validation.
   - **Firewalls and VPN**: Secure communication with firewalls, network segmentation, and Virtual Private Networks (VPNs).
   - **Intrusion Detection Systems (IDS)**: Monitor for malicious activity and anomalies in real-time.
   - **Regular Security Audits**: Conduct vulnerability assessments and penetration testing to identify and fix security flaws.

### 6. **Maintainability**
   Techniques to ensure the system can be easily maintained, updated, and extended:
   - **Modular Architecture**: Follow SOLID principles and use microservices to ensure different parts of the system can be updated independently.
   - **Documentation**: Provide clear and comprehensive documentation.
   - **CI/CD Pipelines**: Automate testing and deployment using Continuous Integration/Continuous Deployment (CI/CD) tools like Jenkins or GitLab CI.
   - **Version Control**: Use tools like Git for source code management and maintain backward compatibility in APIs.
   - **Logging and Monitoring**: Implement structured logging and monitoring (e.g., ELK stack, Prometheus) to easily track issues and performance.
   - **Automated Testing**: Use unit tests, integration tests, and end-to-end tests to detect issues early and ensure the system works as expected.
   - **Configuration Management**: Use tools like Ansible, Puppet, or Terraform for managing configuration and ensuring consistency across environments.

### 7. **Reliability**
   Techniques to ensure the system functions correctly under expected conditions:
   - **Redundancy and Replication**: Ensure redundancy at every level (e.g., application, database, network) to handle failures.
   - **Graceful Degradation**: Ensure that non-critical components can fail without affecting the core system functionality.
   - **Circuit Breakers**: Implement circuit breaker patterns to isolate failing services and prevent cascading failures.
   - **Health Checks and Monitoring**: Regularly check the health of system components and trigger alerts if anomalies are detected.
   - **Chaos Engineering**: Intentionally introduce failures (e.g., using Chaos Monkey) to test the system’s resilience and recovery strategies.
   - **Data Backup and Recovery**: Regularly back up critical data and test the disaster recovery process to ensure data reliability.



### **Robustness**:
`Robustness` refers to the ability of a system to handle unexpected conditions, errors, or failures without crashing or producing incorrect results. To improve robustness in a distributed system, the following techniques can be applied:
1. **Graceful Degradation**:  
   - Allow non-critical components or features to fail without affecting the core functionality of the system. If certain parts of the system encounter an issue, they should degrade gracefully instead of causing the entire system to crash.
   - Example: If a third-party service is down, fallback to a default behavior instead of failing the entire operation.

2. **Exception Handling and Fault Tolerance**:  
   - Implement proper exception handling across the system to catch and manage errors at all layers, from application to infrastructure.
   - Use fault-tolerant mechanisms that allow the system to operate correctly even in the presence of faults.
   - Example: Use retry mechanisms or alternative data sources when a service call fails.

3. **Circuit Breaker Pattern**:  
   - Use circuit breakers to prevent cascading failures. When one component in a system starts to fail, the circuit breaker "opens" and stops further calls to that component, preventing other parts of the system from becoming overwhelmed.
   - Example: If a downstream service is unavailable, the circuit breaker opens, and further requests return immediately with an error or a fallback response.

4. **Idempotency**:  
   - Ensure that operations can be safely retried without unintended side effects. This is particularly important for network calls, where retries may occur due to timeouts or failures.
   - Example: Make HTTP requests or API calls idempotent so that multiple retries don’t affect the system state.

5. **Retry Mechanisms with Backoff**:  
   - Implement retry logic with exponential backoff when dealing with temporary failures. This allows the system to recover from transient issues without overwhelming resources or external services.
   - Example: Use libraries like Resilience4j for retrying failed operations with increasing intervals.

6. **Redundancy and Replication**:  
   - Use redundancy in critical components (e.g., servers, data storage) and replicate data across multiple regions to avoid single points of failure.
   - Example: Replicate databases in multiple regions to ensure availability in case one region goes down.

7. **Self-Healing Mechanisms**:  
   - Use automation tools to detect failures and trigger actions to self-repair the system, such as restarting services or reallocating resources.
   - Example: Use Kubernetes or AWS Auto Recovery to restart failed containers or virtual machines automatically.

8. **Graceful Shutdown**:  
   - Ensure that the system can shut down cleanly, allowing ongoing requests to complete and cleaning up resources before termination.
   - Example: When stopping services, implement logic to handle active requests or connections before shutting down the instance.

9. **Monitoring, Alerts, and Proactive Maintenance**:  
   - Set up comprehensive monitoring with real-time alerting on critical metrics such as error rates, resource utilization, and system performance. Proactively address issues before they lead to failures.
   - Example: Use tools like Prometheus, Grafana, or Datadog to monitor system health and trigger alerts when thresholds are exceeded.

10. **Graceful Handling of Network Partitions**:  
    - In distributed systems, network partitions (partial failures) are inevitable. Design the system to handle them gracefully by falling back on cached or replicated data, or allowing eventual consistency.
    - Example: During a partition, allow read operations from a cache and queue write operations for later processing once the partition is resolved.

11. **Input Validation**:  
    - Ensure that all inputs (both external and internal) are validated to prevent unexpected behavior or security vulnerabilities. This helps the system remain stable when receiving malformed or malicious input.
    - Example: Validate all incoming API requests and user inputs to ensure they meet expected formats and types.

12. **Chaos Engineering**:  
    - Intentionally introduce failures into the system (e.g., network outages, service downtime) to test its robustness and see how it behaves under stress. This allows teams to uncover weaknesses and improve the system's resilience.
    - Example: Use Chaos Monkey (by Netflix) to randomly terminate instances in production to ensure that the system can handle these failures gracefully.

13. **Quotas and Throttling**:  
    - Set limits on resource usage (e.g., CPU, memory, disk I/O) and throttle requests to ensure that no component gets overwhelmed by an unexpected spike in traffic or demand.
    - Example: Implement API rate limiting to prevent a service from being overloaded by too many requests.

14. **Data Integrity Checks**:  
    - Implement checks to ensure the consistency and correctness of data even after errors or failures occur. This includes using checksums, validations, and periodic data consistency audits.
    - Example: Use checksums to verify the integrity of data during transmission or storage to prevent corrupted data from affecting the system.

15. **Rollback and Version Control**:  
    - Maintain the ability to roll back to previous stable versions of software or configurations in case a deployment or update introduces instability.
    - Example: Use blue-green deployments or canary releases for software updates, allowing quick rollback in case of failures.

By applying these techniques, a distributed system can become more robust, ensuring it handles failures and unexpected conditions without significantly impacting the system’s stability or user experience.

Each of these techniques can be used alone or in combination to strengthen specific NFRs. They should be selected based on the system’s architecture, use cases, and performance requirements.


Achieving **high availability (HA)** in a microservice-based application is critical for ensuring that the application remains operational, even in the face of failures or high traffic. In a microservice architecture, individual components (or services) are deployed independently and communicate over a network, which introduces both flexibility and complexity. To ensure high availability, several strategies can be applied at various layers of the application architecture:

### 1. **Service Redundancy and Replication**
   - **Horizontal scaling**: Run multiple instances of each microservice across different servers or nodes. If one instance fails, others can continue to serve requests. This approach distributes the load and improves fault tolerance.
   - **Active-Active replication**: Multiple instances of a service are active and serve traffic simultaneously. Traffic is load-balanced across these instances.
   - **Active-Passive replication**: One instance serves traffic (active), while a standby instance (passive) is ready to take over if the active instance fails.

### 2. **Load Balancing**
   - Use load balancers to distribute incoming traffic across multiple instances of a microservice. This ensures no single instance is overwhelmed and allows for automatic rerouting of traffic if an instance goes down.
   - Implement **health checks** within load balancers to monitor the availability of services and remove unhealthy instances from the pool.
   - Cloud platforms often provide managed load balancers, such as AWS Elastic Load Balancer or Azure Load Balancer, which simplify HA implementation.

### 3. **Service Discovery**
   - In a dynamic microservice environment, services are frequently created, destroyed, or moved. Service discovery mechanisms ensure that services can find and communicate with each other without requiring hardcoded addresses.
   - Tools like **Consul**, **Eureka**, or **Kubernetes’ DNS-based service discovery** can be used to dynamically register and discover available service instances.

### 4. **Circuit Breakers and Retry Logic**
   - Implement **circuit breakers** to prevent cascading failures across microservices. If a service is experiencing issues, the circuit breaker temporarily stops sending requests to that service until it recovers, preventing a complete application failure.
   - **Retry logic** can be used to automatically reattempt failed operations after a brief wait period, with exponential backoff to avoid overwhelming the service.
   - Libraries like **Hystrix** or **Resilience4j** can implement these patterns in a microservices architecture.

### 5. **Auto-scaling**
   - Use auto-scaling mechanisms to automatically adjust the number of service instances based on traffic or resource usage. This ensures that services can handle sudden spikes in demand and maintain availability under heavy load.
   - Cloud providers like AWS (with EC2 Auto Scaling or Kubernetes Horizontal Pod Autoscaler) allow for dynamic scaling based on metrics such as CPU, memory usage, or request rate.

### 6. **Database Replication and Sharding**
   - Use **replication** (master-slave or leader-follower) to ensure that database failures don't result in application downtime. If the primary database fails, a replica can take over to handle traffic.
   - **Sharding**: Distribute data across multiple database nodes (horizontal scaling) to improve performance and availability, especially for large datasets.
   - Ensure that the application is designed to handle read and write operations on different nodes, minimizing the risk of downtime.

### 7. **Stateless Services**
   - Design microservices to be **stateless**, meaning that no session or important data is stored locally in a service instance. Instead, session data is stored in external data stores (e.g., Redis, databases) or passed between services.
   - Stateless services are easier to scale and replace since any instance can handle any request without relying on previous session data.

### 8. **Distributed Data Storage and Caching**
   - Use distributed caching solutions like **Redis**, **Memcached**, or **Hazelcast** to offload frequently accessed data and reduce the load on databases.
   - Ensure caches are replicated or partitioned to avoid a single point of failure.
   - For distributed databases, consider using cloud-native databases that offer built-in high availability (e.g., **AWS DynamoDB**, **Google Cloud Spanner**, or **CockroachDB**).

### 9. **Failover Mechanisms**
   - Implement **failover strategies** to automatically route traffic to backup instances or regions when the primary instance or region experiences a failure.
   - Use tools like **AWS Route 53**, **Azure Traffic Manager**, or **GCP Global Load Balancer** to enable geographic failover and region-based redundancy.

### 10. **Monitoring, Alerts, and Self-Healing**
   - Use monitoring tools like **Prometheus**, **Grafana**, **Datadog**, or **CloudWatch** to monitor the health and performance of microservices and infrastructure.
   - Set up alerting mechanisms (e.g., **PagerDuty** or **Slack** notifications) to notify teams when issues arise, enabling faster resolution.
   - Implement **self-healing** mechanisms such as automated restarts of unhealthy services or automatic scaling to recover from failures.

### 11. **Decentralized Data and Event-Driven Architecture**
   - Design systems using **event-driven architectures** (e.g., **Kafka**, **RabbitMQ**) where services communicate asynchronously. This can decouple services and make the system more resilient to individual service failures.
   - Use **CQRS (Command Query Responsibility Segregation)** and **event sourcing** to manage distributed data states, allowing services to operate independently and recover from failures with minimal impact.

### 12. **Disaster Recovery (DR) and Backups**
   - Implement robust disaster recovery plans that include regular **backups** of databases and application state, and **geo-redundancy** to ensure data is replicated across multiple regions.
   - Test your DR strategy regularly to ensure that systems can recover within an acceptable time frame (e.g., RTO/RPO goals).

### 13. **Graceful Degradation**
   - Implement **graceful degradation** techniques so that when certain parts of the system fail, the overall application can still provide limited functionality (e.g., showing cached content when real-time data is unavailable).
   - This helps to maintain a good user experience even when certain services are down.

### 14. **Chaos Engineering**
   - Use **Chaos Engineering** practices (e.g., **Chaos Monkey** by Netflix) to test the resilience of microservices under failure scenarios. These practices involve deliberately inducing failures (e.g., shutting down servers or simulating network latency) to test how well the system can recover.
   - This helps to identify potential weaknesses and improve the reliability and availability of the system.

### Summary of Strategies:
1. **Redundancy and Replication**: Deploy multiple instances of microservices and ensure replication of data.
2. **Load Balancing**: Distribute traffic effectively and route requests to healthy instances.
3. **Service Discovery**: Automatically manage dynamic microservice instances.
4. **Circuit Breakers and Retry Logic**: Prevent cascading failures and implement resilient communication.
5. **Auto-scaling**: Scale microservices dynamically to handle changing traffic.
6. **Stateless Services**: Design services to be stateless for easy scaling and failover.
7. **Distributed Storage and Caching**: Use replicated, distributed databases and caching systems.
8. **Failover Mechanisms**: Enable automatic failover between instances or regions.
9. **Monitoring and Self-Healing**: Use monitoring and alerts to detect failures and automatically recover.
10. **Event-Driven Architecture**: Decouple services using asynchronous messaging systems.
11. **Disaster Recovery and Backups**: Ensure geo-redundancy and robust disaster recovery.
12. **Graceful Degradation**: Maintain limited functionality during failures.
13. **Chaos Engineering**: Proactively test the system’s resilience with simulated failures.

By combining these strategies, you can build a microservice-based application with strong high availability, ensuring minimal downtime and better fault tolerance.
