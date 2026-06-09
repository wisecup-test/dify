# Adopt State Machine Pattern for Domain Workflow Modeling: State Transitions Emit

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-STATE-001** SHOULD: State transitions SHOULD emit domain events for audit logging and downstream system integration.

### Verify

```bash
# Check for state enumerations in domain entities
grep -r "class.*State.*Enum\|class.*Status.*Enum" --include="*.py" | wc -l

# Check for state transition methods
grep -r "def.*transition\|def.*change_state" --include="*.py" | wc -l

# Check for state transition unit tests
pytest -k "test.*state.*transition" --collect-only | grep "<Function" | wc -l
```

**Accept when:**
- State enumerations are defined for domain entities with lifecycle complexity
- State transition methods exist with validation logic
- Unit tests cover valid and invalid state transitions
- State transitions emit domain events for audit logging and downstream integration

<enforcement>
Clause Code MUST NOT skip or defer verification of state machine patterns in workflow and lifecycle features. Code review checklist MUST include state machine pattern verification. Pull requests introducing new stateful workflows MUST include state diagram or transition matrix documentation.
</enforcement>