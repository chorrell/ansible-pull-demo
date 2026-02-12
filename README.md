# ansible-pull for macOS

[![Test Ansible Playbook](https://github.com/chorrell/ansible-pull-demo/actions/workflows/test.yml/badge.svg)](https://github.com/chorrell/ansible-pull-demo/actions/workflows/test.yml)

Using `ansible-pull` to configure macOS. Unlike the typical Ansible setup where you push changes from a control node, ansible-pull inverts this - each machine pulls its configuration from a git repo and applies it locally.

## Prerequisites

You'll need macOS with Ansible and git installed. Install Ansible with:

```bash
pip3 install -r requirements.txt
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

The playbook handles Homebrew packages (currently ansible-lint, gh, and git). The base role ensures zsh is the default shell.

Edit `local.yml` to customize packages for your setup.

## Files

- `local.yml` - main playbook
- `hosts` - inventory (localhost only)
- `ansible.cfg` - configuration
- `roles/base/` - base system configuration

## Using tags

Run specific parts:

```bash
ansible-pull -U <repo-url> local.yml --tags homebrew
ansible-pull -U <repo-url> local.yml --tags defaults
```
