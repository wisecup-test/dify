# Adopt Third-Party Script Lazy Loading with Window Object Initialization Pattern: Third Party Scripts

These rules are ALWAYS ACTIVE for all third-party script integrations, analytics SDKs, support widgets, tracking scripts, and any external JavaScript that requires window object initialization.

### Rules

- **R-3PS-001** MUST: Third-party scripts MUST be loaded asynchronously using dynamic script element injection to prevent render blocking.

### Verify

```bash
# Check for dynamic script element creation patterns
grep -r "createElement.*script" web/app/components --include="*.tsx" --include="*.ts" | grep -v "node_modules"

# Check for window object extensions with third-party service names
grep -r "window\." web/app/components --include="*.tsx" --include="*.ts" | grep -E "(zendesk|ga|PartnerStack)" | grep -v "node_modules"

# Verify TypeScript window interface declarations have no errors
npm run type-check 2>&1 | grep -i "window" | grep -i "error" && echo "Type errors found" || echo "No window type errors"
```

**Accept when:**
- All third-party script integrations use dynamic script element creation with async attribute
- Window object extensions have corresponding TypeScript interface declarations without type errors
- Script loading logic includes duplicate initialization checks before creating new script elements

<enforcement>
Claude Code MUST NOT skip or defer verification. TypeScript compilation failures for missing window interface declarations block deployment. Performance regression alerts trigger investigation and potential rollback of changes.
</enforcement>