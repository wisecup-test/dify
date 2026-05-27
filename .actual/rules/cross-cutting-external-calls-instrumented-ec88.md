# Standardize Public API Contract Generation and Error Handling Patterns: External Calls Instrumented

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-EX-001** MUST: All external API calls MUST be instrumented with OpenTelemetry decorators or equivalent observability tooling for tracing and monitoring.
- **R-EX-002** MUST: All public API endpoints MUST have corresponding generated contract files in packages/contracts/generated/.
- **R-EX-003** MUST: All API error responses MUST use structured error classes from api/services/errors/ with consistent fields.
- **R-EX-004** MUST: Generated contract files (.gen.ts) MUST NOT be manually edited; regeneration must be automated via pre-commit hooks or CI.
- **R-EX-005** SHOULD: OpenTelemetry decorators SHOULD be configured at the API route/controller level rather than individual functions to minimize boilerplate.
- **R-EX-006** SHOULD: API versioning (e.g., /v1/, /v2/) SHOULD be implemented to maintain backward compatibility for at least two major versions.

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
- Pre-commit hooks regenerate contracts and fail if generated files differ from committed versions
- No manual modifications to .gen.ts or generated files are detected in version control

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. All external API implementations MUST be checked against R-EX-001 through R-EX-006 before approval. Violations block merge and require remediation or documented exception approval.
</enforcement>