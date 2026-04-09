# Obsidian 多人协同笔记系统配置与 Claude Code 使用指南

## 1. 安装 Obsidian 与 Git 插件  
- **下载 Obsidian**：访问 Obsidian 官网（[obsidian.md/download](https://obsidian.md/download)）下载适合你系统的安装包，安装并运行 Obsidian。  
- **创建或打开笔记库（Vault）**：首次运行时可创建一个新 Vault，或打开已有文件夹作为 Vault。Obsidian 会将此 Vault 作为知识库目录。  
- **启用社区插件**：在 Obsidian 左下角点击设置，选择「社区插件」，先关闭安全模式（若提示开启），然后点击「浏览」，搜索并安装 **Obsidian Git** 插件。安装后启用该插件。  

## 2. 配置 Git 仓库与远程同步  
- **创建远程仓库**：在 GitHub/GitLab 等平台新建仓库，用于保存和同步 Obsidian Vault。记下仓库的 HTTPS 或 SSH 地址。  
- **初始化本地仓库**：在 Obsidian Vault 根目录里使用 Git。在 Obsidian 内可打开命令面板（Ctrl/Cmd+P），执行 `Git: Initialize a new repo`（初始化新仓库）命令。或者在命令行运行 `git init`。这样 Vault 文件夹即成为一个 Git 仓库。  
- **设置用户信息**：在 Git 插件设置页输入你的用户名和邮箱，或者在终端使用 `git config user.name "你的名字"` 和 `git config user.email "邮箱"` 设置，以确保提交记录带有正确作者信息。  
- **连接远程仓库**：使用 Obsidian Git 插件的命令将本地仓库与远程关联：打开命令面板，选择 `Git: Edit remotes` 添加远程地址；或者运行 `Git: Clone an existing remote repo` 命令，输入仓库 URL 和凭证，直接克隆远程仓库。克隆后在 Obsidian 中打开该 Vault。  

> 注：也可以先在终端用 `git remote add origin [URL]` 添加远程仓库地址，然后在插件设置中启用“Auto pull on startup”自动拉取。

- **配置自动同步**：在 Obsidian Git 插件设置中，可以启用 **自动提交与同步** 功能。插件支持按计划自动执行“提交+拉取+推送”，也能在 Obsidian 启动时自动拉取远程更新。这样，多人编辑时可保持各端数据同步。  

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
