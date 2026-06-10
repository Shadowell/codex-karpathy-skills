# BitPro Agent 工具接口快照

## 接口发现

先调用 `bitpro_capabilities`。该工具返回：

- `contract_version`：当前为 `bitpro-mcp-v1`；
- `transport`：当前为 `stdio`；
- `transports`：当前为 `stdio` 和 `streamable-http`；
- `api_base_default`：默认 `http://127.0.0.1:8889/api/v2`；
- `remote_mcp`：默认远程路径 `/api/v2/mcp/`，token 环境变量 `BITPRO_MCP_API_TOKEN`，默认请求头 `X-BitPro-MCP-Token`；
- `settings_token_api`：管理员可通过 `GET /api/v2/settings/mcp-token` 查看脱敏状态，通过 `POST /api/v2/settings/mcp-token/generate` 生成一次性明文 `bp_mcp_` token，服务端只保存哈希。
- `tool_groups`：只读、研究/回测/模拟盘写、实盘写；
- `tool_endpoints`：每个工具对应的 HTTP 方法和相对 `/api/v2` 路径；
- `live_trading_enabled` 和 `live_confirmation`；
- `data_policy` 和 `stability_policy`。

## 最小跑通

需要让 Agent 验证整条链路时，在 BitPro 仓库内读取 `docs/integrations/bitpro-agent-mcp-smoke-runbook.md`。最小顺序是：

1. `bitpro_capabilities`
2. `bitpro_health`
3. `market_symbols` / `market_klines`
4. `strategy_search` / `strategy_get`
5. `backtest_start_job` / `backtest_get_job` / `backtest_list_results`
6. `paper_dashboard` / `paper_events` / `paper_equity_curve`
7. 可选只读实盘诊断；不得执行实盘写工具。

创建新策略时，在第 4 步之后先走动态 DB 注册：调用 `strategy_validate_code` 校验完整 `BaseStrategy` 代码，再用 `strategy_create(script_content=...)` 保存，配置中带 `strategy_source=db_script` 和 `script_content_source=db`。迭代已有动态策略时先 `strategy_get`，再用 `strategy_update` 原地更新 `script_content`、`config`、名称或交易池，保持同一个 `strategy_id`。后续回测和模拟盘都用返回的 `strategy_id`，不写 Python 文件、不改 registry、不要求重启 BitPro。

如果 `market_symbols`、`strategy_search`、`paper_dashboard` 等工具返回 `401 请先登录`，先检查 MCP Agent token 是否已生成/配置，并确认 Agent 发送的是 `X-BitPro-MCP-Token` 或 `BITPRO_MCP_AUTH_HEADER` 指定的请求头。不要在聊天里索要 token 明文。

## 工具分组

只读工具：

`bitpro_capabilities`、`bitpro_health`、`market_symbols`、`market_klines`、`market_indicators`、`sync_config`、`sync_status`、`sync_jobs`、`sync_table_stats`、`strategy_search`、`strategy_get`、`agent_get_task`、`agent_get_iterations`、`optimizer_get_run`、`backtest_get_job`、`backtest_list_results`、`backtest_get_result`、`paper_dashboard`、`paper_events`、`paper_equity_curve`、`live_preflight`、`trading_balance`、`trading_positions`、`trading_open_orders`。

研究、回测和模拟盘写工具：

`sync_start_history`、`sync_one`、`strategy_create`、`strategy_update`、`strategy_generate`、`strategy_validate_code`、`agent_create_task`、`agent_accept_iteration`、`optimizer_run_now`、`backtest_start_job`、`backtest_cancel_job`、`backtest_resume_job`、`paper_configure`、`paper_start`、`paper_pause`、`paper_resume`、`paper_stop`。

实盘写工具：

`live_promote`、`trading_spot_order`、`trading_futures_order`、`trading_cancel_order`、`trading_transfer`。

## 最小反问

- 行情：缺 `symbol`、`timeframe` 或范围时反问。
- 回测：缺 `strategy_id`、`start_date`、`end_date` 或资金/成本假设时反问。
- 模拟盘：缺全量/指定实例范围时反问。
- 实盘只读：缺账户/交易所/symbol 范围时反问。
- 实盘写：缺用户明确真实交易授权或确认字段时停止，不调用工具。

## 实盘写确认字段

每个实盘写工具必须由用户明确提供：

- `confirm_live_risk=true`
- `confirmation="I_UNDERSTAND_REAL_TRADING_RISK"`
- 非空 `reason`
- 唯一 `idempotency_key`
