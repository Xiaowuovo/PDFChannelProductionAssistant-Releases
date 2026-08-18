# PDF 渠道研发工作台发布包

这是公开的一体化工作台总包。App、源码、Skill、台账和任务数据都在同一个 ZIP 中。以后始终覆盖同一份 Release 资产，下载链接不变。

## 一、固定下载

- 总包：[PDFChannelProductionAssistantBundle.zip](https://github.com/Xiaowuovo/PDFChannelProductionAssistant-Releases/releases/download/latest/PDFChannelProductionAssistantBundle.zip)
- 校验文件：[PDFChannelProductionAssistantBundle.zip.sha256](https://github.com/Xiaowuovo/PDFChannelProductionAssistant-Releases/releases/download/latest/PDFChannelProductionAssistantBundle.zip.sha256)
- Release 页面：[latest](https://github.com/Xiaowuovo/PDFChannelProductionAssistant-Releases/releases/tag/latest)

不要下载 GitHub 自动生成的 Source code 压缩包作为应用包。

## 二、校验压缩包

在 PowerShell 中执行：

```powershell
Get-FileHash -Algorithm SHA256 ".\PDFChannelProductionAssistantBundle.zip"
```

结果必须与 `.sha256` 文件一致；不一致时不要解压和运行。

## 三、解压和启动

1. 将 ZIP 完整解压到独立目录，例如 `D:\PDFChannelProductionAssistantBundle`。
2. 不要在 ZIP 内直接运行。
3. 关闭已经启动的旧工作台窗口和进程。
4. 双击 `启动工作台.cmd`，也可以直接运行 `app\PDFChannelProductionAssistant.exe`。

## 四、首次配置

1. 打开左侧“工作台”。
2. 设置 Reader、Converter 工作区，以及可选的 Translation、Image 工作区。
3. Reader、Converter 必须存在 `public\ProductIsolationHelper.h`；工作台只检查入口，不扫描或修改 isolation。
4. 选择华军 3101 基线，输入新渠道号并读取渠道配置。
5. 依次处理渠道配置、产品帮助文档、QSS 主题、RES 图标、产品与安装包品牌资源、外部 SDK 身份等卡片。

## 五、生成并执行 Agent 任务

1. 打开需要 Agent 处理的卡片并补齐资料。
2. 点击“生成任务”，工作台生成 `task.json`、提示词和本地状态目录。
3. 将提示词交给能够访问本机文件并读取 Skill 的 Agent。
4. Agent 使用 `skills/run-channel-workbench-task/SKILL.md`，按 `task.json.required_skills` 执行业务 Skill。
5. Agent 原子写回 `status.json`、`events.jsonl`、`result.json` 和 `evidence/`。
6. 工作台轻量同步阶段、进度、阻断、证据与结果；Agent 完成后进入“待验收”，由用户确认完成。

任务事实目录：`app\data\渠道项目\channel-<渠道号>\tasks\<task-id>\`。不要直接修改 SQLite 代替任务状态写回。

## 六、EasyClaw 接入

压缩包提供 `easyclaw/LOCAL_APP_PROMPT.md` 和 `easyclaw/IMPORT_INSTRUCTIONS.md`。在 EasyClaw 的“本地应用”中使用固定 ZIP；必须先下载、校验外部 SHA-256、完整解压并校验内部 `checksums.sha256`，再启动 App 或加载 Skill。

## 七、数据与升级

- `app/data/渠道项目`：渠道任务、提示词、状态、结果和证据。
- `state/channel-workbench.sqlite3`：一致性数据库快照。
- `source/`：权威源码、Web、Schema、台账和发布脚本。
- `skills/`：完整业务 Skill。

升级时关闭当前工作台，重新下载固定总包并校验，完整解压后启动。渠道业务记录默认位于 `%LOCALAPPDATA%\PDFChannelProductionAssistant`，不要删除该目录。
