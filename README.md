# macos-init

一份面向 AI Agent 的 macOS 初始化配置规范。用一份 Markdown 文档描述你的 Mac 目标状态，让 AI Agent 来执行。

灵感来自 [stdrc/macos-init](https://github.com/stdrc/macos-init)。

## 涵盖内容

- **基础工具链**：Homebrew、CLI 工具（ripgrep / fd / fzf / zoxide / tmux 等）、语言运行时（nvm + Node.js、Miniconda）
- **GUI 应用安装**：WezTerm、Rectangle、Obsidian、微信、VS Code、Clash Verge Rev 等 17 款应用，全部从官网下载，使用 [agent-browser](https://github.com/vercel-labs/agent-browser) 自动化
- **macOS 系统设置**：Dock、深色模式、触控板/鼠标速度、键盘重复速率、Stage Manager、触发角、Fliqlo 屏保、电源管理
- **开发工具**：dotfiles 同步（基于 dotbot）、终端配置
- **Dock 排列**：通过 dockutil 按分组自定义应用顺序
- **验收标准**：工具版本、应用安装、架构、系统设置、dotfiles 部署的完整验证脚本

## 使用方法

无需 clone 仓库，直接把以下内容发给 AI Agent（如 Claude Code）：

> 请读取 https://raw.githubusercontent.com/Ziy1-Tan/macos-init/main/macos-config.md ，按其中的规范帮我初始化这台 Mac。

AI Agent 会自动获取配置文档并按顺序执行所有步骤。

## 设计理念

与 shell 脚本或 Ansible playbook 不同，本项目使用 **Markdown 规范**作为载体，专为 AI Agent 阅读和执行而设计：

- 每步骤均为幂等操作（执行前检查当前状态）
- 优先使用 `defaults write` / CLI 命令配置系统设置
- GUI 应用下载通过 agent-browser 自动完成
- Apple Silicon (ARM64) 原生优先
- 每项配置附带验证命令，执行完成后可一键验收

## 依赖

- macOS，Apple Silicon (ARM64) 架构
- 已联网（首次执行需要下载各类工具和应用）
- AI Agent 支持 bash 工具调用及 agent-browser（GUI 应用下载部分）
