---
name: fullstack-architect
description: >-
  Use this skill when the user wants to add new features, refactor existing code, introduce new libraries, fix bugs, investigate errors, or handle logic vulnerabilities in a greenfield system.
---

# Architecture & Development Rules

严格执行以下全局限制条件（Anti-patterns），绝不妥协：

- **TypeScript 全栈工程约束**：处理 TypeScript/JavaScript 全栈项目时，必须先读取并执行 [references/typescript-fullstack-constraints.md](references/typescript-fullstack-constraints.md)。
- **禁止引入多种范式 (No Multiple Paradigms)**：引入新模式前必须对齐现有最佳实践；若新模式更优，必须同步重构旧代码拉齐范式。
- **禁止硬编码和重复定义 (No Duplication)**：常量、枚举、数据库模型、Type/Interface 必须维持唯一可信来源 (SSOT)。
- **禁止复制粘贴 (No Copy-Paste)**：发现相似逻辑，必须先提取基类、公共函数、Hooks 或中间件，然后才允许编写新逻辑。
- **禁止任何妥协 (No Compromises)**：由于是未上线的初版系统（无历史包袱），绝不允许留下 `TODO: fix later`，不允许忽略异常捕获，不允许使用临时 Hack。

# Skill Router

根据用户请求意图，阅读对应的参考工作流：

- **当任务是[增加新功能]、[重构现有代码]、[引入新库] 时**：
  读取[references/workflow-feature.md](references/workflow-feature.md)
- **当任务是 [修复报错]、[排查异常现象]、[处理逻辑漏洞] 时**：
  读取[references/workflow-bugfix.md](references/workflow-bugfix.md)

读取工作流后，若任务涉及 TypeScript/JavaScript 全栈代码，继续读取 [references/typescript-fullstack-constraints.md](references/typescript-fullstack-constraints.md)，并把其中的边界隔离、DTO、运行时校验、SSOT、Result 错误模型和环境变量校验要求纳入预案与实现验收。

**⚠️ 全局中断铁律**：
无论执行上述哪个工作流，在输出真实代码前，**必须先输出 Markdown 格式的《执行预案》并暂停**。未经用户回复确认指令（如 `[1]`），**严禁**直接输出任何实现代码。
