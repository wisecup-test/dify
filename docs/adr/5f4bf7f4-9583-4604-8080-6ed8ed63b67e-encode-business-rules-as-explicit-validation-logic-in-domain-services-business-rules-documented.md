# Encode Business Rules as Explicit Validation Logic in Domain Services: Business Rules Documented

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all domain service implementations that handle business logic and validation.

## Context

- The codebase contains multiple domain services (feature_service.py, knowledge_service.py) that implement business logic and validation rules governing domain operations
- Business rules need to be explicitly encoded and validated before domain operations proceed to ensure data integrity and business constraint compliance
- Pattern detected across 3 files with 91.83% confidence indicates a consistent architectural approach to embedding validation logic within service layers
- Domain services act as gatekeepers that enforce business constraints before delegating to storage or persistence layers
- The facet 'domain.validation' suggests this pattern specifically addresses validation concerns within domain-driven design contexts

## Problem Statement

Without explicit encoding of business rules in domain services, validation logic becomes scattered across controllers, models, and storage layers, leading to inconsistent enforcement, duplicated validation code, and difficulty in maintaining business constraint integrity as requirements evolve.

## Decision

1. SHOULD: Business rules SHOULD be documented inline with validation code using comments or docstrings explaining the business rationale

## Policy Block

- SHOULD Business rules SHOULD be documented inline with validation code using comments or docstrings explaining the business rationale

In scope:
- All domain service classes that implement business operations
- Feature services, knowledge services, and similar domain-focused service layers
- Validation logic for business constraints, domain invariants, and operational preconditions
- Error handling and exception raising for business rule violations

Out of scope:
- Data type validation and schema validation (handled by serialization layers)
- Authentication and authorization checks (handled by security middleware)
- Infrastructure-level validation (network connectivity, file system access)
- Storage-layer constraints (database foreign keys, unique constraints)

Exceptions:
- EXC-001: Simple CRUD operations with no business logic beyond basic existence checks
- EXC-002: Performance-critical paths where validation has been proven to be a bottleneck through profiling

## Rationale

- Pattern detected with 91.83% confidence across 3 service files indicates this is an established architectural practice in the codebase
- Centralizing business rule validation in domain services creates a single source of truth for business constraints, improving maintainability and reducing bugs
- Explicit validation logic makes business rules visible and testable, enabling better collaboration between technical and business stakeholders
- The domain.validation facet alignment confirms this pattern serves the critical function of maintaining domain integrity through explicit rule enforcement

## Consequences

Positive:
- Business rules are centralized in domain services, making them easier to locate, understand, and modify
- Validation logic is testable in isolation from controllers and storage layers, improving test coverage and reliability
- Domain services provide clear contracts about what operations are valid, improving API clarity and reducing runtime errors
- Business rule violations are caught early in the request lifecycle with meaningful error messages

Negative:
- Service layer becomes more complex as it takes on validation responsibilities in addition to orchestration
- May lead to performance overhead if validation logic is computationally expensive or requires additional database queries
- Risk of validation logic becoming tightly coupled to specific service implementations, reducing reusability
- Developers must remember to add validation to new service methods, creating potential for inconsistent enforcement

## Alternatives

- Encode business rules in database constraints and triggers (rejected)
  Rejected because: Database constraints provide limited expressiveness for complex business rules and produce generic error messages that are difficult to map back to business concepts. Additionally, this approach couples business logic to database implementation.
  When valid: Appropriate for fundamental data integrity constraints like foreign keys and uniqueness that should be enforced regardless of application layer
- Implement validation in controller layer before calling services (rejected)
  Rejected because: Controllers should focus on HTTP concerns and request/response handling. Business rule validation in controllers leads to duplication when multiple controllers access the same service, and makes rules harder to test.
  When valid: Only for HTTP-specific validation like request format, content-type headers, or rate limiting
- Use domain model objects with built-in validation methods (deferred)
  Rejected because: Not rejected, but complementary approach. Domain models can validate their own invariants while services validate cross-entity business rules and operational preconditions.
  When valid: Should be used in conjunction with service-layer validation for entity-level invariants

## Risks

- Validation logic becomes scattered across service methods without clear organization, making it difficult to understand complete business rule set
  Mitigation: Establish naming conventions for validation methods (e.g., _validate_*, _check_*) and consider extracting validators into dedicated modules for complex domains
  Owner: Engineering team
- Performance degradation if validation requires expensive operations like database queries or external API calls
  Mitigation: Profile validation performance in critical paths, implement caching strategies for validation data, and consider async validation for non-critical checks
  Owner: Engineering team
- Inconsistent error handling across services makes it difficult for clients to handle business rule violations uniformly
  Mitigation: Define standard exception hierarchy for business rule violations and document expected error responses in API specifications
  Owner: Engineering team

## Implementation Notes

- Create a base service class or mixin that provides common validation utilities and exception types for business rule violations
- Use descriptive exception messages that include the business rule that was violated and guidance on how to correct the issue
- Group related validation logic into private methods (e.g., _validate_feature_limits, _validate_knowledge_permissions) to improve readability
- Write unit tests for validation logic separately from integration tests to ensure business rules are thoroughly tested
- Consider using Python's dataclasses or Pydantic models for complex validation scenarios where declarative validation improves clarity

## Continuation Context


Verify commands:
- grep -r "def _validate_\|def _check_" api/services/ --include="*_service.py" | wc -l
- grep -r "raise.*Exception\|raise.*Error" api/services/ --include="*_service.py" | grep -v "^\s*#" | wc -l
- python -m pytest tests/unit/services/ -k validation -v

Accept when:
- Service files contain explicit validation methods (prefixed with _validate_ or _check_) that are called before domain operations
- Business rule violations raise domain-specific exceptions with descriptive error messages
- Unit tests exist for validation logic covering both valid and invalid cases for each business rule

## Enforcement

- Verified by: Code review checklist includes verification that new service methods include appropriate business rule validation
- Verified by: Unit test coverage requirements mandate tests for validation logic in service layer
- Verified by: Static analysis tools check for validation method naming conventions and exception handling patterns
- Violation handling: Code review feedback requests addition of missing validation logic before merge approval
- Violation handling: CI pipeline fails if service layer test coverage falls below threshold
- Violation handling: Architecture review flags services that delegate validation to controllers or storage layers
- Exception process: Developer documents rationale for exception in service docstring or inline comments
- Exception process: Tech lead reviews and approves exception during code review
- Exception process: Exception is logged in ADR exceptions register with justification and expiration date if temporary