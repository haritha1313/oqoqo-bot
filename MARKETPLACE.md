# Publishing to GitHub Actions Marketplace

Step-by-step guide to publish OQOQO Bot to the GitHub Actions Marketplace.

## Prerequisites

- ✅ Repository pushed to GitHub
- ✅ `action.yml` file created (already done!)
- ✅ Working bot code
- ✅ Good README documentation

## Step 1: Push to GitHub

If you haven't already:

```bash
cd C:\Users\harit\Documents\oqoqo-bot

# Create GitHub repository first at https://github.com/new

# Add remote and push
git remote add origin https://github.com/YOUR_USERNAME/oqoqo-bot.git
git branch -M main
git push -u origin main
```

## Step 2: Create a Release

GitHub Actions are versioned using git tags and releases.

### Option A: Using GitHub Web UI (Easier)

1. **Go to your repository** on GitHub

2. **Click "Releases"** (right sidebar)

3. **Click "Create a new release"**

4. **Choose a tag**:
   - Click "Choose a tag"
   - Type: `v1.0.0`
   - Click "Create new tag: v1.0.0 on publish"

5. **Release title**: `v1.0.0 - Initial Release`

6. **Release description**:
   ```markdown
   ## 🎉 OQOQO Bot v1.0.0

   Initial release of OQOQO Bot - Intelligent PR Analysis & Documentation Bot

   ### ✨ Features
   - 🔍 5-layer Hot-Path semantic analysis
   - 🤖 AI-powered documentation generation (OpenAI & Anthropic)
   - 📊 Automatic PR comments with smart recommendations
   - 💰 Built-in cost tracking and limits
   - 🆓 Free mode available (no AI)

   ### 🚀 Quick Start

   Add to your repository's `.github/workflows/oqoqo-bot.yml`:

   ```yaml
   name: OQOQO Bot
   on:
     pull_request:
       types: [opened, synchronize, reopened]
   permissions:
     pull-requests: write
   jobs:
     analyze:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
           with:
             fetch-depth: 0
         - uses: YOUR_USERNAME/oqoqo-bot@v1
           with:
             github-token: ${{ secrets.GITHUB_TOKEN }}
             enable-llm: 'true'
             openai-api-key: ${{ secrets.OPENAI_API_KEY }}
   ```

   See [README](README.md) for full documentation.

   ### 📋 Supported Languages
   - Python
   - JavaScript
   - TypeScript

   ### 🆓 Free Mode
   Works without API keys! Just set `enable-llm: 'false'`
   ```

7. **Check "Set as the latest release"**

8. **Click "Publish release"**

### Option B: Using Command Line

```bash
# Create and push tag
git tag -a v1.0.0 -m "Initial release"
git push origin v1.0.0

# Then create the release on GitHub web UI
```

## Step 3: Publish to Marketplace

After creating the release:

1. **Go to your repository** on GitHub

2. **You should see a banner** at the top:
   ```
   "Draft a release" or "Publish this Action to the GitHub Marketplace"
   ```

3. **Click "Publish this Action to the GitHub Marketplace"**
   - Or edit your v1.0.0 release
   - Check ☑️ "Publish this Action to the GitHub Marketplace"

4. **Fill in Marketplace details**:

   - **Primary Category**: Choose one:
     - "Code quality" (best fit)
     - "Continuous integration"

   - **Another Category**: (optional)
     - "Utilities"

   - **Icon & Color**: (already set in `action.yml`)
     - Icon: `git-pull-request`
     - Color: `blue`

5. **Accept the terms**:
   - ☑️ "I have read and agree to the GitHub Marketplace Developer Agreement"
   - ☑️ "I have read and agree to the GitHub Marketplace Terms of Service"

6. **Click "Publish release"**

🎉 **Done!** Your action is now on the GitHub Marketplace!

## Step 4: Verify Publication

1. **Search for your action**:
   - Go to https://github.com/marketplace?type=actions
   - Search for "oqoqo-bot"

2. **Check your action page**:
   - Should be at: `https://github.com/marketplace/actions/YOUR_ACTION_NAME`
   - Verify README displays correctly
   - Check that install instructions work

3. **Test installation**:
   - Create a test repository
   - Add the workflow file from your README
   - Create a PR to test it works

## Step 5: Create Versioning Strategy

For future updates, use semantic versioning:

### Major version tag (recommended for users)

```bash
# After releasing v1.0.0, create/update v1 tag
git tag -f v1
git push -f origin v1
```

This allows users to use `@v1` which auto-updates to latest v1.x.x:
```yaml
- uses: YOUR_USERNAME/oqoqo-bot@v1  # Always uses latest v1.x.x
```

### Specific versions

Users can pin to exact versions:
```yaml
- uses: YOUR_USERNAME/oqoqo-bot@v1.0.0  # Locked to exactly 1.0.0
```

### Future releases

When releasing updates:

