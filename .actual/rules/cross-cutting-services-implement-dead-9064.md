# Adopt Message Queue Boundaries for Asynchronous Service Integration: Services Implement Dead

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points requiring temporal decoupling and independent scaling.

### Rules

- **R-MQ-001** SHOULD: Services SHOULD implement dead-letter queues for handling messages that fail processing after retry attempts.

### Verify

```bash
# Check for message queue and consumer patterns in service files
grep -r "message.*queue\|queue.*client\|publish.*message\|consume.*message" --include="*.py" --include="*.ts" --include="*.js" api/ web/ | wc -l

# Verify idempotency patterns are documented or implemented
grep -r "idempotent\|idempotency" --include="*.py" --include="*.ts" api/ | wc -l

# Check for message queue configuration files
find . -name "*queue*config*" -o -name "*message*config*" | wc -l
```

**Accept when:**
- Message queue integration code is present in service files (human_input_service, rag_pipeline_task_proxy) with proper client initialization
- Idempotency patterns are documented or implemented in message handler code
- Configuration files exist for message queue parameters supporting environment-specific deployment
- Dead-letter queue implementation is present for all asynchronous message consumers
- Monitoring and alerting are configured for queue depth and dead-letter queue metrics

<enforcement>
Claude Code MUST NOT skip or defer verification of message queue boundaries, dead-letter queue implementation, and idempotency patterns in asynchronous service integration points.
</enforcement>