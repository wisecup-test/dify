# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Python Unit Tests

These rules are ALWAYS ACTIVE for all Python test file creation and organization activities across the codebase.

### Rules

- **R-PYTEST-001** MUST: Python unit tests MUST be organized under `api/tests/unit_tests/` with directory structure mirroring the source code hierarchy.
- **R-PYTEST-002** MUST: All Python test files MUST follow the `test_*.py` naming convention for pytest auto-discovery.
- **R-PYTEST-003** MUST: Integration tests MUST be organized under `api/tests/integration_tests/` separate from unit tests.
- **R-PYTEST-004** SHOULD: Configure `pytest.ini` or `pyproject.toml` to explicitly define test discovery patterns: `testpaths = ['api/tests']` and `python_files = ['test_*.py']`.
- **R-PYTEST-005** SHOULD: Create project templates or scaffolding scripts that automatically generate test files in the correct location with proper naming.
- **R-PYTEST-006** SHOULD: Document conventions in `CONTRIBUTING.md` or `TESTING.md` with examples for Python test creation.

### Verify

```bash
# Verify no Python files in api/tests/ violate naming convention (excluding __init__.py and conftest.py)
find api/tests -name '*.py' ! -name 'test_*.py' ! -name '__init__.py' ! -name 'conftest.py' -type f | grep -v '__pycache__' | wc -l | grep -q '^0$'

# Verify pytest can discover unit tests
pytest --collect-only api/tests/unit_tests/ 2>&1 | grep -q 'collected'

# Verify pytest can discover integration tests
pytest --collect-only api/tests/integration_tests/ 2>&1 | grep -q 'collected'
```

**Accept when:**
- All Python test files in `api/tests/` follow the `test_*.py` naming convention
- pytest can discover all test files automatically without manual configuration
- Unit tests and integration tests are clearly separated in distinct directory hierarchies (`api/tests/unit_tests/` and `api/tests/integration_tests/`)
- Directory structure under `api/tests/unit_tests/` mirrors the source code hierarchy
- Test discovery commands (`pytest --collect-only`) successfully identify all test files

<enforcement>
Claude Code MUST NOT skip or defer verification of Python test file naming and organization compliance.
</enforcement>