# Adopt Message Queue Boundaries for Asynchronous Service Integration: Message Queue Implementations

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points requiring temporal decoupling and independent scaling.

### Rules

- **R-MQ-001** MUST: Message queue implementations MUST provide at-least-once delivery guarantees to ensure message reliability.
- **R-MQ-002** MUST: All message queue integrations MUST implement idempotent message handlers to prevent data corruption or duplicate side effects when messages are redelivered.
- **R-MQ-003** MUST: All message consumers MUST implement dead-letter queue (DLQ) handling to prevent silent data loss from message processing failures.
- **R-MQ-004** SHOULD: Message queue client implementations SHOULD use established libraries that provide connection pooling, automatic reconnection, and retry logic.
- **R-MQ-005** SHOULD: Structured logging with correlation IDs SHOULD flow through message headers to enable distributed tracing across service boundaries.
- **R-MQ-006** SHOULD: Message TTL (time-to-live) and queue depth limits SHOULD be configured to prevent unbounded queue growth during outages.
- **R-MQ-007** SHOULD: Monitoring dashboards SHOULD track queue depth, message age, processing latency, and dead-letter queue metrics for each integration point.

### Verify

```bash
# Check for message queue integration code presence
grep -r "message.*queue\|queue.*client\|publish.*message\|consume.*message" --include="*.py" --include="*.ts" --include="*.js" api/ web/ | wc -l

# Check for idempotency patterns in code
grep -r "idempotent\|idempotency" --include="*.py" --include="*.ts" api/ | wc -l

# Check for message queue configuration files
find . -name "*queue*config*" -o -name "*message*config*" | wc -l
```

**Accept when:**
- Message queue integration code is present in service files (human_input_service, rag_pipeline_task_proxy) with proper client initialization
- Idempotency patterns are documented or implemented in message handler code
- Configuration files exist for message queue parameters supporting environment-specific deployment
- Dead-letter queue implementations are present in all message consumer code
- Monitoring and alerting configuration exists for queue depth and DLQ metrics

<enforcement>
Claude Code MUST NOT skip or defer verification of message queue implementations. All new service integration points introducing asynchronous communication MUST be verified against these rules. Pull requests introducing synchronous inter-service calls for asynchronous operations MUST be flagged for architecture review. Services without idempotency tests for message handlers MUST be blocked from production deployment.
</enforcement>