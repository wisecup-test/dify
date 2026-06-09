# Standardize Structured Logging with Contextual Metadata: Log Statements Include

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-LOG-001** MUST: Log statements MUST include relevant contextual information such as request IDs, user identifiers, operation types, and resource identifiers where applicable.

### Verify

```bash
# Count logger instantiations in API modules
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Sample log statements to verify structured format
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count Python modules using logging
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l

# Check for print statements in production code paths
grep -r 'print(' api/ --include='*.py' | grep -v '__pycache__' | grep -v '.pyc'
```

**Accept when:**
- All Python modules in api/extensions/ use logging.getLogger() with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded print() statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated code review checks, CI/CD pipeline linting rules, and manual code review verification are mandatory before acceptance.
</enforcement>