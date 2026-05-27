# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Python Integration Tests

These rules are ALWAYS ACTIVE for all Python test file creation and organization activities across the codebase, particularly for integration tests in the api/tests/ directory.

### Rules

- **R-PYTEST-001** MUST: Python integration tests MUST be organized under api/tests/test_containers_integration_tests/ or similar integration-specific directories.
- **R-PYTEST-002** MUST: All Python test files in api/tests/ MUST follow the test_*.py naming convention for pytest auto-discovery.
- **R-PYTEST-003** MUST: Unit tests and integration tests MUST be clearly separated in distinct directory hierarchies (unit_tests/ and integration_tests/ subdirectories).
- **R-PYTEST-004** SHOULD: Configure pytest.ini or pyproject.toml to explicitly define test discovery patterns with testpaths = ['api/tests'] and python_files = ['test_*.py'].
- **R-PYTEST-005** SHOULD: Create project templates or scaffolding scripts that automatically generate test files in the correct location with proper naming.

### Verify

```bash
# Verify no non-compliant Python test files exist in api/tests/
find api/tests -name '*.py' ! -name 'test_*.py' ! -name '__init__.py' ! -name 'conftest.py' -type f | grep -v '__pycache__' | wc -l | grep -q '^0$'

# Verify pytest can discover unit tests
pytest --collect-only api/tests/unit_tests/ 2>&1 | grep -q 'collected'

# Verify pytest can discover integration tests
pytest --collect-only api/tests/test_containers_integration_tests/ 2>&1 | grep -q 'collected'
```

**Accept when:**
- All Python test files in api/tests/ follow the test_*.py naming convention
- pytest can discover all test files automatically without manual configuration
- Unit tests and integration tests are clearly separated in distinct directory hierarchies
- No Python test files exist outside standard directories or with incorrect naming
- Test discovery commands (pytest --collect-only) successfully identify all test files

<enforcement>
Claude Code MUST NOT skip or defer verification. All three verify commands MUST pass before accepting changes to Python test file organization.
</enforcement>