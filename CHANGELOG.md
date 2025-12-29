<!-- file: CHANGELOG.md -->
<!-- version: 1.0.0 -->
<!-- guid: 7c8d9e0f-1a2b-3c4d-5e6f-7a8b9c0d1e2f -->

# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-12-29

### Added

- Initial release of PR Auto Label action
- Keyword-based pattern matching for bug, enhancement, documentation, and tests labels
- File extension-based labeling for language detection (Go, Python, Frontend, Docker)
- Dry-run mode for safe testing without applying labels
- Skip existing labels option to avoid re-labeling PRs
- Detailed analysis output with matched rules and file patterns
- Composite action format (embedded Python, no external dependencies)
- Support for GitHub API authentication via token
- Comprehensive README with examples and troubleshooting

### Migrated From

- `ghcommon/.github/workflows/scripts/intelligent_labeling.py`
- Maintains full backward compatibility with original labeling logic
- Adds new features: dry-run, skip-existing, detailed analysis output
