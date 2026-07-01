# Publishing this project to GitHub

This guide explains how to publish this package as a public GitHub repository.

## Recommended repository settings

Repository name:

```text
xui-one-gdrive-backup-sync
```

Description:

```text
Cron-based Google Drive backup sync for XUI.ONE local SQL backups using PHP CLI and rclone.
```

Topics:

```text
xui-one, google-drive, rclone, backup, ubuntu, cron, php-cli, database-backup
```

Visibility:

```text
Public
```

Do not add a README, license or .gitignore from the GitHub web interface because this package already contains them.

## Publish with Git command line

Extract the package and open the project directory:

```bash
cd xui-one-gdrive-backup-sync
```

Initialize Git:

```bash
git init
git add .
git commit -m "Initial release"
git branch -M main
```

Create an empty repository on GitHub named:

```text
xui-one-gdrive-backup-sync
```

Then connect and push:

```bash
git remote add origin https://github.com/YOUR_USERNAME/xui-one-gdrive-backup-sync.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your GitHub username.

## Publish with GitHub CLI

If GitHub CLI is installed and authenticated, you can publish directly:

```bash
cd xui-one-gdrive-backup-sync
git init
git add .
git commit -m "Initial release"
gh repo create xui-one-gdrive-backup-sync --public --source=. --remote=origin --push
```

## Create the first release

After pushing the repository, create a tag:

```bash
git tag v1.0.0
git push origin v1.0.0
```

Then open GitHub and create a release from tag `v1.0.0`.

Suggested release title:

```text
v1.0.0 - Initial release
```

Suggested release notes:

```text
Initial public release.

Features:
- PHP CLI installer
- Hourly cron sync
- rclone Google Drive support
- Dry-run mode
- Empty-source protection
- Log and logrotate support
```

## Add a social preview image

This package includes:

```text
assets/social-preview.png
```

On GitHub:

1. Open the repository.
2. Go to Settings.
3. Open General.
4. Scroll to Social preview.
5. Upload `assets/social-preview.png`.
