# Adopt State Machine Pattern for Domain Workflow Modeling: State Machines Implement

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

## Context

- The codebase exhibits a recurring pattern of state-driven domain logic across workflow engines, file lifecycle management, OAuth flows, and batch processing systems
- Multiple subsystems independently implement state transition logic with explicit state tracking, validation, and transition rules
- Pattern detected with 90.30% confidence across 4 distinct files spanning workflow orchestration, storage lifecycle, authentication flows, and batch operations
- State machine patterns provide explicit modeling of valid states, transitions, and business rules while preventing invalid state combinations
- The pattern signature (5ed4a7ca6fe2b823845d700b2f34a8d2) indicates consistent architectural approach to modeling domain entities with complex lifecycle requirements

## Problem Statement

Domain entities with complex lifecycles and multi-step workflows require explicit state management to ensure business rule consistency, prevent invalid transitions, and provide clear audit trails. Without a standardized state machine approach, teams may implement ad-hoc state logic leading to inconsistent validation, difficult debugging, and potential business rule violations.

## Decision

1. MAY: State machines MAY implement timeout or expiration logic for time-sensitive states

## Policy Block

- MAY State machines MAY implement timeout or expiration logic for time-sensitive states

In scope:
- Workflow orchestration engines with multi-step execution
- File and resource lifecycle management systems
- Authentication and authorization flows with multiple stages
- Batch processing systems with status tracking
- Domain entities with explicit lifecycle phases (draft, active, archived, etc.)
- Business processes requiring approval workflows or human intervention

Out of scope:
- Simple CRUD operations without lifecycle complexity
- Stateless request-response handlers
- Read-only query operations
- Configuration or reference data without state transitions
- Ephemeral objects that exist only within a single request scope

Exceptions:
- EXC-001: Legacy systems undergoing gradual migration may temporarily maintain dual state management approaches
- EXC-002: Third-party integrations that impose their own state models may use adapter pattern to bridge state representations

## Rationale

- Pattern detected across 4 files with 90.30% significance indicates this is an established architectural approach in the codebase
- State machine pattern provides explicit, testable representation of business rules and prevents invalid state combinations through compile-time or runtime validation
- Centralized state transition logic improves maintainability by providing single source of truth for lifecycle rules and simplifies debugging of workflow issues
- Evidence from workflow engines, OAuth flows, and file lifecycle systems demonstrates pattern applicability across diverse domain contexts

## Consequences

Positive:
- Explicit state modeling makes business rules visible and testable, reducing bugs from invalid state transitions
- Centralized transition logic simplifies maintenance and ensures consistent behavior across the application
- State machines provide natural audit trail through state change events, improving observability and compliance
- Pattern enables parallel development as state contracts define clear interfaces between components

Negative:
- Initial implementation requires more upfront design compared to ad-hoc state management
- State machine complexity can grow with number of states and transitions, requiring careful decomposition
- May introduce performance overhead if state validation involves complex business rule evaluation
- Developers unfamiliar with state machine patterns may face learning curve

## Alternatives

- Ad-hoc boolean flags or status strings without formal state machine structure (rejected)
  Rejected because: Leads to inconsistent validation, difficult debugging, and potential for invalid state combinations. Pattern detection shows codebase has moved beyond this approach.
  When valid: Only acceptable for trivial two-state scenarios (enabled/disabled) without transition rules
- Event sourcing with state derived from event stream (deferred)
  Rejected because: While event sourcing provides excellent audit trail, it adds significant complexity and infrastructure requirements. May be considered for critical workflows in future.
  When valid: Appropriate for domains requiring complete audit history, temporal queries, or event replay capabilities
- Workflow engine framework (Temporal, Airflow, etc.) for all stateful processes (rejected)
  Rejected because: External workflow engines add operational complexity and may be overkill for simple state machines. Evidence shows in-process state machines are preferred pattern.
  When valid: Consider for long-running workflows requiring distributed coordination, retries, or complex scheduling

## Risks

- State machine complexity may grow unbounded as business requirements evolve, leading to maintenance challenges
  Mitigation: Implement hierarchical state machines or decompose complex workflows into smaller state machines with clear boundaries. Conduct regular architecture reviews to identify refactoring opportunities.
  Owner: Engineering team with architecture review board oversight
- Inconsistent state machine implementations across teams may reduce pattern benefits
  Mitigation: Provide shared state machine library or base classes. Document reference implementations and conduct code reviews focusing on state machine patterns.
  Owner: Platform engineering team
- State persistence and concurrency issues may lead to race conditions in high-throughput scenarios
  Mitigation: Use optimistic locking or database-level constraints to ensure atomic state transitions. Implement idempotency for state transition operations.
  Owner: Engineering team

## Implementation Notes

- Start by defining state enumeration and transition matrix before implementing business logic
- Consider using enum types with associated behavior methods (strategy pattern) rather than switch statements
- Implement state transition validation as a separate concern that can be unit tested independently
- Use domain events to decouple state transitions from side effects (notifications, logging, etc.)
- For complex workflows, consider visualization tools to document state machine structure
- Leverage type systems to make invalid states unrepresentable where possible

## Continuation Context


Verify commands:
- grep -r "class.*State.*Enum\|class.*Status.*Enum" --include="*.py" | wc -l
- grep -r "def.*transition\|def.*change_state" --include="*.py" | wc -l
- pytest -k "test.*state.*transition" --collect-only | grep "<Function" | wc -l

Accept when:
- State enumerations are defined for domain entities with lifecycle complexity
- State transition methods exist with validation logic
- Unit tests cover valid and invalid state transitions

## Enforcement

- Verified by: Code review checklist includes state machine pattern verification for workflow and lifecycle features
- Verified by: Static analysis tools check for state enumeration usage and transition validation
- Verified by: Architecture review board reviews state machine designs for complex workflows during design phase
- Violation handling: Code review feedback requires refactoring of ad-hoc state management to formal state machine pattern
- Violation handling: Pull requests introducing new stateful workflows must include state diagram or transition matrix documentation
- Violation handling: Violations identified in production code are tracked as technical debt with prioritized remediation
- Exception process: Request exception through architecture review board with justification and impact analysis
- Exception process: Document exception in ADR exceptions registry with approval date and review timeline
- Exception process: Exceptions are reviewed quarterly to assess if circumstances have changed