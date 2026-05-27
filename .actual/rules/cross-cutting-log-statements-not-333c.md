# Standardize Structured Logging with Contextual Metadata: Log Statements Not

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-LOG-001** MUST NOT: Log statements MUST NOT include sensitive information such as passwords, API keys, tokens, or personally identifiable information (PII) without proper redaction.
- **R-LOG-002** MUST: All Python modules in api/extensions/ use `logging.getLogger()` with appropriate module names following the module hierarchy.
- **R-LOG-003** MUST: Log statements include contextual metadata as structured key-value pairs or extra parameters (e.g., request_id, user_id, tenant_id, operation_type).
- **R-LOG-004** MUST NOT: No hardcoded `print()` statements exist in production code paths (excluding debug scripts).
- **R-LOG-005** MUST: OpenTelemetry trace context (trace_id and span_id) is automatically propagated to log records in instrumented components.
- **R-LOG-006** SHOULD: Use Python's standard logging library with structured formatters (e.g., python-json-logger) for backend services.
- **R-LOG-007** SHOULD: For TypeScript/JavaScript components, use structured logging libraries compatible with the chosen backend format (e.g., winston, pino).
- **R-LOG-008** SHOULD: Configure log levels per environment: DEBUG for development, INFO for staging, WARN/ERROR for production with ability to dynamically adjust for troubleshooting.

### Verify

```bash
# Count structured logging usage
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Verify log statements include contextual metadata
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count modules using logging
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l

# Detect print statements in production code
grep -r 'print(' api/ --include='*.py' | grep -v 'debug' | grep -v '#'

# Verify no sensitive patterns in log statements
grep -r 'logger\.' api/ --include='*.py' | grep -E '(password|api_key|token|secret|credential)' | grep -v 'redact\|sanitize\|mask'
```

**Accept when:**
- All Python modules in api/extensions/ use `logging.getLogger()` with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded `print()` statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components
- No sensitive information (passwords, API keys, tokens, PII) is logged without proper redaction
- Structured logging libraries are consistently used across Python and TypeScript/JavaScript components

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI/CD pipeline enforcement. Violations must be addressed before merge, or documented as approved exceptions with technical lead sign-off.
</enforcement>