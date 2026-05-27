# Adopt Message Queue Boundaries for Asynchronous Service Integration: Services Implement Priority

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points requiring temporal decoupling and independent scaling.

### Rules

- **R-MQ-001** MAY: Services MAY implement priority queues when different message types require different processing urgency.

### Verify

```bash
# Check for message queue and publish/consume patterns in service files
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
- Services implement priority queue logic when different message types require different processing urgency
- Dead-letter queue implementation is present for all message consumers
- Structured logging with correlation IDs flows through message headers for distributed tracing

<enforcement>
Claude Code MUST NOT skip or defer verification. All message queue integration points MUST be verified for idempotency, configuration externalization, and priority queue implementation where applicable. Pull requests introducing synchronous inter-service calls for asynchronous operations MUST be flagged for architecture review.
</enforcement>