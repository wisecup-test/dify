# Standardize Public API Contract Generation and Error Handling Patterns: Public Error Responses

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all public/external API implementations, contract generation, and error handling code.

## Context

- The codebase contains 39 files implementing public/external API patterns with generated contracts, error handling, and integration patterns
- Evidence shows consistent use of generated type contracts (types.gen.ts, orpc.gen.ts) and structured error handling classes across API and web layers
- OpenTelemetry decorators and parsers indicate observability requirements for external API interactions
- Test files demonstrate validation patterns for agent nodes, workflow engines, and API-based extension services
- The pattern spans both frontend TypeScript contracts and backend Python error handling, suggesting a full-stack API contract strategy

## Problem Statement

Public and external APIs require consistent contract definitions, error handling, and observability to ensure reliable integration between services and external consumers. Without standardized patterns for contract generation, error responses, and telemetry, APIs become fragile, difficult to maintain, and prone to breaking changes that impact downstream consumers.

## Decision

1. MUST: Public API error responses MUST use structured error classes with consistent fields including error code, message, and context

## Policy Block

- MUST Public API error responses MUST use structured error classes with consistent fields including error code, message, and context

In scope:
- All REST APIs exposed to external consumers or third-party integrations
- Internal service-to-service APIs that cross bounded contexts
- Generated TypeScript contract files in packages/contracts/generated/
- Python error handling classes in api/services/errors/
- OpenTelemetry instrumentation for API calls in api/extensions/otel/

Out of scope:
- Internal function calls within a single service boundary
- Database access layer interfaces
- UI component prop interfaces that are not API-related
- Development-only or test-only mock APIs

Exceptions:
- EXC-001: Legacy APIs that predate the contract generation system and are scheduled for deprecation
- EXC-002: Prototype or experimental APIs in feature branches not yet released to production

## Rationale

- Pattern detected across 39 files with 90.52% confidence indicates strong organizational consensus on contract-first API design
- Generated contracts eliminate manual synchronization errors between API producers and consumers, reducing integration bugs
- Structured error handling provides consistent developer experience and enables better error recovery in client applications
- OpenTelemetry instrumentation enables production observability, debugging, and performance monitoring of external API interactions
- Separation of generated contracts from business logic allows independent evolution of API schemas and implementation details

## Consequences

Positive:
- Type-safe API contracts reduce runtime errors and improve developer productivity through IDE autocomplete and compile-time validation
- Consistent error handling patterns make it easier to build robust client-side error recovery and user feedback mechanisms
- Automated contract generation reduces manual maintenance burden and ensures API documentation stays synchronized with implementation
- OpenTelemetry instrumentation provides end-to-end visibility into API performance and failure modes in production environments

Negative:
- Additional build-time complexity from code generation tooling and schema management infrastructure
- Learning curve for developers unfamiliar with contract-first API design and code generation workflows
- Potential for schema evolution challenges when making breaking changes to established public APIs
- Increased repository size from generated code files that must be committed to version control

## Alternatives

- Manual API contract maintenance with hand-written TypeScript interfaces and Python models (rejected)
  Rejected because: Manual synchronization is error-prone and leads to drift between client and server contracts, causing integration failures
  When valid: Only acceptable for internal APIs with single maintainer and no external consumers
- Runtime-only validation without compile-time type contracts (rejected)
  Rejected because: Lacks compile-time safety and developer experience benefits; errors discovered too late in development cycle
  When valid: May be acceptable for dynamic scripting scenarios where type safety cannot be enforced
- GraphQL schema-first approach with generated resolvers (deferred)
  Rejected because: Not rejected but not currently adopted; may be valid for future API evolution
  When valid: Could be considered for APIs requiring flexible querying and strong typing with federation support

## Risks

- Breaking changes to public API schemas could impact external consumers without proper versioning strategy
  Mitigation: Implement API versioning (e.g., /v1/, /v2/) and maintain backward compatibility for at least two major versions
  Owner: API Platform Team
- Code generation tooling failures could block builds and deployments
  Mitigation: Commit generated files to version control as fallback; implement CI checks to validate generation consistency
  Owner: DevOps Team
- OpenTelemetry instrumentation overhead could impact API performance in high-throughput scenarios
  Mitigation: Use sampling strategies for high-volume endpoints; monitor performance metrics and adjust instrumentation granularity
  Owner: Engineering Team

## Implementation Notes

- Set up pre-commit hooks or CI checks to regenerate contracts when schema files change and verify no manual edits to .gen.ts files
- Create base error classes (e.g., APIError, ValidationError) that all domain-specific error classes inherit from for consistency
- Configure OpenTelemetry decorators at the API route/controller level rather than individual functions to minimize boilerplate
- Document the contract generation workflow in developer onboarding materials with examples of adding new API endpoints
- Establish a contracts package (e.g., packages/contracts/) as the single source of truth for all generated API types

## Continuation Context


Verify commands:
- grep -r '\.gen\.ts' packages/contracts/generated/ | wc -l
- find api/services/errors/ -name '*.py' -exec grep -l 'class.*Error' {} \;
- grep -r '@otel\|@trace\|OpenTelemetry' api/extensions/otel/ | head -5
- git diff --name-only | grep '\.gen\.' && echo 'ERROR: Generated files modified' || echo 'OK'

Accept when:
- All public API endpoints have corresponding generated contract files in packages/contracts/generated/
- All API error responses use structured error classes from api/services/errors/ with consistent fields
- OpenTelemetry decorators are present on all external API call sites as verified by grep patterns
- CI pipeline includes contract generation validation and rejects manual edits to generated files

## Enforcement

- Verified by: Pre-commit hooks that regenerate contracts and fail if generated files differ from committed versions
- Verified by: CI pipeline checks that scan for manual modifications to .gen.ts or generated files
- Verified by: Code review checklist requiring verification of error handling patterns and OpenTelemetry instrumentation
- Verified by: Automated tests that validate API responses match generated contract schemas
- Violation handling: CI build failures block merge for manual edits to generated files or missing contract generation
- Violation handling: Code review rejection for API endpoints lacking structured error handling or observability instrumentation
- Violation handling: Runtime warnings logged when API responses don't match contract schemas (in development environments)
- Violation handling: Quarterly architecture reviews identify non-compliant APIs and create remediation tickets
- Exception process: Submit exception request to architecture review board with justification and impact analysis
- Exception process: Document exception in API registry with tracking ticket and remediation timeline
- Exception process: Exceptions require tech lead and product owner approval for production APIs
- Exception process: All exceptions reviewed quarterly for potential removal or permanent acceptance