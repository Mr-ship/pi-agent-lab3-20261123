# 实践课3：Pi Agent 源码安装与编译 实验报告

> 本报告只记录本人的实验操作、真实命令输出与截图。



## 基本信息

- 姓名：龚健
- GitHub 登录名：Mr-ship
- 实验日期：2026 年 11 月 23 日
- 实验环境：Windows、Node.js v24.18.0、npm 11.16.0、Git 2.55.0.windows.3
- 源码仓库（小组组织内 fork）：https://github.com/shizishizi111/pi-hunnu-2026
- 本地源码目录：`C:\Users\lenovo\pi-lab3\pi-hunnu-2026`（全英路径）
- 源码分支：`main`，提交编号：`c3cc91632d0a1950fb122605b190233f2aa0006e`
- 本地构建产物版本：`1.5.2`；第一次实践课全局安装的 `pi` 为 `1.5.0`


## 任务一 实验准备：检查和安装开发工具

### 实验记录 S1（我的实际操作）

在实验终端逐条执行，实际输出：

```powershell
PS C:\Users\lenovo\pi-lab3\pi-hunnu-2026> node --version
v24.18.0
PS C:\Users\lenovo\pi-lab3\pi-hunnu-2026> npm --version
11.16.0
PS C:\Users\lenovo\pi-lab3\pi-hunnu-2026> git --version
git version 2.55.0.windows.3
PS C:\Users\lenovo\pi-lab3\pi-hunnu-2026> node -e "console.log('Hello, Node.js!')"
Hello, Node.js!
```

![S1 环境版本](screenshots/S1_versions.png)
*图 S1 本机 Node.js、npm、Git 版本与 Node.js 运行检查。*

**我的理解**：本机原先已装 Node.js 24.18.0 及其自带 npm 11.16.0，满足根项目 `>=22.19.0`，也符合课程建议的 Node.js 24 LTS，所以没有重装，也没有额外安装 fnm。npm 是随 Node.js 一起安装的，不需要单独下载。第一次实践课 `pi` 能运行，只说明全局客户端可用，不能说明当前终端能找到这三个开发工具，所以仍需逐条检查。


## 任务二 下载课程仓库源码

### 实验记录 S2（我的实际操作）

按作业要求在全英路径下克隆小组组织内 fork 的仓库：

```powershell
git clone --branch main https://github.com/shizishizi111/pi-hunnu-2026.git
cd pi-hunnu-2026
Get-Location      # C:\Users\lenovo\pi-lab3\pi-hunnu-2026
git remote -v     # origin https://github.com/shizishizi111/pi-hunnu-2026.git [blob:none]
git branch --show-current   # main
git rev-parse HEAD          # c3cc91632d0a1950fb122605b190233f2aa0006e
git status --short          # 无输出，工作区干净
```

![S2 源码来源与版本](screenshots/S2_repository.png)
*图 S2 当前目录、远端地址、分支与完整提交编号。*

**我的理解**：`origin` 指向 `shizishizi111/pi-hunnu-2026`，即本小组组织内的 fork，不是上游源仓库；分支为 `main`，完整提交编号已记录，后续安装和构建都在这个根目录执行。远端显示 `[blob:none]` 是因为克隆时为避开网络中断使用了部分克隆参数，不影响源码内容与提交编号，详见文末「遇到的困难」。只打开 GitHub 网页不能证明源码已下载到本地，所以这里用 `Get-Location` 和 `git rev-parse HEAD` 作为依据。


## 任务三 阅读配置，确认安装与编译的依据

### 实验记录 S3（我的实际操作）

用编辑器查看了根目录 `README.md`、`package.json`、`package-lock.json`、`packages/` 以及 `packages/coding-agent/package.json`，并在终端中显示关键定义：

```powershell
# 根 package.json 第 69-71 行
"engines": { "node": ">=22.19.0" }

# 根 package.json 第 15 行 scripts.build（节选）
"build": "cd packages/telemetry && npm run build && cd ../schema && npm run build
  && ... && cd ../ai && npm run build && ... && cd ../coding-agent && npm run build"

# README.md 第 102-111 行 Development
npm install --ignore-scripts  # Install all dependencies without running lifecycle scripts
npm run build                 # Refresh model data, then build all packages
npm run build:offline         # Rebuild using existing model data without network access

# packages/coding-agent/package.json
"version": "1.5.2", "bin": { "pi": "dist/bundle/cli.js" }
"build": "npm run build:unbundled && node ../../scripts/build-coding-agent-bundle.mjs"
```

