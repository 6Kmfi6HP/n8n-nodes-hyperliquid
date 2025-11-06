# Hyperliquid n8n 集成完整实施路线图

## 概述

本文档列出了使用 SDK（`@nktkas/hyperliquid`）实现完整 Hyperliquid n8n 集成的所有 OpenSpec 提案，按实施顺序和依赖关系组织。

## 实施阶段

### Phase 1: 基础查询功能（无需签名）
**目标**: 实现公开市场数据查询，建立基础架构

### Phase 2: 凭证系统和签名认证
**目标**: 建立私钥管理和签名认证机制

### Phase 3: 账户信息查询（需要签名）
**目标**: 实现需要签名的账户相关查询

### Phase 4: 交易操作（需要签名）
**目标**: 实现交易执行功能

### Phase 5: 高级功能（可选）
**目标**: WebSocket 订阅和高级特性

---

## 提案清单

### Phase 1: 基础查询功能

#### ✅ Proposal 1: `add-hyperliquid-market-data-node` (已有)
**状态**: 已创建
**描述**: 实现公开市场数据查询节点
**功能范围**:
- 行情数据查询（Ticker）
- 订单簿快照（Order Book）
- 最近成交（Recent Trades）
- 动态交易对下拉列表

**文件位置**: `openspec/changes/add-hyperliquid-market-data-node/`

**依赖**: 无

---

#### Proposal 2: `add-hyperliquid-info-query-node`
**状态**: 待创建
**描述**: 实现 Hyperliquid Info 端点查询功能（无需签名的完整查询）
**功能范围**:
- 交易所元数据和资产上下文（metaAndAssetCtxs）
- 特定资产上下文（assetCtxs）
- L2 订单簿深度查询（l2Book）
- 用户公开信息查询（无需签名）
- K 线快照（candleSnapshot）
- 历史成交查询（tradesHistory）
- 资金费率查询（fundingHistory）

**资源结构**:
- `resources/info/meta.ts` - 元数据查询
- `resources/info/orderBook.ts` - 订单簿查询
- `resources/info/candles.ts` - K 线数据
- `resources/info/trades.ts` - 成交历史

**依赖**: Proposal 1（基础架构）

---

### Phase 2: 凭证系统和签名认证

#### Proposal 3: `add-hyperliquid-credentials-system`
**状态**: 待创建
**描述**: 创建 Hyperliquid 凭证系统，支持私钥安全存储和签名认证
**功能范围**:
- 创建 `HyperliquidApi` 凭证类型
- 私钥加密存储（使用 n8n 凭证系统）
- 支持 Mainnet/Testnet 环境切换
- 支持 API Wallet（代理钱包）模式
- 凭证测试功能

**技术要点**:
- 使用 `ICredentialType` 接口
- 集成 `viem` 或 `ethers` 进行钱包管理
- 支持私钥格式验证
- 凭证加密存储

**文件位置**:
- `credentials/HyperliquidApi.credentials.ts`

**依赖**: 无（但需要先完成 Proposal 1-2 的基础架构）

---

#### Proposal 4: `add-hyperliquid-sdk-integration`
**状态**: 待创建
**描述**: 集成 `@nktkas/hyperliquid` SDK 到节点系统
**功能范围**:
- 安装和配置 SDK 依赖
- 创建 SDK 客户端工厂函数
- 实现 Transport 层（HttpTransport, WebSocketTransport）
- 错误处理和类型安全
- 连接复用和性能优化

**技术要点**:
- 在 `package.json` 中添加 `@nktkas/hyperliquid` 和 `viem` 依赖
- 创建 `shared/sdk.ts` 封装 SDK 客户端创建
- 实现错误转换（SDK 错误 → n8n 错误）
- 支持连接池和超时配置

**文件位置**:
- `shared/sdk.ts`
- `shared/errors.ts`
- `package.json` (依赖更新)

**依赖**: Proposal 3（凭证系统）

---

### Phase 3: 账户信息查询（需要签名）

#### Proposal 5: `add-hyperliquid-account-info-node`
**状态**: 待创建
**描述**: 实现需要签名的账户信息查询功能
**功能范围**:
- 用户清算所状态（clearinghouseState）
- 用户持仓信息（positions）
- 用户资金信息（funding）
- 用户委托订单（openOrders）
- 用户历史订单（historicalOrders）
- 用户成交记录（userFills）
- 用户通知（notifications）

**资源结构**:
- `resources/account/state.ts` - 账户状态
- `resources/account/positions.ts` - 持仓信息
- `resources/account/orders.ts` - 订单查询
- `resources/account/fills.ts` - 成交记录
- `resources/account/funding.ts` - 资金记录

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）

---

### Phase 4: 交易操作（需要签名）

#### Proposal 6: `add-hyperliquid-exchange-order-node`
**状态**: 待创建
**描述**: 实现订单管理功能（下单、撤单、修改）
**功能范围**:
- 下单（Order）- 限价单、市价单
- 撤单（Cancel）- 单个/批量撤单
- 修改订单（Update）
- 订单状态查询
- 订单确认机制

**资源结构**:
- `resources/exchange/order.ts` - 下单操作
- `resources/exchange/cancel.ts` - 撤单操作
- `resources/exchange/update.ts` - 修改订单

**安全特性**:
- 交易金额限制配置
- 二次确认机制（可选）
- 操作日志记录

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）

