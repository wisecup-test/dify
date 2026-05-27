# Standardize Structured Logging with Contextual Metadata: Logging Implementations Compatible

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-LOG-001** MUST: Logging implementations MUST be compatible with OpenTelemetry trace context propagation to enable correlation between logs and traces.
- **R-LOG-002** MUST: All Python modules in api/extensions/ use logging.getLogger() with appropriate module names.
- **R-LOG-003** MUST: Log statements include contextual metadata as structured key-value pairs or extra parameters.
- **R-LOG-004** MUST: No hardcoded print() statements exist in production code paths (excluding debug scripts).
- **R-LOG-005** MUST: OpenTelemetry trace context is automatically propagated to log records in instrumented components.
- **R-LOG-006** SHOULD: Use Python's standard logging library with structured formatters (e.g., python-json-logger) for backend services.
- **R-LOG-007** SHOULD: Establish common contextual fields across the organization (e.g., request_id, user_id, tenant_id, operation_type).
- **R-LOG-008** SHOULD: Configure log levels per environment: DEBUG for development, INFO for staging, WARN/ERROR for production.

### Verify

```bash
# Count logging.getLogger usage in api/
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Sample log statements to verify structured format
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count Python modules importing logging
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l

# Detect print statements in production code paths
grep -r 'print(' api/ --include='*.py' | grep -v '__pycache__' | grep -v '.pyc'

# Verify OpenTelemetry integration in instrumented components
grep -r 'opentelemetry\|trace_id\|span_id' api/ --include='*.py' | head -10
```

**Accept when:**
- All Python modules in api/extensions/ use logging.getLogger() with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded print() statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components
- Logging output in staging environments demonstrates structured format with consistent key-value pairs
- No sensitive data (PII, credentials, tokens) is exposed in log statements

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules marked MUST are mandatory and must be verified before accepting code changes. Violations detected by CI/CD pipeline linting or manual code review require remediation or documented exception approval.
</enforcement>