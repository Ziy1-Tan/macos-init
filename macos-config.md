# macOS 初始化配置规范

## 原则

- CLI 工具通过 Homebrew 安装；GUI 应用从官网下载，不使用 Homebrew Cask
- 架构优先级：arm64 > universal > x86_64
- 每步执行后验证，确认生效再继续

---

## 1. CLI 工具

| 工具 | 安装 | 验证 |
|------|------|------|
| Homebrew | 官方安装脚本 | `brew --version` |
| ripgrep | `brew install ripgrep` | `rg --version` |
| fd | `brew install fd` | `fd --version` |
| fzf | `brew install fzf` | `fzf --version` |
| zoxide | `brew install zoxide` | `zoxide --version` |
| neofetch | `brew install neofetch` | `neofetch --version` |
| curl | `brew install curl` | `curl --version` |
| tldr | `brew install tldr` | `tldr --version` |
| tmux | `brew install tmux` | `tmux -V` |
| zsh | `brew install zsh` | `zsh --version` |
| dockutil | `brew install dockutil` | `dockutil --version` |

---

## 2. 语言运行时

| 工具 | 安装方式 | 验证 |
|------|----------|------|
| nvm | 官方 curl 安装脚本 | `nvm --version` |
| Node.js LTS | `nvm install --lts` | `node --version`（≥ 20.x） |
| npm | 随 Node.js 安装 | `npm --version` |
| Miniconda | 官方 ARM64 安装包（`Miniconda3-latest-MacOSX-arm64.sh`） | `conda --version` |

---

## 3. GUI 应用

### 3.1 标准 DMG 安装（拖拽到 /Applications）

| 应用 | 官网 | 架构 | 验证路径 |
|------|------|------|----------|
| WezTerm | https://wezfurlong.org/wezterm/ | ARM64 | `/Applications/WezTerm.app` |
| Rectangle | https://rectangleapp.com/ | Universal | `/Applications/Rectangle.app` |
| Hidden Bar | https://github.com/dwarvesf/hidden | Universal | `/Applications/Hidden Bar.app` |
| Maccy | https://maccy.app/ | Universal | `/Applications/Maccy.app` |
| Snipaste | https://www.snipaste.com/ | ARM64 | `/Applications/Snipaste.app` |
| BetterDisplay | https://github.com/wahlberg/BetterDisplay | Universal | `/Applications/BetterDisplay.app` |
| Obsidian | https://obsidian.md/ | ARM64 | `/Applications/Obsidian.app` |
| 微信 | https://weixin.qq.com/ | Universal | `/Applications/WeChat.app` |
| QQ | https://im.qq.com/macqq/ | Universal | `/Applications/QQ.app` |
| Microsoft Edge | https://www.microsoft.com/edge | Universal | `/Applications/Microsoft Edge.app` |
| Google Chrome | https://www.google.com/chrome/ | ARM64 | `/Applications/Google Chrome.app` |
| Visual Studio Code | https://code.visualstudio.com/ | ARM64 | `/Applications/Visual Studio Code.app` |
| SwitchHosts | https://github.com/oldj/SwitchHosts | Universal | `/Applications/SwitchHosts.app` |

### 3.2 非标准安装

| 应用 | 官网 | 架构 | 安装方式 | 验证路径 |
|------|------|------|----------|----------|
| Itsycal | https://www.mowglii.com/itsycal/ | Universal | zip 解压后移入 /Applications | `/Applications/Itsycal.app` |
| Fliqlo | https://fliqlo.com/ | Universal | .saver 文件复制到 `/Library/Screen Savers/` | `/Library/Screen Savers/Fliqlo.saver` |
| Nerd Fonts (FiraCode + SauceCodePro) | https://github.com/ryanoasis/nerd-fonts/releases/latest | 与架构无关 | zip 解压，.ttf 复制到 `~/Library/Fonts/` | `ls ~/Library/Fonts/FiraCodeNerdFont* ~/Library/Fonts/SauceCodeProNerdFont*` |
| 微信输入法 | https://z.weixin.qq.com/ | ARM64 | DMG 拖拽安装 | `/Library/Input Methods/WeChatIM.app` |
| Clash Verge Rev | https://github.com/clash-verge-rev/clash-verge-rev/releases/latest | ARM64 | GitHub Release，文件名含 `aarch64`，DMG 拖拽安装 | `/Applications/Clash Verge.app` |
| Microsoft To Do | https://apps.apple.com/app/microsoft-to-do/id1274495053 | — | Mac App Store，需用户手动点击获取 | `/Applications/Microsoft To Do.app` |

