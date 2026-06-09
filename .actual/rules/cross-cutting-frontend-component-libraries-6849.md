# Establish Core Utility and Error Handling Libraries as Foundational Modules: Frontend Component Libraries

These rules are ALWAYS ACTIVE for all frontend component libraries, utility modules, error handling classes, and shared functionality across the codebase.

### Rules

- **R-UTIL-001** SHOULD: Frontend component libraries SHOULD separate presentational logic from state management, using patterns like atoms.ts for state primitives and utils.ts for pure functions.
- **R-UTIL-002** MUST: All error handling classes and custom exceptions MUST be organized in dedicated error modules under services/errors/ or equivalent structure.
- **R-UTIL-003** MUST: Utility functions used across multiple features or services MUST be extracted into dedicated library modules rather than duplicated.
- **R-UTIL-004** SHOULD: Decorator patterns for logging, instrumentation, and aspect-oriented concerns SHOULD be implemented in dedicated decorator modules.
- **R-UTIL-005** SHOULD: Base classes and abstract interfaces used for inheritance SHOULD be organized in dedicated base modules.
- **R-UTIL-006** SHOULD: Parser and serialization utilities for cross-cutting data transformations SHOULD be extracted into library modules.
- **R-UTIL-007** MUST: Library modules MUST NOT import from feature code; imports MUST only be from other library modules or external dependencies.
- **R-UTIL-008** SHOULD: Naming conventions SHOULD follow: 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives.
- **R-UTIL-009** MUST: Library modules MUST be organized by concern rather than by feature (prefer services/errors/ over features/feature1/errors/ when errors are shared).
- **R-UTIL-010** SHOULD: Each library module SHOULD include documentation of its purpose and scope in a README or module docstring.
- **R-UTIL-011** MAY: A utility function MAY be extracted when used in only one location if it is complex enough to warrant extraction for testability (EXC-001).
- **R-UTIL-012** MAY: Performance-critical code paths MAY use inline implementation to avoid function call overhead (EXC-002).

### Verify

```bash
# Count utility modules present
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes
grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l

# Count decorator modules
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l

# Detect circular dependencies
grep -r 'from.*feature' --include='*.ts' --include='*.py' services/ | grep -v test
```

**Accept when:**
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature code
- Naming conventions are consistently applied across the codebase
- Each library module has documented purpose and scope

<enforcement>
Clause Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI pipeline checks. Violations MUST be flagged during code review, and refactoring tickets MUST be created for violations discovered. Exceptions require documented rationale and tech lead approval.
</enforcement>