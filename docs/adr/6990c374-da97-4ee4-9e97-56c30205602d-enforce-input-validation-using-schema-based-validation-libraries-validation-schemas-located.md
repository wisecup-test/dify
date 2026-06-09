# Enforce Input Validation Using Schema-Based Validation Libraries: Validation Schemas Located

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all code that handles external input, including API endpoints, user interfaces, and data processing pipelines.

## Context

- The codebase processes external input from multiple sources including API requests, user form submissions, and inter-service communications, creating potential attack vectors for injection attacks, data corruption, and security vulnerabilities
- Pattern detection identified 7 files with consistent use of schema-based validation libraries (Zod for TypeScript, validation decorators for Python) with 90.83% confidence, indicating an established architectural pattern
- Modern applications require defense-in-depth security strategies where input validation serves as the first line of defense against malicious or malformed data
- The presence of generated contract files (orpc.gen.ts, zod.gen.ts) and dedicated validation components (with-input-validation) suggests a systematic approach to validation across the application stack
- Without standardized input validation, each developer might implement ad-hoc validation logic leading to inconsistent security posture and increased maintenance burden

## Problem Statement

How do we ensure consistent, comprehensive, and maintainable input validation across all application layers (API, UI, services) to prevent security vulnerabilities, data integrity issues, and runtime errors caused by malformed or malicious input?

## Decision

1. SHOULD: Validation schemas SHOULD be co-located with their corresponding API route definitions or shared in a centralized contracts package

## Policy Block

- SHOULD Validation schemas SHOULD be co-located with their corresponding API route definitions or shared in a centralized contracts package

In scope:
- All HTTP API endpoints receiving external requests
- GraphQL resolvers and mutations
- User interface form components and input fields
- File upload handlers and parsers
- WebSocket message handlers
- CLI argument parsers
- Configuration file loaders
- Inter-service API calls where data crosses trust boundaries

Out of scope:
- Internal function calls within the same service where data has already been validated
- Database query results from trusted internal databases
- Hardcoded constants and configuration values defined in code
- Data transformations that occur after successful validation

Exceptions:
- EXC-001: Performance-critical paths where validation overhead is measured and documented as unacceptable, AND alternative security controls are in place
- EXC-002: Legacy endpoints scheduled for deprecation within 90 days that have existing validation logic

## Rationale

- Schema-based validation provides type safety, runtime validation, and automatic TypeScript type inference, reducing the gap between compile-time and runtime type checking
- The pattern detection identified 7 files with 90.83% confidence showing consistent adoption of validation libraries (Zod, validation decorators), indicating this is already an established practice worth formalizing
- Centralized validation schemas enable code generation for API contracts, reducing duplication and ensuring consistency between frontend and backend validation logic
- Early validation at system boundaries follows the principle of 'fail fast' and prevents invalid data from propagating through the application, reducing debugging complexity and potential security vulnerabilities

## Consequences

Positive:
- Significantly reduced risk of injection attacks (SQL injection, XSS, command injection) by validating and sanitizing input at entry points
- Improved developer experience with type-safe schemas that provide autocomplete, compile-time checking, and runtime validation in a single definition
- Better error messages and user experience through structured validation errors that can be mapped to specific form fields
- Reduced maintenance burden through shared validation schemas and generated contracts that stay in sync across the stack
- Easier testing with clear validation boundaries and the ability to test validation logic independently

Negative:
- Additional development overhead to define and maintain validation schemas for all inputs
- Potential performance impact from validation overhead, especially for high-throughput endpoints or large payloads
- Learning curve for developers unfamiliar with schema validation libraries like Zod or Pydantic
- Risk of validation logic becoming overly complex or duplicated if not properly organized and shared

## Alternatives

- Manual validation using conditional checks and type assertions (rejected)
  Rejected because: Manual validation is error-prone, difficult to maintain, lacks type safety, and leads to inconsistent validation logic across the codebase. The pattern detection shows the team has already moved away from this approach.
  When valid: Only acceptable for trivial internal utilities with minimal input requirements
