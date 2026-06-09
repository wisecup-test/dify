# Standardize Public API Contract Generation and Error Handling Patterns: Error Classes Organized

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-API-001** SHOULD: API error classes SHOULD be organized by domain (e.g., app_model_config, plugin) to maintain clear separation of concerns.

### Verify

```bash
# Count generated contract files
grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l

# Find Python error classes
find api/services/errors/ -name '*.py' -exec grep -l 'class.*Error' {} \;

# Verify OpenTelemetry instrumentation
grep -r '@otel\|@trace\|OpenTelemetry' api/extensions/otel/ | head -5

# Ensure generated files are not manually modified
git diff --name-only | grep '\.gen\.' && echo 'ERROR: Generated files modified' || echo 'OK'
```

**Accept when:**
- All public API endpoints have corresponding generated contract files in packages/contracts/generated/
- All API error responses use structured error classes from api/services/errors/ with consistent fields
- OpenTelemetry decorators are present on all external API call sites as verified by grep patterns
- CI pipeline includes contract generation validation and rejects manual edits to generated files
- Error classes are organized by domain with clear separation of concerns

<enforcement>
Claude Code MUST NOT skip or defer verification. Pre-commit hooks and CI pipeline checks MUST validate contract generation consistency and reject manual edits to .gen.ts files. Code review MUST verify error handling patterns and OpenTelemetry instrumentation presence. Violations block merge and require remediation.
</enforcement>