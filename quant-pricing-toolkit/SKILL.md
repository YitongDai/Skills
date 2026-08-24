---
name: quant-pricing-toolkit
description: Build quantitative finance scripts for beta estimation (OLS, rolling, ARIMA/exponential-smoothing forecast) and options pricing (Black-Scholes, binomial tree, real options, pricing dashboard), following Yitong's existing Beta and options_project scripts. Use when asked to estimate or forecast a stock's beta, price an option (European/American, Black-Scholes or binomial), build a real-options analysis, or extend a numbered incremental pricing script (01_black_scholes.py style).
---

# Quant Pricing Toolkit (Beta & Options)

Reusable workflow for beta estimation and options pricing scripts, matching the structure already used in `Beta/beta.py` and `options_project/`.

## Workflow

- **Build incrementally, one capability per file/function** — the `options_project` folder already does this well with `01_black_scholes.py` → `02_binomial_tree.py` → `04_real_options.py` → `06_dashboard.py`. When extending it, add the next numbered script rather than folding everything into one file; keep the same numbering gap convention if a step is skipped.
- **Pin data sources for reproducibility** — when pulling market data (e.g. via `yfinance`), always specify explicit `start`/`end` dates as function arguments, the way `load_real_data(ticker, market, start, end)` in `beta.py` does. Never fetch "recent" data without pinning the window, since prices change day to day.
- **Guard optional dependencies** — wrap `yfinance` / `statsmodels` imports in `try/except ImportError` with a `HAS_X` flag and a simulated-data fallback (`generate_simulated_data`), exactly as `beta.py` does, so the script still runs and is demonstrable without every package installed.

## Beta estimation conventions (from `beta.py`)

- **OLS beta**: `scipy.stats.linregress(ret_market, ret_stock)`; report beta (slope), alpha (intercept), R², std_err, p_value, and a 95% CI (`slope ± 1.96 * std_err`) — always return the full dict, not just the point estimate.
- **Rolling beta**: `rolling_cov / rolling_var` over a window (default 60 trading days, `min_periods=30`), returned as a named `pd.Series`.
- **Forecast beta**: ARIMA or exponential smoothing via `statsmodels`, used only when `HAS_STATSMODELS` is true.
- Always compute returns as `pct_change()` on price series, and `dropna()` after diffing.

## Options pricing conventions (from `options_project`)

- **Black-Scholes**: implement `black_scholes(S, K, T, r, sigma, option_type='call')` returning a dict of `price`, `delta`, `gamma`, `vega` (per 1% vol, i.e. divided by 100), `theta` (per day, i.e. divided by 365) — each rounded to 4 decimals. Support both `'call'` and `'put'` via the same function rather than duplicating it.
- **Binomial tree**: use for American-style or early-exercise features; validate convergence to the Black-Scholes price as steps increase (a convergence plot, as in `binomial_convergence.png`, is a good sanity check to offer).
- **Real options**: frame corporate decisions (e.g. expand/abandon/defer) as call/put options on an underlying project value — reuse the Black-Scholes/binomial engine rather than writing a separate pricing formula.
- **Dashboard**: aggregate multiple pricing outputs (price vs volatility, price vs strike, Greeks) into one multi-panel `matplotlib` figure rather than separate scripts producing separate plots.

## Guardrails

- Ship a known-answer test with every new pricing function — e.g. assert the Black-Scholes call price for `S=100, K=100, T=1, r=0.05, sigma=0.2` matches the textbook value (~$10.45) before trusting further changes.
- State the formula in comments/docstring alongside the code (not just the code) so a sign or annualisation error can be checked against coursework.
- Correctly annualise daily volatility (`sigma_daily * sqrt(252)`) — flag explicitly whenever a volatility input is daily vs annualised.
- Keep S, K, T, r, sigma as clearly named function arguments, never hardcoded inside the pricing formula body.

## Output

Standalone, numbered Python scripts (or dashboard combining them) with an inline plain-English explanation of each formula, a small worked example run in `__main__` / bottom-of-file style (matching the existing scripts), and a matplotlib chart saved or shown where relevant.
