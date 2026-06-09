# Standardize Environment Variable Configuration Sources with Fallback Mechanisms: Configuration Classes Centralize

These rules are ALWAYS ACTIVE for all configuration management implementations across runtime environments, extensions, and remote settings integrations.

### Rules

- **R-CONFIG-001** SHOULD: Configuration classes SHOULD centralize environment variable access rather than scattering os.environ calls throughout business logic.

### Verify

```bash
# Count direct os.environ.get() usage patterns
grep -r 'os\.environ\.get' --include='*.py' | grep -v 'test' | wc -l

# Count unsafe direct dictionary access patterns to flag
grep -r 'os\.environ\[' --include='*.py' | grep -v 'test' | wc -l

# Identify configuration modules that centralize environment access
find . -name 'config*.py' -o -name 'settings*.py' | xargs grep -l 'environ'
```

**Accept when:**
- All configuration retrieval uses os.environ.get() with default values rather than direct dictionary access (os.environ[])
- Configuration modules exist for each major component (runtime, storage, remote settings) that centralize environment variable access
- No unhandled KeyError exceptions occur from missing environment variables in production code paths
- Type conversion utilities (int(), bool(), json.loads()) are applied immediately after retrieving environment variables
- Configuration logging at startup redacts sensitive values

<enforcement>
Clause Code MUST NOT skip or defer verification of configuration centralization patterns. All configuration retrieval MUST use os.environ.get() with documented defaults. Direct os.environ[] access without exception handling is a violation requiring code review rejection.
</enforcement>