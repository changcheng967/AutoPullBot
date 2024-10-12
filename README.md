
# Autopullbot Configuration

This `Autopullbot.yml` file is designed for automating the process of pulling changes from specified branches into a target branch within a GitHub repository.

## Overview

The Autopullbot performs the following functions:

- **Automatic Synchronization:** It triggers on push events to specified source branches, ensuring the target branch stays up to date with the latest commits.
- **Commit History Maintenance:** The bot merges changes into the target branch while preserving the commit history.
- **Configurability:** Users can easily customize branch settings to define how the bot behaves.

## Features

- **Trigger on Push Events:** Automatically responds to pushes made to specified source branches.
- **Pull Requests and Merges:** Executes pull requests and merges based on the defined branch configuration.
- **Automatic Labeling:** Adds predefined labels to pull requests for easier identification and management.

## Usage

To use the Autopullbot:

1. **Edit Branch Settings:** 
   - Update the `source_branch` and `target_branch` fields in the YAML file to specify which branches to synchronize.
   - Ensure that the `branches-ignore` list contains any branches you do not want to trigger the workflow.

2. **Configure User Details:** 
   - Replace the placeholder email in the `git config` section with your email address to ensure proper attribution for the commits.

3. **Manual Triggering (Optional):** 
   - You can manually trigger the workflow for testing by using the GitHub Actions interface.

## Example Configuration

```yaml
name: Autopullbot
on:
  push:
    branches-ignore:
      - main # Exclude 'main' branch from triggering the workflow
  workflow_dispatch: # Allows manual triggering of the workflow for testing

jobs:
  autopull:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
        
      - name: Pull Changes
        run: |
          git config --global user.email "you@example.com" # Update with your email
          git config --global user.name "Autopullbot"
          git fetch origin target_branch
          git checkout target_branch
          git merge origin/source_branch
          git push origin target_branch

      - name: Check If Branch is Behind Main
        run: |
          echo "Fetching the main branch for comparison..."
          git fetch origin main
          BEHIND=$(git rev-list --count HEAD..origin/main)
          echo "Branch is behind main by $BEHIND commits."
          if [ "$BEHIND" -le 0 ]; then
            echo "Branch is up to date with main; no pull request will be created."
            exit 0
          fi

      - name: Create Pull Request
        run: |
          echo "Creating a new pull request..."
          gh pr create \
            --title "Auto Pull Request - ${{ env.BRANCH_NAME }}" \
            --body "Auto-generated PR to merge '${{ env.BRANCH_NAME }}' into 'main'" \
            --base main \
            --head "${{ env.BRANCH_NAME }}" \
            --repo "${{ github.repository }}"