- Runtime type checking only (e.g., TypeScript runtime type guards without schema validation) (rejected)
  Rejected because: Type guards only check types, not business rules or constraints (e.g., string length, email format, numeric ranges). They also don't provide structured error messages for user feedback.
  When valid: Can be used as a supplement to schema validation for internal type narrowing
- Server-side validation only without client-side validation (rejected)
  Rejected because: Lacks immediate user feedback and increases server load with invalid requests. The pattern shows adoption of client-side validation components (with-input-validation), indicating the value of validation at multiple layers.
  When valid: Acceptable for internal APIs or CLI tools where user experience is less critical

## Risks

- Validation schemas may become out of sync with actual API implementations, leading to false positives or negatives in validation
  Mitigation: Implement automated contract testing and schema generation from source definitions. Use CI checks to verify generated contracts are up to date. Consider using tools like tRPC or OpenAPI generators that enforce schema-code alignment.
  Owner: Platform Engineering Team
- Performance degradation on high-throughput endpoints due to validation overhead
  Mitigation: Profile validation performance on critical paths. Consider caching parsed schemas, using faster validation libraries for hot paths, or implementing validation sampling for trusted sources. Document performance requirements and monitor validation latency.
  Owner: Backend Engineering Team
- Overly restrictive validation may block legitimate use cases or make the API difficult to use
  Mitigation: Design validation schemas with appropriate flexibility. Gather feedback from API consumers during design reviews. Version APIs to allow evolution of validation rules. Document validation requirements clearly in API documentation.
  Owner: API Design Team

## Implementation Notes

- For TypeScript projects, use Zod as the primary validation library. Define schemas in a shared contracts package and generate TypeScript types using z.infer<typeof schema>
- For Python projects, use Pydantic models or similar validation frameworks. Leverage FastAPI's automatic validation integration for API endpoints
- Create reusable validation middleware or decorators that can be applied to API routes to automatically validate request bodies, query parameters, and headers
- Implement a standard error response format for validation failures that includes field-level errors, error codes, and user-friendly messages
- For generated contracts (orpc.gen.ts, zod.gen.ts), set up automated generation scripts in the build pipeline and add CI checks to ensure generated files are committed and up to date
- Consider implementing a validation testing utility that can verify validation schemas reject invalid inputs and accept valid inputs across common edge cases

## Continuation Context


Verify commands:
- grep -r "z\.object\|z\.string\|z\.number" packages/contracts/ --include="*.ts" | wc -l
- grep -r "@validate\|ValidationError\|pydantic" api/ --include="*.py" | wc -l
- find . -name "*validation*" -o -name "*zod.gen*" -o -name "*orpc.gen*" | head -20
- npm test -- --testPathPattern=validation --passWithNoTests || echo 'No validation tests found'

Accept when:
- Validation schema definitions are found in at least 80% of API endpoint files
- All generated contract files (*.gen.ts, *.gen.py) are present and up to date in the repository
- Validation test coverage exists for critical API endpoints with both valid and invalid input test cases
- CI pipeline includes checks for schema generation and validation test execution

## Enforcement

- Verified by: Automated CI checks that verify generated validation contracts are up to date
- Verified by: Code review checklist items requiring validation schemas for new API endpoints
- Verified by: Static analysis tools (ESLint plugins, Pylint) configured to detect unvalidated input handling
- Verified by: Security scanning tools that flag endpoints without input validation
- Violation handling: CI pipeline fails if generated contracts are out of sync with source definitions
- Violation handling: Code review blocks merge if new API endpoints lack validation schemas
- Violation handling: Security team flags unvalidated endpoints during periodic security audits
- Violation handling: Post-incident reviews for security vulnerabilities include validation gap analysis
- Exception process: Submit exception request to security team with justification, risk assessment, and alternative controls
- Exception process: Document exception in ADR or security review document with approval from security lead and principal engineer
- Exception process: Set expiration date for temporary exceptions with required follow-up to implement proper validation
- Exception process: Track exceptions in security backlog and review quarterly