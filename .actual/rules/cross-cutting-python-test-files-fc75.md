# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Python Test Files

These rules are ALWAYS ACTIVE for all Python test file creation and organization activities across the codebase, particularly within the api/tests/ directory structure.

### Rules

- **R-PYTEST-001** MUST: Python test files MUST use the test_*.py naming convention and be placed in dedicated test directories under api/tests/
- **R-PYTEST-002** MUST: All Python test files in the api/ directory MUST follow the test_*.py naming pattern for pytest auto-discovery
- **R-PYTEST-003** SHOULD: Organize tests hierarchically by test type using dedicated subdirectories (unit_tests/, integration_tests/) under api/tests/
- **R-PYTEST-004** SHOULD: Configure pytest.ini or pyproject.toml to explicitly define test discovery patterns: testpaths = ['api/tests'] and python_files = ['test_*.py']
- **R-PYTEST-005** MAY: Create project templates or scaffolding scripts that automatically generate test files in the correct location with proper naming

### Verify

```bash
# Verify no non-compliant Python test files exist in api/tests/
find api/tests -name '*.py' ! -name 'test_*.py' ! -name '__init__.py' ! -name 'conftest.py' -type f | grep -v '__pycache__' | wc -l | grep -q '^0$'

# Verify pytest can discover tests in unit_tests directory
pytest --collect-only api/tests/unit_tests/ 2>&1 | grep -q 'collected'

# Verify pytest can discover tests in integration_tests directory
pytest --collect-only api/tests/test_containers_integration_tests/ 2>&1 | grep -q 'collected'
```

**Accept when:**
- All Python test files in api/tests/ follow the test_*.py naming convention
- pytest can discover all test files automatically without manual configuration
- Unit tests and integration tests are clearly separated in distinct directory hierarchies
- No Python test files exist outside api/tests/ with non-standard naming
- pytest --collect-only successfully identifies all test files in both unit and integration test directories

<enforcement>
Claude Code MUST NOT skip or defer verification of Python test file naming and placement compliance. All verification commands MUST execute successfully before accepting changes to test file organization.
</enforcement>