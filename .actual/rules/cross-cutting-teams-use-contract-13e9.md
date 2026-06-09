# Adopt Integration Testing Strategy for External Service Dependencies: Teams Use Contract

These rules are ALWAYS ACTIVE for all code that integrates with external services including AWS S3 storage, OpenTelemetry parsers, feature services, browser-based APIs, and third-party integrations.

### Rules

- **R-INT-001** MAY: Teams MAY use contract testing frameworks to validate API compatibility between services without requiring full end-to-end integration.

### Verify

```bash
# Check for integration test markers and dedicated test directories
grep -r '@integration\|@external\|pytest.mark.integration' tests/ --include='*.py' | wc -l
find tests/ -name '*integration*' -o -name '*external*' | wc -l

# Check for test mocks or test doubles for external services
grep -r 'mock.*aws\|mock.*s3\|mock.*otel' tests/ --include='*.py' --include='*.ts' --include='*.tsx' | wc -l
```

**Accept when:**
- Integration test markers or dedicated test directories are present in the codebase
- At least one integration test exists for each external service dependency (AWS S3, OpenTelemetry, feature service, voice input)
- Test mocks or test doubles are implemented for external services to enable fast CI execution
- CI/CD pipeline configuration shows separate execution of unit and integration test suites
- Integration tests cover error handling, retry logic, and real-world failure scenarios for external service interactions

<enforcement>
Clause Code MUST NOT skip or defer verification of integration test coverage for new external service integrations. Violations must be caught during code review and CI/CD pipeline checks before merge.
</enforcement>