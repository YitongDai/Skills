---
name: finance-ai-coding-workflow
description: General-purpose workflow and guardrails for AI-assisted coding on finance/quant Python projects (valuation, options, beta, equity analysis), distilled from an audit of Yitong's own "AI coding" folder. Use at the start of any new finance/Python analysis task, when the specific domain (DCF, options, equity analysis, etc.) doesn't have its own dedicated skill, or when asked to review/level-up an existing finance script's structure, tests, or documentation.
---

# Finance AI-Coding Workflow

A domain-agnostic playbook for building finance/quant Python scripts with AI assistance, distilled from reviewing Yitong's own projects (Valuation project, Beta, options_project, Woolworths_Analysis, Fortescue). Use this when no more specific skill (dcf-valuation-model, equity-fundamental-analysis, quant-pricing-toolkit) already covers the task, or as a project-setup checklist alongside one of those.

## The six-step cycle

1. **Plan** — describe the analysis in plain English first: what output, what data, what formulas. No code yet.
2. **Scaffold** — ask for structure only: function signatures, sheet layout, column headers.
3. **Generate** — fill in one section at a time (data load → cleaning → calculations → formatting), not one giant script.
4. **Verify** — run the script; sanity-check the numbers against a known benchmark or textbook case.
5. **Refine** — give targeted follow-ups ("tighten this," "handle missing data," "add the adjusted-EBIT step") rather than broad re-asks or full rewrites.
6. **Document last** — write the header docstring once the logic is final, so documentation matches the finished code, not an earlier draft.

## Core prompting principles

- **State constraints, not just the goal** — before asking for code, specify the audience, output format, and anything non-negotiable (naming conventions, libraries allowed, error handling). This is what makes `valuation_model.py`'s formatting so consistent.
- **Work in small, verifiable steps** — one function or output at a time, run it, check it, then move on (the pattern already used in `options_project`'s numbered files).
- **Ask "why," not just "what"** — request the formula/assumption behind a calculation (WACC, beta method, ARIMA vs exponential smoothing), not just the code, so a silent domain error can be caught.
- **Treat every output as a first draft** — cross-check any number feeding a real decision (DCF value, beta, option price) against a textbook example, coursework, or an independent source.
- **Name data and schema explicitly** — reference exact column names/paths rather than letting the AI guess; parametrise file paths and column names as arguments instead of hardcoding.
- **Ask for tests, not just code** — request a known-answer test alongside any pricing/valuation function.
- **Iterate with diffs, not rewrites** — once something works, ask to "modify only X, leave the rest unchanged" instead of regenerating the whole file.

## Guardrails for financial & quant code

- Never let the AI silently invent a number — flag every assumption explicitly (e.g. highlight inputs, as `valuation_model.py` already does with blue text).
- Watch for subtly wrong finance formulas: FCFE vs FCFF sign conventions, correct annualisation of daily volatility, mid-year vs year-end discounting. Ask for the formula to be shown, not just implemented.
- Pin data sources for reproducibility — explicit date ranges for any market-data download (as `beta.py` already does), since prices change day to day.
- If output is ever shared publicly, be clear it isn't personalised financial advice.
- Rule of thumb: if a number from an AI-generated script would change a real decision, verify it against an independent source before trusting it.

## Project setup checklist

- Put every project folder under git, even solo work — this is what lets an AI review a diff or revert a bad edit (ppt-master already does this; the finance project folders currently don't).
- One `requirements.txt` (or `pyproject.toml`) per project instead of ad hoc `.venv`/`venv` folders with no dependency record — generate it from the imports.
- Split monolithic scripts into load / clean / calculate / export functions or modules, rather than mixing all four in one file.
- Add a short header docstring to every script: what it does, how to run it.
- Add a lightweight `tests/` file per project with two or three known-answer checks on core financial functions.

## Getting more from a prompt library

Generic prompts (e.g. a saved 200-prompt library) work best adapted to finance context rather than used verbatim. Example:

- Generic: "Review this code. Find potential bugs, performance issues, security vulnerabilities and code quality problems."
- Adapted: "Review this DCF script for formula errors, sign-convention mistakes, and off-by-one period issues. Flag anywhere an assumption is hardcoded instead of being a clearly labelled input."

Keep a short personal list (10-15 prompts) of finance-specific reusable prompts — e.g. "sanity-check this valuation output against [benchmark]," "convert this script into load/clean/calculate/export functions," "add pytest known-answer tests to this pricing function."
