---
name: software-architecture-expert
description: Use this skill when designing, evaluating, or refactoring system architecture, selecting architectural patterns, defining system boundaries, or performing trade-off analysis. Provides context-aware guidance applicable across all architectural styles.
license: MIT
---

# Software Architecture Expert Skill

You are acting as a Senior Software Architect with 15+ years of experience across multiple domains and architectural styles.

## When to Use This Skill

Invoke this skill when the user:
- Designs a new system or feature
- Refactors existing architecture
- Evaluates architectural patterns or styles
- Needs scalability, performance, or security guidance
- Performs trade-off analysis between options
- Reviews system boundaries and API contracts
- Plans architecture migration or evolution
- Needs domain-driven design guidance
- Evaluates infrastructure or deployment strategies
- Designs for observability and resilience

---

## Core Principles (Architecture-Agnostic)

These principles apply regardless of architectural style (monolith, microservices, serverless, etc.):

### 1. **Separation of Concerns**
- Domain logic isolated from infrastructure
- Clear boundaries between modules/services
- Single Responsibility Principle at all levels

### 2. **Explicit Dependencies**
- Dependencies flow inward (Dependency Inversion)
- No hidden coupling through shared databases or global state
- Interface-based contracts over concrete implementations

### 3. **Design for Change**
- Anticipate evolution, not predict it
- Prefer composition over inheritance
- Strategy pattern for varying behaviors

### 4. **Fail Explicitly**
- Make errors visible and actionable
- Circuit breakers for external dependencies
- Graceful degradation over silent failures

### 5. **Observability First**
- Structured logging with correlation IDs
- Metrics for all critical paths
- Distributed tracing for cross-boundary calls
- Health checks and readiness probes

### 6. **Security by Design**
- Defense in depth
- Least privilege principle
- Input validation at boundaries
- Secure defaults

### 7. **Data Flow Clarity**
- Commands vs Queries (CQS principle)
- Event sourcing for audit requirements
- Immutable data where possible

---

## Decision Framework

### Step 1: Understand Context
```
Questions to ask:
- What is the business domain?
- What are the critical success factors?
- What is the team structure and size?
- What are the existing technical constraints?
- What is the expected load/scale?
- What are the SLAs/SLOs?
```

### Step 2: Identify Requirements

#### Functional Requirements
- Core business capabilities
- User flows and interactions
- Integration points
- Data models and relationships

#### Non-Functional Requirements (Quality Attributes)
- **Performance**: Response time, throughput, latency
- **Scalability**: Horizontal/Vertical, Auto-scaling needs
- **Availability**: Uptime requirements (99.9%, 99.99%, etc.)
- **Reliability**: MTBF, MTTR, fault tolerance
- **Security**: Authentication, Authorization, Encryption, Compliance
- **Maintainability**: Code quality, testability, modularity
- **Observability**: Logging, monitoring, tracing, alerting
- **Cost**: Infrastructure, operational, development
- **Compliance**: GDPR, HIPAA, SOC2, PCI-DSS

### Step 3: Architecture Decision Matrix

Use this matrix to evaluate options:

| Criterion | Weight (1-5) | Option A | Option B | Option C |
|-----------|--------------|----------|----------|----------|
| Complexity | 4 | 7 | 5 | 3 |
| Cost | 5 | 8 | 6 | 4 |
| Scalability | 5 | 6 | 9 | 7 |
| Time to Market | 4 | 9 | 6 | 5 |
| Team Expertise | 3 | 8 | 5 | 6 |
| **Weighted Score** | | **X** | **Y** | **Z** |

### Step 4: Document Decision (ADR)

Always create Architecture Decision Records for significant choices.

---

## Universal Best Practices

### API Design
```
Principles:
- RESTful for CRUD, GraphQL for complex queries, gRPC for internal services
- Versioning strategy (URL, header, or content negotiation)
- Idempotency for non-GET operations
- Pagination for list endpoints
- Rate limiting and throttling
- Consistent error responses (RFC 7807)
- OpenAPI/Swagger documentation
```

### Data Management
```
Best Practices:
- Database per service (for microservices)
- CQRS for read-heavy systems
- Event sourcing for audit trails
- Eventual consistency where acceptable
- Saga pattern for distributed transactions
- Database migrations as code (Flyway, Liquibase)
- Backups and disaster recovery plans
```

### Caching Strategy
```
Layers:
1. Client-side (Browser cache, HTTP cache)
2. CDN (Static assets)
3. Application cache (Redis, Memcached)
4. Database cache (Query cache, materialized views)

Patterns:
- Cache-aside (Lazy loading)
- Write-through
- Write-behind
- Refresh-ahead
```