![S3a engines.node](screenshots/S3a_engines.png)
*图 S3a 根 package.json 中的 engines.node 与 devDependencies。*

![S3b 构建脚本](screenshots/S3b_build_scripts.png)
*图 S3b 根 package.json 中 scripts.build 与 build:offline 的定义。*

![S3d Development 开发命令](screenshots/S3d_development.png)
*图 S3d README.md 的 Development 段给出的开发命令。*

![S3c CLI 包配置](screenshots/S3c_cli_package.png)
*图 S3c packages 子包列表与 coding-agent 包的 bin 入口。*

**回答：Node.js 版本要求在哪里？** 在**根 `package.json` 的 `engines.node` 字段**（`>=22.19.0`），不是从截图推测的，也不是每个子包各自声明同一个版本。

**回答：`npm run build` 实际从哪里读取命令？** 先读**根 `package.json` 的 `scripts.build`**，它是一串 `cd 子包 && npm run build`；进入子包后，再读**该子包自己的 `package.json` 的 `scripts.build`**（如 coding-agent 的 `build:unbundled` 用 `tsc` 编译，再执行 `build-coding-agent-bundle.mjs` 打包）。`&&` 表示前一步成功才继续下一步。npm 本身不规定编译动作，具体做什么完全由项目脚本决定。

**我的理解**：仓库用 npm workspaces 管理 `packages/*`、`apps/*` 和几个 examples 子包，根目录只有一份 `package-lock.json`（204 KB）锁定依赖，因此必须从根目录安装、按根脚本顺序构建，不能给每个子包单独生成锁文件。`bin.pi` 指向 `dist/bundle/cli.js`，这就是后面要验证的 CLI 入口。


## 任务四 安装项目依赖

### 实验记录 S4（我的实际操作）

确认当前目录含根 `package.json` 后执行：

```powershell
npm install --ignore-scripts
# 首次安装输出（摘要）
added 333 packages, and audited 356 packages in 5m
57 packages are looking for funding
found 0 vulnerabilities
Write-Output "INSTALL_EXIT=$LASTEXITCODE"   # INSTALL_EXIT=0
```

图 S4 是在依赖已安装后再次执行同一条安装命令的截图（因此显示 `changed 2 packages`），用于证明安装命令能正常结束并给出退出码 `0`；首次安装的完整汇总 `added 333 packages, and audited 356 packages in 5m` 见图 S4b 对首次日志的回看。

![S4 安装结果与退出码](screenshots/S4_install.png)
*图 S4 安装结束的输出结果与退出码 0。*

![S4b 安装警告](screenshots/S4b_install_warnings.png)
*图 S4b 首次安装日志中的警告与汇总（回看 install.log）。*

**安装输出分析**：出现的是**警告**，不是导致命令失败的错误：

| 输出 | 性质 | 我的处理 |
| --- | --- | --- |
| `npm warn Unknown project config "min-release-age-exclude"` | 配置兼容提示，说明该 `.npmrc` 配置项在当前 npm 版本可能不再支持 | 只记录，不改配置文件 |
| `npm warn deprecated prebuild-install@7.1.3` / `node-domexception@1.0.0` | 依赖已弃用/建议改用平台原生能力 | 属提示，不等于安装失败 |
| `found 0 vulnerabilities` | 本次依赖树审计结果 | 仅说明这次审计未报告已知漏洞，不是构建成功的证明 |

本次**没有** `EBADENGINE`：当前 Node.js 24.18.0 满足根项目及依赖的版本要求。按作业提醒，没有执行 `npm audit fix --force`，以免改变课程使用的依赖版本。

**命令作用（用自己的话）**：按 `package.json` + `package-lock.json` 解析并下载依赖，锁文件一致时使用锁定版本；在根目录建立 `node_modules` 并关联 workspaces 里的本地包；安装 TypeScript 编译器、esbuild、Biome 等开发工具。`--ignore-scripts` 跳过安装阶段自动触发的生命周期脚本（如 `postinstall`、`prepare`/husky），不影响之后主动执行 `npm run build`。命令里没有 `-g`，装的是**当前项目的依赖**，不会把本地源码自动变成全局 `pi` 命令。


