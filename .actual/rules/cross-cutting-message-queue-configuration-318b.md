# Adopt Message Queue Boundaries for Asynchronous Service Integration: Message Queue Configuration

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points requiring temporal decoupling and independent scaling.

### Rules

- **R-MQ-001** SHOULD: Message queue configuration parameters SHOULD be externalized to support different deployment environments.
- **R-MQ-002** MUST: All message queue integrations MUST use established client libraries that provide connection pooling, automatic reconnection, and retry logic.
- **R-MQ-003** MUST: Message handlers MUST implement idempotency patterns to prevent data corruption or duplicate side effects when messages are redelivered.
- **R-MQ-004** MUST: All message consumers MUST implement dead-letter queue handling to prevent silent data loss.
- **R-MQ-005** SHOULD: Structured logging with correlation IDs SHOULD flow through message headers to enable distributed tracing across service boundaries.
- **R-MQ-006** SHOULD: Message TTL (time-to-live) and queue depth limits SHOULD be configured to prevent unbounded queue growth during outages.
- **R-MQ-007** SHOULD: Monitoring dashboards SHOULD track queue depth, message age, processing latency, and dead-letter queue metrics for each integration point.

### Verify

```bash
# Check for message queue and publish/consume patterns
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
- Correlation IDs are propagated through message headers for distributed tracing
- Message TTL and queue depth limits are configured in deployment specifications
- Monitoring and alerting are configured for queue depth, message age, and DLQ metrics

<enforcement>
Claude Code MUST NOT skip or defer verification. All message queue integrations MUST satisfy idempotency requirements and dead-letter queue handling before production deployment. Pull requests introducing synchronous inter-service calls for asynchronous operations MUST be flagged for architecture review. Services without idempotency tests for message handlers MUST be blocked from production deployment.
</enforcement>