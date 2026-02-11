# Architectural Patterns Comprehensive Reference

This document provides detailed guidance on architectural patterns with decision criteria, trade-offs, and real-world application scenarios.

---

## Pattern Selection Matrix

| Pattern | Complexity | Scalability | Maintenance | Team Size | Best For |
|---------|-----------|-------------|-------------|-----------|----------|
| Monolith | Low | Low | Medium | Small (1-5) | MVPs, small apps |
| Modular Monolith | Medium | Medium | High | Medium (5-15) | Growing apps |
| Microservices | High | High | High | Large (15+) | Large scale, multiple teams |
| Event-Driven | High | Very High | Medium | Medium-Large | Real-time, async workflows |
| Serverless | Low-Medium | Very High | Medium | Small-Medium | Variable load, cost optimization |
| Hexagonal | Medium | Medium | Very High | Medium | Domain-rich applications |
| Clean Architecture | Medium | Medium | Very High | Medium | Long-term maintainability |
| CQRS | High | High | Medium | Medium-Large | Read-heavy, complex queries |
| Event Sourcing | Very High | High | Medium | Large | Audit trails, time travel |
| Saga Pattern | High | High | Medium | Large | Distributed transactions |

---

## 1. Monolith Architecture

### Description
Single deployable unit containing all application functionality.

### When to Use
- ✅ Early-stage startups (MVP)
- ✅ Small teams (< 5 developers)
- ✅ Simple business domains
- ✅ Limited traffic (< 10K users)
- ✅ Tight deadlines
- ✅ Uncertain requirements

### When NOT to Use
- ❌ Multiple independent teams
- ❌ Different scaling needs per module
- ❌ High traffic (> 100K concurrent users)
- ❌ Need for independent deployments
- ❌ Different technology requirements per module

### Advantages
- Simple deployment
- Easy development setup
- Strong consistency
- Simple transactions
- Easy testing
- Low operational complexity
- Single codebase
- Straightforward debugging

### Disadvantages
- Tight coupling risk
- Limited scalability
- Single point of failure
- Long deployment times as it grows
- Technology lock-in
- Difficult to parallelize development

### Implementation Best Practices
```
Structure:
/src
  /domain         # Business logic
  /application    # Use cases
  /infrastructure # External concerns
  /presentation   # UI/API layer

Key Principles:
- Modular design (even in monolith)
- Clear boundaries between modules
- Dependency Injection
- Layered architecture
- Single database with schemas per module
```

### Migration Path
```
Monolith → Modular Monolith → Microservices
- Step 1: Introduce clear module boundaries
- Step 2: Separate database schemas
- Step 3: API-based internal communication
- Step 4: Extract services gradually (strangler pattern)
```

### Real-World Examples
- Stack Overflow (modular monolith)
- Basecamp
- GitHub (started as monolith)
- Shopify (modular monolith)

---

## 2. Modular Monolith

### Description
Monolithic deployment with well-defined module boundaries and independent deployability potential.

### When to Use
- ✅ Growing from MVP
- ✅ Medium-sized teams (5-15 developers)
- ✅ Need for clear boundaries
- ✅ Want microservices benefits without complexity
- ✅ Moderate traffic
- ✅ Stable domain understanding

### Advantages
- Simple deployment (like monolith)
- Clear module boundaries
- Easier to extract services later
- Strong consistency
- Lower operational complexity than microservices
- Gradual evolution path

### Disadvantages
- Requires discipline to maintain boundaries
- Still single deployment
- Can become monolith if boundaries erode
- Shared resources

### Implementation Best Practices
```
Module Structure:
/modules
  /orders
    /domain
    /application
    /infrastructure
    /api
  /payments
    /domain
    /application
    /infrastructure
    /api

Communication:
- Modules communicate via APIs (internal)
- Event bus for async communication
- Shared kernel for common code (minimal)
- Database per module (logically separated)
```

### Key Patterns
- **Module API Gateway**: Each module exposes clean API
- **Event Bus**: Async communication between modules
- **Shared Kernel**: Minimal shared code
- **Anti-Corruption Layer**: Protect module boundaries

