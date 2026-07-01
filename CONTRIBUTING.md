# Contributing

Contributions are welcome.

## Guidelines

- Keep all project files and documentation in English.
- Do not include real backup files, tokens, credentials or server-specific configuration.
- Test installer changes on a clean Ubuntu server before opening a pull request.
- Prefer simple Bash and PHP CLI code over additional dependencies.

## Development test

You can run the PHP script with a custom config file:

```bash
php src/sync.php doctor --config=/path/to/config.php
```

Use dry-run before real synchronization:

```bash
php src/sync.php dry-run --config=/path/to/config.php
```
