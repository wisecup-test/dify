# Enforce External Client Boundary Isolation in Internal APIs: Internal Define Explicit

These rules are ALWAYS ACTIVE for all internal API implementations that interact with external clients or services, including storage extensions, feature services, telemetry parsers, workflow engines, and agent nodes.

### Rules

- **R-EX-001** MUST: Internal APIs MUST define explicit boundary interfaces or abstractions when interacting with external clients (cloud services, third-party APIs, external storage).

### Verify

```bash
# Check for direct external client imports in internal service/core modules (outside designated adapter/extension directories)
grep -r 'import boto3' api/services/ api/core/ --exclude-dir=tests | grep -v 'storage/' | wc -l | test $(cat) -eq 0

# Verify all storage, telemetry, and plugin extension modules define abstract interfaces or protocols
grep -r 'class.*Storage.*:' api/extensions/storage/ | grep -E '(ABC|Protocol|Interface)' | wc -l | test $(cat) -gt 0

# Verify unit tests demonstrate successful mocking of external clients
pytest api/tests/unit_tests/ -k 'test_' --collect-only | grep -E '(mock|stub|fake)' | wc -l | test $(cat) -gt 5
```

**Accept when:**
- No direct external client imports (boto3, requests, external SDKs) found in internal service or core business logic modules outside designated adapter/extension directories
- All storage, telemetry, and plugin extension modules define abstract interfaces or protocols that internal APIs depend upon
- Unit tests demonstrate successful mocking of external clients without requiring actual external service connectivity

<enforcement>
Claude Code MUST NOT skip or defer verification. All three verify commands must pass before accepting changes that introduce or modify external client interactions in internal APIs.
</enforcement>