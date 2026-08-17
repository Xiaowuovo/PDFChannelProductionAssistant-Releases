# PDF 渠道研发工作台发布包

这是“PDF 渠道研发工作台”的私有发布仓库。Release 中提供完整的一体化工作台压缩包，不需要再从其他目录单独收集 App、源码、Skill、台账或任务数据。

> 当前发布版本：`2026-channel-v87`

## 一、获取权限

这是私有仓库。使用者需要先登录 GitHub，并由仓库所有者添加为 Collaborator，才能查看和下载 Release。

## 二、第一次下载

1. 打开仓库右侧 **Releases**，进入最新版本。
2. 下载以下两个文件：
   - `PDFChannelProductionAssistantBundle-2026-channel-v87.zip`
   - `PDFChannelProductionAssistantBundle-2026-channel-v87.zip.sha256`
3. 不要只下载源码或单独复制 `skills/`；ZIP 才是完整交付物。

## 三、校验压缩包

在 PowerShell 中执行：

```powershell
Get-FileHash -Algorithm SHA256 ".\PDFChannelProductionAssistantBundle-2026-channel-v87.zip"
```

结果必须与同版本 `.sha256` 文件一致。当前 v87 的 SHA-256 为：

`67BB5F53420D1635F1BCAE9E290E1ABA31AA0B3DD193F84D3CEEA2CEC08E0D88`

哈希不一致时不要解压和运行，应重新下载。

## 四、解压和启动

1. 将 ZIP 完整解压到本机独立目录，例如：
   `D:\PDFChannelProductionAssistantBundle`
2. 不要在 ZIP 压缩包内部直接运行程序。
3. 如果本机已经运行旧版工作台，先关闭旧窗口和 `PDFChannelProductionAssistant.exe` 进程。
4. 双击解压目录中的 `启动工作台.cmd`。
5. 也可以直接启动：
   `app\PDFChannelProductionAssistant.exe`

不要同时运行原工作台和解压后的工作台；两者默认会访问同一份 `%LOCALAPPDATA%` SQLite 数据库。

## 五、首次建立渠道

1. 进入左侧 **工作台**。
2. 在“选择生产工作区”卡片中设置：
   - Reader 工作区；
   - Converter 工作区；
   - Translation 工作区（可选）；
   - Image 工作区（可选）。
3. Reader、Converter 工作区必须存在 `public\ProductIsolationHelper.h`；这里只检查入口是否存在，不扫描或修改 isolation。
4. 选择华军 3101 基线，输入新渠道号。
5. 点击“读取渠道配置”，确认工作区和渠道资料已经载入。
6. 依次处理“配置与资源”中的任务卡片：
   - 渠道配置；
   - 产品帮助文档；
   - QSS 主题；
   - RES 图标；
   - 产品与安装包品牌资源；
   - 外部 SDK 身份及后续任务。

## 六、生成并执行 Codex 任务

1. 进入需要 Codex 处理的任务卡片，补齐该卡片要求的资料。
2. 点击 **生成任务**。
3. 工作台会生成标准 `task.json`、提示词和本地状态目录。
4. 将工作台提供的提示词交给 Codex 客户端。
5. Codex 必须使用包内：
   `skills/run-channel-workbench-task/SKILL.md`
6. Codex 按 `task.json.required_skills` 调用包内其他 Skill，并原子写回：
   - `status.json`
   - `events.jsonl`
   - `result.json`
   - `evidence/`
7. 工作台会以轻量方式同步阶段、进度、阻断、证据和结果，不需要持续刷新整个页面。
8. Codex 完成后进入“待验收”；只有用户在工作台确认后才成为“已完成”。

任务事实目录：

`app\data\渠道项目\channel-<渠道号>\tasks\<task-id>\`

不要直接修改 SQLite 代替任务状态写回。

## 七、EasyClaw 本地应用接入

压缩包内已经提供：

- `easyclaw/LOCAL_APP_PROMPT.md`
- `easyclaw/IMPORT_INSTRUCTIONS.md`

在 EasyClaw 的“本地应用”中上传完整 ZIP，并粘贴对应提示词。EasyClaw 必须先下载 ZIP、校验外部 SHA-256、完整解压并校验内部 `checksums.sha256`，然后才能启动 App 或加载 Skill。

不要让 EasyClaw 重新开发工作台、反编译网关或继续使用以前散落的 Skill 副本。

## 八、数据说明

- `app/data/渠道项目`：渠道任务、提示词、状态、结果和证据。
- `state/channel-workbench.sqlite3`：生成发布包时取得的一致性数据库快照。
- `source/`：工作台权威源码、Web、Schema、台账和发布脚本。
- `skills/`：工作台依赖的 7 个完整业务 Skill。

SQLite 快照只用于需要迁移历史渠道记录的情况。恢复前必须关闭目标 App，并保留目标机器原数据库；App 运行时禁止覆盖数据库。

## 九、升级新版本

1. 关闭当前工作台。
2. 从 Releases 下载新版本 ZIP 和对应 SHA-256。
3. 完成哈希校验。
4. 解压到新的独立目录，不要把新旧版本文件混合覆盖。
5. 启动新版本并确认左下角版本号。

渠道业务记录默认保存在 `%LOCALAPPDATA%\PDFChannelProductionAssistant`，升级前不要删除该目录。
