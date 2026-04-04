---
scope: private
---

# 关键经验教训

## 【纠正】遇到陌生内容任务，先查专用技能再动手（2026-04-04，已重复）
- **问题**：读微信公众号文章，第一反应是 curl/web_fetch/搜索——全都失败了，最后才发现 wechat-article-parser 一直躺在 skills 里
- **Why**：专用工具 vs 通用方案的获取代价完全不同；专用工具失败率低、解析质量高，通用方案费时还拿不到内容
- **How to apply**：遇到任何"如何获取 X 内容"类型任务，第一步查 `~/.openclaw/workspace/skills/` 有没有对应 parser/scraper，再用通用方法兜底
- **复发记录（2026-04-04 14:36）**：Johnson发来BotLearn文章（mp.weixin.qq.com），我又用了 curl/web_fetch/浏览器/summarize 全套，最后Johnson忍不住说"你又忘了，你有微信反爬技能的啊"——再次确认这是系统性遗忘，不是偶然
- **复发记录（2026-04-04 15:16）**：第三次！Johnson发BotLearn文章，我又先用浏览器工具/summarize/curl 折腾一圈，最后Johnson再次提醒——已经是"又"字辈了
- **复发记录（2026-04-04 16:56）**：第四次！同一篇文章，同一个错误。系统事件显示 wechat-article-parser 已在运行，但 assistant 第一反应仍是 curl/浏览器/summarize——说明不是"不知道工具有没有运行"的问题，而是"任务一来就忘记先查 skills/"的本能反应问题。Johnson 原话："你又忘了"。
- **复发记录（2026-04-04 17:56）**：第五次！Johnson 发来 BotLearn 文章 URL，assistant 又经过：浏览器打开失败→summarize→curl→搜索→web_fetch→Agent Browser 安装→尝试 firecrawl→再次尝试 summarize→再 curl→再 web_fetch，全套走完 Johnson 忍不住说"你又忘了"——整整 10 轮失败迭代才想起来。wechat-article-parser 当时已经在 skills/ 里可用。
- **复发记录（2026-04-04 18:36）**：第六次！同一篇文章，wechat-article-parser 已在运行，但 assistant 第一反应仍是先折腾 curl/浏览器/summarize全套。Johnson 原话："你又忘了"（第三次说"又"）。
- **根因**：记忆里已有条目，但遇到实际任务时没有触发检索习惯。需要：任务失败后的复盘中强制检查 skills/ 目录，而不是直接换方法
- **预防机制**：在任何内容获取任务的第一步，先查 skills/ 再动手，已加入 TOOLS.md

---

## 【反馈】opencli Chrome扩展技术限制（2026-04-04）
- **问题**：daemon 要求 `X-OpenCLI: 1` header，但 Chrome 扩展 v1.5.5 没带，导致 Forbidden
- **Why**：Chrome扩展安装需要图形界面操作（Developer Mode → Load unpacked），无法自动化；版本匹配问题（扩展和daemon版本差了一代）
- **How to apply**：Chrome扩展类工具（opencli类）依赖Johnson手动在Chrome GUI安装；我们有 wechat-article-parser 已解决微信文章读取，opencli非阻塞

---

## 【反馈】isolated cron session 不能主动发飞书消息，必须用 main session（2026-04-03 深夜）

**问题**：daily-diary-preview cron（isolated session）生成日记成功，但 announce 发送失败，报错：
> `Channel is required when multiple channels are configured: feishu, openclaw-weixin`

**Why**：
- isolated session 没有飞书聊天上下文，不知道往哪个 chat/用户发消息
- announce 机制依赖"上一个活跃聊天"的上下文，isolated session 没有这个
- `--channel feishu` 只解决了 channel 选择，但 `to`（发给谁）的问题 isolated session 无法推导

**How to apply**：
- 需要主动推送消息到飞书的 cron → 必须用 `--session main` + `--system-event "<trigger>"`，不能用 isolated
- main session 有完整聊天上下文，可以直接用 `message` 工具发送
- systemEvent 触发后，主 agent 收到消息，执行任务，用 `message` 工具推送结果

---

## 【确认】视频号混合拍摄模式（2026-04-04）
- **规则**：Johnson 只拍一次，拆成多段复用；其他所有内容（脚本/素材/剪辑）我全包
- **Why**：效率最优解，真实感 + 最小时间投入兼顾
- **How to apply**：视频号流水线默认模式，每次 E0x 启动前确认素材复用