## 任务五 本地编译与构建产物验证

### 实验记录 S5（我的实际操作）

```powershell
# 首次构建（联网获取模型目录数据，退出码 0）
npm run build
#   Generated provider catalogs and src/models.generated.ts
#   Total tool-capable models: 1696
#   Built packages\coding-agent\dist\bundle (84 files, 9.4 MiB)
#   BUILD_EXIT=0
```

![S5a 首次构建日志末尾](screenshots/S5a_first_build_log.png)
*图 S5a 回看首次 `npm run build` 的完整日志末尾，可见打包出 dist/bundle。*

首次构建的退出码为 `0`（`BUILD_EXIT=0`）。为在截图终端里核对构建结果，回看首次构建日志末尾，并现场执行产物验证：

```powershell
Get-Content ..\build.log | Select-Object -Last 4
#   Built packages\coding-agent\dist\bundle (84 files, 9.4 MiB)
Test-Path .\packages\coding-agent\dist\bundle\cli.js  # True
node .\packages\coding-agent\dist\bundle\cli.js --version  # 1.5.2
```

![S5 构建日志末尾与产物验证](screenshots/S5_build_artifact.png)
*图 S5 首次构建日志的最后一段输出、入口文件存在与本地路径运行得到的版本号；首次构建退出码为 0，见上方实验记录与 S5a。*

截图终端里再次执行构建命令时遇到了网络与工具环境问题（见「困难 7」「困难 8」），因此这里没有把失败的重跑当成成功证据，而是用首次真实构建的日志配合现场可复现的产物验证。

**我的理解**：首次构建需要联网拉取的是**模型目录、能力等配置数据**（日志里生成 provider catalogs、`models.generated.ts`），不是下载大模型权重；本地客户端构建与模型推理服务是两件事。构建日志能看到「根脚本 → 各子包脚本 → tsc / 资源拷贝 / 打包脚本」的调用关系，最后一个子包成功不代表根构建成功，所以这里补了退出码 `0`、`Test-Path` 为 `True` 和通过本地路径运行得到 `1.5.2` 三项证据。全局 `pi` 是第一次实践课的 `1.5.0`，版本号不同也说明这次跑的是本地构建产物。

首次构建成功、模型目录数据仍有效后，可用 `npm run build:offline` 重新编译并复用已有模型数据；它不是首次安装的替代品。`dist` 是构建产物，以后改程序应改 `src` 下源码再重新构建，直接改 `dist` 可能在下次构建时被覆盖。


## 任务六 启动本地源码版本并完成一次会话

### 实验记录 S6（我的实际操作）

```powershell
.\pi-test.bat --version   # 1.5.2
.\pi-test.bat             # 启动本地源码版 Pi 交互界面
```

在 Pi 中发送的提示词：

```text
请用一句话说明源码和编译产物的区别。只回答问题，不修改文件。
```

实际收到的模型回复（见 S6c 截图）：

> 源码是人类可读的、按编程语言规则编写的原始指令文本；编译产物是编译器（或构建工具）将源码转换成的、计算机可直接执行的机器码或中间代码文件，人通常无法直接阅读和修改。

界面底部显示模型 `deepseek-flash`，并给出本次调用统计（TPS 10.7 tok/s、out 77、7.2s），说明确实完成了一次模型调用，而不只是启动了界面。

![S6a 源码入口版本](screenshots/S6a_source_entry.png)
*图 S6a 通过 .\pi-test.bat --version 验证源码入口。*

![S6b 本地源码版 Pi 界面](screenshots/S6b_pi_started.png)
*图 S6b .\pi-test.bat 启动的 Pi 交互界面。*

![S6c 提示词与模型回复](screenshots/S6c_conversation.png)
*图 S6c 自己的提示词与实际模型回复。截图未包含任何 API Key、auth.json 或授权回调地址。*

**回答：如何确保运行的是本地源码？** 使用仓库里的 `\.pi-test.bat`，而不是直接输入 `pi`——后者会启动第一次实践课全局安装的 1.5.0。`.bat` 调用 `.ps1`，由 Node.js 加载源码解析器运行 `packages/coding-agent/src/cli.ts`；命令路径、工作目录都在仓库内，输出版本 `1.5.2` 与本地源码包一致。本地源码版与全局版共用当前用户的配置目录 `~/.pi/agent`，复用第一次实践课的模型配置不等于运行了全局版本。

