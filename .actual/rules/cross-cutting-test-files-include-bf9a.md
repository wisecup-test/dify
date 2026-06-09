# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Test Files Include

These rules are ALWAYS ACTIVE for all test file creation and organization activities across the codebase, including Python test files in api/tests/ and TypeScript/React test files in web/app/components/**/__tests__/.

### Rules

- **R-TEST-001** MAY: Test files MAY include descriptive suffixes beyond the module name to indicate specific test scenarios (e.g., test_parallel_human_input_join_resume.py).

### Verify

```bash
# Verify no Python test files exist outside standard naming convention
find api/tests -name '*.py' ! -name 'test_*.py' ! -name '__init__.py' ! -name 'conftest.py' -type f | grep -v '__pycache__' | wc -l | grep -q '^0$'

# Verify TypeScript test files exist in __tests__ directories with correct naming
find web/app -type d -name '__tests__' -exec find {} -name '*.spec.tsx' -o -name '*.spec.ts' \; | wc -l

# Verify pytest can discover unit and integration tests
pytest --collect-only api/tests/unit_tests/ 2>&1 | grep -q 'collected' && pytest --collect-only api/tests/test_containers_integration_tests/ 2>&1 | grep -q 'collected'
```

**Accept when:**
- All Python test files in api/tests/ follow the test_*.py naming convention and pytest can discover them automatically
- All TypeScript/React test files are located in __tests__ directories and use *.spec.tsx or *.spec.ts naming
- Unit tests and integration tests are clearly separated in distinct directory hierarchies
- Test discovery commands (pytest --collect-only, jest --listTests) successfully identify all test files without manual configuration

<enforcement>
Claude Code MUST NOT skip or defer verification of test file naming conventions and placement. All test files MUST conform to framework-specific patterns before acceptance.
</enforcement>