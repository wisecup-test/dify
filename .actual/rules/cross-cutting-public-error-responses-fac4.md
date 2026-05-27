# Standardize Public API Contract Generation and Error Handling Patterns: Public Error Responses

These rules are ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code across REST APIs exposed to external consumers, internal service-to-service APIs crossing bounded contexts, generated TypeScript contract files, Python error handling classes, and OpenTelemetry instrumentation for API calls.

### Rules

- **R-PUB-ERR-001** MUST: Public API error responses MUST use structured error classes with consistent fields including error code, message, and context.

### Verify

```bash
# Verify generated contract files exist
grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l

# Verify error classes are defined
find api/services/errors/ -name '*.py' -exec grep -l 'class.*Error' {} \;

# Verify OpenTelemetry instrumentation is present
grep -r '@otel\|@trace\|OpenTelemetry' api/extensions/otel/ | head -5

# Verify no manual edits to generated files
git diff --name-only | grep '\.gen\.' && echo 'ERROR: Generated files modified' || echo 'OK'
```

**Accept when:**
- All public API endpoints have corresponding generated contract files in packages/contracts/generated/
- All API error responses use structured error classes from api/services/errors/ with consistent fields (error code, message, context)
- OpenTelemetry decorators are present on all external API call sites
- CI pipeline includes contract generation validation and rejects manual edits to generated files
- No manual modifications to .gen.ts or generated files are detected in version control

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. All public API error responses must be validated against the structured error class requirement before approval.
</enforcement>