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

---

## 2. 官网应用安装

> 所有 GUI 应用一律从官网下载安装，不使用 Homebrew Cask。Agent 应访问官网获取下载链接。Apple Silicon 架构优先。

### 2.1 WezTerm（终端模拟器）

- **官网**：https://wezfurlong.org/wezterm/
- **安装方式**：下载 DMG，打开后拖动 WezTerm.app 到 `/Applications/`
- **架构说明**：下载页面选择 macOS Apple Silicon 版本

```bash
# 挂载 DMG 并安装（示例）
hdiutil attach ~/Downloads/WezTerm-*.dmg
cp -R /Volumes/WezTerm*/WezTerm.app /Applications/
hdiutil detach /Volumes/WezTerm*
```

### 2.2 Rectangle（窗口管理）

- **官网**：https://rectangleapp.com/
- **安装方式**：下载 DMG，打开后拖动 Rectangle.app 到 `/Applications/`
- **架构说明**：官方提供 Universal 二进制，原生支持 Apple Silicon

```bash
hdiutil attach ~/Downloads/Rectangle*.dmg
cp -R /Volumes/Rectangle*/Rectangle.app /Applications/
hdiutil detach /Volumes/Rectangle*
```

### 2.3 HiddenBar（菜单栏管理）

- **官网**：https://github.com/dwarvesf/hidden
- **安装方式**：从 GitHub Release 页面下载 DMG，打开后拖动 Hidden Bar.app 到 `/Applications/`
- **架构说明**：GitHub Release 提供 Universal 二进制

```bash
hdiutil attach ~/Downloads/Hidden\ Bar*.dmg
cp -R "/Volumes/Hidden Bar*/Hidden Bar.app" /Applications/
hdiutil detach "/Volumes/Hidden Bar*"
```

### 2.4 Maccy（剪贴板管理）

- **官网**：https://maccy.app/
- **安装方式**：下载 DMG，打开后拖动 Maccy.app 到 `/Applications/`
- **架构说明**：官方提供 Universal 二进制，原生支持 Apple Silicon

```bash
hdiutil attach ~/Downloads/Maccy*.dmg
cp -R /Volumes/Maccy*/Maccy.app /Applications/
hdiutil detach /Volumes/Maccy*
```

### 2.5 Snipaste（截图/贴图）

- **官网**：https://www.snipaste.com/
- **安装方式**：下载 DMG，打开后拖动 Snipaste.app 到 `/Applications/`
- **架构说明**：下载页面选择 macOS ARM64 版本

```bash
hdiutil attach ~/Downloads/Snipaste-*.dmg
cp -R /Volumes/Snipaste*/Snipaste.app /Applications/
hdiutil detach /Volumes/Snipaste*
```

### 2.6 Itsycal（菜单栏日历）

- **官网**：https://www.mowglii.com/itsycal/
- **安装方式**：下载 zip 文件，解压后将 Itsycal.app 移动到 `/Applications/`
- **架构说明**：官方提供 Universal 二进制

```bash
unzip ~/Downloads/Itsycal*.zip -d /tmp/itsycal
mv /tmp/itsycal/Itsycal.app /Applications/
rm -rf /tmp/itsycal
```

### 2.7 BetterDisplay（显示器管理）

- **官网**：https://github.com/wahlber/BetterDisplay
- **安装方式**：从 GitHub Release 页面下载 DMG，打开后拖动 BetterDisplay.app 到 `/Applications/`
- **架构说明**：GitHub Release 提供 Universal 二进制

```bash
hdiutil attach ~/Downloads/BetterDisplay-*.dmg
cp -R /Volumes/BetterDisplay*/BetterDisplay.app /Applications/
hdiutil detach /Volumes/BetterDisplay*
```

### 2.8 Obsidian（笔记）

- **官网**：https://obsidian.md/
- **安装方式**：下载 DMG，打开后拖动 Obsidian.app 到 `/Applications/`
- **架构说明**：下载页面提供 Apple Silicon (ARM64) 原生版本

```bash
hdiutil attach ~/Downloads/Obsidian-*.dmg
cp -R /Volumes/Obsidian*/Obsidian.app /Applications/
hdiutil detach /Volumes/Obsidian*
```

### 2.9 Fliqlo（翻页时钟屏保）

