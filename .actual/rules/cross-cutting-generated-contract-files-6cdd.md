# Standardize Public API Contract Generation and Error Handling Patterns: Generated Contract Files

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-API-GEN-001** MUST: Generated contract files MUST follow the naming convention `*.gen.ts` for TypeScript or equivalent markers for other languages to clearly identify auto-generated code.
- **R-API-GEN-002** MUST: All public API endpoints MUST have corresponding generated contract files in `packages/contracts/generated/`.
- **R-API-GEN-003** MUST: All API error responses MUST use structured error classes from `api/services/errors/` with consistent fields.
- **R-API-GEN-004** MUST: OpenTelemetry decorators MUST be present on all external API call sites.
- **R-API-GEN-005** MUST: Pre-commit hooks or CI checks MUST regenerate contracts when schema files change and verify no manual edits to `.gen.ts` files.
- **R-API-GEN-006** MUST: CI pipeline MUST include contract generation validation and reject manual edits to generated files.
- **R-API-GEN-007** MUST: API versioning (e.g., `/v1/`, `/v2/`) MUST be implemented to maintain backward compatibility for at least two major versions.
- **R-API-GEN-008** SHOULD: Base error classes (e.g., `APIError`, `ValidationError`) SHOULD be created with all domain-specific error classes inheriting from them for consistency.
- **R-API-GEN-009** SHOULD: OpenTelemetry decorators SHOULD be configured at the API route/controller level rather than individual functions to minimize boilerplate.
- **R-API-GEN-010** SHOULD: Generated files SHOULD be committed to version control as fallback for code generation tooling failures.

### Verify

```bash
# Count generated contract files
grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l

# Verify error handling classes exist
find api/services/errors/ -name '*.py' -exec grep -l 'class.*Error' {} \;

# Verify OpenTelemetry instrumentation
grep -r '@otel\|@trace\|OpenTelemetry' api/extensions/otel/ | head -5

# Verify no manual edits to generated files
git diff --name-only | grep '\.gen\.' && echo 'ERROR: Generated files modified' || echo 'OK'
```

**Accept when:**
- All public API endpoints have corresponding generated contract files in `packages/contracts/generated/`
- All API error responses use structured error classes from `api/services/errors/` with consistent fields
- OpenTelemetry decorators are present on all external API call sites as verified by grep patterns
- CI pipeline includes contract generation validation and rejects manual edits to generated files
- No manual modifications to `.gen.ts` or generated files are detected in version control
- API versioning strategy is documented and implemented for all public APIs
- Base error classes are defined and inherited by all domain-specific error classes

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI pipeline enforcement. Manual edits to generated files MUST be rejected. Missing contract generation or observability instrumentation MUST block merge.
</enforcement>