---

## 3. Microservices Architecture

### Description
Application composed of small, independent services communicating via APIs.

### When to Use
- ✅ Large teams (15+ developers)
- ✅ Multiple independent teams
- ✅ Different scaling needs per service
- ✅ Polyglot technology requirements
- ✅ Independent deployment needs
- ✅ High traffic with variable patterns
- ✅ Well-understood domain

### When NOT to Use
- ❌ Small teams (< 10 developers)
- ❌ Unclear domain boundaries
- ❌ Limited DevOps maturity
- ❌ Tight latency requirements
- ❌ Strong consistency needs
- ❌ Startup MVP

### Advantages
- Independent scaling
- Technology flexibility
- Independent deployments
- Fault isolation
- Team autonomy
- Easier to understand individual services
- Parallel development

### Disadvantages
- High operational complexity
- Distributed system challenges
- Network latency
- Eventual consistency
- Difficult debugging
- More complex testing
- Higher infrastructure costs
- Requires mature DevOps

### Implementation Best Practices
```
Service Boundaries:
- Bounded contexts (DDD)
- Business capability alignment
- Data ownership per service
- Independent database per service

Communication:
- Synchronous: REST, gRPC
- Asynchronous: Message queues (RabbitMQ, Kafka)
- Service mesh for complex routing (Istio, Linkerd)

Data Management:
- Database per service
- Event-driven data synchronization
- Saga pattern for distributed transactions
- CQRS for complex queries
```

### Essential Patterns
- **API Gateway**: Single entry point
- **Service Discovery**: Dynamic service location
- **Circuit Breaker**: Fault tolerance
- **Saga Pattern**: Distributed transactions
- **Event Sourcing**: Audit and consistency
- **CQRS**: Separate read/write models
- **Sidecar**: Cross-cutting concerns

### Migration Strategy
```
Phase 1: Preparation
- Identify service boundaries
- Setup CI/CD
- Implement monitoring
- Create service templates

Phase 2: Extract First Service
- Choose least coupled module
- Implement strangler pattern
- Setup API gateway
- Monitor and learn

Phase 3: Scale Migration
- Extract services gradually
- Decompose database
- Implement event-driven communication
- Build platform capabilities
```

### Real-World Examples
- Netflix
- Amazon
- Uber
- Spotify

---

## 4. Event-Driven Architecture

### Description
Services communicate primarily through events via message brokers.

### When to Use
- ✅ Loose coupling required
- ✅ Real-time processing needs
- ✅ High throughput requirements
- ✅ Multiple consumers per event
- ✅ Audit trail requirements
- ✅ Complex workflows
- ✅ Async processing acceptable

### When NOT to Use
- ❌ Strong consistency required
- ❌ Simple CRUD operations
- ❌ Immediate feedback needed
- ❌ Small team without expertise
- ❌ Debugging complexity unacceptable

### Advantages
- Loose coupling
- High scalability
- Fault tolerance
- Easy to add consumers
- Natural audit trail
- Supports eventual consistency
- Peak load handling

### Disadvantages
- Eventual consistency
- Complex debugging
- Event schema evolution
- Duplicate event handling
- Ordering challenges
- Higher infrastructure complexity

### Event Types

#### 1. Domain Events
```
Purpose: Represent state changes in the domain
Example: OrderPlaced, PaymentCompleted, UserRegistered

Characteristics:
- Past tense naming
- Immutable
- Rich with domain information
- Published by aggregates
```

#### 2. Integration Events
```
Purpose: Cross-service communication
Example: OrderCreated, InventoryReserved

Characteristics:
- Minimal payload
- Versioned schema
- Platform-agnostic
- Published at service boundaries
```

#### 3. System Events
```
Purpose: Technical/infrastructure events
Example: ServiceStarted, DatabaseConnectionLost

Characteristics:
- Operational focus
- Used for monitoring
- Often ephemeral
```