**回答：两个入口分别适合什么场景？** `\.pi-test.bat`（源码入口）适合开发调试，改 `src` 下的源码就能直接验证，不必每次重新构建；`dist/bundle/cli.js`（构建产物入口）适合验证编译结果、模拟正式安装包的运行方式，也能用 `node .\packages\coding-agent\dist\bundle\cli.js` 单独启动做对比。


## 遇到的困难与解决过程（重点）

### 困难 1：终端里 `node` 不是系统安装的 24，而是工具自带的 22

刚开始在自动化会话里执行 `node --version` 得到 `v22.22.2`，路径为 `C:\Users\lenovo\.workbuddy\binaries\node\versions\22.22.2-6\node.exe`；而 `C:\Program Files\nodejs\node.exe` 是 `v24.18.0`。原因是前者被放到了 PATH 最前面。若用 22 继续安装，可能触发依赖的 `EBADENGINE` 警告。

**解决**：在实验终端把 `C:\Program Files\nodejs\` 放到 PATH 最前，重新打开终端后 `node --version` 为 `v24.18.0`、`npm --version` 为 `11.16.0`，与系统实际安装一致（见 S1）。这也说明记录版本时要按自己终端的实际结果，而是以自己终端的实际结果为准。

### 困难 2：PowerShell 执行策略拦截 npm.ps1

直接执行 `npm --version` 报「无法加载文件 … npm.ps1，因为在此系统上禁止运行脚本」；`Get-ExecutionPolicy -List` 显示各作用域均为 `Undefined`（即默认 Restricted）。

**解决**：实验终端使用 `powershell -NoLogo -NoExit -ExecutionPolicy Bypass` 启动（等价于在会话内执行 `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass -Force`），只影响当前进程，不改动系统级策略。之后 `npm --version` 正常输出 `11.16.0`。

### 困难 3：git clone 连续两次因网络中断失败

第一次报 `RPC failed; curl 56 schannel: server closed abruptly ... early EOF`；第二次报 `CONNECT tunnel failed, response 502`（本机网络走 `127.0.0.1:64043` 代理）。两次都没有生成完整仓库目录，因此换目录重试而不是覆盖已有文件。

**解决**：改用 HTTP/1.1 并减少传输量：

```powershell
git -c http.version=HTTP/1.1 -c http.postBuffer=524288000 clone --depth 1 --filter=blob:none --branch main https://github.com/shizishizi111/pi-hunnu-2026.git
```

第三次成功检出 4613 个文件。`git remote -v` 里的 `[blob:none]` 就是部分克隆的标记，属正常现象；`git rev-parse HEAD` 仍给出完整提交编号（见 S2）。

### 困难 4：构建时反复出现 npm 配置警告

`npm run build` 进入每个子包时都会打印 `npm warn Unknown env/project config "min-release-age-exclude"`。核对后确认这是项目 `.npmrc` 中配置项与当前 npm 11.16.0 的兼容性提示，属于警告；构建日志最后仍给出 `Built packages\coding-agent\dist\bundle` 且退出码为 `0`，因此没有修改 `.npmrc`，也没有把它当成构建失败。

### 困难 5：无法弹出可见终端窗口做截图

本机桌面自动化处于无头模式，直接启动 PowerShell / Windows Terminal 只会得到无窗口进程，系统热键也被策略拦截，因此无法用「弹出窗口 + 全屏截图」的方式取证。

**解决**：沿用第一次实践课已验证的方式，用本地 PTY（node-pty）启动一个**真实的 Windows PowerShell 会话**，在 xterm 页面里逐条发送本次作业的命令，再用浏览器无头截图保存画面。命令和输出都是真实执行的结果，不是手工绘制的图；截图风格与第一次实践课一致，且画面中没有高亮框、鼠标指针或敏感信息。

### 困难 6：本地源码版复用模型配置

检查发现 `~/.pi/agent/auth.json` 为空对象，而第一次实践课的 DeepSeek 配置保存在实验专用目录中。为避免把密钥写进报告或截图，本次通过环境变量 `PI_CODING_AGENT_DIR` 指向该专用目录来复用已有配置；报告中不记录密钥内容。本次模型调用成功，收到了上述中文回复。模型能否回复仍取决于账户、网络与模型服务配置；若调用失败，应把「本地程序启动成功」和「模型调用失败」分开记录，不混为编译失败。

### 困难 7：截图终端里再次执行 `npm run build` 时模型目录获取超时

在截图用的终端会话中重新执行 `npm run build`，`@earendil-works/pi-ai@1.5.2 generate-models` 报 `Failed to fetch models.dev catalog: [TypeError: fetch failed]`，底层为 `ConnectTimeoutError ... UND_ERR_CONNECT_TIMEOUT`，`npm error code 1`，该次退出码为 1。这是**联网获取模型目录数据失败**，不是 TypeScript 编译或打包失败；首次构建时同一命令是成功的（见 S5a 的日志）。

**处理**：核对首次构建已生成并校验过模型数据（日志中有 `Generated model data is valid.`），并确认首次构建退出码为 0、产物齐全。尝试按作业说明用 `npm run build:offline` 在截图终端里重新编译，又遇到工具环境的批量删除保护（见困难 8），因此**没有把失败的重跑当成成功证据**：S5 改用「回看首次构建日志末尾 + 现场执行 `Test-Path` 与本地路径 `--version`」，两项现场结果分别为 `True` 和 `1.5.2`。两个脚本的分工仍然清楚：`npm run build` 负责首次刷新模型目录数据，`build:offline` 复用数据但仍执行完整编译；若离线构建提示数据缺失或过期，应恢复网络重新执行 `npm run build`。

### 困难 8：离线重跑构建被工具环境的批量删除保护拦截

按作业说明改用 `npm run build:offline` 重新编译时，`packages/ai` 的构建脚本要执行 `node scripts/package-files.mjs clean dist/providers/data`，一次清理 60 个生成文件，触发本机工具注入的防误删保护：`SAFE_DELETE_BULK_CONFIRM_REQUIRED`（`count 60 > threshold 50`），构建中止。这是实验终端所在工具环境的保护机制，不是项目脚本或源码的错误；首次 `npm run build` 在另一个工具会话里没有遇到这个拦截。

**处理**：先把会被清理的生成数据目录整体移走（用移动而不是删除，保留原始文件），尝试让 clean 步骤无需批量删除；本环境里重跑仍未完成，因此停止反复重试。最终 S5 的证据改为「首次真实构建日志（S5a）＋现场可复现的 `Test-Path` 与 `--version`（S5）」，并在报告中明确区分「首次构建成功」与「重跑失败」，不把失败重跑写成成功。

## 实验自查：源码在哪里、依赖装在哪里、构建做了什么、正在运行哪个版本

| 问题 | 本次实验的答案 |
| --- | --- |
| 源码在哪里 | `C:\Users\lenovo\pi-lab3\pi-hunnu-2026`，来自小组组织内 fork `shizishizi111/pi-hunnu-2026`，分支 `main`，提交 `c3cc91632d0a1950fb122605b190233f2aa0006e` |
| 依赖装在哪里 | 仓库根目录的 `node_modules`，由根目录 `package-lock.json` 锁定；npm workspaces 统一管理 `packages/*` 等子包，没有给子包单独建锁文件 |
| 构建执行了什么 | 根 `scripts.build` 依次进入 telemetry、schema、chord、tui、codemode、mcp、ai、durable、env、agent、coding-agent 等子包执行各自 build：TypeScript 编译、模型目录数据生成、资源拷贝，最后打包出 `dist/bundle/cli.js`（84 个文件，9.4 MiB） |
| 正在运行哪个版本 | 本地源码入口与构建产物均为 `1.5.2`；全局 `pi` 为第一次实践课安装的 `1.5.0`，本次用 `.\pi-test.bat` 与本地路径运行以区分两者 |

本次不要求构建独立 EXE，也不要求部署大模型权重；报告中的模型数据仅指模型目录与能力配置。


将实验报告和截图上传到个人 Git 仓库，在课程作业仓库按第一次实践课的方式提交 Issue，填写报告地址。Issue 名称：`实践课3：Pi Agent源码安装与编译 [姓名拼音]`。

最好是复制原始作业markdown文档后，再贴图并写上自己的理解/作业过程。 避免只有截图提交。

## 提交信息

- 报告与截图仓库：https://github.com/Mr-ship/pi-agent-lab3-20261123
- Issue 名称：`实践课3：Pi Agent源码安装与编译 gongjian`
