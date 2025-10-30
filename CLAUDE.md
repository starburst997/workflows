# CLAUDE.md

This file provides guidance to Claude (claude.ai/code) when working with code in this repository.

## Repository Purpose

This repository contains reusable GitHub Actions workflows for automating development tasks with AI assistance. The workflows are designed to be called from other repositories using the `workflow_call` trigger pattern.

## Key Workflows Architecture

### 1. claude.yml - AI-Powered Issue/PR Management

- **Dual trigger system**: Responds to both `workflow_call` (reusable) and event triggers (issue comments, PR reviews, labels)
- **Two-job pattern**:
  - `label-comment` job: Adds comment to trigger JD when label is applied
  - `claude` job: Main AI processing with complex conditional logic
- **Trigger phrase**: `/jd` in comments, issues, or PR reviews
- **Label trigger**: `jd` label
- **Bot rebranding**: Post-processing step that replaces references to "Claude" with "JD" in completion comments, removes bot mention tasks, and strips PR creation links and branch footers
- **Plugin integration**: Uses `common@jd` plugin from custom marketplace (starburst997/claude)
- **Restricted tool access**: Uses `claude_args` with `--allowed-tools` to limit operations (git, gh CLI, web operations, basic file operations)

### 2. claude-code-review.yml - Automated PR Review

- **Simpler architecture**: Single job triggered on PR opened/ready for review
- **Inline prompt**: Embedded review guidelines directly in workflow
- **Review guidance**: Focuses on code quality, bugs, performance, security, and test coverage
- **Uses gh CLI**: Review comments posted via `gh pr comment`

### 3. gh-pages.yml - Documentation Deployment

- **Two-stage deployment**: Build and deploy jobs
- **Triggers**: Pushes to main affecting docs/ directory or workflow file

### 4. release.yml - Automated Versioning

- **Auto-versioning**: Uses starburst997/auto-version for semantic versioning
- **Changelog generation**: Automated with starburst997/commits-logs

## Common Patterns

### Custom Bot Initialization

All workflows use `starburst997/custom-bot-init@v1` to handle authentication:

- Optional bot credentials via `BOT_ID` and `BOT_KEY` secrets
- Generates bot token and metadata (name, slug) for subsequent steps

## Important Conventions

### Naming and Branding

- AI assistant referred to as "JD" not "Claude" in output
- Bot username convention: `jd-botvin[bot]`

### Trigger Authorization

Workflows only execute when triggered by:

- Repository owner (`github.repository_owner`)
- The configured bot (`jd-botvin[bot]`)

This prevents unauthorized usage and controls costs for AI powered workflows.

## Documentation Sync

When modifying workflows, update:

1. **README.md** - User-facing documentation with usage examples
2. **docs/index.html** - GitHub Pages documentation (if it exists)

Key sections to update:

- Workflow triggers and secrets
- Example usage YAML snippets
- Available workflows table
