# Project Context

## Purpose
这是一个 n8n 社区节点包项目，用于为 [Hyperliquid](https://hyperliquid.xyz) 交易平台提供 n8n 集成。项目目标是创建可复用的 n8n 节点，使开发者能够在 n8n 工作流中轻松集成 Hyperliquid 的 API 功能，包括市场数据查询、交易执行和账户管理等功能。

## Tech Stack
- **TypeScript** (5.9.2) - 主要开发语言
- **Node.js** (v22+) - 运行时环境
- **n8n-workflow** - n8n 工作流框架（peer dependency）
- **@n8n/node-cli** - n8n 节点开发和构建工具
- **ESLint** (9.32.0) + **Prettier** (3.6.2) - 代码质量和格式化

## Project Conventions

### Code Style
- 使用 **声明式开发模式**（Declarative Style）- 推荐用于 HTTP API 集成
  - 通过 `routing` 配置自动处理请求，减少样板代码
  - 参考 `nodes/GithubIssues/` 作为标准实现示例
- 遵循 n8n 的代码风格指南
- 使用 ESLint 和 Prettier 进行代码格式化
- 运行 `npm run lint:fix` 自动修复常见问题

### Architecture Patterns
- **节点结构**：每个节点位于 `nodes/[NodeName]/` 目录
  - 主节点文件：`[NodeName].node.ts`（实现 `INodeType` 接口）
  - 节点配置：`[NodeName].node.json`（可选）
  - 资源定义：`resources/` 子目录（按资源类型组织）
  - 共享工具：`shared/` 目录（描述、传输、工具函数）
  - 列表搜索：`listSearch/` 目录（动态下拉选项）
- **声明式配置**：使用 `INodeProperties` 和 `routing` 配置定义操作
- **资源导向**：按 API 资源（如 Issue、IssueComment）组织代码
- **认证方式**：支持多种认证（OAuth2、Access Token 等），通过 `credentials/` 目录管理

### Testing Strategy
- 使用 `npm run dev` 在本地 n8n 环境中测试节点
- 在 n8n 编辑器中进行手动测试和验证
- 运行 `npm run build` 确保代码编译成功
- 运行 `npm run lint` 检查代码质量

### Git Workflow
- 使用 OpenSpec 进行变更管理
- 变更提案位于 `openspec/changes/` 目录
- 完成后的变更移至 `openspec/changes/archive/`
- 使用动词引导的变更 ID（如 `add-`, `update-`, `remove-`）

## Domain Context
- **n8n**：一个开源的工作流自动化工具
- **n8n 节点**：可重用的工作流组件，封装特定服务的 API 集成
- **声明式节点**：通过配置而非代码定义 API 请求，n8n 自动处理 HTTP 请求
- **Hyperliquid**：去中心化衍生品交易平台，提供市场数据和交易 API
- **节点操作**：节点可以执行的操作（如 Get、Get All、Create）
- **资源（Resource）**：节点操作的对象类型（如 Issue、IssueComment）

## Important Constraints
- 节点包名称必须以 `n8n-nodes-` 开头
- 必须使用 MIT 许可证
- 验证节点要求：无外部包依赖（仅使用 n8n-workflow）
- 必须遵循 n8n 的设计指南
- 所有节点必须在 `package.json` 的 `n8n.nodes` 数组中注册
- 凭证必须在 `package.json` 的 `n8n.credentials` 数组中注册

## External Dependencies
- **n8n-workflow**：n8n 工作流类型定义和接口（peer dependency）
- **Hyperliquid API**：Hyperliquid 平台的 REST API 端点
- **GitHub API**：示例节点使用的 API（用于参考）
