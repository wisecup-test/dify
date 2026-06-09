# Standardize Environment Variable Configuration Sources with Fallback Mechanisms: Configuration Values Sourced

These rules are ALWAYS ACTIVE for all configuration management implementations across runtime environments, extensions, and remote settings integrations.

### Rules

- **R-CONFIG-001** MUST: All configuration values MUST be sourced from environment variables using `os.environ.get()` or equivalent methods that support default values.

### Verify

```bash
# Count os.environ.get() usage patterns
grep -r 'os\.environ\.get' --include='*.py' | grep -v 'test' | wc -l

# Count direct os.environ[] access (should be minimal/zero in production code)
grep -r 'os\.environ\[' --include='*.py' | grep -v 'test' | wc -l

# Find configuration modules that should centralize environment access
find . -name 'config*.py' -o -name 'settings*.py' | xargs grep -l 'environ'
```

**Accept when:**
- All configuration retrieval uses `os.environ.get()` with default values rather than direct dictionary access (`os.environ[]`)
- Configuration modules exist for each major component (runtime, storage, remote settings) that centralize environment variable access
- No unhandled `KeyError` exceptions occur from missing environment variables in production code paths
- Type conversion utilities are applied immediately after retrieving environment variables to fail fast on invalid values
- Configuration logging at startup redacts sensitive values

<enforcement>
Clause Code MUST NOT skip or defer verification of environment variable configuration patterns. All configuration retrieval must use safe access methods with documented defaults.
</enforcement>