# ansible-cachyos

Configure a CachyOS laptop with Ansible.

## What it installs

- Browsers and desktop apps: Firefox, Brave, DuckDuckGo Browser, Slack, VS Code, Teams, TickTick, WhatsApp, Telegram, Steam, Signal, Obsidian, Discord
- Developer tools: Git, Azure CLI, Terraform, Vagrant
- GOG client: Minigalaxy as the Linux-compatible GOG client

## Usage

Run the playbook against the local machine:

```bash
ansible-playbook playbooks/laptop.yml --ask-become-pass
```

The playbook installs official CachyOS packages with pacman and AUR packages with `paru`.

Review `vars/packages.yml` before running the playbook. The AUR install step uses `paru --noconfirm --needed`, so the listed AUR packages are installed without additional prompts.
