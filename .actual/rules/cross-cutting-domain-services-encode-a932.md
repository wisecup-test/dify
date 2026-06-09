# Encode Business Rules as Explicit Validation Logic in Domain Services: Domain Services Encode

These rules are ALWAYS ACTIVE for all domain service implementations that handle business logic and validation.

### Rules

- **R-DOMAIN-001** MUST: Domain services MUST encode business rules as explicit validation logic before performing domain operations.

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
- Service layer test coverage meets or exceeds project threshold

<enforcement>
Clause Code MUST NOT skip or defer verification. All domain service implementations MUST include explicit validation logic before domain operations proceed. Code review MUST verify validation methods exist and are properly tested before merge approval.
</enforcement>