# Stock Valuation Workflow Checklist

> **Note:** I keep this workflow as a reusable checklist for building different equity valuation projects in Python.  
> It will guide the structure of my implementations for **DCF valuation**, **Comparable Multiples valuation**, and the **RIM and DDM**.  
> The goal is to follow the same professional process each time (question → assumptions → data → cleaning → model → checks → sensitivity → output)

## Context

I started this valuation workflow as I built foundations in finance and data tools:
- I followed a graduate course at **CBS**, where I studied the **DCF (Discounted Cash Flow)** method.
- I then completed the **Bloomberg Market Concepts (BMC)** certificate.
- I also did self-study and personal research.
- In parallel, I am finalizing several **Python certificates and training** to strengthen my technical skills.

## Purpose of this checklist

This document is a **practical checklist** to regroup all the key steps of a valuation project and, most importantly, to highlight the **critical points** where mistakes often happen (data consistency, assumptions, EV vs equity bridge, terminal value, sensitivity, traceability, etc.).

**Additional goal (workflow discipline):**
- separate **Inputs / Assumptions / Outputs** clearly so that any valuation can be rerun later with the same results
- keep an **audit trail** (sources + pull date + data version + assumptions version) for every run
- build reusable “bricks” so the same process can be applied to many stocks (multi-stock workflow)

## Evolving workflow

This workflow is **evolving** and will be updated as my understanding improves  
It should **not** be taken as “gospel”; it is a living reference designed to help me, avoid common pitfalls, and produce cleaR outputs.

**Good practice for an evolving workflow:**
- add “common pitfalls” under each step (what typically breaks)
- add “minimum checks” (warnings) to prevent silent mistakes
- keep assumptions in a versioned file (ex: `configs/stocks/<ticker>.yaml`)

---

## Stock Valuation Workflow (Checklist)

> Goal: keep this as a “don’t miss steps” checklist when we'll build Python valuation projects.  
> For each step, we try to answer **why it matters**.

### 1) Define the business question
- **What are we valuing (company/ticker)?**
  - Importance: avoids valuing the wrong entity (parent vs subsidiary), wrong share class, wrong currency, or wrong listing. Also defines what “equity” we are pricing (common shares, ADR, etc).
  - Additional checks:
    - confirm the exact **listing** (exchange) and **share class**
    - confirm whether the valuation target is **equity** (price per share) or **enterprise value**
    - confirm whether the company has **multiple share classes** (A/B, ADR ratio, etc.)
- **Why are we valuing it (investment decision, report, screening)?**
  - Importance: sets the level of rigor. A quick internal check can be simple, but an investor memo needs stronger assumptions, documentation, and consistency checks.
  - Additional reminder:
    - the output format should match the purpose (quick range vs full memo-style report)
- **Which method: DCF / Multiples / DDM / RIM?**
  - Importance: the method must match the business model and data quality.
  - DCF fits when cash flows can be modeled.
  - Multiples fit when the firm is comparable to peers and the market prices the sector consistently.
  - DDM fits mainly when dividends are stable and meaningful.
  - RIM fits when **book value and earnings are meaningful**, and when cash flows/dividends are harder to model.
  - Additional reminder:
    - it is normal to run **2 methods** (ex: DCF + multiples) to triangulate a valuation range
- **Horizon, currency, update frequency**
  - Importance:
    - Horizon (e.g., 5–10 years) changes how much the valuation depends on the terminal value.
    - Currency matters for rates, inflation, and comparability.
    - Update frequency decides how automated the pipeline must be (one-off vs monthly vs daily).
  - Additional checks:
    - confirm discount rate inputs are in the **same currency** as cash flows
    - confirm if you want a workflow that supports **refreshing** valuations (monthly/quarterly)

**Audit trail**
- store a small metadata record per run:
  - ticker, company name, share class, currency
  - valuation method(s)
  - horizon and forecast period
  - date/time the run was executed
  - data source(s) used and pull date(s)

