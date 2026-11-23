### 1. What are Microservices?

[Microservices](https://www.geeksforgeeks.org/system-design/microservices/) is an architectural style in which an application is divided into multiple small, independent, and loosely coupled services. Each service performs a specific business function and communicates with other services using lightweight protocols such as HTTP/REST, gRPC, or messaging systems like [Kafka](https://www.geeksforgeeks.org/apache-kafka/apache-kafka/) or [RabbitMQ](https://www.geeksforgeeks.org/blogs/introduction-to-rabbitmq/).

- Services can be developed, deployed, and scaled independently.
- Supports different programming languages and databases.
- Improves maintainability and fault isolation.

### 2. Why are Microservices used?

Microservices are used to simplify the development, deployment, and maintenance of large applications by dividing them into smaller independent services.

![applications_of_microservices](https://media.geeksforgeeks.org/wp-content/uploads/20260716173359788886/applications_of_microservices.webp "Click to enlarge")

****Some Advantages are:****

- Faster development and deployment.
- Independent scaling of services.
- Easier maintenance.
- Supports Continuous Integration and Continuous Deployment (CI/CD).

### 3. What is the difference between Monolithic and Microservices Architecture?

[Monolithic architecture](https://www.geeksforgeeks.org/system-design/monolithic-architecture-system-design/) builds the entire application as a single unit, whereas [Microservices architecture](https://www.geeksforgeeks.org/blogs/microservice-architecture-introduction-challenges-best-practices/) divides the application into multiple independent services.

### Monolithic Architecture

It is a software architecture where the entire application is developed and deployed as a single, unified unit. All components such as the user interface, business logic, and database access are tightly coupled within one codebase.

- All application modules are packaged and deployed together.
- A change in one module usually requires redeploying the entire application.
- Best suited for small and simple applications.

![devops_9](https://media.geeksforgeeks.org/wp-content/uploads/20260120121708468357/devops_9.webp "Click to enlarge")

### Microservices Architecture

It is a software architecture where an application is divided into multiple small, independent services. Each service is responsible for a specific business functionality and communicates with other services through APIs.

- Each service is developed, deployed, and scaled independently.
- Services are loosely coupled and focus on a single business capability.
- Best suited for large, complex, and scalable applications.

![devops_10](https://media.geeksforgeeks.org/wp-content/uploads/20260120121708674412/devops_10.webp "Click to enlarge")

### 4. What is REST API in Microservices?

A [REST API](https://www.geeksforgeeks.org/node-js/rest-api-introduction/) (Representational State Transfer API) is the most common way for microservices to communicate over HTTP. It uses standard HTTP methods to perform operations on resources.

![account_service](https://media.geeksforgeeks.org/wp-content/uploads/20260716173645066324/account_service.webp "Click to enlarge")

****Common HTTP Methods****

- ****GET:**** Retrieve data.
- ****POST:**** Create new data.
- ****PUT:**** Update existing data.
- ****DELETE:**** Remove data.
- ****PATCH:**** Partially update data.

### 5. What are the main components of a Microservices Architecture?

A Microservices architecture consists of several components that work together to build distributed applications.

![7-Microservices-Architecture](https://media.geeksforgeeks.org/wp-content/uploads/20260716173753610138/7-Microservices-Architecture.jpg "Click to enlarge")

- Each module (Employee, Course, Customer) is an independent microservice.
- Git stores the source code for each service.
- Jenkins automatically builds, tests, and deploys the services (CI/CD).
- Each service runs on its own server/container.
- Every microservice has its own database, ensuring loose coupling and independent data management.
- Services can be developed, deployed, updated, and scaled independently.

### 6. Why is Spring Boot widely used for Microservices?

Spring Boot is the most popular Java framework for developing microservices because it minimizes configuration and provides production-ready features.

![Microservices-with-Spring-Boot](https://media.geeksforgeeks.org/wp-content/uploads/20260720152242017366/Microservices-with-Spring-Boot.webp "Click to enlarge")

****Advantages of it****

- Auto Configuration
- Embedded Web Server
- Starter Dependencies
- Easy REST API development
- Spring Cloud integration
- Built-in Actuator support

### 7. What is Eureka Server ?

Eureka Server is a service registry provided by Spring Cloud Netflix that enables service discovery in a microservices architecture. It allows microservices to register themselves and discover other services dynamically without hardcoding their IP addresses or URLs.

- Enables automatic service discovery.
- Eliminates hardcoded service URLs.
- Supports dynamic scaling and load balancing.

### 8. What are the advantages of Microservices over Monolithic Architecture?

Microservices provide greater flexibility, scalability, and maintainability by dividing an application into small independent services.

****Advantages****

- Independent development and deployment.
- Faster release cycles.
- Better scalability.
- Improved fault isolation.
- Easier maintenance and testing.
- Supports different technologies and databases.

### 9. What is Inter-Service Communication?

Inter-Service Communication is the process through which microservices communicate with each other to exchange data and perform business operations.

![rest_template](https://media.geeksforgeeks.org/wp-content/uploads/20260717120346375912/rest_template.webp "Click to enlarge")

****Common Communication Methods****

- REST APIs
- OpenFeign
- gRPC
- Apache Kafka
- RabbitMQ

### 10. What does stateless mean in the context of microservices?

A [stateless microservice](https://www.geeksforgeeks.org/system-design/stateful-vs-stateless-microservices/) does not store client session or request data between requests. Each request is processed independently and contains all the information needed to complete it.

- Makes services scalable and fault-tolerant.
- Easier to deploy and load balance.

### 11. What is an API Gateway?

An [API Gateway](https://www.geeksforgeeks.org/system-design/what-is-api-gateway-system-design/) acts as the single entry point for all client requests in a microservices architecture. It forwards requests to the appropriate microservice and provides common functionalities.

![Microservices-Architecture](https://media.geeksforgeeks.org/wp-content/uploads/20260717121418703431/Microservices-Architecture.webp "Click to enlarge")

****Responsibilities****

- Request routing.
- Authentication and Authorization.
- Load balancing.
- Rate limiting.
- Logging and monitoring.

### 12. What is Synchronous and Asynchronous Communication in Microservices?

Microservices communicate either synchronously or asynchronously depending on business requirements.

|Feature|Synchronous Communication|Asynchronous Communication|
|---|---|---|
|Communication|Immediate request-response|Message-based communication|
|Coupling|Tightly coupled|Loosely coupled|
|Response|Waits for response|Does not wait for response|
|Protocols|REST, gRPC|Kafka, RabbitMQ|
|Performance|Slower under heavy load|Better scalability and performance|

### 13. What is the difference between Stateless and Stateful Microservices?

A [Stateless microservice](https://www.geeksforgeeks.org/system-design/stateful-vs-stateless-microservices/) does not store client session data between requests, whereas a Stateful microservice maintains session or user state across multiple requests.

|****Feature****|****Stateless Microservice****|****Stateful Microservice****|
|---|---|---|
|Session Data|Does not store session data|Stores session data|
|Request Handling|Each request is independent|Requests depend on previous interactions|
|Scalability|Easy to scale|More difficult to scale|
|Fault Tolerance|High|Lower|
|Example|REST APIs|Online shopping cart, gaming session|

### 14. What is the Database-per-Service Pattern?

The [Database-per-Service](https://www.geeksforgeeks.org/system-design/database-per-service-pattern-for-microservices/) Pattern is a microservices design pattern in which each microservice owns and manages its own dedicated database. Other services cannot access this database directly and must communicate through APIs or events.

- Prevents direct database sharing between services.
- Enables independent scaling and technology choices.
- Communication occurs through REST APIs, gRPC, or messaging.

### 15. Why should each Microservice have its own Database?

Each microservice should have its own database to maintain data ownership, loose coupling, and independent scalability. Sharing a database creates tight coupling and reduces the independence of services.

- Improves fault isolation.
- Enables independent deployment and scaling
- Prevents unintended data modifications by other services.

### 16. What are the common communication protocols used in Microservices?

Microservices communicate using synchronous or asynchronous protocols depending on the application's requirements.

|****Protocol****|****Purpose****|
|---|---|
|****REST (HTTP/HTTPS)****|Simple request-response communication between services.|
|****gRPC****|High-performance communication using Protocol Buffers.|
|****Apache Kafka****|Event streaming and asynchronous messaging.|
|****RabbitMQ****|Reliable message queuing between services.|
|****WebSockets****|Real-time, bidirectional communication.|

> ****Note:**** REST and gRPC are used for synchronous communication, while Kafka and RabbitMQ are preferred for asynchronous, event-driven communication.

### 17. What are the Challenges of Microservices?

Although microservices improve scalability and flexibility, they introduce additional complexity in managing distributed systems.

****Common Challenges****

- Managing communication between multiple services.
- Ensuring data consistency across services.
- Increased deployment and operational complexity.
- Monitoring, logging, and distributed tracing.
- Handling network latency and failures.
- Managing security and authentication across services.

### 18. What is Load Balancing in Microservices?

[Load Balancing](https://www.geeksforgeeks.org/system-design/what-is-load-balancer-system-design/) is the process of distributing incoming requests across multiple instances of a microservice to improve performance, availability, and reliability.

- Prevents server overload.
- Improves scalability and fault tolerance.
- Ensures high availability by routing requests to healthy instances.
- Can be Client-Side (Spring Cloud LoadBalancer) or Server-Side (Nginx, HAProxy).

## Java Intermediate Microservices Interview Questions

### 19. What is Centralized Configuration?

Centralized Configuration is a mechanism where the configuration of all microservices is stored and managed from a single centralized location instead of maintaining separate configuration files for each service.

****Advantages****

- Supports multiple environments (Dev, Test, Production).
- Ensures configuration consistency across services.
- Allows configuration updates without rebuilding applications.

### 20. What is Client-Side Load Balancing?

[Client-side load balancing](https://www.geeksforgeeks.org/advance-java/java-spring-boot-microservices-client-side-load-balancing-with-spring-cloud-loadbalancer/) distributes requests among multiple instances of a microservice. The client selects an available service instance using a load-balancing algorithm before sending the request.

- Improves application availability.
- Distributes traffic evenly.
- Prevents server overload.
- Commonly implemented using Spring Cloud LoadBalancer.

![Client-Side-Load-Balacer](https://media.geeksforgeeks.org/wp-content/uploads/20260717120514679664/Client-Side-Load-Balacer.png "Click to enlarge")

### 21. How does Spring Cloud Config work?

Spring Cloud Config provides a centralized configuration server that stores application properties in a Git repository and supplies them to microservices at startup or runtime.

****Working****

- Configuration files are stored in a Git repository.
- Config Server reads the configuration from Git.
- Microservices request their configuration from the Config Server.
- Configurations can be refreshed without restarting the application.

****Advantages****

- Centralized configuration management.
- Supports version control through Git.
- Simplifies environment-specific configuration.

### 22. What is Fault Tolerance in Microservices?

[Fault Tolerance](https://www.geeksforgeeks.org/system-design/fault-tolerance-in-system-design/) is the ability of a microservices system to continue functioning even when one or more services fail. It prevents cascading failures.

- Improves application availability.
- Enables graceful degradation.
- Achieved using retries, circuit breakers, fallback methods, and load balancing

### 23. What is the Circuit Breaker Pattern?

The Circuit Breaker Pattern prevents repeated calls to a failing service by temporarily stopping requests until the service becomes healthy again.

****Working States****

- ****Closed:**** Requests are allowed.
- ****Open****: Requests are blocked because failures exceeded the threshold.
- ****Half-Open:**** A few requests are allowed to check if the service has recovered.

****Benefits of Circuit Breaker Pattern****

- Prevents cascading failures.
- Improves fault tolerance.
- Enables faster system recovery

### 24. What is Resilience4j?

Resilience4j is a lightweight Java library used to implement fault tolerance in microservices.

- ****Circuit Breaker->**** Prevents repeated calls to a failing service.
- ****Retry**** –> Automatically retries failed requests before reporting an error.
- ****Rate Limiter**** –> Limits the number of requests allowed in a given time period.
- ****Bulkhead**** –> Isolates resources to prevent one service failure from affecting others.
- ****Time Limiter**** –> Sets a maximum time limit for service execution.
- ****Fallback Support**** –> Provides an alternative response when a service fails.

### 25. What is Distributed Tracing?

Distributed Tracing tracks a request as it travels across multiple microservices, helping identify delays and failures.

****Benefits****

- Tracks end-to-end request flow.
- Simplifies debugging.
- Measures service latency.
- Identifies performance bottlenecks.

****Common Tools****

- Zipkin
- Jaeger
- OpenTelemetry

### 26. Why is Docker used in Microservices?

Docker packages a microservice along with its dependencies into a lightweight container, ensuring consistent execution across different environments.

****Advantages****

- Consistent runtime environment.
- Simplifies deployment.
- Supports portability.
- Enables rapid scaling.
- Works seamlessly with Kubernetes.

### 27. Why is Kubernetes used in Microservices?

Kubernetes is a container orchestration platform used to deploy, manage, and scale containerized microservices automatically.

****Advantages of Kubernetes****

- Automatic scaling.
- Self-healing containers.
- Load balancing.
- Rolling updates and rollbacks.
- High availability.

### 28. How do Microservices communicate with each other?

Microservices communicate with each other using synchronous or asynchronous communication mechanisms over a network. The choice depends on the application's performance, scalability, and reliability requirements.

****Types of communication****

- ****Synchronous:**** Uses REST APIs (HTTP/HTTPS) or gRPC, where one service waits for a response.
- ****Asynchronous:**** Uses message brokers like Apache Kafka or RabbitMQ, where services exchange messages without waiting.

### 29. How do you monitor microservices effectively?

Microservices are monitored using tools that collect health information, metrics, logs, and distributed traces to ensure high availability and quickly identify issues.

- Use Spring Boot Actuator to expose health and monitoring endpoints.
- Collect metrics using Prometheus.
- Visualize metrics and dashboards using Grafana.

****Common Monitoring Tools****

- ****Spring Boot Actuator:**** Exposes health, metrics, and application information.
- ****Prometheus:**** Collects application metrics.
- ****Grafana:**** Visualizes metrics through dashboards.
- ****ELK Stack (Elasticsearch, Logstash, Kibana):**** Centralizes and analyzes logs.
- ****Zipkin / Jaeger:**** Provides distributed tracing across multiple microservices.

### 30. What are some common deployment strategies for microservices?

Deployment strategies define how new versions of microservices are released with minimal downtime and risk.

****Common Deployment Strategies****

- ****Blue-Green Deployment:**** Maintains two identical environments; traffic switches to the new version after successful testing.
- ****Canary Deployment:**** Releases the new version to a small percentage of users before a full rollout.
- ****Rolling Deployment:**** Gradually replaces old service instances with new ones.
- ****Recreate Deployment:**** Stops the old version completely before deploying the new version.
- ****A/B Testing:**** Routes different users to different versions to compare performance or features.

****Benefits****

- Minimizes downtime.
- Reduces deployment risk.
- Enables quick rollback if issues occur.

### 31. How do you manage configuration across microservices?

Configuration across microservices is managed using a centralized configuration server, allowing all services to access and share configuration from a single source.

- Supports multiple environments (Dev, Test, Production).
- Configuration can be updated without rebuilding applications (with refresh support).

****Common Approaches****

- ****Spring Cloud Config Server:**** Centralizes configuration for all microservices.
- ****Git Repository:**** Stores configuration files with version control.
- ****Environment Variables:**** Provides environment-specific settings (Dev, Test, Production).
- ****application.properties / application.yml:**** Defines service-specific configuration.
- ****Kubernetes ConfigMaps and Secrets:**** Manages configuration and sensitive data in Kubernetes deployments.

### 32. How do you test microservices?

Microservices are tested at different levels to ensure that each service works correctly both independently and when communicating with other services.

- Ensures reliable service communication.
- Improves application quality.

****Types of Testing****

- ****Unit Testing:**** Tests individual classes or methods (JUnit, Mockito).
- ****Integration Testing:**** Verifies interaction with databases, APIs, or other services.
- ****Contract Testing:**** Ensures communication between service providers and consumers (Spring Cloud Contract, Pact).
- ****End-to-End (E2E) Testing:**** Tests the complete business workflow across multiple microservices.
- ****Performance Testing:**** Evaluates scalability and response time under load (JMeter, Gatling).

### 33. What are the differences between REST, gRPC, and Message Queues?

|****Feature****|****REST****|****gRPC****|****Message Queues****|
|---|---|---|---|
|Communication|Synchronous|Synchronous|Asynchronous|
|Protocol|HTTP/HTTPS|HTTP/2|Messaging Broker|
|Data Format|JSON/XML|Protocol Buffers|Messages/Events|
|Performance|Moderate|Very High|High|
|Best Use|Public APIs|Internal service communication|Event-driven systems|
|Examples|REST APIs|gRPC|Kafka, RabbitMQ|

## Advanced Microservices Interview Questions

### ****34.**** How do you decompose a monolithic application into microservices using DDD and Bounded Context?

A monolithic e-commerce application can be broken into independent microservices by dividing it based on business capabilities. Each microservice is responsible for a single business function, has its own database, and communicates with other services using REST APIs, gRPC, or message brokers (Kafka/RabbitMQ).

****Role of Domain-Driven Design (DDD):**** Domain-Driven Design is a design approach that models software around business domains rather than technical layers.

It helps to:

- Define service boundaries.
- Reduce coupling between services.
- Improve maintainability and scalability.

****Role of Bounded Context****: A Bounded Context is a clearly defined boundary where a specific business model and terminology apply.

In microservices:

- Each Bounded Context usually becomes one microservice.
- Each service has its own business logic, database, and API.
- Services communicate only through APIs or events.

****Example:****

- Product Catalog-> Product Service
- Order Management-> Order Service
- Payment Processing-> Payment Service

### 35. What is the Saga Pattern?

The Saga Pattern is a design pattern used to manage distributed transactions across multiple microservices without using a global transaction. It breaks a transaction into a series of local transactions, where each service performs its own operation. If any step fails, compensating transactions undo the previous successful operations.

****Types of Saga****

- ****Choreography:**** Services communicate through events without a central coordinator.
- ****Orchestration:**** A central orchestrator coordinates all transaction steps.

****Benefits****

- Eliminates distributed locking.
- Improves scalability.
- Supports eventual consistency.

### 36. What is Eventual Consistency?

Eventual Consistency is a consistency model where all microservices become consistent over time after data changes are propagated asynchronously.

- Data synchronization happens through events.
- Improves scalability and availability.
- Commonly used with Kafka and RabbitMQ.

### 37. What are the Best Practices for designing Microservices?

Designing microservices correctly improves scalability, maintainability, and reliability.

****Best Practices****

- Follow the Single Responsibility Principle.
- Design services around business capabilities (DDD).
- Keep services loosely coupled and highly cohesive.
- Make services stateless whenever possible.
- Use Database-per-Service.
- Communicate through well-defined APIs or events.
- Implement centralized logging and monitoring

### 38. How do you secure Microservices?

Microservices are secured by protecting communication, authentication, authorization, and sensitive data.

****Security Measures****

- Authenticate users using OAuth2 or JWT.
- Authorize requests using Role-Based Access Control (RBAC).
- Secure communication with HTTPS/TLS.
- Protect APIs using an API Gateway.
- Store secrets using Kubernetes Secrets or Vault.
- Encrypt sensitive data.
- Enable logging and monitoring for security events.

### 39. What’s the difference between Event Sourcing and Traditional CRUD?

Event Sourcing stores every change as an immutable event, while Traditional CRUD stores only the latest state of the data.

|****Feature****|****Event Sourcing****|****Traditional CRUD****|
|---|---|---|
|****Data Storage****|Stores every state change as events|Stores only the current state|
|****History****|Complete audit trail available|Previous changes are overwritten unless audited separately|
|****Data Recovery****|Current state can be rebuilt by replaying events|Only current data is available|
|****Performance****|Better for audit/event-driven systems|Simpler and faster for basic CRUD applications|
|****Complexity****|More complex to implement|Easier to design and maintain|
|****Best Use Cases****|Banking, e-commerce orders, finance, event-driven systems|CRUD-based business applications like inventory, HR, CMS|

### 40. How do you manage schema evolution in event-driven systems?

Schema evolution is the process of modifying the structure of events over time without breaking existing producers or consumers.

****How to Manage Schema Evolution****

- ****Use Schema Registry:**** Store and manage event schemas centrally (e.g., Apache Avro Schema Registry).
- ****Maintain Backward Compatibility:**** New consumers should be able to read older events.
- ****Maintain Forward Compatibility:**** Older consumers should ignore newly added fields.
- ****Use Versioning:**** Assign schema versions to track changes and support multiple versions.
- ****Avoid Breaking Changes:**** Do not remove or rename existing fields; prefer adding optional fields.
- ****Use Serialization Formats:**** Use Avro, Protobuf, or JSON Schema for structured and versioned events.
- ****Test Compatibility:**** Validate schema changes before deployment to prevent consumer failures.

### 41. When should you use WebSockets instead of REST APIs in microservices? Explain the advantages and limitations.

WebSockets provide a persistent, two-way communication channel between the client and server. They are preferred over REST APIs when applications require real-time, low-latency communication.

****Use WebSockets When****

- Real-time chat and messaging applications.
- Live notifications and alerts.
- Online gaming.
- Stock market or cryptocurrency price updates.
- Live dashboards and collaborative applications.

****Advantages****

- Full-duplex (two-way) communication.
- Reduces repeated HTTP requests.
- Efficient for continuous real-time updates.

****Limitations****

- More complex to implement and scale.
- Persistent connections consume server resources.
- Not suitable for simple request-response operations.