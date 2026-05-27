# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Test File Names

These rules are ALWAYS ACTIVE for all test file creation and organization activities across the codebase, including all Python test files in the api/ directory and all TypeScript/React test files in the web/ directory.

### Rules

- **R-TEST-001** SHOULD: Test file names SHOULD clearly indicate the module or component being tested (e.g., test_agent_node.py for agent_node.py, index.spec.tsx for index.tsx).

### Verify

```bash
# Verify no Python test files exist outside test_*.py naming convention
find api/tests -name '*.py' ! -name 'test_*.py' ! -name '__init__.py' ! -name 'conftest.py' -type f | grep -v '__pycache__' | wc -l | grep -q '^0$'

# Verify TypeScript/React test files exist in __tests__ directories with correct naming
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
Claude Code MUST NOT skip or defer verification. All test files must comply with framework-specific naming conventions and directory structure before acceptance.
</enforcement>