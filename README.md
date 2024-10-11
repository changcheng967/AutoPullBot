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

## Usage

To use the Autopullbot:

1. **Edit Branch Settings:** Update the `source_branch` and `target_branch` fields in the YAML file to specify which branches to synchronize.
2. **Configure User Details:** Replace the placeholder email with your email in the `git config` section to ensure proper attribution for the commits.

## Example Configuration

```yaml
name: Autopullbot
on:
  push:
    branches:
      - source_branch # Replace with your source branch name

jobs:
  autopull:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2
        
      - name: Pull Changes
        run: |
          git config --global user.email "you@example.com" # Update with your email
          git config --global user.name "Autopullbot"
          git fetch origin target_branch
          git checkout target_branch
          git merge origin/source_branch
          git push origin target_branch
