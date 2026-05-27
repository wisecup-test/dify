# Enforce External Client Boundary Isolation in Internal APIs: External Client Interactions

These rules are ALWAYS ACTIVE for all internal API implementations that interact with external clients or services, including cloud storage services, third-party APIs, telemetry parsers, workflow engines, and feature services.

### Rules

- **R-EX-001** MUST: All external client interactions MUST be mockable or stubbable for unit testing purposes.
- **R-EX-002** MUST: No direct external client imports (boto3, requests, external SDKs) are permitted in internal service or core business logic modules outside designated adapter/extension directories.
- **R-EX-003** MUST: All storage, telemetry, and plugin extension modules MUST define abstract interfaces or protocols that internal APIs depend upon.
- **R-EX-004** MUST: Unit tests MUST demonstrate successful mocking of external clients without requiring actual external service connectivity.
- **R-EX-005** SHOULD: Use dependency injection frameworks to manage external client lifecycle and injection.
- **R-EX-006** SHOULD: Create interface definitions or abstract base classes for each external client type before implementing concrete adapters.
- **R-EX-007** SHOULD: Establish naming conventions for boundary components (e.g., *Client for interfaces, *Adapter for implementations, *Mock for test doubles).

### Verify

```bash
# Verify no direct external client imports in internal service/core modules
grep -r 'import boto3' api/services/ api/core/ --exclude-dir=tests | grep -v 'storage/' | wc -l | test $(cat) -eq 0

# Verify storage/telemetry/plugin modules define abstract interfaces
grep -r 'class.*Storage.*:' api/extensions/storage/ | grep -E '(ABC|Protocol|Interface)' | wc -l | test $(cat) -gt 0

# Verify unit tests include mocking patterns
pytest api/tests/unit_tests/ -k 'test_' --collect-only | grep -E '(mock|stub|fake)' | wc -l | test $(cat) -gt 5
```

**Accept when:**
- No direct external client imports (boto3, requests, external SDKs) found in internal service or core business logic modules outside designated adapter/extension directories
- All storage, telemetry, and plugin extension modules define abstract interfaces or protocols that internal APIs depend upon
- Unit tests demonstrate successful mocking of external clients without requiring actual external service connectivity

<enforcement>
Claude Code MUST NOT skip or defer verification. All three verify commands MUST pass before accepting changes that introduce or modify external client interactions.
</enforcement>