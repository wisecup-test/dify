# Establish Core Utility and Error Handling Libraries as Foundational Modules: Library Modules Maintain

These rules are ALWAYS ACTIVE for all utility modules, error handling classes, decorators, state management primitives, and base classes that serve cross-cutting concerns across multiple features or services.

### Rules

- **R-LIB-001** SHOULD: Library modules SHOULD maintain single responsibility, with clear boundaries between error handling, utilities, decorators, and state management.
- **R-LIB-002** SHOULD: Extract utility functions to library modules when used across three or more locations (rule of three).
- **R-LIB-003** SHOULD: Organize library modules by concern rather than by feature (prefer `services/errors/` over `features/feature1/errors/`).
- **R-LIB-004** SHOULD: Use consistent naming conventions: 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives.
- **R-LIB-005** MUST: Library modules MUST NOT import from feature code; only import from other library modules or external dependencies.
- **R-LIB-006** SHOULD: Document the purpose and scope of each library module in a README or module docstring.
- **R-LIB-007** SHOULD: Keep library modules under 500 lines or 20 functions to prevent bloat and maintain clarity.
- **R-LIB-008** MAY: Extract a utility function used in only one location if it is complex enough to warrant extraction for testability (EXC-001).
- **R-LIB-009** MAY: Inline performance-critical code paths to avoid function call overhead (EXC-002).

### Verify

```bash
# Count utility modules across the codebase
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes in dedicated error modules
grep -r 'class.*Error' --include='*.py' api/services/errors/ 2>/dev/null | wc -l

# Count decorator modules for cross-cutting concerns
find . \( -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' \) -type f | wc -l

# Detect potential circular dependencies between library modules and feature code
grep -r 'from.*features' --include='*.py' --include='*.ts' services/ 2>/dev/null | head -20
```

**Accept when:**
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Library modules do not import from feature-specific code
- Library modules maintain clear single responsibility with documented scope

<enforcement>
Claude Code MUST NOT skip or defer verification. Code review process MUST check for code duplication and suggest extraction to library modules. Static analysis tools in CI pipeline MUST detect duplicate code patterns and flag for refactoring. Architecture review MUST validate proper use of library modules for cross-cutting concerns. Automated dependency analysis MUST prevent circular dependencies between library modules and feature code.
</enforcement>