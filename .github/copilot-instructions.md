# file: .github/copilot-instructions.md

# version: 1.0.0

# guid: 5d6e7f8a-9b0c-1d2e-3f4a-5b6c7d8e9f0a

# AI Agent Instructions (Standard)

- Use VS Code tasks for non-git operations; prefer MCP GitHub tools for git operations.
- Commits must use conventional commits (type(scope): description).
- Include versioned headers in docs/configs and bump versions on changes.
- This is a composite GitHub Action that embeds Python logic directly in action.yml.
- All changes to labeling logic should be made in the embedded Python script within action.yml.
- Follow GitHub Actions composite action best practices.
- Provide concise plans and progress updates.

## Repository Context

This repository provides a composite GitHub Action that automatically applies intelligent labels to pull requests based on content analysis.

**Key Files:**

- [action.yml](action.yml) - Main composite action with embedded Python script
- [README.md](README.md) - Comprehensive documentation and examples
- [CHANGELOG.md](CHANGELOG.md) - Version history

**Labeling Rules:**

- **Keywords**: bug, enhancement, documentation, tests (based on PR title/body)
- **File Extensions**: go, python, frontend, docker, documentation
- **File Patterns**: test files trigger "tests" label

**Design Pattern:**

- Composite action using `shell: python` to embed the entire Python script
- Single file action.yml contains all logic (no separate src/ directory needed)
- All environment variables passed as action inputs
- Outputs exposed via GitHub Actions output commands
