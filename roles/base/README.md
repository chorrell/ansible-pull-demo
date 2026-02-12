# Base Role

Base system configuration for macOS.

## Requirements

- macOS
- Ansible >= 2.9

## Role Variables

Available variables with their default values (see `defaults/main.yml`):

```yaml
default_shell: /bin/zsh
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: local
  roles:
    - base
```

## Tags

- `shell` - Shell configuration tasks

## License

MPL-2.0
