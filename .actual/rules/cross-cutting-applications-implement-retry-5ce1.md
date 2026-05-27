# Adopt Third-Party Script Lazy Loading with Window Object Initialization Pattern: Applications Implement Retry

These rules are ALWAYS ACTIVE for all third-party script integrations, analytics SDKs, support widgets, tracking scripts, and any external JavaScript that requires window object initialization across the application.

### Rules

- **R-LAZY-001** MAY: Applications MAY implement retry logic for failed third-party script loads based on service criticality.
- **R-LAZY-002** MUST: All third-party script integrations use dynamic script element creation with async attribute to prevent render-blocking.
- **R-LAZY-003** MUST: Window object extensions have corresponding TypeScript interface declarations without type errors.
- **R-LAZY-004** MUST: Script loading logic includes duplicate initialization checks before creating new script elements.
- **R-LAZY-005** SHOULD: Create reusable utility functions or hooks for common script loading patterns to reduce code duplication.
- **R-LAZY-006** SHOULD: Document TypeScript window interface extensions in a centralized types file (e.g., window.d.ts).
- **R-LAZY-007** SHOULD: Implement feature flags for third-party integrations to enable/disable services without code changes.
- **R-LAZY-008** SHOULD: Add comprehensive error logging for script load failures to aid debugging in production environments.

### Verify

```bash
# Check for dynamic script element creation patterns
grep -r "createElement.*script" web/app/components --include="*.tsx" --include="*.ts" | grep -v "node_modules"

# Check for window object usage with third-party services
grep -r "window\." web/app/components --include="*.tsx" --include="*.ts" | grep -E "(zendesk|ga|PartnerStack)" | grep -v "node_modules"

# Verify TypeScript window interface declarations
npm run type-check 2>&1 | grep -i "window" | grep -i "error" && echo "Type errors found" || echo "No window type errors"
```

**Accept when:**
- All third-party script integrations use dynamic script element creation with async attribute
- Window object extensions have corresponding TypeScript interface declarations without type errors
- Script loading logic includes duplicate initialization checks before creating new script elements
- No render-blocking synchronous third-party scripts are detected in the codebase
- TypeScript compilation succeeds with no window-related type errors

<enforcement>
Claude Code MUST NOT skip or defer verification. All three verify commands MUST execute successfully before accepting changes. TypeScript compilation failures for missing window interface declarations MUST block deployment. Pull requests with synchronous third-party script loads MUST be flagged for review and revision.
</enforcement>