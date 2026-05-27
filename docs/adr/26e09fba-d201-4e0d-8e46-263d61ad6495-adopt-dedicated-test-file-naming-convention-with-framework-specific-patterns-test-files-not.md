# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Test Files Not

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all test file creation and organization activities across the codebase.

## Context

- The codebase contains multiple testing frameworks (Python pytest, TypeScript/React Testing Library) requiring consistent organization patterns
- Test files are distributed across dedicated test directories (api/tests/, web/app/components/**/__tests__/) following framework-specific conventions
- Python tests use test_*.py naming with hierarchical directory structure (unit_tests/, integration_tests/), while TypeScript tests use *.spec.tsx/ts naming within __tests__ directories
- The pattern shows 20 files with 91.75% confidence, indicating strong consistency in test organization across both backend and frontend codebases
- Clear separation between unit tests and integration tests is maintained through directory structure and naming conventions

## Problem Statement

Without standardized test file naming and organization conventions, test discovery becomes unreliable, test categorization is inconsistent, and developers lack clear guidance on where to place new tests. This leads to fragmented test suites, difficulty in running specific test categories, and reduced maintainability of the testing infrastructure.

## Decision

1. MUST_NOT: Test files MUST NOT be mixed with production source code in the same directory without clear separation (e.g., __tests__ subdirectory)

## Policy Block

- MUST_NOT Test files MUST NOT be mixed with production source code in the same directory without clear separation (e.g., __tests__ subdirectory)

In scope:
- All Python test files in the api/ directory
- All TypeScript/React test files in the web/ directory
- Unit tests, integration tests, and component tests
- Test files for services, workflows, nodes, and UI components

Out of scope:
- End-to-end test files which may follow different conventions
- Performance or load test files which may require separate organization
- Test fixtures, mocks, or helper utilities that are not test files themselves
- Documentation or example files that demonstrate testing patterns

Exceptions:
- EX-001: Legacy test files exist in non-standard locations and refactoring would break existing CI/CD pipelines
- EX-002: Third-party or vendor-specific testing tools require alternative naming conventions

## Rationale

- The detected pattern shows consistent adoption across 20 files with 91.75% confidence, indicating this is an established and effective practice in the codebase
- Framework-specific conventions (pytest for Python, Jest/React Testing Library for TypeScript) align with industry standards and enable automatic test discovery
- Hierarchical organization by test type (unit vs integration) enables selective test execution, faster feedback loops, and clearer test categorization
- Co-location of frontend tests with components improves maintainability and makes it easier to find and update tests when components change

## Consequences

Positive:
- Test discovery is automated and reliable through framework-native conventions (pytest auto-discovery, Jest pattern matching)
- Developers can quickly locate tests for specific modules or components through predictable naming and organization
- CI/CD pipelines can selectively run test categories (unit vs integration) to optimize build times and feedback cycles
- New team members can easily understand where to place tests by following clear, consistent patterns
- Test coverage tools can accurately map tests to source code through mirrored directory structures

Negative:
- Requires discipline to maintain consistency, especially when adding new test types or frameworks
- Refactoring existing tests to match conventions may require significant effort if legacy tests exist
- Co-located frontend tests increase directory nesting depth, which may complicate navigation in some IDEs
- Different conventions for Python and TypeScript may cause confusion for developers working across both stacks

## Alternatives

- Use a single unified naming convention (e.g., *.test.* for all languages) (rejected)
  Rejected because: Would conflict with framework defaults and community conventions, breaking automatic test discovery in pytest and Jest
  When valid: Only valid in greenfield projects where custom test runners are built from scratch
- Centralize all tests in a single top-level tests/ directory regardless of language or component (rejected)
  Rejected because: Would break co-location benefits for frontend components and make it harder to maintain tests alongside rapidly changing UI code
  When valid: May be appropriate for small projects with minimal frontend complexity
- Use test suffixes instead of prefixes for Python tests (e.g., agent_node_test.py instead of test_agent_node.py) (rejected)
  Rejected because: Conflicts with pytest's default discovery pattern and Python community conventions
  When valid: Not recommended unless using a custom test runner that requires this pattern

## Risks

- Inconsistent application of conventions as the codebase grows or new developers join the team
  Mitigation: Implement automated linting rules to enforce naming conventions and directory structure. Add pre-commit hooks to validate test file placement.
  Owner: Engineering team and CI/CD maintainers
- Framework updates or migrations may require changes to naming conventions, causing widespread refactoring
  Mitigation: Monitor framework release notes for convention changes. Use automated refactoring tools and maintain comprehensive test coverage to ensure safe migrations.
  Owner: Platform engineering team
- Deep nesting of __tests__ directories in frontend code may complicate imports and path resolution
  Mitigation: Use module path aliases (e.g., @/ prefix) and configure Jest moduleNameMapper to simplify imports. Document import patterns in testing guidelines.
  Owner: Frontend engineering team

## Implementation Notes

- Configure pytest.ini or pyproject.toml to explicitly define test discovery patterns: testpaths = ['api/tests'] and python_files = ['test_*.py']
- Configure Jest in package.json or jest.config.js with testMatch patterns: ['**/__tests__/**/*.spec.[jt]s?(x)']
- Create project templates or scaffolding scripts that automatically generate test files in the correct location with proper naming
- Document the conventions in CONTRIBUTING.md or TESTING.md with examples for both Python and TypeScript test creation
- Set up IDE workspace settings to recognize test directories and enable test runner integrations for both pytest and Jest

## Continuation Context


Verify commands:
- find api/tests -name '*.py' ! -name 'test_*.py' ! -name '__init__.py' ! -name 'conftest.py' -type f | grep -v '__pycache__' | wc -l | grep -q '^0$'
- find web/app -type d -name '__tests__' -exec find {} -name '*.spec.tsx' -o -name '*.spec.ts' \; | wc -l
- pytest --collect-only api/tests/unit_tests/ 2>&1 | grep -q 'collected' && pytest --collect-only api/tests/test_containers_integration_tests/ 2>&1 | grep -q 'collected'

Accept when:
- All Python test files in api/tests/ follow the test_*.py naming convention and pytest can discover them automatically
- All TypeScript/React test files are located in __tests__ directories and use *.spec.tsx or *.spec.ts naming
- Unit tests and integration tests are clearly separated in distinct directory hierarchies
- Test discovery commands (pytest --collect-only, jest --listTests) successfully identify all test files without manual configuration

## Enforcement

- Verified by: Automated CI/CD pipeline checks that validate test file naming and location patterns
- Verified by: Pre-commit hooks that reject commits containing test files in non-standard locations
- Verified by: Code review checklist items requiring verification of test file placement
- Verified by: Periodic automated audits using find/grep commands to detect non-compliant test files
- Violation handling: CI pipeline fails if test files are detected outside standard directories or with incorrect naming
- Violation handling: Automated PR comments flag non-compliant test files and provide guidance on correct placement
- Violation handling: Test coverage reports exclude non-compliant test files to incentivize proper organization
- Violation handling: Quarterly cleanup sprints to refactor legacy tests into compliant structure
- Exception process: Developer submits exception request via GitHub issue or architecture review board with justification
- Exception process: Engineering lead or architect reviews the request and assesses impact on test infrastructure
- Exception process: If approved, exception is documented in .test-exceptions.yml with expiration date and migration plan
- Exception process: Exceptions are reviewed quarterly and must be renewed or resolved through refactoring