# Contributing to OQOQO Bot

Thank you for your interest in contributing to OQOQO Bot! This document provides guidelines and instructions for contributing.

## 🚀 Getting Started

### Development Setup

1. **Fork and clone the repository**

```bash
git clone https://github.com/YOUR_USERNAME/oqoqo-bot.git
cd oqoqo-bot
```

2. **Install dependencies**

```bash
pip install -r requirements.txt
```

3. **Set up environment variables**

```bash
cp .env.example .env
```

Edit `.env` and add your API keys for testing:
```bash
GITHUB_TOKEN=your_test_token
OPENAI_API_KEY=your_openai_key  # Optional
ANTHROPIC_API_KEY=your_anthropic_key  # Optional
```

4. **Create a test branch**

```bash
git checkout -b feature/your-feature-name
```

## 📝 How to Contribute

### Reporting Bugs

Found a bug? Please open an issue with:

- **Clear title** - Describe the bug concisely
- **Steps to reproduce** - How can we recreate it?
- **Expected behavior** - What should happen?
- **Actual behavior** - What actually happens?
- **Environment** - OS, Python version, etc.
- **Logs** - Include relevant error messages

**Example:**
```
Title: Bot fails to analyze TypeScript interfaces

Steps to reproduce:
1. Create a PR with new TypeScript interfaces
2. Bot runs analysis
3. Bot crashes with error "Cannot parse interface"

Expected: Bot should analyze TypeScript interfaces
Actual: Bot crashes

Environment: Ubuntu 22.04, Python 3.11
Error: [paste error log here]
```

### Suggesting Features

Have an idea? Open an issue with:

- **Use case** - Why is this feature needed?
- **Proposed solution** - How should it work?
- **Alternatives** - Other approaches you considered
- **Examples** - Similar features in other tools

### Submitting Pull Requests

1. **Create an issue first** (for non-trivial changes)
2. **Fork the repository**
3. **Create a feature branch** (`git checkout -b feature/amazing-feature`)
4. **Make your changes**
5. **Test thoroughly**
6. **Commit with clear messages** (see commit guidelines below)
7. **Push to your fork** (`git push origin feature/amazing-feature`)
8. **Open a Pull Request**

## 🎯 Development Guidelines

### Code Style

- **Python**: Follow PEP 8
- **Line length**: Max 100 characters
- **Docstrings**: Use Google style
- **Type hints**: Add where helpful

**Example:**
```python
def analyze_changes(pr_number: int, base_sha: str, head_sha: str) -> AnalysisResult:
    """Analyzes code changes in a pull request.

    Args:
        pr_number: The pull request number
        base_sha: Base commit SHA
        head_sha: Head commit SHA

    Returns:
        AnalysisResult object containing the analysis

    Raises:
        ValueError: If PR number is invalid
    """
    # Implementation here
```

### Commit Messages

Follow conventional commits:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `refactor:` - Code refactoring
- `test:` - Adding tests
- `chore:` - Maintenance tasks

**Examples:**
```bash
feat(analysis): add support for Java language
fix(llm): handle API rate limiting errors
docs(readme): add troubleshooting section
refactor(pipeline): optimize Hot-Path layer 3
```

### Testing

Before submitting:

1. **Test locally** - Run the bot on a test repository
2. **Check edge cases** - Empty PRs, large PRs, etc.
3. **Verify API costs** - Ensure LLM calls are optimized
4. **Test both modes** - Free mode and paid mode

**Test checklist:**
- [ ] Bot posts comment on new PR
- [ ] Analysis is accurate
- [ ] `/update-docs` command works
- [ ] Cost tracking is correct
- [ ] No errors in workflow logs
- [ ] Works with different file types

## 🏗️ Project Structure

Understanding the codebase:

```
src/
├── pr_bot.py                 # Entry point for PR analysis
│   ├── analyze_pr()          # Main analysis function
│   ├── post_pr_comment()     # Posts to GitHub
│   └── format_analysis()     # Formats output
│
├── update_docs.py            # Entry point for /update-docs
│   ├── generate_docs()       # Generates documentation
│   ├── commit_changes()      # Commits to PR branch
│   └── update_comment()      # Updates bot comment
│
├── hotpath_integration.py    # Wrapper for Hot-Path
│   └── analyze_changes()     # Calls Hot-Path pipeline
│
└── hotpath/                  # Hot-Path analysis engine
    ├── pipeline.py           # Main orchestrator
    ├── semantic.py           # Semantic analysis (Layer 2)
    ├── llm_doc_generator.py  # LLM integration
    ├── doc_analyzer.py       # Doc coverage (Layer 4)
    └── local_git_adapter.py  # Git operations
```

### Key Concepts

**Hot-Path Layers:**

1. **Layer 1: Merkle Tree** - Fast file change detection
   - Code: `pipeline.py` → `compute_merkle_tree()`

2. **Layer 2: Semantic Analysis** - Change classification
   - Code: `semantic.py` → `classify_change()`
   - Types: identical, refactor, minor, major, rewrite