```bash
# For bug fixes (1.0.0 → 1.0.1)
git tag v1.0.1
git tag -f v1  # Move v1 tag forward
git push origin v1.0.1
git push -f origin v1

# For new features (1.0.0 → 1.1.0)
git tag v1.1.0
git tag -f v1
git push origin v1.1.0
git push -f origin v1

# For breaking changes (1.0.0 → 2.0.0)
git tag v2.0.0
git tag v2  # Create new major version tag
git push origin v2.0.0
git push origin v2
```

## Marketplace Listing Tips

### Good README makes a difference

Your action's marketplace page shows your README. Make sure it has:

- ✅ **Clear description** - What it does in one sentence
- ✅ **Quick example** - Copy-paste workflow
- ✅ **Visual example** - Screenshot of bot comment
- ✅ **All inputs documented** - What each parameter does
- ✅ **Examples** - Common use cases
- ✅ **Troubleshooting** - Common issues

### Improve Discoverability

**Add topics to repository**:
1. Go to repository homepage
2. Click gear icon (⚙️) next to "About"
3. Add topics:
   - `github-actions`
   - `pr-bot`
   - `code-analysis`
   - `documentation`
   - `ai`
   - `semantic-analysis`
   - `automation`

**Good description**:
```
Intelligent PR analysis with Hot-Path semantic analysis and AI-powered documentation generation
```

### Badge for README

Add this badge to show it's on Marketplace:

```markdown
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-OQOQO%20Bot-blue?logo=github)](https://github.com/marketplace/actions/YOUR-ACTION-NAME)
```

## Updating Your Action

When you make changes:

1. **Make your changes and commit**:
   ```bash
   git add .
   git commit -m "feat: add Java support"
   git push
   ```

2. **Create new release**:
   ```bash
   # New patch version
   git tag v1.0.1
   git tag -f v1  # Update major version pointer
   git push origin v1.0.1
   git push -f origin v1
   ```

3. **Create GitHub Release**:
   - Go to Releases → "Draft a new release"
   - Tag: `v1.0.1`
   - Title: `v1.0.1 - Bug Fixes`
   - Description: Changelog
   - Check "Publish to Marketplace"

4. **Users automatically get updates** (if they use `@v1`)

## Testing Your Action

Before releasing, test it works:

### Local Testing (Act)

Use [act](https://github.com/nektos/act) to test locally:

```bash
# Install act
# On Windows: choco install act-cli
# On Mac: brew install act
# On Linux: curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash

# Test your action
cd oqoqo-bot
act pull_request
```

### Test Repository

Create a test repo:
1. Create `test-oqoqo-bot` repository
2. Add workflow file using your action
3. Create a test PR
4. Verify bot comments correctly

## Common Issues

### Issue: Action not found

**Problem**: Users get "action not found" error

**Solution**:
- Make sure repository is public
- Verify tag exists: `git tag -l`
- Check action.yml is in root directory
- Wait 5-10 minutes after publishing

### Issue: Action fails on marketplace

**Problem**: Action runs fine locally but fails on marketplace

**Solution**:
- Check all paths use `${{ github.action_path }}`
- Verify dependencies are installed in `action.yml`
- Test with `act` before publishing

### Issue: README doesn't update

**Problem**: Changed README but marketplace shows old version

**Solution**:
- Create a new release
- Marketplace updates from releases, not commits

## Unpublishing

To remove from marketplace:

1. Go to repository Settings
2. Scroll to "Danger Zone"
3. "Unpublish from GitHub Marketplace"

**Note**: Existing users can still use published versions.

## Analytics

Track your action usage:

1. **GitHub Insights**:
   - Repository → Insights → Traffic
   - See clones, visitors, referrers

2. **Download statistics**:
   - Can't see direct action usage counts (GitHub limitation)
   - Track via stars, forks, issues

3. **Monitor feedback**:
   - Watch GitHub Issues
   - Check Marketplace reviews
   - Monitor Stars and feedback

## Checklist Before Publishing

- [ ] `action.yml` is correct and in root
- [ ] README has clear usage example
- [ ] Example workflow file provided
- [ ] All features work and tested
- [ ] LICENSE file exists (MIT)
- [ ] Repository is public
- [ ] Good description and topics added
- [ ] Created v1.0.0 release
- [ ] Tested with a sample repository
- [ ] Ready to support users via Issues

---

## After Publishing

1. **Announce it**:
   - Twitter/X
   - Reddit (r/github, r/devops)
   - Dev.to blog post
   - Hacker News (Show HN)

2. **Submit to lists**:
   - [Awesome Actions](https://github.com/sdras/awesome-actions)
   - [Awesome Bots](https://github.com/DopplerHQ/awesome-bots)

3. **Maintain it**:
   - Respond to issues quickly
   - Merge good PRs
   - Release updates regularly
   - Keep documentation current

---

**Ready to publish?** Follow Step 1-3 above! 🚀
