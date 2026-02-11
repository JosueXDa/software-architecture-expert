# Comprehensive Diagram Guidelines

This document provides standards and best practices for creating architecture diagrams using the C4 model and Mermaid.

---

## Overview

### Why Diagram?
- **Communication**: Shared understanding across teams
- **Documentation**: Visual reference for system design
- **Onboarding**: Quick ramp-up for new team members
- **Decision Making**: Visualize trade-offs and options
- **Maintenance**: Track system evolution

### Core Principles
1. **Clarity over Completeness**: Show what matters, hide what doesn't
2. **Consistency**: Use standard notation and styles
3. **Simplicity**: Minimize cognitive load
4. **Purpose-Driven**: Each diagram serves specific audience
5. **Living Documentation**: Keep diagrams updated with system

---

## The C4 Model

The C4 model provides a hierarchical way to visualize software architecture at different levels of abstraction.

### C4 Levels

```
Level 1: System Context (Bird's eye view)
   ↓
Level 2: Container (System decomposition)
   ↓
Level 3: Component (Container internals)
   ↓
Level 4: Code (Class diagrams) - Optional
```

### When to Use Each Level

| Level | Audience | Purpose | Update Frequency |
|-------|----------|---------|------------------|
| Context | Everyone | System boundaries | Rarely (quarterly) |
| Container | Tech leads, architects | High-level tech choices | Monthly |
| Component | Developers | Internal structure | Weekly |
| Code | Developers | Implementation details | Daily (auto-generated) |

---

## Level 1: System Context Diagram

### Purpose
Show the system in context of its environment - users, external systems, and interactions.

### Audience
- Business stakeholders
- Product managers
- Architects
- Anyone new to the system

### What to Include
- ✅ Your system (as single box)
- ✅ Users/personas
- ✅ External systems
- ✅ Key interactions (arrows)

### What to Exclude
- ❌ Internal system details
- ❌ Technology choices
- ❌ Deployment infrastructure
- ❌ Data flows within system

### Example: E-Commerce Platform

```mermaid
graph TB
    Customer[Customer<br/>User]
    Admin[Administrator<br/>User]
    
    System[E-Commerce Platform<br/>Software System]
    
    PaymentGateway[Payment Gateway<br/>External System]
    EmailService[Email Service<br/>External System]
    InventorySystem[Inventory System<br/>External System]
    
    Customer -->|Browse products,<br/>Place orders| System
    Admin -->|Manage products,<br/>View analytics| System
    
    System -->|Process payments| PaymentGateway
    System -->|Send notifications| EmailService
    System -->|Check stock| InventorySystem
    
    style System fill:#1168bd,color:#fff
    style Customer fill:#08427b,color:#fff
    style Admin fill:#08427b,color:#fff
    style PaymentGateway fill:#999999,color:#fff
    style EmailService fill:#999999,color:#fff
    style InventorySystem fill:#999999,color:#fff
```

### Best Practices
- Keep to 5-9 boxes (cognitive limit)
- Use clear, business-friendly names
- Show direction of information flow
- Include brief descriptions
- Use consistent coloring:
  - Your system: Blue
  - Users: Dark blue
  - External systems: Gray

---

## Level 2: Container Diagram

### Purpose
Show the high-level technology choices and how containers communicate.

### Audience
- Development team
- DevOps/SRE
- Technical architects
- Tech leads

### What to Include
- ✅ Applications (web apps, APIs, mobile apps)
- ✅ Databases
- ✅ File systems
- ✅ Message brokers
- ✅ Technology stack (in labels)
- ✅ Communication protocols

### What to Exclude
- ❌ Internal code structure
- ❌ Detailed data models
- ❌ Deployment infrastructure

### Example: E-Commerce Container Diagram