---

### 2) Set the key assumptions 
- **Why assumptions are critical**
  - Importance: assumptions are the “engine” of the model. Two analysts can use the same data and get very different values because of different assumptions.
  - Good practice: assumptions must be **explicit**, **consistent**, and **defensible** (not hidden in formulas).
  - Additional good practice:
    - keep assumptions in a dedicated file (ex: YAML) so they are visible and versioned
    - separate “base case” assumptions from “bull/bear” scenarios
- **Growth assumptions**
  - Importance: drives future revenue and cash flows. If growth is too optimistic/pessimistic, the valuation becomes unrealistic.
  - Common pitfalls:
    - growth assumptions inconsistent with reinvestment/capex needs
    - terminal growth higher than long-term nominal growth expectations
- **Margins (profitability)**
  - Importance: explains how much of revenue becomes operating profit/cash. Small margin changes can have a big impact.
  - Common pitfalls:
    - assuming mean reversion without justification (or assuming expansion forever)
- **Taxes**
  - Importance: affects after-tax cash flows. Wrong tax rates distort the valuation.
  - Common pitfalls:
    - mixing effective tax rate vs statutory without consistency
- **Capex (investment)**
  - Importance: captures the money needed to maintain or grow the business. Ignoring capex can overestimate free cash flow.
  - Common pitfalls:
    - capex sign errors (capex accidentally positive)
- **Working capital (WC)**
  - Importance: growth often “consumes” cash (inventory/receivables). Missing WC effects can inflate FCF.
  - Common pitfalls:
    - forgetting WC changes when revenue grows
- **Discount rate (WACC / cost of equity)**
  - Importance: converts future cash flows into today’s value and reflects risk. A small change in WACC can move fair value a lot.
  - Common pitfalls:
    - mixing country/currency assumptions (rf, ERP, beta not coherent)
    - WACC lower than risk-free rate (should trigger a check)
- **Terminal value choice (g or exit multiple)**
  - Importance: terminal value can be a large part of the total valuation. Bad terminal assumptions create misleading results.
  - Common pitfalls:
    - terminal value becomes “too big” (dominates the entire valuation)
    - exit multiple inconsistent with peer multiples and cycle position

**Assumptions discipline**
- write assumptions as explicit parameters:
  - base case
  - bull case
  - bear case
- keep a log of:
  - why each assumption is chosen
  - what source or reasoning supports it
  - what range is considered plausible

---

### 3) Collect data 
- **Why data collection matters**
  - Importance: the model is only as good as its inputs. Wrong data → wrong valuation, even with perfect formulas.
  - Good practice: always keep **sources** and **dates** (audit trail).
  - Additional good practice:
    - cache raw API pulls (do not repeatedly re-download)
    - store “raw snapshot” vs “cleaned normalized dataset”
- **Financial statements**
  - Importance: needed to compute cash flows, margins, reinvestment, and to understand business performance over time.
  - Additional checks:
    - confirm whether you use FY, TTM, quarterly, or a mix (and document it)
- **Shares outstanding**
  - Importance: converts equity value into **price per share**. Using the wrong share count (basic vs diluted) changes the final value.
  - Additional checks:
    - track **basic vs diluted** explicitly
    - document the share count date (latest quarter vs average)
- **Debt and cash (net debt)**
  - Importance: required to move from **enterprise value (EV)** to **equity value**. Forgetting net debt is one of the most common valuation errors.
  - Additional checks:
    - confirm what is included in debt (ST debt, LT debt, leases if applicable)
    - confirm cash definition (cash vs cash + marketable securities)
- **Market inputs (risk-free rate, beta, equity risk premium if used)**
  - Importance: required for discount rates and risk. If these are outdated or inconsistent (currency/region), the discount rate becomes incorrect.
  - Additional checks:
    - store the pull date for rf/beta/ERP
    - store the chosen proxy (government bond, region, maturity)
