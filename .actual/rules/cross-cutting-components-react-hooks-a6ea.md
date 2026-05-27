# Adopt 'use client' Directive for Client-Side Hydration in React Server Components: Components React Hooks

These rules are ALWAYS ACTIVE for all React components (.tsx, .jsx files) in the application, including components in web/app/components directory and subdirectories, utility scripts that generate React components, and third-party component wrappers requiring client-side features.

### Rules

- **R-RSC-001** MUST: Components using React hooks (useState, useEffect, useContext, etc.) MUST be marked with 'use client' directive as the very first line of the file, before any imports or other code.

### Verify

```bash
# Count files with 'use client' directive
grep -r "'use client'" web/app/components --include='*.tsx' --include='*.jsx' | wc -l

# Check for ESLint violations of React Server Components rules
eslint --ext .tsx,.jsx web/app/components --rule 'react/no-client-component-in-server-component: error'

# Find components using client-only features without 'use client' directive
find web/app/components -name '*.tsx' -exec grep -l "useState\|useEffect\|onClick" {} \; | xargs grep -L "'use client'"
```

**Accept when:**
- All components using React hooks, browser APIs, or event handlers include the 'use client' directive
- ESLint checks pass with no violations of React Server Components rules
- No runtime errors occur due to client-only features being used in server components
- Bundle size analysis shows appropriate distribution between server and client components
- The 'use client' directive appears as the first line in all affected files

<enforcement>
Claude Code MUST NOT skip or defer verification. All verify commands MUST pass before accepting changes. ESLint rules for React Server Components MUST be enforced in CI/CD pipeline. Code review MUST verify appropriate 'use client' usage. Pull requests MUST be blocked until violations are resolved.
</enforcement>