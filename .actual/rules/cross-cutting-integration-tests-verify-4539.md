# Adopt Integration Testing Strategy for External Service Dependencies: Integration Tests Verify

These rules are ALWAYS ACTIVE for all code that integrates with external services including AWS S3 storage, OpenTelemetry parsers, feature services, browser-based APIs, and third-party integrations.

### Rules

- **R-INT-001** MUST: Integration tests MUST verify error handling and retry logic for external service failures, timeouts, and degraded performance scenarios.

### Verify

```bash
# Check for integration test markers or dedicated test directories
grep -r '@integration\|@external\|pytest.mark.integration' tests/ --include='*.py' | wc -l

# Find integration test files by naming convention
find tests/ -name '*integration*' -o -name '*external*' | wc -l

# Check for test mocks or doubles for external services
grep -r 'mock.*aws\|mock.*s3\|mock.*otel' tests/ --include='*.py' --include='*.ts' --include='*.tsx' | wc -l
```

**Accept when:**
- Integration test markers or dedicated test directories are present in the codebase
- At least one integration test exists for each external service dependency (AWS S3, OpenTelemetry, feature service, voice input)
- Test mocks or test doubles are implemented for external services to enable fast CI execution
- CI/CD pipeline configuration shows separate execution of unit and integration test suites

<enforcement>
Claude Code MUST NOT skip or defer verification of integration test coverage for external service dependencies. All new external service integrations require corresponding integration tests or approved exceptions before merge.
</enforcement>