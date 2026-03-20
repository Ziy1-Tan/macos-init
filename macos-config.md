# macOS 初始化配置规范

---

## 0. 目标与原则

- **本文档用于在新 Mac 上执行一次完整的初始化配置**，覆盖从基础工具链到开发环境的全部步骤。
- **适用范围**：全新 Mac，Apple Silicon (ARM64) 架构。
- **CLI 工具通过 Homebrew 安装；GUI 应用一律从官网下载**，不使用 Homebrew Cask。下载过程使用 [agent-browser](https://github.com/vercel-labs/agent-browser) 自动化。
- **系统设置优先使用 `defaults write` / CLI 命令**；无稳定 CLI 的设置使用 UI 自动化（如 AppleScript / System Events）。
- **幂等性**：每个步骤执行前先检查当前状态（如 `command -v brew`），避免重复执行或覆盖已有配置。
- **架构要求**：优先 arm64 原生二进制，可接受 universal，避免仅 x86_64 的构建产物。
- **最小验证**：每项配置执行后做最小验证，确认生效后再进入下一步。

---

## 1. 基础工具链（必须按顺序）

### 1.1 Homebrew

```bash
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi
```

### 1.2 CLI 工具（brew install）

| 分类 | 工具 |
|------|------|
| 搜索/导航 | `ripgrep`, `fd`, `fzf`, `zoxide` |
| 系统信息 | `neofetch` |
| 网络 | `curl`, `tldr` |
| 终端 | `tmux` |
| Shell | `zsh` |

```bash
brew install ripgrep fd fzf zoxide neofetch curl tldr tmux zsh
```

### 1.3 语言运行时

| 工具 | 安装方式 | 验证命令 |
|------|----------|----------|
| nvm | `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/HEAD/install.sh \| bash` | `nvm --version` |
| Node.js LTS | `nvm install --lts` | `node --version`（>= 20.x）、`npm --version` |
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
```

---

## 2. 官网应用安装

### 2.0 前置依赖：安装 agent-browser

```bash
if ! command -v agent-browser &>/dev/null; then
  npm install -g agent-browser
  agent-browser install
fi
```

### 2.1 DMG 应用（标准拖拽安装）

| 应用 | 用途 | 官网 | 架构 |
|------|------|------|------|
| WezTerm | 终端模拟器 | https://wezfurlong.org/wezterm/ | ARM64 |
| Rectangle | 窗口管理 | https://rectangleapp.com/ | Universal |
| Hidden Bar | 菜单栏管理 | https://github.com/dwarvesf/hidden | Universal |
| Maccy | 剪贴板管理 | https://maccy.app/ | Universal |
| Snipaste | 截图/贴图 | https://www.snipaste.com/ | ARM64 |
| BetterDisplay | 显示器管理 | https://github.com/wahlber/BetterDisplay | Universal |
| Obsidian | 笔记 | https://obsidian.md/ | ARM64 |
| 微信 | 即时通讯 | https://weixin.qq.com/ | Universal |
| QQ | 即时通讯 | https://im.qq.com/macqq/index.shtml | Universal |
| Microsoft Edge | 浏览器 | https://www.microsoft.com/edge | Universal |
| Google Chrome | 浏览器 | https://www.google.com/chrome/ | ARM64 |
| Visual Studio Code | 代码编辑器 | https://code.visualstudio.com/ | ARM64 |
| SwitchHosts | Hosts 管理 | https://github.com/oldj/SwitchHosts | Universal |
| Clash Verge Rev | 代理客户端 | https://github.com/clash-verge-rev/clash-verge-rev/releases/latest | ARM64 |

**自动下载流程（Agent 对表格中每个应用重复执行）**

```bash
# 1. 访问官网，截图定位下载入口
agent-browser open "<官网URL>"
agent-browser screenshot --annotate

# 2. 定位 Apple Silicon / ARM64 / Universal 下载链接并点击
agent-browser snapshot -i
agent-browser click <下载按钮ref>          # Agent 根据 snapshot 结果确定 ref

# 3. 如有中间页面（如 GitHub Release），继续导航并定位 .dmg 资产
agent-browser snapshot -i
agent-browser download <dmg链接ref> ~/Downloads/<应用名>.dmg

# 4. 挂载并安装
hdiutil attach ~/Downloads/<应用名>.dmg
cp -R /Volumes/<卷名>/<应用名>.app /Applications/
hdiutil detach /Volumes/<卷名>
```

### 2.2 非标准安装

#### Itsycal（菜单栏日历 · zip 下载）

- **官网**：https://www.mowglii.com/itsycal/
- **架构**：Universal

```bash
agent-browser open "https://www.mowglii.com/itsycal/"
agent-browser snapshot -i
agent-browser download <zip链接ref> ~/Downloads/Itsycal.zip

unzip ~/Downloads/Itsycal.zip -d /tmp/itsycal
mv /tmp/itsycal/Itsycal.app /Applications/
rm -rf /tmp/itsycal
```

#### Fliqlo（翻页时钟屏保 · .saver 文件）

- **官网**：https://fliqlo.com/
- **架构**：通用格式

```bash
agent-browser open "https://fliqlo.com/"
agent-browser snapshot -i
agent-browser download <下载按钮ref> ~/Downloads/Fliqlo.saver

cp ~/Downloads/Fliqlo.saver /Library/Screen\ Savers/
```

#### Nerd Fonts（编程字体 · zip 下载）

- **官网**：https://www.nerdfonts.com/
- **GitHub Release**：https://github.com/ryanoasis/nerd-fonts/releases
- 需要安装的字体：**FiraCode**、**SourceCodePro (SauceCodePro)**
- 字体文件与 CPU 架构无关

```bash
agent-browser open "https://github.com/ryanoasis/nerd-fonts/releases/latest"
agent-browser snapshot -i
agent-browser download <FiraCode.zip-ref> ~/Downloads/FiraCode.zip
agent-browser download <SourceCodePro.zip-ref> ~/Downloads/SourceCodePro.zip

for font in FiraCode SourceCodePro; do
  unzip ~/Downloads/${font}.zip -d /tmp/${font}
  cp /tmp/${font}/*.ttf ~/Library/Fonts/
  rm -rf /tmp/${font}
done
```

#### Microsoft To Do（Mac App Store）

- **官网**：https://apps.apple.com/app/microsoft-to-do/id1274495053

```bash
agent-browser open "https://apps.apple.com/app/microsoft-to-do/id1274495053"
# App Store 应用需用户在弹出的 App Store 中点击"获取"，无法全自动完成
```

#### 搜狗输入法（安装器 · 非拖拽）

- **官网**：https://pinyin.sogou.com/mac/
- **架构**：ARM64

```bash
agent-browser open "https://pinyin.sogou.com/mac/"
agent-browser snapshot -i
agent-browser download <下载按钮ref> ~/Downloads/sogou_mac.dmg

hdiutil attach ~/Downloads/sogou_mac.dmg
open /Volumes/sogou*/安装搜狗输入法.app || open /Volumes/sogou*/*.pkg
hdiutil detach /Volumes/sogou*
```

#### Clash Verge Rev（代理客户端 · GitHub Release DMG）

- **GitHub Release**：https://github.com/clash-verge-rev/clash-verge-rev/releases/latest
- **架构**：ARM64（文件名含 `aarch64`）

```bash
agent-browser open "https://github.com/clash-verge-rev/clash-verge-rev/releases/latest"
agent-browser snapshot -i
agent-browser download <clash-verge-rev_*_aarch64.dmg-ref> ~/Downloads/ClashVergeRev.dmg

hdiutil attach ~/Downloads/ClashVergeRev.dmg
cp -R "/Volumes/Clash Verge/Clash Verge.app" /Applications/
hdiutil detach "/Volumes/Clash Verge"
```

### 2.3 安装验证

```bash
apps=(
  /Applications/WezTerm.app
  /Applications/Rectangle.app
  "/Applications/Hidden Bar.app"
  /Applications/Maccy.app
  /Applications/Snipaste.app
  /Applications/Itsycal.app
  /Applications/BetterDisplay.app
  /Applications/Obsidian.app
  "/Library/Screen Savers/Fliqlo.saver"
  /Applications/WeChat.app
  /Applications/QQ.app
  "/Applications/Microsoft To Do.app"
  "/Applications/Microsoft Edge.app"
  "/Applications/Google Chrome.app"
  "/Applications/Visual Studio Code.app"
  /Applications/SwitchHosts.app
  "/Applications/Clash Verge.app"
)

for app in "${apps[@]}"; do
  [ -e "$app" ] && echo "✓ $app" || echo "✗ $app 未安装"
done

ls ~/Library/Fonts/*FiraCode* ~/Library/Fonts/*SauceCodePro* 2>/dev/null \
  && echo "✓ Nerd Fonts 已安装" || echo "✗ Nerd Fonts 未安装"

[ -d "/Library/Input Methods/SogouInput.app" ] \
  && echo "✓ 搜狗输入法已安装" || echo "✗ 搜狗输入法未安装"
```

---

## 3. macOS 系统设置

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

| 角落 | 动作 | 配置命令 |
|------|------|----------|
| 左上角 | Mission Control | `defaults write com.apple.dock wvous-tl-corner -int 2 && defaults write com.apple.dock wvous-tl-modifier -int 0` |
| 左下角 | 无操作 | `defaults write com.apple.dock wvous-bl-corner -int 1 && defaults write com.apple.dock wvous-bl-modifier -int 0` |
| 右下角 | 启动屏幕保护 | `defaults write com.apple.dock wvous-br-corner -int 5 && defaults write com.apple.dock wvous-br-modifier -int 0` |

**生效命令**

```bash
killall Dock
```

### 3.7 屏幕保护

选择 Fliqlo 为活跃屏保：系统设置 > 屏幕保护程序 > 选择 Fliqlo（需 Fliqlo 已在第 2 节安装）。

### 3.8 电源与睡眠

| 设置 | 目标值 | 配置命令 | 验证命令 |
|------|--------|----------|----------|
| 接电源时显示器睡眠 | 永不 (0) | `sudo pmset -c displaysleep 0` | `pmset -g custom` 中 AC Power 下 `displaysleep 0` |
| 接电源时系统睡眠 | 1 分钟 | `sudo pmset -c sleep 1` | `pmset -g custom` 中 AC Power 下 `sleep 1` |

### 3.9 一键配置脚本

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

---

## 4. Dock 排列规范 Dock 排列规范

**安装 dockutil**

```bash
brew install dockutil
```

**配置 Dock**

```bash
# 清空 Dock
dockutil --remove all
sleep 1

# ── 系统 ──
dockutil --add "/System/Applications/System Settings.app" --no-restart
dockutil --add "/System/Applications/Music.app" --no-restart
# ── 浏览器 ──
dockutil --add "/Applications/Microsoft Edge.app" --no-restart
dockutil --add "/Applications/Google Chrome.app" --no-restart
# ── 开发工具 ──
dockutil --add "/Applications/Visual Studio Code.app" --no-restart
dockutil --add "/Applications/WezTerm.app" --no-restart
dockutil --add "/Applications/SwitchHosts.app" --no-restart
# ── 日常工具 ──
dockutil --add "/Applications/WeChat.app" --no-restart
dockutil --add "/Applications/QQ.app" --no-restart
dockutil --add "/Applications/Obsidian.app" --no-restart
dockutil --add "/Applications/Microsoft To Do.app"
sleep 1

# 插入分隔线（必须在所有应用添加完成后执行，否则后续 app 可能丢失）
dockutil --add '' --type small-spacer --section apps --after 'Music' --no-restart
dockutil --add '' --type small-spacer --section apps --after 'Google Chrome' --no-restart
dockutil --add '' --type small-spacer --section apps --after 'SwitchHosts'
```

---

## 5. 验收标准

### 5.1 工具版本检查

```bash
brew --version
node --version          # >= 20.x
npm --version
conda --version
nvm --version
```

### 5.2 应用安装验证

> 见第 2.3 节安装验证脚本，此处不再重复。

### 5.3 架构验证

```bash
# 确认关键应用为 ARM64 架构
lipo -archs /Applications/WezTerm.app/Contents/MacOS/wezterm-gui
# 预期输出包含 arm64
```

### 5.4 系统设置验证

```bash
defaults read com.apple.dock autohide              # → 0
defaults read com.apple.dock tilesize              # → 35
defaults read com.apple.dock orientation           # → right
defaults read NSGlobalDomain AppleInterfaceStyle   # → Dark
defaults read NSGlobalDomain KeyRepeat             # → 2
defaults read NSGlobalDomain InitialKeyRepeat      # → 15
defaults read com.apple.dock wvous-tl-corner       # → 2
defaults read com.apple.dock wvous-br-corner       # → 5
defaults read com.apple.WindowManager GloballyEnabled  # → 1
```

---

## 6. 备注（执行策略）

- 遇到需要 `sudo` 或密码输入的步骤时，使用 Terminal.app 执行以便用户输入密码。
- 官网优先级高于其他来源；除非官网不可用，不使用第三方下载站。
- UI 自动化执行系统设置时，优先使用 deep link（如 `open "x-apple.systempreferences:com.apple.Displays-Settings.extension"`）定位设置页面，避免依赖搜索框（输入法可能影响搜索词）。
- 若自动化工具无法直接控制某页面，退回"打开官网 + 人工操作"的兜底流程。
- 架构优先级：arm64 > universal > x86_64（仅在无其他选择时接受）。
- 本文档设计为一次性执行，不用于记录历史进展。
