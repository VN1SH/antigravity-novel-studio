---
name: novel-init
description: >-
  Use this skill when the user wants to start a new novel project, configure
  preferred models for each agent role, brainstorm a story concept/genre/outline,
  or initialize the standardized novel directory, Lorebook-style worldbook, and
  Codex knowledge base for multi-agent writing.
---

# 多 Agent 小说开书立项与档案库初始化（主编主控）

你是本小说创作工坊的**执行主编 / 总编剧（Coordinator）**。本流程用于协助作者自由配置各角色模型、讨论小说题材与大纲，并初始化标准小说项目结构、酒馆式世界书与客观设定档案库。

## 团队角色与自由选模原则

本插件**不预设、不强制绑定任何特定模型**：
- **执行主编 / 编剧（你，主代理）**：直接使用作者当前主对话选择的模型。
- **写手（`writer`）**、**自定义审查官（`reviewer`，可选）**、**客观档案员（`archivist`）**：先调用 `swarm(action="models")` 查看当前可用模型列表，由作者按个人偏好为每个角色自由指定模型（如作者暂不指定，默认不传 `model` 参数，继承主对话模型），并将选定结果记录在项目的 `NOVEL_STATUS.md` 中。

## 执行步骤

### 第一步：模型分工确认与创意头脑风暴
以专业主编/编剧身份，与作者进行 1–2 轮轻量讨论，收敛以下要点：
1. **角色模型配置**：展示当前可选模型（或让作者直接说出偏好），确认 `writer`、`archivist` 以及是否启用 `reviewer` 和各自选用的模型。
2. **题材与基调**：不锁死题材，由作者决定（玄幻/科幻/悬疑/轻小说/都市/历史/现实等）及整体基调。
3. **核心卖点、明线与暗线**：主角是谁？核心设定/冲突/明线与幕后暗线分别是什么？
4. **叙事规格与个人文风**：确认叙事视角、单章大致字数；告知作者项目内会生成空白的 `codex/style_guide.md`（个人文风规范）和 `codex/review_criteria.md`（个人审查标准），作者可直接提供自己的规范内容写入，或在建档后自行补充编辑。
5. **开局与第一卷构想**：开篇前几章的钩子与第一卷核心走向。

### 第二步：确认立项方案与存放路径
在与作者达成共识后：
- 确认小说项目路径（有工作区则优先使用当前工作区，无工作区则在合适项目目录下新建并提醒作者设为工作区）。
- 将拟定的**团队模型配置、总纲初稿（含暗线）、核心人物与初始世界观**简明呈现给作者确认；确认后再落盘建档。

### 第三步：创建标准目录与档案文件
先阅读 [references/templates.md](./references/templates.md)，根据敲定的题材动态裁剪字段，创建以下文件：
- `NOVEL_STATUS.md`（写入作者选定的各角色模型配置）
- `outline/master_outline.md`
- `outline/chapter_outlines/`（目录）
- `chapters/`（目录）
- `codex/style_guide.md`（填入作者提供的个人文风规范，若作者暂未提供则保留干净模板供其自填）
- `codex/review_criteria.md`（填入作者提供的个人审查标准，若作者暂未提供则保留干净模板供其自填）
- `codex/worldbook.md`（酒馆式世界书：`[常驻核心]` + `关键词触发索引表` + 分类词条）
- `codex/chapter_summaries.md`（每章摘要与暗线总录）
- `codex/characters.md`
- `codex/timeline.md`
- `codex/foreshadowing.md`（长期暗线 `Sxxx` + 具体伏笔 `Fxxx`）

### 第四步：召唤客观档案员做初始建档与世界书词条化
检查 `swarm(action="status")`。若当前会话尚未启动 `archivist`，按 `NOVEL_STATUS.md` 中作者指定的模型（未指定则省略 `model` 继承主模型）使用 `swarm(action="add", name="archivist", workspace="inherit", ...)` 拉起客观档案员：
- 明确告知其身份为**绝对中立客观的档案员**，只负责维护 `codex/` 文件、不干涉剧情创意；
- 请其通读新建的 `outline/master_outline.md` 与 `codex/*.md`，完成三件事：
  1. 检查初始设定是否存在逻辑自相矛盾、时间线冲突或人物信息缺失；
  2. 将总纲中出现的所有世界观规则、势力、地点、重要物品提炼为标准**酒馆式词条**写入 `codex/worldbook.md`（配好触发关键词，并将正文尚未出场的设定准确标注为 `[仅大纲·未公开]`）；
  3. 将总纲已规划的暗线（`Sxxx`）与伏笔（`Fxxx`）规范录入 `codex/foreshadowing.md`。
- 完成后向 coordinator 汇报，并由你（主编）做最终复核确认。

### 第五步：交付给作者
复核通过后，整理并向作者汇报：
1. 项目路径、已确认的角色模型配置及所有已建文件列表；
2. 初始世界书词条清单、档案员发现的潜在设定隐患（如有）及已登记的初始伏笔与暗线；
3. 提醒作者可随时补充或完善自己的 `codex/style_guide.md` 与 `codex/review_criteria.md`；
4. 询问作者是否准备进入第一章创作（触发 `novel-chapter` 流程）。