### Implementation Best Practices
```
Event Schema:
{
  "eventId": "uuid",
  "eventType": "OrderPlaced",
  "timestamp": "2025-02-11T10:30:00Z",
  "version": "1.0",
  "correlationId": "uuid",
  "causationId": "uuid",
  "payload": {
    "orderId": "123",
    "customerId": "456",
    "items": [...]
  }
}

Message Broker Choices:
- RabbitMQ: Complex routing, transactions
- Apache Kafka: High throughput, log-based
- AWS SNS/SQS: Serverless, AWS-native
- Azure Service Bus: Enterprise messaging
- Google Pub/Sub: Global scale

Patterns:
- Event Notification
- Event-Carried State Transfer
- Event Sourcing
- Event Collaboration
```

### Key Patterns

#### Event Sourcing
```
Description: Store state changes as events

Advantages:
- Complete audit trail
- Time travel (replay events)
- Easy debugging
- Event-driven projections

Disadvantages:
- Complex to implement
- Event schema evolution
- Storage requirements
- Learning curve

When to use:
- Audit requirements
- Complex business rules
- Need for temporal queries
- Regulatory compliance
```

#### CQRS (with Event-Driven)
```
Description: Separate read and write models

Write Side:
- Command handlers
- Domain model
- Event publishing

Read Side:
- Event handlers
- Projections/Views
- Optimized queries

Benefits:
- Independent scaling of reads/writes
- Optimized query models
- Simplified commands
```

### Real-World Examples
- LinkedIn (Kafka for data pipeline)
- Netflix (event-driven workflows)
- Uber (event-driven dispatch)

---

## 5. Hexagonal Architecture (Ports & Adapters)

### Description
Domain logic isolated from external concerns through ports and adapters.

### When to Use
- ✅ Complex domain logic
- ✅ Long-term maintainability priority
- ✅ Framework independence needed
- ✅ High testability requirements
- ✅ Multiple UI/API types
- ✅ Technology flexibility needed

### Structure
```
/domain          # Core business logic
  /model         # Entities, value objects
  /ports         # Interfaces (input/output)
  /services      # Domain services

/application     # Use cases
  /usecases      # Application-specific logic
  
/adapters        # Implementations
  /primary       # Driving adapters (REST, GraphQL, CLI)
  /secondary     # Driven adapters (DB, External APIs)

Dependencies flow: Adapters → Domain (never reverse)
```

### Ports
```
Input Ports (Driving):
- Define use cases
- Called by primary adapters
- Example: OrderService interface

Output Ports (Driven):
- Define infrastructure needs
- Implemented by secondary adapters
- Example: OrderRepository interface
```

### Advantages
- Framework independence
- Highly testable
- Technology agnostic
- Clear separation of concerns
- Easy to change infrastructure
- Domain focus

### Disadvantages
- Initial setup complexity
- More boilerplate
- Learning curve
- Overkill for simple applications

### Implementation Example
```typescript
// Domain (port)
interface OrderService {
  placeOrder(order: Order): Promise<OrderId>;
}

interface OrderRepository {
  save(order: Order): Promise<void>;
  findById(id: OrderId): Promise<Order>;
}

// Application (use case)
class PlaceOrderUseCase implements OrderService {
  constructor(private repo: OrderRepository) {}
  
  async placeOrder(order: Order): Promise<OrderId> {
    // Domain logic here
    await this.repo.save(order);
    return order.id;
  }
}

// Adapter (infrastructure)
class PostgresOrderRepository implements OrderRepository {
  async save(order: Order): Promise<void> {
    // SQL implementation
  }
  
  async findById(id: OrderId): Promise<Order> {
    // SQL implementation
  }
}

// Adapter (primary)
class RestApiAdapter {
  constructor(private orderService: OrderService) {}
  
  @Post('/orders')
  async createOrder(req: Request): Response {
    const order = this.mapToOrder(req.body);
    const orderId = await this.orderService.placeOrder(order);
    return { orderId };
  }
}
```

### Testing Strategy
```
Unit Tests:
- Test domain logic in isolation
- Mock all ports
- No infrastructure dependencies

Integration Tests:
- Test adapters with real infrastructure
- Verify port contracts
- Test data mapping

E2E Tests:
- Full system with real adapters
- Critical user flows
```

---

## 6. Clean Architecture

