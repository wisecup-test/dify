# Enforce Input Validation Using Schema-Based Validation Libraries: Validation Error Messages

These rules are ALWAYS ACTIVE for all code that handles external input, including API endpoints, user interfaces, and data processing pipelines.

### Rules

- **R-VAL-001** SHOULD: Validation error messages SHOULD be user-friendly and specific enough to guide correction without exposing internal system details.

### Verify

```bash
# Check for validation schema definitions in TypeScript projects
grep -r "z\.object\|z\.string\|z\.number" packages/contracts/ --include="*.ts" | wc -l

# Check for validation decorators and Pydantic usage in Python projects
grep -r "@validate\|ValidationError\|pydantic" api/ --include="*.py" | wc -l

# Find validation-related files and generated contracts
find . -name "*validation*" -o -name "*zod.gen*" -o -name "*orpc.gen*" | head -20

# Run validation tests
npm test -- --testPathPattern=validation --passWithNoTests || echo 'No validation tests found'
```

**Accept when:**
- Validation schema definitions are found in at least 80% of API endpoint files
- All generated contract files (*.gen.ts, *.gen.py) are present and up to date in the repository
- Validation test coverage exists for critical API endpoints with both valid and invalid input test cases
- CI pipeline includes checks for schema generation and validation test execution
- Error messages are user-friendly and do not expose internal system details

<enforcement>
Claude Code MUST NOT skip or defer verification. Validation error messages must be reviewed during code review to ensure they are user-friendly and do not expose internal system details. Generated contracts must be verified as up to date before merge.
</enforcement>