# HEARTBEAT.md

# Keep this file empty (or with only comments) to skip heartbeat API calls.

# Add tasks below when you want the agent to check something periodically.

## 每日Eval自检

每日起床后（第一次心跳），对照6条Eval自检昨天的工作：

| # | 检查项 | 昨日得分 |
|---|--------|----------|
| 1 | 交付前停顿，逐条过 Eval？ | — |
| 2 | 先搜索记忆？ | ✅ |
| 3 | 结论先行？ | ✅ |
| 4 | 在能力范围内？ | ✅ |
| 5 | 不确定时说"不确定"？ | ✅ |
| 6 | 简洁？ | ✅ |
| 7 | 幽默？ | ✅ |

**新增必检项（2026-03-31）：**
| 7 | 读图/读文件成功才分析？ | ✅ |
| 8 | 路径/特殊字符优先用 write？ | ✅ |
| 9 | 交付前停顿已养成习惯？ | — |

做得好保持，做得不好记录到 .learnings/LEARNINGS.md 并反思改进。

## 每日IMA日记（必做）

每天 **22:30 前** 将当日最重要的 3-5 件事写入 IMA 日记（note_id: 7445856324188686）。

**流程：**
1. 读取 `memory/YYYY-MM-DD.md` 当日笔记
2. 提炼成结构化日记（最重要的一件事 + 主要工作内容 + 教训 + 明日展望 + 金句）
3. 调用 IMA append_doc 接口写入

**凭证预检：** 先验证 IMA_OPENAPI_CLIENTID / IMA_OPENAPI_APIKEY 可用（凭证偶发失效，遇 20004 直接重新生成 Key）。

- compaction 已配置 safeguard + memoryFlush，会自动触发
- 仅当 session 对话轮次明显过长（>20轮）时，主动提议压缩
- **不要**每次心跳都读 memory/ 文件查日期，避免 token 浪费
