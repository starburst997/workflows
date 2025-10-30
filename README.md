# Reusable GitHub Workflows

This repository contains reusable GitHub Actions workflows for automating common development tasks.

## Available Workflows

### 1. JD Code (`jd.yml`)

Automates issue and PR management using JD AI.

**Triggers:**

- `workflow_call` - Can be called from other workflows
- Issue comments, PR review comments, and PR reviews containing `/jd`
- Issues opened, assigned, or labeled with `jd`

**Required Secrets:**

- `CLAUDE_CODE_OAUTH_TOKEN` - Claude Code OAuth token for authentication

**Optional Secrets:**

- `BOT_ID` - GitHub App ID for custom bot
- `BOT_KEY` - GitHub App private key for custom bot

**Example Usage:**

```yaml
name: JD Code

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned, labeled]
  pull_request_review:
    types: [submitted]

jobs:
  jd:
    permissions: # Optional - remove if using BOT_ID / BOT_KEY
      issues: write
      contents: read
      pull-requests: read
      id-token: write
      actions: write
    uses: starburst997/workflows/.github/workflows/jd.yml@v1
    secrets: inherit
```

### 2. JD Review (`jd-review.yml`)

Automatically reviews pull requests using JD AI.

**Triggers:**

- `workflow_call` - Can be called from other workflows
- Pull requests opened or marked ready for review

**Required Secrets:**

- `CLAUDE_CODE_OAUTH_TOKEN` - Claude Code OAuth token for authentication

**Optional Secrets:**

- `BOT_ID` - GitHub App ID for custom bot
- `BOT_KEY` - GitHub App private key for custom bot

**Example Usage:**

```yaml
name: JD Review

on:
  pull_request:
    types: [opened, ready_for_review]

jobs:
  review:
    permissions: # Optional - remove if using BOT_ID / BOT_KEY
      contents: read
      pull-requests: read
      issues: read
      id-token: write
    uses: starburst997/workflows/.github/workflows/jd-review.yml@v1
    secrets: inherit
```

### 3. GitHub Pages Deployment (`gh-pages.yaml`)

Automatically deploys documentation from the `docs/` directory to GitHub Pages.

**Triggers:**

- `workflow_call` - Can be called from other workflows
- Push to main branch when `docs/**` files change

**Optional Secrets:**

- `BOT_ID` - GitHub App ID for custom bot
- `BOT_KEY` - GitHub App private key for custom bot

**Example Usage:**

```yaml
name: GitHub Pages

on:
  push:
    branches:
      - main
    paths:
      - "docs/**"

jobs:
  deploy:
    permissions: # Optional - remove if using BOT_ID / BOT_KEY
      contents: read
      pages: write
      id-token: write
    uses: starburst997/workflows/.github/workflows/gh-pages.yaml@v1
    secrets: inherit
```

### 4. Automated Release (`release.yml`)

Automatically creates releases with version bumping and changelog generation.

**Triggers:**

- `workflow_call` - Can be called from other workflows
- Push to main branch

**Optional Inputs:**

- `update-major-minor` - Update major and minor version tags (default: `true`)

**Optional Secrets:**

- `BOT_ID` - GitHub App ID for custom bot
- `BOT_KEY` - GitHub App private key for custom bot

**Example Usage:**

```yaml
name: Release

on:
  push:
    branches:
      - main

jobs:
  release:
    permissions: # Optional - remove if using BOT_ID / BOT_KEY
      contents: write
    uses: starburst997/workflows/.github/workflows/release.yml@v1
    with:
      update-major-minor: true # Optional, defaults to true
    secrets: inherit
```

## Setup

To use these workflows in your repository:

1. Add the required secrets to your repository settings:

   - `CLAUDE_CODE_OAUTH_TOKEN` - Obtain from Claude Code
   - `BOT_ID` and `BOT_KEY` (optional) - For custom GitHub App integration

2. Create a workflow file in your repository's `.github/workflows/` directory

3. Reference the desired workflow using the examples above

## License

MIT
