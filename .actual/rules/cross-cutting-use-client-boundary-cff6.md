# Adopt 'use client' Directive for Client-Side Hydration in React Server Components: Use Client Boundary

These rules are ALWAYS ACTIVE for all React components (.tsx, .jsx files) in the application, including components in web/app/components directory and subdirectories, utility scripts that generate React components, and third-party component wrappers requiring client-side features.

### Rules

- **R-RSC-001** SHOULD: The 'use client' boundary SHOULD be placed as deep in the component tree as possible to minimize client-side JavaScript bundle size.
- **R-RSC-002** MUST: Place 'use client' as the very first line of the file, before any imports or other code.
- **R-RSC-003** SHOULD: When refactoring, identify the minimal component boundary that requires client-side features and apply 'use client' only to that component.
- **R-RSC-004** SHOULD: Consider creating separate client and server component files when a component has both server and client concerns.
- **R-RSC-005** SHOULD: Use TypeScript and ESLint plugins for React Server Components to catch violations early in development.
- **R-RSC-006** SHOULD: Document the reason for 'use client' usage in component comments when the necessity is not immediately obvious.

### Verify

```bash
# Count existing 'use client' directives in components
grep -r "'use client'" web/app/components --include='*.tsx' --include='*.jsx' | wc -l

# Run ESLint checks for React Server Components violations
eslint --ext .tsx,.jsx web/app/components --rule 'react/no-client-component-in-server-component: error'

# Find components using client-only features without 'use client' directive
find web/app/components -name '*.tsx' -exec grep -l "useState\|useEffect\|onClick" {} \; | xargs grep -L "'use client'"
```

**Accept when:**
- All components using React hooks, browser APIs, or event handlers include the 'use client' directive
- ESLint checks pass with no violations of React Server Components rules
- No runtime errors occur due to client-only features being used in server components
- Bundle size analysis shows appropriate distribution between server and client components
- 'use client' directives are placed as the first line of files
- Minimal client-side boundaries are enforced to reduce JavaScript bundle size

<enforcement>
Claude Code MUST NOT skip or defer verification. ESLint rules for React Server Components in CI/CD pipeline are mandatory. Code review checklist verification of appropriate 'use client' usage is required. Automated bundle size analysis in pull request checks is mandatory. Runtime error monitoring in development and staging environments is required. CI/CD pipeline MUST fail if ESLint rules detect violations. Pull requests MUST be blocked until 'use client' directive issues are resolved.
</enforcement>