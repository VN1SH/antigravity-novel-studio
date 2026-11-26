---
name: novel-audit
description: >-
  Use this skill when the user wants to audit existing novel chapters for plot
  holes, continuity errors (吃书), character OOC/state drift, timeline conflicts,
  review all open foreshadowing threads and hidden subplots (伏笔与暗线盘点),
  rebuild or consolidate the Lorebook-style worldbook (酒馆式世界书) and
  per-chapter summaries (每章总结) from existing chapters/outlines, or run a
  user-defined prose review based on codex/review_criteria.md with Editor
  triage/veto.
---

# 全书审计：逻辑查吃书、世界书与每章总结补建、伏笔暗线盘点及自定义审校

本流程由**执行主编（Coordinator）**统筹把关，按作者需要调度：
- **客观档案员（`archivist`，模型读取 `NOVEL_STATUS.md` 或由用户指定）**：负责事实核验、查吃书、伏笔与暗线盘点、补建/整理 `codex/chapter_summaries.md` 与酒馆式 `codex/worldbook.md`；
- **自定义审查官（`reviewer`，可选，模型由用户指定）**：严格按照用户自行编写的 `codex/review_criteria.md` 与 `codex/style_guide.md` 扫描目标章节；
- **执行主编（你）**：复核档案员报告、对审查官建议行使**合理采纳 / 不合理驳回**的裁决权，并汇总落地修补方案交由作者拍板。

## 执行步骤

### 第一步：识别审计模式与模型配置
读取项目 `NOVEL_STATUS.md` 中的模型配置（如尚未配置，先调用 `swarm(action="models")` 请用户自由指定，未指定则默认继承主模型），并确认本次审计模式：
- **模式 A（逻辑查吃书 & 伏笔/暗线盘点）**：召唤 `archivist`。
- **模式 B（存量小说补建/升级世界书与每章总结）**：作者已有若干已写章节，由 `archivist` 逐章通读 `chapters/` 与 `outline/master_outline.md`，一次性补齐 `codex/chapter_summaries.md`（每章总结）、`codex/timeline.md`、`codex/foreshadowing.md`（明坑+暗线）以及带触发关键词的酒馆式 `codex/worldbook.md`（准确区分 `[正文已确立]` 与 `[仅大纲·未公开]`），使后续写作立刻享受长篇滑动窗口机制。
- **模式 C（按用户自定义标准执行专项文本审校）**：确认 `codex/review_criteria.md` 已填写（若为空则先请用户提供审查标准），召唤 `reviewer` 扫描目标章节。
- **模式 D（全套体检）**：同时派发 `archivist` 与 `reviewer` 并行扫描目标章节（两者均只读 `chapters/`，互不冲突）。

### 第二步：派发审查任务
先查 `swarm(action="status")`，复用已有 `waiting` 状态的 worker，未创建再按用户模型配置 `swarm(action="add", workspace="inherit", ...)` 拉起：

1. **派给 `archivist`**（模式 A / B / D）：
   要求保持**绝对客观（只摆事实与原文证据，不擅自修改正文）**，输出《全书客观一致性、世界书与伏笔暗线审计报告》：
   - **🔴 明确矛盾 / 疑似吃书项**：精确标注冲突涉及的两处章节与原句摘录（时间线、地点移动、道具、规则、信息差、以及正文与大纲冲突处）。
   - **🟡 未填伏笔与幕后暗线盘点**：分别列出所有 `[未填]` / `[推进中]` 的明面伏笔（`Fxxx`）、幕后暗线（`Sxxx`）及正文中埋下但漏记的线索，按沉睡章数与紧迫度排序。
   - **📘 世界书与每章摘要完备度检查**：检查 `codex/chapter_summaries.md` 是否覆盖全部已写章节、检查 `codex/worldbook.md` 是否完整提炼了正文与大纲设定且正确标注了 `[正文已确立]` / `[仅大纲·未公开]`（模式 B 直接完成补建与词条化整理）。

2. **派给 `reviewer`**（模式 C / D）：
   要求严格对照用户自己的 `codex/review_criteria.md` 与 `codex/style_guide.md` 逐章扫描，**只读不改正文**，输出带编号（`#1`, `#2`...）的修改建议清单提交主编裁决。

### 第三步：主编审核裁决与向作者交付方案
你（主编）先核验档案员与审查官的全部产出，再向作者呈现清晰的执行方案：
1. **高危吃书修复选项**（如有）：针对每条 🔴 矛盾给出 1–2 个最小改动方案，**等作者拍板后再安排修改**，绝不擅自改已定稿正文。
2. **伏笔与暗线填坑排期建议**（如有）：结合 `outline/master_outline.md` 规划后续各章分别推进哪些暗线、回收哪些伏笔。
3. **对自定义审查建议先行逐条裁决**（如有）：标出 **✅ 合理采纳** 与 **❌ 不合理驳回**（附理由），经作者确认后再派 `writer` 按核准清单定向精修，并由你终审定稿。
4. **世界书与每章总结核验确认**：向作者汇报本次补齐/更新的每章摘要与酒馆式世界书词条清单。
