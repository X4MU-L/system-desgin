# Core Banking App System Design

## Table of Contents

- [What is Core Banking?](docs/README.core-app.md#What-is-Core-Banking?)
  - [Key Components of Core Banking](docs/README.core-app.md##Key-Components-of-Core-Banking)
- [Architecture Design Overview](docs/README.info.md#Architecture-Design-Overview)
- [Communication](docs/README.info.md#communication)
- [Key Considerations](docs/README.info.md#key-considerations)

![Core Banking System design](images/core-banking-desgin.png)

## Example of a service implementation (Account Service)

```mermaid
    flowchart TB
    subgraph "Sync Communication"
        direction TB
        APIRequest[API Request]
        AccountController[Account Controller]
        AccountService[Account Service Logic]
        AccountRepository[Account Repository]
    end

    subgraph "Async Communication (Event Bus)"
        direction TB
        KafkaTopic[Kafka Topic: account-events]
        NotificationProducer[Notification Event Producer]
        EventConsumer[Event Consumer]
        NotificationService[Notification Service]
    end

    subgraph "Database Interactions"
        AccountDatabase[(PostgreSQL Account Database)]
    end

    subgraph "External Integrations"
        CreditScoreService[Credit Score Service]
        CoreBankingSystem[Core Banking System]
    end

    APIRequest --> AccountController
    AccountController --> AccountService
    AccountService --> AccountRepository
    AccountRepository --> AccountDatabase

    AccountService --> NotificationProducer
    NotificationProducer --> KafkaTopic
    KafkaTopic --> EventConsumer
    EventConsumer --> NotificationService

    AccountService --> CreditScoreService
    AccountService --> CoreBankingSystem
```

## Asyncronous Event Flow Diagram

```mermaid
sequenceDiagram
    participant Client
    participant AccountService
    participant KafkaTopic
    participant NotificationService
    participant UserNotification

    Client->>AccountService: Create New Account
    AccountService->>AccountService: Validate Account Details
    AccountService->>KafkaTopic: Publish Account Created Event
    KafkaTopic->>NotificationService: Consume Account Created Event
    NotificationService->>UserNotification: Send Welcome Email/SMS
```
