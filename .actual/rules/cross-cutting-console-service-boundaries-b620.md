# Adopt oRPC for Generated API Service Boundaries: Console Service Boundaries

These rules are ALWAYS ACTIVE for all console API service boundary definitions in the console API layer, including all console API endpoints exposed to frontend clients, service boundaries between packages/contracts and consuming services, type definitions for request/response payloads in console APIs, and generated TypeScript contract files in packages/contracts/generated/.

### Rules

- **R-ORPC-001** MUST: All console API service boundaries MUST be defined using oRPC framework with generated TypeScript contracts.

### Verify

```bash
# Verify at least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/
find packages/contracts/generated/api/console -name 'orpc.gen.ts' | wc -l | grep -q '^[4-9]\|^[1-9][0-9]'

# Verify all console API service boundaries have corresponding generated contract files
grep -r 'orpc.gen.ts' packages/contracts/generated/api/console/ | grep -v node_modules

# Verify specific service boundaries exist
test -f packages/contracts/generated/api/console/app/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/agents/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/apps/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/all-workspaces/orpc.gen.ts
```

**Accept when:**
- At least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/ directory structure
- All console API service boundaries (app, apps, agents, all-workspaces) have corresponding generated contract files
- Generated files contain valid TypeScript with oRPC type definitions and no manual modifications
- Generated contracts are up-to-date with source definitions (verified by CI pipeline)
- No manual edits exist in orpc.gen.ts files (verified by pre-commit hooks)

<enforcement>
Claude Code MUST NOT skip or defer verification. All console API service boundaries MUST use oRPC framework with generated TypeScript contracts. Violations block CI builds and require exception approval from Tech Lead and Architecture Review Board.
</enforcement>