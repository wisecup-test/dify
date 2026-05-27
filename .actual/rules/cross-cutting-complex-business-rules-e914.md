# Encode Business Rules as Explicit Validation Logic in Domain Services: Complex Business Rules

These rules are ALWAYS ACTIVE for all domain service implementations that handle business logic and validation.

### Rules

- **R-DOMAIN-001** SHOULD: Complex business rules SHOULD be extracted into dedicated validator classes or functions for reusability and testability.

### Verify

```bash
# Count validation methods in service files
grep -r "def _validate_\|def _check_" api/services/ --include="*_service.py" | wc -l

# Count exception raising patterns in service files
grep -r "raise.*Exception\|raise.*Error" api/services/ --include="*_service.py" | grep -v "^\s*#" | wc -l

# Run validation-focused unit tests
python -m pytest tests/unit/services/ -k validation -v
```

**Accept when:**
- Service files contain explicit validation methods (prefixed with `_validate_` or `_check_`) that are called before domain operations
- Business rule violations raise domain-specific exceptions with descriptive error messages
- Unit tests exist for validation logic covering both valid and invalid cases for each business rule

<enforcement>
Clause Code MUST NOT skip or defer verification. Service layer validation is mandatory for all domain operations handling business constraints.
</enforcement>