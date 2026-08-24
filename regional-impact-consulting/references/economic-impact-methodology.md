# Economic Impact Methodology

How to build a defensible, multi-source regional economic evidence base — from raw public data to a benchmarked, presentation-ready set of figures.

## 1. Choosing and sequencing data sources

For most regional economic contribution questions, you'll be triangulating across:

- **A regional economic modelling platform** (e.g. REMPLAN or an equivalent) — usually the strongest source *if* it's the same tool the region's own local government already uses and publishes, because that shared provenance makes your numbers instantly credible to the client and any government audience.
- **National statistical office input-output tables** (e.g. ABS National I-O Tables) — authoritative at the national level but *not valid to apply directly at a sub-regional level* without adjustment (see §3).
- **National labour force / census / industry survey data** — for employment counts, industry classification, and population denominators.
- **The client's own financial and membership data** (funding received, grants made, membership numbers) — treat this as ground truth for the client's own activity, but audit it before quoting it (see §4).

Default priority order: platform data > client's own audited records > self-derived national-data estimates. Only fall back to self-derivation for the specific figures the platform genuinely doesn't publish.

## 2. The multiplier ratio trap

Free/public tiers of regional modelling platforms often publish **employment, output, and value-added totals** for a region or industry, but not **Type 1A/1B multiplier ratios** (the ratio that tells you how much additional economic activity is generated per direct dollar/job). These look similar in a spreadsheet — both are just numbers next to an industry name — but they answer completely different questions.

Before using any imported figure downstream:
1. Confirm whether it's a **total** ($ or headcount, describing the size of something) or a **ratio** (a multiplier, dimensionless, describing a flow-on effect).
2. If a workbook or template inherited from someone else has a sheet or column labelled "multipliers" — verify the actual contents match the label. This is a common, easy-to-miss mislabeling error, and every calculation built on top of it inherits the error silently.
3. If the platform doesn't publish the ratio you need, check for a **regional aggregate toggle or benchmark view** first (many platforms let you view a whole region, not just one LGA/sub-area, as a single profile) — this is usually enough for comparative framing even without the exact multiplier.

## 3. When you have to self-derive from national data

If the ratio genuinely isn't available anywhere and the client needs it:

1. Pull the relevant national Input-Output table for the most recent year available.
2. **Regionalise it** using Location Quotients (LQ) built from regional vs. national employment-by-industry data. Applying a national I-O table's ratios directly to a small region without this adjustment is methodologically unsound — regional industry mix differs from the national mix, sometimes drastically.
3. Compute LQs per industry: `LQ = (regional industry employment / regional total employment) / (national industry employment / national total employment)`.
4. Use the LQs to adjust the national technical coefficients matrix before running a Leontief inversion to get regionalised multipliers.
5. **Label every self-derived figure as an approximation** wherever it appears in the final deliverable — in the table caption, not just a footnote — and note the platform-based figure as the preferred source where one exists.

A basic version of this can be built as a Python notebook: ingest the I-O table, clean/reshape it, compute LQs from employment data, build the regionalised coefficient matrix, invert it. Offer to draft this skeleton if the user needs it and hasn't already got one.

## 4. Standard integration template

A workbook with these tabs has worked well for keeping a multi-person data integration effort auditable:

- **Guide** — one-page explanation of what every other tab contains and how to add new data to it, written for a teammate who wasn't there when it was set up.
- **[Region]_Master** — one row per sub-region/area, the headline comparable metrics.
- **Industry_Detail** — full industry-by-industry breakdown per sub-region.
- **Multipliers** (only if genuinely multiplier ratios — see §2) — kept separate from totals so the two categories can never be pasted into the same column by accident.
- **Data_Gaps_Log** — every place where a needed figure wasn't available, why, and what was used instead (or left blank). This tab is as important as the data tabs themselves; it's what lets you write an honest limitations section later.
- **Sources_Audit** — URL/publication, date pulled, and which specific figure came from where, so a client or examiner can retrace every number.

## 5. Standard comparative analyses worth running

These recur across most regional contribution engagements:

- **Representation index**: (share of regional employment in the sector of interest, for a given sub-area) ÷ (same share, region-wide average). Values above 1 mean the sub-area is over-represented in that sector relative to the region; below 1 means under-represented. Useful for showing which sub-areas most depend on or benefit from the sector.
- **Per-capita output/GRP cross-section**: compare a per-capita economic metric across all sub-areas side by side — a simple bar comparison often does more persuasive work than a single regional average.
- **Relative sector size**: express the sector of interest's output as a percentage of a comparison sector (e.g. the sector under transition/decline) to make the scale of the gap or opportunity concrete rather than abstract.
- **Funding trend**: year-over-year percentage change in the client's public/grant funding, especially where a cliff or sharp drop exists — this is often the single most persuasive advocacy chart in the deck.
- **Re-granting transparency gap**: if the client re-distributes funding it receives (e.g. as small grants to third parties) and there's no recipient-level geographic breakdown available, flag this explicitly as an evidence gap rather than estimating a distribution — it's a legitimate finding about the client's own reporting, not a hole to be papered over.