```mermaid
graph TB
    Customer[Customer<br/>User]
    
    subgraph "E-Commerce Platform"
        WebApp[Web Application<br/>React, TypeScript<br/>Container]
        MobileApp[Mobile App<br/>React Native<br/>Container]
        APIGateway[API Gateway<br/>Kong<br/>Container]
        
        OrderService[Order Service<br/>Node.js, Express<br/>Container]
        ProductService[Product Service<br/>Node.js, Express<br/>Container]
        PaymentService[Payment Service<br/>Node.js, Express<br/>Container]
        
        OrderDB[(Order Database<br/>PostgreSQL<br/>Container)]
        ProductDB[(Product Database<br/>PostgreSQL<br/>Container)]
        
        Cache[(Cache<br/>Redis<br/>Container)]
        Queue[Message Queue<br/>RabbitMQ<br/>Container]
    end
    
    PaymentGateway[Payment Gateway<br/>External System]
    
    Customer -->|HTTPS| WebApp
    Customer -->|HTTPS| MobileApp
    
    WebApp -->|HTTPS/JSON| APIGateway
    MobileApp -->|HTTPS/JSON| APIGateway
    
    APIGateway -->|HTTP/REST| OrderService
    APIGateway -->|HTTP/REST| ProductService
    APIGateway -->|HTTP/REST| PaymentService
    
    OrderService -->|JDBC| OrderDB
    ProductService -->|JDBC| ProductDB
    
    OrderService -->|TCP| Cache
    ProductService -->|TCP| Cache
    
    OrderService -->|AMQP| Queue
    PaymentService -->|AMQP| Queue
    
    PaymentService -->|HTTPS| PaymentGateway
    
    style WebApp fill:#1168bd,color:#fff
    style MobileApp fill:#1168bd,color:#fff
    style APIGateway fill:#1168bd,color:#fff
    style OrderService fill:#1168bd,color:#fff
    style ProductService fill:#1168bd,color:#fff
    style PaymentService fill:#1168bd,color:#fff
    style OrderDB fill:#1168bd,color:#fff
    style ProductDB fill:#1168bd,color:#fff
    style Cache fill:#1168bd,color:#fff
    style Queue fill:#1168bd,color:#fff
```

### Best Practices
- Show technology stack in labels (e.g., "Node.js, Express")
- Include communication protocols (HTTP, gRPC, AMQP)
- Group related containers visually
- Limit to 10-15 containers per diagram
- Use database cylinder notation
- Show data stores separately from applications

### Container Types
- **Web Application**: Browser-based UI
- **Mobile App**: Native or hybrid mobile
- **API**: RESTful or GraphQL service
- **Background Worker**: Async job processor
- **Database**: Data storage
- **Cache**: In-memory data store
- **Message Broker**: Event/message queue

---

## Level 3: Component Diagram

### Purpose
Show the internal structure of a single container and how components interact.

### Audience
- Developers working on the container
- Code reviewers
- New team members

### What to Include
- ✅ Major components/modules
- ✅ Responsibilities
- ✅ Interfaces/APIs
- ✅ Dependencies between components

### What to Exclude
- ❌ Individual classes (use Level 4 for that)
- ❌ Implementation details
- ❌ Other containers (reference only)

### Example: Order Service Components

```mermaid
graph TB
    APIGateway[API Gateway<br/>External Container]
    
    subgraph "Order Service Container"
        OrderController[Order Controller<br/>REST endpoints<br/>Component]
        OrderService[Order Service<br/>Business logic<br/>Component]
        OrderRepository[Order Repository<br/>Data access<br/>Component]
        PaymentClient[Payment Client<br/>External integration<br/>Component]
        EventPublisher[Event Publisher<br/>Message publishing<br/>Component]
    end
    
    OrderDB[(Order Database<br/>External Container)]
    Queue[Message Queue<br/>External Container]
    PaymentService[Payment Service<br/>External Container]
    
    APIGateway -->|HTTP/JSON| OrderController
    OrderController -->|calls| OrderService
    OrderService -->|calls| OrderRepository
    OrderService -->|calls| PaymentClient
    OrderService -->|calls| EventPublisher
    
    OrderRepository -->|JDBC| OrderDB
    PaymentClient -->|HTTP| PaymentService
    EventPublisher -->|AMQP| Queue
    
    style OrderController fill:#1168bd,color:#fff
    style OrderService fill:#1168bd,color:#fff
    style OrderRepository fill:#1168bd,color:#fff
    style PaymentClient fill:#1168bd,color:#fff
    style EventPublisher fill:#1168bd,color:#fff
```