---

#### Proposal 7: `add-hyperliquid-exchange-position-node`
**状态**: 待创建
**描述**: 实现仓位管理功能
**功能范围**:
- 修改杠杆（updateLeverage）
- 调整保证金（updateIsolatedMargin）
- 平仓操作（closePosition）
- 仓位查询

**资源结构**:
- `resources/exchange/leverage.ts` - 杠杆管理
- `resources/exchange/margin.ts` - 保证金管理
- `resources/exchange/closePosition.ts` - 平仓操作

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）, Proposal 6（订单功能）

---

#### Proposal 8: `add-hyperliquid-exchange-withdraw-node`
**状态**: 待创建
**描述**: 实现资金提取功能
**功能范围**:
- 提取资金（withdraw3）
- 提取历史查询
- 提取确认机制

**资源结构**:
- `resources/exchange/withdraw.ts` - 提取操作

**安全特性**:
- 提取金额限制
- 地址白名单（可选）
- 二次确认机制

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）

---

#### Proposal 9: `add-hyperliquid-exchange-staking-node`
**状态**: 待创建
**描述**: 实现质押相关功能
**功能范围**:
- 质押存款（cDeposit）
- 质押提取（cWithdraw）
- 质押状态查询

**资源结构**:
- `resources/exchange/staking.ts` - 质押操作

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）

---

#### Proposal 10: `add-hyperliquid-exchange-agent-node`
**状态**: 待创建
**描述**: 实现 API Wallet（代理钱包）管理功能
**功能范围**:
- 批准代理钱包（approveAgent）
- 撤销代理钱包（removeAgent）
- 代理钱包列表查询

**资源结构**:
- `resources/exchange/agent.ts` - 代理钱包管理

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）

---

### Phase 5: 高级功能（可选）

#### Proposal 11: `add-hyperliquid-websocket-subscription`
**状态**: 待创建
**描述**: 实现 WebSocket 实时数据订阅功能
**功能范围**:
- 实时行情订阅（allMids, bbo）
- 实时订单簿更新（l2Book）
- 实时成交推送（trades）
- 实时账户更新（clearinghouseState, openOrders）
- 实时通知推送（notifications）

**技术挑战**:
- n8n 对 WebSocket 支持有限
- 需要触发器节点或后台连接管理
- 连接生命周期管理

**资源结构**:
- `resources/websocket/subscriptions.ts` - 订阅管理
- `triggers/hyperliquidWebSocket.trigger.ts` - 触发器节点（如需要）

**依赖**: Proposal 3（凭证系统）, Proposal 4（SDK 集成）

---

#### Proposal 12: `add-hyperliquid-error-handling-enhancements`
**状态**: 待创建
**描述**: 增强错误处理和用户反馈
**功能范围**:
- 详细的错误消息转换
- 错误恢复建议
- 速率限制处理
- 网络错误重试机制
- 操作日志记录

**依赖**: 所有前面的提案

---

#### Proposal 13: `add-hyperliquid-testing-utilities`
**状态**: 待创建
**描述**: 添加测试工具和示例
**功能范围**:
- Testnet 环境配置
- 测试工作流示例
- 单元测试工具
- 集成测试指南

**依赖**: 所有前面的提案

---

## 实施顺序总结

```
Phase 1: 基础查询
├── Proposal 1: Market Data Node ✅ (已有)
└── Proposal 2: Info Query Node

Phase 2: 认证系统
├── Proposal 3: Credentials System
└── Proposal 4: SDK Integration

Phase 3: 账户查询
└── Proposal 5: Account Info Node

Phase 4: 交易功能
├── Proposal 6: Exchange Order Node
├── Proposal 7: Exchange Position Node
├── Proposal 8: Exchange Withdraw Node
├── Proposal 9: Exchange Staking Node
└── Proposal 10: Exchange Agent Node

Phase 5: 高级功能（可选）
├── Proposal 11: WebSocket Subscription
├── Proposal 12: Error Handling
└── Proposal 13: Testing Utilities
```

## 优先级建议

### 🔴 高优先级（核心功能）
1. Proposal 1: Market Data Node ✅
2. Proposal 2: Info Query Node
3. Proposal 3: Credentials System
4. Proposal 4: SDK Integration
5. Proposal 5: Account Info Node
6. Proposal 6: Exchange Order Node

### 🟡 中优先级（重要功能）
7. Proposal 7: Exchange Position Node
8. Proposal 8: Exchange Withdraw Node
9. Proposal 10: Exchange Agent Node

### 🟢 低优先级（增强功能）
10. Proposal 9: Exchange Staking Node
11. Proposal 11: WebSocket Subscription
12. Proposal 12: Error Handling
13. Proposal 13: Testing Utilities

## 注意事项

1. **安全性**: 所有涉及私钥和交易的操作都需要严格的安全审查
2. **测试**: 建议先在 Testnet 环境完成所有测试
3. **文档**: 每个提案完成后需要更新 README 和文档
4. **依赖管理**: 注意 `package.json` 的依赖版本管理
5. **向后兼容**: 后续提案需要考虑与已有功能的兼容性

## 下一步行动

1. 完成 Proposal 1 的实施和测试
2. 创建 Proposal 2-4 的详细规格
3. 按顺序逐步实施每个提案
4. 每完成一个阶段进行集成测试

