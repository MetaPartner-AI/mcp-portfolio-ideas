# MCP Portfolio Ideas (Hosted Remote MCP Server)

`mcp-portfolio-ideas` is a hosted MCP server for portfolio understanding and idea generation with LLMs.

With the guest key below, your MCP client can:
- create equity portfolios and inspect their allocations
- analyze risk, concentration, sector exposure, and tail risk
- optimize portfolios using the full no-input strategy suite
- compare portfolios against benchmarks
- explore available strategies and settings

The server endpoint is:
- `https://mcp-portfolio-ideas.metapartner.ai/mcp/sse/`

## Why Use It

Without MCP, portfolio analysis in chat is mostly unstructured text and **guesses**.

With this server, your LLM can call typed tools and return:
- reproducible outputs
- strategy-aware optimization
- auditable settings used for each run
- a consistent workflow from portfolio input to optimized result

## Quick Start (LM Studio First)

LM Studio uses an `mcp.json` file with an `mcpServers` object.

1. Open your LM Studio MCP config file.
2. Add the server entry below under `mcpServers`.
3. Restart/reload MCP servers in LM Studio.

Use this entry:

```json
{
  "mcpServers": {
    "mcp-portfolio-ideas": {
      "url": "https://mcp-portfolio-ideas.metapartner.ai/mcp/sse/",
      "transport": "sse",
      "headers": {
        "X-API-Key": "mcp_portf_ideas_guest_512B6vEMEMUZqJorLW9JlEzQrpMagnlwi-95QkqvxZk"
      }
    }
  }
}
```

Copy-paste example file:
- `github/examples/lm-studio-mcp.json`

## Use In Other MCP Clients

Any MCP client that supports:
- remote `sse` transport
- custom HTTP headers

can use this same server. See:
- `github/examples/remote-mcp-entry.json`

## Tool Catalog

Current toolset:

### Portfolio management

1. `create_portfolio`
- Purpose: create and store an equity portfolio from symbols or symbol→weight map.
- Args:
  - `portfolio`: `dict[str,float]` or `list[str]`
  - `portfolio_name` (optional): `str`
- Note: bond ETFs (BND, AGG, TLT, etc.) are not available at this level and will be excluded.
- Access: guest + registered.

2. `list_portfolios`
- Purpose: list accessible portfolios and pre-built example portfolios.
- Args:
  - `examples` (optional): `bool`
- Access: guest + registered.

3. `get_portfolio_details`
- Purpose: inspect a portfolio's metrics, concentration, sector exposure, tail risk, or performance contributors.
- Args:
  - `portfolio_id`: `str`
  - `detail` (optional): `"metrics" | "concentration" | "sector_exposure" | "tail_risk" | "contributors" | "all" | "help"`
- Access: guest + registered.

4. `compare_benchmark_portfolios`
- Purpose: compare a portfolio's risk/return profile against a benchmark.
- Args:
  - `portfolio_name`: `str`
  - `benchmark_portfolio_name`: `str`
- Access: guest + registered.

5. `rename_portfolio`
- Purpose: rename an existing portfolio.
- Args:
  - `old_name`: `str`
  - `new_name`: `str`
- Access: registered users only.

6. `delete_portfolio`
- Purpose: delete a portfolio.
- Args:
  - `portfolio_id`: `str`
- Access: registered users only.

### Optimization

7. `manage_optimization_params`
- Purpose: explore available strategies, constraints, and settings; validate proposed settings.
- Args:
  - `operation`: `"get" | "validate" | "set" | "strategies" | "hierarchy" | "constraints" | "profiles" | "help"`
  - `portfolio_name` (optional): `str`
  - `settings` (optional): `dict` — used with `validate` or `set`
  - `scope`: `"user" | "portfolio"` (default: `"user"`)
- Access: guest + registered.

8. `optimize_portfolio`
- Purpose: optimize an existing portfolio and save result as `optimized_{PORTFOLIO}_{TIMESTAMP}`.
- Args:
  - `portfolio_name`: `str`
  - `strategy`: strategy name or `"auto"`
  - `constraints` (optional): `dict` — e.g. `{"max_weight_per_asset": 0.30}`
  - `strategy_parameters` (optional): `dict` — e.g. `{"risk_free_rate": 0.03}`
  - `save_as_preference` (optional): `bool`
- Access: guest + registered.

### Discovery and feedback

8. `get_help`
- Purpose: task-focused help and prompt guidance.
- Args:
  - `topic` (optional): `str`
- Access: guest + registered.

9. `get_capabilities`
- Purpose: inspect strategies, constraints, library support, and role permissions.
- Args: none.
- Access: guest + registered.

10. `submit_feedback`
- Purpose: send product feedback / feature requests.
- Args:
  - `text`: `str`
- Access: guest + registered.

## Available Strategies

Strategies available at guest level (no additional inputs required):
- `maximize_sharpe_ratio` — maximize risk-adjusted return
- `minimize_volatility` — minimize portfolio variance
- `maximize_return` — maximize expected return
- `hrp` — Hierarchical Risk Parity
- `herc` — Hierarchical Equal Risk Contribution
- `equal_risk_contribution` — equalize each asset's risk contribution
- `maximum_diversification` — maximize the diversification ratio

Strategy aliases also accepted:
- `mean-variance`, `mean_variance`, `markowitz`

Additional strategies (higher access levels):
- target-return and target-risk strategies
- CVaR-based strategies
- Black-Litterman (requires forward-looking views)

## Example User Interactions

Starter prompts for your LLM:

1. "Show me the available example portfolios."
2. "Create a portfolio named `CoreTech` with AAPL 40%, MSFT 35%, GOOGL 25%."
3. "Show performance metrics and sector exposure for `CoreTech`."
4. "Optimize `CoreTech` for maximum Sharpe ratio with max 30% per asset."
5. "Optimize `CoreTech` using HRP — no inputs needed."
6. "Compare `CoreTech` against `example_sector_technology`."

More prompt flows:
- `examples/prompt-playbook.md`

## Technical Implementation

High-level architecture:
- FastAPI service with MCP mounted at `/mcp`
- SSE transport at `/mcp/sse/`

Implementation highlights:
- Optimization:
  - supports `PyPortfolioOpt` and `RiskfolioLib`
  - loads data via `yfinance` 
- Defaults:
  - 1 year horizon
  - mean-variance portfolio optimization
  - maximize Share ratio (relative return, scaled by associated risk)

## Access Modes and Limits

- The config above uses a **guest** key:
  - create equity portfolios, run the full optimization suite, analyze and compare
  - guests operate in a **shared workspace** — it is cleaned up nightly
- For private portfolios and access to advanced strategies (target-return, CVaR, Black-Litterman), use a registered user key (ask us).

## Disclaimer

This service is for informational and educational purposes, it does not constitute financial advice.  Portfolio optimization is based on historical data which may not predict future performance. Past performance does not guarantee
future results. All investments carry risk including potential loss of principal. Consult with a qualified financial
advisor before making investment decisions. Calculations have been done using your assets percentages, but
scaled to an account value of $100,000.00 for illustrative purposes. You are expected to learn and make up
your mind from this analysis, not to to follow it blindly.