### Description
Concentric layers with dependencies pointing inward, ensuring business logic independence.

### Layers (Outside → Inside)
```
1. Frameworks & Drivers (UI, DB, External APIs)
2. Interface Adapters (Controllers, Presenters, Gateways)
3. Application Business Rules (Use Cases)
4. Enterprise Business Rules (Entities)

Dependency Rule: Dependencies only point inward
```

### When to Use
- ✅ Long-term projects (5+ years)
- ✅ Complex business rules
- ✅ Need for testability
- ✅ Framework independence
- ✅ Multiple interfaces (Web, Mobile, CLI)

### Advantages
- Highly maintainable
- Independent of frameworks
- Testable
- UI independent
- Database independent
- External agency independent

### Disadvantages
- Higher initial complexity
- More code
- Steeper learning curve
- Can be overkill for simple apps

### Structure Example
```
/entities            # Core business objects
  Order.ts
  Customer.ts

/usecases           # Application-specific rules
  PlaceOrderUseCase.ts
  CancelOrderUseCase.ts

/interface-adapters # Convert data for use cases/entities
  /controllers
  /presenters
  /gateways

/frameworks-drivers # External tools
  /web
  /database
  /devices
```

### Key Principles
- **Dependency Inversion**: High-level modules don't depend on low-level
- **Single Responsibility**: Each layer has one reason to change
- **Screaming Architecture**: Structure reveals intent
- **Testability**: Business logic testable without infrastructure

---

## 7. CQRS (Command Query Responsibility Segregation)

### Description
Separate models for reading and writing data.

### When to Use
- ✅ Read/write ratio imbalance (90% reads)
- ✅ Complex query requirements
- ✅ Different optimization needs
- ✅ Event sourcing implementation
- ✅ Scalability of reads vs writes differs
- ✅ Reporting requirements differ from operational

### When NOT to Use
- ❌ Simple CRUD applications
- ❌ Balanced read/write patterns
- ❌ Small team without expertise
- ❌ Strong consistency required everywhere

### Structure
```
Command Side (Write):
- Commands (imperatives): PlaceOrder, UpdateInventory
- Command Handlers
- Domain Model
- Write Database (optimized for writes)
- Event Publishing

Query Side (Read):
- Queries: GetOrderById, SearchProducts
- Query Handlers
- Read Models (projections)
- Read Database (optimized for reads)
- Event Subscription
```

### Advantages
- Independent scaling of reads/writes
- Optimized data models for each
- Simplified commands (no query logic)
- Simplified queries (denormalized views)
- Better performance
- Flexibility in read models

### Disadvantages
- Eventual consistency
- Increased complexity
- Duplicate code potential
- Synchronization needed
- Learning curve

### Implementation Patterns

#### Simple CQRS
```
- Same database
- Separate models/handlers
- Immediate consistency possible
```

#### CQRS with Event Sourcing
```
- Event store for write side
- Projections for read side
- Eventual consistency
- Complete audit trail
```

#### CQRS with Separate Databases
```
- Write database (transactional)
- Read database (query-optimized)
- Synchronization via events
- Maximum flexibility
```

### Example
```typescript
// Command Side
class PlaceOrderCommand {
  constructor(
    public orderId: string,
    public items: OrderItem[]
  ) {}
}

class PlaceOrderHandler {
  async handle(cmd: PlaceOrderCommand): Promise<void> {
    const order = new Order(cmd.orderId, cmd.items);
    await this.repository.save(order);
    await this.eventBus.publish(new OrderPlaced(order));
  }
}

// Query Side
class GetOrderQuery {
  constructor(public orderId: string) {}
}

class GetOrderHandler {
  async handle(query: GetOrderQuery): Promise<OrderView> {
    return await this.readModel.getOrder(query.orderId);
  }
}

// Read Model (projection)
class OrderProjection {
  async on(event: OrderPlaced): Promise<void> {
    // Update denormalized view
    await this.db.upsert({
      orderId: event.orderId,
      customerName: event.customerName,
      totalAmount: event.totalAmount,
      status: 'Placed'
    });
  }
}
```

---

## 8. Saga Pattern

