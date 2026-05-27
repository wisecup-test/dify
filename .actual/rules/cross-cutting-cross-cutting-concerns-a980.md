# Establish Core Utility and Error Handling Libraries as Foundational Modules: Cross Cutting Concerns

These rules are ALWAYS ACTIVE for all files in utility modules, error handling services, decorators, and shared library code across the codebase.

### Rules

- **R-CCN-001** MUST: Extract cross-cutting concerns such as error handling, logging decorators, and utility functions into dedicated library modules rather than duplicating them across feature implementations.
- **R-CCN-002** MUST: Organize all error handling classes and custom exceptions in dedicated error modules under services/errors/ or equivalent structure.
- **R-CCN-003** MUST: Implement decorator patterns for logging, instrumentation, and aspect-oriented concerns in dedicated decorator modules.
- **R-CCN-004** MUST: Prevent circular dependencies between library modules and feature code by ensuring library modules only import from other library modules or external dependencies, never from feature code.
- **R-CCN-005** SHOULD: Follow consistent naming conventions: use 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives.
- **R-CCN-006** SHOULD: Apply the rule of three: extract functionality to a library module when it is used in three or more locations.
- **R-CCN-007** SHOULD: Organize library modules by concern rather than by feature (prefer services/errors/ over features/feature1/errors/ when errors are shared across features).
- **R-CCN-008** SHOULD: Document the purpose and scope of each library module in a README or module docstring to prevent it from becoming a catch-all for unrelated functionality.
- **R-CCN-009** MAY: Inline a utility function in a single location if it is complex enough to warrant extraction for testability (EXC-001).
- **R-CCN-010** MAY: Implement performance-critical code paths inline to avoid function call overhead (EXC-002).

### Verify

```bash
# Count utility modules present in appropriate directories
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes in dedicated error modules
grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l

# Count decorator modules for cross-cutting concerns
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l

# Detect code duplication patterns that should be extracted
grep -r 'def.*(' --include='*.py' | sort | uniq -d | wc -l
```

**Accept when:**
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature code, only from other library modules or external dependencies
- Each library module has documented purpose and scope

<enforcement>
Claude Code MUST NOT skip or defer verification. Code reviews MUST flag inline implementations of cross-cutting concerns that should use library modules. CI pipeline MUST warn for code duplication above threshold (10+ lines duplicated). Architecture review is REQUIRED for PRs introducing new utility patterns outside library modules. Exceptions MUST be documented in code comments with approval links and reviewed quarterly.
</enforcement>