# Enforce External Client Boundary Isolation in Internal APIs: Internal Use Adapter

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all internal API implementations that interact with external clients or services.

## Context

- Internal APIs frequently need to interact with external clients, third-party services, and cloud providers (e.g., AWS S3, OpenTelemetry parsers, plugin services)
- The codebase demonstrates a consistent pattern of establishing clear boundaries between internal API logic and external client interactions across 7 files with 91.57% confidence
- Test files, storage extensions, feature services, and telemetry parsers all exhibit this boundary isolation pattern, indicating a system-wide architectural concern
- Without explicit boundaries, internal APIs risk tight coupling to external dependencies, making testing, mocking, and service substitution difficult
- The pattern appears in both production code (storage, services, parsers) and test infrastructure (unit tests, integration tests), suggesting intentional architectural design

## Problem Statement

Internal APIs that directly couple to external clients create maintenance burdens, testing complexity, and deployment fragility. When internal business logic is tightly bound to external service implementations, teams cannot easily mock dependencies for testing, swap providers, or isolate failures. This pattern addresses the need for a consistent approach to managing external client boundaries in internal API design.

## Decision

1. SHOULD: Internal APIs SHOULD use adapter or wrapper patterns to encapsulate external client-specific implementation details

## Policy Block

- SHOULD Internal APIs SHOULD use adapter or wrapper patterns to encapsulate external client-specific implementation details

In scope:
- All internal API modules that interact with cloud storage services (AWS S3, Azure Blob, GCS)
- Service layer components that call external third-party APIs or plugins
- Telemetry and observability parsers that send data to external systems (OpenTelemetry, monitoring services)
- Workflow engines and agent nodes that invoke external services
- Feature services that depend on external feature flag providers or configuration services

Out of scope:
- Public-facing REST or GraphQL API endpoints (covered by separate API gateway patterns)
- Direct database access layers (covered by data access patterns)
- Internal service-to-service communication within the same deployment boundary
- Static utility functions that do not maintain state or connections to external services

Exceptions:
- EXC-001: Prototype or proof-of-concept code explicitly marked as non-production
- EXC-002: Performance-critical paths where abstraction overhead is measured and documented as unacceptable

## Rationale

- Pattern detected across 7 files with 91.57% confidence indicates this is an established architectural practice worth codifying
- Evidence spans multiple domains (storage, services, telemetry, workflow, testing) suggesting system-wide applicability and intentional design
- Boundary isolation enables independent testing, as demonstrated by the presence of this pattern in both unit and integration test files
- Decoupling internal APIs from external clients reduces vendor lock-in and enables easier migration between cloud providers or third-party services

## Consequences

Positive:
- Improved testability through mockable external dependencies, reducing test execution time and eliminating external service dependencies in CI/CD
- Enhanced maintainability by isolating external client changes to boundary adapters, preventing ripple effects through internal business logic
- Increased flexibility to swap external service providers without refactoring core internal API implementations
- Better failure isolation and resilience through centralized error handling at boundary layers

Negative:
- Additional abstraction layers increase initial development time and code complexity
- Potential performance overhead from indirection, though typically negligible compared to network I/O
- Requires discipline to maintain boundary abstractions and avoid leaking external client details into internal APIs
- May lead to over-engineering for simple external integrations that are unlikely to change

## Alternatives

- Direct instantiation of external clients within internal API methods without abstraction layers (rejected)
  Rejected because: Creates tight coupling, makes unit testing impossible without hitting real external services, and prevents provider substitution
  When valid: Never recommended for production code; only acceptable in throwaway scripts or one-off utilities
- Use service locator pattern to resolve external clients at runtime (rejected)
  Rejected because: Service locator is an anti-pattern that hides dependencies, makes testing harder, and obscures the dependency graph
  When valid: Only in legacy codebases where dependency injection framework is not available
- Implement a generic external client gateway that handles all external interactions through a single interface (deferred)
  Rejected because: May be too generic and lose type safety; requires further investigation
  When valid: Could be reconsidered if the number of external clients grows significantly and common patterns emerge

## Risks

- Developers may bypass boundary abstractions under time pressure, creating inconsistent patterns across the codebase
  Mitigation: Implement automated linting rules to detect direct external client instantiation; include boundary pattern compliance in code review checklist
  Owner: Engineering team leads
- Over-abstraction may lead to complex adapter hierarchies that are harder to understand than direct integration
  Mitigation: Establish clear guidelines for when abstraction is required vs. optional; conduct periodic architecture reviews to identify over-engineering
  Owner: Architecture review board
- Boundary abstractions may not adequately capture provider-specific features, forcing workarounds or leaky abstractions
  Mitigation: Design abstractions around use cases rather than provider APIs; allow provider-specific extensions through well-defined extension points
  Owner: API design working group

## Implementation Notes

- Use dependency injection frameworks (e.g., Python's dependency-injector, Java Spring) to manage external client lifecycle and injection
- Create interface definitions or abstract base classes for each external client type (storage, telemetry, plugins) before implementing concrete adapters
- Establish naming conventions for boundary components (e.g., *Client for interfaces, *Adapter for implementations, *Mock for test doubles)
- Document the external client boundary pattern in team onboarding materials and provide code templates or scaffolding tools

## Continuation Context


Verify commands:
- grep -r 'import boto3' api/services/ api/core/ --exclude-dir=tests | grep -v 'storage/' | wc -l | test $(cat) -eq 0
- grep -r 'class.*Storage.*:' api/extensions/storage/ | grep -E '(ABC|Protocol|Interface)' | wc -l | test $(cat) -gt 0
- pytest api/tests/unit_tests/ -k 'test_' --collect-only | grep -E '(mock|stub|fake)' | wc -l | test $(cat) -gt 5

Accept when:
- No direct external client imports (boto3, requests, external SDKs) found in internal service or core business logic modules outside designated adapter/extension directories
- All storage, telemetry, and plugin extension modules define abstract interfaces or protocols that internal APIs depend upon
- Unit tests demonstrate successful mocking of external clients without requiring actual external service connectivity

## Enforcement

- Verified by: Automated static analysis in CI pipeline checking for direct external client imports in restricted directories
- Verified by: Code review checklist requiring verification of boundary abstractions for any new external integrations
- Verified by: Architecture review for any PR introducing new external service dependencies
- Violation handling: CI pipeline fails if static analysis detects direct external client usage in internal API modules
- Violation handling: PR blocked until boundary abstraction is implemented and unit tests with mocks are provided
- Violation handling: Existing violations tracked as technical debt tickets and prioritized in sprint planning
- Exception process: Developer submits exception request with performance benchmarks or technical justification to tech lead
- Exception process: Tech lead reviews and either approves with documentation requirements or requests refactoring
- Exception process: Approved exceptions documented in code comments with ADR reference and expiration date for re-evaluation