---

## 4. macOS 系统设置

### 4.1 Dock

| 设置 | 目标值 | 配置命令 | 验证命令（期望输出） |
|------|--------|----------|----------------------|
| 自动隐藏 | 关闭 | `defaults write com.apple.dock autohide -bool false` | `defaults read com.apple.dock autohide` → `0` |
| 图标大小 | 35px | `defaults write com.apple.dock tilesize -integer 35` | `defaults read com.apple.dock tilesize` → `35` |
| 位置 | 右侧 | `defaults write com.apple.dock orientation -string right` | `defaults read com.apple.dock orientation` → `right` |
| 放大效果 | 关闭 | `defaults write com.apple.dock magnification -bool false` | `defaults read com.apple.dock magnification` → `0` |
| 最近使用应用 | 隐藏 | `defaults write com.apple.dock show-recents -bool false` | `defaults read com.apple.dock show-recents` → `0` |

生效：`killall Dock`

### 4.2 外观

| 设置 | 目标值 | 配置命令 | 验证命令（期望输出） |
|------|--------|----------|----------------------|
| 外观 | 随系统 | `defaults delete NSGlobalDomain AppleInterfaceStyle 2>/dev/null; true` | `defaults read NSGlobalDomain AppleInterfaceStyle` → 报错"does not exist"（key 不存在即为随系统） |

### 4.3 触控板与鼠标

| 设置 | 目标值 | 配置命令 | 验证命令（期望输出） |
|------|--------|----------|----------------------|
| 触控板速度 | 0.875 | `defaults write NSGlobalDomain com.apple.trackpad.scaling -float 0.875` | `defaults read NSGlobalDomain com.apple.trackpad.scaling` → `0.875` |
| 鼠标速度 | 2 | `defaults write NSGlobalDomain com.apple.mouse.scaling -float 2` | `defaults read NSGlobalDomain com.apple.mouse.scaling` → `2` |
| 轻点来点按 | 开启 | `defaults write com.apple.AppleMultitouchTrackpad Clicking -bool true` | `defaults read com.apple.AppleMultitouchTrackpad Clicking` → `1` |
| 自然滚动 | 开启 | `defaults write NSGlobalDomain com.apple.swipescrolldirection -bool true` | `defaults read NSGlobalDomain com.apple.swipescrolldirection` → `1` |

### 4.4 键盘

| 设置 | 目标值 | 配置命令 | 验证命令（期望输出） |
|------|--------|----------|----------------------|
| 按键重复速率 | 最快 | `defaults write NSGlobalDomain KeyRepeat -int 2` | `defaults read NSGlobalDomain KeyRepeat` → `2` |
| 重复前延迟 | 最短 | `defaults write NSGlobalDomain InitialKeyRepeat -int 15` | `defaults read NSGlobalDomain InitialKeyRepeat` → `15` |

### 4.5 桌面与窗口

| 设置 | 目标值 | 配置命令 | 验证命令（期望输出） |
|------|--------|----------|----------------------|
| Stage Manager | 开启 | `defaults write com.apple.WindowManager GloballyEnabled -bool true` | `defaults read com.apple.WindowManager GloballyEnabled` → `1` |

### 4.6 触发角

| 角落 | 动作 | 配置命令 |
|------|------|----------|
| 左上角 | Mission Control (2) | `defaults write com.apple.dock wvous-tl-corner -int 2 && defaults write com.apple.dock wvous-tl-modifier -int 0` |
| 左下角 | 无操作 (1) | `defaults write com.apple.dock wvous-bl-corner -int 1 && defaults write com.apple.dock wvous-bl-modifier -int 0` |
| 右下角 | 启动屏幕保护 (5) | `defaults write com.apple.dock wvous-br-corner -int 5 && defaults write com.apple.dock wvous-br-modifier -int 0` |

验证：`defaults read com.apple.dock wvous-tl-corner` → `2`；`defaults read com.apple.dock wvous-br-corner` → `5`

