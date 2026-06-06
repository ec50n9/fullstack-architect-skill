# Review Workflow

当加载本文件时，采取严格代码审查视角：优先找会导致 Bug、回归、数据泄露、安全问题、类型失效、架构边界破坏或缺失测试的问题。不要把审查写成泛泛总结。

若项目是 TypeScript/JavaScript 全栈项目，同时读取 [typescript-fullstack-constraints.md](typescript-fullstack-constraints.md)，并把其中规则作为审查清单。

## Step 1: Scope

- 明确审查对象：用户指定的 diff、分支、提交、文件或整个工作区。
- 若用户未指定范围，优先审查当前工作区未提交 diff；若无 diff，再审查最近提交或用户上下文中提到的文件。
- 读取相关调用链和测试，不只看改动行；至少追踪到 API 边界、状态流转、Service/Domain、Adapter/DB 或外部系统交互中受影响的部分。

## Step 2: Findings

只报告有明确证据和实际影响的问题。每条发现必须包含：

1. **严重级别**：`Critical` / `High` / `Medium` / `Low`。
2. **位置**：文件和具体行号。
3. **问题**：当前代码会如何失败，或违反了哪条工程约束。
4. **影响**：用户可见后果、数据一致性风险、安全风险、类型安全风险或维护风险。
5. **修复方向**：简明说明应如何修正；不输出完整补丁，除非用户明确要求修复。

重点检查：

- 前端是否运行时导入后端模块，或共享包是否混入平台专属 API。
- API 是否直接返回 Entity/ORM Model，是否缺少 DTO/Mapper。
- 外部输入、数据库读取、三方响应、环境变量是否缺少运行时校验。
- 是否新增 `any`、无依据类型断言、裸 ID、重复类型/枚举/校验规则。
- 核心 Service/Domain 是否依赖 ORM、框架对象、HTTP 客户端或 `process.env`。
- 业务错误是否用 `throw Error` 控制流程，调用方是否无法静态处理。
- 是否存在并发竞态、事务边界错误、权限绕过、敏感字段泄露、缓存失效或状态不一致。
- 测试是否覆盖新行为、错误路径、边界输入和回归风险。

## Step 3: Output Format

输出顺序固定：

1. **Findings**：按严重级别从高到低列出。若没有发现问题，明确说“未发现明确问题”。
2. **Open Questions / Assumptions**：只列会影响判断的问题；没有则省略。
3. **Residual Risk / Test Gaps**：说明未验证或缺测试的地方。
4. **Change Summary**：只在用户需要了解背景时简短总结；不要让总结压过 findings。

## Step 4: Fix Follow-up

Review 默认只审查不改代码。若用户要求继续修复，切换到 [workflow-bugfix.md](workflow-bugfix.md) 或 [workflow-feature.md](workflow-feature.md)，先输出对应《执行预案》并暂停，等待用户确认后再写代码。
