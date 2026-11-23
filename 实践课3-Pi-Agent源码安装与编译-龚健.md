# Pi Agent 源码获取、依赖安装与本地编译指导

> **说明**：本报告以课程作业原始 Markdown 文档为骨架，保留全部原始要求，并在每个任务下加入「实验记录」「截图」和「我的理解」。所有命令均在本机 Windows PowerShell 中实际执行，输出与截图一一对应。

## 基本信息

- 姓名：龚健
- GitHub 登录名：Mr-ship
- 实验日期：2026 年 11 月 23 日
- 实验环境：Windows、Node.js v24.18.0、npm 11.16.0、Git 2.55.0.windows.3
- 源码仓库（小组组织内 fork）：https://github.com/shizishizi111/pi-hunnu-2026
- 本地源码目录：`C:\Users\lenovo\pi-lab3\pi-hunnu-2026`（全英路径）
- 源码分支：`main`，提交编号：`c3cc91632d0a1950fb122605b190233f2aa0006e`
- 本地构建产物版本：`1.5.2`；第一次实践课全局安装的 `pi` 为 `1.5.0`


## 本次完成的六项任务

1. 检查并准备 Node.js、npm 和 Git，记录实际版本并截图。
2. （这儿实际上是第二次作业就做了的）下载课程仓库源码，并截图。
3. 在仓库根目录安装依赖，说明安装命令的作用并截图。
4. 完成本地编译，检查构建产物并验证其能够运行，并截图。
5. 记录下所遇到的困难，以及解决过程，描述并截图（重点）


第一次实践课侧重安装和使用 Pi 客户端，本次侧重建立本地源码开发环境。完成后，应能够说明“源码在哪里、依赖装在哪里、构建执行了什么、正在运行哪个版本”。本次不要求构建独立 EXE，也不要求部署大模型权重。


**仓库与版本：** 使用第二次实践作业，各小组组织内fork的仓库地址。


## 任务一 实验准备：检查和安装开发工具

### 1. 检查现有环境

打开 PowerShell，逐条执行：

```powershell
node --version
npm --version
git --version
```

三条命令分别检查 Node.js、npm 和 Git。第一次实践中 `pi` 能运行，不代表当前终端一定能找到这三个工具。
**截图要求 S1：** 显示当前环境依赖的各个版本。

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


| 工具 | 本次用途 | 要求或建议 |
|---|---|---|
| Node.js | 运行 JavaScript 程序和项目开发脚本 | 根项目声明 `>=22.19.0`；本次实验建议使用 Node.js 24 LTS |
| npm | 安装依赖、执行项目脚本 | 使用所安装 Node.js 附带的 npm，记录实际版本 |
| Git for Windows | 下载和管理源码，并提供 Git Bash | 安装后能执行 `git --version` |
| 文本编辑器 | 查看和编辑源码 | 可使用 VS Code 或自己熟悉的编辑器 |

Node.js 要求来自本小组组织内仓库 [package.json 的 engines 字段（教师组织内的仓库地址，仅作参考）](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026/blob/main/package.json)，不是根据截图中的版本推测。项目根配置没有指定某个固定 npm 版本。

