# Enforce Input Validation Using Schema-Based Validation Libraries: Teams Implement Custom

These rules are ALWAYS ACTIVE for all code that handles external input, including API endpoints, user interfaces, and data processing pipelines.

### Rules

- **R-VAL-001** MAY: Teams MAY implement custom validation rules beyond standard type checking when domain-specific constraints are required.

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
Claude Code MUST NOT skip or defer verification. Validation schemas must be present for all API endpoints receiving external requests, GraphQL resolvers, form components, file upload handlers, WebSocket handlers, CLI parsers, configuration loaders, and inter-service API calls crossing trust boundaries.
</enforcement>