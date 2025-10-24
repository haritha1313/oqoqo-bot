# Testing OQOQO Bot Before Publishing

Complete guide to test your GitHub Action before publishing to the Marketplace.

## Method 1: Test in a GitHub Repository (Recommended) ⭐

This is the most accurate test since it runs in the real GitHub Actions environment.

### Step 1: Push to GitHub

First, push your bot to GitHub:

```bash
cd C:\Users\harit\Documents\oqoqo-bot

# Create repository at https://github.com/new first (name it oqoqo-bot)

# Add remote and push
git remote add origin https://github.com/YOUR_USERNAME/oqoqo-bot.git
git branch -M main
git push -u origin main
```

### Step 2: Create a Test Repository

1. **Create a new test repository** on GitHub:
   - Name: `test-oqoqo-bot` (or anything)
   - Visibility: **Public** or **Private** (your choice)
   - Initialize with a README

2. **Clone it locally**:
   ```bash
   cd C:\Users\harit\Documents
   git clone https://github.com/YOUR_USERNAME/test-oqoqo-bot.git
   cd test-oqoqo-bot
   ```

### Step 3: Add Test Code

Create some Python code to test with:

```bash
# Create a simple Python file
mkdir src
cat > src/calculator.py << 'EOF'
def add(a, b):
    """Add two numbers."""
    return a + b

def subtract(a, b):
    """Subtract b from a."""
    return a - b

def multiply(a, b):
    # New function without docs!
    return a * b
EOF

git add src/calculator.py
git commit -m "Add calculator functions"
git push origin main
```

### Step 4: Create Workflow Using Your Action

Create `.github/workflows/oqoqo-bot.yml`:

```yaml
name: OQOQO Bot Test

on:
  pull_request:
    types: [opened, synchronize, reopened]
  issue_comment:
    types: [created]

permissions:
  contents: write
  pull-requests: write

jobs:
  analyze:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      # Test your action - use YOUR repository name!
      - uses: YOUR_USERNAME/oqoqo-bot@main  # Use @main for testing
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          enable-llm: 'false'  # Start with free mode
          docs-dir: 'docs'

  update-docs:
    if: >
      github.event_name == 'issue_comment' &&
      github.event.issue.pull_request &&
      contains(github.event.comment.body, '/update-docs')
    runs-on: ubuntu-latest
    steps:
      - name: Get PR details
        id: pr
        uses: actions/github-script@v7
        with:
          script: |
            const pr = await github.rest.pulls.get({
              owner: context.repo.owner,
              repo: context.repo.repo,
              pull_number: context.issue.number
            });
            core.setOutput('ref', pr.data.head.ref);
            core.setOutput('sha', pr.data.head.sha);
            core.setOutput('base_sha', pr.data.base.sha);

      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          ref: ${{ steps.pr.outputs.ref }}
          token: ${{ secrets.GITHUB_TOKEN }}

      - uses: YOUR_USERNAME/oqoqo-bot@main
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          mode: 'update-docs'
          enable-llm: 'false'
          docs-dir: 'docs'
```

**Important**: Use `@main` (not `@v1`) when testing!

### Step 5: Configure Permissions

1. Go to test repository **Settings** → **Actions** → **General**
2. Workflow permissions → **"Read and write permissions"**
3. Check ✅ **"Allow GitHub Actions to create and approve pull requests"**
4. Click **Save**

### Step 6: Create a Test PR

```bash
# Create test branch
git checkout -b test-pr

# Make changes to test
cat >> src/calculator.py << 'EOF'

def divide(a, b):
    # Another function without documentation
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
EOF

# Commit and push
git add src/calculator.py
git commit -m "Add divide function"
git push origin test-pr
```

### Step 7: Open Pull Request

1. Go to your test repository on GitHub
2. Click "Pull requests" → "New pull request"
3. Base: `main` ← Compare: `test-pr`
4. Click "Create pull request"

### Step 8: Watch It Run!

