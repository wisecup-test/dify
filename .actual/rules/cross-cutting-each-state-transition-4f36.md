# Adopt State Machine Pattern for Domain Workflow Modeling: Each State Transition

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-STATE-001** MUST: Each state transition MUST be atomic and maintain domain invariants throughout the transition.

### Verify

```bash
# Check for state enumeration definitions
grep -r "class.*State.*Enum\|class.*Status.*Enum" --include="*.py" | wc -l

# Check for state transition methods
grep -r "def.*transition\|def.*change_state" --include="*.py" | wc -l

# Check for state transition tests
pytest -k "test.*state.*transition" --collect-only | grep "<Function" | wc -l
```

**Accept when:**
- State enumerations are defined for domain entities with lifecycle complexity
- State transition methods exist with validation logic
- Unit tests cover valid and invalid state transitions
- State transitions maintain domain invariants and are atomic

<enforcement>
Claude Code MUST NOT skip or defer verification of state machine pattern compliance for stateful domain entities.
</enforcement>
