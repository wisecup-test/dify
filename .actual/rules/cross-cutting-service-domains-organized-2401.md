# Adopt oRPC for Generated API Service Boundaries: Service Domains Organized

These rules are ALWAYS ACTIVE for all console API endpoints exposed to frontend clients, service boundaries between packages/contracts and consuming services, type definitions for request/response payloads in console APIs, and generated TypeScript contract files in packages/contracts/generated/.

### Rules

- **R-ORPC-001** SHOULD: Service domains SHOULD be organized by functional area (app, apps, agents, workspaces) to maintain clear separation of concerns.

### Verify

```bash
# Verify at least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/
find packages/contracts/generated/api/console -name 'orpc.gen.ts' | wc -l | grep -q '^[4-9]\|^[1-9][0-9]'

# Verify all console API service boundaries have corresponding generated contract files
grep -r 'orpc.gen.ts' packages/contracts/generated/api/console/ | grep -v node_modules

# Verify specific service boundary files exist
test -f packages/contracts/generated/api/console/app/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/agents/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/apps/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/all-workspaces/orpc.gen.ts
```

**Accept when:**
- At least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/ directory structure
- All console API service boundaries (app, apps, agents, all-workspaces) have corresponding generated contract files
- Generated files contain valid TypeScript with oRPC type definitions and no manual modifications
- Code generation scripts are idempotent and can be run safely multiple times without side effects
- Pre-commit hooks validate that generated files are up-to-date with source definitions

<enforcement>
Claude Code MUST NOT skip or defer verification. Generated contracts must be validated against source definitions before accepting any changes to API service boundaries. CI build MUST fail if generated contracts are out of sync with source definitions.
</enforcement>