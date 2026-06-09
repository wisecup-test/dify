# Standardize Environment Variable Configuration Sources with Fallback Mechanisms: Environment Variable Names

These rules are ALWAYS ACTIVE for all configuration management implementations across runtime environments, extensions, and remote settings integrations.

### Rules

- **R-ENV-001** MUST: Environment variable names MUST follow a consistent naming convention (uppercase with underscores) and include component prefixes where appropriate.

### Verify

```bash
# Count os.environ.get() usage patterns
grep -r 'os\.environ\.get' --include='*.py' | grep -v 'test' | wc -l

# Count direct os.environ[] access (should be minimal/zero in production code)
grep -r 'os\.environ\[' --include='*.py' | grep -v 'test' | wc -l

# Find centralized configuration modules
find . -name 'config*.py' -o -name 'settings*.py' | xargs grep -l 'environ'
```

**Accept when:**
- All configuration retrieval uses `os.environ.get()` with default values rather than direct dictionary access (`os.environ[]`)
- Configuration modules exist for each major component (runtime, storage, remote settings) that centralize environment variable access
- No unhandled `KeyError` exceptions occur from missing environment variables in production code paths
- Environment variable names follow uppercase with underscores convention (e.g., `OTEL_EXPORTER_OTLP_ENDPOINT`, `CLICKZETTA_STORAGE_PATH`)
- Component prefixes are consistently applied across related configuration variables

<enforcement>
Claude Code MUST NOT skip or defer verification. All configuration retrieval patterns must be reviewed for compliance with centralized `os.environ.get()` usage and consistent naming conventions before accepting changes.
</enforcement>