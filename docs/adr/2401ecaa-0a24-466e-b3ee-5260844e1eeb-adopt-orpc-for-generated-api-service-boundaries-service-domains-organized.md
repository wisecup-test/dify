# Adopt oRPC for Generated API Service Boundaries: Service Domains Organized

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all API service boundary definitions in the console API layer.

## Context

- The codebase requires a standardized approach to defining API service boundaries between frontend console and backend services
- Multiple API domains exist (app, all-workspaces, apps, agents) that need consistent contract definitions
- Type-safe communication between services is critical to prevent runtime errors and maintain API contract integrity
- Generated code patterns indicate a deliberate choice to use code generation for API contracts rather than manual definitions
- The oRPC framework has been adopted across 4 distinct API service boundaries with consistent patterns

## Problem Statement

Without a standardized framework for defining service boundaries, API contracts become inconsistent, type safety is compromised, and maintaining synchronization between client and server implementations becomes error-prone and time-consuming. Manual API contract maintenance leads to drift between documentation, implementation, and client usage.

## Decision

1. SHOULD: Service domains SHOULD be organized by functional area (app, apps, agents, workspaces) to maintain clear separation of concerns

## Policy Block

- SHOULD Service domains SHOULD be organized by functional area (app, apps, agents, workspaces) to maintain clear separation of concerns

In scope:
- All console API endpoints exposed to frontend clients
- Service boundaries between packages/contracts and consuming services
- Type definitions for request/response payloads in console APIs
- Generated TypeScript contract files in packages/contracts/generated/

Out of scope:
- Internal service-to-service communication not exposed through console API
- Third-party API integrations and external service contracts
- Database schemas and ORM definitions
- WebSocket or streaming API definitions (unless oRPC supports them)
- Legacy API endpoints scheduled for deprecation

Exceptions:
- EXC-001: Prototyping new API concepts that are not yet stable enough for contract generation
- EXC-002: Integrating with external systems that provide their own contract definitions (e.g., OpenAPI specs)

## Rationale

- Pattern detected across 4 distinct service boundaries (app, all-workspaces, apps, agents) with 90% confidence indicates deliberate architectural choice
- Code generation ensures single source of truth for API contracts, eliminating drift between client and server
- oRPC provides type-safe RPC-style APIs with TypeScript, reducing runtime errors and improving developer experience
- Centralized contract definitions in packages/contracts enable contract-first development and easier API versioning

## Consequences

Positive:
- Type safety across service boundaries eliminates entire classes of runtime errors
- Automated contract generation reduces manual maintenance burden and human error
- Consistent API patterns across all console services improve developer productivity and onboarding
- Contract-first approach enables parallel development of client and server implementations
- Centralized contracts facilitate API versioning and backward compatibility management

Negative:
- Introduces build-time dependency on code generation tooling, adding complexity to build pipeline
- Developers must learn oRPC framework conventions and tooling
- Generated code increases repository size and may cause merge conflicts if not properly managed
- Debugging issues may require understanding both source definitions and generated code
- Framework lock-in makes migration to alternative approaches more costly

## Alternatives

- Manual TypeScript interface definitions for API contracts (rejected)
  Rejected because: Manual definitions are error-prone, lead to drift between client and server, and require duplicate maintenance effort
  When valid: Only for simple internal APIs with single consumer and no versioning requirements
- OpenAPI/Swagger with generated TypeScript clients (rejected)
  Rejected because: OpenAPI is REST-focused and less ergonomic for RPC-style APIs; oRPC provides better TypeScript integration
  When valid: When exposing public REST APIs to external consumers who expect OpenAPI documentation
- GraphQL for all API boundaries (rejected)
  Rejected because: GraphQL adds complexity and overhead for simple RPC-style operations; oRPC is lighter weight for internal APIs
  When valid: When flexible querying and field selection are critical requirements for the API

## Risks

- oRPC framework becomes unmaintained or has breaking changes in future versions
  Mitigation: Monitor framework health, maintain version pinning, and document migration strategy. Consider contributing to oRPC if it becomes critical dependency.
  Owner: Platform Engineering Team
- Generated code conflicts in version control when multiple developers modify contracts simultaneously
  Mitigation: Implement pre-commit hooks to regenerate contracts, use clear branching strategy, and document conflict resolution procedures
  Owner: Engineering Team
- Build pipeline failures due to contract generation errors block all development
  Mitigation: Implement robust error handling in generation scripts, provide clear error messages, and maintain fallback mechanisms for local development
  Owner: DevOps Team

## Implementation Notes

- Ensure code generation scripts are idempotent and can be run safely multiple times without side effects
- Add pre-commit hooks to validate that generated files are up-to-date with source definitions
- Document the contract definition process and generation commands in project README and developer onboarding materials
- Consider implementing contract versioning strategy (e.g., /v1/, /v2/ paths) for backward compatibility
- Set up CI checks to validate that generated contracts match source definitions and fail builds on mismatch

## Continuation Context


Verify commands:
- find packages/contracts/generated/api/console -name 'orpc.gen.ts' | wc -l | grep -q '^[4-9]\|^[1-9][0-9]'
- grep -r 'orpc.gen.ts' packages/contracts/generated/api/console/ | grep -v node_modules
- test -f packages/contracts/generated/api/console/app/orpc.gen.ts && test -f packages/contracts/generated/api/console/agents/orpc.gen.ts

Accept when:
- At least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/ directory structure
- All console API service boundaries (app, apps, agents, all-workspaces) have corresponding generated contract files
- Generated files contain valid TypeScript with oRPC type definitions and no manual modifications

## Enforcement

- Verified by: Automated CI pipeline checks verify generated contracts are up-to-date with source definitions
- Verified by: Code review process ensures new API endpoints follow oRPC contract generation pattern
- Verified by: Pre-commit hooks validate that orpc.gen.ts files are not manually edited
- Verified by: Static analysis tools detect direct imports of non-generated API types
- Violation handling: CI build fails if generated contracts are out of sync with source definitions
- Violation handling: Pull requests with manual edits to orpc.gen.ts files are automatically flagged for review
- Violation handling: Runtime type validation errors are logged and monitored for contract violations
- Violation handling: Quarterly architecture reviews audit API boundaries for compliance with oRPC patterns
- Exception process: Developer submits exception request to Tech Lead with justification and timeline
- Exception process: Architecture review board evaluates exception for impact on system consistency
- Exception process: Approved exceptions are documented in ADR amendments with migration plan and expiration date
- Exception process: Exceptions are reviewed quarterly and must be renewed or migrated to compliant approach