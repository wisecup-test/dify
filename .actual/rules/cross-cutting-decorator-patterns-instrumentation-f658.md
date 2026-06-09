# Establish Core Utility and Error Handling Libraries as Foundational Modules: Decorator Patterns Instrumentation

These rules are ALWAYS ACTIVE for all utility modules, error handling classes, decorator patterns, and cross-cutting concerns across the codebase.

### Rules

- **R-CORE-001** MUST: Decorator patterns for instrumentation and cross-cutting concerns MUST be implemented as library modules in dedicated directories (e.g., extensions/otel/decorators/).
- **R-CORE-002** MUST: All error handling classes and custom exceptions MUST be organized in dedicated error modules under services/errors/ or equivalent structure.
- **R-CORE-003** MUST: Utility functions used across multiple features or services MUST be extracted into dedicated library modules (utils.ts, utils.py, base.py, atoms.ts).
- **R-CORE-004** MUST: Library modules MUST NOT import from feature code; imports MUST only be from other library modules or external dependencies.
- **R-CORE-005** SHOULD: Follow the rule of three: extract functionality to library module when used in three or more locations.
- **R-CORE-006** SHOULD: Use consistent naming conventions: 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives.
- **R-CORE-007** SHOULD: Organize library modules by concern rather than by feature (prefer services/errors/ over features/feature1/errors/ when errors are shared).
- **R-CORE-008** SHOULD: Document the purpose and scope of each library module in a README or module docstring to prevent it from becoming a catch-all.
- **R-CORE-009** MAY: Extract a utility function used in only one location if it is complex enough to warrant extraction for testability (EXC-001).
- **R-CORE-010** MAY: Implement performance-critical code paths inline to avoid function call overhead (EXC-002).

### Verify

```bash
# Count utility modules across the codebase
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes in dedicated error modules
grep -r 'class.*Error' --include='*.py' api/services/errors/ 2>/dev/null | wc -l

# Count decorator modules in dedicated directories
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' 2>/dev/null | wc -l

# Check for circular dependencies between library modules and feature code
grep -r 'from.*features' --include='*.py' api/services/ 2>/dev/null | grep -v test | wc -l
```

**Accept when:**
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature code, only from other library modules or external dependencies
- Naming conventions are consistent across the codebase (utils, base, errors, decorators, atoms)

<enforcement>
Clause Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI pipeline checks.
</enforcement>