# Adopt oRPC for Generated API Service Boundaries: Each Service Boundary

These rules are ALWAYS ACTIVE for all console API endpoints exposed to frontend clients, service boundaries between packages/contracts and consuming services, and generated TypeScript contract files in packages/contracts/generated/.

### Rules

- **R-ORPC-001** MUST: Each service boundary MUST generate an orpc.gen.ts file containing the complete type-safe contract definition.

### Verify

```bash
# Verify at least 4 orpc.gen.ts files exist in the console API directory structure
find packages/contracts/generated/api/console -name 'orpc.gen.ts' | wc -l | grep -q '^[4-9]\|^[1-9][0-9]'

# Verify orpc.gen.ts files are present in the generated directory
grep -r 'orpc.gen.ts' packages/contracts/generated/api/console/ | grep -v node_modules

# Verify all core service boundaries have generated contract files
test -f packages/contracts/generated/api/console/app/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/agents/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/apps/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/all-workspaces/orpc.gen.ts
```

**Accept when:**
- At least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/ directory structure
- All console API service boundaries (app, apps, agents, all-workspaces) have corresponding generated contract files
- Generated files contain valid TypeScript with oRPC type definitions and no manual modifications
- Generated contracts are up-to-date with source definitions and verified by CI pipeline

<enforcement>
Claude Code MUST NOT skip or defer verification. Generated contract files must be validated before accepting any changes to API service boundaries.
</enforcement>