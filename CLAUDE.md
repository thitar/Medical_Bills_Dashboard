# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Git workflow

- **`main` only takes documentation changes directly.** Any code change
  (anything under `html/`, `nginx.conf`, `docker-compose.yml`, etc.)
  must go through a feature branch and a pull request — never commit
  or push code changes straight to `main`.
- Branch naming: short, descriptive, kebab-case (e.g.
  `feat/audit-no-completed-date-section`, `fix/nginx-502`).
- Open the PR with `gh pr create` against `main` once the branch is
  pushed.
