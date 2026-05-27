# Adopt State Machine Pattern for Domain Workflow Modeling: State Machines Separate

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-SM-001** SHOULD: State machines SHOULD separate state representation from transition logic using dedicated state handler or strategy classes.

### Verify

```bash
# Count state enumerations in the codebase
grep -r "class.*State.*Enum\|class.*Status.*Enum" --include="*.py" | wc -l

# Count state transition methods
grep -r "def.*transition\|def.*change_state" --include="*.py" | wc -l

# Count state transition tests
pytest -k "test.*state.*transition" --collect-only | grep "<Function" | wc -l
```

**Accept when:**
- State enumerations are defined for domain entities with lifecycle complexity
- State transition methods exist with validation logic
- Unit tests cover valid and invalid state transitions
- State transition logic is separated from state representation using dedicated handler or strategy classes

<enforcement>
Clause Code MUST NOT skip or defer verification of state machine separation patterns in workflow, lifecycle, and stateful domain implementations.
</enforcement>