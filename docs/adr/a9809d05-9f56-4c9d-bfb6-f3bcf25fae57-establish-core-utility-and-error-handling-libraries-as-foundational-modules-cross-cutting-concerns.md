# Establish Core Utility and Error Handling Libraries as Foundational Modules: Cross Cutting Concerns

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The codebase exhibits a consistent pattern of organizing utility functions, error handling classes, and shared functionality into dedicated library modules across 46 files
- Both frontend (TypeScript/React) and backend (Python) codebases demonstrate parallel architectural patterns for module organization, including utilities, decorators, error services, and component atoms
- The pattern spans multiple domains including OpenTelemetry instrumentation, plugin systems, workflow engines, billing components, and testing infrastructure
- Cross-cutting concerns like error handling, service decorators, and state management are consistently extracted into reusable library modules rather than being duplicated across feature code
- The detection signature (352185f1ce9ffb39c86c964ce8959ec4) appears in files with naming conventions like utils.ts, base.py, errors/*.py, atoms.ts, and decorators/*.py, indicating a deliberate modularization strategy

## Problem Statement

Without a standardized approach to organizing core utilities, error handling, and shared functionality, codebases risk code duplication, inconsistent error handling patterns, tight coupling between features, and difficulty in maintaining cross-cutting concerns. The challenge is to establish clear guidelines for when and how to extract functionality into library modules while maintaining discoverability and preventing over-abstraction.

## Decision

1. MUST: Cross-cutting concerns such as error handling, logging decorators, and utility functions MUST be extracted into dedicated library modules rather than duplicated across feature implementations

## Policy Block

- MUST Cross-cutting concerns such as error handling, logging decorators, and utility functions MUST be extracted into dedicated library modules rather than duplicated across feature implementations

In scope:
- All error handling classes and custom exceptions
- Utility functions used across multiple features or services
- Decorator patterns for logging, instrumentation, and aspect-oriented concerns
- State management primitives and atoms for frontend applications
- Base classes and abstract interfaces used for inheritance
- Parser and serialization utilities for cross-cutting data transformations

Out of scope:
- Feature-specific business logic that is not reused elsewhere
- Single-use helper functions tightly coupled to one component
- Test fixtures and mocks specific to individual test files
- Configuration files and environment-specific settings

Exceptions:
- EXC-001: A utility function is used in only one location but is complex enough to warrant extraction for testability
- EXC-002: Performance-critical code paths require inline implementation to avoid function call overhead

## Rationale

- The pattern detected across 46 files with 90.95% confidence demonstrates a mature, consistent approach to code organization that has proven effective across both frontend and backend codebases
- Extracting cross-cutting concerns into library modules reduces code duplication, improves testability, and creates clear separation of concerns between business logic and infrastructure code
- Standardized error handling through dedicated error service modules enables consistent error reporting, logging, and recovery strategies across the application
- The parallel structure between TypeScript and Python implementations (utils.ts/utils.py, atoms.ts/base.py) suggests this pattern is language-agnostic and represents a fundamental architectural principle

## Consequences

Positive:
- Reduced code duplication through centralized utility functions and error handling classes
- Improved testability by isolating cross-cutting concerns into independently testable modules
- Enhanced maintainability through clear separation between feature code and infrastructure code
- Better discoverability of shared functionality through consistent naming conventions and module organization
- Easier onboarding for new developers who can quickly locate utility functions and error handling patterns

Negative:
- Risk of over-abstraction if utilities are extracted prematurely before patterns are fully understood
- Potential for utility modules to become dumping grounds for miscellaneous functions without clear organization
- Additional indirection may make code harder to follow for simple use cases
- Requires discipline to maintain clear boundaries and prevent circular dependencies between library modules

## Alternatives

- Inline all utility functions and error handling directly in feature code without extraction (rejected)
  Rejected because: This approach leads to significant code duplication, inconsistent error handling, and makes cross-cutting changes extremely difficult as evidenced by the 46 files already following the library module pattern
  When valid: Only appropriate for truly one-off functions that will never be reused and are tightly coupled to a single feature
- Use a monolithic shared library with all utilities, errors, and decorators in a single module (rejected)
  Rejected because: A single monolithic module would create tight coupling, make it difficult to understand module boundaries, and lead to merge conflicts as all teams modify the same file
  When valid: May be acceptable for very small projects with fewer than 10 utility functions
- Adopt a microservices-style approach where each service/feature has its own isolated utilities with no sharing (rejected)
  Rejected because: This would duplicate common patterns across services and prevent standardization of cross-cutting concerns like error handling and instrumentation
  When valid: Appropriate when services are truly independent with no shared infrastructure or when polyglot architectures require service-specific implementations

## Risks

- Library modules may accumulate technical debt and become bloated with poorly organized utilities over time
  Mitigation: Establish regular refactoring cycles to review library modules, enforce single responsibility principle, and split modules that grow beyond 500 lines or 20 functions
  Owner: Engineering team leads
- Circular dependencies may emerge between library modules and feature code if boundaries are not carefully maintained
  Mitigation: Implement dependency analysis tools in CI pipeline to detect circular imports, establish clear layering with library modules at the foundation
  Owner: Platform engineering team
- Developers may struggle to decide when to extract functionality into library modules versus keeping it local
  Mitigation: Apply the rule of three: extract to library module when functionality is used in three or more locations, document decision criteria in engineering guidelines
  Owner: Architecture review board

## Implementation Notes

- Start by identifying cross-cutting concerns in your codebase: error handling, logging, validation, serialization, and common data transformations are good candidates for library modules
- Follow consistent naming conventions: use 'utils' for pure functions, 'base' for abstract classes, 'errors' for exception hierarchies, 'decorators' for aspect-oriented patterns, and 'atoms' for state primitives
- Organize library modules by concern rather than by feature: prefer services/errors/ over features/feature1/errors/ when errors are shared across features
- Document the purpose and scope of each library module in a README or module docstring to prevent it from becoming a catch-all for unrelated functionality
- Use static analysis tools to detect code duplication and suggest candidates for extraction into library modules
- Establish import conventions: library modules should not import from feature code, only from other library modules or external dependencies

## Continuation Context


Verify commands:
- find . -type f \( -name 'utils.ts' -o -name 'utils.py' -o -name 'base.py' -o -name 'atoms.ts' \) | wc -l
- grep -r 'class.*Error' --include='*.py' api/services/errors/ | wc -l
- find . -path '*/decorators/*.py' -o -path '*/extensions/*/decorators/*.py' | wc -l

Accept when:
- Utility modules (utils.ts, utils.py, base.py, atoms.ts) are present in appropriate directories and contain reusable functions
- Error handling classes are organized in dedicated error modules under services/errors/ or equivalent structure
- Decorator patterns for cross-cutting concerns are implemented in dedicated decorator modules
- No code duplication exists for cross-cutting concerns that could be extracted into library modules

## Enforcement

- Verified by: Code review process checks for code duplication and suggests extraction to library modules
- Verified by: Static analysis tools in CI pipeline detect duplicate code patterns and flag for refactoring
- Verified by: Architecture review for new features validates proper use of library modules for cross-cutting concerns
- Verified by: Automated dependency analysis prevents circular dependencies between library modules and feature code
- Violation handling: Code reviews flag inline implementations of cross-cutting concerns that should use library modules
- Violation handling: CI pipeline warnings for code duplication above threshold (e.g., 10+ lines duplicated)
- Violation handling: Refactoring tickets created for violations discovered during code review
- Violation handling: Architecture review required for PRs that introduce new utility patterns outside library modules
- Exception process: Developer documents rationale for exception in PR description with specific justification
- Exception process: Tech lead or architect reviews exception request and approves/rejects based on criteria
- Exception process: Approved exceptions are documented in code comments with links to approval discussion
- Exception process: Exceptions are reviewed quarterly to determine if they should be refactored or if guidelines should be updated