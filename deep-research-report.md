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

- **源代码控制视图**：在侧边栏中打开 Git 的「源代码控制视图」可以实时查看已修改的文件。在此界面，你可以对单个文件点击「Stage current file」（暂存文件）或「Unstage current file」（取消暂存）。也可以使用命令面板 (`Ctrl/Cmd+P`) 输入 `Git: Open source control view` 打开此视图。  
- **提交（Commit）**：选定文件后，在命令面板选择 `Commit`（提交）或 `Commit all changes`（提交所有已暂存改动）。提交时可添加自定义消息，也可以使用 `Commit-and-sync` 命令一次性提交并同步（相当于提交+拉取+推送）。例如，执行 `Commit-and-sync` 会把所有改动提交到本地，然后自动拉取远端更新并推送自己更改。  
- **查看历史与差异**：可在命令面板选择 `Open history view` 打开「历史视图」，查看仓库提交日志和文件变化。若要对比文件版本，选择 `Open diff view` 可打开「差异视图」，直观显示文本修改情况。下图示例展示了文件编辑前后的对比。  
- **推送和拉取**：编辑并提交后，使用命令面板的 `Push` 将更改推送到远程仓库，其他协作成员才能看到更新。同样，`Pull` 命令可拉取最新远程更改到本地。如果多人同时修改同一文件，拉取时需处理可能出现的冲突。通常流程为：先 `Commit`，再 `Push`；在打开 Obsidian 时可自动拉取远程最新版本。  
- **常用命令摘要**：Obsidian Git 插件提供丰富命令，可在「命令面板」中查找使用。常用命令如：`List changed files`（列出所有改动）、`Stage current file`/`Unstage current file`（暂存/取消暂存文件）、`Commit`（提交）系列命令、`Commit-and-sync`、`Push`、`Pull`、`Clone an existing remote repo`、`Initialize a new repo` 等。此外，插件支持行内修改标记（Signs in the Editor）功能，在编辑器左侧显示新增/修改/删除行的标记，可直接在编辑界面对变更进行跳转、暂存或重置。  

### 常用命令速查表

| 命令分类 | 关键命令                          | 功能描述             |
| ---- | ----------------------------- | ---------------- |
| 基础操作 | Commit-and-sync               | 提交所有变更并同步到远程     |
|      | Open diff view                | 打开当前文件的差异视图      |
|      | Open source control view      | 打开源代码控制侧边栏       |
| 文件管理 | Stage current file            | 暂存当前文件           |
|      | Add file to .gitignore        | 将当前文件添加到忽略列表     |
| 远程操作 | Push/Pull                     | 推送 / 拉取远程变更      |
|      | Clone an existing remote repo | 克隆远程仓库           |
| 高级功能 | Create new branch             | 创建新分支            |
|      | Open file on GitHub           | 在 GitHub 中打开当前文件 |

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
