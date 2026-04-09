# Obsidian 多人协同笔记系统配置与 Claude Code 使用指南

## 1. 安装 Obsidian 与 Git 插件  
- **下载 Obsidian**：访问 Obsidian 官网（[obsidian.md/download](https://obsidian.md/download)）下载适合你系统的安装包，安装并运行 Obsidian。  
- **创建或打开笔记库（Vault）**：首次运行时可创建一个新 Vault，或打开已有文件夹作为 Vault。Obsidian 会将此 Vault 作为知识库目录。  
- **启用社区插件**：在 Obsidian 左下角点击设置，选择「社区插件」，先关闭安全模式（若提示开启），然后点击「浏览」，搜索并安装 **Obsidian Git** 插件。安装后启用该插件。  

## 2. 基础设置

完成以下三步配置后，你将获得**无感同步体验**——每次打开 Obsidian 自动拉取最新内容，编辑后自动提交并推送到远程，全程无需手动操作。

### 步骤一：配置个人信息

在 Obsidian Git 插件设置（设置 → 社区插件 → Git → ⚙️）中填写你的 **用户名** 和 **邮箱**，或在 Vault 根目录打开终端执行：

```bash
git config user.name "你的名字"
git config user.email "你的邮箱@example.com"
```

> 这决定了提交记录上显示的作者信息，方便团队成员识别谁做了哪些修改。

### 步骤二：关联远程仓库

1. 在 [GitHub](https://github.com/new) / [GitLab](https://gitlab.com/projects/new) / [Gitee](https://gitee.com/projects/new) 新建一个仓库（建议设为 **Private**）
2. 记下仓库地址（HTTPS 或 SSH）
3. 在 Obsidian 中打开命令面板（`Ctrl/Cmd + P`），执行 `Git: Edit remotes`，添加远程地址

> **新成员加入**：直接执行 `Git: Clone an existing remote repo`，输入仓库 URL，克隆后用 Obsidian 打开即可。

### 步骤三：配置自动同步

在 Obsidian Git 插件设置中，调整以下关键选项：

| 设置项 | 推荐值 | 作用 |
|--------|--------|------|
| **Auto save interval** | `5` - `10` 分钟 | 每隔多久自动提交一次 |
| **Auto push interval** | `10` - `20` 分钟 | 每隔多久自动推送到远程 |
| **Auto pull interval** | `10` - `20` 分钟 | 每隔多久自动拉取远程更新 |
| **Auto pull on boot** | ✅ 启用 | 每次 Obsidian 启动时自动拉取最新内容 |
| **Pull before push** | ✅ 启用 | 推送前先拉取，避免冲突 |
| **Commit message** | `vault backup: {{date}}` | 自动提交的默认消息 |
| **Commit date format** | `YYYY-MM-DD HH:mm:ss` | 日期格式 |
| **Sync method** | `merge` | 同步策略（保持默认即可） |

> ✅ **配完即忘**：以上设置完成后，你的工作流将变成：**打开 Obsidian → 编辑笔记 → 关闭 Obsidian**，同步全自动在后台完成，无需任何额外操作。

## 3. 安装与配置 Claudian（Claude Code）插件  

- **下载插件文件**：访问 Claudian GitHub 发布页（[github.com/YishenTu/claudian/releases/latest](https://github.com/YishenTu/claudian/releases/latest)），下载最新版本的 `main.js`、`manifest.json` 和 `styles.css` 三个文件。  
- **创建插件文件夹**：在你的 Vault 目录下，进入 `.obsidian/plugins/` 文件夹（若不存在则创建），新建一个名为 `claudian` 的子文件夹，如：`/path/to/vault/.obsidian/plugins/claudian/`。  
- **复制文件并启用插件**：将刚下载的三个文件复制到上述 `claudian` 文件夹中。然后在 Obsidian 设置的「社区插件」页中，找到并启用「Claudian」插件。启用后，Vault 根目录会自动出现一个 `.claudian` 文件夹，内部包含 `claudian-settings.json` 等配置文件。  
- **编辑配置文件**：用文本编辑器打开 `vault/.claudian/claudian-settings.json`，把内容改为：  
  ```{ "userName": "", "permissionMode": "yolo", "model": "GLM-5.1", "thinkingBudget": "off", "effortLevel": "high", "serviceTier": "default", "enableAutoTitleGeneration": true, "titleGenerationModel": "", "excludedTags": [], "mediaFolder": "", "systemPrompt": "", "persistentExternalContextPaths": [], "sharedEnvironmentVariables": "", "envSnippets": [], "customContextLimits": {}, "keyboardNavigation": { "scrollUpKey": "w", "scrollDownKey": "s", "focusInputKey": "i" }, "locale": "en", "providerConfigs": { "claude": { "safeMode": "acceptEdits", "cliPath": "", "cliPathsByHost": {}, "loadUserSettings": false, "enableChrome": false, "enableBangBash": false, "enableOpus1M": false, "enableSonnet1M": false, "lastModel": "GLM-5.1", "environmentVariables": "ANTHROPIC_BASE_URL=https://ai.ghostclaw.dev\nANTHROPIC_AUTH_TOKEN=api_key\nANTHROPIC_MODEL=GLM-5.1\nCLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1\nCLAUDE_CODE_ATTRIBUTION_HEADER=0", "environmentHash": "ANTHROPIC_BASE_URL=https://ai.ghostclaw.dev|ANTHROPIC_MODEL=GLM-5.1" }, "codex": { "enabled": false, "safeMode": "workspace-write", "cliPath": "", "cliPathsByHost": {}, "reasoningSummary": "detailed", "environmentVariables": "", "environmentHash": "", "installationMethodsByHost": { "GC-20260105155959": "native-windows" }, "wslDistroOverridesByHost": {} } }, "settingsProvider": "claude", "savedProviderModel": { "claude": "GLM-5.1" }, "savedProviderEffort": { "claude": "high" }, "savedProviderServiceTier": {}, "savedProviderThinkingBudget": { "claude": "off" }, "lastCustomModel": "GLM-5.1", "maxTabs": 3, "tabBarPosition": "input", "enableAutoScroll": true, "openInMainTab": false, "hiddenProviderCommands": {} }```

  其中api_key、所有url、所有模型ID都要替换成自己的。
  配置完成后保存文件。此文件中还包括语言、显示模式、键盘快捷键等其他 Claudian 设置，可根据需要调整。  

- **启动机器人聊天**：  
  完成配置后，打开任意笔记窗口。此时 Obsidian 左侧边栏会出现一个 **机器人图标**（Claudian 图标）。点击该图标，或在命令面板中输入 "Claudian: Open Chat" 打开聊天界面。现在你可以像在 Claude Code 终端那样与智能助理对话，选中文本后可以使用指定快捷键（默认为 `Ctrl/Cmd+Enter`）让 Claude 对选中内容进行编辑或回答。  

通过以上步骤，你已成功配置 Obsidian 的多人协作同步环境，并集成了 Claudian 插件。团队成员只需克隆同一远程仓库并开启 Obsidian Git 插件，即可实现实时同步；同时使用 Claudian，可在笔记中直接调用 Claude Code 进行智能问答和代码生成，加速知识管理和学习工作。祝你使用愉快！  

---

# 附录

> 以下是 Git 仓库配置、Obsidian Git 插件使用及高级配置的详细参考内容。日常使用只需完成正文的「基础设置」即可，遇到具体问题时再查阅本附录。

## A. Git 仓库详细配置

### A.1 初始化本地仓库

**方式一：通过 Obsidian Git 插件（推荐）**
1. 打开命令面板（`Ctrl/Cmd + P`）
2. 输入并执行 `Git: Initialize a new repo`
3. 插件会自动在当前 Vault 根目录创建 `.git` 文件夹

**方式二：通过命令行**
```bash
cd /path/to/your/vault
git init
```

### A.2 配置 `.gitignore` 文件

Vault 中并非所有文件都需要纳入版本控制。在 Vault 根目录创建 `.gitignore` 文件：

```gitignore
# Obsidian 个人状态文件（不需要同步）
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
```

> 💡 如果团队希望共享插件配置，可以将 `.obsidian/plugins/` 保留在版本控制中，但应排除 `workspace.json` 和各插件的 `data.json`。

### A.3 配置 SSH 密钥（免密推送）

使用 SSH 地址时，配置密钥可避免每次推送输入密码：

```bash
# 1. 生成 SSH 密钥
ssh-keygen -t ed25519 -C "你的邮箱@example.com"

# 2. 启动 SSH Agent 并添加密钥
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 3. 复制公钥，添加到 GitHub → Settings → SSH and GPG keys → New SSH key
cat ~/.ssh/id_ed25519.pub

# 4. 测试连接
ssh -T git@github.com
```

> 🔒 Windows：`clip < ~/.ssh/id_ed25519.pub` 复制公钥；macOS：`pbcopy < ~/.ssh/id_ed25519.pub`。

### A.4 连接远程仓库的两种方式

**已有本地 Vault，连接远程空仓库**：
```bash
git remote add origin https://github.com/用户名/仓库名.git
git push -u origin main
```

**新成员克隆远程仓库**：
```bash
git clone https://github.com/用户名/仓库名.git
# 然后在 Obsidian 中打开克隆下来的文件夹
```

### A.5 HTTPS 凭证管理

```bash
# Windows（Git for Windows 自带）
git config --global credential.helper manager

# macOS（使用钥匙串）
git config --global credential.helper osxkeychain

# 或使用 GitHub CLI
gh auth login
```

## B. Obsidian Git 插件使用详解

### B.1 日常工作流

```
打开 Obsidian → 自动拉取（Auto Pull on Boot）
      ↓
编辑笔记（自动保存触发自动提交）
      ↓
定期自动推送（Auto Push）或手动 Commit-and-sync
      ↓
关闭 Obsidian 前确认同步状态
```

### B.2 源代码控制视图

**打开方式**：命令面板（`Ctrl/Cmd + P`）→ `Git: Open source control view`，或点击左侧边栏源代码控制图标。

| 区域 | 作用 |
|------|------|
| **Changes** | 已修改但未暂存的文件 |
| **Staged** | 已暂存、准备提交的文件 |
| **Untracked** | 新创建但未被 Git 管理的文件 |

**单文件操作**：➕ 暂存 / ➖ 取消暂存 / ↩️ 丢弃修改 / 右键 → Add to .gitignore

### B.3 提交命令对比

| 命令 | 适用场景 |
|------|----------|
| `Commit-and-sync` | **最常用** — 提交 + 拉取 + 推送一步完成 |
| `Commit all` | 自动暂存所有变更并提交 |
| `Commit` | 仅提交已手动暂存的文件 |
| `Commit staged` | 仅提交已暂存的文件 |

### B.4 查看历史与差异

- **历史视图**：`Git: Open history view` — 查看所有提交记录
- **差异视图**：`Git: Open diff view` — 对比当前文件与最近版本的差异（红色=删除，绿色=新增）
- **行内标记**：编辑器左侧 Gutter 区域实时显示行级修改标记，可暂存或撤销单行变更

### B.5 冲突处理

多人同时编辑同一文件时可能产生冲突。冲突文件中会出现：

```markdown
<<<<<<< HEAD
你的修改内容
=======
对方的修改内容
>>>>>>> origin/main
```

**解决步骤**：
1. 打开冲突文件，找到冲突标记
2. 决定保留哪一方的修改（或手动合并）
3. 删除冲突标记（`<<<<<<<`、`=======`、`>>>>>>>` 三行）
4. 保存文件 → 暂存 → `Git: Commit` → `Git: Push`

> ⚠️ **避免冲突**：频繁拉取、避免多人同时编辑同一文件、编辑前先 Pull。

### B.6 分支操作

| 命令 | 功能 |
|------|------|
| `Git: Create new branch` | 创建新分支 |
| `Git: Switch branch` | 切换到已有分支 |
| `Git: Delete branch` | 删除分支 |
| `Git: Merge branch` | 将指定分支合并到当前分支 |

**典型场景**：📝 撰写长文用独立分支 → 完成后合并；🧪 实验性内容用分支 → 不满意直接删除。

### B.7 撤销与恢复

| 命令 | 风险 | 说明 |
|------|------|------|
| `Discard all changes` | 🔴 高 | 永久丢失未提交的修改 |
| `Undo last commit` | 🟡 中 | 撤销上次提交，保留修改为未暂存 |
| `Restore file from revision` | 🟡 中 | 从历史版本恢复指定文件 |

### B.8 多设备同步策略

| 策略 | 说明 |
|------|------|
| 以远程仓库为中心 | 所有设备 Pull → 编辑 → Push |
| 合理设置同步间隔 | 避免多设备同时推送造成冲突 |
| 手机端手动同步 | 移动端支持 Git 插件，但建议手动操作 |
| 启用 Auto pull on boot | 每次打开自动拉取最新版本 |

### B.9 常用命令速查表

> 💡 所有命令通过命令面板（`Ctrl/Cmd + P`）输入关键字查找执行。

#### 📌 提交与同步

| 命令 | 快捷搜索 | 功能 | 频率 |
|------|----------|------|------|
| `Commit-and-sync` | `sync` | 提交 + 拉取 + 推送一步到位 | ⭐⭐⭐⭐⭐ |
| `Commit` | `commit` | 仅提交已暂存文件 | ⭐⭐⭐⭐ |
| `Commit all` | `commit all` | 暂存所有变更并提交 | ⭐⭐⭐⭐ |
| `Push` | `push` | 推送到远程 | ⭐⭐⭐⭐ |
| `Pull` | `pull` | 拉取远程更新 | ⭐⭐⭐⭐ |

#### 📂 文件管理

| 命令 | 功能 | 频率 |
|------|------|------|
| `Stage current file` | 暂存当前文件 | ⭐⭐⭐⭐ |
| `Unstage current file` | 取消暂存 | ⭐⭐⭐ |
| `Stage all` | 暂存所有变更 | ⭐⭐⭐ |
| `Add file to .gitignore` | 添加到忽略列表 | ⭐⭐⭐ |
| `List changed files` | 列出已修改文件 | ⭐⭐⭐ |
| `Discard all changes` | ⚠️ 丢弃所有未提交修改 | ⭐ |

#### 🔍 查看与对比

| 命令 | 功能 | 频率 |
|------|------|------|
| `Open source control view` | 打开源代码控制侧边栏 | ⭐⭐⭐⭐⭐ |
| `Open diff view` | 打开差异对比视图 | ⭐⭐⭐⭐ |
| `Open history view` | 查看提交日志 | ⭐⭐⭐⭐ |
| `Open file history` | 查看当前文件修改历史 | ⭐⭐⭐ |
| `Open file on GitHub` | 在浏览器打开 GitHub 页面 | ⭐⭐ |

#### 🌿 分支操作

| 命令 | 功能 | 频率 |
|------|------|------|
| `Create new branch` | 创建新分支 | ⭐⭐⭐ |
| `Switch branch` | 切换分支 | ⭐⭐⭐ |
| `Delete branch` | 删除分支 | ⭐⭐ |
| `Merge branch` | 合并分支 | ⭐⭐ |
| `Edit remotes` | 编辑远程仓库地址 | ⭐⭐ |

#### 🏗️ 仓库初始化

| 命令 | 功能 | 频率 |
|------|------|------|
| `Initialize a new repo` | 初始化 Git 仓库 | ⭐ |
| `Clone an existing remote repo` | 克隆远程仓库 | ⭐ |

#### 🛠️ 高级操作

| 命令 | 功能 | 频率 |
|------|------|------|
| `Undo last commit` | 撤销上次提交 | ⭐⭐ |
| `Edit .gitignore` | 编辑 .gitignore | ⭐⭐ |
| `Open diff view (staged)` | 查看已暂存差异 | ⭐⭐ |
| `Push backup` | 强制推送备份 | ⭐ |

#### 🔄 日常操作快速参考

**最简操作（单人）**：
> 打开 Obsidian → 编辑笔记 → `Ctrl/Cmd + P` → `sync` → 回车 ✅

**多人协作**：
> 打开 Obsidian（自动 Pull）→ 编辑 → Commit → Push ✅

**查看同事修改**：
> `Ctrl/Cmd + P` → `history` → 查看最新提交记录 ✅

**误操作恢复**：
> `Ctrl/Cmd + P` → `undo commit` → 撤销上次提交 ✅

## C. 高级配置与优化

### C.1 自动同步详细设置

进入插件设置界面（设置 → 社区插件 → Git → ⚙️），可调整：

- **自动提交间隔**（推荐 5-15 分钟）
- **自动推送间隔**（推荐 10-30 分钟）
- **自动拉取间隔**（推荐 10-30 分钟）
- **启用 Auto pull on boot**（启动时自动拉取）
- **启用 Pull before push**（推送前拉取，防冲突）
- **提交信息模板**（如 `vault backup: {{date}}`）

### C.2 行内修改标记

在插件设置中启用 Signs in Editor 后，编辑器左侧 Gutter 区域会实时显示行级修改标记：
- 🟢 绿色 = 新增行
- 🔵 蓝色 = 修改行
- 🔴 红色 = 删除行

可在标记上直接暂存单行变更（Stage hunk）或撤销修改（Reset hunk）。

### C.3 差异视图模式

插件设置中可切换差异视图的显示模式：
- **Split（分屏）**：左右并排显示新旧版本
- **Unified（统一）**：在同一个视图中用颜色标注差异