### Best Practices
- Focus on one container at a time
- Show component responsibilities
- Indicate architectural patterns (MVC, Hexagonal, etc.)
- Limit to 8-12 components
- Group by layer or responsibility
- Show external dependencies clearly

### Common Component Patterns

#### Layered Architecture
```
Presentation Layer → Service Layer → Data Layer
```

#### Hexagonal Architecture
```
Adapters (Primary) → Domain → Adapters (Secondary)
```

#### Clean Architecture
```
Controllers → Use Cases → Entities
```

---

## Level 4: Code Diagram (Optional)

### Purpose
Class/interface level details (usually auto-generated).

### Audience
- Developers only

### Tools
- UML class diagrams (PlantUML)
- Auto-generated from code (Doxygen, JavaDoc)

### When to Create
- Complex domain models
- Framework design
- Library APIs

**Recommendation**: Auto-generate these rather than maintaining manually.

---

## Sequence Diagrams

### Purpose
Show how components/containers interact over time for specific scenarios.

### When to Use
- API flows
- Authentication flows
- Error scenarios
- Complex multi-step processes

### Example: Order Placement Flow

```mermaid
sequenceDiagram
    participant Customer
    participant WebApp
    participant APIGateway
    participant OrderService
    participant PaymentService
    participant OrderDB
    participant Queue
    
    Customer->>WebApp: Click "Place Order"
    WebApp->>APIGateway: POST /api/orders
    APIGateway->>OrderService: createOrder(orderData)
    
    OrderService->>OrderDB: INSERT order (status: pending)
    OrderDB-->>OrderService: orderId
    
    OrderService->>PaymentService: processPayment(orderId, amount)
    
    alt Payment Success
        PaymentService-->>OrderService: paymentId
        OrderService->>OrderDB: UPDATE order (status: confirmed)
        OrderService->>Queue: PUBLISH OrderConfirmed event
        OrderService-->>APIGateway: 200 OK
        APIGateway-->>WebApp: Order confirmed
        WebApp-->>Customer: Show success page
    else Payment Failed
        PaymentService-->>OrderService: Payment error
        OrderService->>OrderDB: UPDATE order (status: failed)
        OrderService-->>APIGateway: 402 Payment Required
        APIGateway-->>WebApp: Payment failed
        WebApp-->>Customer: Show error message
    end
```

### Best Practices
- Focus on one scenario per diagram
- Include happy path and key error paths
- Show async operations clearly
- Label arrows with method/message names
- Include HTTP status codes for API calls
- Use `alt`/`opt` blocks for conditionals
- Keep participants to 5-8

---

## Data Flow Diagrams

### Purpose
Show how data moves through the system.

### When to Use
- ETL pipelines
- Event-driven architectures
- Data processing workflows
- Integration patterns

### Example: Analytics Pipeline

```mermaid
graph LR
    UserAction[User Actions<br/>Events]
    WebApp[Web Application]
    EventBus[Event Bus<br/>Kafka]
    
    Processor1[Stream Processor<br/>Flink]
    Processor2[Batch Processor<br/>Spark]
    
    DataLake[(Data Lake<br/>S3)]
    DataWarehouse[(Data Warehouse<br/>Redshift)]
    
    Dashboard[Analytics Dashboard<br/>Metabase]
    
    UserAction -->|Real-time| WebApp
    WebApp -->|Publish| EventBus
    EventBus -->|Stream| Processor1
    EventBus -->|Batch| Processor2
    
    Processor1 -->|Store raw| DataLake
    Processor2 -->|Transform & Load| DataWarehouse
    
    DataWarehouse -->|Query| Dashboard
    
    style EventBus fill:#ff9900,color:#fff
    style DataLake fill:#1168bd,color:#fff
    style DataWarehouse fill:#1168bd,color:#fff
```

