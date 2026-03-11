# Tool Call Examples (Arguments)

These examples show practical argument payloads to send via MCP tool calls.
All examples below work with the guest key.

## create_portfolio

> Bond ETF symbols (BND, AGG, TLT, etc.) are excluded at this access level and will be stripped if submitted.

Weighted portfolio:

```json
{
  "portfolio": {
    "AAPL": 0.4,
    "MSFT": 0.35,
    "GOOGL": 0.25
  },
  "portfolio_name": "CoreTech"
}
```

Symbols only (equal-weighted automatically):

```json
{
  "portfolio": ["AAPL", "MSFT", "GOOGL"],
  "portfolio_name": "CoreTechEqual"
}
```

## get_portfolio_details

```json
{
  "portfolio_id": "CoreTech",
  "detail": "metrics"
}
```

Available `detail` values: `"metrics"`, `"concentration"`, `"sector_exposure"`, `"tail_risk"`, `"contributors"`, `"all"`, `"help"`

## manage_optimization_params

Get effective settings:

```json
{
  "operation": "get",
  "portfolio_name": "CoreTech"
}
```

Validate proposed settings:

```json
{
  "operation": "validate",
  "settings": {
    "strategy": "maximize_sharpe_ratio",
    "constraints": {
      "max_weight_per_asset": 0.30
    }
  }
}
```

Set optimization defaults for the session:

```json
{
  "operation": "set",
  "scope": "user",
  "settings": {
    "strategy": "minimize_volatility",
    "constraints": {
      "max_weight_per_asset": 0.25
    }
  }
}
```

## optimize_portfolio

Maximize Sharpe ratio with position cap:

```json
{
  "portfolio_name": "CoreTech",
  "strategy": "maximize_sharpe_ratio",
  "constraints": {
    "max_weight_per_asset": 0.3
  },
  "strategy_parameters": {
    "risk_free_rate": 0.03
  }
}
```

Minimize volatility:

```json
{
  "portfolio_name": "CoreTech",
  "strategy": "minimize_volatility",
  "constraints": {
    "max_weight_per_asset": 0.3
  }
}
```

Hierarchical Risk Parity (no inputs required):

```json
{
  "portfolio_name": "CoreTech",
  "strategy": "hrp"
}
```

Equal Risk Contribution (no inputs required):

```json
{
  "portfolio_name": "CoreTech",
  "strategy": "equal_risk_contribution"
}
```

Auto — resolve all settings from saved preferences:

```json
{
  "portfolio_name": "CoreTech",
  "strategy": "auto"
}
```

## compare_benchmark_portfolios

```json
{
  "portfolio_name": "CoreTech",
  "benchmark_portfolio_name": "example_sector_technology"
}
```

Use `list_portfolios(examples=True)` to browse available benchmark portfolios.

## get_help

```json
{
  "topic": "cheat sheet"
}
```

## get_capabilities

```json
{}
```

## submit_feedback

```json
{
  "text": "Please add examples for multi-asset class portfolios."
}
```
