# Adopt 'use client' Directive for Client-Side Hydration in React Server Components: Components Accessing Browser

These rules are ALWAYS ACTIVE for all React components (.tsx, .jsx files) in the application, including components in web/app/components directory and subdirectories, utility scripts that generate React components, and third-party component wrappers requiring client-side features.

### Rules

- **R-RSC-001** MUST: Components accessing browser APIs (window, document, localStorage, etc.) MUST be marked with 'use client' directive as the very first line of the file, before any imports or other code.
- **R-RSC-002** MUST: Components using React hooks that require client-side execution (useState, useEffect, useContext, etc.) MUST be marked with 'use client'.
- **R-RSC-003** MUST: Components with event handlers (onClick, onChange, onSubmit, etc.) MUST be marked with 'use client'.
- **R-RSC-004** SHOULD: When refactoring, identify the minimal component boundary that requires client-side features and apply 'use client' only to that component.
- **R-RSC-005** SHOULD: Consider creating separate client and server component files when a component has both server and client concerns.
- **R-RSC-006** SHOULD: Document the reason for 'use client' usage in component comments when the necessity is not immediately obvious.

### Verify

```bash
# Count files with 'use client' directive
grep -r "'use client'" web/app/components --include='*.tsx' --include='*.jsx' | wc -l

# Check for React Server Components linting violations
eslint --ext .tsx,.jsx web/app/components --rule 'react/no-client-component-in-server-component: error'

# Find components using client-only features without 'use client' directive
find web/app/components -name '*.tsx' -exec grep -l "useState\|useEffect\|onClick" {} \; | xargs grep -L "'use client'"
```

**Accept when:**
- All components using React hooks, browser APIs, or event handlers include the 'use client' directive
- ESLint checks pass with no violations of React Server Components rules
- No runtime errors occur due to client-only features being used in server components
- Bundle size analysis shows appropriate distribution between server and client components
- 'use client' directive appears as the first line in all client components

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory and must be checked before accepting component code.
</enforcement>