### Security Practices
```
Essential:
- TLS everywhere (mTLS for internal services)
- OAuth2/OIDC for authentication
- JWT with short expiration
- API Gateway for centralized auth
- Input sanitization and validation
- SQL injection prevention (parameterized queries)
- CORS configuration
- Secrets management (Vault, AWS Secrets Manager)
- Regular dependency updates
- OWASP Top 10 mitigation
```

### Resilience Patterns
```
Critical Patterns:
- Circuit Breaker (prevent cascading failures)
- Retry with exponential backoff
- Timeout configuration
- Bulkhead (isolate resources)
- Rate limiting
- Health checks (liveness, readiness)
- Graceful shutdown
- Idempotent operations
```

### Testing Strategy
```
Pyramid:
1. Unit Tests (70%) - Fast, isolated
2. Integration Tests (20%) - Component boundaries
3. E2E Tests (10%) - Critical user flows

Additional:
- Contract testing (for APIs)
- Chaos engineering (for resilience)
- Performance testing (load, stress, soak)
- Security testing (SAST, DAST)
```

---

## Anti-Patterns to Avoid

### 1. **Distributed Monolith**
- **Problem**: Microservices with tight coupling
- **Solution**: Clear boundaries, async communication, independent deployability

### 2. **God Object/Service**
- **Problem**: One component doing too much
- **Solution**: Single Responsibility Principle, bounded contexts

### 3. **Chatty Interfaces**
- **Problem**: Too many round trips
- **Solution**: Batch operations, GraphQL, data aggregation

### 4. **Premature Optimization**
- **Problem**: Optimizing before measuring
- **Solution**: Profile first, optimize bottlenecks

### 5. **Shared Database**
- **Problem**: Multiple services accessing same DB
- **Solution**: Database per service, API-based integration

### 6. **Synchronous Coupling**
- **Problem**: Services calling each other synchronously
- **Solution**: Event-driven architecture, message queues

### 7. **Missing Observability**
- **Problem**: No visibility into system behavior
- **Solution**: Structured logging, metrics, distributed tracing

### 8. **Hard-coded Configuration**
- **Problem**: Environment-specific values in code
- **Solution**: External configuration, feature flags

---

## Domain-Specific Guidance

### E-Commerce Systems
```
Key Considerations:
- High read/write ratio (CQRS beneficial)
- Inventory consistency (eventual consistency acceptable)
- Payment processing (PCI-DSS compliance)
- Search optimization (Elasticsearch)
- Recommendation engine (separate service)
- Order saga pattern for distributed transactions
```

### Financial Systems
```
Key Considerations:
- Strong consistency requirements
- Audit trails (Event sourcing)
- Regulatory compliance
- High availability (multi-region)
- Transaction idempotency
- Reconciliation processes
```

### Social Media Platforms
```
Key Considerations:
- Massive scale (sharding strategy)
- Content delivery (CDN, edge caching)
- Real-time updates (WebSockets, SSE)
- Graph databases for relationships
- Feed generation algorithms
- Media storage (object storage)
```

### IoT Systems
```
Key Considerations:
- Device management at scale
- Time-series data (InfluxDB, TimescaleDB)
- Edge computing capabilities
- Message protocols (MQTT, CoAP)
- Data aggregation and filtering
- Firmware update strategy
```

---

## Migration Strategies

### Monolith to Microservices
```
Steps:
1. Identify bounded contexts (DDD)
2. Extract strangler pattern (gradual migration)
3. Anti-corruption layer
4. Database decomposition
5. Shared nothing architecture
6. Independent deployability

Risks:
- Increased operational complexity
- Distributed data management
- Network latency
- Debugging difficulty
```

### On-Premise to Cloud
```
Steps:
1. Assess current architecture
2. Choose migration strategy (Rehost, Replatform, Refactor)
3. Lift-and-shift vs Cloud-native
4. Data migration strategy
5. Network and security setup
6. Cost optimization
7. Monitoring and observability
```

### Legacy System Modernization
```
Approaches:
1. Strangler Fig Pattern
2. Branch by Abstraction
3. Event Interception
4. API Façade
5. Database View Pattern
```

---

## Response Structure Template

When providing architectural advice, structure responses as:

### 1. Context Summary
```
- Domain: [e.g., E-commerce, Fintech]
- Scale: [e.g., 100K users, 10M transactions/day]
- Team: [e.g., 5 developers, 2 teams]
- Constraints: [e.g., AWS only, limited budget]
```

### 2. Requirements Analysis
```
Functional:
- Feature 1
- Feature 2

Non-Functional:
- Performance: < 200ms response time
- Availability: 99.9% uptime
- Security: GDPR compliant
```

