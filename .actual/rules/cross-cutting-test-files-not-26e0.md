# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Test Files Not

These rules are ALWAYS ACTIVE for all test file creation and organization activities across the codebase, including Python test files in the api/ directory and TypeScript/React test files in the web/ directory.

### Rules

- **R-TEST-001** MUST_NOT: Test files MUST NOT be mixed with production source code in the same directory without clear separation (e.g., __tests__ subdirectory).
- **R-TEST-002** MUST: All Python test files in api/tests/ MUST follow the test_*.py naming convention for pytest auto-discovery.
- **R-TEST-003** MUST: All TypeScript/React test files MUST be located in __tests__ directories and use *.spec.tsx or *.spec.ts naming.
- **R-TEST-004** MUST: Unit tests and integration tests MUST be clearly separated in distinct directory hierarchies.
- **R-TEST-005** SHOULD: Configure pytest.ini or pyproject.toml to explicitly define test discovery patterns: testpaths = ['api/tests'] and python_files = ['test_*.py'].
- **R-TEST-006** SHOULD: Configure Jest in package.json or jest.config.js with testMatch patterns: ['**/__tests__/**/*.spec.[jt]s?(x)'].
- **R-TEST-007** SHOULD: Document conventions in CONTRIBUTING.md or TESTING.md with examples for both Python and TypeScript test creation.

### Verify

```bash
# Verify no non-compliant Python test files exist
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
Claude Code MUST NOT skip or defer verification. All test files MUST comply with naming and directory structure rules before acceptance.
</enforcement>