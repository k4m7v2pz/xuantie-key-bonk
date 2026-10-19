# KeyBonk 发声键盘

## 项目概述

KeyBonk 是一款 Windows 桌面音效软件：通过低级全局钩子监听键盘/鼠标，按下时按虚拟键值（VK）播放对应 wav，并以置顶透明分层窗口展示背景图。

### 当前基线（C++）

- 语言/标准：C++17，宽字符（Unicode）构建
- GUI：原生 Win32 API + GDI+（分层窗口 `UpdateLayeredWindow`）
- 音频：第三方 yumo audio-player（预加载 + 混合播放），二进制库构建期下载，不入库
- 构建：MSYS2/MinGW GNU Make（64/32 位），Inno Setup 打包
- 目标平台：仅 Windows（x64/x86）

### 重写方向

本仓库计划将 C++ 版本重写为玄铁语言（xuantie）。当前文档与目录先作为 C++ 现状基线整理，重写时以 `docs/架构概览.md` 为模块与流程对照。

- origin：`k4m7v2pz/xuantie-key-bonk`（本 fork）
- upstream：`keyBonk-org/KeyBonk`

## 开发须知

- C++ 基线在 Windows + MSYS2/MinGW 环境下用根目录 `make` 构建，`iscc installer.iss` 打安装包；无 Windows 环境时，至少保证改动不破坏既有目录结构与接口，并做静态核对。
- 进入玄铁重写阶段后，每次修改完源码，用玄铁工具链（moon / xtl）构建并测试通过后再提交。
- 发版前按 `docs/编译前版本检查.md` 核对三处版本号：`include/globalDevelopmentControl.hpp`、`installer.iss`、`README.md`。

## 提交约定

### 自动提交授权

本仓库允许 Agent 在完成用户既定的改动后，**自动执行 `git add` / `commit` / `push` 到当前跟踪分支，无需逐次等待人类批准**。自动提交仍须遵守：

- 先过「提交前防泄漏守卫」核对清单与内容；
- 如实写 Co-Authored-By 与提交小尾巴；
- **禁止 force push / `--force-with-lease`**；确需改写历史必须先说明理由并给出备份方案；
- 只提交用户本次明确要求范围内的改动，不擅自夹带超出范围的内容。

### 提交尾部 Co-Authored-By

提交信息末尾用 trailer（空行隔开）`Co-Authored-By`，名字填写**实际调用工具、执行本次提交的模型/Agent**（不是写代码的模型，二者可能不同——如强力模型写代码、切小模型来提交）；不知道实际提交者则该名字不写。`noreply@` 域名无对应则不写域名只留名字。格式示例：`Co-Authored-By: <实际提交的Agent名> <noreply@<对应域名>>`。

### 提交前防泄漏守卫（硬约束）

进入 git 的内容默认互联网可见（公开仓库直接可见）。**禁止提交**：私钥/token/密码、个人邮箱、真实姓名、私人服务器地址、代理端口（HTTP/SOCKS 复用端口）、内部 IP、SSH 口令、VPS/VM 连接信息。

- 敏感凭据与连接信息一律不入 commit/issue/PR/公开文档；需本地保存时放入已被 `.gitignore` 的目录（如 `secrets/`）。
- 文档里举例远端/邮箱/端口一律用占位符：`<example@example.com>`、`<proxy-port>`、`<ssh-port>`、`<host>`。
- **提交前核对流程（不得跳过）**：
  1. `git add` 后先 `git diff --cached --name-only` 确认暂存区文件清单，无 `secrets/`、`*.pem`、`id_rsa`、`id_ed25519`、`*.key`、`credentials` 等；
  2. `git diff --cached` 扫内容，发现真实 IP / 端口 / 邮箱 / token → `git restore --staged <file>` 摘出，替换为占位符或删除后重新 `git add`；
  3. 确认无泄漏再 `git commit`。

### 提交小尾巴（如实标注，禁止编造）

issue/PR/追评小尾巴如实标注人的参与程度，**禁止把「批准」写成「审阅/查看」**：

- 仅当人类**实际查看过**内容（描述/代码）才写"已查看/已审阅"。
- 人类仅授权、未逐字审阅 → 只写"批准"，档位按「是否理解（能否接住）」划分：
  - 已理解 → `人类已在Agent对话中批准提交（已理解）`
  - 未能完全理解（如正文表格未接住）→ `人类已在Agent对话中批准提交（未能完全理解，可附具体）`
- 末尾如实标注提交方式与提交者：`提交方式：Personal access tokens (classic)｜提交者：豆包工作 Mac版`；模型/推理强度可选（AI 知道才写）。
- 已发内容的小尾巴不改。
