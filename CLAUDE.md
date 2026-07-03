# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

Single-playbook Ansible project that configures a laptop running CachyOS or Ubuntu. It runs locally against `localhost` and installs packages via the appropriate package manager for the detected OS (pacman/paru for Arch-based, apt/snap for Debian-based), plus app-specific methods (AppImage, systemd).

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
inventory/hosts.ini  # Groups: cachyos_laptop, ubuntu_laptop; parent group: laptop
vars/packages.yml    # Package lists: cachyos_pacman_packages, cachyos_aur_packages,
                     #                ubuntu_apt_packages, ubuntu_snap_packages,
                     #                ubuntu_snap_classic_packages
playbooks/laptop.yml # All tasks in one file, loads vars from ../vars/packages.yml
requirements.yml     # community.general + kewlfft.aur collections
```

**All tasks live in `playbooks/laptop.yml`** — there are no roles. The playbook targets the `laptop` inventory group (which includes `cachyos_laptop` and `ubuntu_laptop` as children).

OS-specific task blocks use `ansible_os_family` to conditionally run tasks:
- `ansible_os_family == 'Archlinux'` → CachyOS / Arch Linux tasks (pacman, AUR/paru)
- `ansible_os_family == 'Debian'` → Ubuntu / Debian tasks (apt, snap)

Common tasks (Espanso AppImage, rclone systemd service) run on all supported OS families.

### AUR handling (CachyOS / Arch Linux only)

AUR packages require an unprivileged `aurbuilder` user. The playbook:
1. Creates the `aurbuilder` system user
2. Grants it passwordless sudo for `pacman` and `paru` only
3. Checks whether `paru` is installed; if not, clones and builds it from source
4. Uses `kewlfft.aur` to install packages via `paru` as `aurbuilder`

### Adding packages

- **Official/CachyOS packages** → add to `cachyos_pacman_packages` in `vars/packages.yml`
- **AUR packages** → add to `cachyos_aur_packages` in `vars/packages.yml`
- **Ubuntu apt packages** → add to `ubuntu_apt_packages` in `vars/packages.yml`
- **Ubuntu snap packages** → add to `ubuntu_snap_packages` (or `ubuntu_snap_classic_packages` for classic confinement) in `vars/packages.yml`
- **Other install methods** (AppImage, systemd service, etc.) → add tasks directly to `playbooks/laptop.yml`

### Inventory setup

- **CachyOS / Arch Linux**: uncomment (or add) `localhost ansible_connection=local` under `[cachyos_laptop]`
- **Ubuntu**: uncomment (or add) your host under `[ubuntu_laptop]`

## CI

GitHub Actions runs `ansible-lint` on every pull request to `main`. No deployment pipeline — the playbook is run manually on the target machine.
