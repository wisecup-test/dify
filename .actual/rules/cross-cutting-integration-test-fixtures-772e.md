# Adopt Integration Testing Strategy for External Service Dependencies: Integration Test Fixtures

These rules are ALWAYS ACTIVE for all code that integrates with external services including AWS S3 storage, OpenTelemetry parsers, feature services, browser-based APIs, and third-party integrations across backend (Python) and frontend (TypeScript/React) components.

### Rules

- **R-INTTEST-001** SHOULD: Integration test fixtures SHOULD provide realistic test data that mirrors production usage patterns for storage operations, telemetry events, and feature configurations.

### Verify

```bash
# Check for integration test markers and dedicated test directories
grep -r '@integration\|@external\|pytest.mark.integration' tests/ --include='*.py' | wc -l
find tests/ -name '*integration*' -o -name '*external*' | wc -l

# Verify test mocks or test doubles for external services
grep -r 'mock.*aws\|mock.*s3\|mock.*otel' tests/ --include='*.py' --include='*.ts' --include='*.tsx' | wc -l
```

**Accept when:**
- Integration test markers or dedicated test directories are present in the codebase
- At least one integration test exists for each external service dependency (AWS S3, OpenTelemetry, feature service, voice input)
- Test mocks or test doubles are implemented for external services to enable fast CI execution
- CI/CD pipeline configuration shows separate execution of unit and integration test suites

<enforcement>
Claude Code MUST NOT skip or defer verification of integration test coverage for new external service integrations. Code review MUST block merging until integration test requirements are satisfied or an approved exception is documented.
</enforcement>