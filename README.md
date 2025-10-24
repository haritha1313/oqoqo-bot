# OQOQO Bot

> **Intelligent PR Analysis & Documentation Bot powered by Hot-Path semantic analysis and AI**

OQOQO Bot automatically reviews your pull requests, analyzes code changes at a semantic level, and helps maintain high-quality documentation using AI-powered suggestions.

## What Does It Do?

When you open a PR, OQOQO Bot will:

1. **Analyze your code changes** using 5-layer semantic analysis (not just diff lines!)
2. **Check documentation coverage** for new/modified functions
3. **Post an intelligent comment** with prioritized recommendations
4. **Generate documentation** automatically when you comment `/update-docs`

## Quick Start (2 Minutes)

### GitHub Action (Recommended)

The easiest way! Just create a workflow file:

**Step 1:** Create `.github/workflows/oqoqo-bot.yml` in your repository:

```yaml
name: OQOQO Bot

on:
  pull_request:
    types: [opened, synchronize, reopened]
  issue_comment:
    types: [created]

permissions:
  contents: read
  pull-requests: write
  issues: write

jobs:
  analyze:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: OQOQO Bot – PR Analysis & Documentation
        uses: haritha1313/oqoqo-bot@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          enable-llm: 'true' # Set to 'false' for free mode
          anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
```

**Step 2:** (Optional) Add API key for AI features:
- Go to **Settings** → **Secrets and variables** → **Actions**
- Add `OPENAI_API_KEY` or `ANTHROPIC_API_KEY`

**Step 3:** Create a PR and watch the bot comment! 🎉

See [example-usage.yml](.github/workflows/example-usage.yml) for the full workflow including `/update-docs` command.

--

## Configuration

### Basic Configuration

Edit `.github/workflows/pr-bot.yml`:

```yaml
env:
  # Enable or disable AI features
  ENABLE_LLM: 'true'              # Set to 'false' for free mode (no AI)

  # Documentation settings
  DOCS_DIR: 'docs'                # Where your docs are stored
  DOCS_EXTRAS: 'README.md'        # Additional files to check

  # API keys (set in GitHub Secrets, not here!)
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```
No action needed! Just open a PR and wait for the bot comment.

### Manual Documentation Generation

1. Open your pull request
2. Add a comment: `/update-docs`
3. Wait ~1-2 minutes
4. The bot will:
   - Generate documentation for new/changed functions
   - Commit directly to your PR branch
   - Reply with a summary
## Troubleshooting

### Bot doesn't post comments

**Check GitHub Actions permissions:**
1. Go to Settings → Actions → General
2. Ensure "Read and write permissions" is enabled
3. Check "Allow GitHub Actions to create and approve pull requests"

**Check workflow run:**
1. Go to the **Actions** tab in your repository
2. Find the failed workflow run
3. Check the logs for errors

### `/update-docs` command doesn't work

**Missing API key:**
- Make sure you added `OPENAI_API_KEY` or `ANTHROPIC_API_KEY` to GitHub Secrets
- Check the workflow logs for "API key not configured" errors

**No documentation generated:**
- The bot only documents functions that are new or modified
- Check that you have a `docs/` directory (or the directory specified in `DOCS_DIR`)

### High API costs

**Cost control features:**
- The bot limits to 3 suggestions per PR
- Use free mode by setting `ENABLE_LLM: 'false'`
- Use Anthropic Claude (generally cheaper than OpenAI)

**To switch AI providers:**

Edit `.github/workflows/pr-bot.yml`:
```yaml
# Use only Anthropic
ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
# OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}  # Comment out
```

### Bot not detecting certain files

**Configure exclusions:**

The bot automatically excludes:
- `scripts/` - Infrastructure code
- `.github/` - Workflow files
- `tests/` - Test files
- `node_modules/`, `venv/` - Dependencies

To customize, edit `src/pr_bot.py` around line 50.

---