1. **Go to the "Actions" tab** in your test repository
2. You should see the workflow running
3. Wait ~30-60 seconds
4. **Check the PR** - bot should post a comment!

### Expected Result:

```
🔍 Hot-Path Analysis Results

📊 Changes Summary:
• 1 file modified
• 1 function added

⚠️ Documentation Issues:
• New function `divide()` needs documentation
```

### Step 9: Test `/update-docs` Command

1. **Add an API key** to test AI features:
   - Test repo **Settings** → **Secrets and variables** → **Actions**
   - Add `OPENAI_API_KEY` or `ANTHROPIC_API_KEY`

2. **Update workflow** to enable LLM:
   ```yaml
   enable-llm: 'true'
   openai-api-key: ${{ secrets.OPENAI_API_KEY }}
   ```

3. **Comment on the PR**: `/update-docs`

4. **Wait ~1-2 minutes**

5. **Check for**:
   - New commit on PR branch
   - Bot comment updated with success message

---

## Method 2: Test Locally with Act (Faster)

Test without pushing to GitHub using [Act](https://github.com/nektos/act).

### Install Act

**Windows (PowerShell as Administrator):**
```powershell
choco install act-cli
# Or download from: https://github.com/nektos/act/releases
```

**Mac:**
```bash
brew install act
```

**Linux:**
```bash
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash
```

### Setup Test Environment

1. **Create test workflow in your bot repo**:

Create `.github/workflows/test-local.yml`:

```yaml
name: Test Locally

on: [pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install requests openai anthropic

      - name: Test bot directly
        env:
          GITHUB_TOKEN: fake_token_for_testing
          REPO: test/repo
          PR_NUMBER: 1
          BASE_SHA: abc123
          HEAD_SHA: def456
          ENABLE_LLM: 'false'
          DOCS_DIR: docs
        run: |
          cd src/hotpath
          export PYTHONPATH="${PYTHONPATH}:$(pwd)"
          cd ..
          python pr_bot.py || echo "Expected to fail without real PR data"
```

2. **Run with Act**:

```bash
cd C:\Users\harit\Documents\oqoqo-bot
act pull_request -j test
```

**Note**: This tests the action runs, but won't post actual PR comments.

---

## Method 3: Test Components Individually

Test the Python scripts directly before testing as an action.

### Test PR Bot Script

```bash
cd C:\Users\harit\Documents\oqoqo-bot\src

# Set up environment
$env:GITHUB_TOKEN = "your_test_token"
$env:REPO = "YOUR_USERNAME/test-repo"
$env:PR_NUMBER = "1"
$env:BASE_SHA = "abc123"
$env:HEAD_SHA = "def456"
$env:ENABLE_LLM = "false"
$env:DOCS_DIR = "docs"

# Add to Python path
cd hotpath
$env:PYTHONPATH = "${env:PYTHONPATH};$(Get-Location)"
cd ..

# Run the bot (will fail without real PR, but tests imports)
python pr_bot.py
```

Expected errors are OK - this tests that:
- ✅ All imports work
- ✅ No syntax errors
- ✅ Dependencies are installed

---

## Test Checklist

Use this checklist to verify everything works:

### Free Mode (No API Key)
- [ ] Bot posts comment on new PR
- [ ] Comment shows semantic analysis results
- [ ] Comment shows documentation coverage
- [ ] No errors in workflow logs
- [ ] Works with Python files
- [ ] Works with JavaScript/TypeScript files

### AI Mode (With API Key)
- [ ] Bot posts comment with AI suggestions
- [ ] `/update-docs` command works
- [ ] Documentation is generated correctly
- [ ] Changes are committed to PR branch
- [ ] Bot comment is updated with results
- [ ] Cost tracking shows in comment

### Edge Cases
- [ ] Works with empty PR (no code changes)
- [ ] Works with large PR (many files)
- [ ] Works with docs-only changes
- [ ] Handles files in subdirectories
- [ ] Respects excluded paths
- [ ] Handles non-code files gracefully

### Action-Specific
- [ ] Action installs dependencies correctly
- [ ] All inputs work as expected
- [ ] Paths resolve correctly (`${{ github.action_path }}`)
- [ ] Works from external repositories
- [ ] Error messages are helpful

---

## Common Issues During Testing

### Issue: "Action not found"

**Cause**: Repository is private or action path is wrong

**Solution**:
- Make bot repository **public** (required for actions used by others)
- Or test in the same repository (can be private)
- Verify repository name in workflow: `YOUR_USERNAME/oqoqo-bot@main`

### Issue: "Permission denied"

**Cause**: Workflow permissions not set

**Solution**:
- Settings → Actions → General
- Workflow permissions → "Read and write permissions"
- Enable "Allow GitHub Actions to create and approve pull requests"

### Issue: Bot doesn't post comment

**Cause**: Several possibilities

**Solutions**:
1. Check workflow logs for errors
2. Verify `GITHUB_TOKEN` has correct permissions
3. Check bot script for errors
4. Verify PR has code changes (not just docs)

### Issue: Module import errors

**Cause**: Python path not set correctly

**Solution**:
Check `action.yml` has:
```yaml
cd ${{ github.action_path }}/src/hotpath
export PYTHONPATH="${PYTHONPATH}:$(pwd)"
```

### Issue: "/update-docs doesn't work"

**Cause**: API key not configured or job not triggered

**Solution**:
1. Add API key to repository secrets
2. Verify workflow has `issue_comment` trigger
3. Check comment exactly says `/update-docs`
4. Look at Actions tab for triggered runs

---

## Debugging Tips

### View Detailed Logs

1. Go to Actions tab
2. Click on the workflow run
3. Expand each step to see output
4. Look for red X marks

### Add Debug Logging

Add to workflow for more details:

```yaml
env:
  ACTIONS_STEP_DEBUG: true
  ACTIONS_RUNNER_DEBUG: true
```

### Test Individual Steps

Break down the action into individual steps:

```yaml
- name: Checkout
  uses: actions/checkout@v4
  with:
    fetch-depth: 0

- name: Setup Python
  uses: actions/setup-python@v5
  with:
    python-version: '3.11'

- name: Install dependencies
  run: pip install requests openai anthropic

- name: Test imports
  run: python -c "import requests; import openai; import anthropic; print('OK')"

- name: Run bot
  run: |
    cd ${{ github.action_path }}/src/hotpath
    export PYTHONPATH="${PYTHONPATH}:$(pwd)"
    cd ..
    python pr_bot.py
```

---

## When to Publish

✅ **Ready to publish when**:
- [ ] Bot posts comments successfully in test repo
- [ ] All features work (analyze + /update-docs)
- [ ] No errors in workflow logs
- [ ] Tested with both free and AI mode
- [ ] README has clear usage instructions
- [ ] All test checklist items pass

❌ **Not ready if**:
- Workflow fails with errors
- Bot doesn't post comments
- Comments are malformed
- API integration doesn't work
- Documentation is unclear

---

## Quick Test Script

Copy this to quickly test everything:

```bash
# 1. Push bot to GitHub
cd C:\Users\harit\Documents\oqoqo-bot
git remote add origin https://github.com/YOUR_USERNAME/oqoqo-bot.git
git push -u origin main

# 2. Create test repo
# (Do this on GitHub web: https://github.com/new)

# 3. Clone and setup test
cd C:\Users\harit\Documents
git clone https://github.com/YOUR_USERNAME/test-oqoqo-bot.git
cd test-oqoqo-bot

# 4. Add test code
mkdir src
echo "def test(): pass" > src/test.py
git add .
git commit -m "Initial commit"
git push

# 5. Create workflow
mkdir -p .github/workflows
# Copy example workflow here

# 6. Create test PR
git checkout -b test
echo "def new_function(): return True" >> src/test.py
git add .
git commit -m "Add function"
git push origin test

# 7. Open PR on GitHub and watch!
```

---

**Ready to test?** Start with Method 1 (GitHub repository) - it's the most reliable! 🧪
