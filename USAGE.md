# Reusable Claude Workflows

This repository contains reusable GitHub Actions workflows for integrating Claude AI into your development workflow.

## Available Workflows

### 1. Claude Code (`claude.yml`)

A comprehensive workflow that responds to issue comments, PR comments, and issue assignments to provide AI-powered coding assistance.

### 2. Claude Code Review (`claude-code-review.yml`)

Automated code review workflow that provides feedback on pull requests.

## Using These Workflows

### Prerequisites

You need the following secrets in your repository:
- `BOT_ID` - Your GitHub App ID
- `BOT_KEY` - Your GitHub App private key
- `CLAUDE_CODE_OAUTH_TOKEN` - Your Claude Code OAuth token

### Example: Using Claude Code Workflow

Create a workflow file in your repository (e.g., `.github/workflows/claude.yml`):

```yaml
name: Claude Code Assistant

on:
  issue_comment:
    types: [created]
  issues:
    types: [opened, assigned]

jobs:
  claude-assistant:
    uses: starburst997/workflows/.github/workflows/claude.yml@main
    secrets: inherit
```

### Example: Using Claude Code Review Workflow

```yaml
name: Automated Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  code-review:
    uses: starburst997/workflows/.github/workflows/claude-code-review.yml@main
    secrets: inherit
```

### Example: Using with Custom Inputs

You can override default values by providing inputs:

```yaml
name: Claude Code with Custom Settings

on:
  issue_comment:
    types: [created]

jobs:
  claude-custom:
    uses: starburst997/workflows/.github/workflows/claude.yml@main
    with:
      trigger_phrase: '/claude'
      branch_prefix: 'claude/'
      plugins: 'my-custom-plugin@v1'
      plugin_marketplaces: 'https://github.com/myorg/claude-plugins.git'
    secrets: inherit
```

## Available Inputs

### Claude Code Workflow

| Input | Description | Default |
|-------|-------------|---------|
| `trigger_phrase` | The phrase that triggers Claude | `/jd` |
| `branch_prefix` | Prefix for created branches | `jd/` |
| `plugins` | Plugins to load | `common@jd` |
| `plugin_marketplaces` | Plugin marketplace URLs | `https://github.com/starburst997/claude.git` |
| `allowed_bots` | Allowed bot slugs | Auto-detected |

### Claude Code Review Workflow

| Input | Description | Default |
|-------|-------------|---------|
| `pr_number` | Pull request number to review | Auto-detected |
| `plugins` | Plugins to load | `common@jd` |
| `plugin_marketplaces` | Plugin marketplace URLs | `https://github.com/starburst997/claude.git` |
| `trigger_phrase` | The trigger phrase | `/jd` |
| `branch_prefix` | Prefix for created branches | `jd/` |

## Secrets Inheritance

Both workflows use `secrets: inherit` which means:
- All secrets from the calling repository are automatically available
- You don't need to explicitly pass each secret
- The required secrets (`BOT_ID`, `BOT_KEY`, `CLAUDE_CODE_OAUTH_TOKEN`) must exist in your repository

## How It Works

1. **Trigger**: The workflow is triggered by GitHub events (comments, PRs, issues)
2. **Authentication**: Uses your GitHub App credentials for authentication
3. **Claude Integration**: Runs Claude Code with the specified configuration
4. **Response**: Claude analyzes the request and provides assistance through comments or code changes

## Local Usage (This Repository)

If you're working on this repository itself, the workflows can also be triggered locally without the `workflow_call` trigger. They will work as before with the configured triggers.

## Support

For issues or questions:
- File an issue in this repository
- Check the [Claude Code documentation](https://docs.claude.com/en/docs/claude-code)
- Review the [claude-code-action repository](https://github.com/anthropics/claude-code-action)
