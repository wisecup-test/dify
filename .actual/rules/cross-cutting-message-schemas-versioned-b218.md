# Adopt Message Queue Boundaries for Asynchronous Service Integration: Message Schemas Versioned

These rules are ALWAYS ACTIVE for all asynchronous inter-service communication patterns, long-running task coordination, event-driven workflows, and service integration points requiring temporal decoupling and independent scaling.

### Rules

- **R-MQ-001** SHOULD: Message schemas SHOULD be versioned to support backward-compatible evolution of service contracts.

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
- Message schemas include version information in their structure or metadata
- Services consuming messages validate schema versions and handle backward compatibility

<enforcement>
Claude Code MUST NOT skip or defer verification. All asynchronous service integrations MUST demonstrate message queue adoption with versioned schemas. Pull requests introducing synchronous inter-service calls for asynchronous operations MUST be flagged for architecture review. Services without idempotency tests for message handlers MUST be blocked from production deployment.
</enforcement>