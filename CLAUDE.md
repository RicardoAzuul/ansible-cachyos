# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

Single-playbook Ansible project that configures a CachyOS laptop. It runs locally against `localhost` and installs packages via pacman, paru (AUR), and app-specific methods (AppImage, systemd).

## Commands

Install Ansible collections (required before first run):
```bash
ansible-galaxy collection install -r requirements.yml
```

Run the full playbook:
```bash
ansible-playbook playbooks/laptop.yml --ask-become-pass
```

Lint (mirrors CI):
```bash
ansible-lint
```

Run with verbose output for debugging:
```bash
ansible-playbook playbooks/laptop.yml --ask-become-pass -v
```

## Architecture

```
ansible.cfg          # Sets inventory = inventory/hosts.ini
inventory/hosts.ini  # Single group: cachyos_laptop → localhost (local connection)
vars/packages.yml    # Two lists: pacman_packages and aur_packages
playbooks/laptop.yml # All tasks in one file, loads vars from ../vars/packages.yml
requirements.yml     # community.general + kewlfft.aur collections
```

**All tasks live in `playbooks/laptop.yml`** — there are no roles. The playbook targets the `cachyos_laptop` inventory group.

### AUR handling

AUR packages require an unprivileged `aurbuilder` user. The playbook:
1. Creates the `aurbuilder` system user
2. Grants it passwordless sudo for `pacman` and `paru` only
3. Checks whether `paru` is installed; if not, clones and builds it from source
4. Uses `kewlfft.aur` to install packages via `paru` as `aurbuilder`

### Adding packages

- **Official/CachyOS packages** → add to `pacman_packages` in `vars/packages.yml`
- **AUR packages** → add to `aur_packages` in `vars/packages.yml`
- **Other install methods** (AppImage, systemd service, etc.) → add tasks directly to `playbooks/laptop.yml`

## CI

GitHub Actions runs `ansible-lint` on every pull request to `main`. No deployment pipeline — the playbook is run manually on the target machine.