---

## Deployment Diagrams

### Purpose
Show how containers are deployed to infrastructure.

### When to Use
- Infrastructure planning
- DevOps documentation
- Scaling strategies
- Disaster recovery planning

### Example: Kubernetes Deployment

```mermaid
graph TB
    subgraph "AWS Cloud"
        subgraph "VPC"
            subgraph "Public Subnet"
                ALB[Application Load Balancer]
            end
            
            subgraph "Private Subnet - K8s Cluster"
                subgraph "Namespace: production"
                    WebPod1[Web App Pod]
                    WebPod2[Web App Pod]
                    APIPod1[API Pod]
                    APIPod2[API Pod]
                    WorkerPod[Worker Pod]
                end
            end
            
            subgraph "Data Subnet"
                RDS[(RDS PostgreSQL<br/>Multi-AZ)]
                ElastiCache[(ElastiCache Redis)]
            end
        end
    end
    
    Internet[Internet] -->|HTTPS| ALB
    ALB -->|HTTP| WebPod1
    ALB -->|HTTP| WebPod2
    WebPod1 -->|HTTP| APIPod1
    WebPod2 -->|HTTP| APIPod2
    
    APIPod1 -->|SQL| RDS
    APIPod2 -->|SQL| RDS
    APIPod1 -->|TCP| ElastiCache
    APIPod2 -->|TCP| ElastiCache
    
    WorkerPod -->|SQL| RDS
    
    style ALB fill:#ff9900,color:#fff
    style RDS fill:#1168bd,color:#fff
    style ElastiCache fill:#ff0000,color:#fff
```

### Best Practices
- Show availability zones for HA
- Include load balancers
- Show data stores separately
- Indicate network boundaries (VPC, subnets)
- Label instance types/sizes
- Show scaling strategy (min/max pods)

---

## Integration Patterns Diagrams

### Common Patterns

#### API Gateway Pattern
```mermaid
graph LR
    Client[Client Apps]
    Gateway[API Gateway]
    Service1[Service A]
    Service2[Service B]
    Service3[Service C]
    
    Client -->|All requests| Gateway
    Gateway -->|Route| Service1
    Gateway -->|Route| Service2
    Gateway -->|Route| Service3
    
    style Gateway fill:#ff9900,color:#fff
```

#### Event-Driven Pattern
```mermaid
graph TB
    Service1[Order Service]
    Service2[Inventory Service]
    Service3[Notification Service]
    EventBus[Event Bus]
    
    Service1 -->|OrderPlaced| EventBus
    EventBus -->|Subscribe| Service2
    EventBus -->|Subscribe| Service3
    
    style EventBus fill:#ff9900,color:#fff
```

#### Saga Pattern (Orchestration)
```mermaid
sequenceDiagram
    participant Orchestrator
    participant OrderService
    participant PaymentService
    participant InventoryService
    
    Orchestrator->>OrderService: Create Order
    OrderService-->>Orchestrator: Order Created
    
    Orchestrator->>PaymentService: Process Payment
    PaymentService-->>Orchestrator: Payment Success
    
    Orchestrator->>InventoryService: Reserve Inventory
    
    alt Inventory Available
        InventoryService-->>Orchestrator: Reserved
        Orchestrator->>OrderService: Confirm Order
    else Inventory Unavailable
        InventoryService-->>Orchestrator: Not Available
        Orchestrator->>PaymentService: Refund Payment
        Orchestrator->>OrderService: Cancel Order
    end
```

---

## Diagram Styling Guidelines

