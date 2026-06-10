---
name: bitpro-agent-interface
description: 用于外部 Agent 通过 BitPro MCP/API 进行 tool calling、接口选择、参数反问和风险边界控制。适用于需要访问 BitPro 行情/K线、基础数据同步、策略库、回测结果、模拟盘数据、只读实盘诊断或受控实盘写操作时，也适用于维护 BitPro 对外接口稳定性、接口清单和 Agent 接入说明。
---

# BitPro Agent Interface

## 核心原则

- 先调用 `bitpro_capabilities`，再选择具体工具；不要凭记忆猜工具名或底层路径。
- BitPro 对外稳定合同是 `bitpro-mcp-v1`。仓库内标准清单是 `docs/integrations/bitpro-agent-tool-interface.md`，代码事实来源是 `backend/app/mcp/schemas.py` 的 `MCP_TOOL_ENDPOINTS`。
- 需要验证 Agent 是否能跑通完整 MCP 流程时，读取 `docs/integrations/bitpro-agent-mcp-smoke-runbook.md` 并按其冒烟流程执行。
- 本地同机 Agent 优先使用 `stdio`；远程 Agent 使用 `streamable-http`，默认 URL 为 `/api/v2/mcp/`，并通过 `X-BitPro-MCP-Token` 或 `BITPRO_MCP_AUTH_HEADER` 指定的 header 提供 MCP Agent token。管理员可在 BitPro 设置面板 `MCP Agent Token` 或 `POST /api/v2/settings/mcp-token/generate` 生成 `bp_mcp_` token；环境变量 `BITPRO_MCP_API_TOKEN` 仍是兼容来源。
- 只使用真实 BitPro API 状态和真实行情/K 线。数据不足时调用 `sync_start_history` / `sync_one` 或缩短区间；不得使用 mock、synthetic、random fallback 或静默生成 OHLCV。
- 外部 Agent 开发新策略默认使用动态 DB 策略：先 `strategy_validate_code`，再 `strategy_create(script_content=...)`，服务端写入 `strategy_source=db_script` / `script_content_source=db` 并在回测和模拟盘启动时优先加载数据库代码；迭代已有动态策略时先 `strategy_get` 再 `strategy_update`，保持同一个 `strategy_id`；不要要求改 Python 文件或重启服务。
- 默认生命周期是研究、回测、模拟盘。真实账户写操作是高危边界，必须等用户明确要求并提供完整确认字段。
- 不要向用户索要 API key、secret、passphrase 或 webhook 原文；凭证应由 BitPro 服务器环境管理。
- 不要向用户索要 MCP token 明文；如果遇到 `401 请先登录`，报告需要管理员生成/配置 MCP Agent Token，而不是在聊天里索要 token。

## 标准流程

1. 调用 `bitpro_capabilities`，确认 `contract_version`、`transports`、`remote_mcp`、`tool_groups`、`tool_endpoints`、`live_trading_enabled` 和 `data_policy`。
2. 调用 `bitpro_health`，确认 BitPro API 可用。
3. 如果目标是端到端验收，按 `docs/integrations/bitpro-agent-mcp-smoke-runbook.md` 记录每一步工具、参数、结果、证据和跳过的高危步骤。
4. 选择最小工具面：
   - 行情/K 线/指标：`market_symbols`、`market_klines`、`market_indicators`。
   - 数据覆盖：`sync_config`、`sync_status`、`sync_jobs`、`sync_table_stats`；缺历史数据时再用 `sync_start_history` 或 `sync_one`。
   - 策略：`strategy_search`、`strategy_get`；新策略研发默认 `strategy_validate_code` 后用 `strategy_create(script_content=...)` 动态注册，已有动态策略迭代用 `strategy_update` 原地更新，并按命名规范写入。
   - 回测：`backtest_start_job` 后轮询 `backtest_get_job`，完成后读 `backtest_list_results` / `backtest_get_result`。
   - 模拟盘：优先只读 `paper_dashboard`、`paper_events`、`paper_equity_curve`；启动/暂停/停止前确认实例。
   - 实盘只读：`trading_balance`、`trading_positions`、`trading_open_orders`、`live_preflight`。
   - 实盘写：仅在用户明确要求真实交易后使用 `live_promote`、`trading_spot_order`、`trading_futures_order`、`trading_cancel_order` 或 `trading_transfer`。
4. 回答时说明调用过的工具、关键参数、数据限制和未验证部分。

## 策略命名规范

所有出现在 seed、生产 `strategies` 行、UI 文案、文档和新生成策略元数据中的策略名，都必须使用下面的规范结构：

```text
[资产类型][K线周期][策略类型] 标的范围 · 策略名称 · 资金版本
```

示例：

- `[合约][5M][马丁] BTC · ATR马丁网格 · 100U`
- `[合约][15M][CTA] 1INCH · EMA5/20趋势跟踪激进版 · 100U`
- `[合约][1H][CTA] Top20 · 动态趋势跟踪 · 100U`
- `[现货][4H][CTA] 多品种 · 趋势跟踪稳健版 · 10000U`

