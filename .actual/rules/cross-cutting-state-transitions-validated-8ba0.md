# Adopt State Machine Pattern for Domain Workflow Modeling: State Transitions Validated

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-STATE-001** MUST: State transitions MUST be validated against a defined set of allowed transitions before persistence.

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
- State transitions are validated before any persistence operation

<enforcement>
Claude Code MUST NOT skip or defer verification of state transition validation. All stateful domain entities must demonstrate explicit state machine patterns with validated transitions before persistence.
</enforcement>