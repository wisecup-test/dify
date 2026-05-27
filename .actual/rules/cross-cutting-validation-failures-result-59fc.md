# Enforce Input Validation Using Schema-Based Validation Libraries: Validation Failures Result

These rules are ALWAYS ACTIVE for all code that handles external input, including API endpoints, user interfaces, and data processing pipelines.

### Rules

- **R-VAL-001** MUST: Validation failures MUST result in explicit error responses with appropriate HTTP status codes (400 Bad Request) and MUST NOT proceed with processing invalid data.

### Verify

```bash
# Check for Zod schema definitions in TypeScript contracts
grep -r "z\.object\|z\.string\|z\.number" packages/contracts/ --include="*.ts" | wc -l

# Check for validation decorators and Pydantic in Python
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
Claude Code MUST NOT skip or defer verification of validation schema presence and error response handling in all external input handlers.
</enforcement>