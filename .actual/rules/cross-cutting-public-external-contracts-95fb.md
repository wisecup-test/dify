# Standardize Public API Contract Generation and Error Handling Patterns: Public External Contracts

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-PUB-001** MUST: All public/external API contracts MUST be generated from a single source of truth using code generation tools (e.g., OpenAPI, TypeScript generators).
- **R-PUB-002** MUST: All public API endpoints have corresponding generated contract files in packages/contracts/generated/.
- **R-PUB-003** MUST: All API error responses use structured error classes from api/services/errors/ with consistent fields.
- **R-PUB-004** MUST: OpenTelemetry decorators are present on all external API call sites.
- **R-PUB-005** MUST: Pre-commit hooks or CI checks regenerate contracts when schema files change and verify no manual edits to .gen.ts files.
- **R-PUB-006** SHOULD: Implement API versioning (e.g., /v1/, /v2/) and maintain backward compatibility for at least two major versions.
- **R-PUB-007** SHOULD: Configure OpenTelemetry decorators at the API route/controller level rather than individual functions to minimize boilerplate.
- **R-PUB-008** SHOULD: Use sampling strategies for high-volume endpoints to mitigate OpenTelemetry instrumentation overhead.

### Verify

```bash
# Count generated contract files
grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l

# Find Python error handling classes
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
- Pre-commit hooks regenerate contracts and fail if generated files differ from committed versions
- No manual modifications to .gen.ts or generated files are detected in version control

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. All public API implementations MUST comply with contract generation, error handling, and observability requirements before merge. CI pipeline MUST block merges for violations.
</enforcement>