# Quick Start Guide

Get Claude AI working in your repository in 5 minutes.

## Step 1: Set Up Secrets

Add these three secrets to your repository (Settings → Secrets and variables → Actions):

1. `BOT_ID` - Your GitHub App ID
2. `BOT_KEY` - Your GitHub App private key
3. `CLAUDE_CODE_OAUTH_TOKEN` - Your Claude Code OAuth token

**Don't have these?**
- GitHub App: Create at [github.com/settings/apps/new](https://github.com/settings/apps/new)
- Claude Token: Get from [claude.ai/code](https://claude.ai/code)

## Step 2: Choose Your Method

### Method A: Copy Templates (Easiest)

1. Copy files from `templates/` to your `.github/workflows/`
2. Done! Claude is now active in your repo

### Method B: Call Reusable Workflow

Create `.github/workflows/claude.yml` in your repo:

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
    secrets: inherit
```

## Step 3: Test It

1. Create an issue or PR
2. Comment with `/jd` (or your trigger phrase)
3. Claude will respond!

## Common Customizations

### Change Trigger Phrase

```yaml
with:
  trigger_phrase: '/claude'
  branch_prefix: 'claude/'
  label_trigger: claude
```

### Add Auto Code Review

Create `.github/workflows/claude-review.yml`:

```yaml
name: Claude Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    uses: starburst997/workflows/.github/workflows/claude-code-review-reusable.yml@main
    secrets: inherit
```

### Enable Commit Signing

```yaml
with:
  use_commit_signing: true
```

## Need Help?

See [REUSABLE_WORKFLOWS.md](REUSABLE_WORKFLOWS.md) for complete documentation.