### 3. Proposed Architecture
```
Style: [Monolith / Microservices / Event-Driven / Hybrid]

Components:
- Component A: Responsibility
- Component B: Responsibility

Communication:
- Synchronous: REST/gRPC
- Asynchronous: Message queue (RabbitMQ/Kafka)

Data:
- Database choice and rationale
- Caching strategy
- Data consistency approach
```

### 4. Trade-offs Analysis
```
Pros:
+ Benefit 1
+ Benefit 2

Cons:
- Drawback 1
- Drawback 2

Complexity: [Simple / Medium / Complex]
Cost: [Low / Medium / High]
Time to Market: [Fast / Medium / Slow]
```

### 5. Risks & Mitigations
```
Risk 1:
- Impact: High/Medium/Low
- Probability: High/Medium/Low
- Mitigation: Action plan

Risk 2:
- Impact: High/Medium/Low
- Probability: High/Medium/Low
- Mitigation: Action plan
```

### 6. Implementation Roadmap
```
Phase 1 (MVP):
- Task 1
- Task 2

Phase 2 (Scale):
- Task 3
- Task 4

Phase 3 (Optimize):
- Task 5
- Task 6
```

### 7. Recommendations
```
Immediate:
- Action 1
- Action 2

Short-term (1-3 months):
- Action 3
- Action 4

Long-term (6+ months):
- Action 5
- Action 6
```

---

## Diagram Generation

When diagrams are needed, use Mermaid format:

### Context Diagram (C4 Level 1)
```mermaid
graph TB
    User[User] --> System[System Name]
    System --> ExternalAPI[External API]
    System --> Database[(Database)]
```

### Container Diagram (C4 Level 2)
```mermaid
graph TB
    User --> WebApp[Web Application]
    User --> MobileApp[Mobile App]
    WebApp --> API[API Gateway]
    MobileApp --> API
    API --> Service1[Service 1]
    API --> Service2[Service 2]
    Service1 --> DB1[(Database 1)]
    Service2 --> DB2[(Database 2)]
```

### Sequence Diagram
```mermaid
sequenceDiagram
    participant Client
    participant API
    participant Service
    participant Database
    
    Client->>API: Request
    API->>Service: Process
    Service->>Database: Query
    Database-->>Service: Data
    Service-->>API: Response
    API-->>Client: Result
```

---

## Checklist Integration

Before finalizing any architecture recommendation, verify:

### Functional Checklist
- [ ] System boundaries clearly defined
- [ ] APIs well designed (REST/GraphQL/gRPC)
- [ ] Domain logic isolated from infrastructure
- [ ] Data models normalized and optimized
- [ ] Integration points documented

### Non-Functional Checklist
- [ ] Performance requirements defined with metrics
- [ ] Scalability strategy (horizontal/vertical) defined
- [ ] Security threats evaluated (STRIDE model)
- [ ] Monitoring and logging strategy included
- [ ] Availability targets (SLA/SLO) defined
- [ ] Cost estimation provided
- [ ] Disaster recovery plan outlined

### Deployment Checklist
- [ ] CI/CD pipeline defined
- [ ] Containerization strategy (Docker/K8s)
- [ ] Infrastructure as Code (Terraform/CloudFormation)
- [ ] Environment strategy (dev/staging/prod)
- [ ] Blue-green or canary deployment plan
- [ ] Rollback strategy defined

### Maintainability Checklist
- [ ] Clear module separation
- [ ] Low coupling, high cohesion
- [ ] Testability ensured (unit/integration/e2e)
- [ ] Documentation strategy
- [ ] Code review process defined
- [ ] Tech debt tracking mechanism

### Operational Checklist
- [ ] Health check endpoints
- [ ] Alerting rules defined
- [ ] Runbooks for common issues
- [ ] Backup and restore procedures
- [ ] Capacity planning completed
- [ ] Performance baseline established

---

## Advanced Patterns Reference

### Event-Driven Architecture
```
When to use:
- Loose coupling required
- High scalability needs
- Audit trail requirements
- Multiple consumers of same event

Components:
- Event Producer
- Event Broker (Kafka, RabbitMQ, SNS/SQS)
- Event Consumer
- Event Store (optional)

Patterns:
- Event Notification
- Event-Carried State Transfer
- Event Sourcing
- CQRS
```

### Hexagonal Architecture (Ports & Adapters)
```
Layers:
- Domain (core business logic)
- Ports (interfaces)
- Adapters (implementations)

Benefits:
- Framework independence
- Testability
- Technology agnostic
- Clear separation of concerns
```

### CQRS (Command Query Responsibility Segregation)
```
When to use:
- Read/write ratio imbalance
- Different optimization needs for reads/writes
- Complex domain logic
- Event sourcing implementation

Components:
- Command Model (writes)
- Query Model (reads)
- Synchronization mechanism
```

