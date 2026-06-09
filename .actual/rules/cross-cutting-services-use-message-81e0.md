# Adopt Message Queue Boundaries for Asynchronous Service Integration: Services Use Message

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points that cross process boundaries.

### Rules

- **R-MQ-001** MUST: Services MUST use message queues as the primary integration mechanism for asynchronous operations that cross service boundaries.
- **R-MQ-002** MUST: All message queue client implementations MUST use established libraries that provide connection pooling, automatic reconnection, and retry logic.
- **R-MQ-003** MUST: Message handlers MUST implement idempotency patterns to safely handle message redelivery without causing data corruption or duplicate side effects.
- **R-MQ-004** MUST: All message consumers MUST implement dead-letter queue (DLQ) handling for failed message processing.
- **R-MQ-005** MUST: Message queue parameters MUST be externalized and configurable to support environment-specific deployment.
- **R-MQ-006** SHOULD: Implement structured logging with correlation IDs that flow through message headers to enable distributed tracing across service boundaries.
- **R-MQ-007** SHOULD: Configure appropriate message TTL (time-to-live) and queue depth limits to prevent unbounded queue growth during outages.
- **R-MQ-008** SHOULD: Establish monitoring dashboards tracking queue depth, message age, processing latency, and dead-letter queue metrics for each integration point.

### Verify

```bash
# Check for message queue integration code presence
grep -r "message.*queue\|queue.*client\|publish.*message\|consume.*message" --include="*.py" --include="*.ts" --include="*.js" api/ web/ | wc -l

# Check for idempotency patterns in message handlers
grep -r "idempotent\|idempotency" --include="*.py" --include="*.ts" api/ | wc -l

# Check for message queue configuration files
find . -name "*queue*config*" -o -name "*message*config*" | wc -l
```

**Accept when:**
- Message queue integration code is present in service files (human_input_service, rag_pipeline_task_proxy) with proper client initialization
- Idempotency patterns are documented or implemented in message handler code
- Configuration files exist for message queue parameters supporting environment-specific deployment
- Dead-letter queue implementation is present for all message consumers
- Correlation IDs are implemented in message headers for distributed tracing
- Message TTL and queue depth limits are configured
- Monitoring and alerting for queue metrics are established

<enforcement>
Claude Code MUST NOT skip or defer verification of message queue adoption for asynchronous inter-service communication. All R-MQ rules marked MUST are non-negotiable for production deployment. Violations must be flagged for architecture review.
</enforcement>