- **Comparable companies (for multiples)**
  - Importance: peers define what “market is paying” for similar businesses. Bad peer selection makes multiples meaningless.
  - Additional checks:
    - document peer selection logic (sector, business model, geography)
    - ensure peer metrics use consistent periods (TTM vs FY)
- **Consistency across sources**
  - Importance: mixing FY vs TTM, different currencies, or different accounting definitions can silently break the model.
  - Additional checks:
    - create a “data dictionary” (definitions, units, currency, period) for key fields

**Audit trail**
- keep a small table (or file) with:
  - data field name
  - source (API / filing / terminal)
  - pull date
  - unit (USD, millions, etc.)
  - period definition (FY, TTM, quarterly)

---

### 4) Clean and normalize the data 
- **Why cleaning matters**
  - Importance: raw finance data is messy. If periods, units, and definitions are not consistent, the model can be “right” mathematically but wrong financially.
  - Goal: make all inputs **comparable** and **usable** for the valuation formulas.
  - Additional reminder:
    - the cleaning step should produce a normalized dataset that can be reused in all valuation models
- **Align time periods (FY vs TTM vs quarterly)**
  - Importance: mixing annual and trailing data creates distorted growth and margin numbers.
  - Additional checks:
    - document which period is used for each metric (ex: revenue = FY, margin = TTM)
- **Normalize units (thousands / millions / billions)**
  - Importance: unit mistakes are a common source of “10×” or “100×” valuation errors.
  - Additional checks:
    - enforce one standard unit in the normalized dataset (ex: all in millions)
- **Standardize currency**
  - Importance: discount rates and cash flows must be in the same currency to be meaningful.
  - Additional checks:
    - flag inconsistent currency fields before running valuation mechanics
- **Handle one-offs / non-recurring items**
  - Importance: unusual charges/gains can inflate or depress earnings and cash flows. If we want a “normal” valuation, we need a cleaner baseline.
  - Additional checks:
    - log one-off adjustments explicitly so they can be reviewed later
- **Check basic consistency**
  - Importance: quick sanity checks catch broken data (missing values, sign errors, wrong totals, negative shares, etc.).
  - Example checks: debt ≥ 0, shares > 0, cash flow signs make sense, CAPEX not accidentally positive, etc.
  - Additional minimum checks:
    - dates sorted and no duplicate periods
    - no impossible ratios (ex: negative shares, negative equity when not expected)
    - “unit sanity” (values not off by 100×)

**Common pitfalls**
- mixing FY and TTM silently
- mixing currencies (USD statements + EUR rf rate)
- unit errors (thousands vs millions)
- sign convention errors (capex, debt, WC)
- missing items in net debt definition

---

### 5) Build the valuation mechanics 
- **Why this step matters**
  - Importance: this is where we convert business assumptions + cleaned data into a fair value estimate in a consistent, repeatable way.
  - Goal: a model that is **transparent** (easy to explain) and **reproducible** (same inputs → same outputs).
  - Additional good practice:
    - separate inputs, formulas, and outputs (so the model can be audited)
- **DCF mechanics (high level)**
  - Importance: DCF is driven by cash flows and risk.
  - Steps: estimate **FCF** → forecast → discount at **WACC** → add terminal value → get **enterprise value (EV)** → subtract net debt → get **equity value** → divide by shares → **fair price**
- **Multiples mechanics (high level)**
  - Importance: multiples anchor valuation to how the market prices the sector and peers.
  - Steps: choose peer group → choose metric (P/E, EV/EBITDA, etc.) → compute peer multiple → apply to company metric → adjust for differences → **fair value range**
- **DDM mechanics (high level)**
  - Importance: DDM values equity based on expected dividends.
  - Steps: forecast dividends → discount at **cost of equity** → add terminal dividend value (if multi-stage) → **fair price**
