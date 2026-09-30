---
name: fullstack-architect
description: >-
  全栈架构与开发纪律。当用户只有一个模糊的灵感/想法需要收敛成 MVP、想快速实现 MVP/原型验证、MVP 跑通后要做项目基建或技术选型、开发新功能、重构、引入新库、修 bug/排查报错/处理逻辑漏洞、代码或架构审查时，必须使用本 skill。Use this skill whenever the user mentions a vague idea, MVP, prototype, 基建, tech stack selection, new feature, refactor, bug fix, debugging, or code/architecture review — even if they don't explicitly ask for architecture guidance, and even if the request is just "我有个想法" or "帮我加个功能".
---

# Architecture & Development Rules

以下全局限制条件（Anti-patterns）的适用严度以下方「Stage Detection」表为准；基建化/存量演进阶段绝不妥协：

- **TypeScript 全栈工程约束**：处理 TypeScript/JavaScript 全栈项目时，必须先读取并执行 [references/typescript-fullstack-constraints.md](references/typescript-fullstack-constraints.md)。
- **禁止引入多种范式 (No Multiple Paradigms)**：引入新模式前必须对齐现有最佳实践；若新模式更优，必须同步重构旧代码拉齐范式。
- **禁止硬编码和重复定义 (No Duplication)**：常量、枚举、数据库模型、Type/Interface 必须维持唯一可信来源 (SSOT)。
- **禁止复制粘贴 (No Copy-Paste)**：发现相似逻辑，必须先提取基类、公共函数、Hooks 或中间件，然后才允许编写新逻辑。
- **禁止任何妥协 (No Compromises)**：基建化/存量演进阶段绝不允许留下 `TODO: fix later`，不允许忽略异常捕获，不允许使用临时 Hack；MVP 验证阶段降级为「每个妥协必须当场登记 `DEBT.md`」。

# Stage Detection (先判定阶段)

路由工作流之前先判定项目阶段。**默认直接问用户一句**；用户不确定时按兜底信号取较高档：

- 有真实用户 / 外部数据 / 多人协作 / 收入 → 至少按「基建化」执行。（取高档是因为降档容易升档难：按低档执行会埋下未登记的妥协，事后无法追溯。）

| 阶段 | 判定信号 | 铁律适用度 |
|---|---|---|
| **灵感构思** | 只有模糊想法，无明确需求 | 不适用（不写代码、不选型） |
| **MVP 验证** | 验证想法闭环，无真实用户/数据风险 | 放宽：允许 hack/硬编码/复制粘贴，但每个妥协必须登记 `DEBT.md` |
| **基建化 / 存量演进** | MVP 已跑通、有明确业务需求、或命中兜底信号 | 全部铁律 |

**阶段晋升**：MVP 命中兜底信号时，提示晋升审计——全量清偿 `DEBT.md`、评估三方库留换、规划数据迁移——然后切换到基建档。

# Skill Router

阶段 × 意图二维路由。所有工作流以阶段判定为输入：审查与修复的严度跟随阶段，不对 MVP 阶段代码套用基建档验收标准（已登记 `DEBT.md` 的妥协不重复报警）。根据用户请求意图，阅读对应的参考工作流：

- **当用户只有[模糊的灵感/想法]，需要构思、找边界、收敛为 MVP 种子时**：
  读取[references/workflow-ideation.md](references/workflow-ideation.md)
- **当任务是[快速实现 MVP]、[验证想法闭环]（且阶段判定为 MVP 验证）时**：
  读取[references/workflow-mvp.md](references/workflow-mvp.md)
- **当任务是[增加新功能]、[重构现有代码]、[引入新库] 时**：
  读取[references/workflow-feature.md](references/workflow-feature.md)
- **当任务是 [修复报错]、[排查异常现象]、[处理逻辑漏洞] 时**：
  读取[references/workflow-bugfix.md](references/workflow-bugfix.md)
- **当任务是 [代码审查]、[架构审查]、[PR Review]、[安全/类型/边界合规性检查] 时**：
  读取[references/workflow-review.md](references/workflow-review.md)

读取工作流后，若任务涉及 TypeScript/JavaScript 全栈代码，继续读取 [references/typescript-fullstack-constraints.md](references/typescript-fullstack-constraints.md)，并把其中的边界隔离、DTO、运行时校验、SSOT、Result 错误模型和环境变量校验要求纳入预案与实现验收。（MVP 验证阶段除外：该阶段以 workflow-mvp.md 纪律为准，工程约束仅作参考，差距登记 `DEBT.md`。）

**⚠️ 全局中断铁律**：
无论执行上述哪个工作流，在输出真实代码前，**必须先输出 Markdown 格式的《执行预案》并暂停**。未经用户回复确认指令（如 `[1]`），**严禁**直接输出任何实现代码。（暂停的意义：代码一旦输出，错误方向的沉没成本远高于一次确认；预案是用户能在爆炸半径确定前否决方向的唯一时机。）