### Description
Manage distributed transactions through coordinated sequence of local transactions.

### When to Use
- ✅ Distributed transactions needed
- ✅ Microservices architecture
- ✅ Long-running processes
- ✅ Need for compensation logic
- ✅ Multiple services must coordinate

### Types

#### 1. Choreography
```
Description: Event-driven coordination
Each service publishes events, others react

Pros:
- Simple for basic workflows
- Loose coupling
- No single point of failure

Cons:
- Hard to understand overall flow
- Difficult to track saga state
- Cyclic dependencies risk

Example:
Order Service → OrderPlaced event
Payment Service → listens → PaymentProcessed event
Fulfillment Service → listens → OrderShipped event
```

#### 2. Orchestration
```
Description: Central coordinator manages workflow
Saga orchestrator calls services in sequence

Pros:
- Clear workflow visibility
- Centralized monitoring
- Easier to add steps

Cons:
- Central coordinator is critical
- Tighter coupling
- Potential bottleneck

Example:
Order Saga Orchestrator:
1. Call Payment Service
2. If success, call Inventory Service
3. If success, call Shipping Service
4. If any fail, compensate in reverse order
```

### Compensation
```
Each step must have compensation:

Step: Reserve Inventory
Compensation: Release Inventory

Step: Charge Payment
Compensation: Refund Payment

Step: Send Email
Compensation: Send Cancellation Email
```

### Implementation Best Practices
```
1. Idempotency
   - All operations must be idempotent
   - Use unique saga/step IDs

2. Timeout Handling
   - Each step has timeout
   - Automatic compensation on timeout

3. State Management
   - Persist saga state
   - Recover from failures

4. Observability
   - Trace saga execution
   - Monitor compensation rates
   - Alert on failures
```

### Example
```typescript
class OrderSagaOrchestrator {
  async execute(order: Order): Promise<void> {
    const sagaId = generateId();
    
    try {
      // Step 1: Reserve inventory
      await this.inventory.reserve(order.items, sagaId);
      
      // Step 2: Process payment
      await this.payment.charge(order.amount, sagaId);
      
      // Step 3: Create shipment
      await this.shipping.ship(order, sagaId);
      
      // Success
      await this.order.complete(order.id);
      
    } catch (error) {
      // Compensate in reverse order
      await this.shipping.cancel(sagaId);
      await this.payment.refund(sagaId);
      await this.inventory.release(sagaId);
      await this.order.fail(order.id);
    }
  }
}
```

---

## 9. Serverless Architecture

### Description
Applications built with Function-as-a-Service and managed services.

### When to Use
- ✅ Variable/unpredictable load
- ✅ Event-driven workloads
- ✅ Cost optimization priority
- ✅ Rapid development needed
- ✅ No server management desired
- ✅ Microservices architecture

### Advantages
- Pay per execution
- Auto-scaling (zero to infinity)
- No server management
- Fast development
- Built-in high availability
- Focus on business logic

### Disadvantages
- Cold start latency
- Vendor lock-in
- Limited execution time (15 min typically)
- Stateless design required
- Debugging complexity
- Monitoring challenges

### Best Practices
```
Function Design:
- Single purpose functions
- Stateless design
- Idempotent operations
- Short execution time
- Minimal dependencies

State Management:
- External state stores (DynamoDB, S3)
- Step Functions for workflows
- Event-driven coordination

Cold Start Optimization:
- Provisioned concurrency
- Lightweight dependencies
- Connection pooling
- Lazy loading
```

### Common Patterns
```
1. API Pattern
   API Gateway → Lambda → Database

2. Event Processing
   S3 Upload → Lambda → Process → Store

3. Stream Processing
   Kinesis/DynamoDB Stream → Lambda → Transform

4. Scheduled Tasks
   EventBridge → Lambda → Execute Job

5. Webhook Handler
   API Gateway → Lambda → External API
```

---

## 10. Service Mesh

### Description
Infrastructure layer for service-to-service communication with traffic management, security, and observability.