- **RIM mechanics (high level)**
  - Importance: RIM links value to **book value** and **excess returns** (returns above the cost of equity).
  - Steps: start with **book value** → forecast earnings/ROE → compute residual income → discount residual income → add to book value → **fair value**
- **Link between EV and Equity**
  - Importance: many mistakes happen here.
  - Reminder: **EV** values the whole business; **Equity value** is what remains for shareholders after debt is considered.

**EV → Equity bridge checklist**
- start from **Enterprise Value (EV)**
- subtract **net debt** = (total debt − cash & equivalents)
- adjust for **minority interest** (if applicable)
- adjust for **preferred equity** (if applicable)
- add/subtract **non-operating assets/investments** (if applicable)
- result = **Equity Value**
- divide by **diluted shares outstanding**
- result = **Fair Value per Share**

**Common pitfalls**
- forgetting minority interest / preferred equity when relevant
- mixing basic vs diluted shares
- applying EV multiples but comparing to equity metrics (or the opposite)
- terminal value dominates and hides weak forecasts

---

### 6) Run sanity checks 
- **Why sanity checks are critical**
  - Importance: valuation models can produce “clean-looking” numbers that are totally unrealistic. Sanity checks prevent bad decisions based on bad outputs.
  - Goal: confirm the result is **financially plausible**, not just mathematically correct.

**Minimum sanity checks**
- **Terminal value share of EV**: if terminal value > 70–80% of EV, flag and review assumptions
- **WACC vs risk-free rate**: WACC should not be below rf (flag if it is)
- **Terminal growth**: g should be conservative (flag if unrealistic for the currency/region)
- **FCF plausibility**: FCF margin should be plausible vs sector history
- **Margin path plausibility**: margins should not expand forever without explanation
- **Reinvestment consistency**: growth should be consistent with reinvestment (capex + WC)
- **Net debt sanity**: ensure net debt calculation includes the right debt/cash components
- **Share count sanity**: diluted shares should be >= basic shares
- **Units/currency sanity**: no 10× errors; currency consistent across model
- **Range sanity**: if fair value implies extreme upside/downside, verify inputs and bridge

---

### 7) Scenario + sensitivity analysis
- **Why scenarios are required**
  - Importance: valuation is not one “true” number. It is a range based on uncertainty.

**Scenario structure**
- **Base / Bull / Bear** scenarios with explicit differences in:
  - growth
  - margins
  - WACC / cost of equity
  - terminal assumptions
- Sensitivity tables (minimum):
  - **WACC × terminal growth (g)** grid (DCF)
  - **exit multiple × margin** (multiples / DCF terminal multiple)
- Output should always show:
  - value range (min / base / max)
  - which assumptions move value the most

**Common pitfalls**
- changing many assumptions at once without isolating drivers
- not keeping scenarios consistent (bull case with bearish discount rate)
- presenting a sensitivity table without explaining which range is plausible

---

### 8) Produce a clear, traceable output (WHY this step matters + how to do it)
- **Clear output**
  - Include fair value + upside/downside + key assumptions + scenarios/sensitivity
- **Traceable output**
  - Document data sources + version assumptions + separate inputs/outputs

**Output discipline**
- Separate outputs into:
  - **Inputs** (raw + cleaned datasets, cached API pulls)
  - **Assumptions** (versioned config file)
  - **Outputs** (valuation results, tables, plots, report)
- Include in the report:
  - data pull date(s)
  - assumptions version
  - method used (DCF / multiples / RIM / DDM)
  - EV → equity bridge table (explicit)
  - scenario summary + sensitivity tables
- Recommended output artifacts:
  - `reports/<ticker>/report.html` (or PDF later)
  - `reports/<ticker>/tables/*.csv`
  - `figures/<ticker>/*.png`
  - `metadata.json` (audit trail snapshot)

**Final reminder (reusability):**
- the goal is that the same workflow can be reused for any new stock by changing:
  - the ticker
  - the assumptions config
  - the peer group (for multiples)
  - the data source (API/file)
