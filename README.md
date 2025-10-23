# OQOQO Bot 🤖

> **Intelligent PR Analysis & Documentation Bot powered by Hot-Path semantic analysis and AI**

OQOQO Bot automatically reviews your pull requests, analyzes code changes at a semantic level, and helps maintain high-quality documentation using AI-powered suggestions.

## 🎯 What Does It Do?

When you open a PR, OQOQO Bot will:

1. **Analyze your code changes** using 5-layer semantic analysis (not just diff lines!)
2. **Check documentation coverage** for new/modified functions
3. **Post an intelligent comment** with prioritized recommendations
4. **Generate documentation** automatically when you comment `/update-docs`

### Example Bot Comment

```
🔍 Hot-Path Analysis Results

📊 Changes Summary:
• 3 files modified
• 5 functions added/changed
• 2 documentation files affected

⚠️ High Priority:
• New function `processPayment()` in src/payment.ts needs documentation
• Major refactor in `UserService` - docs may need updating

💡 Suggestions:
• Document the payment processing flow
• Update API reference for UserService
• Add error handling examples

💰 Analysis cost: $0.03 (OpenAI GPT-4)
```

---

## 🚀 Quick Start (5 Minutes)

### Option 1: Add to Your Existing Repository

**Step 1: Copy the bot files**

```bash
# Clone the OQOQO Bot repository
git clone https://github.com/YOUR_USERNAME/oqoqo-bot.git

# Go to your project repository
cd /path/to/your/project

# Copy the bot files
cp -r ../oqoqo-bot/src ./bot
cp ../oqoqo-bot/requirements.txt ./bot-requirements.txt
cp ../oqoqo-bot/.github/workflows/pr-bot.yml ./.github/workflows/
```

**Step 2: Update the workflow file**

Edit `.github/workflows/pr-bot.yml` and change the paths:

```yaml
# Change this line (around line 54):
cd src/hotpath

# To match where you placed the files:
cd bot/hotpath
```

**Step 3: Install dependencies**

Add to your CI/CD or local setup:
```bash
pip install -r bot-requirements.txt
```

**Step 4: Configure GitHub Actions permissions**

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Actions** → **General**
3. Scroll to **Workflow permissions**
4. Select **"Read and write permissions"**
5. Check **"Allow GitHub Actions to create and approve pull requests"**
6. Click **Save**

**Step 5: (Optional) Enable AI Features**

To enable AI-powered documentation suggestions:

1. Get an API key:
   - **OpenAI**: https://platform.openai.com/api-keys
   - **Anthropic**: https://console.anthropic.com/settings/keys

2. Add to GitHub Secrets:
   - Go to **Settings** → **Secrets and variables** → **Actions**
   - Click **"New repository secret"**
   - Name: `OPENAI_API_KEY` or `ANTHROPIC_API_KEY`
   - Value: Your API key
   - Click **Add secret**

**Step 6: Test it!**

1. Create a test branch and make some code changes
2. Open a pull request
3. Wait ~30 seconds for the bot to post a comment
4. Try commenting `/update-docs` to test documentation generation

✅ **Done!** OQOQO Bot is now active on your repository.

---

### Option 2: Use as a GitHub Action (Simplest)

Coming soon! We're working on publishing this as a GitHub Action for one-line setup.

---

## ⚙️ Configuration

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

### Free vs Paid Mode

| Feature | Free Mode | Paid Mode (LLM) |
|---------|-----------|-----------------|
| Semantic code analysis | ✅ | ✅ |
| Change classification | ✅ | ✅ |
| Documentation coverage check | ✅ | ✅ |
| AI-powered suggestions | ❌ | ✅ |
| `/update-docs` command | ❌ | ✅ |
| Cost per PR | $0 | ~$0.02-0.10 |

**To use free mode:** Set `ENABLE_LLM: 'false'` in the workflow file.

---

## 📖 Usage Guide

### Automatic Analysis

The bot runs automatically on:
- ✅ Pull request opened
- ✅ New commits pushed to PR
- ✅ Pull request reopened

No action needed! Just open a PR and wait for the bot comment.

### Manual Documentation Generation

1. Open your pull request
2. Add a comment: `/update-docs`
3. Wait ~1-2 minutes
4. The bot will:
   - Generate documentation for new/changed functions
   - Commit directly to your PR branch
   - Reply with a summary

