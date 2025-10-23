# OQOQO Bot - Intelligent PR Analysis & Documentation Bot

OQOQO Bot is an intelligent GitHub PR bot that uses Hot-Path semantic analysis and LLM-powered insights to automatically analyze code changes, suggest documentation updates, and help maintain high-quality documentation for your projects.

## Features

- **Hot-Path Semantic Analysis**: 5-layer deep code analysis
  - Layer 1: Merkle Tree (file changes)
  - Layer 2: Semantic Analysis (change classification)
  - Layer 3: Communities (code relationships)
  - Layer 4: Cross-References (code-to-docs mapping)
  - Layer 5: Similarity (code patterns)

- **Intelligent PR Comments**: Automatically posts analysis comments on PRs with:
  - Code change impact assessment
  - Documentation coverage analysis
  - Prioritized recommendations

- **AI-Powered Documentation**: Optional LLM integration for:
  - Smart documentation suggestions
  - Automated doc generation via `/update-docs` command
  - Support for OpenAI and Anthropic models

- **Cost Control**: Built-in cost tracking and limits for LLM usage

## Setup

### Prerequisites

- Python 3.8 or higher
- GitHub repository with Actions enabled
- (Optional) OpenAI or Anthropic API key for LLM features

### Installation

1. **Add the bot to your repository**:

   Create the directory structure:
   ```bash
   mkdir -p .github/workflows
   ```

2. **Copy the workflow file**:

   Copy `.github/workflows/pr-bot.yml` to your repository's `.github/workflows/` directory.

3. **Add the bot source code**:

   Copy the `src/` directory to your repository (you can place it in a `scripts/` or `bot/` directory).

4. **Install dependencies**:

   Add to your repository:
   ```bash
   pip install -r requirements.txt
   ```

5. **Configure GitHub Secrets** (for LLM features):

   Go to your repository Settings > Secrets and variables > Actions, and add:
   - `OPENAI_API_KEY` (optional) - for OpenAI models
   - `ANTHROPIC_API_KEY` (optional) - for Anthropic Claude models

   Note: `GITHUB_TOKEN` is automatically provided by GitHub Actions.

### Configuration

Edit the workflow file `.github/workflows/pr-bot.yml` to customize:

```yaml
env:
  ENABLE_LLM: 'true'        # Set to 'false' to disable LLM features
  DOCS_DIR: 'docs'          # Your documentation directory
  DOCS_EXTRAS: 'README.md'  # Additional files to check
```

## Usage

### Automatic PR Analysis

The bot automatically runs when:
- A pull request is opened
- A pull request is synchronized (new commits pushed)
- A pull request is reopened

It will post a comment with:
- Semantic analysis of code changes
- Documentation impact assessment
- Recommended actions

### Manual Documentation Updates

To trigger documentation generation:

1. Comment on the PR with:
   ```
   /update-docs
   ```

2. The bot will:
   - Analyze the code changes
   - Generate documentation using LLM
   - Commit and push to the PR branch
   - Update the bot comment with results

## How It Works

### Analysis Pipeline

```
PR Event
  ↓
Hot-Path Analysis (5 layers)
  ├─ File changes (Merkle Tree)
  ├─ Semantic classification
  ├─ Code communities
  ├─ Cross-references
  └─ Similarity patterns
  ↓
LLM Suggestions (optional)
  ↓
PR Comment Posted
```

### Documentation Update Flow

```
User: /update-docs
  ↓
Hot-Path + Entity Analysis
  ↓
LLM Documentation Generation
  ↓
Insert into Doc Files
  ↓
Git Commit + Push
  ↓
Update PR Comment
```

## Architecture

```
oqoqo-bot/
├── src/
│   ├── pr_bot.py                 # Main PR analysis bot
│   ├── update_docs.py            # Documentation updater
│   ├── hotpath_integration.py    # Hot-Path interface
│   └── hotpath/                  # Hot-Path module
│       ├── pipeline.py           # Analysis pipeline
│       ├── semantic.py           # Semantic analysis
│       ├── llm_doc_generator.py  # LLM integration
│       ├── doc_analyzer.py       # Documentation analyzer
│       ├── local_git_adapter.py  # Git adapter
│       └── pattern_matching.py   # Pattern matching
├── .github/
│   └── workflows/
│       └── pr-bot.yml            # GitHub Actions workflow
├── requirements.txt              # Python dependencies
├── .env.example                  # Environment template
└── README.md                     # This file
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `REPO` | Yes | Repository in format `owner/repo` |
| `PR_NUMBER` | Yes | Pull request number |
| `BASE_SHA` | Yes | Base commit SHA |
| `HEAD_SHA` | Yes | Head commit SHA |
| `GITHUB_TOKEN` | Yes | GitHub API token (auto-provided) |
| `ENABLE_LLM` | No | Enable LLM features (`true`/`false`) |
| `OPENAI_API_KEY` | No | OpenAI API key (for LLM) |
| `ANTHROPIC_API_KEY` | No | Anthropic API key (for LLM) |
| `DOCS_DIR` | No | Documentation directory (default: `docs`) |
| `DOCS_EXTRAS` | No | Additional doc files (default: `README.md`) |

## Cost Control

When LLM features are enabled:
- Maximum 3 documentation suggestions per PR
- Cost tracking per request
- Support for both OpenAI and Anthropic
- Configurable model selection

## Supported Languages

- Python
- JavaScript
- TypeScript

(More languages can be added via the Hot-Path semantic analyzer)

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Support

For issues and questions, please open an issue on GitHub.

## Credits

Built with Hot-Path semantic analysis technology and powered by LLM integration for intelligent documentation generation.
