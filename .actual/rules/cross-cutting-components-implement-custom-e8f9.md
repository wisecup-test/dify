# Standardize Structured Logging with Contextual Metadata: Components Implement Custom

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-LOG-001** MUST: Use Python's standard logging library with structured formatters (e.g., python-json-logger) for all backend services; ensure logger names follow module hierarchy.
- **R-LOG-002** MUST: Integrate OpenTelemetry SDK's automatic context injection to include trace_id and span_id in all log records for correlation.
- **R-LOG-003** MUST: Include contextual metadata as structured key-value pairs or extra parameters in all log statements (e.g., request_id, user_id, tenant_id, operation_type).
- **R-LOG-004** MUST: Exclude hardcoded print() statements from all production code paths; print statements are only acceptable in debug scripts that are never committed.
- **R-LOG-005** SHOULD: Use TypeScript/JavaScript structured logging libraries compatible with the chosen backend format (e.g., winston, pino) for web application components.
- **R-LOG-006** SHOULD: Configure log levels per environment: DEBUG for development, INFO for staging, WARN/ERROR for production with ability to dynamically adjust for troubleshooting.
- **R-LOG-007** MAY: Components MAY implement custom log formatters or handlers to meet specific operational requirements while maintaining structured format compatibility.
- **R-LOG-008** MUST NOT: Log sensitive information (PII, credentials, tokens) without sanitization; implement automated scanning for sensitive patterns.

### Verify

```bash
# Count structured logging usage
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Verify log statements include contextual metadata
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count modules using logging
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l

# Detect print statements in production code
grep -r 'print(' api/ --include='*.py' | grep -v 'debug' | grep -v 'test'
```

**Accept when:**
- All Python modules in api/extensions/ use logging.getLogger() with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded print() statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components
- Log output in staging environments verifies structured format and appropriate content
- No sensitive data (PII, credentials, tokens) is exposed in log statements

<enforcement>
Claude Code MUST NOT skip or defer verification. All logging implementations MUST comply with R-LOG-001 through R-LOG-008. CI/CD pipeline MUST fail if print statements are detected in production code paths or if structured logging compliance cannot be verified.
</enforcement>