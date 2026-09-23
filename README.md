# Jev 决策技能包

**Jev 让 Agent 做出类型化判断**：要选项就从给定选项里挑（choice），要是非就给概率（noul），要评级就按档位打分（score）。结果由代码校验，不靠模型自我感觉。

## 包含什么

- **5 个技能**：`jev`（决策设计）、`jev-act`（动作选择）、`jev-documents`（证据定位）、`jev-eval`（输出评判）、`jev-triage`（批量分类）
- **108 个场景索引** + 16 个请求模板 + 25 篇指南
- **`scripts/jev.py`**：纯标准库 CLI（17.3 KB，无需 pip 安装任何依赖）

## 三种接入方式

**一、技能直用（装完即用，零开发）**
Agent 读文档 → 按协议拼请求 → 跑 CLI → 读回结果。
`python3 <skill-dir>/scripts/jev.py decide request.json --dry-run`（离线校验，不联网不花钱）；真实判断加 `--provider openrouter` 或 `--provider typesafe`。exit 0=出结果、2=需人工复核、1=出错。

**二、做成 Operit 原生工具（推荐）**
用沙盒包 `Tools.Network.httpPost()` 直打决策端点，把 choice / noul / score 的校验抄一份——「让它判断一下」变成聊天里一次工具调用。不需要终端、不需要浏览器、不依赖 node。

**三、封装成 MCP（跨客户端复用）**
写 stdio MCP 把 CLI 包成 `jev_decide` 工具，Claude Code / Cursor / Codex CLI 等都能挂；Operit 侧写进 `mcp_plugins/mcp_config.json`。最重的一条。

> 另有**浏览器自动化型** `jev-ultrafast-mcp`（一次调用跑完整个网页流程），需要 Chromium 系浏览器 + CDP 通道，与本包互不依赖，属独立项目。

## 前置条件

- Python ≥ 3.10（仅标准库）
- **需自备 API Key**：`OPENROUTER_API_KEY` 或 `TYPESAFE_API_KEY`（任一枚即可）
- 没有 key 也能用：降级为模拟模式，如实标注 `jev_called: false`、`probability: null`

## ⚠️ 关于 API Key（装之前请看这五点）

**1. 要花钱吗？** 本包本身免费（MIT）。真实判断由官方 Jev 服务按 token 计费，扣的是**你 key 所属账户**，与作者无关。

**2. 没有 key 能用吗？** 能。可以安装、阅读全部文档、离线校验请求格式（`--dry-run` 不联网、不花钱）。但**做不了真实判断**——此时结果会明确标注为「模拟」，写明 `jev_called: false`、概率为空，那不是 Jev 的答案。

**3. 去哪拿 key？** 二选一即可：OpenRouter（openrouter.ai/settings/keys）或 TypeSafe 官方（console.typesafe.ai）。

**4. key 放哪？** 放**环境变量**。**不要发在聊天里、不要写进公开仓库、不要贴进 issue**；技能只检查 key 是否存在，从不读取也不打印它的值。

**5. 会发什么出去？** 你交给它判断的那段内容（`state`）会原样发送给所选服务商。涉及隐私的数据请先确认再发。

## 副作用与边界（请务必阅读）

- **会联网**：真实模式向 `openrouter.ai` 或 `api.typesafe.ai` 发起 POST 请求；`--dry-run` 与模拟模式不联网
- **数据出境**：请求体里的 `state` 会原样发送给第三方服务，可能包含你提供的证据/文本
- **会产生费用**：按 token 计费，由 key 所属账户承担；响应 `usage` 中含 `cost`（美元）
- **需要密钥**：技能只检查 key 是否存在，不读取、不打印密钥值
- **本地文件**：自带 assets / references / scripts；除读取你指定的请求 JSON 外，不改动任何文件
- **不做的事**：不创建账户、不自动安装依赖、不静默切换供应商、不替你执行动作（**选择 ≠ 授权**）
- **能力边界**：`confidence` 不是准确率，`probability` 不是「结果正确」，请按自己的任务实测校准

## 来源与许可

基于 `wuyoscar/jev-skill`（MIT License，414⭐）整理；端点为 TypeSafe System One 与 OpenRouter Decisions API。请保留上游署名与 MIT 许可。
