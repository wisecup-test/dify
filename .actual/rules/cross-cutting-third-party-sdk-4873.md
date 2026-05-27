# Adopt Third-Party Script Lazy Loading with Window Object Initialization Pattern: Third Party Sdk

These rules are ALWAYS ACTIVE for all third-party SDK integrations, analytics scripts, support widgets, tracking scripts, and any external JavaScript that requires window object initialization.

### Rules

- **R-TPSK-001** MUST: Third-party SDK initialization functions MUST be attached to the window object with proper TypeScript interface declarations.

### Verify

```bash
# Check for dynamic script element creation with async attribute
grep -r "createElement.*script" web/app/components --include="*.tsx" --include="*.ts" | grep -v "node_modules"

# Check for window object extensions with third-party SDKs
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