### Color Scheme

```
System/Container (Internal): #1168bd (Blue)
User/Person: #08427b (Dark Blue)
External System: #999999 (Gray)
Database: #1168bd (Blue) with cylinder shape
Message Queue: #ff9900 (Orange)
Cache: #ff0000 (Red)
Important/Critical: #ff0000 (Red)
```

### Font and Labels

```
Component Name: Bold
Component Type: Italic or in parentheses
Technology Stack: Small text below name

Example:
Order Service
(Node.js, Express)
REST API
```

### Arrow Styles

- Solid arrow: Synchronous call
- Dashed arrow: Asynchronous message
- Bidirectional: Request/response
- Label arrows with protocol (HTTP, gRPC, AMQP)

### Box Styles

- Rectangle: Service/Component
- Cylinder: Database
- Hexagon: External system
- Actor/Person icon: User
- Cloud: External cloud service

---

## Mermaid Syntax Reference

### Basic Graph
```mermaid
graph TB
    A[Square] --> B[Square]
    A --> C{Diamond}
    C -->|Yes| D[Result 1]
    C -->|No| E[Result 2]
```

### Sequence Diagram
```mermaid
sequenceDiagram
    Alice->>Bob: Hello Bob
    Bob-->>Alice: Hello Alice
    Alice->>Bob: How are you?
    Bob-->>Alice: I'm good, thanks!
```

### Class Diagram
```mermaid
classDiagram
    class Order {
        +String orderId
        +Date createdAt
        +placeOrder()
        +cancelOrder()
    }
    class OrderItem {
        +String productId
        +int quantity
    }
    Order "1" --> "*" OrderItem
```

### State Diagram
```mermaid
stateDiagram-v2
    [*] --> Pending
    Pending --> Processing: payment received
    Processing --> Shipped: order packed
    Processing --> Cancelled: cancellation requested
    Shipped --> Delivered: delivery confirmed
    Delivered --> [*]
    Cancelled --> [*]
```

### Entity-Relationship Diagram
```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : "ordered in"
    
    CUSTOMER {
        string id PK
        string name
        string email
    }
    ORDER {
        string id PK
        string customerId FK
        date orderDate
    }
```

---

## Diagram Best Practices

### Do's
- ✅ Use consistent notation across all diagrams
- ✅ Include a legend if using custom symbols
- ✅ Keep diagrams simple (5-9 boxes ideal)
- ✅ Use descriptive names (not technical IDs)
- ✅ Show protocols on arrows
- ✅ Version your diagrams
- ✅ Store diagrams as code (Mermaid in Markdown)
- ✅ Update diagrams with code changes
- ✅ Include diagrams in code reviews
- ✅ Link diagrams to ADRs

### Don'ts
- ❌ Don't cram too much into one diagram
- ❌ Don't use proprietary tools (prefer open formats)
- ❌ Don't create diagrams that duplicate each other
- ❌ Don't include implementation details in high-level diagrams
- ❌ Don't use cryptic abbreviations
- ❌ Don't let diagrams become stale
- ❌ Don't use screenshots (use text-based formats)
- ❌ Don't create diagrams that can't be version controlled

---

## Diagram Review Checklist

Before finalizing a diagram, check:

- [ ] Purpose is clear (what does this show?)
- [ ] Audience is appropriate (who is this for?)
- [ ] Abstraction level is correct (not too detailed/too high)
- [ ] All boxes are labeled clearly
- [ ] Arrows show direction and are labeled
- [ ] Technology stack is indicated (where relevant)
- [ ] Color coding is consistent
- [ ] Diagram fits on one page/screen
- [ ] Legend included (if custom notation)
- [ ] Date and version on diagram
- [ ] Source file is in version control
- [ ] Linked from relevant documentation

---

## Tool Recommendations

### Text-Based (Preferred)
- **Mermaid**: In Markdown, GitHub/GitLab support
- **PlantUML**: More features, steeper learning curve
- **Structurizr**: C4 Model native, DSL-based

