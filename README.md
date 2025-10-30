# Claude Reusable Workflows

Reusable GitHub Actions workflows for integrating Claude AI into your development workflow.

## Quick Start

See [QUICKSTART.md](QUICKSTART.md) to get started in 5 minutes.

## What's Included

This repository provides reusable workflows for:

- **Claude Code**: Respond to comments, issues, and PR events with Claude AI
- **Claude Code Review**: Automatically review pull requests with Claude

## Documentation

- [Quick Start Guide](QUICKSTART.md) - Get up and running fast
- [Complete Documentation](REUSABLE_WORKFLOWS.md) - Full configuration reference
- [Templates](templates/) - Ready-to-use workflow files

## Usage

### Option 1: Copy Templates

Copy workflow files from `templates/` to your `.github/workflows/` directory.

### Option 2: Call as Reusable Workflow

```yaml
jobs:
  claude:
    uses: starburst997/workflows/.github/workflows/claude-reusable.yml@main
    secrets: inherit
```

See [REUSABLE_WORKFLOWS.md](REUSABLE_WORKFLOWS.md) for complete examples and configuration options.

## Requirements

- GitHub App (with App ID and private key)
- Claude Code OAuth token
- Repository secrets configured

## License

MIT - See [LICENSE](LICENSE) for details.
