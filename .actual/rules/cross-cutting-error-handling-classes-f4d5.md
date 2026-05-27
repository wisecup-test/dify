# Establish Core Utility and Error Handling Libraries as Foundational Modules: Error Handling Classes

These rules are ALWAYS ACTIVE for all error handling classes, custom exceptions, and utility functions used across multiple features or services in the codebase.

### Rules

- **R-ERR-001** MUST: Error handling classes MUST be organized into service-specific error modules (e.g., services/errors/plugin.py, services/errors/app_model_config.py) with clear inheritance hierarchies.
- **R-ERR-002** MUST: All custom exceptions MUST inherit from appropriate base exception classes within their service-specific error modules.
- **R-ERR-003** SHOULD: Error modules SHOULD follow consistent naming conventions and be located at the service level rather than scattered across feature directories.
- **R-ERR-004** SHOULD: Error handling classes SHOULD be independently testable and not tightly coupled to feature-specific business logic.
- **R-ERR-005** MAY: Utility functions MAY be extracted to library modules when used in three or more locations (rule of three).

### Verify

```bash
# Count utility modules across the codebase
find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l

# Count error handling classes in service error modules
grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l

# Find decorator patterns for cross-cutting concerns
find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l
```

**Accept when:**
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure with clear inheritance hierarchies
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules
- Error modules do not import from feature-specific business logic code
- Circular dependencies do not exist between library modules and feature code

<enforcement>
Claude Code MUST NOT skip or defer verification of error handling organization and library module structure. All error classes MUST be reviewed for proper placement in service-specific error modules before approval.
</enforcement>