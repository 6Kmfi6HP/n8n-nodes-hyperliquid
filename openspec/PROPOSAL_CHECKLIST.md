# Hyperliquid n8n 集成提案清单

## 快速参考：每个提案需要的文件结构

### 提案模板结构

每个提案需要以下文件：
```
openspec/changes/[change-id]/
├── proposal.md          # Why, What Changes, Impact
├── tasks.md            # 实施检查清单
├── design.md           # 技术设计（可选，仅在需要时）
└── specs/
    └── [capability]/
        └── spec.md     # 需求规格（ADDED/MODIFIED/REMOVED）
```

---

## 详细提案清单

### ✅ Proposal 1: `add-hyperliquid-market-data-node`
**状态**: 已创建
**文件**: 
- `openspec/changes/add-hyperliquid-market-data-node/proposal.md` ✅
- `openspec/changes/add-hyperliquid-market-data-node/tasks.md` ✅
- `openspec/changes/add-hyperliquid-market-data-node/specs/hyperliquid-market-data/spec.md` ✅

---

### Proposal 2: `add-hyperliquid-info-query-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-info-query-node/proposal.md`
- `openspec/changes/add-hyperliquid-info-query-node/tasks.md`
- `openspec/changes/add-hyperliquid-info-query-node/specs/hyperliquid-info/spec.md`

**功能范围**:
- metaAndAssetCtxs
- assetCtxs
- l2Book
- candleSnapshot
- tradesHistory
- fundingHistory

---

### Proposal 3: `add-hyperliquid-credentials-system`
**待创建文件**:
- `openspec/changes/add-hyperliquid-credentials-system/proposal.md`
- `openspec/changes/add-hyperliquid-credentials-system/tasks.md`
- `openspec/changes/add-hyperliquid-credentials-system/design.md` (需要：涉及安全架构)
- `openspec/changes/add-hyperliquid-credentials-system/specs/hyperliquid-credentials/spec.md`

**功能范围**:
- 私钥存储
- 环境配置（Mainnet/Testnet）
- API Wallet 支持
- 凭证测试

---

### Proposal 4: `add-hyperliquid-sdk-integration`
**待创建文件**:
- `openspec/changes/add-hyperliquid-sdk-integration/proposal.md`
- `openspec/changes/add-hyperliquid-sdk-integration/tasks.md`
- `openspec/changes/add-hyperliquid-sdk-integration/design.md` (需要：涉及外部依赖)
- `openspec/changes/add-hyperliquid-sdk-integration/specs/hyperliquid-sdk/spec.md`

**功能范围**:
- SDK 依赖管理
- 客户端工厂
- 错误处理
- 连接管理

---

### Proposal 5: `add-hyperliquid-account-info-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-account-info-node/proposal.md`
- `openspec/changes/add-hyperliquid-account-info-node/tasks.md`
- `openspec/changes/add-hyperliquid-account-info-node/specs/hyperliquid-account/spec.md`

**功能范围**:
- clearinghouseState
- positions
- openOrders
- historicalOrders
- userFills
- notifications

---

### Proposal 6: `add-hyperliquid-exchange-order-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-exchange-order-node/proposal.md`
- `openspec/changes/add-hyperliquid-exchange-order-node/tasks.md`
- `openspec/changes/add-hyperliquid-exchange-order-node/design.md` (需要：涉及安全)
- `openspec/changes/add-hyperliquid-exchange-order-node/specs/hyperliquid-exchange-order/spec.md`

**功能范围**:
- order (下单)
- cancel (撤单)
- update (修改订单)

---

### Proposal 7: `add-hyperliquid-exchange-position-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-exchange-position-node/proposal.md`
- `openspec/changes/add-hyperliquid-exchange-position-node/tasks.md`
- `openspec/changes/add-hyperliquid-exchange-position-node/specs/hyperliquid-exchange-position/spec.md`

**功能范围**:
- updateLeverage
- updateIsolatedMargin
- closePosition

---

### Proposal 8: `add-hyperliquid-exchange-withdraw-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-exchange-withdraw-node/proposal.md`
- `openspec/changes/add-hyperliquid-exchange-withdraw-node/tasks.md`
- `openspec/changes/add-hyperliquid-exchange-withdraw-node/design.md` (需要：涉及安全)
- `openspec/changes/add-hyperliquid-exchange-withdraw-node/specs/hyperliquid-exchange-withdraw/spec.md`

**功能范围**:
- withdraw3

---

### Proposal 9: `add-hyperliquid-exchange-staking-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-exchange-staking-node/proposal.md`
- `openspec/changes/add-hyperliquid-exchange-staking-node/tasks.md`
- `openspec/changes/add-hyperliquid-exchange-staking-node/specs/hyperliquid-exchange-staking/spec.md`

**功能范围**:
- cDeposit
- cWithdraw

---

### Proposal 10: `add-hyperliquid-exchange-agent-node`
**待创建文件**:
- `openspec/changes/add-hyperliquid-exchange-agent-node/proposal.md`
- `openspec/changes/add-hyperliquid-exchange-agent-node/tasks.md`
- `openspec/changes/add-hyperliquid-exchange-agent-node/specs/hyperliquid-exchange-agent/spec.md`

