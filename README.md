# Claude Reusable Workflows

Reusable GitHub Actions workflows for integrating Claude AI into your development workflow.

## What's Included

- **Claude Code** (`claude-reusable.yml`): Respond to comments, issues, and PR events with Claude AI
- **Claude Code Review** (`claude-code-review-reusable.yml`): Automatically review pull requests with Claude

## Usage

Call these workflows from your repository:

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    secrets: inherit
```

## Requirements

- GitHub App (App ID and private key)
- Claude Code OAuth token
- Repository secrets: `BOT_ID`, `BOT_KEY`, `CLAUDE_CODE_OAUTH_TOKEN`

## License

MIT
