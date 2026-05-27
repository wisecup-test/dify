# Adopt Integration Testing Strategy for External Service Dependencies: Integration Tests Isolated

These rules are ALWAYS ACTIVE for all code that integrates with external services including AWS S3 storage, OpenTelemetry parsers, feature services, browser-based APIs, and third-party integrations requiring network calls.

### Rules

- **R-INT-001** MUST: Integration tests MUST be isolated from unit tests and clearly marked to enable selective execution in CI/CD pipelines.
- **R-INT-002** MUST: Use test markers or tags (e.g., `@integration`, `@external`, `pytest.mark.integration`) to clearly distinguish integration tests from unit tests in test runners.
- **R-INT-003** MUST: Create dedicated test fixtures and factories for external service test data that can be reused across integration tests.
- **R-INT-004** MUST: Implement test helpers for common integration patterns like service authentication, connection setup, and cleanup operations.
- **R-INT-005** MUST: Configure CI/CD pipelines with separate stages: fast unit tests on every commit, integration tests with mocks on PR validation, full integration tests with real services on merge to main.
- **R-INT-006** SHOULD: Document integration test setup requirements including environment variables, service credentials, and network access in README files.
- **R-INT-007** SHOULD: Consider using testcontainers or similar tools for services that can be containerized to provide consistent test environments.
- **R-INT-008** SHOULD: Implement retry logic, circuit breakers, and health checks in test infrastructure to mitigate flakiness from external service instability.

### Verify

```bash
# Count integration test markers in Python files
grep -r '@integration\|@external\|pytest.mark.integration' tests/ --include='*.py' | wc -l

# Find dedicated integration test directories or files
find tests/ -name '*integration*' -o -name '*external*' | wc -l

# Count mock implementations for external services
grep -r 'mock.*aws\|mock.*s3\|mock.*otel' tests/ --include='*.py' --include='*.ts' --include='*.tsx' | wc -l
```

**Accept when:**
- Integration test markers or dedicated test directories are present in the codebase
- At least one integration test exists for each external service dependency (AWS S3, OpenTelemetry, feature service, voice input)
- Test mocks or test doubles are implemented for external services to enable fast CI execution
- CI/CD pipeline configuration shows separate execution of unit and integration test suites
- New external service integrations include corresponding integration tests or approved exceptions

<enforcement>
Claude Code MUST NOT skip or defer verification of integration test isolation and marking requirements. All new external service integrations MUST include integration tests or documented exceptions approved by tech lead. Code review MUST block merging of external service changes without integration test coverage.
</enforcement>