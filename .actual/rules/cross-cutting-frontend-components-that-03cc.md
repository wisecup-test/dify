# Enforce Input Validation Using Schema-Based Validation Libraries: Frontend Components That

These rules are ALWAYS ACTIVE for all frontend components that accept user input, including form components, input fields, and any UI elements that handle external data.

### Rules

- **R-FE-VAL-001** SHOULD: Frontend components that accept user input SHOULD use validation wrappers (e.g., with-input-validation HOC) to provide immediate feedback.

### Verify

```bash
# Check for validation wrapper usage in frontend components
grep -r "with-input-validation\|ValidationWrapper\|useValidation" src/ --include="*.tsx" --include="*.ts" | wc -l

# Verify Zod schema definitions exist in contracts
grep -r "z\.object\|z\.string\|z\.number" packages/contracts/ --include="*.ts" | wc -l

# Find validation-related files
find . -name "*validation*" -o -name "*zod.gen*" -o -name "*orpc.gen*" | head -20

# Check for validation test coverage
npm test -- --testPathPattern=validation --passWithNoTests || echo 'No validation tests found'
```

**Accept when:**
- Validation wrapper usage is found in at least 80% of form component files
- Zod schema definitions are present in the contracts package
- Generated contract files (*.gen.ts) are present and up to date
- Validation test coverage exists for critical form components with both valid and invalid input test cases
- CI pipeline includes checks for schema generation and validation test execution

<enforcement>
Claude Code MUST NOT skip or defer verification of validation wrapper adoption in frontend components. All new form components MUST be reviewed for proper validation wrapper implementation before merge.
</enforcement>