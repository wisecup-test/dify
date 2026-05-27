# Standardize Public API Contract Generation and Error Handling Patterns: Public Implementations Include

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-API-001** SHOULD: Public API implementations SHOULD include comprehensive unit tests covering validation, error cases, and integration scenarios.

### Verify

```bash
# Count generated contract files
grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l

# Find Python error handling classes
find api/services/errors/ -name '*.py' -exec grep -l 'class.*Error' {} \;

# Verify OpenTelemetry instrumentation presence
grep -r '@otel\|@trace\|OpenTelemetry' api/extensions/otel/ | head -5

# Ensure generated files are not manually modified
git diff --name-only | grep '\.gen\.' && echo 'ERROR: Generated files modified' || echo 'OK'
```

**Accept when:**
- All public API endpoints have corresponding generated contract files in packages/contracts/generated/
- All API error responses use structured error classes from api/services/errors/ with consistent fields
- OpenTelemetry decorators are present on all external API call sites as verified by grep patterns
- CI pipeline includes contract generation validation and rejects manual edits to generated files
- Public API implementations include comprehensive unit tests covering validation, error cases, and integration scenarios

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. Pre-commit hooks and CI pipeline checks are mandatory to enforce contract generation consistency and prevent manual modifications to generated files. Code review must verify error handling patterns and OpenTelemetry instrumentation on all public API endpoints.
</enforcement>