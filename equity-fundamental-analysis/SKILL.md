---
name: equity-fundamental-analysis
description: Perform equity fundamental/accounting analysis from a company's reported financial line items (e.g. Woolworths_Analysis, Fortescue/FMG projects) — clean messy CSV financials, compute margins and adjusted/core earnings, and run competitive-advantage frameworks like VRIO. Use when asked to analyse a company's financial statements from a CSV/spreadsheet, adjust reported EBIT for significant items, compare a company to peers, or run a VRIO / competitive-advantage writeup on a resources or consumer-staples company.
---

# Equity Fundamental Analysis

Reusable workflow for turning raw reported financials into a decision-ready analysis, following the pattern already used in the Woolworths and Fortescue (FMG) projects.

## Workflow

1. **Load & clean** — read the CSV with `pandas.read_csv(..., index_col=0)`. Write a small `clean_data(x)` helper that strips `,` and `%` from string cells, then apply it across the whole table with `.map(clean_data).apply(pd.to_numeric, errors='coerce')` — this is the exact pattern already used in `Woolworths_Analysis/analysis.py` and should be reused rather than re-derived.
2. **Name columns explicitly** — assign long/special-character column names (e.g. `'F23 (2023财年)'`) to short variables (`col_f23`) once, instead of repeating the literal string everywhere. Never guess a column name — read the actual header first.
3. **Compute base metrics as new rows** — append derived metrics with `df.loc['New Metric'] = ...` (e.g. `Reported EBIT Margin (%) = Group EBIT / Group Sales * 100`) so the output table keeps raw and derived figures together.
4. **Adjust for bias / significant items** — when a "Significant Items" or one-off line exists, compute `Adjusted EBIT = Reported EBIT - Significant Items` and a corresponding `Adjusted EBIT Margin`, so the analysis reflects sustainable core earnings, not just the reported number. Always ask whether such one-off items exist in the data before skipping this step — it is a deliberate judgement call, not something a generic prompt produces on its own.
5. **Competitive-advantage framing (VRIO)** — for resource/commodity or moat-driven companies (as in the Fortescue project), structure the qualitative writeup around VRIO: is the resource/asset Valuable, Rare, Inimitable, and is the Organization set up to exploit it. Pair this with any quantitative peer comparison (e.g. cost curve, margin comparison via matplotlib) rather than doing the qualitative and quantitative pieces in isolation.
6. **Export & round** — round the final table to 2 decimals (`.round(2)`) and export with `to_csv(...)` under a clearly adjusted filename (e.g. `Woolworths_Adjusted_Analysis.csv`) so raw and adjusted outputs never overwrite each other.

## Guardrails

- Never silently drop or ignore an unrecognized column — flag it rather than dropping it, since a mis-cleaned column corrupts every derived metric.
- State the accounting judgement explicitly ("removing X as non-recurring because...") rather than adjusting silently — this is domain judgement the model should surface, not hide.
- Cross-check at least one derived margin or ratio against a public source (annual report, investor presentation) before treating it as final.
- Keep load / clean / calculate / export as separate functions or clearly separated sections rather than one monolithic script, so future edits to one step don't risk breaking another.
- If the analysis will be shared beyond coursework/portfolio use, note clearly that it is not personalised financial advice.

## Output

A cleaned + adjusted CSV (or DataFrame) with both reported and adjusted metrics, plus — where a competitive framework is requested — a short structured VRIO or comparable-peer writeup alongside any chart.