3. **Layer 3: Communities** - Code relationships
   - Code: `pipeline.py` → `detect_communities()`

4. **Layer 4: Cross-References** - Code-to-docs mapping
   - Code: `doc_analyzer.py` → `find_cross_references()`

5. **Layer 5: Similarity** - Pattern detection
   - Code: `pipeline.py` → `compute_similarity()`

## 🔧 Common Modifications

### Adding a New Language

To add support for a new language (e.g., Java):

1. **Update semantic analyzer** (`src/hotpath/semantic.py`):

```python
LANGUAGE_PARSERS = {
    'python': parse_python_ast,
    'javascript': parse_js_ast,
    'typescript': parse_ts_ast,
    'java': parse_java_ast,  # Add this
}
```

2. **Implement parser**:

```python
def parse_java_ast(code: str) -> AST:
    """Parse Java code into AST."""
    # Use javalang or similar library
    pass
```

3. **Add file extensions** (`src/pr_bot.py`):

```python
SUPPORTED_EXTENSIONS = [
    '.py', '.js', '.ts', '.tsx',
    '.java',  # Add this
]
```

4. **Update README** - Add to supported languages section

### Customizing LLM Prompts

LLM prompts are in `src/hotpath/llm_doc_generator.py`:

```python
def generate_documentation_prompt(entity, code_snippet):
    prompt = f"""Generate documentation for this function:

Function: {entity.name}
Code:
{code_snippet}

Requirements:
- Clear description
- Parameter documentation
- Return value description
- Usage example
"""
    return prompt
```

Modify this to change documentation style.

### Adding Cost Controls

To modify cost limits (`src/pr_bot.py`):

```python
MAX_LLM_SUGGESTIONS = 3  # Change this
MAX_COST_PER_PR = 0.50  # Add cost cap (in USD)
```

### Custom Exclusion Patterns

To exclude specific files/directories (`src/hotpath_integration.py`):

```python
EXCLUDED_PATHS = [
    'scripts/',
    '.github/',
    'tests/',
    'vendor/',  # Add custom exclusions
    'generated/',
]
```

## 🧪 Testing Locally

### Test the PR Analysis Bot

1. Create a test repository with a PR
2. Set up environment variables:

```bash
export GITHUB_TOKEN="your_token"
export REPO="username/repo-name"
export PR_NUMBER="1"
export BASE_SHA="abc123"
export HEAD_SHA="def456"
export ENABLE_LLM="true"
export OPENAI_API_KEY="your_key"
```

3. Run the bot:

```bash
cd src/hotpath
export PYTHONPATH="${PYTHONPATH}:$(pwd)"
cd ..
python pr_bot.py
```

4. Check GitHub for the bot comment

### Test Documentation Generation

```bash
export DOCS_DIR="docs"
export DOCS_EXTRAS="README.md"
python update_docs.py
```

## 📦 Adding Dependencies

If you need to add a new Python package:

1. Install it: `pip install package-name`
2. Add to `requirements.txt`
3. Specify minimum version: `package-name>=1.0.0`
4. Document why it's needed in your PR

## 🔍 Debugging

### Enable Debug Logging

Add to your `.env`:
```bash
DEBUG=true
LOG_LEVEL=DEBUG
```

### Check GitHub Actions Logs

1. Go to Actions tab in your repository
2. Click on the failed workflow
3. Expand the failed step
4. Look for error messages

### Common Issues

**Import errors:**
- Make sure `PYTHONPATH` includes `src/hotpath/`
- Check that `__init__.py` exists in all directories

**API errors:**
- Verify API keys are correct
- Check API rate limits
- Ensure sufficient API credits

**Git errors:**
- Verify `fetch-depth: 0` in workflow (needs full history)
- Check git config is set up correctly

## 📖 Documentation

When adding features:

1. **Update README.md** - User-facing documentation
2. **Update CONTRIBUTING.md** - Development documentation
3. **Add code comments** - Explain complex logic
4. **Update .env.example** - If adding new env vars

## 🎨 Pull Request Checklist

Before submitting your PR:

- [ ] Code follows style guidelines
- [ ] All tests pass
- [ ] New code has comments
- [ ] README updated (if needed)
- [ ] Commit messages follow conventions
- [ ] No sensitive data (API keys, tokens) committed
- [ ] PR description explains changes clearly
- [ ] Breaking changes documented
- [ ] Backwards compatible (if possible)

## 🤝 Code Review Process

After submitting a PR:

1. **Automated checks** will run
2. **Maintainer review** - May take 1-7 days
3. **Feedback** - Address review comments
4. **Approval** - Once approved, PR will be merged
5. **Release** - Changes included in next release

## 📄 License

By contributing, you agree that your contributions will be licensed under the MIT License.

## 💬 Questions?

- Open an issue with the `question` label
- Tag maintainers for urgent issues
- Join discussions in GitHub Discussions

## 🙏 Thank You!

Every contribution helps make OQOQO Bot better. Thank you for your time and effort!

---

**Happy coding!** 🚀
