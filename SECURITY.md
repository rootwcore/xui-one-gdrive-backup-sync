# Security Policy

## Sensitive files

Never publish server-specific files or secrets, including:

- `/etc/xui-gdrive-sync/config.php`
- `/etc/xui-gdrive-sync/rclone.conf`
- SQL backup files
- OAuth tokens
- activation codes
- panel credentials
- server logs containing private data

## Backup sensitivity

XUI.ONE SQL backups may contain sensitive panel and user data. Store them only in accounts and folders that you control.

For stronger privacy, use an rclone `crypt` remote before sending backups to cloud storage.

## Reporting security issues

Please do not open public issues for security vulnerabilities.

Instead, contact the repository owner privately.
