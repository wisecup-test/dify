# Establish Core Utility and Error Handling Libraries as Foundational Modules: Teams Create Domain

These rules are ALWAYS ACTIVE for all files matching the configured scope, particularly utility modules, error handling classes, decorators, and shared functionality across frontend (TypeScript/React) and backend (Python) codebases.

### Rules

- **R-UTIL-001** MAY: Teams MAY create domain-specific utility modules (e.g., workflow/utils, plugins/utils) when functionality is specific to that domain but reused within it.
- **R-UTIL-002** MUST: All error handling classes and custom exceptions be organized in dedicated error modules (e.g., services/errors/).
- **R-UTIL-003** MUST: Utility functions used across multiple features or services be extracted into library modules rather than duplicated.
- **R-UTIL-004** SHOULD: Decorator patterns for logging, instrumentation, and aspect-oriented concerns be implemented in dedicated decorator modules.
- **R-UTIL-005** SHOULD: State management primitives and atoms for frontend applications be organized in dedicated library modules (e.g., atoms.ts).
- **R-UTIL-006** SHOULD: Base classes and abstract interfaces used for inheritance be extracted into library modules (e.g., base.py).
- **R-UTIL-007** SHOULD: Parser and serialization utilities for cross-cutting data transformations be centralized in library modules.
- **R-UTIL-008** MUST NOT: Extract feature-specific business logic that is not reused elsewhere into library modules.
- **R-UTIL-009** MUST NOT: Extract single-use helper functions tightly coupled to one component into library modules.
- **R-UTIL-010** MUST NOT: Allow library modules to import from feature code; only import from other library modules or external dependencies.
- **R-UTIL-011** SHOULD: Apply the rule of three: extract to library module when functionality is used in three or more locations.
- **R-UTIL-012** SHOULD: Use consistent naming conventions: 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives.
- **R-UTIL-013** SHOULD: Organize library modules by concern rather than by feature (prefer services/errors/ over features/feature1/errors/ when errors are shared).
- **R-UTIL-014** SHOULD: Document the purpose and scope of each library module in a README or module docstring to prevent it from becoming a catch-all.
- **R-UTIL-015** SHOULD: Maintain library modules under 500 lines or 20 functions to prevent bloat and technical debt accumulation.
- **R-UTIL-EXC-001** MAY: Extract a utility function used in only one location if it is complex enough to warrant extraction for testability.
- **R-UTIL-EXC-002** MAY: Implement performance-critical code paths inline to avoid function call overhead.

### Verify

```bash
# Count utility modules across the codebase
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes in dedicated error modules
grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l

# Count decorator modules for cross-cutting concerns
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l

# Detect code duplication that should be extracted
grep -r 'def ' --include='*.py' | sort | uniq -d | wc -l

# Check for circular dependencies between library modules and feature code
grep -r 'from.*feature' --include='*.py' api/services/ | wc -l
```

**Accept when:**
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature code, only from other library modules or external dependencies
- Library modules are organized by concern rather than by feature
- Each library module has documented purpose and scope
- No library module exceeds 500 lines or 20 functions without being split

<enforcement>
Claude Code MUST NOT skip or defer verification. Code review process MUST check for code duplication and suggest extraction to library modules. Static analysis tools in CI pipeline MUST detect duplicate code patterns and flag for refactoring. Architecture review MUST validate proper use of library modules for cross-cutting concerns. Automated dependency analysis MUST prevent circular dependencies between library modules and feature code.
</enforcement>