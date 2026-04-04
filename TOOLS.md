# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## 工具调用规则

**生图 → MeiGen（mcporter）**
- 命令：`mcporter call creative-toolkit.generate_image -p "{prompt}" -a {比例} -r {分辨率}`
- 环境变量：`MEIGEN_API_TOKEN=meigen_sk_eUsJyeGZrcZ9e9R2gx5JQNjZLdhiKeeG`
- 模型：`nanobanana-2`（默认）

**生视频 → LibTV**
- Key：`sk-libtv-09eabe09c16e4cc08126bad086b23f62`
- Base URL：`https://im.liblib.tv`
- 注意：Seedance 2.0 需 VIP，可灵等备用

## 时间感知规则（自我约束）

- **不信任 metadata timestamp 做当前时间判断**
- 说"今天/明天/昨天"之前，**必须**执行 `date` 命令或 `session_status` 验证实际时间
- metadata 里的时间只代表"消息发送时间"，不代表 AI 的真实感知时间

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

## Meigen AI 生图（2026-04-02 确认）
- **网站**：https://www.meigen.ai
- **Token**：`MEIGEN_API_TOKEN=meigen_sk_eUsJyeGZrcZ9e9R2gx5JQNjZLdhiKeeG`（环境变量）
- **调用方式**：`mcporter call creative-toolkit.generate_image`（provider="meigen"）
- **可用工具**：
  - `mcporter call creative-toolkit.generate_image` → 生图
  - `mcporter call creative-toolkit.enhance_prompt` → 优化提示词
  - `mcporter call creative-toolkit.search_gallery` → 搜灵感
- **生成图片本地保存**：`~/Pictures/meigen/`
- **公网 CDN**：`https://images.meigen.art/generations/`
- ⚠️ **教训**：工具配置完成后**立即**写入 TOOLS.md，不要等下次调用时再说

---

## ⚠️ 任务执行第一条（必须遵守）

**遇到内容获取任务，第一步查 skills/，第二步再动手！**

已翻车3次教训（2026-04-04 × 3）：
- 微信公众号文章 → `skills/wechat-article-parser/` 有专用 parser，别用 curl/web_fetch/浏览器
- 通用方案失败率高，专用工具就在 skills/ 里躺着

---

Add whatever helps you do your job. This is your cheat sheet.
