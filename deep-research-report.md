# Obsidian 多人协同笔记系统配置与 Claude Code 使用指南

## 1. 安装 Obsidian 与 Git 插件  
- **下载 Obsidian**：访问 Obsidian 官网（[obsidian.md/download](https://obsidian.md/download)）下载适合你系统的安装包，安装并运行 Obsidian。  
- **创建或打开笔记库（Vault）**：首次运行时可创建一个新 Vault，或打开已有文件夹作为 Vault。Obsidian 会将此 Vault 作为知识库目录。  
- **启用社区插件**：在 Obsidian 左下角点击设置，选择「社区插件」，先关闭安全模式（若提示开启），然后点击「浏览」，搜索并安装 **Obsidian Git** 插件。安装后启用该插件。  

## 2. 配置 Git 仓库与远程同步

### 2.1 创建远程仓库

在 [GitHub](https://github.com/new)、[GitLab](https://gitlab.com/projects/new) 或 [Gitee](https://gitee.com/projects/new) 等平台新建仓库，用于保存和同步 Obsidian Vault。

> ⚠️ **安全提示**：如果你的 Vault 包含敏感信息（如日记、密码等），建议将仓库设为 **Private（私有）**。公开仓库会暴露所有笔记内容。

创建完成后记下仓库地址，支持以下两种格式：
- **HTTPS**：`https://github.com/用户名/仓库名.git`（推荐新手使用，需要配置凭证管理器）
- **SSH**：`git@github.com:用户名/仓库名.git`（需要预先配置 SSH 密钥，更方便免密操作）

### 2.2 初始化本地仓库

有两种方式将 Vault 文件夹初始化为 Git 仓库：

**方式一：通过 Obsidian Git 插件（推荐）**
1. 打开命令面板（`Ctrl/Cmd + P`）
2. 输入并执行 `Git: Initialize a new repo`
3. 插件会自动在当前 Vault 根目录创建 `.git` 文件夹

**方式二：通过命令行**
```bash
# 进入 Vault 根目录
cd /path/to/your/vault
# 初始化 Git 仓库
git init
```

### 2.3 配置 `.gitignore` 文件

Vault 中并非所有文件都需要纳入版本控制。在 Vault 根目录创建 `.gitignore` 文件，排除不需要同步的内容：

```gitignore
# Obsidian 内部配置（通常不需要同步，但如果多人共用配置可以选择保留）
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/plugins/obsidian-git/data.json

# 系统文件
.DS_Store
Thumbs.db
desktop.ini

# 临时/缓存文件
.trash/
*.tmp
*.temp

# Claudian 插件本地配置（含 API 密钥，切勿提交）
.claudian/

# 其他不需要同步的文件夹（根据实际情况添加）
# 02-areas/private/
```

> 💡 **提示**：如果团队希望共享插件配置，可以将 `.obsidian/plugins/` 保留在版本控制中，但应排除 `workspace.json`（包含窗口布局等个人状态）和各插件的 `data.json`（含个人偏好设置）。

### 2.4 设置 Git 用户信息

确保每次提交都带有正确的作者信息：

**方式一：命令行设置（仓库级别）**
```bash
# 仅对当前仓库生效
git config user.name “你的名字”
git config user.email “你的邮箱@example.com”
```

**方式二：命令行设置（全局级别）**
```bash
# 对所有仓库生效
git config --global user.name “你的名字”
git config --global user.email “你的邮箱@example.com”
```

**方式三：Obsidian Git 插件设置**
在插件设置界面中也可以直接配置用户名和邮箱。

### 2.5 配置 SSH 密钥（推荐用于免密推送）

如果你使用 SSH 地址克隆仓库，建议配置 SSH 密钥以避免每次推送都输入密码：

```bash
# 1. 生成 SSH 密钥（使用你的 GitHub 邮箱）
ssh-keygen -t ed25519 -C “你的邮箱@example.com”
# 按提示选择保存位置（默认 ~/.ssh/id_ed25519），可设置密码短语

# 2. 启动 SSH Agent 并添加密钥
eval “$(ssh-agent -s)”
ssh-add ~/.ssh/id_ed25519

# 3. 复制公钥内容
cat ~/.ssh/id_ed25519.pub
# 将输出内容添加到 GitHub → Settings → SSH and GPG keys → New SSH key

# 4. 测试连接
ssh -T git@github.com
# 看到类似 “Hi username! You've successfully authenticated” 即成功
```

> 🔒 Windows 用户可使用 `clip < ~/.ssh/id_ed25519.pub` 直接复制公钥到剪贴板；macOS 用户使用 `pbcopy < ~/.ssh/id_ed25519.pub`。

### 2.6 连接远程仓库

**场景 A：已有本地 Vault，连接到远程空仓库**
```bash
# 添加远程仓库地址
git remote add origin https://github.com/用户名/仓库名.git
# 首次推送并设置上游分支
git push -u origin main
```

或在 Obsidian 中通过命令面板执行 `Git: Edit remotes`，输入名称 `origin` 和仓库 URL。

**场景 B：从远程仓库克隆到本地（适合新成员加入）**
1. 在命令面板执行 `Git: Clone an existing remote repo`
2. 输入仓库 URL 和本地目标路径
3. 克隆完成后用 Obsidian 打开该文件夹作为 Vault

也可以通过命令行：
```bash
git clone https://github.com/用户名/仓库名.git
# 然后在 Obsidian 中打开克隆下来的文件夹
```

### 2.7 配置自动同步

在 Obsidian Git 插件设置（设置 → 社区插件 → Git → ⚙️）中配置以下关键选项：

| 设置项 | 推荐值 | 说明 |
|--------|--------|------|
| **Auto save interval** | `5` - `15`（分钟） | 自动提交间隔，太频繁会产生大量提交记录 |
| **Auto push interval** | `10` - `30`（分钟） | 自动推送间隔，设为 `0` 则禁用 |
| **Auto pull interval** | `10` - `30`（分钟） | 自动拉取间隔，设为 `0` 则禁用 |
| **Auto pull on boot** | ✅ 启用 | 每次 Obsidian 启动时自动拉取远程更新 |
| **Pull before push** | ✅ 启用 | 推送前先拉取，减少冲突概率 |
| **Commit message** | `vault backup: {{date}}` | 自动提交的默认消息模板 |
| **Commit date format** | `YYYY-MM-DD HH:mm:ss` | 日期格式 |
| **Sync method** | `merge` | 同步方式（merge 为最安全默认值） |
| **Show status bar** | ✅ 启用 | 在底部状态栏显示 Git 状态 |

> 💡 **多人协作建议**：
> - 将自动提交间隔设为 **5-15 分钟**，推送间隔设为 **10-30 分钟**
> - 务必启用 **Pull before push**（推送前拉取），这是预防冲突的关键设置
> - 建议启用 **Auto pull on boot**，确保每次打开 Obsidian 都能获取最新内容
> - 如果团队在同一文件上频繁编辑，考虑缩短拉取间隔

### 2.8 HTTPS 凭证管理（Windows/macOS）

使用 HTTPS 方式推送时，可配置凭证管理器避免每次输入密码：

```bash
# Windows（安装 Git for Windows 时自带）
git config --global credential.helper manager

# macOS（使用 macOS 钥匙串）
git config --global credential.helper osxkeychain

# 也可以使用 GitHub CLI 简化认证
# 安装：https://cli.github.com/
gh auth login
# 按提示完成登录后，Git 操作会自动使用 gh 的认证
```

## 3. 使用 Obsidian Git 插件进行同步

### 3.1 日常工作流概述

多人协作时，推荐遵循以下标准工作流：

```
打开 Obsidian → 自动拉取（Auto Pull on Boot）
      ↓
编辑笔记（自动保存会触发自动提交）
      ↓
定期自动推送（Auto Push）或手动 Commit-and-sync
      ↓
关闭 Obsidian 前确认同步状态
```

### 3.2 源代码控制视图

**打开方式**：
- 命令面板（`Ctrl/Cmd + P`）→ 输入 `Git: Open source control view`
- 或点击左侧边栏的 **源代码控制图标**（分支形状）

**功能说明**：

源代码控制视图是日常使用最频繁的界面，它会实时列出所有已修改的文件，分为以下区域：

| 区域 | 作用 |
|------|------|
| **Changes（未暂存）** | 已修改但尚未标记为"准备提交"的文件 |
| **Staged（已暂存）** | 已标记为"准备提交"的文件 |
| **Untracked（未跟踪）** | 新创建但尚未被 Git 管理的文件 |

**对单个文件的操作**：
- 点击文件旁的 **➕ 图标** → 暂存文件（Stage）
- 点击文件旁的 **➖ 图标** → 取消暂存（Unstage）
- 点击文件旁的 **↩️ 图标** → 丢弃修改，恢复到上一次提交的版本
- 右键文件 → 可选择 **Add to .gitignore** 将其排除在版本控制之外

**批量操作**：
- 点击 **Stage all** 按钮 → 暂存所有变更文件
- 在提交信息框输入描述 → 点击 **Commit** 提交

### 3.3 提交（Commit）操作

Obsidian Git 插件提供了多种提交方式：

| 命令 | 适用场景 |
|------|----------|
| `Git: Commit` | 仅提交已暂存（Staged）的文件 |
| `Git: Commit all` | 自动暂存所有变更并提交（不包括未跟踪文件） |
| `Git: Commit all (staged and unstaged)` | 暂存所有变更（含未跟踪文件）并提交 |
| `Git: Commit-and-sync` | **最常用** — 提交所有变更 + 拉取远程 + 推送到远程，一步完成 |
| `Git: Commit staged` | 仅提交已手动暂存的文件 |

> 💡 **最佳实践**：
> - 日常使用直接执行 **`Commit-and-sync`** 即可，它会自动完成提交→拉取→推送全流程
> - 如需精确控制提交内容（如只提交某个文件），先在源代码控制视图中暂存目标文件，再执行 `Git: Commit`
> - 建议为每次提交写有意义的消息（如"添加会议纪要"而非默认的"vault backup"），方便日后查看历史

### 3.4 推送（Push）与拉取（Pull）

**推送**（将本地变更上传到远程仓库）：
- 命令面板 → `Git: Push`
- 或在底部状态栏点击 Git 状态区域 → 选择 Push
- 其他协作者需要你推送后才能看到你的更新

**拉取**（获取远程仓库的最新变更）：
- 命令面板 → `Git: Pull`
- 建议在以下时机主动拉取：
  - 每天第一次打开 Obsidian 时
  - 开始编辑共享笔记之前
  - 长时间未同步时

### 3.5 查看历史与差异

**历史视图**（History View）：
- 命令面板 → `Git: Open history view`
- 显示所有提交记录，包含：提交信息、作者、时间、涉及文件
- 点击某次提交可查看该次提交的完整变更内容
- 可按文件筛选，查看某个文件的修改历史

**差异视图**（Diff View）：
- 命令面板 → `Git: Open diff view`
- 直观显示当前文件与最近提交版本的差异
- 支持 **Split（分屏）** 和 **Unified（统一）** 两种对比模式，可在插件设置中切换
- **红色高亮** 表示删除的内容，**绿色高亮** 表示新增的内容

**行内标记**（Signs in Editor）：
- 在插件设置中启用后，编辑器左侧边栏（Gutter 区域）会实时显示行级修改标记
- 绿色标记 = 新增行，蓝色标记 = 修改行，红色标记 = 删除行
- 可直接在标记上进行跳转、暂存单行变更（Stage hunk）或撤销修改（Reset hunk）

### 3.6 冲突处理

当多人同时编辑同一文件时，Git 可能无法自动合并，此时会产生**合并冲突**。

**识别冲突**：
- Obsidian Git 会在状态栏和通知中提示冲突
- 冲突文件中会出现特殊标记：

```markdown
<<<<<<< HEAD
你的修改内容
=======
对方的修改内容
>>>>>>> origin/main
```

**解决冲突的步骤**：
1. 打开冲突文件，找到冲突标记区域
2. 决定保留哪一方的修改（或手动合并两者的内容）
3. 删除冲突标记（`<<<<<<<`、`=======`、`>>>>>>>` 三行）
4. 保存文件
5. 在源代码控制视图中暂存该文件
6. 执行 `Git: Commit` 完成冲突解决
7. 执行 `Git: Push` 推送合并结果

> ⚠️ **避免冲突的建议**：
> - 频繁拉取（启用自动拉取或手动拉取）
> - 避免多人同时编辑同一个文件，可以通过分工或使用不同的章节来减少冲突
> - 在编辑共享文件前先执行一次 Pull
> - 如果冲突难以解决，可使用 `Git: Open diff view` 对比两方修改后再决定

### 3.7 分支操作

分支适合在大型修改、实验性编辑或特定任务中使用，避免直接影响主分支：

| 命令 | 功能 |
|------|------|
| `Git: Create new branch` | 创建新分支 |
| `Git: Switch branch` | 切换到已有分支 |
| `Git: Delete branch` | 删除分支 |
| `Git: Merge branch` | 将指定分支合并到当前分支 |

**典型使用场景**：
- 📝 **撰写长文**：创建 `draft/文章名` 分支，完成后合并回主分支
- 🧪 **实验性内容**：在独立分支上尝试新的笔记结构，不满意直接删除分支
- 👥 **多人分工**：每人使用自己的分支编辑，定期合并

### 3.8 撤销与恢复

| 命令 | 功能 | 风险等级 |
|------|------|----------|
| `Git: Discard all changes` | 丢弃所有未提交的修改 | 🔴 高（不可恢复） |
| `Git: Undo last commit` | 撤销上一次提交（保留修改为未暂存状态） | 🟡 中 |
| `Git: Restore file from revision` | 从历史版本恢复指定文件 | 🟡 中 |
| `Git: Stage current file` / `Unstage current file` | 暂存或取消暂存当前文件 | 🟢 低 |

> ⚠️ **Discard all changes 会永久丢失未提交的修改**，使用前务必确认！

### 3.9 多设备同步策略

如果你在多台设备（如台式机、笔记本、手机）上使用同一 Vault：

| 策略 | 说明 |
|------|------|
| **以远程仓库为中心** | 所有设备都从远程 Pull 最新版本，编辑后 Push 回去 |
| **设置合理的自动同步间隔** | 避免多设备同时推送造成冲突 |
| **手机端使用 Obsidian Git** | 移动端 Obsidian 也支持 Git 插件，但建议手动同步以避免意外 |
| **启用 Auto pull on boot** | 每次打开 Obsidian 自动拉取最新版本 |  

### 常用命令速查表

> 💡 **使用方式**：所有命令均通过命令面板（`Ctrl/Cmd + P`）输入关键字查找执行。以下表格按使用频率排列。

#### 📌 提交与同步

| 命令 | 快捷搜索关键字 | 功能描述 | 使用频率 |
|------|----------------|----------|----------|
| `Commit-and-sync` | `sync` | 提交所有变更 + 拉取远程 + 推送到远程（一步到位） | ⭐⭐⭐⭐⭐ |
| `Commit` | `commit` | 仅提交已暂存的文件 | ⭐⭐⭐⭐ |
| `Commit all` | `commit all` | 暂存所有变更并提交 | ⭐⭐⭐⭐ |
| `Commit staged` | `commit staged` | 仅提交已手动暂存的文件 | ⭐⭐⭐ |
| `Push` | `push` | 将本地提交推送到远程仓库 | ⭐⭐⭐⭐ |
| `Pull` | `pull` | 拉取远程仓库的最新变更 | ⭐⭐⭐⭐ |

#### 📂 文件管理

| 命令 | 功能描述 | 使用频率 |
|------|----------|----------|
| `Stage current file` | 暂存当前正在编辑的文件 | ⭐⭐⭐⭐ |
| `Unstage current file` | 取消暂存当前文件 | ⭐⭐⭐ |
| `Stage all` | 暂存所有变更文件 | ⭐⭐⭐ |
| `Add file to .gitignore` | 将当前文件添加到 .gitignore 忽略列表 | ⭐⭐⭐ |
| `List changed files` | 列出所有已修改但未提交的文件 | ⭐⭐⭐ |
| `Discard all changes` | ⚠️ 丢弃所有未提交的修改（不可恢复） | ⭐ |
| `Restore file from revision` | 从历史版本恢复指定文件 | ⭐⭐ |

#### 🔍 查看与对比

| 命令 | 功能描述 | 使用频率 |
|------|----------|----------|
| `Open source control view` | 打开源代码控制侧边栏，查看所有变更 | ⭐⭐⭐⭐⭐ |
| `Open diff view` | 打开当前文件的差异对比视图 | ⭐⭐⭐⭐ |
| `Open history view` | 查看仓库的提交日志和文件变化历史 | ⭐⭐⭐⭐ |
| `Open file on GitHub` | 在浏览器中打开当前文件的 GitHub 页面 | ⭐⭐ |
| `Open file history` | 查看当前文件的完整修改历史 | ⭐⭐⭐ |

#### 🌿 分支操作

| 命令 | 功能描述 | 使用频率 |
|------|----------|----------|
| `Create new branch` | 创建并切换到新分支 | ⭐⭐⭐ |
| `Switch branch` | 切换到已有分支 | ⭐⭐⭐ |
| `Delete branch` | 删除指定分支 | ⭐⭐ |
| `Merge branch` | 将指定分支合并到当前分支 | ⭐⭐ |
| `Edit remotes` | 编辑远程仓库地址 | ⭐⭐ |

#### 🏗️ 仓库初始化

| 命令 | 功能描述 | 使用频率 |
|------|----------|----------|
| `Initialize a new repo` | 在当前 Vault 初始化 Git 仓库 | ⭐ |
| `Clone an existing remote repo` | 克隆远程仓库到本地 | ⭐ |

#### 🛠️ 高级操作

| 命令 | 功能描述 | 使用频率 |
|------|----------|----------|
| `Undo last commit` | 撤销上一次提交（保留修改为未暂存状态） | ⭐⭐ |
| `Edit .gitignore` | 直接编辑 .gitignore 文件 | ⭐⭐ |
| `Open diff view (staged)` | 查看已暂存内容的差异 | ⭐⭐ |
| `Push backup` | 强制推送备份 | ⭐ |

---

#### 🔄 日常操作快速参考

**最简操作（适合单人使用）**：
> 打开 Obsidian → 编辑笔记 → `Ctrl/Cmd + P` → 输入 `sync` → 回车执行 Commit-and-sync → 完成 ✅

**多人协作操作**：
> 打开 Obsidian（自动 Pull）→ 编辑笔记 → 暂存文件 → 写提交消息 → Commit → Push → 完成 ✅

**查看同事修改了什么**：
> `Ctrl/Cmd + P` → 输入 `history` → 打开历史视图 → 查看最新提交记录 ✅

**误操作恢复**：
> `Ctrl/Cmd + P` → 输入 `undo commit` → 撤销上次提交 → 重新编辑后再提交 ✅

## 四、高级配置与优化

### 1. 自动同步设置

进入插件设置界面，配置以下选项：

- **配置自动提交间隔**（推荐 5-15 分钟）
- **启用自动拉取和自动推送选项**
- **设置提交信息模板**（如 "Auto commit: {{date}}"）

## 4. 安装与配置 Claudian（Claude Code）插件  

- **下载插件文件**：访问 Claudian GitHub 发布页（[github.com/YishenTu/claudian/releases/latest](https://github.com/YishenTu/claudian/releases/latest)），下载最新版本的 `main.js`、`manifest.json` 和 `styles.css` 三个文件。  
- **创建插件文件夹**：在你的 Vault 目录下，进入 `.obsidian/plugins/` 文件夹（若不存在则创建），新建一个名为 `claudian` 的子文件夹，如：`/path/to/vault/.obsidian/plugins/claudian/`。  
- **复制文件并启用插件**：将刚下载的三个文件复制到上述 `claudian` 文件夹中。然后在 Obsidian 设置的「社区插件」页中，找到并启用「Claudian」插件。启用后，Vault 根目录会自动出现一个 `.claudian` 文件夹，内部包含 `claudian-settings.json` 等配置文件。  
- **编辑配置文件**：用文本编辑器打开 `vault/.claudian/claudian-settings.json`，把内容改为：  
  ```{ "userName": "", "permissionMode": "yolo", "model": "GLM-5.1", "thinkingBudget": "off", "effortLevel": "high", "serviceTier": "default", "enableAutoTitleGeneration": true, "titleGenerationModel": "", "excludedTags": [], "mediaFolder": "", "systemPrompt": "", "persistentExternalContextPaths": [], "sharedEnvironmentVariables": "", "envSnippets": [], "customContextLimits": {}, "keyboardNavigation": { "scrollUpKey": "w", "scrollDownKey": "s", "focusInputKey": "i" }, "locale": "en", "providerConfigs": { "claude": { "safeMode": "acceptEdits", "cliPath": "", "cliPathsByHost": {}, "loadUserSettings": false, "enableChrome": false, "enableBangBash": false, "enableOpus1M": false, "enableSonnet1M": false, "lastModel": "GLM-5.1", "environmentVariables": "ANTHROPIC_BASE_URL=https://ai.ghostclaw.dev\nANTHROPIC_AUTH_TOKEN=api_key\nANTHROPIC_MODEL=GLM-5.1\nCLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1\nCLAUDE_CODE_ATTRIBUTION_HEADER=0", "environmentHash": "ANTHROPIC_BASE_URL=https://ai.ghostclaw.dev|ANTHROPIC_MODEL=GLM-5.1" }, "codex": { "enabled": false, "safeMode": "workspace-write", "cliPath": "", "cliPathsByHost": {}, "reasoningSummary": "detailed", "environmentVariables": "", "environmentHash": "", "installationMethodsByHost": { "GC-20260105155959": "native-windows" }, "wslDistroOverridesByHost": {} } }, "settingsProvider": "claude", "savedProviderModel": { "claude": "GLM-5.1" }, "savedProviderEffort": { "claude": "high" }, "savedProviderServiceTier": {}, "savedProviderThinkingBudget": { "claude": "off" }, "lastCustomModel": "GLM-5.1", "maxTabs": 3, "tabBarPosition": "input", "enableAutoScroll": true, "openInMainTab": false, "hiddenProviderCommands": {} }```

  其中api_key、所有url、所有模型ID都要替换成自己的。
  配置完成后保存文件。此文件中还包括语言、显示模式、键盘快捷键等其他 Claudian 设置，可根据需要调整。  

- **启动机器人聊天**：  
  完成配置后，打开任意笔记窗口。此时 Obsidian 左侧边栏会出现一个 **机器人图标**（Claudian 图标）。点击该图标，或在命令面板中输入 “Claudian: Open Chat” 打开聊天界面。现在你可以像在 Claude Code 终端那样与智能助理对话，选中文本后可以使用指定快捷键（默认为 `Ctrl/Cmd+Enter`）让 Claude 对选中内容进行编辑或回答。  

通过以上步骤，你已成功配置 Obsidian 的多人协作同步环境，并集成了 Claudian 插件。团队成员只需克隆同一远程仓库并开启 Obsidian Git 插件，即可实现实时同步；同时使用 Claudian，可在笔记中直接调用 Claude Code 进行智能问答和代码生成，加速知识管理和学习工作。祝你使用愉快！  
