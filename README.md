# XUI.ONE Google Drive Backup Sync

A lightweight PHP CLI installer and cron-based synchronization tool that mirrors local XUI.ONE SQL backups from `/home/xui/backups` to Google Drive using `rclone`.

This project is designed for XUI.ONE servers where the panel already creates local backup files such as:

```text
backup_2026-06-25_00:28:01.sql
```

The tool does not create XUI.ONE backups itself. It only synchronizes the backup files that already exist on the server.

## Features

- Hourly cron synchronization
- Mirrors `/home/xui/backups` to Google Drive
- Uploads newly created backup files
- Removes remote files when they are deleted locally
- Dry-run mode before making real changes
- Empty-source protection to prevent accidental Drive wipes
- Isolated rclone config file
- Log file and logrotate support
- Simple PHP CLI command
- No database required
- No web panel required

## How it works

XUI.ONE creates local backup files in:

```bash
/home/xui/backups
```

This project installs a command called:

```bash
xui-gdrive-sync
```

Cron runs it every hour:

```cron
0 * * * * root /usr/local/bin/xui-gdrive-sync sync >/dev/null 2>&1
```

The actual synchronization is performed by `rclone sync`:

```bash
rclone sync /home/xui/backups gdrive:xui-one-backups/YOUR_SERVER_HOSTNAME
```

Because this is a mirror sync, files deleted from the local backup directory will also be deleted from the configured Google Drive folder.

## Requirements

- Ubuntu 20.04, 22.04 or 24.04
- Root SSH access
- PHP CLI
- cron
- rclone
- A Google Drive account
- Existing XUI.ONE backups in `/home/xui/backups`

## Installation

Clone the repository:

```bash
git clone https://github.com/rootwcore/xui-one-gdrive-backup-sync.git
cd xui-one-gdrive-backup-sync
```

Run the installer:

```bash
sudo bash install.sh
```

Custom install example:

```bash
sudo SOURCE_DIR=/home/xui/backups \
     REMOTE_NAME=gdrive \
     REMOTE_DIR='xui-one-backups/{hostname}' \
     CRON_MINUTE=0 \
     bash install.sh
```

The installer creates:

```text
/opt/xui-gdrive-sync/sync.php
/etc/xui-gdrive-sync/config.php
/etc/xui-gdrive-sync/rclone.conf
/var/log/xui-gdrive-sync/sync.log
/etc/cron.d/xui-gdrive-sync
/etc/logrotate.d/xui-gdrive-sync
/usr/local/bin/xui-gdrive-sync
```

## Configure Google Drive

Run:

```bash
sudo rclone config --config /etc/xui-gdrive-sync/rclone.conf
```

Recommended choices:

```text
n) New remote
name> gdrive
Storage> Google Drive / drive
Use auto config?> n
```

On a headless server, rclone will give you an authorization command or URL. Complete the authorization on a computer with a browser, then paste the token back into the server.

Protect the rclone token file:

```bash
sudo chmod 600 /etc/xui-gdrive-sync/rclone.conf
```

## Validate the installation

Run:

```bash
sudo xui-gdrive-sync doctor
```

You should see checks for PHP, rclone, source directory and the configured rclone remote.

## Test before syncing

Always run a dry-run first:

```bash
sudo xui-gdrive-sync dry-run
```

If the output looks correct, run the real sync:

```bash
sudo xui-gdrive-sync sync
```

## Commands

```bash
sudo xui-gdrive-sync help
sudo xui-gdrive-sync doctor
sudo xui-gdrive-sync local
sudo xui-gdrive-sync remote
sudo xui-gdrive-sync status
sudo xui-gdrive-sync dry-run
sudo xui-gdrive-sync sync
sudo xui-gdrive-sync version
```

## Configuration

Main config file:

```bash
sudo nano /etc/xui-gdrive-sync/config.php
```

Default configuration:

```php
return [
    'source_dir' => '/home/xui/backups',
    'remote_name' => 'gdrive',
    'remote_dir' => 'xui-one-backups/{hostname}',
    'include_patterns' => [
        'backup_*.sql',
        'backup_*.sql.gz',
    ],
    'allow_empty_source' => false,
    'drive_use_trash' => false,
    'transfers' => 4,
    'checkers' => 8,
    'fast_list' => true,
    'stats_interval' => '30s',
    'log_level' => 'INFO',
    'extra_rclone_flags' => [],
    'log_dir' => '/var/log/xui-gdrive-sync',
    'rclone_config' => '/etc/xui-gdrive-sync/rclone.conf',
    'lock_file' => '/run/xui-gdrive-sync.lock',
];
```

### Important options

| Option | Description |
| --- | --- |
| `source_dir` | Local XUI.ONE backup directory. |
| `remote_name` | rclone remote name. Must match the remote created with `rclone config`. |
| `remote_dir` | Google Drive folder path. `{hostname}` is replaced with the server hostname. |
| `include_patterns` | Backup filename patterns to synchronize. |
| `allow_empty_source` | Keeps sync from wiping Drive if the source folder is empty. |
| `drive_use_trash` | If `false`, deleted files are removed permanently instead of moved to Drive Trash. |
| `extra_rclone_flags` | Optional additional rclone flags. |

## Logs

View the last sync log entries:

```bash
sudo tail -n 100 /var/log/xui-gdrive-sync/sync.log
```

Follow logs in real time:

```bash
sudo tail -f /var/log/xui-gdrive-sync/sync.log
```

## Cron

Installed cron file:

```bash
cat /etc/cron.d/xui-gdrive-sync
```

To change the schedule, edit:

```bash
sudo nano /etc/cron.d/xui-gdrive-sync
sudo systemctl restart cron
```

## Uninstall

Remove application files and cron entry:

```bash
sudo bash scripts/uninstall.sh
```

The uninstall script does not remove configuration, OAuth tokens or logs by default.

To remove everything:

```bash
sudo rm -rf /etc/xui-gdrive-sync /var/log/xui-gdrive-sync
```

## Security notes

XUI.ONE database backups may contain sensitive data such as users, panel settings, credentials, tokens or server information.

Do not commit any of these files to GitHub:

- SQL backups
- rclone config files
- OAuth tokens
- server-specific config files
- log files
- activation or license data

For stronger privacy, create an rclone `crypt` remote on top of your Google Drive remote and set `remote_name` to the encrypted remote.

## Disclaimer

This project is not affiliated with, endorsed by or officially connected to XUI.ONE.

Use it only on systems and backups that you own or are authorized to manage.

## License

MIT License. See [LICENSE](LICENSE).
