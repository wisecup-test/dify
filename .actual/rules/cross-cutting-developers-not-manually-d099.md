# Adopt oRPC for Generated API Service Boundaries: Developers Not Manually

These rules are ALWAYS ACTIVE for all console API endpoints exposed to frontend clients, service boundaries between packages/contracts and consuming services, type definitions for request/response payloads in console APIs, and generated TypeScript contract files in packages/contracts/generated/.

### Rules

- **R-ORPC-001** MUST_NOT: Developers MUST NOT manually edit generated orpc.gen.ts files; all changes must be made to source definitions and regenerated.

### Verify

```bash
# Verify at least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/
find packages/contracts/generated/api/console -name 'orpc.gen.ts' | wc -l | grep -q '^[4-9]\|^[1-9][0-9]'

# Verify orpc.gen.ts files are present in the directory structure
grep -r 'orpc.gen.ts' packages/contracts/generated/api/console/ | grep -v node_modules

# Verify all required service boundary files exist
test -f packages/contracts/generated/api/console/app/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/agents/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/apps/orpc.gen.ts && \
test -f packages/contracts/generated/api/console/all-workspaces/orpc.gen.ts
```

**Accept when:**
- At least 4 orpc.gen.ts files exist in packages/contracts/generated/api/console/ directory structure
- All console API service boundaries (app, apps, agents, all-workspaces) have corresponding generated contract files
- Generated files contain valid TypeScript with oRPC type definitions and no manual modifications
- Generated contracts are verified by CI pipeline to be up-to-date with source definitions

<enforcement>
Claude Code MUST NOT skip or defer verification. All API service boundary definitions MUST follow oRPC code generation patterns. Manual edits to orpc.gen.ts files are violations that must be flagged during code review.
</enforcement>