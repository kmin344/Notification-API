# Notification API (Email, SMS, Kakaotalk(Korean Messaging Service), App Push)

## Project Overview
Developed a unified and efficient notification system to replace individually written PHP services for various notification channels. The new system aimed to operate asynchronously without interfering with core business logic and provide a single point of entry for multiple notification services.

---
This is Notification configuration
<img width="1019" alt="image" src="https://github.com/user-attachments/assets/49e654ba-8c6e-46d1-bdbb-a2eb7535a79e">

## Architecture
```mermaid
graph TD
    A[Client] -->|API Request| B(API Gateway)
    B --> C[Core Business Logic]
    C -->|Trigger Notification| D[Notification Service]
    D -->|Enqueue| E[AWS SQS]
    E -->|Dequeue| F[Notification Worker]
    F -->|Hash Check| G{Duplicate Check}
    G -->|No| H[Notification Dispatcher]
    G -->|Yes| I[Discard]
    H --> J[Email Service]
    H --> K[SMS Service]
    H --> L[Kakaotalk Service]
    H --> M[App Push Service]
    N[Kubernetes Cluster] -->|Hosts| F
    O[Datadog] -->|Monitor| N
    P[Consumer Scaler] -->|Adjust| N
    H -->|Log| Q[Logging System]
    H -->|Bill| R[Billing System]

    subgraph CQRS
    C
    D
    E
    F
    end

    subgraph Hashing Algorithm
    S[SHA-256]
    end

    style CQRS fill:#f9f,stroke:#333,stroke-width:2px
    style Hashing Algorithm fill:#f0f,stroke:#333,stroke-width:2px
```



## Key Challenges
- Existing PHP notification services interfered with core logic when malfunctioning
- Need for asynchronous operation independent of core logic
- Requirement for a single API call to trigger multiple notification services
- Delays of up to 24 hours in app push notification delivery

## Technical Implementation
- Applied CQRS pattern to queue notification tasks when triggered by core logic
- Implemented data hashing to prevent duplicate notifications
- Set up dedicated notification servers for scalable mass notification dispatch
- Designed system for dynamic scaling of consumers to prevent long-term notification queuing

## Achievements
- Enabled immediate customer interaction with notifications sent by site operators
- Reduced customer notification credit consumption (SMS, Kakaotalk, App Push) by 30% through duplicate prevention
- Enhanced marketing capabilities through improved notification responsiveness
- Prevented server downtime by decoupling notification logic from core business logic

## Technologies Used
- CQRS pattern
- Queue systems
- Hashing algorithms
- Scalable server architecture
