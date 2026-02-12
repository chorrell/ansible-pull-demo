# ansible-pull-demo

Ansible playbook for macOS configuration using ansible-pull pattern.
Manages system settings and Homebrew packages.

## Core Commands

• Install dependencies: `pip3 install -r requirements.txt &&
  ansible-galaxy collection install -r collections/requirements.yml`
• Syntax check: `ansible-playbook local.yml --syntax-check`
• Test run (dry-run): `ansible-playbook local.yml --check`
• Run with ansible-pull: `ansible-pull -U <repo-url> local.yml`

**DO NOT** run `ansible-playbook local.yml` directly - this modifies your
system. Use `--syntax-check` and `--check` modes only for development.

CI/CD runs syntax check and check mode on every PR.

## Project Layout

```text
.
├── local.yml                     # Main playbook
├── hosts                         # Inventory (localhost only)
├── ansible.cfg                   # Ansible configuration
├── group_vars/local.yml          # Variables for local host group
├── collections/requirements.yml  # Galaxy collection dependencies
└── roles/
    ├── base/                     # Shell configuration
    └── homebrew/                 # Homebrew package management
```

• Playbook code lives in `roles/*/tasks/main.yml`
• Variables defined in `roles/*/defaults/main.yml` and
  `group_vars/local.yml`
• Role metadata in `roles/*/meta/main.yml`

## Development Patterns & Conventions

### Ansible Style

• Use `ansible_facts` dictionary format (e.g., `ansible_facts.user_id`)
  instead of injected variables to avoid deprecation warnings
• Tag all tasks appropriately (`shell`, `homebrew`, etc.)
• Use `changed_when: false` for read-only command tasks
• Prefer idempotent tasks with `when` conditions over always-running
  privileged operations
• macOS-specific: Use `dscl` commands for Directory Services queries,
  not Linux `getent`
• All Ansible code must pass ansible-lint checks (no trailing spaces,
  proper YAML formatting, use FQCN for modules)

### Role Structure

• All roles must have: `tasks/`, `defaults/`, `meta/`, and `README.md`
• Document all variables with defaults and examples in role README
• Use `community.general` collection for Homebrew modules
• Keep roles focused on single responsibility

### Variables

• Package lists go in `group_vars/local.yml`
• Role defaults in `roles/*/defaults/main.yml`
• Never hardcode values that differ per environment

## Git Workflow

1. **All changes must be made via pull request** - direct pushes to `main`
   are blocked by branch protection rules
2. Branch from `main` with descriptive name: `feature/<description>` or
   `fix/<description>`
3. Run syntax check before committing:
   `ansible-playbook local.yml --syntax-check`
4. Test in check mode: `ansible-playbook local.yml --check`
5. All Markdown files must pass markdownlint (use Docker:
   `docker run --rm -v "$PWD:/workdir"
   ghcr.io/igorshubovych/markdownlint-cli:latest <file>`)
6. PR descriptions should be **brief** - one paragraph maximum
7. Force pushes allowed only on feature branches using
   `git push --force-with-lease`
8. Squash commits before merging (single commit per PR)

## Evidence Required for PRs

• Syntax check passes (`ansible-playbook local.yml --syntax-check`)
• Check mode succeeds (`ansible-playbook local.yml --check`)
• ansible-lint passes (all Ansible code must pass linting)
• CI tests pass (GitHub Actions)
• All Markdown files pass markdownlint
• Changes confined to relevant files
• Brief description (1-3 sentences) of what changed and why

## Architecture Overview

Two-role system:

- **base**: Manages macOS shell configuration using Directory Services
- **homebrew**: Handles package installation with idempotency checks

Facts gathered once at playbook level for efficiency. Roles use
conditional execution to minimize unnecessary privilege escalation.

## External Dependencies

• `community.general` collection (version ≥9.0.0) for Homebrew modules
• Homebrew must be pre-installed at `/opt/homebrew/bin/brew`
  (Apple Silicon) or `/usr/local/bin/brew` (Intel)
• Python 3.14+ with Ansible ≥13.0

## Gotchas

• macOS doesn't have `getent` - use
  `dscl . -read /Users/<user> <attribute>` instead
• Homebrew role checks for installation but doesn't install it
  (set `homebrew_required: true` to fail if missing)
• Shell changes require `become: true` - minimized via idempotency checks
• Facts must use dictionary format (`ansible_facts.user_id`) not
  top-level vars to avoid deprecation warnings in Ansible 2.24+
