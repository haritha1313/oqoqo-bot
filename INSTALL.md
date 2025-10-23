# Installation Guide

Detailed step-by-step instructions for installing OQOQO Bot on your repository.

## Table of Contents

- [Quick Install (5 minutes)](#quick-install)
- [Detailed Install (First Time)](#detailed-install)
- [Configuration Options](#configuration-options)
- [Verification & Testing](#verification--testing)
- [Troubleshooting](#troubleshooting)

---

## Quick Install

For experienced users who just need the commands:

```bash
# Clone OQOQO Bot
git clone https://github.com/YOUR_USERNAME/oqoqo-bot.git

# Navigate to your project
cd /path/to/your/project

# Copy files
cp -r ../oqoqo-bot/src ./bot
cp ../oqoqo-bot/.github/workflows/pr-bot.yml ./.github/workflows/

# Update workflow paths in .github/workflows/pr-bot.yml:
# Change "cd src/hotpath" to "cd bot/hotpath"

# Configure GitHub Actions permissions:
# Settings → Actions → General → Workflow permissions → Read and write

# (Optional) Add API keys:
# Settings → Secrets and variables → Actions → New secret
# Add OPENAI_API_KEY or ANTHROPIC_API_KEY

# Test by creating a PR!
```

---

## Detailed Install

### Prerequisites

Before you begin, make sure you have:

- ✅ A GitHub account
- ✅ A GitHub repository (public or private)
- ✅ GitHub Actions enabled (usually enabled by default)
- ✅ Basic knowledge of git commands
- ✅ (Optional) OpenAI or Anthropic API key for AI features

### Step-by-Step Installation

#### Step 1: Clone OQOQO Bot Repository

Open your terminal and run:

```bash
git clone https://github.com/YOUR_USERNAME/oqoqo-bot.git
```

This will create a folder called `oqoqo-bot` with all the bot files.

**Expected output:**
```
Cloning into 'oqoqo-bot'...
remote: Enumerating objects: 25, done.
remote: Counting objects: 100% (25/25), done.
...
```

#### Step 2: Navigate to Your Project

Go to the repository where you want to install the bot:

```bash
cd /path/to/your/project
```

**Example:**
```bash
cd ~/projects/my-awesome-app
```

#### Step 3: Create Required Directories

If they don't exist already:

```bash
mkdir -p .github/workflows
```

This creates the folder structure needed for GitHub Actions.

#### Step 4: Copy Bot Files

Copy the bot source code to your project:

```bash
cp -r ../oqoqo-bot/src ./bot
```

This creates a `bot/` directory in your project with all the bot code.

**Your project structure now looks like:**
```
your-project/
├── .github/
│   └── workflows/
├── bot/               ← New!
│   ├── pr_bot.py
│   ├── update_docs.py
│   └── hotpath/
├── src/              ← Your existing code
└── ...
```

#### Step 5: Copy Workflow File

Copy the GitHub Actions workflow:

```bash
cp ../oqoqo-bot/.github/workflows/pr-bot.yml ./.github/workflows/
```

#### Step 6: Update Workflow Paths

Open `.github/workflows/pr-bot.yml` in your text editor.

**Find these lines (around line 54 and 124):**
```yaml
cd src/hotpath
export PYTHONPATH="${PYTHONPATH}:$(pwd)"
cd ..
```

**Change them to:**
```yaml
cd bot/hotpath
export PYTHONPATH="${PYTHONPATH}:$(pwd)"
cd ..
```

**Why?** We placed the bot files in `bot/` instead of `src/`, so we need to update the paths.

#### Step 7: Install Dependencies (Optional)

If you want to test the bot locally:

```bash
pip install requests openai anthropic
```

Or add to your existing `requirements.txt`:
```
requests>=2.31.0
openai>=1.0.0
anthropic>=0.18.0
```

**Note:** GitHub Actions will install these automatically, so this step is optional.

#### Step 8: Configure GitHub Actions Permissions

This is **critical** for the bot to work!

1. Go to your repository on GitHub
2. Click **Settings** (top menu)
3. Click **Actions** (left sidebar)
4. Click **General**
5. Scroll down to **Workflow permissions**
6. Select **"Read and write permissions"**
7. Check ✅ **"Allow GitHub Actions to create and approve pull requests"**
8. Click **Save**

**Screenshot guide:**
```
Settings → Actions → General
↓
Workflow permissions:
○ Read repository contents and packages permissions
● Read and write permissions  ← Select this
☑ Allow GitHub Actions to create and approve pull requests  ← Check this
```

#### Step 9: (Optional) Enable AI Features

To get AI-powered documentation suggestions, you need an API key.

##### Get an API Key

Choose one:

**Option A: OpenAI (GPT-4)**
1. Go to https://platform.openai.com/api-keys
2. Sign up or log in
3. Click "Create new secret key"
4. Copy the key (starts with `sk-...`)
5. **Important:** Save it somewhere safe!

**Option B: Anthropic (Claude)**
1. Go to https://console.anthropic.com/settings/keys
2. Sign up or log in
3. Click "Create Key"
4. Copy the key
5. **Important:** Save it somewhere safe!

##### Add API Key to GitHub Secrets

1. Go to your repository on GitHub
2. Click **Settings** (top menu)
3. Click **Secrets and variables** (left sidebar)
4. Click **Actions**
5. Click **"New repository secret"**
6. For OpenAI:
   - Name: `OPENAI_API_KEY`
   - Secret: Paste your API key
   - Click **Add secret**
7. For Anthropic:
   - Name: `ANTHROPIC_API_KEY`
   - Secret: Paste your API key
   - Click **Add secret**

**Security note:** Secrets are encrypted and never shown in logs. Don't commit API keys to your repository!

#### Step 10: Commit Changes

Add the bot files to your repository:

```bash
git add .github/workflows/pr-bot.yml bot/
git commit -m "Add OQOQO Bot for PR analysis"
git push origin main
```

#### Step 11: Test the Bot

Time to see if it works!

1. **Create a test branch:**
   ```bash
   git checkout -b test-oqoqo-bot
   ```

2. **Make a small code change:**
   ```bash
   # Edit any file, e.g., add a function
   echo "def test_function():\n    return 'Hello OQOQO!'" >> test.py
   git add test.py
   git commit -m "Test OQOQO Bot"
   git push origin test-oqoqo-bot
   ```

3. **Open a Pull Request:**
   - Go to your repository on GitHub
   - Click "Pull requests"
   - Click "New pull request"
   - Select your test branch
   - Click "Create pull request"

4. **Wait for the bot** (~30 seconds):
   - Go to the "Actions" tab to watch it run
   - The bot should post a comment on your PR

**Expected bot comment:**
```
🔍 Hot-Path Analysis Results

📊 Changes Summary:
• 1 file modified
• 1 function added
...
```

✅ **Success!** If you see the bot comment, installation is complete!

---

## Configuration Options

### Basic Configuration

Edit `.github/workflows/pr-bot.yml`:

```yaml
env:
  # Enable/disable AI features
  ENABLE_LLM: 'true'              # 'true' = AI on, 'false' = AI off

  # Documentation settings
  DOCS_DIR: 'docs'                # Where your docs are
  DOCS_EXTRAS: 'README.md'        # Extra files to check

  # API keys (stored in GitHub Secrets)
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

### Configuration Scenarios

#### Scenario 1: Free Mode (No AI)

If you don't want to use AI features:

```yaml
env:
  ENABLE_LLM: 'false'  # Disable AI
  DOCS_DIR: 'docs'
```

- ✅ Semantic analysis still works
- ✅ Documentation coverage check still works
- ❌ No AI suggestions
- ❌ No `/update-docs` command
- 💰 Cost: $0

#### Scenario 2: AI Mode with OpenAI

```yaml
env:
  ENABLE_LLM: 'true'
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  # Don't set ANTHROPIC_API_KEY
```

- ✅ All features enabled
- 🤖 Uses GPT-4 for documentation
- 💰 Cost: ~$0.02-0.10 per PR

#### Scenario 3: AI Mode with Anthropic

```yaml
env:
  ENABLE_LLM: 'true'
  ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
  # Don't set OPENAI_API_KEY
```

- ✅ All features enabled
- 🤖 Uses Claude for documentation
- 💰 Cost: ~$0.01-0.05 per PR (usually cheaper)

#### Scenario 4: Custom Documentation Location

If your docs are in a different folder:

```yaml
env:
  ENABLE_LLM: 'true'
  DOCS_DIR: 'documentation'       # Custom location
  DOCS_EXTRAS: 'README.md,GUIDE.md'  # Multiple files
```

### Advanced: Custom Bot Location

If you want to put bot files elsewhere:

1. Copy to different location:
   ```bash
   cp -r ../oqoqo-bot/src ./scripts/oqoqo-bot
   ```

2. Update workflow file:
   ```yaml
   run: |
     cd scripts/oqoqo-bot/hotpath
     export PYTHONPATH="${PYTHONPATH}:$(pwd)"
     cd ..
     python pr_bot.py
   ```

---

## Verification & Testing

### Verify Installation

**Checklist:**

- [ ] Files copied to `bot/` directory
- [ ] Workflow file at `.github/workflows/pr-bot.yml`
- [ ] Workflow paths updated to use `bot/` instead of `src/`
- [ ] GitHub Actions permissions set to "Read and write"
- [ ] (Optional) API keys added to GitHub Secrets

### Test Without API Keys (Free Mode)

1. Set `ENABLE_LLM: 'false'` in workflow file
2. Create a test PR
3. Bot should post analysis without AI suggestions

**Expected comment:**
```
🔍 Hot-Path Analysis Results
(No AI suggestions - ENABLE_LLM is false)
```

### Test With API Keys (AI Mode)

1. Set `ENABLE_LLM: 'true'` in workflow file
2. Add API key to GitHub Secrets
3. Create a test PR
4. Bot should post analysis with AI suggestions

**Expected comment:**
```
🔍 Hot-Path Analysis Results
💡 AI Suggestions:
• Add documentation for...
```

### Test `/update-docs` Command

1. Open a PR with code changes
2. Wait for bot analysis
3. Comment on the PR: `/update-docs`
4. Wait ~1-2 minutes
5. Bot should commit documentation to your branch

**Expected:**
- New commit on PR branch
- Updated bot comment with success message

---

## Troubleshooting

### Problem: Bot doesn't post comments

**Solution 1: Check permissions**
- Settings → Actions → General
- Ensure "Read and write permissions" enabled

**Solution 2: Check workflow run**
- Go to Actions tab
- Find the workflow run
- Check for errors

**Solution 3: Check workflow file**
- Ensure paths are correct (`bot/hotpath` not `src/hotpath`)
- Check YAML syntax

### Problem: "Permission denied" error

**Cause:** GitHub Actions doesn't have write permission.

**Solution:**
1. Settings → Actions → General
2. Workflow permissions → "Read and write permissions"
3. Save and re-run the workflow

### Problem: `/update-docs` doesn't work

**Cause 1:** API key not configured

**Solution:**
- Add `OPENAI_API_KEY` or `ANTHROPIC_API_KEY` to GitHub Secrets
- Make sure secret name exactly matches

**Cause 2:** ENABLE_LLM is false

**Solution:**
- Set `ENABLE_LLM: 'true'` in workflow file

### Problem: High API costs

**Solution 1:** Use free mode
```yaml
ENABLE_LLM: 'false'
```

**Solution 2:** Switch to Anthropic (cheaper)
```yaml
ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

**Solution 3:** Limit to important branches
```yaml
on:
  pull_request:
    branches: [main, develop]  # Only these branches
```

### Problem: Bot not analyzing certain files

**Cause:** Files are in excluded paths.

**Solution:** Edit `bot/pr_bot.py`, find `EXCLUDED_PATHS`, and customize:

```python
EXCLUDED_PATHS = [
    'scripts/',   # Remove if you want to analyze scripts
    '.github/',
    'tests/',
]
```

---

## Uninstallation

If you want to remove OQOQO Bot:

```bash
# Remove bot files
rm -rf bot/

# Remove workflow
rm .github/workflows/pr-bot.yml

# Commit changes
git add .
git commit -m "Remove OQOQO Bot"
git push
```

---

## Next Steps

After installation:

1. ⭐ **Star the repository** - Help others discover it!
2. 📖 **Read the README** - Learn about features
3. 🔧 **Customize** - See CONTRIBUTING.md for customization
4. 💬 **Share feedback** - Open issues with suggestions

---

## Getting Help

- 📖 **Documentation**: [README.md](README.md)
- 🐛 **Report bugs**: [GitHub Issues](https://github.com/YOUR_USERNAME/oqoqo-bot/issues)
- 💬 **Ask questions**: [GitHub Discussions](https://github.com/YOUR_USERNAME/oqoqo-bot/discussions)
- 🔧 **Contribute**: [CONTRIBUTING.md](CONTRIBUTING.md)

---

**Installation complete!** Enjoy using OQOQO Bot! 🎉
