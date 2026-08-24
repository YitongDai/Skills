---
name: dcf-valuation-model
description: Build a company valuation model (DDM, FCFE/levered DCF, FCFF/unlevered DCF) as a formatted Excel workbook using openpyxl, following Yitong's established conventions from ACCT90002 coursework (valuation_model.py). Use when asked to build, extend, or fix a DCF/DDM/comps valuation workbook, value a company from 10-K/annual-report data, or produce a "blue inputs / black formulas / green links" style finance Excel model in Python.
---

# DCF / DDM Valuation Model Builder

Reusable workflow and formatting conventions for building equity valuation workbooks in Python with `openpyxl`, matching the style already established in Yitong's `valuation_model.py` (ACCT90002, University of Melbourne — DDM, FCFE, FCFF).

## Workflow

Follow this six-step cycle — do not generate the whole workbook in one shot:

1. **Plan (no code)** — confirm in plain English: which method(s) (DDM / FCFE / FCFF / comps), what output (single workbook, which sheets), what data source (10-K, annual report, CSV), and the forecast horizon.
2. **Scaffold** — write sheet layout and section headers first (e.g. Assumptions, FCFF Build, WACC, Sensitivity, Output Summary) before filling in formulas.
3. **Generate one section at a time** — Assumptions/inputs → forecast drivers → FCFE/FCFF build → discount rate (WACC or cost of equity) → terminal value → sensitivity table.
4. **Verify** — sanity-check the implied share price against a rough market-cap check or a textbook example before trusting it.
5. **Refine** — give targeted follow-ups ("only change the terminal growth assumption cell", "add a FCFE row") rather than regenerating the file.
6. **Document last** — write the header docstring (what it does, `python valuation_model.py` usage, output filename) once the logic is final.

## Formatting conventions (must follow exactly)

These are Yitong's established conventions — always reuse them, never invent a different colour scheme:

- **Font colour = data type**: blue `0000FF` = hardcoded input the user edits; black `000000` = formula/calculation; green `006400` = cross-sheet link.
- **Background**: navy `1F3864` section headers (white bold text), mid-blue `2E75B6` sub-headers, light blue `D6E4F0` alternating rows, yellow `FFFF00` for key assumption cells that must stand out, white/grey `F2F2F2` for normal cells.
- **Number formats**: currency `$#,##0.00` or `$#,##0`, percent `0.00%`, multiples `0.0x`.
- Font is Arial size 10; thin grey borders (`BFBFBF`) on data cells; medium navy border under section headers.
- Build reusable style-helper functions at the top of the script (`style_header_cell`, `style_input_cell`, `style_formula_cell`, `style_link_cell`, `fill()`, `center()`, `thin_border()`) rather than repeating style code inline for every cell — mirror the structure already in `valuation_model.py`.

## Guardrails (finance-specific)

- Never let the model silently invent a number — every hardcoded assumption must be a blue input cell with a visible label, not buried inside a formula.
- State the formula in plain English before writing code for WACC, cost of equity (CAPM), FCFE vs FCFF sign conventions, and terminal value (Gordon growth vs exit multiple) — sign errors here are easy to miss visually but expensive.
- Be explicit about mid-year vs year-end discounting convention and keep it consistent across the whole workbook.
- Show both FCFE and FCFF where relevant, and reconcile them, rather than presenting only one.
- Parametrise ticker, file paths, and forecast years as variables/arguments near the top of the script instead of hardcoding them mid-file.
- For any function producing a number that feeds a decision, offer a known-answer check (e.g. verify the WACC arithmetic against a hand-computed example) before treating the output as final.

## Output

- A single `.xlsx` workbook via `openpyxl.Workbook()`, saved with a descriptive filename (e.g. `valuation_output.xlsx`).
- Include a short module docstring at the top: course/context, what the model computes, how to run it, and what file it produces — same pattern as the existing header in `valuation_model.py`.
