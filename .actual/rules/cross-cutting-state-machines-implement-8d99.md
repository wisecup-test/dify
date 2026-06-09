# Adopt State Machine Pattern for Domain Workflow Modeling: State Machines Implement

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-SM-001** MAY: State machines MAY implement timeout or expiration logic for time-sensitive states.

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

<enforcement>
Claude Code MUST NOT skip or defer verification. Code review checklist MUST include state machine pattern verification for workflow and lifecycle features. Pull requests introducing new stateful workflows MUST include state diagram or transition matrix documentation.
</enforcement>