### When to Use
- ✅ 10+ microservices
- ✅ Complex routing needs (canary, A/B)
- ✅ mTLS requirements
- ✅ Distributed tracing needed
- ✅ Multi-cloud deployments

### Capabilities
```
Traffic Management:
- Load balancing
- Circuit breaking
- Timeout/retry configuration
- Traffic splitting (canary deployments)

Security:
- Mutual TLS (mTLS)
- Authentication
- Authorization policies
- Certificate management

Observability:
- Distributed tracing
- Metrics collection
- Access logging
- Service dependency graphs
```

### Popular Options
- **Istio**: Full-featured, complex
- **Linkerd**: Simple, lightweight
- **Consul**: HashiCorp ecosystem
- **AWS App Mesh**: AWS-native

---

## Pattern Combination Strategies

### Microservices + Event-Driven
```
Use when:
- High scalability needed
- Loose coupling priority
- Multiple teams

Implementation:
- Services communicate via events
- Message broker (Kafka/RabbitMQ)
- Event sourcing for key aggregates
- CQRS for complex queries
```

### Hexagonal + Clean + CQRS
```
Use when:
- Complex business domain
- Long-term maintainability
- High testability needs

Implementation:
- Clean architecture layers
- Hexagonal ports/adapters
- CQRS for read/write separation
- Domain-driven design
```

### Microservices + Saga + Event Sourcing
```
Use when:
- Distributed transactions
- Audit trail requirements
- Complex workflows

Implementation:
- Event-sourced aggregates
- Saga orchestration
- Event-driven communication
- CQRS for queries
```

---

## Anti-Patterns to Avoid

### 1. Big Ball of Mud
**Problem**: No clear structure, everything depends on everything
**Solution**: Introduce bounded contexts, modular design

### 2. God Service
**Problem**: One service doing too much
**Solution**: Split by bounded context, single responsibility

### 3. Chatty Services
**Problem**: Too many synchronous calls
**Solution**: Aggregate data, use async events, API composition

### 4. Shared Database
**Problem**: Multiple services accessing same database
**Solution**: Database per service, event-driven sync

### 5. Anemic Domain Model
**Problem**: Objects with no behavior, just getters/setters
**Solution**: Rich domain model with business logic

### 6. Leaky Abstraction
**Problem**: Implementation details leak through interfaces
**Solution**: Clear ports/adapters, proper encapsulation

### 7. Distributed Monolith
**Problem**: Microservices with tight coupling
**Solution**: Proper bounded contexts, async communication

---

## Decision Tree

```
Start: What are you building?

├─ Simple CRUD app, small team
│  └─→ Monolith (Modular)

├─ Growing app, 5-15 developers
│  └─→ Modular Monolith or Hexagonal Architecture

├─ Large scale, multiple teams
│  ├─ Independent scaling needed?
│  │  └─ Yes → Microservices
│  └─ Real-time processing?
│     └─ Yes → Event-Driven Architecture

├─ Variable load, cost sensitive
│  └─→ Serverless

├─ Complex business rules
│  └─→ Clean Architecture + Hexagonal

├─ High read/write imbalance
│  └─→ CQRS

├─ Audit trail required
│  └─→ Event Sourcing

└─ Distributed transactions needed
   └─→ Saga Pattern
```

---

## Migration Patterns

### Strangler Fig Pattern
```
Step 1: Identify module to migrate
Step 2: Create new implementation
Step 3: Route traffic to new (gradually)
Step 4: Monitor and verify
Step 5: Remove old implementation
```

### Branch by Abstraction
```
Step 1: Create abstraction layer
Step 2: Refactor to use abstraction
Step 3: Create new implementation
Step 4: Switch implementations
Step 5: Remove old implementation
```

### Parallel Run
```
Step 1: Run old and new systems in parallel
Step 2: Compare outputs
Step 3: Build confidence
Step 4: Switch to new system
Step 5: Decommission old system
```

---

## Conclusion

**Key Takeaways:**
1. No one-size-fits-all pattern
2. Start simple, evolve as needed
3. Understand trade-offs
4. Match pattern to context
5. Consider team capabilities
6. Patterns can be combined
7. Architecture is iterative
8. Document decisions (ADR)