# Adopt oRPC for Generated API Service Boundaries: Generated Contracts Placed

These rules are ALWAYS ACTIVE for all console API endpoints exposed to frontend clients and service boundaries between packages/contracts and consuming services.

### Rules

- **R-ORPC-001** MUST: Generated API contracts MUST be placed in the packages/contracts/generated/api/console/ directory structure organized by service domain.

### Verify

```bash
# Verify at least 4 orpc.gen.ts files exist in the correct directory structure
find packages/contracts/generated/api/console -name 'orpc.gen.ts' | wc -l | grep -q '^[4-9]\|^[1-9][0-9]'

# Verify generated files are present in the directory
grep -r 'orpc.gen.ts' packages/contracts/generated/api/console/ | grep -v node_modules

# Verify all required service boundaries have generated contracts
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
Claude Code MUST NOT skip or defer verification. Generated API contracts MUST comply with R-ORPC-001 placement requirements. Pre-commit hooks MUST validate that orpc.gen.ts files are not manually edited. CI builds MUST fail if generated contracts are out of sync with source definitions.
</enforcement>