# Enforce Input Validation Using Schema-Based Validation Libraries: External Input Requests

These rules are ALWAYS ACTIVE for all code that handles external input, including API endpoints, user interfaces, and data processing pipelines.

### Rules

- **R-EIR-001** MUST: All external input (API requests, form submissions, file uploads, query parameters) MUST be validated using a schema-based validation library before processing.

### Verify

```bash
# Check for Zod schema definitions in TypeScript contracts
grep -r "z\.object\|z\.string\|z\.number" packages/contracts/ --include="*.ts" | wc -l

# Check for Pydantic validation in Python API code
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

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated CI checks MUST verify generated validation contracts are up to date. Code review MUST block merge if new API endpoints lack validation schemas. Security scanning tools MUST flag endpoints without input validation.
</enforcement>