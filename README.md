# Jev 决策技能包（Operit Skill）

本仓库供支持 skill 的客户端使用（Operit / Claude Code / Cursor / Codex CLI）。技能只提供方法论与场景索引，真正的判断由 Jev 模型完成。

- 配套插件（ToolPkg，推荐）：https://github.com/what-my-names/operit-jev-bundle
- 安装：把 `skills/` 下的目录放进客户端的 skills 目录（Operit 为 `/sdcard/Download/Operit/skills/`）
- 下载：Release v1.0.0 的 `operit-jev-skills-v1.0.0.zip`
- Jev 官方 API 文档：https://docs.typesafe.ai ｜ 控制台（拿 key）：https://console.typesafe.ai
- 密钥可选：未配 key 时可用插件侧的本机作答，或 CLI 的 `--dry-run` 离线校验

---

# Jev 决策（插件 + 技能）

**Jev 让 Agent 做出类型化判断**：要选项就从给定选项里挑（choice），要是非就给概率（noul），要评级就按档位打分（score）。结果由代码校验，不靠模型自我感觉。

## 两种形态，按需选一个（或都装）

| 形态 | 装什么 | 适合谁 |
|---|---|---|
| **ToolPkg 插件**（推荐） | 本市场安装，或取 Release 里的 `.toolpkg` | Operit 用户：装完就得到开关 + 面板 + 两个工具 |
| **Skill 技能包** | 仓库 `skills/` 或 Release 里的 zip | 其它 AI 客户端（Claude Code / Cursor / Codex CLI）；以及想读完整方法论的进阶用户 |

## 插件给了什么（装完就能看到）

- 输入框旁的 **「Jev 模式」开关**：开启就向系统提示词追加 Jev 规范，关闭则**完全不追加（零开销）**
- 侧边栏 **「Jev 设置」面板**：模式开关、5 档通道、密钥抽屉、系统提示词可整段替换（改完下一轮即生效）
- 两个工具：**`jev_decide`**（做判断）、**`jev_reference`**（离线手册 7 节，不联网不花钱）
- AI 写的 `<jev>…</jev>` 会渲染成一行决策
- **技能联动**：装了同源 5 个 skill 时自动检测，并在提示词里写明配合用法

## 五档通道（面板里点选，点一下即存）

| 档位 | 行为 |
|---|---|
| 🔄 **自动**（默认） | 有 key 就走真 Jev；没 key 自动转「本机作答」 |
| 🌐 **OpenRouter** | 只用 OpenRouter 通道 |
| 🛡 **TypeSafe** | 只用官方直连通道 |
| 🤖 **本机作答**（`mode=host`） | **零成本**：插件把规范化题目与每题型字段要求交回当前模型自行作答，并强制标注「不是真 Jev」 |
| 🚫 **仅模拟** | 只回格式骨架，不产出答案 |

## 三种接入方式（技能侧）

**一、技能直用（装完即用，零开发）**
Agent 读文档 → 按协议拼请求 → 跑 CLI → 读回结果。
`python3 <skill-dir>/scripts/jev.py decide request.json --dry-run`（离线校验，不联网不花钱）；真实判断加 `--provider openrouter` 或 `--provider typesafe`。exit 0=出结果、2=需人工复核、1=出错。

**二、Operit 原生工具（已实现，即上方插件）**
用沙盒包 `Tools.Network.httpPost()` 直打决策端点，把 choice / noul / score 的校验也抄一份。不需要终端、不需要浏览器、不依赖 node。

**三、封装成 MCP（跨客户端复用，尚未提供）**
写 stdio MCP 把 CLI 包成 `jev_decide` 工具，Claude Code / Cursor / Codex CLI 等都能挂。最重的一条，目前未做。

> 另有**浏览器自动化型** `jev-ultrafast-mcp`（一次调用跑完整个网页流程），需要 Chromium 系浏览器 + CDP 通道，与本包互不依赖，属独立项目。

## 前置条件

- Python ≥ 3.10（**仅技能侧的 CLI 需要**；插件不需要 Python）
- **密钥可选**：有 `OPENROUTER_API_KEY` 或 `TYPESAFE_API_KEY` 就能用真 Jev；没有也能装、能用（自动转本机作答或离线校验）

## ⚠️ 关于 API Key（装之前请看这五点）

**1. 要花钱吗？** 插件与技能本身免费（MIT）。真实判断由官方 Jev 服务按 token 计费，扣的是**你 key 所属账户**，与作者无关。
**2. 没有 key 能用吗？** 能用，而且有产出：插件会自动转 `mode=host`——把规范化题目交回当前模型自行作答，并在结果与提示词里强制标注「本机作答，不是真 Jev」。如果你想要纯粹的格式骨架，可在面板选「仅模拟」。
**3. 去哪拿 key？** 二选一：OpenRouter（openrouter.ai/settings/keys）或 TypeSafe 官方（console.typesafe.ai）。
**4. key 放哪？** 放**环境变量**（或直接在「Jev 设置」面板的密钥抽屉里粘贴保存）。**不要发在聊天里、不要写进公开仓库、不要贴进 issue**。面板不回显密钥值；AI 侧只检查 key 是否存在，从不读取也不打印它的值。
**5. 会发什么出去？** 你交给它判断的那段内容（`state`）会原样发送给所选服务商。涉及隐私的数据请先确认再发。

