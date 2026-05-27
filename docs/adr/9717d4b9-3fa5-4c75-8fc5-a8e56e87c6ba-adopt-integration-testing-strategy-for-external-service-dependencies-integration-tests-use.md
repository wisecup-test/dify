# Adopt Integration Testing Strategy for External Service Dependencies: Integration Tests Use

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The codebase integrates with multiple external services including AWS S3 storage, OpenTelemetry parsers for LLM and retrieval operations, feature services, and browser-based voice input components
- These integrations span both backend API services (Python) and frontend components (TypeScript/React), requiring consistent testing approaches across technology stacks
- External service dependencies introduce complexity in testing due to network latency, service availability, authentication requirements, and state management challenges
- Pattern detected across 5 files with 91.64% confidence indicates a systematic approach to handling integration testing for external dependencies
- The testing.integration facet suggests a deliberate architectural decision to validate component interactions with external systems rather than relying solely on unit tests

## Problem Statement

How should the system validate interactions with external services (cloud storage, observability platforms, feature flags, browser APIs) to ensure reliability while maintaining fast feedback cycles and avoiding flaky tests caused by network dependencies and external service variability?

## Decision

1. SHOULD: Integration tests SHOULD use test doubles (mocks, stubs, fakes) for external services in standard CI runs to ensure fast feedback and reliability

## Policy Block

- SHOULD Integration tests SHOULD use test doubles (mocks, stubs, fakes) for external services in standard CI runs to ensure fast feedback and reliability

In scope:
- AWS S3 storage operations (upload, download, delete, list)
- OpenTelemetry parser integrations for LLM and retrieval tracing
- Feature service API calls and configuration management
- Browser API integrations (voice input, media devices)
- External authentication and authorization services
- Third-party API integrations requiring network calls

Out of scope:
- Pure unit tests with no external dependencies
- Internal service-to-service calls within the same deployment boundary
- Database integration tests (covered by separate data layer testing strategy)
- UI component tests that don't interact with external services
- Performance and load testing (covered by separate performance testing strategy)

Exceptions:
- EX-001: Legacy code without existing integration test coverage may defer integration tests until the next major refactoring
- EX-002: Prototype or experimental features in early development phases may skip integration tests until design is validated

## Rationale

- Pattern detected across 5 diverse files (storage, services, observability, UI) with 91.64% confidence indicates a consistent architectural approach to integration testing
- External service dependencies are critical failure points that cannot be adequately validated through unit tests alone, requiring integration-level verification
- Separating integration tests from unit tests enables faster development feedback loops while maintaining comprehensive validation in CI/CD pipelines
- Testing error handling and retry logic at the integration level catches real-world failure scenarios that mocks cannot simulate effectively

## Consequences

Positive:
- Increased confidence in external service integrations through systematic validation of interaction contracts and error handling
- Faster detection of breaking changes in external service APIs or behavior before production deployment
- Improved system reliability through comprehensive testing of failure scenarios, timeouts, and retry logic
- Better developer experience with clear separation between fast unit tests and slower integration tests

Negative:
- Increased CI/CD pipeline complexity requiring separate test execution strategies for unit vs integration tests
- Additional infrastructure requirements for maintaining test doubles and staging environments with real external services
- Potential for test flakiness if integration tests are not properly isolated or if external services have reliability issues
- Higher maintenance burden as integration tests must be updated when external service contracts change

## Alternatives

- Unit tests only with comprehensive mocking of all external dependencies (rejected)
  Rejected because: Mocks cannot validate actual service behavior, API contract changes, or real-world failure scenarios, leading to false confidence and production issues
  When valid: Only appropriate for pure business logic with no external dependencies
- End-to-end tests only against production-like environments (rejected)
  Rejected because: E2E tests are too slow for rapid feedback, expensive to maintain, and difficult to debug when failures occur across multiple service boundaries
  When valid: Useful as a final validation layer but insufficient as the primary testing strategy
- Contract testing with consumer-driven contracts (CDC) (deferred)
  Rejected because: Not rejected but deferred as a complementary approach; requires additional tooling and team coordination
  When valid: Valuable addition for services with multiple consumers requiring API stability guarantees

## Risks

- Integration tests may become flaky due to external service instability, network issues, or rate limiting, reducing developer trust in test results
  Mitigation: Implement retry logic, circuit breakers, and health checks in test infrastructure; use test doubles for standard CI runs and reserve real service tests for dedicated integration pipelines
  Owner: Engineering team with DevOps support
- Test maintenance burden increases as external service APIs evolve, requiring frequent updates to integration test fixtures and assertions
  Mitigation: Establish API versioning contracts with external services; use contract testing to detect breaking changes early; maintain shared test fixture libraries
  Owner: Engineering team with architecture review
- Integration test execution time may slow down CI/CD pipelines, impacting developer productivity and deployment velocity
  Mitigation: Run integration tests in parallel; use selective test execution based on code changes; maintain separate fast feedback loop with unit tests
  Owner: DevOps and engineering team

## Implementation Notes

- Use test markers or tags (e.g., @integration, @external) to clearly distinguish integration tests from unit tests in test runners
- Create dedicated test fixtures and factories for external service test data that can be reused across integration tests
- Implement test helpers for common integration patterns like service authentication, connection setup, and cleanup operations
- Configure CI/CD pipelines with separate stages: fast unit tests on every commit, integration tests with mocks on PR validation, full integration tests with real services on merge to main
- Document integration test setup requirements including environment variables, service credentials, and network access in README files
- Consider using testcontainers or similar tools for services that can be containerized to provide consistent test environments

## Continuation Context


Verify commands:
- grep -r '@integration\|@external\|pytest.mark.integration' tests/ --include='*.py' | wc -l
- find tests/ -name '*integration*' -o -name '*external*' | wc -l
- grep -r 'mock.*aws\|mock.*s3\|mock.*otel' tests/ --include='*.py' --include='*.ts' --include='*.tsx' | wc -l

Accept when:
- Integration test markers or dedicated test directories are present in the codebase
- At least one integration test exists for each external service dependency (AWS S3, OpenTelemetry, feature service, voice input)
- Test mocks or test doubles are implemented for external services to enable fast CI execution
- CI/CD pipeline configuration shows separate execution of unit and integration test suites

## Enforcement

- Verified by: Automated CI/CD pipeline checks that verify integration test coverage for new external service integrations
- Verified by: Code review checklist requiring integration tests for any PR that adds or modifies external service interactions
- Verified by: Static analysis tools that detect external service calls without corresponding integration test coverage
- Violation handling: PR builds fail if new external service integrations are added without corresponding integration tests
- Violation handling: Code review process blocks merging until integration test requirements are satisfied or exception is approved
- Violation handling: Quarterly architecture reviews identify gaps in integration test coverage and create remediation plans
- Exception process: Developer submits exception request to tech lead with justification and technical debt tracking ticket
- Exception process: Tech lead reviews exception against policy criteria (legacy code, prototype status) and approves or rejects
- Exception process: Approved exceptions are documented in code comments with ticket references and reviewed in sprint planning
- Exception process: All exceptions are reviewed quarterly to assess whether they can be resolved or need extension