- **官网**：https://fliqlo.com/
- **安装方式**：下载后获得 `.saver` 文件，双击安装到 `/Library/Screen Savers/`
- **架构说明**：屏保文件为通用格式，兼容 Apple Silicon

```bash
# 双击 .saver 文件会自动弹出安装对话框，或手动复制
cp ~/Downloads/Fliqlo.saver /Library/Screen\ Savers/
```

### 2.10 Fira Code Nerd Font（编程字体）

- **官网**：https://www.nerdfonts.com/
- **GitHub Release**：https://github.com/ryanoasis/nerd-fonts/releases
- **安装方式**：从 GitHub Release 下载 `FiraCode.zip`，解压后将所有 `.ttf` 文件复制到 `~/Library/Fonts/`
- **架构说明**：字体文件与 CPU 架构无关

```bash
# 下载并解压
unzip ~/Downloads/FiraCode.zip -d /tmp/FiraCode

# 安装字体
cp /tmp/FiraCode/*.ttf ~/Library/Fonts/

# 清理临时文件
rm -rf /tmp/FiraCode
```

### 2.11 SauceCodePro Nerd Font（编程字体）

- **官网**：https://www.nerdfonts.com/
- **GitHub Release**：https://github.com/ryanoasis/nerd-fonts/releases
- **安装方式**：从 GitHub Release 下载 `SourceCodePro.zip`，解压后将所有 `.ttf` 文件复制到 `~/Library/Fonts/`
- **架构说明**：字体文件与 CPU 架构无关

```bash
# 下载并解压
unzip ~/Downloads/SourceCodePro.zip -d /tmp/SauceCodePro

# 安装字体
cp /tmp/SauceCodePro/*.ttf ~/Library/Fonts/

# 清理临时文件
rm -rf /tmp/SauceCodePro
```

### 2.12 安装验证

```bash
# 应用安装验证
ls /Applications/WezTerm.app
ls /Applications/Rectangle.app
ls "/Applications/Hidden Bar.app"
ls /Applications/Maccy.app
ls /Applications/Snipaste.app
ls /Applications/Itsycal.app
ls /Applications/BetterDisplay.app
ls /Applications/Obsidian.app
ls /Library/Screen\ Savers/Fliqlo.saver
ls ~/Library/Fonts/*FiraCode* ~/Library/Fonts/*SauceCodePro*
```

---

## 3. macOS 系统设置

> 所有设置项均使用 `defaults write` / CLI 命令配置，每项附带验证命令。修改 Dock 相关设置后需执行 `killall Dock` 使其生效。需要 `sudo` 的命令建议在 Terminal.app 中执行以便输入密码。

### 3.1 Dock

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| 自动隐藏 | 关闭 | `defaults write com.apple.dock autohide -bool false` | `defaults read com.apple.dock autohide` → `0` |
| 图标大小 | 35px | `defaults write com.apple.dock tilesize -integer 35` | `defaults read com.apple.dock tilesize` → `35` |
| 位置 | 右侧 | `defaults write com.apple.dock orientation -string right` | `defaults read com.apple.dock orientation` → `right` |
| 放大效果 | 关闭 | `defaults write com.apple.dock magnification -bool false` | `defaults read com.apple.dock magnification` → `0` |

**生效命令**

```bash
killall Dock
```

### 3.2 外观

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| 深色模式 | 开启 | `defaults write NSGlobalDomain AppleInterfaceStyle -string Dark` | `defaults read NSGlobalDomain AppleInterfaceStyle` → `Dark` |

### 3.3 触控板与鼠标

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| 触控板速度 | 0.875 | `defaults write NSGlobalDomain com.apple.trackpad.scaling -float 0.875` | `defaults read NSGlobalDomain com.apple.trackpad.scaling` → `0.875` |
| 鼠标速度 | 2 | `defaults write NSGlobalDomain com.apple.mouse.scaling -float 2` | `defaults read NSGlobalDomain com.apple.mouse.scaling` → `2` |
| 轻点来点按 | 开启 | `defaults write com.apple.AppleMultitouchTrackpad Clicking -bool true` | `defaults read com.apple.AppleMultitouchTrackpad Clicking` → `1` |
| 自然滚动方向 | 开启 | `defaults write NSGlobalDomain com.apple.swipescrolldirection -bool true` | `defaults read NSGlobalDomain com.apple.swipescrolldirection` → `1` |

