# Standardize Structured Logging with Contextual Metadata: Error Exception Logging

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-EX-001** SHOULD: Error and exception logging SHOULD include stack traces and relevant error context to support root cause analysis.

### Verify

```bash
# Count logger usage in API modules
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Sample log statements to verify structured format
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count Python modules using logging
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l
```

**Accept when:**
- All Python modules in api/extensions/ use logging.getLogger() with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded print() statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components
- Error and exception log statements include stack traces via exc_info=True or equivalent
- Exception handlers capture and log relevant error context (error type, message, affected resource identifiers)

<enforcement>
Claude Code MUST NOT skip or defer verification. All logging statements in error/exception paths MUST be reviewed for inclusion of stack traces and contextual metadata before code acceptance.
</enforcement>