# macOS 初始化配置规范

---

## 0. 目标与原则

- **本文档用于在新 Mac 上执行一次完整的初始化配置**，覆盖从基础工具链到开发环境的全部步骤。
- **适用范围**：全新 Mac，Apple Silicon (ARM64) 架构。
- **CLI 工具通过 Homebrew 安装；GUI 应用一律从官网下载**，不使用 Homebrew Cask。
- **系统设置优先使用 `defaults write` / CLI 命令**；无稳定 CLI 的设置使用 UI 自动化（如 AppleScript / System Events）。
- **幂等性**：每个步骤执行前先检查当前状态（如 `command -v brew`），避免重复执行或覆盖已有配置。
- **架构要求**：优先 arm64 原生二进制，可接受 universal，避免仅 x86_64 的构建产物。
- **最小验证**：每项配置执行后做最小验证，确认生效后再进入下一步。

---

## 1. 基础工具链（必须按顺序）

> 本节中的步骤存在依赖关系，请严格按照 1.1 -> 1.2 -> 1.3 的顺序执行。

### 1.1 Homebrew

**安装**

```bash
# 检查是否已安装
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi
```

**验证**

```bash
brew --version
# 预期输出：Homebrew 4.x.x
```

### 1.2 CLI 工具（brew install）

| 分类 | 工具 |
|------|------|
| 搜索/导航 | `ripgrep`, `fd`, `fzf`, `zoxide` |
| 系统信息 | `neofetch` |
| 网络 | `curl`, `tldr` |
| 终端 | `tmux` |
| Shell | `zsh` |

**安装**

```bash
brew install ripgrep fd fzf zoxide neofetch curl tldr tmux zsh
```

**验证**

```bash
command -v rg fd fzf zoxide neofetch curl tldr tmux zsh
# 每个命令都应输出对应的可执行文件路径
```

### 1.3 语言运行时

| 工具 | 安装方式 | 验证命令 |
|------|----------|----------|
| nvm | `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/HEAD/install.sh \| bash` | `nvm --version` |
| Node.js LTS | `nvm install --lts` | `node --version`（>= 20.x）、`npm --version` |
| bun | `curl -fsSL https://bun.sh/install \| bash` | `bun --version` |
| uv | `curl -LsSf https://astral.sh/uv/install.sh \| sh` | `uv --version` |
| Miniconda | 见下方安装步骤 | `conda --version` |

**nvm + Node.js**

```bash
# 安装 nvm
if ! command -v nvm &>/dev/null; then
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/HEAD/install.sh | bash
  export NVM_DIR="$HOME/.nvm"
  [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
fi

# 安装 Node.js LTS
nvm install --lts

# 验证
nvm --version
node --version   # >= 20.x
npm --version
```

**bun**

```bash
if ! command -v bun &>/dev/null; then
  curl -fsSL https://bun.sh/install | bash
fi

# 验证
bun --version
```

**uv**

```bash
if ! command -v uv &>/dev/null; then
  curl -LsSf https://astral.sh/uv/install.sh | sh
fi

# 验证
uv --version
```

**Miniconda**

```bash
if ! command -v conda &>/dev/null; then
  # 下载 ARM64 安装包
  curl -fsSL -o /tmp/Miniconda3-latest-MacOSX-arm64.sh \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh

  # 静默安装到 ~/miniconda3
  bash /tmp/Miniconda3-latest-MacOSX-arm64.sh -b -p "$HOME/miniconda3"

  # 初始化 zsh 集成
  "$HOME/miniconda3/bin/conda" init zsh

  # 清理安装包
  rm -f /tmp/Miniconda3-latest-MacOSX-arm64.sh
fi

# 验证（需要重新加载 shell 或 source ~/.zshrc）
conda --version
```
