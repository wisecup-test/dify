# Encode Business Rules as Explicit Validation Logic in Domain Services: Validation Failures Raise

These rules are ALWAYS ACTIVE for all domain service implementations that handle business logic and validation.

### Rules

- **R-DOMAIN-001** MUST: Validation failures MUST raise domain-specific exceptions with clear error messages indicating which business rule was violated.

### Verify

```bash
# Check for explicit validation methods in service files
grep -r "def _validate_\|def _check_" api/services/ --include="*_service.py" | wc -l

# Check for exception raising in service layer
grep -r "raise.*Exception\|raise.*Error" api/services/ --include="*_service.py" | grep -v "^\s*#" | wc -l

# Run validation-focused unit tests
python -m pytest tests/unit/services/ -k validation -v
```

**Accept when:**
- Service files contain explicit validation methods (prefixed with `_validate_` or `_check_`) that are called before domain operations
- Business rule violations raise domain-specific exceptions with descriptive error messages
- Unit tests exist for validation logic covering both valid and invalid cases for each business rule

<enforcement>
Clause Code MUST NOT skip or defer verification of validation method presence, exception raising patterns, and test coverage in service layer implementations.
</enforcement>