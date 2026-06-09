# Adopt 'use client' Directive for Client-Side Hydration in React Server Components: Components That Work

Status: proposed
Date: 2025-01-20
Deciders: Detection Pipeline (automated)

## Context

- The codebase uses React Server Components (RSC) architecture, which requires explicit distinction between server-side and client-side rendering contexts
- Components requiring browser APIs, event handlers, or client-side state management need to be marked for client-side hydration
- Pattern detected in 3 files with 90.83% confidence, indicating consistent adoption of the 'use client' directive for components requiring client-side interactivity
- The paradigm.concurrency_model facet suggests this pattern addresses the concurrent rendering model between server and client execution contexts
- Modern React frameworks (Next.js 13+, React 18+) default to server components, requiring explicit opt-in for client components

## Problem Statement

In React Server Components architecture, components are rendered on the server by default. However, components that require browser-specific APIs, event handlers, hooks like useState/useEffect, or client-side interactivity cannot execute in the server context. Without a clear mechanism to designate client-side execution boundaries, the application would fail at runtime when attempting to use client-only features in server components.

## Decision

1. MAY: Components that work in both server and client contexts MAY omit the 'use client' directive to allow server-side rendering

## Policy Block

- MAY Components that work in both server and client contexts MAY omit the 'use client' directive to allow server-side rendering

In scope:
- All React components (.tsx, .jsx files) in the application
- Components in web/app/components directory and subdirectories
- Utility scripts that generate React components
- Third-party component wrappers requiring client-side features

Out of scope:
- Pure server-side components without interactivity
- Node.js scripts and build tools
- API routes and server-side middleware
- Configuration files and type definitions

Exceptions:
- EXC-001: Component is being migrated from client-only to server-compatible implementation
- EXC-002: Third-party library components that cannot be modified directly

## Rationale

- The pattern was detected in 3 files with 90.83% confidence, indicating established architectural practice in the codebase
- React Server Components architecture requires explicit client-side boundaries to optimize performance and reduce JavaScript bundle sizes
- The 'use client' directive provides clear, declarative syntax for marking client-side execution boundaries, improving code maintainability
- This pattern aligns with the paradigm.concurrency_model facet by explicitly managing concurrent execution contexts between server and client

## Consequences

Positive:
- Reduced JavaScript bundle sizes by keeping non-interactive components on the server
- Improved initial page load performance through server-side rendering of static content
- Clear separation of concerns between server and client execution contexts
- Better developer experience with explicit, compile-time errors for misuse of client-only features in server components
- Enables progressive enhancement and better SEO through server-rendered HTML

Negative:
- Developers must understand the distinction between server and client components, increasing learning curve
- Potential for confusion when determining appropriate placement of 'use client' boundaries
- May require refactoring existing components to optimize client/server boundaries
- Additional cognitive overhead when designing component hierarchies

## Alternatives

- Use client-side rendering exclusively for all components (traditional SPA approach) (rejected)
  Rejected because: Eliminates server-side rendering benefits including improved performance, SEO, and reduced JavaScript bundle sizes. Does not align with modern React Server Components architecture.
  When valid: For applications that require extensive client-side interactivity throughout and do not prioritize initial load performance or SEO
- Use server-side rendering exclusively without client-side hydration (rejected)
  Rejected because: Prevents any client-side interactivity, making it impossible to implement dynamic user interfaces, form handling, or real-time updates
  When valid: For purely static content sites with no interactive features
- Use automatic detection of client-side features without explicit directives (rejected)
  Rejected because: Would require complex static analysis and could lead to unpredictable behavior. The explicit 'use client' directive provides clarity and compile-time guarantees
  When valid: Not recommended; explicit is better than implicit for execution context boundaries

## Risks

- Developers may overuse 'use client' directive, negating server-side rendering benefits
  Mitigation: Provide clear guidelines and code review checklist for appropriate 'use client' usage. Implement bundle size monitoring to detect excessive client-side code.
  Owner: Engineering team and tech leads
- Missing 'use client' directive on components requiring client features will cause runtime errors
  Mitigation: Enable strict TypeScript checking and React Server Components linting rules. Provide clear error messages and documentation for common scenarios.
  Owner: Engineering team
- Inconsistent application of the pattern across the codebase may lead to maintenance issues
  Mitigation: Establish automated linting rules to enforce the pattern. Conduct team training on React Server Components architecture and best practices.
  Owner: Engineering team and DevOps

## Implementation Notes

- Place 'use client' as the very first line of the file, before any imports or other code
- When refactoring, identify the minimal component boundary that requires client-side features and apply 'use client' only to that component
- Consider creating separate client and server component files when a component has both server and client concerns
- Use TypeScript and ESLint plugins for React Server Components to catch violations early in development
- Document the reason for 'use client' usage in component comments when the necessity is not immediately obvious

## Continuation Context


Verify commands:
- grep -r "'use client'" web/app/components --include='*.tsx' --include='*.jsx' | wc -l
- eslint --ext .tsx,.jsx web/app/components --rule 'react/no-client-component-in-server-component: error'
- find web/app/components -name '*.tsx' -exec grep -l "useState\|useEffect\|onClick" {} \; | xargs grep -L "'use client'"

Accept when:
- All components using React hooks, browser APIs, or event handlers include the 'use client' directive
- ESLint checks pass with no violations of React Server Components rules
- No runtime errors occur due to client-only features being used in server components
- Bundle size analysis shows appropriate distribution between server and client components

## Enforcement

- Verified by: ESLint rules for React Server Components in CI/CD pipeline
- Verified by: Code review checklist requiring verification of appropriate 'use client' usage
- Verified by: Automated bundle size analysis in pull request checks
- Verified by: Runtime error monitoring in development and staging environments
- Violation handling: CI/CD pipeline fails if ESLint rules detect violations
- Violation handling: Pull requests blocked until 'use client' directive issues are resolved
- Violation handling: Runtime errors in development environment provide clear guidance on adding 'use client'
- Violation handling: Code review feedback provided for suboptimal client/server boundary placement
- Exception process: Developer documents exception rationale in pull request description
- Exception process: Tech lead reviews and approves exception with justification
- Exception process: Exception documented in component comments with ticket reference for future resolution
- Exception process: Exceptions tracked in technical debt backlog for periodic review