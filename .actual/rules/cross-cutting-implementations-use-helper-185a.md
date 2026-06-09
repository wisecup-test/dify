# Standardize Public API Contract Generation and Error Handling Patterns: Implementations Use Helper

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-API-001** MAY: API implementations MAY use helper utilities (e.g., use-interactions.helpers.ts) to encapsulate common interaction patterns.
- **R-API-002** MUST: All public API endpoints have corresponding generated contract files in packages/contracts/generated/.
- **R-API-003** MUST: All API error responses use structured error classes from api/services/errors/ with consistent fields.
- **R-API-004** MUST: OpenTelemetry decorators are present on all external API call sites.
- **R-API-005** MUST: Pre-commit hooks or CI checks regenerate contracts when schema files change and verify no manual edits to .gen.ts files.
- **R-API-006** MUST: Do not manually edit generated contract files (.gen.ts); regenerate from source schemas instead.
- **R-API-007** SHOULD: Configure OpenTelemetry decorators at the API route/controller level rather than individual functions to minimize boilerplate.
- **R-API-008** SHOULD: Create base error classes (e.g., APIError, ValidationError) that all domain-specific error classes inherit from for consistency.

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
- No .gen.ts or generated files show evidence of manual modification in git diff

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. All public API implementations MUST comply with contract generation, error handling, and observability patterns. CI pipeline checks MUST block merges for manual edits to generated files or missing contract generation.
</enforcement>