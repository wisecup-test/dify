# Establish Core Utility and Error Handling Libraries as Foundational Modules: Utility Modules Follow

These rules are ALWAYS ACTIVE for all utility modules, error handling classes, decorators, and shared functionality across the codebase.

### Rules

- **R-UTIL-001** SHOULD: Utility modules SHOULD follow naming conventions that clearly indicate their purpose: `utils.ts`/`utils.py` for general utilities, `base.py` for base classes, `atoms.ts` for state management primitives.
- **R-UTIL-002** SHOULD: Error handling classes and custom exceptions SHOULD be organized in dedicated error modules under `services/errors/` or equivalent structure.
- **R-UTIL-003** SHOULD: Decorator patterns for logging, instrumentation, and aspect-oriented concerns SHOULD be implemented in dedicated decorator modules.
- **R-UTIL-004** SHOULD: Cross-cutting concerns like error handling, logging, validation, and serialization SHOULD be extracted into library modules rather than duplicated across feature code.
- **R-UTIL-005** SHOULD: Library modules SHOULD NOT import from feature code; only from other library modules or external dependencies.
- **R-UTIL-006** SHOULD: Functionality used in three or more locations SHOULD be extracted to a library module (rule of three).
- **R-UTIL-007** MAY: A utility function MAY be extracted when used in only one location if it is complex enough to warrant extraction for testability (EXC-001).
- **R-UTIL-008** MAY: Performance-critical code paths MAY require inline implementation to avoid function call overhead (EXC-002).

### Verify

```bash
# Count utility module files
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes
grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l

# Count decorator modules
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l

# Check for code duplication in cross-cutting concerns
grep -r 'def.*error\|def.*log\|def.*validate' --include='*.py' | grep -v 'services/errors\|decorators' | head -20
```

**Accept when:**
- Utility modules (`utils.ts`, `utils.py`, `base.py`, `atoms.ts`) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under `services/errors/` or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature code, only from other library modules or external dependencies
- Functionality used in three or more locations has been extracted to library modules

<enforcement>
Claude Code MUST NOT skip or defer verification. Code review process MUST check for code duplication and suggest extraction to library modules. Static analysis tools in CI pipeline MUST detect duplicate code patterns and flag for refactoring. Architecture review MUST validate proper use of library modules for cross-cutting concerns. Automated dependency analysis MUST prevent circular dependencies between library modules and feature code.
</enforcement>