# Adopt State Machine Pattern for Domain Workflow Modeling: Domain Entities Lifecycle

These rules are ALWAYS ACTIVE for all domain entities with lifecycle states, multi-step workflows, state-dependent business logic, and complex lifecycle management across workflow orchestration, file lifecycle management, OAuth flows, and batch processing systems.

### Rules

- **R-LIFECYCLE-001** MUST: Domain entities with lifecycle states MUST explicitly define all valid states as enumerated types or constants.

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
- State transition validation is implemented as a separate testable concern
- Domain events are used to decouple state transitions from side effects

<enforcement>
Claude Code MUST NOT skip or defer verification of state machine pattern compliance for domain entities with lifecycle states. All stateful domain entities must be reviewed against these rules during code review and architecture validation.
</enforcement>