---

## 【确认】IMA 日记写入打通（2026-04-03 深夜）
- **写入成功**：note_id: 7445856324188686，2026-04-03 日记
- **Skill 路径**：`/Users/mac/.openclaw_backup/workspace/skills/ima-note/SKILL.md`
- **配置**：`~/.config/ima/` 下有 `client_id` 和 `api_key`
- **环境变量**：`IMA_OPENAPI_CLIENTID`、`IMA_OPENAPI_APIKEY`
- **API endpoint**：`https://ima.qq.com/openapi/note/v1/import_doc`
- **How to apply**：完整流程通了；cron 推送后，等 Johnson 确认，再写入 IMA

---

## 【确认】Johnson 每天早上要 Dream 汇报（2026-04-04）
- **规则**：每天早上起床后主动汇报大闸蟹做梦（Dream 记忆整合）的结果
- **Why**：Johnson 希望通过 Dream 结果了解记忆整合情况，主动汇报是他的"检查机制"
- **How to apply**：Dream cron 完成后，在飞书主动推送一句话总结结果

---

## 【确认】口播脚本风格：更轻松（2026-04-04 凌晨）
- **规则**：脚本语气更轻松，少用"我是xxx创始人"，多用真实感受
- **Why**：Johnson 确认轻松风格更适合个人IP视频号
- **How to apply**：写口播文案时默认轻松口语风格，除非明确要求专业正式

## 【确认】Claude Code源码 + WeChat文章对比：Harness工程是AI能力差距的根本（2026-04-04）

- **规则**：工具本身不值钱，Harness（外壳工程）才值钱。同一个模型，Claude Code harness得78%，其他框架42%。
- **Why**：源码+文章联合分析揭示——权限分类引擎、Context管理、forked agent模式、双层记忆作用域，这些"非模型"层面的设计才是决定AI表现的关键。
- **How to apply**：后续能力建设优先做Harness层面的优化（P0权限确认、P1双层作用域已落地），而非追求更强模型

---

## 【确认】口播脚本风格：更轻松（2026-04-04 凌晨）
- **规则**：脚本语气更轻松，少用"我是xxx创始人"，多用真实感受口语化
- **Why**：Johnson 确认轻松风格更适合个人IP视频号，太正式会拉开距离感
- **How to apply**：写口播文案时默认轻松口语风格，除非 Johnson 明确要求专业正式

---

## 【确认】P0权限分级 + P1双层作用域双任务完成（2026-04-04 01:47）

- **规则**：两个子agent并行执行，全部完成，Johnson确认结果
- **Why**：Johnson说"这两个都做"，执行后确认产出符合预期
- **How to apply**：下次Johnson说"都做"时，直接并行spawn，不要串行

---

## 飞书文档公开分享（2026-04-04）
开启文档互联网阅读权限（任何人打开链接可读）：
- 先获取 tenant_access_token
- 调用 `PATCH https://open.feishu.cn/open-apis/drive/v1/permissions/{token}/public?type=docx`
- Body: `{"link_share_entity": "anyone_readable"}`
- 注意：`type` 必须放 query 参数，不能放 body

## 【反馈】auto-backup cron 陷阱：$(date) 在 crontab 里会被预计算（2026-04-04）
- **问题**：直接写 `git commit -m "auto-backup-$(date +%Y%m%d)"` 在 crontab 里，`$(date)` 会在**安装时**被计算，而非运行时
- **Why**：crontab 文件没有 shebang，不走 shell 解析，变量在安装时就被展开
- **How to apply**：涉及动态时间变量的 cron job，必须写成独立 shell 脚本再 cron 调用；不能用一行命令搞定
- **正确做法**：创建 `~/.openclaw/auto-backup.sh` 脚本，在脚本里用 `$(date)`，cron 只调用脚本路径

## IMA凭证失效处理（2026-04-04）
- 症状：所有IMA接口返回 code:20004 skill auth failed
- 解决：重新生成 Client ID + API Key（旧的被服务端吊销，无法预先感知）
- 凭证存 ~/.zshrc（不写进 workspace 文件）
- 下次遇到同样问题：直接重新生成，不用排查其他原因