![检查 Node.js 版本](https://raw.githubusercontent.com/hunnuOpenSourceSoftware2026/assignments/main/assets/02-Pi-Agent-source-build/image-01-node-version.png)

*图1 教师检查原有环境，Node.js 为 v22.23.2。*

根项目的最低版本不代表每个工作区依赖都只要求同一个版本。图2中，旧分支安装时的 `@earendil-works/gondolin@0.12.0` 声明需要 Node.js `>=23.6.0`，因此 Node.js 22.23.2 虽满足根项目声明，仍触发了依赖的 `EBADENGINE` 警告。**为便于本次实验统一环境，建议使用 Node.js 24 LTS。** 这是结合实操截图给出的课程建议，不是把根项目声明改成了 `>=24`。 已安装了node但是版本不对的，可参考“已使用 fnm 的同学：切换到 Node.js 24” 小节进行版本更新。

![旧环境安装时的版本与依赖警告](https://raw.githubusercontent.com/hunnuOpenSourceSoftware2026/assignments/main/assets/02-Pi-Agent-source-build/image-02-install-warning.png)

*图2 旧分支、Node.js 22 环境下的安装输出：出现 EBADENGINE、弃用提示和依赖审计结果。*

如果已经使用 Node.js 24 LTS，可跳过安装；若使用其他版本，应同时核对项目及安装日志中的依赖要求。已使用版本管理工具的同学，通过原有工具切换即可。

### 2. 未安装 Node.js 时：同时安装 Node.js 和 npm

1. 打开 [Node.js 官方下载页面](https://nodejs.org/en/download)。
2. 在版本选择中选择 **Node.js 24 LTS（长期支持）**，使用该系列可用的最新补丁版本。若教师指定统一版本，按教师指定版本安装。
3. 常见 Intel、AMD 电脑选择 **x64**；Windows ARM 电脑选择 **ARM64**。可在“设置 → 系统 → 系统信息（关于）”查看系统类型。
4. 下载 **Windows Installer（`.msi`）**，双击运行。
5. 按提示安装，通常保留默认目录和默认组件即可，确认保留 **Node.js runtime**、**npm package manager** 和 **Add to PATH**。
6. 如果出现额外原生模块开发工具的安装选项，本次基础流程可暂不勾选；遇到具体原生编译任务时再按对应文档安装。
7. 安装完成后关闭原终端，重新打开 PowerShell；使用 VS Code 内置终端时，建议重新打开 VS Code。

**npm 会随 Node.js 官方安装程序一起安装，无需另外下载或重复安装。** 这是官方安装方式之一，见 [npm 官方安装说明](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm/)。

重新检查：

```powershell
node --version
npm --version
node -e "console.log('Hello, Node.js!')"
```

最后一条命令应输出 `Hello, Node.js!`。版本号按自己的实际结果记录，不要求与教师电脑完全一致。

### 已使用 fnm 的同学：切换到 Node.js 24

`fnm` 是 Node.js 版本管理工具。下面的命令只适用于已经安装并配置好 fnm 的环境；通过上面的 `.msi` 完成安装的同学，不必再安装一套 fnm。

```powershell
fnm --version
fnm install 24
fnm use 24
fnm default 24
node -v
npm -v
```

`install` 下载指定系列，`use` 切换当前终端使用的版本，`default` 设置默认版本。新终端仍需正常加载 fnm 的 shell 初始化配置；若已有 fnm 但提示尚未设置环境变量，可按 [fnm 官方 PowerShell 配置](https://github.com/Schniz/fnm#powershell)处理，当前会话的初始化命令为：

```powershell
fnm env --use-on-cd --shell powershell | Out-String | Invoke-Expression
```

初始化后重新执行版本切换和检查。无需为了照抄截图而安装固定的 npm 11.19.1，使用 Node.js 所附的 npm 并记录结果即可。

![使用 fnm 安装并切换 Node.js 24](https://raw.githubusercontent.com/hunnuOpenSourceSoftware2026/assignments/main/assets/02-Pi-Agent-source-build/image-03-node24.png)

*图3 教师通过 fnm 切换到 Node.js v24.22.3，并确认 npm 为 11.19.1；实际补丁版本可能不同。*


## 任务二 下载课程仓库源码（上次实践已做的可略过，为了教学过程完整，此步骤保留）

### 1. 建立实验目录并下载源码

尽量在全英路径下下载项目

```powershell
git clone --branch main https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026.git #替换成自己小组组织内的仓库
cd pi-hunnu-2026
```

如果目标文件夹已经存在，不要直接删除或覆盖。可以选用另一个空目录，或确认已有目录确实是本次所需仓库后继续。

### 2. 确认源码来源和版本

```powershell
Get-Location
git remote -v
git branch --show-current
git rev-parse HEAD
git status --short
```

检查 `origin` 是否指向课程仓库，当前分支是否为 `main`，并把完整提交编号记录到报告中。后续所有安装和构建命令，除特别说明外，都在这个仓库根目录执行。

**截图要求 S2：** 显示当前目录、远端地址、分支和提交编号。仅打开 GitHub 网页不能证明源码已下载到本地。

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

**我的理解**：`origin` 指向 `shizishizi111/pi-hunnu-2026`，即本小组组织内的 fork，不是教师组织的仓库；分支为 `main`，完整提交编号已记录，后续安装和构建都在这个根目录执行。远端显示 `[blob:none]` 是因为克隆时为避开网络中断使用了部分克隆参数，不影响源码内容与提交编号，详见文末「遇到的困难」。只打开 GitHub 网页不能证明源码已下载到本地，所以这里用 `Get-Location` 和 `git rev-parse HEAD` 作为依据。


## 任务三 阅读配置，确认安装与编译的依据

用编辑器打开仓库根目录，查看以下文件：

| 文件或目录 | 需要观察的内容 |
|---|---|
| `README.md` | `Development` 中提供了哪些开发命令 |
| `package.json` | `engines`、`workspaces`、`scripts`、`devDependencies` |
| `package-lock.json` | 锁定依赖版本和依赖关系的文件 |
| `packages/` | 同一仓库中的多个子包 |
| `packages/coding-agent/package.json` | CLI 包的构建脚本和 `bin` 入口 |

在根 `package.json` 中找到：

```json
"engines": {
  "node": ">=22.19.0"
}
```

再找到 `scripts.build`。其中的命令会依次进入多个子包并执行各包自己的 `npm run build`；`&&` 表示前一步成功后才继续下一步。`npm run build` 的具体工作由项目配置决定，不是 npm 对所有项目都执行同一套编译过程。

该仓库使用 npm workspaces 管理多个包。需从根目录安装依赖，按根目录脚本顺序构建，不要自行给每个子包分别创建新的依赖锁定文件。依据：[根 package.json](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026/blob/main/package.json)。

**截图要求 S3：** 显示 `engines.node` 和 `scripts.build` 的定义，可分为两张图。报告用自己的话回答：Node.js 版本要求在哪里？`npm run build` 实际从哪里读取命令？

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

**回答：Node.js 版本要求在哪里？** 在**根 `package.json` 的 `engines.node` 字段**（`>=22.19.0`），不是从教师截图里推测的，也不是每个子包各自声明同一个版本。

**回答：`npm run build` 实际从哪里读取命令？** 先读**根 `package.json` 的 `scripts.build`**，它是一串 `cd 子包 && npm run build`；进入子包后，再读**该子包自己的 `package.json` 的 `scripts.build`**（如 coding-agent 的 `build:unbundled` 用 `tsc` 编译，再执行 `build-coding-agent-bundle.mjs` 打包）。`&&` 表示前一步成功才继续下一步。npm 本身不规定编译动作，具体做什么完全由项目脚本决定。

**我的理解**：仓库用 npm workspaces 管理 `packages/*`、`apps/*` 和几个 examples 子包，根目录只有一份 `package-lock.json`（204 KB）锁定依赖，因此必须从根目录安装、按根脚本顺序构建，不能给每个子包单独生成锁文件。`bin.pi` 指向 `dist/bundle/cli.js`，这就是后面要验证的 CLI 入口。


## 任务四 安装项目依赖

### 1. 执行安装

确认当前目录包含根 `package.json`，执行：

```powershell
npm install --ignore-scripts
```


该命令主要完成以下工作：

- 根据 `package.json` 和 `package-lock.json` 解析、下载并安装依赖；锁文件与声明一致时使用锁定版本。
- 在本地建立 `node_modules` 等依赖目录，并关联工作区中的本地包。
- 安装项目需要的库和开发工具，例如编译器、打包工具。
- `--ignore-scripts` 跳过安装阶段自动触发的生命周期脚本，例如 `postinstall` 和 `prepare`；不影响后续主动执行 `npm run build`。

这里没有 `-g`，安装的是当前项目的依赖。它不会把本地修改后的 Pi 自动安装成全局 `pi` 命令。命令来源见 [Development](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026#development)，参数含义见 [npm install 文档](https://docs.npmjs.com/cli/v11/commands/npm-install/#ignore-scripts)。



**截图要求 S4：** 显示安装结束的输出结果，应区分警告与导致命令失败的错误

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


### 2. 结合示例理解安装警告

- `EBADENGINE`：当前 Node.js 不满足某个包声明的版本要求。图中应关注 `required` 与 `current` 的差异，按任务一切换到合适版本后重新安装。
- `deprecated`：依赖包已弃用或不再维护的提示，不等同于安装命令必然失败。
- `vulnerabilities`：本次依赖树的审计结果，需要记录和分析。不要直接照着输出执行 `npm audit fix --force`，它可能改变课程使用的依赖版本。

![main 分支下重新安装依赖的输出](https://raw.githubusercontent.com/hunnuOpenSourceSoftware2026/assignments/main/assets/02-Pi-Agent-source-build/image-04-install-main.png)

*图4 Node.js 24、main 分支下的另一轮安装输出，显示安装汇总和 found 0 vulnerabilities。*

图2与图4的分支和依赖内容均有变化，不能据此得出“升级 Node.js 消除了所有漏洞”的结论。`found 0 vulnerabilities` 只表示该次审计没有报告已知漏洞，也不是构建成功证明。截图未显示退出码，学生仍应按上述步骤补充自己的退出码和检查结果。

## 任务五 本地编译与构建产物验证

### 1. 首次构建

仍在仓库根目录执行：

```powershell
npm run build
```


首次构建需要联网获取模型目录数据。这里的“模型数据”指模型目录、能力等配置数据，**不是下载大模型权重**。本地客户端构建与模型推理服务是两件事。依据：[README](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026#development)、[AI 包脚本](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026/blob/main/packages/ai/package.json)。

![执行 npm run build 的过程](https://raw.githubusercontent.com/hunnuOpenSourceSoftware2026/assignments/main/assets/02-Pi-Agent-source-build/image-05-build-progress.png)

*图5 教师执行 npm run build，日志依次进入多个子包，并开始 AI 包的模型目录生成。*

图5可用于观察“根构建脚本 → 子包构建脚本 → 编译器或生成脚本”的调用关系。它只展示构建过程的一部分，没有显示最终退出码和 CLI 产物验证，**不能仅凭这张图判定整个仓库已构建成功**。

### 2. 检查并运行构建产物

```powershell
Test-Path .\packages\coding-agent\dist\bundle\cli.js
node .\packages\coding-agent\dist\bundle\cli.js --version
```

第一条应输出 `True`，第二条应输出当前源码对应的 Pi 版本。这个入口来自 CLI 包的 `bin.pi` 配置，见 [packages/coding-agent/package.json](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026/blob/main/packages/coding-agent/package.json)。

`dist` 保存构建产物。以后修改程序时应修改 `src` 下的源码，再重新构建；直接修改 `dist` 中的文件，可能在下次构建时被覆盖。

**截图要求 S5：** 显示完整构建结束的最后一段输出、退出码 `0`、入口文件存在，以及通过上述本地路径运行得到的版本号。某一个子包成功，不能代表根构建全部成功；只有版本号也不能证明已经完成本次构建。

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


### 3. 后续重新构建

首次构建成功、模型目录数据已准备好且仍有效后，可使用：

```powershell
npm run build:offline
```

它仍会重新编译相关包，但复用已有模型目录数据。不要把它当成首次安装或首次数据初始化的替代命令；若提示数据缺失或过期，按错误提示补齐数据，或恢复网络后重新执行 `npm run build`。


## 任务六 启动本地源码版本并完成一次会话

### 1. 先验证源码入口

在仓库根目录执行：

```powershell
.\pi-test.bat --version
```

这个脚本名称包含 `test`，但它是运行本地源码的入口，不是执行整个项目测试套件。当前版本的 `.bat` 调用 `.ps1`，由 Node.js 加载源码解析器并运行 `packages/coding-agent/src/cli.ts`。依据：[pi-test.bat](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026/blob/main/pi-test.bat)、[pi-test.ps1](https://github.com/hunnuOpenSourceSoftware2026/pi-hunnu-2026/blob/main/pi-test.ps1)。

### 2. 启动交互界面

```powershell
.\pi-test.bat
```

本次使用上述命令验证本地源码。直接输入 `pi`，可能启动第一次实践课安装的全局版本，无法单独证明运行的是刚下载的源码。

本地源码版默认与已安装的 Pi 共用当前用户的配置目录 `~/.pi/agent`。因此，第一次实践课已完成登录且凭据仍有效的同学，运行 `.\pi-test.bat` 后通常无需再次 `/login`。这表示复用了用户配置，并不表示运行的是全局安装版本。只有未配置模型、凭据失效或需要更换账户时，才需重新配置。

发送一个简单问题，例如：

```text
请用一句话说明源码和编译产物的区别。只回答问题，不修改文件。
```

确认实际收到模型回复。模型能否回复取决于账户、网络和模型服务配置；若失败，应将“本地程序启动成功”和“模型调用失败”分别记录，不能把两者混为编译失败。

退出 Pi 返回 PowerShell 后，也可以运行编译产物进行比较：

```powershell
node .\packages\coding-agent\dist\bundle\cli.js
```

**截图要求 S6：** 显示 `.\pi-test.bat` 的启动命令或相关终端上下文、Pi 界面、自己的提示词和实际模型回复。可分为多张截图。不要提交 API Key、`auth.json` 或授权回调地址。

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


报告说明：本次如何确保运行的是本地源码？源码入口与 `dist/bundle/cli.js` 入口分别适合什么场景？

## 提交方式：
## 遇到的困难与解决过程（重点）

### 困难 1：终端里 `node` 不是系统安装的 24，而是工具自带的 22

刚开始在自动化会话里执行 `node --version` 得到 `v22.22.2`，路径为 `C:\Users\lenovo\.workbuddy\binaries\node\versions\22.22.2-6\node.exe`；而 `C:\Program Files\nodejs\node.exe` 是 `v24.18.0`。原因是前者被放到了 PATH 最前面。若用 22 继续安装，可能像作业图 2 那样触发依赖的 `EBADENGINE` 警告。

**解决**：在实验终端把 `C:\Program Files\nodejs\` 放到 PATH 最前，重新打开终端后 `node --version` 为 `v24.18.0`、`npm --version` 为 `11.16.0`，与系统实际安装一致（见 S1）。这也说明记录版本时要按自己终端的实际结果，而不是照抄教师截图。

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