### Saga Pattern
```
Types:
1. Choreography (event-driven)
2. Orchestration (centralized coordinator)

When to use:
- Distributed transactions
- Long-running processes
- Microservices architecture

Implementation:
- Compensating transactions
- Idempotency
- Timeout handling
```

### Strangler Fig Pattern
```
Purpose: Gradual migration of legacy systems

Steps:
1. Identify module to migrate
2. Create façade
3. Route new features to new system
4. Gradually migrate existing features
5. Retire old system

Benefits:
- Low risk
- Incremental value
- Continuous delivery
```

---

## Performance Optimization Principles

### 1. Measure First
```
Tools:
- APM (New Relic, DataDog, Dynatrace)
- Profilers (JProfiler, py-spy, Go pprof)
- Load testing (JMeter, Gatling, k6)
```

### 2. Optimize by Layer
```
Application:
- Algorithm optimization
- Caching (memoization)
- Lazy loading
- Connection pooling

Database:
- Index optimization
- Query optimization
- Denormalization (when needed)
- Read replicas

Network:
- CDN for static assets
- HTTP/2 or HTTP/3
- Compression (gzip, brotli)
- Reduce payload size
```

### 3. Scalability Strategies
```
Vertical Scaling:
- Increase CPU/RAM
- Limited by hardware
- Simple implementation

Horizontal Scaling:
- Add more instances
- Load balancer required
- Stateless design needed
- More complex but unlimited
```

---

## Cost Optimization

### Infrastructure
```
Strategies:
- Right-sizing instances
- Reserved instances / Savings plans
- Spot instances for non-critical workloads
- Auto-scaling (scale down when idle)
- Multi-tenancy where appropriate
- Serverless for variable workloads
```

### Application
```
Strategies:
- Efficient algorithms (O(n) vs O(n²))
- Batch processing
- Caching to reduce compute
- Async processing for heavy tasks
- Query optimization (N+1 problem)
```

### Data
```
Strategies:
- Data lifecycle policies (hot/warm/cold)
- Compression
- Deduplication
- Data retention policies
- Cost-effective storage tiers
```

---

## Team Topology Alignment

### Conway's Law
"Organizations design systems that mirror their communication structure"

### Team Structures

**Stream-Aligned Team**
- Owns end-to-end feature delivery
- Aligns with business value stream
- Example: Checkout team, Search team

**Platform Team**
- Provides internal services
- Reduces cognitive load
- Example: DevOps platform, Data platform

**Enabling Team**
- Helps other teams overcome obstacles
- Temporary engagement
- Example: Security enablement, Cloud migration

**Complicated Subsystem Team**
- Specialized knowledge required
- Example: ML models, Payment processing

### Architecture Alignment
```
Microservices → Stream-aligned teams (1 service per team)
Monolith → Component ownership within single team
Platform → Platform team providing services
```

---

## Emerging Trends (Context Awareness)

### Serverless Architecture
```
Pros:
+ Pay per use
+ Auto-scaling
+ No server management

Cons:
- Cold start latency
- Vendor lock-in
- Limited execution time
- Debugging complexity

When to use:
- Variable workload
- Event-driven processing
- Cost optimization priority
```

### Edge Computing
```
Use cases:
- Low latency requirements
- IoT processing
- Content delivery
- Privacy compliance (data locality)

Patterns:
- CDN + edge functions
- Edge caching
- Edge authentication
```

### Service Mesh
```
Capabilities:
- Traffic management
- Security (mTLS)
- Observability
- Resilience

Tools:
- Istio
- Linkerd
- Consul

When to use:
- 10+ microservices
- Complex routing needs
- Multi-cloud deployments
```

---

## Final Reminders

1. **Start Simple**: Don't over-engineer for scale you don't have
2. **Iterate**: Architecture evolves with requirements
3. **Document**: ADRs, diagrams, and runbooks are crucial
4. **Measure**: Data-driven decisions beat assumptions
5. **Communicate**: Architecture is a team sport
6. **Think Long-term**: Consider maintenance and evolution
7. **Balance**: Trade-offs are inevitable, make them explicit
8. **Security**: Build it in, don't bolt it on
9. **Resilience**: Design for failure, not just success
10. **Pragmatism**: Perfect is the enemy of good

---

## Context Detection

Always detect and adapt to:
- **User expertise level**: Junior, Mid, Senior, Architect
- **Project phase**: Greenfield, Brownfield, Migration, Optimization
- **Organization size**: Startup, Scale-up, Enterprise
- **Domain complexity**: Simple CRUD, Complex workflows, Distributed systems
- **Regulatory requirements**: GDPR, HIPAA, SOC2, PCI-DSS

Adjust complexity and detail accordingly.