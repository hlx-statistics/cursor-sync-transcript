# cursor-sync-transcript

基于 Cursor [Hooks](https://cursor.com/docs/hooks)，在每次智能体回答完毕(`afterAgentResponse` )后，将会话内容100%同步到项目内 `chat/YYYYMMDD--<conversation_id>.md`文件

---
## 🤖 自动安装

复制给 cursor agent 的内容：

```text
请在本项目（当前工作区根目录）安装 Hook：打开 GitHub 仓库 https://github.com/hlx-statistics/cursor-sync-transcript.git ，严格按该仓库 README 里「📋 给 Cursor Agent 的安装任务说明」中的清单执行；从当前分支拉取 `sync-transcript.mjs` 文件并合并 `.cursor/hooks.json`文件，不要覆盖我已有的其它 Hook。
```

### 📋 给 Cursor Agent 的安装任务说明

**目标**：在用户当前打开的**工作区根目录**（单文件夹工作区即该文件夹路径）部署本 Hook，使助手每轮回复结束后自动生成 `chat/YYYYMMDD--<conversation_id>.md`。

**约束**：

- 所有路径相对于工作区根；`hooks.json` 中的 `command` 必须为：`node .cursor/hooks/sync-transcript.mjs`（与本文档一致）。
- `node` 须在用户环境中可用（`node -v`）。
- 若已存在 `.cursor/hooks.json`，**禁止整文件覆盖**：只合并 `hooks.afterAgentResponse`，保留其余键与其它 Hook。
- **幂等**：若 `afterAgentResponse` 数组里已有任一元素的 `command` 等于 `node .cursor/hooks/sync-transcript.mjs`，则不再追加该项。
- 创建目录：`mkdir -p`（POSIX）或 `New-Item -ItemType Directory -Force`（PowerShell）。

#### 📌 Agent 安装清单

1. 令 `ROOT` = 工作区根目录（ Cursor 打开的文件夹）。
2. 确保目录存在：`ROOT/.cursor/hooks/`。
3. 将本仓库中的 `.cursor/hooks/sync-transcript.mjs` 写入 `ROOT/.cursor/hooks/sync-transcript.mjs`（内容与 upstream 一致）。
4. 处理 `ROOT/.cursor/hooks.json`：
   - **不存在**：写入本文「🛠️ 手动安装：脚本路径与 hooks 配置」一节中的 `hooks.json` 示例全文。
   - **已存在**：解析 JSON；若无顶层 `"version"`，设为 `1`；若无 `hooks`，设为 `{}`。将 `hooks.afterAgentResponse` 视为数组（缺失则 `[]`）；按上文幂等规则追加本条 command；写回文件且保持合法 JSON 格式。
5. 告知用户：若 Hook 未触发，可重启 Cursor；可按本文「✅ 安装后自检（确认已生成 md）」一节检查 `ROOT/chat/*.md`。


---

## 📁 项目结构

| 路径 | 说明 |
|------|------|
| `.cursor/hooks.json` | 注册 `afterAgentResponse` |
| `.cursor/hooks/sync-transcript.mjs` | 导出脚本 |
| `LICENSE` | MIT 许可全文 |

---

## 🛠️ 手动安装：脚本路径与 hooks 配置

在工作区根目录：

1. 放置脚本：将 `sync-transcript.mjs` 保存为 `.cursor/hooks/sync-transcript.mjs`（无目录则先建 `.cursor/hooks/`）。
2. 配置 Hook：编辑 `.cursor/hooks.json`。若没有该文件，可直接使用下列全文；若已有其它 Hook，在 `hooks.afterAgentResponse` 数组里**追加**一条（勿重复相同的 `command`），勿删掉原有条目。

```json
{
  "version": 1,
  "hooks": {
    "afterAgentResponse": [
      {
        "command": "node .cursor/hooks/sync-transcript.mjs"
      }
    ]
  }
}
```

从 GitHub 安装时，在仓库里打开同名路径的文件复制即可；无需额外脚本。

---

## ⚙️ 运行前要满足的环境（Cursor 与工作区）

- Cursor 支持 Hooks；建议在 Composer / Agent 会话中验证。
- `node` 在 `PATH` 中。
- 用 Cursor **打开文件夹**作为工作区；多根工作区以 stdin `workspace_roots[0]` 为准。

---

## ✅ 安装后自检（确认已生成 md）

助手回复结束后，在工作区根下应出现 `chat/YYYYMMDD--<conversation_id>.md`。未生效时可重启 Cursor。

---

## 📤 导出文件路径与 Git 提交建议

| 路径 | 说明 |
|------|------|
| `chat/YYYYMMDD--<conversation_id>.md` | 会话 Markdown |

若不希望把聊天记录提交到远端，可将 `chat/` 加入 `.gitignore`。

---

## 📝 生成 Markdown 的格式说明

**示例文件**：本仓库内真实记录导出见 [`chat/20260515--f0f4a38e-0c2f-4ef4-8780-ccc333db55d5.md`](chat/20260515--f0f4a38e-0c2f-4ef4-8780-ccc333db55d5.md)。

---

## 🗑️ 卸载 Hook 与清理导出文件

从 `.cursor/hooks.json` 移除对应 `afterAgentResponse` 项；按需删除 `.cursor/hooks/sync-transcript.mjs` 与 `chat/`。

---

## 📄 许可证

本仓库采用 **MIT License**，Copyright (c) 2026 HLX；条款全文见根目录 [`LICENSE`](LICENSE)。
