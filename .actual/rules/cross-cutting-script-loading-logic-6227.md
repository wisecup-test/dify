# Adopt Third-Party Script Lazy Loading with Window Object Initialization Pattern: Script Loading Logic

These rules are ALWAYS ACTIVE for all third-party script integrations, analytics SDKs, support widgets, tracking scripts, and any external JavaScript that requires window object initialization.

### Rules

- **R-SCRIPT-001** MUST: Script loading logic MUST check for existing window object properties to prevent duplicate initialization.

### Verify

```bash
# Check for dynamic script element creation with async attribute
grep -r "createElement.*script" web/app/components --include="*.tsx" --include="*.ts" | grep -v "node_modules"

# Check for window object extensions related to third-party services
grep -r "window\." web/app/components --include="*.tsx" --include="*.ts" | grep -E "(zendesk|ga|PartnerStack)" | grep -v "node_modules"

# Verify TypeScript window interface declarations
npm run type-check 2>&1 | grep -i "window" | grep -i "error" && echo "Type errors found" || echo "No window type errors"
```

**Accept when:**
- All third-party script integrations use dynamic script element creation with async attribute
- Window object extensions have corresponding TypeScript interface declarations without type errors
- Script loading logic includes duplicate initialization checks before creating new script elements

<enforcement>
Claude Code MUST NOT skip or defer verification. TypeScript compilation failures for missing window interface declarations block deployment. Pull requests with synchronous third-party script loads are flagged for review and revision.
</enforcement>