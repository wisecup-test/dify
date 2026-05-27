# Adopt Dedicated Test File Naming Convention with Framework-Specific Patterns: Typescript React Test

These rules are ALWAYS ACTIVE for all TypeScript/React test file creation and organization activities across the codebase.

### Rules

- **R-TEST-001** MUST: TypeScript/React test files MUST use the `*.spec.tsx` or `*.spec.ts` naming convention and be placed in `__tests__` directories adjacent to the code under test.

### Verify

```bash
# Verify no TypeScript test files exist outside __tests__ directories with incorrect naming
find web/app -type f \( -name '*.test.tsx' -o -name '*.test.ts' \) ! -path '*/__tests__/*' | wc -l | grep -q '^0$'

# Verify all TypeScript test files in __tests__ directories use correct naming
find web/app -type d -name '__tests__' -exec find {} -type f \( -name '*.tsx' -o -name '*.ts' \) ! -name '*.spec.tsx' ! -name '*.spec.ts' \; | wc -l | grep -q '^0$'

# Verify Jest can discover all test files
jest --listTests 2>&1 | grep -q 'spec.tsx\|spec.ts'
```

**Accept when:**
- All TypeScript/React test files are located in `__tests__` directories
- All test files use `*.spec.tsx` or `*.spec.ts` naming convention
- Jest test discovery successfully identifies all test files without manual configuration
- No test files with alternative naming patterns (e.g., `*.test.tsx`) exist outside of legacy exceptions

<enforcement>
Claude Code MUST NOT skip or defer verification of test file naming and placement compliance.
</enforcement>