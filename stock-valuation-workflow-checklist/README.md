

> **Note:** I keep this workflow as a reusable checklist for building different equity valuation projects in Python.  
> It will guide the structure of my implementations for **DCF valuation**, **Comparable Multiples valuation**, and the **third valuation approach** described in this document.  
> The goal is to follow the same professional process each time (question → assumptions → data → cleaning → model → checks → sensitivity → output) to ensure consistency, traceability, and reliable results.

### Context

I started this valuation workflow as I built foundations in finance and data tools:
- I followed graduate course at **CBS**, where I studied the **DCF (Discounted Cash Flow)** method.
- I then completed the **Bloomberg Market Concepts (BMC)** certificate.
- I also did self-study and personal research.
- In parallel, I am finalizing several **Python certificates and training** to strengthen my technical skills.

### Purpose of this checklist

This document is a **practical checklist** to regroup all the key steps of a valuation project and, most importantly, to highlight the **critical points** where mistakes often happen (data consistency, assumptions, EV vs equity bridge, terminal value, sensitivity, traceability, etc.).

### Important note (evolving workflow)

This workflow is **evolving** and will be updated as my understanding improves and as I build more projects (DCF, comparable multiples, and DDM).  
It should **not** be taken as “gospel”, it is a living reference designed to help me stay consistent, avoid common pitfalls, and produce clear, traceable outputs.

---

## Stock Valuation Workflow (Checklist)

> Goal: keep this as a “don’t miss steps” checklist when we'll build Python valuation projects.
> For each step, we tried to answer why it matterd

### 1) Define the business question
- **What are we valuing (company/ticker)?**
  - Importance: avoids valuing the wrong entity (parent vs subsidiary), wrong share class, wrong currency, or wrong listing. Also defines what “equity” we are pricing (common shares, ADR, etc).
- **Why are we valuing it (investment decision, report, screening)?**
  - Importance: sets the level of rigor. A quick internal check can be simple, but an investor memo needs stronger assumptions, documentation, and consistency checks.
- **Which method: DCF / Multiples / DDM?**
  - Importance: the method must match the business model and data quality.
  - DCF fits when cash flows can be modeled.
  - Multiples fit when the firm is comparable to peers and the market prices the sector consistently.
  - DDM fits mainly when dividends are stable and meaningful.
- **Horizon, currency, update frequency**
  - Importance:
    - Horizon (e.g., 5–10 years) changes how much the valuation depends on the terminal value.
    - Currency matters for rates, inflation, and comparability.
    - Update frequency decides how automated the pipeline must be (one-off vs monthly vs daily).

### 2) Set the key assumptions 
- **Why assumptions are critical**
  - Importance: assumptions are the “engine” of the model. Two analysts can use the same data and get very different values because of different assumptions.
  - Good practice: assumptions must be **explicit**, **consistent**, and **defensible** (not hidden in formulas).
- **Growth assumptions**
  - Importance: drives future revenue and cash flows. If growth is too optimistic/pessimistic, the valuation becomes unrealistic.
- **Margins (profitability)**
  - Importance: explains how much of revenue becomes operating profit/cash. Small margin changes can have a big impact.
- **Taxes**
  - Importance: affects after-tax cash flows. Wrong tax rates distort the valuation.
- **Capex (investment)**
  - Importance: captures the money needed to maintain or grow the business. Ignoring capex can overestimate free cash flow.
- **Working capital (WC)**
  - Importance: growth often “consumes” cash (inventory/receivables). Missing WC effects can inflate FCF.
- **Discount rate (WACC / cost of equity)**
  - Importance: converts future cash flows into today’s value and reflects risk. A small change in WACC can move fair value a lot.
- **Terminal value choice (g or exit multiple)**
  - Importance: terminal value can be a large part of the total valuation. Bad terminal assumptions create misleading results.

### 3) Collect data 
- **Why data collection matters**
  - Importance: the model is only as good as its inputs. Wrong data → wrong valuation, even with perfect formulas.
  - Good practice: always keep **sources** and **dates** (audit trail).
- **Financial statements**
  - Importance: needed to compute cash flows, margins, reinvestment, and to understand business performance over time.
- **Shares outstanding**
  - Importance: converts equity value into **price per share**. Using the wrong share count (basic vs diluted) changes the final value.
- **Debt and cash (net debt)**
  - Importance: required to move from **enterprise value (EV)** to **equity value**. Forgetting net debt is one of the most common valuation errors.
- **Market inputs (risk-free rate, beta, equity risk premium if used)**
  - Importance: required for discount rates and risk. If these are outdated or inconsistent (currency/region), the discount rate becomes incorrect.
- **Comparable companies (for multiples)**
  - Importance: peers define what “market is paying” for similar businesses. Bad peer selection makes multiples meaningless.
- **Consistency across sources**
  - Importance: mixing FY vs TTM, different currencies, or different accounting definitions can silently break the model.

### 4) Clean and normalize the data 
- **Why cleaning matters**
  - Importance: raw finance data is messy. If periods, units, and definitions are not consistent, the model can be “right” mathematically but wrong financially.
  - Goal: make all inputs **comparable** and **usable** for the valuation formulas.
- **Align time periods (FY vs TTM vs quarterly)**
  - Importance: mixing annual and trailing data creates distorted growth and margin numbers.
