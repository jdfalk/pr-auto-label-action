<!-- file: README.md -->
<!-- version: 1.0.0 -->
<!-- guid: 9a2b3c4d-5e6f-7a8b-9c0d-1e2f3a4b5c6d -->
<!-- last-edited: 2026-01-19 -->

# PR Auto Label Action

Automatically applies intelligent labels to pull requests based on:

- PR title and body content
- Files changed
- Commit messages

This composite GitHub Action replaces the functionality of
`ghcommon/.github/workflows/scripts/intelligent_labeling.py`.

## Features

✨ **Intelligent Analysis**

- Keyword-based pattern matching for issue types (bug, enhancement,
  documentation, tests)
- File extension-based labeling (go, python, frontend, docker, etc.)
- Automatic deduplication of suggested labels

🔍 **Detailed Analysis**

- Outputs analysis summary with matched rules and file patterns
- Dry-run mode for testing without applying labels
- Skip existing labels option

🔒 **Safety & Control**

- Optional skip if PR already has labels
- Dry-run mode for safe testing
- Detailed output for debugging

## Usage

### Basic Usage

```yaml
name: Auto Label PR
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - name: Auto Label PR
        uses: jdfalk/pr-auto-label-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          pr-number: ${{ github.event.pull_request.number }}
          repository: ${{ github.repository }}
```

### With Options

```yaml
- name: Auto Label PR
  uses: jdfalk/pr-auto-label-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    pr-number: ${{ github.event.pull_request.number }}
    repository: ${{ github.repository }}
    skip-existing: 'true' # Don't label if already has labels
    dry-run: 'false' # Test without applying
```

### Using Outputs

```yaml
- name: Auto Label PR
  id: label
  uses: jdfalk/pr-auto-label-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    pr-number: ${{ github.event.pull_request.number }}
    repository: ${{ github.repository }}

- name: Report Results
  run: |
    echo "Applied labels: ${{ steps.label.outputs.labels-applied }}"
    echo "Analysis: ${{ steps.label.outputs.analysis-summary }}"
```

## Inputs

| Input           | Description                                  | Required | Default   |
| --------------- | -------------------------------------------- | -------- | --------- |
| `github-token`  | GitHub token for API access                  | ✅ Yes   | -         |
| `pr-number`     | Pull request number to label                 | ✅ Yes   | -         |
| `repository`    | Repository in `owner/repo` format            | ✅ Yes   | -         |
| `label-config`  | Optional JSON configuration for custom rules | ❌ No    | `''`      |
| `skip-existing` | Skip labeling if PR already has labels       | ❌ No    | `'false'` |
| `dry-run`       | Analyze without applying labels              | ❌ No    | `'false'` |

## Outputs

| Output             | Description                                                       |
| ------------------ | ----------------------------------------------------------------- |
| `labels-applied`   | Comma-separated list of labels applied to the PR                  |
| `labels-suggested` | Comma-separated list of all suggested labels                      |
| `analysis-summary` | JSON summary of analysis (keywords, file patterns, rules matched) |

## Label Rules

### Keyword-Based Rules

| Keywords                                  | Label           | Category      |
| ----------------------------------------- | --------------- | ------------- |
| `fix`, `bug`, `error`, `issue`, `problem` | `bug`           | Issue Type    |
| `feature`, `add`, `new`, `implement`      | `enhancement`   | Enhancement   |
| `doc`, `readme`, `documentation`          | `documentation` | Documentation |
| `test`, `testing`, `spec`                 | `tests`         | Testing       |

### File-Based Rules

| Pattern                          | Label           |
| -------------------------------- | --------------- |
| `*.md`, `*.rst`                  | `documentation` |
| `*.go`                           | `go`            |
| `*.js`, `*.ts`, `*.jsx`, `*.tsx` | `frontend`      |
| `*.py`                           | `python`        |
| `Dockerfile`, `*.docker`         | `docker`        |
| `*test*`                         | `tests`         |

## Examples

### Example 1: Bug Fix PR

**PR Title:** `fix: resolve connection timeout issue in database handler`

**Changed Files:**

- `src/database/handler.go`
- `test/database_handler_test.go`

**Applied Labels:** `bug`, `go`, `tests`

### Example 2: Feature PR

**PR Title:** `feat: add React dashboard component`

**Changed Files:**

- `frontend/src/Dashboard.tsx`
- `frontend/src/Dashboard.test.tsx`
- `README.md`

**Applied Labels:** `enhancement`, `frontend`, `tests`, `documentation`

### Example 3: Documentation Update

**PR Title:** `docs: update API documentation`

**Changed Files:**

- `docs/api.md`
- `docs/authentication.md`

**Applied Labels:** `documentation`

## Dry Run Example

Test labeling without applying:

```yaml
- name: Test Auto Label
  uses: jdfalk/pr-auto-label-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    pr-number: ${{ github.event.pull_request.number }}
    repository: ${{ github.repository }}
    dry-run: 'true'
```

Output will show suggested labels but won't apply them.

## Comparison with Original Script

This action provides the same labeling logic as
`ghcommon/.github/workflows/scripts/intelligent_labeling.py` with these
improvements:

| Feature                  | Original | Action |
| ------------------------ | -------- | ------ |
| Core labeling logic      | ✅       | ✅     |
| Keyword analysis         | ✅       | ✅     |
| File-based labeling      | ✅       | ✅     |
| Dry-run mode             | ❌       | ✅     |
| Skip existing labels     | ❌       | ✅     |
| Detailed analysis output | ❌       | ✅     |
| Composite action format  | ❌       | ✅     |
| Reusable in workflows    | ❌       | ✅     |

## Error Handling

The action handles the following scenarios:

- **Missing environment variables** - Exits with status 1 and error message
- **API failures** - Logs error and exits with appropriate status
- **No suggested labels** - Skips labeling and reports success
- **Existing labels with `skip-existing`** - Skips labeling silently

## Troubleshooting

### Action fails with "Missing required environment variables"

Ensure you're providing:

- `github-token` - Use `${{ secrets.GITHUB_TOKEN }}`
- `pr-number` - Use `${{ github.event.pull_request.number }}`
- `repository` - Use `${{ github.repository }}`

### No labels are being applied

1. Check `dry-run` is not set to `'true'`
2. Review the `analysis-summary` output to see what rules matched
3. Check if `skip-existing` is enabled and PR already has labels
4. Ensure the labels in the rules exist in your repository

### API rate limiting

If you hit API rate limits, consider:

- Running the action only on specific triggers
- Using a token with higher rate limits if needed
- Batching label operations

## Contributing

Improvements to the labeling logic are welcome. Consider extending the action
with:

- Custom label rule configuration via JSON
- Machine learning-based content analysis
- Integration with issue templates

## License

MIT License - See LICENSE file for details

## Support

For issues, questions, or suggestions, please open an issue in the repository.