**功能范围**:
- approveAgent
- removeAgent

---

### Proposal 11: `add-hyperliquid-websocket-subscription`
**待创建文件**:
- `openspec/changes/add-hyperliquid-websocket-subscription/proposal.md`
- `openspec/changes/add-hyperliquid-websocket-subscription/tasks.md`
- `openspec/changes/add-hyperliquid-websocket-subscription/design.md` (需要：涉及架构复杂性)
- `openspec/changes/add-hyperliquid-websocket-subscription/specs/hyperliquid-websocket/spec.md`

**功能范围**:
- WebSocket 订阅管理
- 实时数据推送

---

### Proposal 12: `add-hyperliquid-error-handling-enhancements`
**待创建文件**:
- `openspec/changes/add-hyperliquid-error-handling-enhancements/proposal.md`
- `openspec/changes/add-hyperliquid-error-handling-enhancements/tasks.md`
- `openspec/changes/add-hyperliquid-error-handling-enhancements/specs/hyperliquid-error-handling/spec.md`

**功能范围**:
- 错误消息转换
- 错误恢复建议
- 重试机制

---

### Proposal 13: `add-hyperliquid-testing-utilities`
**待创建文件**:
- `openspec/changes/add-hyperliquid-testing-utilities/proposal.md`
- `openspec/changes/add-hyperliquid-testing-utilities/tasks.md`
- `openspec/changes/add-hyperliquid-testing-utilities/specs/hyperliquid-testing/spec.md`

**功能范围**:
- 测试工具
- 示例工作流

---

## 创建提案的命令参考

### 基础命令
```bash
# 列出所有现有提案
openspec list

# 列出所有规格
openspec list --specs

# 创建提案目录结构
mkdir -p openspec/changes/[change-id]/specs/[capability]

# 验证提案
openspec validate [change-id] --strict
```

### 创建提案的步骤

1. **创建目录结构**
```bash
CHANGE_ID="add-hyperliquid-info-query-node"
mkdir -p openspec/changes/$CHANGE_ID/specs/hyperliquid-info
```

2. **创建 proposal.md**
```bash
cat > openspec/changes/$CHANGE_ID/proposal.md << 'EOF'
## Why
[为什么需要这个功能]

## What Changes
- [变更列表]

## Impact
- [影响范围]
EOF
```

3. **创建 tasks.md**
```bash
cat > openspec/changes/$CHANGE_ID/tasks.md << 'EOF'
## 1. Implementation
- [ ] 1.1 [任务1]
- [ ] 1.2 [任务2]
EOF
```

4. **创建 spec.md**
```bash
cat > openspec/changes/$CHANGE_ID/specs/hyperliquid-info/spec.md << 'EOF'
## ADDED Requirements
### Requirement: [需求名称]
[需求描述]

#### Scenario: [场景名称]
- **WHEN** [条件]
- **THEN** [预期结果]
EOF
```

5. **验证提案**
```bash
openspec validate $CHANGE_ID --strict
```

---

## 提案创建顺序建议

### 阶段 1: 立即创建（可以并行）
- [ ] Proposal 2: Info Query Node
- [ ] Proposal 3: Credentials System
- [ ] Proposal 4: SDK Integration

### 阶段 2: 等待阶段1完成后
- [ ] Proposal 5: Account Info Node

### 阶段 3: 核心交易功能
- [ ] Proposal 6: Exchange Order Node
- [ ] Proposal 7: Exchange Position Node
- [ ] Proposal 8: Exchange Withdraw Node

### 阶段 4: 增强功能
- [ ] Proposal 9: Exchange Staking Node
- [ ] Proposal 10: Exchange Agent Node

### 阶段 5: 高级功能（可选）
- [ ] Proposal 11: WebSocket Subscription
- [ ] Proposal 12: Error Handling
- [ ] Proposal 13: Testing Utilities

---

## 检查清单模板

创建每个提案时，确保包含：

- [ ] proposal.md (Why, What Changes, Impact)
- [ ] tasks.md (实施步骤)
- [ ] design.md (如果涉及架构、安全、外部依赖或复杂决策)
- [ ] specs/[capability]/spec.md (至少一个 Requirement 和 Scenario)
- [ ] 运行 `openspec validate [change-id] --strict` 通过验证

---

## 注意事项

1. **设计文档**: 仅在以下情况创建 `design.md`:
   - 涉及跨模块/服务的变更
   - 新增外部依赖或重大数据模型变更
   - 涉及安全、性能或迁移复杂性
   - 存在需要技术决策的模糊性

2. **规格格式**:
   - 使用 `## ADDED Requirements` 表示新功能
   - 每个 Requirement 至少包含一个 `#### Scenario:`
   - Scenario 格式: `- **WHEN** ... - **THEN** ...`

3. **变更 ID 命名**:
   - 使用 kebab-case
   - 动词开头 (`add-`, `update-`, `remove-`)
   - 简短且描述性

