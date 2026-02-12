# ansible-pull for macOS

[![Test Ansible Playbook](https://github.com/chorrell/ansible-pull-demo/actions/workflows/test.yml/badge.svg)](https://github.com/chorrell/ansible-pull-demo/actions/workflows/test.yml)

Using `ansible-pull` to configure macOS. Unlike the typical Ansible setup where you push changes from a control node, ansible-pull inverts this - each machine pulls its configuration from a git repo and applies it locally.

## Prerequisites

You'll need macOS with Ansible and git installed. Install Ansible and required collections:

```bash
pip3 install -r requirements.txt
ansible-galaxy collection install -r collections/requirements.yml
```

## Usage

Run ansible-pull to apply the configuration:

```bash
ansible-pull -U https://github.com/YOUR_USERNAME/ansible-pull-demo.git local.yml
```

For automated runs (only applies if the repo has changed):

```bash
ansible-pull -U https://github.com/YOUR_USERNAME/ansible-pull-demo.git local.yml -o
```

Test locally without pulling from git:

```bash
ansible-playbook local.yml
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