- **Normalize units (thousands / millions / billions)**
  - Importance: unit mistakes are a common source of “10×” or “100×” valuation errors.
- **Standardize currency**
  - Importance: discount rates and cash flows must be in the same currency to be meaningful.
- **Handle one-offs / non-recurring items**
  - Importance: unusual charges/gains can inflate or depress earnings and cash flows. If we want a “normal” valuation, we need a cleaner baseline.
- **Check basic consistency**
  - Importance: quick sanity checks catch broken data (missing values, sign errors, wrong totals, negative shares, etc.).
  - Example checks: debt ≥ 0, shares > 0, cash flow signs make sense, CAPEX not accidentally positive, etc.

### 5) Build the valuation mechanics 
- **Why this step matters**
  - Importance: this is where we convert business assumptions + cleaned data into a fair value estimate in a consistent, repeatable way.
  - Goal: a model that is **transparent** (easy to explain) and **reproducible** (same inputs → same outputs).
- **DCF mechanics (high level)**
  - Importance: DCF is driven by cash flows and risk.
  - Steps: estimate **FCF** → forecast → discount at **WACC** → add terminal value → get **enterprise value (EV)** → subtract net debt → get **equity value** → divide by shares → **fair price**
- **Multiples mechanics (high level)**
  - Importance: multiples anchor valuation to how the market prices similar companies.
  - Steps: choose peer group → choose metric (P/E, EV/EBITDA, etc.) → compute peer multiple → apply to company metric → adjust for differences → **fair value range**
- **Link between EV and Equity**
  - Importance: many mistakes happen here.
  - Reminder: **EV** values the whole business; **Equity value** is what remains for shareholders after debt is considered.

### 6) Run sanity checks 
- **Why sanity checks are critical**
  - Importance: valuation models can produce “clean-looking” numbers that are totally unrealistic. Sanity checks prevent bad decisions based on bad outputs.
  - Goal: confirm the result is **financially plausible**, not just mathematically correct.
- **Check the big drivers**
  - Importance: understand what actually explains the fair value (growth, margins, WACC, terminal value). If the model is dominated by one extreme assumption, it’s fragile.
- **Check terminal value dominance**
  - Importance: if most of the value comes from the terminal value, small assumption changes can swing the result. That’s a warning sign to review long-term assumptions.
- **Compare to market reality**
  - Importance: compare implied valuation to market cap, EV, and sector multiples. Huge gaps often mean an input or assumption problem (or a strong contrarian view that must be justified).
- **Reasonableness checks**
  - Examples:
    - terminal growth is realistic
    - margins move in a believable way (not magic)
    - reinvestment (capex/WC) is consistent with growth
    - WACC/cost of equity is in a credible range

### 7) Scenario + sensitivity analysis
- **Why scenarios are required**
  - Importance: valuation is not one “true” number. It is a range based on uncertainty. Scenarios help communicate uncertainty clearly.
- **Base / Bull / Bear**
  - Importance: shows how the valuation changes with different stories about the company (strong execution vs normal vs bad outcome).
  - Good practice: change only a few key assumptions per scenario (the ones that truly reflect the story).
- **Sensitivity tables (e.g., WACC × terminal growth)**
  - Importance: shows how fragile or robust the valuation is. If a small change in WACC moves the value a lot, we need to be cautious.
- **Identify the top drivers**
  - Importance: helps focus analysis on what matters most (usually 2–3 assumptions). This is also where we spend most time improving data/assumptions.
- **Decision support**
  - Importance: scenarios + sensitivities turn a valuation into an actual tool for decisions (risk, upside/downside, margin of safety).

### 8) Produce a clear, traceable output (WHY this step matters + how to do it)
- **Why this matters**
  - Importance: in finance, a valuation must be **understandable** (so others can review it) and **traceable** (so we can defend it and reproduce it later). A number without sources and assumptions is not usable in a professional setting.
- **Clear output (make it easy to read)**
  - Include a one-page summary:
    - **Fair value** (price per share) + **upside/downside** vs current price
    - Key assumptions (growth, margins, WACC, terminal value method)
    - Method used (DCF / multiples / DDM)
  - Show the main drivers:
    - What explains the result? (e.g., “value is driven mainly by margins + WACC”)
  - Add scenarios and sensitivity highlights:
    - Base / bull / bear
    - One sensitivity table (e.g., WACC × terminal growth) so the reader sees uncertainty
- **Traceable output (make it auditable and reproducible)**
  - **Data sources**: list where each dataset comes from (filings, API, vendor, date pulled)
  - **Assumption versioning**: record assumption sets with dates (so we can compare runs over time)
  - **Inputs vs outputs separation**
    - Inputs: raw data + cleaned data + assumptions
    - Outputs: final tables/graphs + exported files
  - **Reproducibility**: same inputs should produce the same result
    - keep file names consistent and dated (e.g., `assumptions_2026-03-08.json`)
    - keep a short changelog (“what changed since last run”)
- **Professional deliverables (typical)**
  - Export: Excel/PDF/dashboard
  - A short “investment note” paragraph:
    - “We value X at €Y (Z% upside) based on … Main risks are …”

---

### Common mistakes to avoid (quick reminders)
- Forgetting **net debt** when moving from enterprise value to equity value
- Using an unrealistic terminal growth rate
- Skipping sensitivity/scenario analysis
- Not documenting data sources and assumption versions
