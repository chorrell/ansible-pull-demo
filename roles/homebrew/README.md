# Homebrew Role

Manages Homebrew packages and cask applications on macOS.

## Requirements

- macOS
- Ansible >= 2.9
- community.general collection

## Role Variables

Available variables with their default values (see `defaults/main.yml`):

```yaml
# Path to Homebrew binary
homebrew_binary_path: /opt/homebrew/bin/brew

# Whether to fail if Homebrew is not installed
homebrew_required: false

# List of Homebrew packages to install
homebrew_packages: []

# List of Homebrew cask applications to install
homebrew_cask_packages: []
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: local
  vars:
    homebrew_packages:
      - git
      - gh
      - ansible-lint
    homebrew_cask_packages:
      - visual-studio-code
  roles:
    - homebrew
```

## Tags

- `homebrew` - All Homebrew tasks

## License

MPL-2.0
