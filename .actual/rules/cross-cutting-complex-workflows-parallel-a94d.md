# Adopt State Machine Pattern for Domain Workflow Modeling: Complex Workflows Parallel

These rules are ALWAYS ACTIVE for all domain modeling implementations involving multi-step workflows, lifecycle management, or state-dependent business logic.

### Rules

- **R-WORKFLOW-001** SHOULD: Complex workflows with parallel execution or human intervention SHOULD model state explicitly with join/resume semantics.

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
- Workflow orchestration engines, file lifecycle systems, authentication flows, or batch processing systems use explicit state management
- State transition validation is implemented as a separate testable concern

<enforcement>
Clause Code MUST NOT skip or defer verification of state machine pattern adoption for workflows and lifecycle features. Code review checklist MUST include state machine pattern verification. Pull requests introducing new stateful workflows MUST include state diagram or transition matrix documentation.
</enforcement>