生效：`killall Dock`

### 4.7 屏幕保护

Fliqlo 设为活跃屏保（需 Fliqlo 已安装）。通过系统设置 UI 操作，无稳定 CLI。

### 4.8 电源与睡眠

| 设置 | 目标值 | 配置命令 | 验证命令（期望输出） |
|------|--------|----------|----------------------|
| 接电源时显示器睡眠 | 永不 | `sudo pmset -c displaysleep 0` | `pmset -g custom`（AC Power 下 `displaysleep 0`） |
| 接电源时系统睡眠 | 永不 | `sudo pmset -c sleep 0` | `pmset -g custom`（AC Power 下 `sleep 0`） |

---

## 5. Dock 排列

使用 `dockutil` 配置，先清空再按以下顺序添加，组间插入 small-spacer。

| 顺序 | 应用 | 路径 |
|------|------|------|
| — | *(清空所有现有图标)* | `dockutil --remove all` |
| 1 | System Settings | `/System/Applications/System Settings.app` |
| 2 | Music | `/System/Applications/Music.app` |
| — | *(分隔线)* | small-spacer |
| 3 | Microsoft Edge | `/Applications/Microsoft Edge.app` |
| 4 | Google Chrome | `/Applications/Google Chrome.app` |
| — | *(分隔线)* | small-spacer |
| 5 | Visual Studio Code | `/Applications/Visual Studio Code.app` |
| 6 | WezTerm | `/Applications/WezTerm.app` |
| 7 | SwitchHosts | `/Applications/SwitchHosts.app` |
| — | *(分隔线)* | small-spacer |
| 8 | WeChat | `/Applications/WeChat.app` |
| 9 | QQ | `/Applications/QQ.app` |
| 10 | Obsidian | `/Applications/Obsidian.app` |
| 11 | Microsoft To Do | `/Applications/Microsoft To Do.app` |

验证：`dockutil --list` 输出与上表顺序一致。

---

## 6. 验收检查

```bash
# CLI 工具
brew --version
node --version       # ≥ 20.x
npm --version
nvm --version
conda --version

# 应用安装
for app in \
  "/Applications/WezTerm.app" \
  "/Applications/Rectangle.app" \
  "/Applications/Hidden Bar.app" \
  "/Applications/Maccy.app" \
  "/Applications/Snipaste.app" \
  "/Applications/Itsycal.app" \
  "/Applications/BetterDisplay.app" \
  "/Applications/Obsidian.app" \
  "/Library/Screen Savers/Fliqlo.saver" \
  "/Applications/WeChat.app" \
  "/Applications/QQ.app" \
  "/Applications/Microsoft To Do.app" \
  "/Applications/Microsoft Edge.app" \
  "/Applications/Google Chrome.app" \
  "/Applications/Visual Studio Code.app" \
  "/Applications/SwitchHosts.app" \
  "/Applications/Clash Verge.app"; do
  [ -e "$app" ] && echo "✓ $app" || echo "✗ $app"
done

# 字体
ls ~/Library/Fonts/FiraCodeNerdFont* ~/Library/Fonts/SauceCodeProNerdFont* 2>/dev/null \
  && echo "✓ Nerd Fonts" || echo "✗ Nerd Fonts"

# 微信输入法
[ -d "/Library/Input Methods/WeChatIM.app" ] \
  && echo "✓ 微信输入法" || echo "✗ 微信输入法"

# 架构（关键应用应为 arm64 或 universal）
lipo -archs /Applications/WezTerm.app/Contents/MacOS/wezterm-gui

# 系统设置
defaults read com.apple.dock autohide              # → 0
defaults read com.apple.dock tilesize              # → 35
defaults read com.apple.dock orientation           # → right
defaults read NSGlobalDomain AppleInterfaceStyle   # → 报错（key 不存在 = 随系统）
defaults read NSGlobalDomain KeyRepeat             # → 2
defaults read NSGlobalDomain InitialKeyRepeat      # → 15
defaults read com.apple.dock wvous-tl-corner       # → 2
defaults read com.apple.dock wvous-br-corner       # → 5
defaults read com.apple.WindowManager GloballyEnabled  # → 1
pmset -g custom   # AC Power: displaysleep 0, sleep 1
```