字段规则：

- `[资产类型]`：现货策略使用 `[现货]`；合约、永续、swap 或衍生品策略使用 `[合约]`。
- `[K线周期]`：使用策略定义的执行 K 线周期，并规范为大写展示：`[1M]`、`[5M]`、`[15M]`、`[30M]`、`[1H]`、`[4H]`、`[12H]` 或 `[1D]`。如果策略确实没有固定周期，只有 AI 决策观察策略可使用 `[AI]`，并必须在策略配置或文档中说明该例外。
- `[策略类型]`：使用一个简洁分类标签，例如 `[CTA]`、`[马丁]`、`[网格]`、`[套利]`、`[AI]` 或 `[均值回归]`。CTA 趋势跟踪策略使用 `[CTA]`，不要再额外加 `[趋势]` 类型标签；具体趋势逻辑写在 `策略名称` 中。
- `标的范围`：单标的策略写单个基础资产，例如 `BTC`、`1INCH`；小型固定篮子用斜杠分隔，例如 `BTC/ETH/SOL`；动态或特殊交易池使用稳定标签，例如 `Top20`、`Top50`、`Pre-IPO3`、`TradFi贵金属`。
- `策略名称`：描述真实方法和版本，例如 `ATR马丁网格`、`EMA5/20趋势跟踪激进版`、`动态趋势跟踪`、`资金费率套利` 或 `高胜率浮盈保护`。
- `资金版本`：使用配置的 paper 或 live 资金规模，例如 `100U`、`1000U` 或 `10000U`；不要写成 `100U版`。

执行要求：

- 新策略研发、`strategy_create` 动态注册、`strategy_update` 策略重命名、seed 新增或策略重命名时，都要直接按这个 canonical 结构写入策略名。
- `strategy_key`、代码类名和文件名不要求和中文展示名一致，但展示名必须遵守该规范。
- 历史名称只能作为 `db_name_aliases` 保留，用于生产 SQLite 行迁移；不要把旧名继续当作新的 canonical 名称。
- 当用户只给出策略逻辑而没有给出完整名称时，先按上面的结构补齐，再执行创建、回测、seed 或模拟盘启动流程。

## 反问规则

只问完成工具调用所必需的问题，最多一到两个：

- 行情问题缺上下文：问 `exchange`、`symbol`、`timeframe`、时间范围或 `limit`。
- 回测问题缺上下文：问 `strategy_id` 或策略名、`start_date`、`end_date`、初始资金、手续费/滑点是否使用默认值。
- 模拟盘问题缺上下文：问看全部实例还是指定 `strategy_id` / `instance_id`。
- 只读实盘诊断缺上下文：问交易所、账户范围、symbol 过滤条件；明确只做只读诊断。
- 实盘写操作缺确认：停止调用并要求用户明确真实交易意图，以及 `confirm_live_risk=true`、`confirmation="I_UNDERSTAND_REAL_TRADING_RISK"`、非空 `reason`、唯一 `idempotency_key`。

## 实盘边界

实盘写工具默认被 `BITPRO_MCP_ENABLE_LIVE_TRADING=1` 关闭。即使开关已开启，也必须由用户提供完整确认字段。Agent 不得替用户生成、推断或补齐确认字段。

优先使用产品级 `/live-real` 订阅和 `live_preflight` 预检；直接 `trading_futures_order` / `trading_spot_order` 只用于明确批准的高危直接操作。

## 接口稳定性维护

维护 BitPro 对外接口时同步更新：

- `backend/app/mcp/schemas.py`：`MCP_CONTRACT_VERSION`、`MCP_TOOL_ENDPOINTS`、工具分组。
- `backend/app/mcp/tools.py`：`bitpro_capabilities` 输出。
- `backend/app/mcp/server.py` 和 `backend/app/core/auth_middleware.py`：远程 `streamable-http` 挂载和 MCP token 认证。
- `backend/app/services/mcp_token_service.py` 和 `backend/app/api/v2/endpoints/settings.py`：MCP token 生成、哈希保存、状态查询和认证复用。
- `docs/integrations/bitpro-agent-tool-interface.md`：对外接口清单。
- `docs/integrations/bitpro-agent-mcp-smoke-runbook.md`：给外部 Agent 跑通 MCP 冒烟验收流程。
- `docs/contracts/模型上下文协议策略研究.md`：MCP 行为合同。
- `docs/product/specification.md` 和 `docs/project/progress.md`。
- 本 skill 以及全局安装副本 `$CODEX_HOME/skills/bitpro-agent-interface`。

破坏性变更必须提升 `MCP_CONTRACT_VERSION` 并更新回归测试；新增工具优先做加法兼容。

## 参考资料

- 在 BitPro 仓库内优先读 `docs/integrations/bitpro-agent-tool-interface.md`。
- 需要验证完整链路时，读 `docs/integrations/bitpro-agent-mcp-smoke-runbook.md`。
- 如果不在仓库上下文中，读取本 skill 的 `references/tool-interface.md` 作为接口快照。
