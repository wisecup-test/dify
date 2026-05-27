# Adopt Message Queue Boundaries for Asynchronous Service Integration: Services Implement Dead

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The system requires asynchronous communication between services to handle long-running operations without blocking client requests
- Multiple services (human_input_service, rag_pipeline_task_proxy) need to coordinate work across process boundaries with temporal decoupling
- Message queues provide a natural boundary for service integration, enabling independent scaling and fault isolation
- The pattern appears in 3 distinct files with 91.07% confidence, indicating a consistent architectural approach to inter-service communication
- Configuration utilities suggest message queue parameters are externalized and configurable across different deployment environments

## Problem Statement

Services need to communicate asynchronously across boundaries without tight coupling, while maintaining reliability, scalability, and fault tolerance. Direct synchronous API calls create temporal coupling and prevent independent service evolution, making the system brittle and difficult to scale.

## Decision

1. SHOULD: Services SHOULD implement dead-letter queues for handling messages that fail processing after retry attempts

## Policy Block

- SHOULD Services SHOULD implement dead-letter queues for handling messages that fail processing after retry attempts

In scope:
- Asynchronous inter-service communication patterns
- Long-running task coordination (RAG pipeline, human input processing)
- Event-driven workflows requiring temporal decoupling
- Service integration points that require independent scaling

Out of scope:
- Synchronous request-response patterns within the same service boundary
- Real-time streaming data pipelines requiring sub-second latency
- Direct database access patterns within a single service
- Client-facing API endpoints that require immediate responses

Exceptions:
- EXC-001: Services require guaranteed ordering of messages and the message queue technology does not support FIFO guarantees
- EXC-002: Legacy services cannot be modified to support message queue integration within current sprint constraints

## Rationale

- Pattern detected with 91.07% confidence across 3 distinct service integration points (human_input_service, rag_pipeline_task_proxy, configuration utilities), indicating a deliberate architectural choice
- Message queues provide natural service boundaries that enable independent deployment, scaling, and failure isolation without cascading failures
- Asynchronous communication via message queues prevents blocking operations from degrading user experience and allows services to process work at their own pace
- The presence of configuration utilities for message queue parameters demonstrates infrastructure-level commitment to this integration pattern

## Consequences

Positive:
- Services can scale independently based on their specific workload characteristics without affecting other services
- Temporal decoupling allows services to be deployed, upgraded, and maintained independently without coordinated downtime
- Message queues provide natural buffering during traffic spikes, preventing cascading failures across service boundaries
- Fault isolation ensures that failures in one service do not immediately propagate to dependent services

Negative:
- Increased operational complexity requiring message queue infrastructure monitoring, maintenance, and capacity planning
- Eventual consistency model makes debugging distributed workflows more challenging compared to synchronous calls
- Additional latency introduced by message queue hops may not be acceptable for latency-sensitive operations
- Message queue infrastructure becomes a critical dependency and potential single point of failure if not properly configured for high availability

## Alternatives

- Direct synchronous HTTP/gRPC calls between services (rejected)
  Rejected because: Creates tight temporal coupling, prevents independent scaling, and causes cascading failures when downstream services are slow or unavailable
  When valid: Only appropriate for synchronous request-response patterns requiring immediate feedback within the same transaction boundary
- Shared database for inter-service communication (rejected)
  Rejected because: Violates service autonomy, creates tight coupling through shared schema, and introduces database contention as a scaling bottleneck
  When valid: May be acceptable within a single bounded context where services share the same data model and lifecycle
- Event streaming platform (Kafka, Kinesis) for all integration (deferred)
  Rejected because: Adds complexity and operational overhead for simple task queue patterns that do not require event replay or stream processing capabilities
  When valid: Should be considered when event sourcing, event replay, or complex event processing requirements emerge

## Risks

- Message queue infrastructure failure could halt all asynchronous processing across the system
  Mitigation: Implement high-availability message queue deployment with clustering, implement circuit breakers, and maintain fallback mechanisms for critical paths
  Owner: Platform Engineering Team
- Message processing failures without proper dead-letter queue handling could result in silent data loss
  Mitigation: Mandate dead-letter queue implementation for all message consumers, implement monitoring and alerting on DLQ depth, establish runbooks for DLQ processing
  Owner: Service Development Teams
- Non-idempotent message handlers could cause data corruption or duplicate side effects when messages are redelivered
  Mitigation: Enforce idempotency testing in CI pipeline, provide idempotency key patterns in service templates, conduct code reviews focusing on message handler idempotency
  Owner: Engineering Team

## Implementation Notes

- Use established message queue client libraries that provide connection pooling, automatic reconnection, and retry logic
- Implement structured logging with correlation IDs that flow through message headers to enable distributed tracing across service boundaries
- Configure appropriate message TTL (time-to-live) and queue depth limits to prevent unbounded queue growth during outages
- Establish monitoring dashboards tracking queue depth, message age, processing latency, and dead-letter queue metrics for each integration point

## Continuation Context


Verify commands:
- grep -r "message.*queue\|queue.*client\|publish.*message\|consume.*message" --include="*.py" --include="*.ts" --include="*.js" api/ web/ | wc -l
- grep -r "idempotent\|idempotency" --include="*.py" --include="*.ts" api/ | wc -l
- find . -name "*queue*config*" -o -name "*message*config*" | wc -l

Accept when:
- Message queue integration code is present in service files (human_input_service, rag_pipeline_task_proxy) with proper client initialization
- Idempotency patterns are documented or implemented in message handler code
- Configuration files exist for message queue parameters supporting environment-specific deployment

## Enforcement

- Verified by: Automated code review checks for message queue usage in new service integration points
- Verified by: Architecture review for new services verifying message queue adoption for asynchronous operations
- Verified by: CI pipeline verification ensuring message handler idempotency tests are present
- Violation handling: Pull requests introducing synchronous inter-service calls for asynchronous operations are flagged for architecture review
- Violation handling: Services without idempotency tests for message handlers are blocked from production deployment
- Violation handling: Quarterly architecture audits identify services not following message queue patterns and create remediation tickets
- Exception process: Submit exception request to architecture review board with justification and alternative approach
- Exception process: Document approved exceptions in service architecture documentation with expiration date
- Exception process: Review all active exceptions quarterly to assess whether they can be resolved