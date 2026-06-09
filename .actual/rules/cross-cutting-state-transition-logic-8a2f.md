# Adopt State Machine Pattern for Domain Workflow Modeling: State Transition Logic

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-STM-001** MUST NOT: State transition logic MUST NOT be scattered across multiple layers; it MUST be centralized in domain model or service layer.

### Verify

```bash
# Count state enumerations defined in codebase
grep -r "class.*State.*Enum\|class.*Status.*Enum" --include="*.py" | wc -l

# Count state transition methods
grep -r "def.*transition\|def.*change_state" --include="*.py" | wc -l

# Count state transition unit tests
pytest -k "test.*state.*transition" --collect-only | grep "<Function" | wc -l
```

**Accept when:**
- State enumerations are defined for domain entities with lifecycle complexity
- State transition methods exist with validation logic
- Unit tests cover valid and invalid state transitions
- State transition logic is centralized in domain model or service layer, not scattered across multiple layers

<enforcement>
Claude Code MUST NOT skip or defer verification of state machine pattern compliance during code review and architecture assessment.
</enforcement>