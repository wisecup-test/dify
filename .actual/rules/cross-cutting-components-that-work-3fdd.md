# Adopt 'use client' Directive for Client-Side Hydration in React Server Components: Components That Work

These rules are ALWAYS ACTIVE for all React components (.tsx, .jsx files) in the application, including components in web/app/components directory and subdirectories, utility scripts that generate React components, and third-party component wrappers requiring client-side features.

### Rules

- **R-RSC-001** MAY: Components that work in both server and client contexts MAY omit the 'use client' directive to allow server-side rendering.

### Verify

```bash
# Count existing 'use client' directives in component files
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

<enforcement>
Claude Code MUST NOT skip or defer verification. All verify commands MUST pass before accepting changes to component files.
</enforcement>