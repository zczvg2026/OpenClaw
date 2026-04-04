---
name: extract-skill
version: 1.0.0
description: "每轮/定时记忆提取 — 从会话中实时抽取值得持久化的信息，写入 memory/ topic 文件。"
author: dazhaxie
---

# Extract Skill 🦀🧠

## 与 Dream Skill 的关系

| | Dream（做梦） | Extract（提取） |
|--|-------------|----------------|
| 触发 | 每24小时 cron | 每30分钟 cron |
| 任务 | 深度整合、Prune、纠错 | 实时捕获新记忆 |
| 范围 | 全量记忆文件 | 仅新消息（相对上次） |
| 时效 | 深夜批处理 | 日常增量 |

两者互补：Extract 负责"进来"，Dream 负责"整理"。

## 触发时机

- cron：`every 30m`，在 07:00–23:00 之间
- 独立 isolated session，不打扰主会话
- cron id：`extract-realtime-memory`

## 核心机制

### 1. Cursor（断点）

存储在 `memory/.dream-lock.json` 里：
```json
{
  "dreaming": false,
  "lastDreamedAt": "...",
  "lastExtractedAt": "2026-04-02T07:00:00+08:00"
}
```

每次提取后更新 `lastExtractedAt`。

### 2. 新消息识别

读取当前会话 transcript（飞书主会话），过滤：
- 时间戳 > lastExtractedAt 的消息
- 排除自己的心跳消息（内容含 HEARTBEAT_OK 的）

### 3. 提取 prompt（4类记忆）

参考 Claude Code 的 taxonomy：

**user**：用户角色、偏好、背景
- 例："Johnson 出差常去杭州、上海" → user-johnson.md

**feedback**：对的工作+错的工作（含 Why）
- 例："太擎≠太驍，字不要写错" → key-lessons.md

**project**：项目进展、决策、截止日期
- 例："决定落地 extractMemories" → projects.md

**reference**：外部系统信息（工具配置、API路径）
- 例："IMA ClientID 是..." → 已存在则跳过

### 4. 工具约束

- Read/grep/glob：不限
- Bash：只读（ls/find/cat/stat/wc/head/tail）
- Edit/Write：只能写 memory/ 目录下的 .md 文件

### 5. 互斥检测

如果 Extract 执行期间，主 agent 写了记忆（通过检查 memory/ 目录的 mtime），则本次跳过，避免重复写入。

## 文件结构

```
workspace/
├── memory/
│   ├── .dream-lock.json     # 含 lastExtractedAt
│   └── *.md                # topic 文件
└── skills/extract-skill/
    └── EXTRACT_PROMPT.md
```

## 注意事项

- 只提取消息内容，不重复造轮子
- 发现重复 topic 直接更新，不创建新文件
- 输出：一句话摘要，不发飞书消息