## 安装与仓库

- **插件（ToolPkg）**：在本市场安装；或从 GitHub Release 取 `.toolpkg` 文件导入
- **技能（Skill）**：`repository_url` = https://github.com/what-my-names/operit-jev-skills
- **插件源码与 Release**：https://github.com/what-my-names/operit-jev-bundle

## 价格（官方公开数据）

TypeSafe 官网明示：**Jev $42 / 十亿 input token**（自称比某大模型输入价低 238×）。换算：一次判断约几百 token → **约 $0.00002 / 次**，一分钱能跑几百次。OpenRouter 通道走 `api/alpha/decisions` 专用端点，未列入公开模型价格表，以你账户计费为准。

## 副作用与边界（请务必阅读）

- **会联网**：真实模式向 `openrouter.ai` 或 `api.typesafe.ai` 发起 POST；`--dry-run`、本机作答与模拟模式不联网
- **数据出境**：请求体里的 `state` 会原样发送给第三方服务，可能包含你提供的证据 / 文本 / 工单内容
- **注入系统提示词**：插件开启「Jev 模式」时，会向 system prompt 末尾追加约 20 行 Jev 规范（占用少量 token）；**关闭开关则完全不追加（零开销）**；你可在面板里整段替换默认文案
- **新增界面元素**：输入菜单里的「Jev 模式」开关、侧边栏「Jev 设置」入口（可在包管理里停用整个插件）
- **面板会写环境变量**：保存通道 / 密钥 / 自定义提示词时写入本机环境变量（不写云端）
- **本地执行（仅技能侧）**：内置 `jev.py` 以当前用户权限在本地运行，只做参数解析与 HTTP 请求，不修改系统
- **不承诺正确**：probability / confidence 需按你自己的任务实测校准，**不是正确率**；低于阈值会标 `needs_review`
- **选择≠授权**：Jev 只给建议，执行与否由使用者决定

## 已知限制

- 上游为 alpha 阶段端点（`/api/alpha/decisions`），路径或模型名可能变动
- 插件侧 `Tools.Network.httpPost` 的参数形状在官方 types 缺失下取自已装包源码，**真机真实调用尚未验证**（无密钥）；遇错会返回 `mode=error` 与状态码，**不猜答案**
- 面板与开关基于 Operit Compose DSL，宿主版本差异可能导致表现不同

## 来源与许可

决策协议与题型规范来自 `wuyoscar/jev-skill`（MIT）及 TypeSafe / OpenRouter 公开接口文档。本插件与技能包为独立实现，请保留上游署名与 MIT 许可。

### 配套 Skill（可选增强，建议一起装）
- 5 个技能（决策设计 / 动作选择 / 证据定位 / 输出评判 / 批量分类）：https://github.com/what-my-names/operit-jev-skills
- 仓库布局为 skills/<名>/SKILL.md，也适用于 Claude Code / Cursor / Codex CLI 等支持 skill 的客户端
- 装了之后插件会**自动检测**，并在系统提示词里写明「先按对应 skill 的规范走，判断仍走 jev_decide」

### Jev API 与实现（想自己接的看这里）
- 官方文档：https://docs.typesafe.ai （入门 /introduction、快速开始、三种题型、Confidence、Patterns；给 AI 读的全站索引 https://docs.typesafe.ai/llms.txt）
- 拿 key：https://console.typesafe.ai
- 两个端点（二选一）：
  - TypeSafe 官方：POST https://api.typesafe.ai/v1/systemone ，模型 jev-1.13.0，头 Authorization: Bearer $TYPESAFE_API_KEY
  - OpenRouter：POST https://openrouter.ai/api/alpha/decisions ，模型 typesafe/jev-1.13，头 Authorization: Bearer $OPENROUTER_API_KEY
- 请求体只有三个顶层字段：{"model": "…", "state": "证据或上下文", "questions": {"q1": {"type": "choice", "instructions": "…", "criteria": {"A": "…", "B": "…"}}}}
- 响应：{"model": "…", "answers": {"q1": {"choice": "A", "probabilities": {"A": 0.8, "B": 0.2}, "confidence": 0.7}}, "usage": {"input_tokens": …, "output_tokens": …, "cost": …}}
- 题型字段：Choice → choice / probabilities / confidence；Score → score / probabilities / confidence；Noul → noul（0–1，无 confidence）
- 实现要点（官方建议）：一题只做一件事，多因子请拆题后在代码里自己加权；独立题在同一请求里并行评估、互不影响，加题几乎不增耗时；用 confidence 做门控（高置信自动执行，低置信转人工复核）
- 本插件的实现：manifest.json + main.js（系统提示词钩子 / 输入菜单开关 / <jev> 渲染 / 侧边栏面板）+ packages/jev_decide.js（校验 + Tools.Network.httpPost + needs_review + host 兜底）+ packages/jev_reference.js（离线手册）；源码 https://github.com/what-my-names/operit-jev-bundle
