# macos-init

一份面向 AI Agent 的 macOS 初始化配置规范。用一份 Markdown 文档描述你的 Mac 目标状态，让 AI Agent 来执行。

灵感来自 [stdrc/macos-init](https://github.com/stdrc/macos-init)。

## 涵盖内容

- **基础工具链**：Homebrew、CLI 工具、语言运行时（nvm、Node.js、bun、uv、Miniconda）
- **GUI 应用安装**：全部从官网下载安装（不使用 Homebrew Cask）
- **macOS 系统设置**：Dock、外观、触控板、键盘、触发角、屏幕保护、电源管理
- **开发工具**：dotfiles 同步、终端配置
- **Dock 排列**：通过 dockutil 自定义应用顺序
- **验收标准**：每个步骤均包含验证方式

## 使用方法

无需 clone 仓库，直接告诉 AI Agent：

> 请遵照 https://raw.githubusercontent.com/Ziy1-Tan/macos-init/main/macos-config.md 的配置规范，帮我初始化这台 Mac。

AI Agent 会自动读取配置文档并按顺序执行所有步骤。

## 设计理念

与 shell 脚本或 Ansible playbook 不同，本项目使用 **Markdown 规范**作为载体，专为 AI Agent（如 Claude Code）阅读和执行而设计。规范包含：

- 逐步安装说明
- 系统设置的 `defaults write` 命令
- 无 CLI 支持的设置项的 UI 自动化指引
- 每项配置的验证命令

让初始化流程更易读、易维护、可复现。
