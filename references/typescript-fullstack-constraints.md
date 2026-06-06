# TypeScript Fullstack Constraints

在 TypeScript/JavaScript 全栈项目中，除非用户明确要求放宽，否则将以下约束视为架构验收标准。若现有代码不满足，预案必须指出差距，并把必要的修正纳入实现范围。

## Architecture Boundaries

- **前后端物理隔离**：前端不得直接 `import` 后端运行时代码。只允许 `import type` 引入后端的 `type`/`interface`，且更推荐把共享契约放入 `packages/shared`、`packages/contracts` 等跨平台包。
- **共享包跨平台**：常量、枚举、工具函数、Zod/TypeBox Schema 等需要前后端复用的静态逻辑必须进入共享包；共享包不得依赖 Node-only API、Browser-only API、密钥读取或具体框架运行时。
- **动态数据走 API**：前端读取后端配置、权限、业务状态或动态数据时，必须通过 HTTP/RPC/tRPC 等接口，不得绕过 API 边界。
- **ESLint 强制边界**：涉及导入边界调整时，优先补充 `no-restricted-imports`、`import/no-cycle` 或等价规则，在 CI 中阻断违规导入和循环依赖。

## DTO and Domain Isolation

- **实体不直出**：禁止把数据库 Entity、ORM Model 或 DB Schema 类型直接作为 API 响应暴露给前端。
- **DTO 明确转换**：后端必须通过 Mapper 将数据库实体转换为 RequestDTO/ResponseDTO，只返回 API 契约需要的字段，避免泄露密码哈希、软删除标记、内部审计字段等敏感数据。
- **领域逻辑纯洁性**：核心 Service/Domain 层保持纯 TypeScript，不直接 import ORM、HTTP 客户端、框架对象或环境变量读取逻辑。数据库和三方服务通过 Adapter/Repository/Port 接口注入。

## Type Safety

- **零容忍 `any`**：不得新增 `any`。无法预知结构时使用 `unknown`，通过 Zod/TypeBox 解析、类型守卫或判别联合收窄后再使用。
- **谨慎类型断言**：避免无理由的 `as Type`。需要断言时必须有边界校验、框架限制或不可表达类型关系作为依据。
- **开启 strict**：新增或调整 TypeScript 配置时必须保持 `"strict": true`，不得通过关闭严格选项绕过错误。
- **品牌类型**：核心业务中的同构 ID 不得裸用 `string`/`number`。优先使用品牌类型区分 `UserId`、`OrderId`、`TenantId` 等领域标识，避免误传导致越权或数据串域。

## Runtime Boundary Defense

- **边界必须校验**：API 入口、三方回调/响应、文件读取、环境变量、数据库读取和跨进程消息都视为不可信边界，必须做运行时校验。
- **Schema 作为 SSOT**：优先使用 Zod 或 TypeBox 定义运行时 Schema，并通过 `z.infer` 或等价方式反推 TS 类型，避免手写类型与校验规则分叉。
- **前后端复用校验**：表单校验、API Body 校验、DTO 契约和共享枚举应复用同一 Schema/契约定义；后端仍必须独立校验，不能信任前端校验。

## Data Consistency and Organization

- **SSOT**：同一业务概念、规则、枚举、类型和校验逻辑只能有一个可信定义。发现重复定义时优先收敛到共享包、生成代码或 API 契约层。
- **职责归属**：跨模块通用逻辑进入共享层；特定业务逻辑放在 feature/domain 内部；底层工具不得反向依赖业务模块。
- **端到端契约**：适合时优先采用 tRPC、OpenAPI 生成、共享 DTO Schema 或等价机制，让数据库 Schema、API 契约和前端类型形成闭环。

## Error and Config Discipline

- **业务错误不用 throw 控流**：核心业务层的预期错误应作为显式返回值处理，例如 `Result<Data, BusinessError>`、判别联合或项目已有等价模式。仅不可恢复的系统级故障交给全局异常处理。
- **catch 中按 unknown 处理**：捕获异常后必须先收窄类型，不得假设 `error.message` 一定存在。
- **环境变量集中校验**：禁止在业务代码中散落读取 `process.env.X`。应用启动早期必须通过集中 `config/env.ts` 或等价模块校验环境变量，失败时 fail-fast，并导出只读配置对象。

## Proposal and Review Checklist

在执行预案、代码实现和最终验收中检查：

- 是否存在前端运行时代码导入后端模块的风险。
- API 是否通过 DTO/Mapper 隔离数据库实体。
- 新增共享逻辑是否位于跨平台共享包，且没有平台专属依赖。
- 所有外部输入是否经过运行时 Schema 校验。
- 是否新增 `any`、无依据断言、裸 ID 或重复类型定义。
- 业务错误是否显式建模，环境变量是否集中校验。
