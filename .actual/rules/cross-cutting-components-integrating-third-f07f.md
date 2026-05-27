# Adopt Third-Party Script Lazy Loading with Window Object Initialization Pattern: Components Integrating Third

These rules are ALWAYS ACTIVE for all components integrating third-party scripts (analytics, support widgets, tracking scripts, marketing automation) that require client-side initialization via the window object.

### Rules

- **R-THIRD-001** SHOULD: Components integrating third-party scripts SHOULD use React hooks (useEffect) to manage script lifecycle and cleanup.
- **R-THIRD-002** MUST: All third-party script integrations MUST use dynamic script element creation with async attribute to prevent render-blocking.
- **R-THIRD-003** MUST: Window object extensions MUST have corresponding TypeScript interface declarations without type errors.
- **R-THIRD-004** MUST: Script loading logic MUST include duplicate initialization checks before creating new script elements.
- **R-THIRD-005** SHOULD: Reusable utility functions or hooks SHOULD be created for common script loading patterns to reduce code duplication.
- **R-THIRD-006** SHOULD: TypeScript window interface extensions SHOULD be documented in a centralized types file (e.g., window.d.ts).
- **R-THIRD-007** SHOULD: Feature flags SHOULD be implemented for third-party integrations to enable/disable services without code changes.
- **R-THIRD-008** SHOULD: Comprehensive error logging SHOULD be added for script load failures to aid debugging in production environments.

### Verify

```bash
# Check for script element creation patterns
grep -r "createElement.*script" web/app/components --include="*.tsx" --include="*.ts" | grep -v "node_modules"

# Check for window object usage with third-party services
grep -r "window\." web/app/components --include="*.tsx" --include="*.ts" | grep -E "(zendesk|ga|PartnerStack)" | grep -v "node_modules"

# Verify TypeScript window type declarations
npm run type-check 2>&1 | grep -i "window" | grep -i "error" && echo "Type errors found" || echo "No window type errors"
```

**Accept when:**
- All third-party script integrations use dynamic script element creation with async attribute
- Window object extensions have corresponding TypeScript interface declarations without type errors
- Script loading logic includes duplicate initialization checks before creating new script elements
- Components use useEffect hooks for script lifecycle management and cleanup
- Error handling is implemented for script load failures

<enforcement>
Claude Code MUST NOT skip or defer verification. TypeScript compilation failures for missing window interface declarations block deployment. Pull requests with synchronous third-party script loads are flagged for review and revision. Performance regression alerts trigger investigation and potential rollback of changes.
</enforcement>