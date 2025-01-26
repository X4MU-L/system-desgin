# Architecture Design Overview

Designing a high-level system architecture for an on-premises core banking application involves creating a robust, secure, and scalable system that handles a variety of banking operations such as account management, transactions, and user authentication. Here's an overview of the system design components:

## 1. Client Layer (User Interaction)

This layer consists of the interfaces through which users interact with the banking system. There are typically two types of clients:

Web Clients: Users access the bank's services via a web application.
Mobile Clients: Users access services through mobile apps (iOS/Android).

Both types of clients should communicate securely with the backend via HTTPS.

## 2. Load Balancer

The Load Balancer sits in front of the API Gateway, this allows the Load balancer handles raw network distribution first, the also enables scaling of API Gateway instances themselves and multiple instances of API Gateway can be deployed. Load balancer performs health checks on gateways
and Automatic failover between gateway instances.

## 3. API Gateway

The API Gateway sits behind serves as a unified entry point for client requests. It handles:

Routing: Directing requests to the appropriate microservices.
Rate Limiting: Protecting against denial of service (DoS) attacks.
Authentication and Authorization: Validating user token using the authentication service
Request Aggregation: Combining data from multiple services into one response for complex requests.
Monitoring and Logging: Monitoring an logging of requests

## 4. Microservices Layer (Business Logic)

Microservices are designed to handle specific business functions, and each service is independent. They communicate via RESTful APIs or asynchronous messaging (e.g., using Kafka).

Some core microservices might include:

Authentication & Authorisation Service
Handles authentication and authosisation of users, staff, admin, agents..etc
Handles Validation of signed tokens

Account Service
Manages user accounts (savings, checking, etc.).
Handles account creation, updates, and balance queries.
Each account has its own unique identifier (AccountID).

Transaction Service
Handles all financial transactions such as transfers, deposits, withdrawals, and payments.
Supports real-time processing and ensures idempotency to avoid duplicate transactions.

User Service
Manages customer profiles and handles user authentication (should decide if there will be an authentication service, being that each service will be decoupled and independent)

Loan Service
Manages loan applications, interest calculations, and repayment plans.

Payment Service
Integrates with third-party payment gateways for online payments, bill payments, and card processing.

Notification Service
Handles sending notifications via SMS, email, or push notifications for events like successful transactions, low balances, and account activity.

## 5. Database Layer (Data Storage)

Each microservice typically has its own database to ensure loose coupling. Here are examples of the databases used:

Relational Databases (SQL): Services like Account, Transaction, and User Service would use relational databases like PostgreSQL or MySQL for ACID-compliant transactions and complex queries.
NoSQL Databases: For services handling unstructured data like notifications or logs, NoSQL databases like MongoDB or Cassandra may be used.

Additionally, distributed databases (e.g., CockroachDB) can be considered for high availability and scalability.

Data Consistency and Transactions
Use the Saga Pattern to manage distributed transactions across multiple services, ensuring consistency without relying on a single database.

Have a Data Lake Database for analytics purposes only and populated with the help of event sourcing

## 6. Security Layer

Security is paramount in banking systems. Some key security practices include:

Data Encryption: Both data at rest (in databases) and in transit (between clients and services) are encrypted (e.g., AES encryption for at rest, TLS for transit).
Authentication: Implement strong authentication mechanisms like multi-factor authentication (MFA) and OAuth2.
Role-Based Access Control (RBAC): Ensure that only authorized users can perform specific actions.
Monitoring & Auditing: Implement extensive logging of user activity and system events for auditing and fraud detection.

## 7. External Integrations

Third-Party Services: The banking system may integrate with third-party payment gateways, credit scoring services, and external financial services.
Core Banking Systems: A legacy core banking system might handle critical financial functions like settlements, reconciliation, or credit checks. API integration with these systems may be necessary.

## 8. Caching Layer

A caching mechanism, such as Redis or Memcached, is implemented for frequently accessed data (e.g., user balances, exchange rates). This reduces load on the database and improves response times.

## 9. Monitoring and Logging

Tools like Prometheus (for monitoring) and ELK Stack (Elasticsearch, Logstash, Kibana) (for logging and visualization) ensure that system health is tracked and any anomalies are detected quickly.

## 10. Load Balancer

A load balancer (e.g., NGINX or AWS Elastic Load Balancing) ensures incoming traffic is distributed evenly across multiple instances of microservices to prevent overload and ensure availability.

## 11. Scalability & High Availability

Horizontal Scaling: Each microservice can be scaled independently based on load. For example, the Transaction Service can have more instances during peak usage.
Database Replication: Databases are replicated across multiple data centers for high availability and disaster recovery.
Containerization: Docker containers for microservices and Kubernetes for orchestration to enable auto-scaling and easier deployments.

# Communication

Inter-microservice communication happens in two primary ways:

Synchronous (REST): For real-time, blocking calls like checking balances or initiating payments.
Asynchronous (Message Broker): For non-blocking, event-driven processes like notifications or updating reports. Technologies like Kafka or RabbitMQ are used for message queues.

# Key Considerations

Compliance: Ensure the system meets regulatory requirements like PCI-DSS for payment processing and GDPR for user data protection.
Latency and Performance: Optimize for low-latency responses, especially for real-time operations like transaction processing.
Data Backups and Recovery: Implement regular backups and disaster recovery protocols.

This high-level system design ensures scalability, fault tolerance, security, and compliance, making it suitable for a modern banking application. Let me know if you'd like to dive into any specific component in more detail!
