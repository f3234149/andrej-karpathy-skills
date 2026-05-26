# AGENTS.md

## 工作区概览
- 本工作区在 `D:\WorkSpace\openapi` 下包含三个独立项目：
  - `interface-platform`：基于 Egg.js 的服务端/平台侧项目，负责对外暴露并维护 API。
  - `api-demo-node`：客户端/调用方 Node.js 示例项目，负责提供请求调用示例与集成用法。
  - `api-demo-java`：客户端/调用方 Java 示例项目，负责提供请求调用示例与集成用法。
- 协作基线：先确认服务端 API 契约，再对齐客户端调用。

## 仓库边界
- 将 `interface-platform`、`api-demo-node` 和 `api-demo-java` 视为独立项目，并分别控制变更范围。

## 默认工作规则
- 先读后改：编辑前先检查现有代码路径和配置。
- 优先做最小且聚焦的修改；除非明确要求，避免大范围重构。
- 除非任务明确要求破坏性变更，否则保持行为兼容。
- 未经明确批准，不要执行破坏性的 git/文件操作，例如强制重置、改写历史、批量删除。
- 如存在不确定性，在回复中说明假设，并通过可运行检查进行验证。

## 项目命令
- `interface-platform` (from `D:\WorkSpace\openapi\interface-platform`):
  - 安装依赖：`npm i`
  - 开发启动：`npm run dev`
  - 测试：`npm test`
  - 代码检查：`npm run lint`
  - 启动守护进程：`npm start`
  - 停止守护进程：`npm stop`
- `api-demo-node` (from `D:\WorkSpace\openapi\api-demo-node`):
  - 安装依赖：`npm i`
  - 如果后续新增可运行示例，请添加对应的 `scripts`，并更新 README 用法说明。

## API 集成流程
- 推荐顺序：
  1. 确认 `interface-platform` 的 endpoint 行为已实现，并可在本地访问。
  2. 固定 API 契约细节：path、method、headers、payload、signature、response fields 和 error codes。
  3. 按照已固定的契约实现或更新 `api-demo-node` 的调用流程。
  4. 使用真实的 request/response 示例进行端到端验证。
- 任何 API 契约变更，都必须在同一任务中同步更新客户端示例，或明确记录为后续事项。

## 变更范围与提交规范
- 每个任务输出都应明确说明：
  - 目标项目
  - 已变更的行为/接口
  - 已执行的验证
- 保持 commit/review 聚焦；避免将无关清理与功能/修复工作混在一起。
- 对于涉及多个项目的任务，在变更摘要中按项目分别说明实现思路。

## 验证清单
- 完成前至少验证：
  - 目标服务或脚本可以成功启动。
  - 与任务相关的关键 API path 可调用，并返回预期结构。
  - 如果包含客户端变更，请确认请求示例流程可以成功执行。
  - 文档中使用的命令与实际 `package.json` scripts 保持一致。

## 语言与文档约定
- 为方便团队阅读，文档和注释以中文为主。
- 关键技术标识保持英文，例如 API fields、command names、code symbols。
- 文档保持简洁、可执行，并与真实项目行为同步。
