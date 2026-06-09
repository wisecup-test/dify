# Establish Core Utility and Error Handling Libraries as Foundational Modules: Feature Specific Code

These rules are ALWAYS ACTIVE for all feature-specific code files that implement cross-cutting concerns, error handling, utilities, decorators, and state management primitives.

### Rules

- **R-CORE-001** MUST NOT: Feature-specific code MUST NOT directly implement cross-cutting concerns that could be generalized into library modules.
- **R-CORE-002** MUST: All error handling classes and custom exceptions MUST be organized in dedicated error modules (e.g., services/errors/, errors/*.py).
- **R-CORE-003** MUST: Utility functions used across multiple features or services MUST be extracted into dedicated library modules (utils.ts, utils.py, base.py).
- **R-CORE-004** MUST: Decorator patterns for logging, instrumentation, and aspect-oriented concerns MUST be implemented in dedicated decorator modules.
- **R-CORE-005** MUST: State management primitives and atoms for frontend applications MUST be organized in dedicated atom modules (atoms.ts).
- **R-CORE-006** MUST: Base classes and abstract interfaces used for inheritance MUST be extracted into dedicated base modules.
- **R-CORE-007** MUST: Parser and serialization utilities for cross-cutting data transformations MUST be centralized in library modules.
- **R-CORE-008** SHOULD: Follow consistent naming conventions: use 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives.
- **R-CORE-009** SHOULD: Organize library modules by concern rather than by feature (prefer services/errors/ over features/feature1/errors/ when errors are shared).
- **R-CORE-010** SHOULD: Document the purpose and scope of each library module in a README or module docstring to prevent it from becoming a catch-all.
- **R-CORE-011** SHOULD: Apply the rule of three: extract to library module when functionality is used in three or more locations.
- **R-CORE-012** MAY: A utility function is used in only one location but is complex enough to warrant extraction for testability (EXC-001).
- **R-CORE-013** MAY: Performance-critical code paths may require inline implementation to avoid function call overhead (EXC-002).

### Verify

```bash
# Count utility modules present in appropriate directories
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes organized in dedicated error modules
grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l

# Count decorator patterns in dedicated decorator modules
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l

# Detect code duplication for cross-cutting concerns
grep -r 'def.*error\|class.*Exception' --include='*.py' | grep -v 'services/errors' | head -20
```

**Accept when:**
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature code, only from other library modules or external dependencies
- Circular dependencies do not exist between library modules and feature code

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI pipeline checks. Violations must be flagged during code review, and exceptions require documented justification and tech lead approval.
</enforcement>