**Example:**
```
✅ Documentation updated!

Generated documentation for:
• processPayment() → docs/api/payment.md
• validateUser() → docs/api/auth.md

📝 2 files updated
💰 Cost: $0.08 (OpenAI GPT-4)
```

---

## 🏗️ How It Works

### 5-Layer Hot-Path Analysis

Unlike traditional diff tools, OQOQO Bot uses semantic analysis:

1. **Layer 1: Merkle Tree** - Efficient file change detection
2. **Layer 2: Semantic Analysis** - Classifies changes (refactor, minor, major, rewrite)
3. **Layer 3: Communities** - Understands code relationships and dependencies
4. **Layer 4: Cross-References** - Maps code to documentation
5. **Layer 5: Similarity** - Detects patterns and duplicated logic

### Architecture

```
📁 oqoqo-bot/
├── 📂 src/                      # Bot source code
│   ├── pr_bot.py                # Main analyzer (runs on PR events)
│   ├── update_docs.py           # Doc generator (runs on /update-docs)
│   ├── hotpath_integration.py   # Hot-Path wrapper
│   └── 📂 hotpath/              # Hot-Path analysis engine
│       ├── pipeline.py          # Main analysis pipeline
│       ├── semantic.py          # Semantic change detection
│       ├── llm_doc_generator.py # AI documentation generator
│       ├── doc_analyzer.py      # Doc coverage analyzer
│       ├── local_git_adapter.py # Git operations
│       └── pattern_matching.py  # Code pattern detection
├── 📂 .github/workflows/
│   └── pr-bot.yml               # GitHub Actions workflow
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment variable template
└── README.md                    # You are here!
```

---

## 🔧 Troubleshooting

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

## 🌍 Supported Languages

Currently supported:
- ✅ Python
- ✅ JavaScript
- ✅ TypeScript

Coming soon:
- Java
- Go
- Rust
- C/C++

Want to add a language? See [CONTRIBUTING.md](CONTRIBUTING.md)

---

## 💡 FAQ

**Q: Is my code sent to OpenAI/Anthropic?**
A: Only if you enable LLM features (`ENABLE_LLM: 'true'`) and add an API key. In free mode, all analysis runs locally.

**Q: How much does it cost?**
A: Free mode costs $0. Paid mode costs ~$0.02-0.10 per PR depending on PR size. The bot includes cost tracking and limits.

**Q: Can I use it on private repositories?**
A: Yes! It works on both public and private repositories.

**Q: Does it work with GitHub Enterprise?**
A: Yes, as long as GitHub Actions is enabled.

**Q: Can I customize the analysis?**
A: Yes! All source code is available. See [CONTRIBUTING.md](CONTRIBUTING.md) for customization guide.

**Q: What if I don't have a `docs/` directory?**
A: Set `DOCS_DIR` to your documentation location (e.g., `documentation/`, `wiki/`, or even `.` for root).

**Q: Can it update existing documentation?**
A: Yes! It can insert into existing files or create new ones.

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

- 🐛 **Report bugs** - Open an issue
- 💡 **Suggest features** - Open an issue with your idea
- 🔧 **Submit PRs** - See [CONTRIBUTING.md](CONTRIBUTING.md)
- ⭐ **Star the repo** - Help others discover it!

### Development Setup

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/oqoqo-bot.git
cd oqoqo-bot

# Install dependencies
pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Edit .env with your settings

# Run tests (if available)
python -m pytest tests/
```

---

## 📜 License

MIT License - see [LICENSE](LICENSE) file for details.

Free to use, modify, and distribute. Attribution appreciated!

---

## 🙏 Credits

Built with:
- **Hot-Path** semantic analysis technology
- **OpenAI** GPT-4 for documentation generation
- **Anthropic** Claude for intelligent analysis
- **GitHub Actions** for CI/CD automation

---

## 📞 Support

- 📫 **Issues**: [GitHub Issues](https://github.com/YOUR_USERNAME/oqoqo-bot/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/YOUR_USERNAME/oqoqo-bot/discussions)
- 📖 **Documentation**: This README + code comments

---

## 🗺️ Roadmap

- [ ] Publish as GitHub Action for one-line setup
- [ ] Support for more programming languages
- [ ] Custom prompt templates for documentation
- [ ] Integration with popular doc tools (Sphinx, JSDoc, etc.)
- [ ] Web dashboard for analytics
- [ ] Slack/Discord notifications

---

Made with ❤️ by the OQOQO Bot team

**⭐ Star us on GitHub if you find this useful!**
