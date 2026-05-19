# ansible-pull for macOS

[![Test Ansible Playbook](https://github.com/chorrell/ansible-pull-demo/actions/workflows/test.yml/badge.svg)](https://github.com/chorrell/ansible-pull-demo/actions/workflows/test.yml)

Using `ansible-pull` to configure macOS. Unlike the typical Ansible setup where you push changes from a control node, ansible-pull inverts this - each machine pulls its configuration from a git repo and applies it locally.

## Prerequisites

You'll need macOS with git installed. Install dependencies using uv:

```bash
# Install uv (one-time)
brew install uv

# Install Ansible and dependencies
uv sync

# Install Galaxy collections
uv run ansible-galaxy collection install -r collections/requirements.yml
```

## Usage

Test locally without pulling from git:

```bash
uv run ansible-playbook local.yml
```

For automated runs with `ansible-pull`, install Ansible globally using uv tools:

```bash
uv tool install --with-executables-from ansible-core ansible
ansible-pull -U https://github.com/YOUR_USERNAME/ansible-pull-demo.git local.yml
```

For automated runs that only apply if the repo has changed:

```bash
ansible-pull -U https://github.com/YOUR_USERNAME/ansible-pull-demo.git local.yml -o
```

## What's included

The playbook includes two roles:

- **base** - Ensures zsh is the default shell
- **homebrew** - Manages Homebrew packages (currently ansible-lint, gh, and git)

Edit `group_vars/local.yml` to customize packages for your setup.

## Project Structure

- `local.yml` - main playbook
- `hosts` - inventory (localhost only)
- `ansible.cfg` - Ansible configuration
- `group_vars/local.yml` - variables for local host group
- `collections/requirements.yml` - Ansible Galaxy collection dependencies
- `roles/base/` - base system configuration (shell setup)
- `roles/homebrew/` - Homebrew package management

## Using tags

Run specific parts:

```bash
ansible-pull -U <repo-url> local.yml --tags homebrew
ansible-pull -U <repo-url> local.yml --tags shell
```
