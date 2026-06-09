# Adopt Message Queue Boundaries for Asynchronous Service Integration: Services Implement Idempotent

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points that require independent scaling and temporal decoupling.

### Rules

- **R-MQ-001** MUST: Services MUST implement idempotent message handlers to safely handle duplicate message delivery.

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
- Message handlers include idempotency tests in the CI pipeline
- Dead-letter queue implementation is present for all message consumers

<enforcement>
Claude Code MUST NOT skip or defer verification of idempotent message handler implementation. All asynchronous inter-service communication MUST be validated against R-MQ-001 before approval.
</enforcement>