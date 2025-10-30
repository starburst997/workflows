# Reusable Claude Workflows

This repository provides reusable GitHub Actions workflows for integrating Claude AI into your development process. These workflows can be called from any repository with inherited secrets.

## Available Workflows

### 1. Claude Code (claude-reusable.yml)
Enables Claude to respond to comments, issues, and PR events in your repository.

### 2. Claude Code Review (claude-code-review-reusable.yml)
Automatically triggers Claude to review pull requests with customizable criteria.

## Setup Instructions

### Prerequisites

1. **GitHub App**: Create a GitHub App or use an existing one
   - Note the App ID
   - Generate and save a private key

2. **Claude Code OAuth Token**: Obtain from [Claude Code](https://claude.ai/code)

3. **Repository Secrets**: Add these secrets to repositories that will use these workflows:
   - `BOT_ID`: Your GitHub App ID
   - `BOT_KEY`: Your GitHub App private key
   - `CLAUDE_CODE_OAUTH_TOKEN`: Your Claude Code OAuth token

### Installation

#### Option 1: Copy Templates (Recommended)

1. Copy the workflow files from the `templates/` directory to your repository's `.github/workflows/` directory
2. Rename them to remove the `-reusable` suffix if desired
3. The workflows are now ready to use in your repository

#### Option 2: Call as Reusable Workflows

If this repository's workflows are made callable (moved to `.github/workflows/`), you can call them from other repositories:

**Example: Using Claude Code Workflow**

```yaml
name: Claude Code

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned]
  pull_request_review:
    types: [submitted]

jobs:
  claude:
    if: |
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '/jd')) ||
      (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '/jd')) ||
      (github.event_name == 'pull_request_review' && contains(github.event.review.body, '/jd')) ||
      (github.event_name == 'issues' && (contains(github.event.issue.body, '/jd') || contains(github.event.issue.title, '/jd')))
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    with:
      trigger_phrase: '/jd'
      branch_prefix: 'jd/'
      label_trigger: jd
      plugin_marketplaces: 'https://github.com/starburst997/claude.git'
      plugins: 'common@jd'
      use_commit_signing: true
      claude_args: '--allowed-tools "Bash(gh issue view:*),Bash(gh search:*),Bash(gh issue list:*),Bash(gh pr comment:*),Bash(gh pr diff:*),Bash(gh pr view:*),Bash(gh pr list:*),Bash(gh pr create:*),WebSearch,WebFetch(domain:github.com),WebFetch(domain:*),Bash(mkdir:*),Bash(chmod:*)"'
    secrets: inherit
```

**Example: Using Claude Code Review Workflow**

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  claude-review:
    uses: starburst997/workflows/.github/workflows/claude-code-review-reusable.yml@main
    with:
      trigger_phrase: '/jd'
      branch_prefix: 'jd/'
      label_trigger: jd
      plugin_marketplaces: 'https://github.com/starburst997/claude.git'
      plugins: 'common@jd'
      use_commit_signing: true
      claude_args: '--allowed-tools "Bash(gh issue view:*),Bash(gh search:*),Bash(gh issue list:*),Bash(gh pr comment:*),Bash(gh pr diff:*),Bash(gh pr view:*),Bash(gh pr list:*),Bash(gh pr create:*),WebSearch,WebFetch(domain:github.com),WebFetch(domain:*),Bash(mkdir:*),Bash(chmod:*)"'
    secrets: inherit
```

## Configuration Options

### Claude Code Workflow Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `trigger_phrase` | Phrase that triggers Claude | No | `/jd` |
| `branch_prefix` | Prefix for branches created by Claude | No | `jd/` |
| `assignee_trigger` | Assignee name that triggers Claude | No | Auto-detected from bot |
| `label_trigger` | Label that triggers Claude | No | `jd` |
| `plugin_marketplaces` | Plugin marketplace repositories | No | `` |
| `plugins` | Plugins to load | No | `` |
| `claude_args` | Additional Claude arguments | No | `` |
| `use_commit_signing` | Enable commit signing | No | `false` |
| `custom_prompt` | Custom prompt for Claude | No | `` |

### Claude Code Review Workflow Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `trigger_phrase` | Phrase that triggers Claude | No | `/jd` |
| `branch_prefix` | Prefix for branches created by Claude | No | `jd/` |
| `assignee_trigger` | Assignee name that triggers Claude | No | Auto-detected from bot |
| `label_trigger` | Label that triggers Claude | No | `jd` |
| `plugin_marketplaces` | Plugin marketplace repositories | No | `` |
| `plugins` | Plugins to load | No | `` |
| `claude_args` | Additional Claude arguments | No | `` |
| `use_commit_signing` | Enable commit signing | No | `false` |
| `review_prompt` | Custom code review prompt | No | Default review prompt |
| `paths_filter` | File patterns to review | No | `` |
| `author_filter` | Only review PRs from specific authors | No | `` |

### Required Secrets

All workflows require these secrets (use `secrets: inherit` to pass them automatically):

- `BOT_ID`: GitHub App ID
- `BOT_KEY`: GitHub App private key
- `CLAUDE_CODE_OAUTH_TOKEN`: Claude Code OAuth token

## Usage Examples

### Basic Setup

Minimal configuration using default settings:

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    secrets: inherit
```

### Custom Trigger Phrase

Use a different trigger phrase:

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    with:
      trigger_phrase: '/claude'
      branch_prefix: 'claude/'
      label_trigger: claude
    secrets: inherit
```

### With Plugins

Enable custom plugins:

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    with:
      plugin_marketplaces: 'https://github.com/your-org/claude-plugins.git'
      plugins: 'plugin-name@version'
    secrets: inherit
```

### Code Review with Filters

Only review specific file types:

```yaml
on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - 'src/**/*.ts'
      - 'src/**/*.tsx'

jobs:
  claude-review:
    uses: starburst997/workflows/.github/workflows/claude-code-review-reusable.yml@main
    with:
      paths_filter: 'src/**/*.ts,src/**/*.tsx'
    secrets: inherit
```

### Review Only External Contributors

```yaml
jobs:
  claude-review:
    if: |
      github.event.pull_request.user.login == 'external-contributor' ||
      github.event.pull_request.author_association == 'FIRST_TIME_CONTRIBUTOR'
    uses: starburst997/workflows/.github/workflows/claude-code-review-reusable.yml@main
    secrets: inherit
```

## Advanced Configuration

### Custom Allowed Tools

Restrict Claude's available tools:

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    with:
      claude_args: '--allowed-tools "Bash(gh issue view:*),Bash(gh pr view:*),WebSearch"'
    secrets: inherit
```

### Commit Signing

Enable GPG commit signing:

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    with:
      use_commit_signing: true
    secrets: inherit
```

### Custom Review Prompt

Provide a custom code review prompt:

```yaml
jobs:
  claude-review:
    uses: starburst997/workflows/.github/workflows/claude-code-review-reusable.yml@main
    with:
      review_prompt: |
        Review this PR focusing on:
        - TypeScript type safety
        - React best practices
        - Accessibility concerns

        Use gh pr comment to post your review.
    secrets: inherit
```

## Migration Guide

### From Local Workflows to Reusable Workflows

If you have existing Claude workflows in your repository:

1. **Backup**: Save your current workflow files
2. **Replace**: Update your workflow to call the reusable workflow
3. **Configure**: Add any custom inputs you were using
4. **Test**: Trigger the workflow to ensure it works correctly

Example migration:

**Before** (local workflow):
```yaml
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          # ... many more config options
```

**After** (reusable workflow):
```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    with:
      trigger_phrase: '/jd'
    secrets: inherit
```

## Troubleshooting

### Secrets Not Available

**Problem**: Workflow fails with "secret not found" error

**Solution**: Ensure you're using `secrets: inherit` in the calling workflow, or explicitly pass the secrets:

```yaml
secrets:
  BOT_ID: ${{ secrets.BOT_ID }}
  BOT_KEY: ${{ secrets.BOT_KEY }}
  CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

### Workflow Not Triggering

**Problem**: Claude doesn't respond to comments

**Solution**: Check that:
1. The trigger condition includes your event type
2. The trigger phrase matches your comment
3. The bot has appropriate permissions

### Permission Errors

**Problem**: Bot cannot create branches or comments

**Solution**: Verify your GitHub App has these permissions:
- Contents: Read & Write
- Pull Requests: Read & Write
- Issues: Read & Write
- Actions: Read (for CI status)

## Support

For issues or questions:
- Check the [Claude Code documentation](https://docs.claude.com/en/docs/claude-code)
- Review the [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action) repository
- Open an issue in this repository

## License

See [LICENSE](LICENSE) file for details.