### 3.4 键盘

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| 按键重复速率 | 最快 (2) | `defaults write NSGlobalDomain KeyRepeat -int 2` | `defaults read NSGlobalDomain KeyRepeat` → `2` |
| 重复前延迟 | 最短 (15) | `defaults write NSGlobalDomain InitialKeyRepeat -int 15` | `defaults read NSGlobalDomain InitialKeyRepeat` → `15` |

### 3.5 桌面与窗口

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| Stage Manager | 开启 | `defaults write com.apple.WindowManager GloballyEnabled -bool true` | `defaults read com.apple.WindowManager GloballyEnabled` → `1` |

### 3.6 触发角

> 触发角数值含义：`1` = 无操作，`2` = Mission Control，`5` = 启动屏幕保护程序。`modifier` 设为 `0` 表示不需要修饰键。

| 角落 | 动作 | 配置命令 |
|------|------|----------|
| 左上角 | Mission Control | `defaults write com.apple.dock wvous-tl-corner -int 2 && defaults write com.apple.dock wvous-tl-modifier -int 0` |
| 左下角 | 无操作 | `defaults write com.apple.dock wvous-bl-corner -int 1 && defaults write com.apple.dock wvous-bl-modifier -int 0` |
| 右下角 | 启动屏幕保护 | `defaults write com.apple.dock wvous-br-corner -int 5 && defaults write com.apple.dock wvous-br-modifier -int 0` |

**生效命令**

```bash
killall Dock
```

**验证**

```bash
defaults read com.apple.dock wvous-tl-corner   # → 2
defaults read com.apple.dock wvous-br-corner   # → 5
defaults read com.apple.dock wvous-bl-corner   # → 1
```

### 3.7 屏幕保护

- Fliqlo 须在第 2 节安装完成后配置。
- 触发方式：右下角触发角（见 3.6）。
- 选择 Fliqlo 为活跃屏保可能需要 UI 自动化（系统设置 > 屏幕保护程序 > 选择 Fliqlo）。

### 3.8 电源与睡眠

> 需要 `sudo` 权限，Agent 应使用 Terminal.app 执行以便输入密码。

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| 接电源时显示器睡眠 | 永不 (0) | `sudo pmset -c displaysleep 0` | `pmset -g custom` 中 AC Power 下 `displaysleep 0` |
| 接电源时系统睡眠 | 1 分钟 | `sudo pmset -c sleep 1` | `pmset -g custom` 中 AC Power 下 `sleep 1` |

### 3.9 一键配置脚本

> 将 3.1 ~ 3.8 中所有 `defaults write` 与 `pmset` 命令整合为一个脚本，便于一次性执行。

```bash
#!/bin/bash
set -euo pipefail

echo "=== macOS 系统设置配置开始 ==="

# 3.1 Dock
defaults write com.apple.dock autohide -bool false
defaults write com.apple.dock tilesize -integer 35
defaults write com.apple.dock orientation -string right
defaults write com.apple.dock magnification -bool false

# 3.2 外观
defaults write NSGlobalDomain AppleInterfaceStyle -string Dark

# 3.3 触控板与鼠标
defaults write NSGlobalDomain com.apple.trackpad.scaling -float 0.875
defaults write NSGlobalDomain com.apple.mouse.scaling -float 2
defaults write com.apple.AppleMultitouchTrackpad Clicking -bool true
defaults write NSGlobalDomain com.apple.swipescrolldirection -bool true

# 3.4 键盘
defaults write NSGlobalDomain KeyRepeat -int 2
defaults write NSGlobalDomain InitialKeyRepeat -int 15

# 3.5 桌面与窗口
defaults write com.apple.WindowManager GloballyEnabled -bool true

# 3.6 触发角
defaults write com.apple.dock wvous-tl-corner -int 2
defaults write com.apple.dock wvous-tl-modifier -int 0
defaults write com.apple.dock wvous-bl-corner -int 1
defaults write com.apple.dock wvous-bl-modifier -int 0
defaults write com.apple.dock wvous-br-corner -int 5
defaults write com.apple.dock wvous-br-modifier -int 0

# 3.8 电源与睡眠（需要 sudo）
sudo pmset -c displaysleep 0
sudo pmset -c sleep 1

# 重启 Dock 使设置生效
killall Dock

echo "=== macOS 系统设置配置完成 ==="
```
