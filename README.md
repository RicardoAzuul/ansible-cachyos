# ansible-cachyos

Configure a CachyOS laptop with Ansible.

## What it installs

- Browsers and desktop apps: Firefox, Brave, Slack, VS Code, Teams, TickTick, Telegram, Steam, Signal, Obsidian, Discord, Espanso, OnlyOffice
- Developer tools: Git, Azure CLI, Terraform, Vagrant, rclone, pipx
- GOG client: Minigalaxy as the Linux-compatible GOG client

## Usage

Before running the playbook on a CachyOS laptop, install the required tooling manually:

```bash
# Sync and update all packages
sudo pacman -Syu
sudo pacman -S --needed ansible git base-devel
ansible-galaxy collection install community.general
ansible-galaxy collection install kewlfft.aur
```

Run the playbook against the local machine:

```bash
ansible-playbook playbooks/laptop.yml --ask-become-pass
```

The playbook installs official CachyOS packages with pacman, automatically installs the `paru` AUR helper if it is not already present, installs AUR packages with `kewlfft.aur.aur` running as the `aurbuilder` user, and installs Homebrew to manage Vagrant via `brew tap hashicorp/tap` and `brew install hashicorp/tap/vagrant`.

Review `vars/packages.yml` before running the playbook. The AUR install step uses `paru` via the `kewlfft.aur` collection, so packages are installed without additional prompts.