### Visual (Secondary)
- **Draw.io**: Free, good for quick sketches
- **Lucidchart**: Collaboration features
- **Excalidraw**: Hand-drawn style

### Auto-Generated
- **Structurizr**: From code
- **PlantUML**: From code annotations
- **Dependency diagrams**: From build tools

---

## Maintenance Strategy

### Version Control
```
docs/
  architecture/
    diagrams/
      context/
        system-context-v1.0.md
        system-context-v2.0.md (current)
      containers/
        containers-v3.0.md (current)
      components/
        order-service-components-v1.5.md (current)
```

### Review Schedule
- **Context diagrams**: Quarterly or on major changes
- **Container diagrams**: Monthly or on new services
- **Component diagrams**: Sprint basis
- **Sequence diagrams**: As needed for new flows

### Change Process
1. Identify needed change
2. Update diagram source
3. Create PR with diagram changes
4. Review with team
5. Merge and update documentation
6. Communicate changes (Slack, email)

---

## Example: Complete Set for E-Commerce

### 1. Context Diagram
High-level system boundaries (shown earlier)

### 2. Container Diagram
Services, databases, queues (shown earlier)

### 3. Component Diagram - Order Service
Internal structure (shown earlier)

### 4. Sequence Diagram - Checkout Flow
Step-by-step interaction (shown earlier)

### 5. Deployment Diagram
Infrastructure and scaling (shown earlier)

### 6. Data Flow Diagram
Event processing pipeline

```mermaid
graph LR
    WebApp[Web App]
    OrderService[Order Service]
    Kafka[Kafka Event Bus]
    
    Analytics[Analytics Service]
    Notification[Notification Service]
    Inventory[Inventory Service]
    
    DataWarehouse[(Data Warehouse)]
    
    WebApp -->|Order Created| OrderService
    OrderService -->|Publish| Kafka
    
    Kafka -->|Subscribe| Analytics
    Kafka -->|Subscribe| Notification
    Kafka -->|Subscribe| Inventory
    
    Analytics -->|Store| DataWarehouse
    Notification -->|Send Email| Customer[Customer]
    Inventory -->|Update Stock| InventoryDB[(Inventory DB)]
    
    style Kafka fill:#ff9900,color:#fff
```

---

## Common Mistakes to Avoid

### 1. Technology Proliferation
**Problem**: Showing every library and framework
**Solution**: Show only significant technology choices

### 2. Wrong Abstraction Level
**Problem**: Mixing levels (e.g., classes in a container diagram)
**Solution**: Stick to one level per diagram

### 3. Spaghetti Diagrams
**Problem**: Too many arrows crossing each other
**Solution**: Reorganize layout, split into multiple diagrams

### 4. Stale Diagrams
**Problem**: Diagrams don't match reality
**Solution**: Include diagram updates in code review, automate where possible

### 5. No Legend
**Problem**: Custom symbols without explanation
**Solution**: Always include a legend for non-standard notation

---

## Template Checklist

When creating a new diagram:

- [ ] Choose appropriate C4 level
- [ ] Identify target audience
- [ ] List elements to include
- [ ] Choose tool (Mermaid preferred)
- [ ] Create first draft
- [ ] Review with team
- [ ] Add to documentation
- [ ] Link from relevant docs
- [ ] Version in Git
- [ ] Schedule review date

---

## Additional Resources

### C4 Model
- Website: c4model.com
- Examples: github.com/structurizr/examples

### Mermaid
- Documentation: mermaid.js.org
- Live editor: mermaid.live

### Best Practices
- "Documenting Software Architectures" - Bass et al.
- "Software Systems Architecture" - Rozanski & Woods
- "The Art of Scalability" - Abbott & Fisher

---

**Last Updated**: 2025-02-11  
**Version**: 2.0  
**Maintained By**: Architecture Team