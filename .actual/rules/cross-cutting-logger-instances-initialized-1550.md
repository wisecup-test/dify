# Standardize Structured Logging with Contextual Metadata: Logger Instances Initialized

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-LOG-001** SHOULD: Logger instances SHOULD be initialized at module level with appropriate namespace identifiers to facilitate log filtering and routing.

### Verify

```bash
# Count logger initialization patterns in API extensions
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Sample log statements to verify structured format
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count modules with logging imports
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l
```

**Accept when:**
- All Python modules in api/extensions/ use logging.getLogger() with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded print() statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated code review checks, CI/CD pipeline linting rules, and manual code review verification are mandatory before acceptance.
</enforcement>