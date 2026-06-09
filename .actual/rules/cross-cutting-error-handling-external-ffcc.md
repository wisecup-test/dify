# Enforce External Client Boundary Isolation in Internal APIs: Error Handling External

These rules are ALWAYS ACTIVE for all internal API implementations that interact with external clients or services, including cloud storage services, third-party APIs, telemetry parsers, workflow engines, and feature services.

### Rules

- **R-EX-001** SHOULD: Error handling for external client failures SHOULD be isolated at the boundary layer and translated to internal domain exceptions.

### Verify

```bash
# Verify no direct external client imports in internal service or core modules
grep -r 'import boto3' api/services/ api/core/ --exclude-dir=tests | grep -v 'storage/' | wc -l | test $(cat) -eq 0

# Verify storage and extension modules define abstract interfaces
grep -r 'class.*Storage.*:' api/extensions/storage/ | grep -E '(ABC|Protocol|Interface)' | wc -l | test $(cat) -gt 0

# Verify unit tests demonstrate mocking of external clients
pytest api/tests/unit_tests/ -k 'test_' --collect-only | grep -E '(mock|stub|fake)' | wc -l | test $(cat) -gt 5
```

**Accept when:**
- No direct external client imports (boto3, requests, external SDKs) found in internal service or core business logic modules outside designated adapter/extension directories
- All storage, telemetry, and plugin extension modules define abstract interfaces or protocols that internal APIs depend upon
- Unit tests demonstrate successful mocking of external clients without requiring actual external service connectivity

<enforcement>
Claude Code MUST NOT skip or defer verification. All three verify commands must pass before accepting changes to internal API error handling patterns.
</enforcement>