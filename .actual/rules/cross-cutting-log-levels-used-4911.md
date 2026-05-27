# Standardize Structured Logging with Contextual Metadata: Log Levels Used

These rules are ALWAYS ACTIVE for all Python backend services, API extensions, OpenTelemetry integration components, storage integration layers, and web application components that perform server-side logging.

### Rules

- **R-LOG-001** SHOULD: Log levels SHOULD be used appropriately: DEBUG for detailed diagnostic information, INFO for general operational events, WARN for potentially harmful situations, ERROR for error events that might still allow the application to continue running.

### Verify

```bash
# Count logging.getLogger usage in api/ directory
grep -r 'logging.getLogger' api/ --include='*.py' | wc -l

# Sample log statements to verify appropriate levels
grep -r 'logger\.(debug|info|warning|error)' api/ --include='*.py' | head -20

# Count Python modules using logging
find api/ -name '*.py' -exec grep -l 'import logging' {} \; | wc -l
```

**Accept when:**
- All Python modules in api/extensions/ use logging.getLogger() with appropriate module names
- Log statements include contextual metadata as structured key-value pairs or extra parameters
- No hardcoded print() statements exist in production code paths (excluding debug scripts)
- OpenTelemetry trace context is automatically propagated to log records in instrumented components
- Log levels (DEBUG, INFO, WARN, ERROR) are used consistently according to their defined purposes

<enforcement>
Claude Code MUST NOT skip or defer verification of logging patterns and log level appropriateness. Automated code review checks and CI/CD pipeline linting rules enforce compliance. Violations result in CI/CD pipeline failure for print statements in production code paths and code review feedback requiring contextual metadata additions.
</enforcement>