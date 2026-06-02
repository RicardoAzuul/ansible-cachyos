# ansible-cachyos

Configure a CachyOS laptop with Ansible.

## What it installs

- Browsers and desktop apps: Firefox, Brave, DuckDuckGo Browser, Slack, VS Code, Teams, TickTick, WhatsApp, Telegram, Steam, Signal, Obsidian, Discord
- Developer tools: Git, Azure CLI, Terraform, Vagrant
- GOG client: Minigalaxy as the Linux-compatible GOG client

## Usage

Before running the playbook on a CachyOS laptop, install the required tooling manually:

```bash
# Sync and update all packages
sudo pacman -Syu
sudo pacman -S --needed ansible git base-devel
ansible-galaxy collection install community.general
```

The playbook also expects `paru` to be available for AUR packages. If `paru` is not already installed, bootstrap it manually:

```bash
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
cd ..
rm -rf paru
```

Run the playbook against the local machine:

```bash
ansible-playbook playbooks/laptop.yml --ask-become-pass
```

The playbook installs official CachyOS packages with pacman, AUR packages with `paru`, and installs Homebrew to manage Vagrant via `brew tap hashicorp/tap` and `brew install hashicorp/tap/vagrant`.

Review `vars/packages.yml` before running the playbook. The AUR install step uses `paru --noconfirm --needed`, so the listed AUR packages are installed without additional prompts.
