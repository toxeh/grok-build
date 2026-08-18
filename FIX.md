# Update and Release Instructions

## Purpose of this Fork
This fork specifically exists to maintain a patch that fixes the "breaking of quotes" (bash quotes escaping) issue. 
**Important:** If, during a future rebase, you notice that this issue has been resolved in the upstream repository (even if implemented differently), this fork will no longer be relevant and should be deprecated in favor of the upstream `xai-org/grok-build` repository.

## Overview
This document outlines the procedure for maintaining the current fork (with custom patches) synchronized with the upstream `xai-org/grok-build` repository, as well as the process for automated release builds via GitHub Actions.

## 1. Upstream Synchronization and Rebase (with Backup)

To integrate the latest changes from the upstream repository while preserving your custom patches, perform a rebase operation. It is strictly recommended to create a backup of your current branch prior to rebasing.

```bash
# Ensure you are on your working branch (e.g., fix-bash-quotes-escaping)
git checkout fix-bash-quotes-escaping

# 1. Create a backup of the current branch state
git branch backup-fix-bash-quotes-escaping-$(date +%Y%m%d)

# 2. Add the original repository as upstream (if not already added)
git remote add upstream https://github.com/xai-org/grok-build.git || true

# 3. Fetch the latest changes from the upstream repository
git fetch upstream

# 4. Rebase your current branch onto the upstream main branch
git rebase upstream/main
```

## 2. Conflict Resolution

If merge conflicts occur during the rebase process, Git will pause the operation. Follow these steps to resolve them:

1. Open the conflicting files and locate the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
2. Resolve the conflicts by preserving both the necessary upstream changes and your custom patches. Remove the conflict markers.
3. Stage the resolved files:
   ```bash
   git add <filename>
   ```
4. Continue the rebase process:
   ```bash
   git rebase --continue
   ```
*(Repeat these steps until the rebase operation completes successfully).*

## 3. Creating a Release and Building Binaries

Once the branch is updated and thoroughly tested, you can proceed to create a new release. The versioning scheme relies on Git tags.

1. Push your rebased branch to your remote fork. Due to the rewritten commit history, a force push is required:
   ```bash
   git push origin HEAD --force-with-lease
   ```

2. Create a new Git tag for the release (e.g., `v0.2.998a`):
   ```bash
   git tag v0.2.998a
   ```

3. Push the tag to the GitHub repository:
   ```bash
   git push origin v0.2.998a
   ```

### GitHub Actions Build Process

Pushing a tag that matches the release pattern (e.g., `v*`) will automatically trigger the GitHub Actions release workflow. The workflow performs the following automated steps:
- Provisions the build environment with the required Rust toolchain.
- Compiles the executable binaries for multiple platforms, explicitly including macOS, Linux, and **Windows**.
- Generates a new GitHub Release associated with the pushed tag.
- Uploads the compiled binaries for macOS, Linux, and Windows as release assets.
