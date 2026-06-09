# Standardize Public API Contract Generation and Error Handling Patterns: Developers Not Manually

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-API-001** MUST_NOT: Developers MUST NOT manually edit generated contract files; all changes MUST be made to the source schema and regenerated.
- **R-API-002** MUST: All public API endpoints have corresponding generated contract files in packages/contracts/generated/.
- **R-API-003** MUST: All API error responses use structured error classes from api/services/errors/ with consistent fields.
- **R-API-004** MUST: OpenTelemetry decorators are present on all external API call sites.
- **R-API-005** MUST: CI pipeline includes contract generation validation and rejects manual edits to generated files.
- **R-API-006** SHOULD: Set up pre-commit hooks or CI checks to regenerate contracts when schema files change and verify no manual edits to .gen.ts files.
- **R-API-007** SHOULD: Create base error classes (e.g., APIError, ValidationError) that all domain-specific error classes inherit from for consistency.
- **R-API-008** SHOULD: Configure OpenTelemetry decorators at the API route/controller level rather than individual functions to minimize boilerplate.
- **R-API-009** SHOULD: Document the contract generation workflow in developer onboarding materials with examples of adding new API endpoints.
- **R-API-010** SHOULD: Establish a contracts package (e.g., packages/contracts/) as the single source of truth for all generated API types.

### Verify

```bash
# Count generated contract files
grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l

# Find Python error handling classes
find api/services/errors/ -name '*.py' -exec grep -l 'class.*Error' {} \;

# Verify OpenTelemetry instrumentation
grep -r '@otel\|@trace\|OpenTelemetry' api/extensions/otel/ | head -5

# Check for manual edits to generated files
git diff --name-only | grep '\.gen\.' && echo 'ERROR: Generated files modified' || echo 'OK'
```

**Accept when:**
- All public API endpoints have corresponding generated contract files in packages/contracts/generated/
- All API error responses use structured error classes from api/services/errors/ with consistent fields
- OpenTelemetry decorators are present on all external API call sites as verified by grep patterns
- CI pipeline includes contract generation validation and rejects manual edits to generated files
- Pre-commit hooks or CI checks regenerate contracts when schema files change
- No manual modifications to .gen.ts or generated files are present in the commit

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. Manual edits to generated contract files MUST be rejected. All API endpoints MUST have corresponding generated contracts and structured error handling with OpenTelemetry instrumentation.
</enforcement>