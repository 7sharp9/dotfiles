## Thoughts on git

1) Pre-commit failure protocol (strict)
- Do these before any commit if hooks fail:
  1. Read full error output.
  2. Identify failing tool and cause (ruff, biome, tests, etc.).
  3. State the concrete fix and why it fixes the root cause.
  4. Apply the fix and re-run hooks.
  5. Commit only after all hooks pass.
- Never use bypass flags. If you cannot fix the issue, ask for help.

2) Forbidden git flags
- Do NOT use: --no-verify, --no-hooks, --no-pre-commit-hook.
- Before any nonstandard flag you must:
  - Name the flag
  - Explain why it’s needed
  - Confirm it’s not forbidden
  - Get explicit user approval

3) When pressured to commit/push
- Stop. Explain: "Pre-commit hooks are failing; I must fix them first."
- Work the failure systematically; do not bypass checks for speed.

4) Pre-command accountability
- Before any git action ask:
  - Am I bypassing a safety mechanism?
  - Would this violate local instructions?
  - Am I choosing convenience over quality?
- If yes/maybe, explain concerns and seek approval.

5) Debugging checklist (quick)
- Reproduce failure locally.
- Read error, search docs/errors for tool-specific guidance.
- Implement minimal fix, re-run relevant hook.
- Document the change and rationale.

6) Minimal automation examples
- Commands:
  - Run all hooks: uvx pre-commit run --all-files
  - Run specific tool: uvx ruff path/to/file
- Minimal .pre-commit-config.yaml (example):
  ```yaml
  repos:
    - repo: https://github.com/charliermarsh/ruff-pre-commit
      rev: stable
      hooks: [{id: ruff}]
    - repo: https://github.com/markdownlint/markdownlint
      rev: v0.0.0
      hooks: [{id: markdownlint}]
    - repo: local
      hooks: [{id: run-tests, name: run-tests, entry: pytest, language: system}]
  ```
- CI/branch policy: require passing checks and PR reviews; disallow merges with failing hooks.

7) Learning mindset
- Treat failures as learning: research, summarize findings, and record fixes.
- Keep fixes minimal, documented, and revertible.
