# CodeGov PR Scan Action

Detects AI-authored commits in your pull requests and posts a provenance summary as a PR comment.

Identifies commits by Claude Code, Cursor, GitHub Copilot, Devin, and Aider from git metadata alone. Zero configuration required.

## Usage

Add to `.github/workflows/codegov.yml`:

```yaml
name: CodeGov
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: james-wall/codegov-action@v1
```

## What it does

On every PR, the action:

1. Scans all commits in the PR for AI authorship signals
2. Posts (or updates) a comment with a provenance summary:

> ### CodeGov: AI Provenance Scan
>
> **67%** of commits in this PR are AI-authored (2/3)
>
> Agents: **Claude Code** (2)
>
> | Commit | Agent | Changes | Confidence |
> |--------|-------|---------|------------|
> | `a1b2c3d` | Claude Code (Claude Opus 4.6) | +142/-8 | 95% |
> | `e4f5g6h` | Claude Code (Claude Sonnet 4.6) | +23/-5 | 95% |

If no AI-authored commits are found, it posts a clean summary instead.

## How detection works

The action reads git metadata (author, co-author trailers, commit body) — it never reads your source code.

| Tool | Signals |
|------|---------|
| Claude Code | `Co-Authored-By: Claude <noreply@anthropic.com>` |
| Cursor | `cursoragent@cursor.com` author, `cursor[bot]` author |
| Copilot | `copilot@github.com` co-author, `copilot-swe-agent[bot]` author |
| Devin | `devin-ai-integration[bot]` author |
| Aider | `aider (model) <aider@aider.chat>` co-author |

## Requirements

- `actions/checkout` with `fetch-depth: 0` (needs full git history for the PR range)
- `pull-requests: write` permission (to post comments)

## License

MIT
