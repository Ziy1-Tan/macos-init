# macos-init

An agent-readable macOS bootstrap spec. Define your target Mac setup in a Markdown document, then let an AI agent execute it.

Inspired by [stdrc/macos-init](https://github.com/stdrc/macos-init).

## What It Covers

- **Base toolchain**: Homebrew, CLI tools, language runtimes (nvm, Node.js, bun, uv, Miniconda)
- **GUI applications**: All installed from official websites (no Homebrew Cask)
- **macOS system settings**: Dock, appearance, trackpad, keyboard, hot corners, screensaver, power management
- **Dev tools**: dotfiles sync, terminal configuration
- **Dock arrangement**: Custom app order via dockutil
- **Verification**: Every step includes acceptance criteria

## Usage

```bash
git clone https://github.com/Ziy1-Tan/macos-init.git
cd macos-init

# Hand macos-config.md to an AI agent and let it execute the steps in order
```

## Approach

Instead of shell scripts or Ansible playbooks, this project uses a **Markdown specification** designed to be read and executed by AI agents (e.g., Claude Code). The spec includes:

- Step-by-step installation instructions
- `defaults write` commands for system settings
- UI automation guidance for settings without CLI support
- Verification commands for every configuration item

This makes the setup process readable, maintainable, and reproducible.
