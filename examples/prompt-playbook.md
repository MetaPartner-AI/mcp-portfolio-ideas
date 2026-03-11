# Prompt Playbook for MCP Portfolio Ideas

Use these prompts in LM Studio (or any MCP-enabled LLM client) after connecting `mcp-portfolio-ideas`.

> **Access level:** All prompts below work with the guest key.
> Bond ETFs, mixed portfolio analysis, target-return/CVaR strategies, stress testing, and Black-Litterman require an upgraded account.

## 1) Onboarding and discovery

1. "What tools are available and what can I do?" → `user_guide(topic="quick_start.tools")`
2. "What optimization strategies can I use at my current access level?" → `user_guide(topic="strategies")`

## 2) Build a portfolio

> **Note:** Bond ETFs (BND, AGG, TLT, etc.) are not available at this access level and will be excluded if submitted.

1. "Create a portfolio named `CoreTech` with AAPL 40%, MSFT 35%, GOOGL 25%."
2. "Create an equal-weighted portfolio with AAPL, MSFT, NVDA, GOOGL." → symbols only, weights assigned automatically
3. "Show me all my portfolios." → `list_portfolios()`
4. "Show me the example portfolios available." → `list_portfolios(examples=True)`

## 3) Analyze a portfolio

1. "Show performance metrics for `CoreTech`." → `get_portfolio_details("CoreTech")`
2. "How concentrated is `CoreTech`?" → `get_portfolio_details("CoreTech", detail="concentration")`
3. "Show sector exposure for `CoreTech`." → `get_portfolio_details("CoreTech", detail="sector_exposure")`
4. "What are the tail risk metrics for `CoreTech`?" → `get_portfolio_details("CoreTech", detail="tail_risk")`
5. "Which holdings drive the most risk and return?" → `get_portfolio_details("CoreTech", detail="contributors")`
6. "Give me everything at once." → `get_portfolio_details("CoreTech", detail="all")`

## 4) Run optimization with explicit controls

1. "Optimize `CoreTech` with strategy `maximize_sharpe_ratio`, max 35% per asset, risk-free rate 0.03."
2. "Optimize `CoreTech` to minimize volatility with no position larger than 30%."
3. "Show the optimized portfolio weights and performance from the result."

## 5) Run optimization with auto settings

1. "Set my optimization defaults to strategy `minimize_volatility`, max 25% per asset."
2. "Optimize `CoreTech` using `auto` settings."
3. "Explain exactly which settings were used and where they came from."

## 6) Hierarchical and risk-parity strategies

These strategies require no user inputs beyond the portfolio itself — they use cluster-based or
risk-contribution methods that are more robust to estimation error than standard mean-variance.

1. "Optimize `CoreTech` with `hrp` and explain why hierarchical methods might help diversification."
   → `optimize_portfolio("CoreTech", strategy="hrp")`
2. "Optimize `CoreTech` with `herc` — how does it differ from HRP?"
   → `optimize_portfolio("CoreTech", strategy="herc")`
3. "Optimize `CoreTech` with `equal_risk_contribution` — what does equal risk mean in practice?"
   → `optimize_portfolio("CoreTech", strategy="equal_risk_contribution")`
4. "Which strategy gives the most diversified result?" → `user_guide(topic="strategies")`

## 7) Compare against a benchmark

1. "Compare `CoreTech` against the `example_sector_technology` example portfolio."
   → `compare_benchmark_portfolios("CoreTech", "example_sector_technology")`
2. "Browse available example portfolios to find a benchmark." → `list_portfolios(examples=True)`
3. "How does my portfolio's Sharpe ratio compare to a broad market benchmark?"

## 8) Optimization settings — discovery and configuration

`manage_optimization_params` uses a single `operation` parameter for both reading and writing.
Pass the intent directly — no secondary parameter needed.

**Strategy discovery:**
1. "What optimization strategies can I use?" → `operation="strategies"`
2. "Which strategies are locked and why?" → `operation="strategies"` (locked_strategies section)
3. "Which strategy should I use to minimize losses?" → `operation="strategies"`

**Settings source:**
1. "Where are my settings coming from?" → `operation="hierarchy"`
2. "Did my saved strategy preference take effect?" → `operation="hierarchy"`
3. "What are the system defaults for optimization?" → `operation="hierarchy"`

**Constraints:**
1. "Do I have any position limits set?" → `operation="constraints"`
2. "Will the optimizer concentrate heavily without limits?" → `operation="constraints"`

**Profiles:**
1. "What optimization profiles are available?" → `operation="profiles"`
2. "What does the conservative profile look like?" → `operation="profiles"`

**Discovery:**
- "What operations are available for manage_optimization_params?"
  → LLM should call `manage_optimization_params(operation="help")`

## 9) Feedback

1. "Submit feedback asking for sector-level exposure breakdown in optimization outputs."
