# Asset Allocation in Excel: Efficient Frontier & Minimum-Variance Benchmark
<img width="1213" height="506" alt="01_inputs_portfolios_table png" src="https://github.com/user-attachments/assets/98deb05f-1516-4181-a717-93ac4876681a" />

## Overview
This project shows how I built a simple **two-asset portfolio allocation model in Excel**.  
The objective is to decide how to allocate **$1,000** between **Santander** and **CaixaBank** by evaluating **expected return** and **risk** (variance / volatility) across different portfolio weights.

**Work note:** I built the Excel model and computed the portfolio metrics myself for each simulated allocation (101 portfolios): **E[Portfolio Variance]**, **E[Portfolio Std Dev]**, and **E[Portfolio Return]**, using the input parameters defined in the assumptions.

---

## Inputs (assumptions)
- Asset 1: **Santander**
  - Expected return: **10%**
  - Variance: **0.0036** (Std Dev: **6%**)
- Asset 2: **CaixaBank**
  - Expected return: **15%**
  - Variance: **0.0064** (Std Dev: **8%**)
- Correlation: **0.6**

This is a simulation exercise: expected returns, volatilities, and correlation are assumed inputs used to illustrate the Markowitz framework.

**Note on expected returns:** In this project, the expected returns for Santander (10%) and CaixaBank (15%) are **assumptions**, as stated in the inputs, and are used to illustrate the mean–variance allocation logic.

**Note on return calculation:** If expected returns were estimated directly from historical prices, I would prefer using **log returns** to better capture intermediate price movements between periods (time-additive property), instead of relying only on simple period-to-period changes.



---

## Step 1 — Simulating 101 hypothetical portfolios
I simulated **101 portfolios** by changing the weight invested in Santander and in CaixaBank:
- w₁ = 0.00, 0.01, 0.02, …, 1.00  
- w₂ = 1 − w₁

This produces a grid of portfolios to observe how risk and return evolve with allocation.

---

## Step 2 — Computing portfolio return and risk
For each portfolio, I computed:
- Expected portfolio return: E[Rp]
- Expected portfolio variance: Var(Rp)
- Expected portfolio volatility: σₚ

Formulas (two-asset case):
- E[Rp] = w₁ · E[R₁] + w₂ · E[R₂]
- Var(Rp) = w₁² · σ₁² + w₂² · σ₂² + 2 · w₁ · w₂ · Cov(R₁,R₂)
- σₚ = √Var(Rp)
- Cov(X,Y) = ρₓᵧ · σₓ · σᵧ


---

## Step 3 — Plotting the Efficient Frontier (Markowitz)
I plotted the **Markowitz efficient frontier** using the 101 simulated portfolios (risk on the x-axis, expected return on the y-axis).

From the efficient frontier, I selected a **low-vol point** offering a **strong risk/return trade-off**:
- Expected return: **~11.7%**
- Standard deviation: **~6.0%**


<img width="850" height="533" alt="03_efficient_frontier png" src="https://github.com/user-attachments/assets/369fac24-a959-4b86-8e50-d5a73f14be83" />

---

## Step 4 — Identifying the portfolio on the simulation table
After selecting the point on the efficient frontier, I located the matching row in the simulation table. In my simulation table, this corresponds to **Portfolio 67**.


**Portfolio 67** allocates:
- **66%** of wealth to **Santander** → **$660** (out of $1,000)
- **34%** of wealth to **CaixaBank** → **$340** (out of $1,000)

<img width="852" height="243" alt="04_portfolio_67_selection png" src="https://github.com/user-attachments/assets/f0fc6c3d-0ee2-4300-a5bf-b823d2e5420d" />

---

## Step 5 — Minimum-Variance Portfolio (Min-Var)
The **minimum-variance portfolio (Min-Var)** is the portfolio with the **lowest possible volatility (risk)**, regardless of expected return.

On the Markowitz efficient frontier, it is the **left-most point** (smallest σₚ).  
This Min-Var portfolio is a **reference allocation**: it represents the lowest-risk configuration available given the two assets and their correlation.

**Why it matters (asset allocation perspective):**
- Min-Var is a benchmark to measure how far the **current portfolio** (selected in Step 4) is from the **lowest-risk** configuration.
- It helps frame the trade-off: accepting more risk should be justified by a higher expected return.

Screenshots:
<img width="870" height="522" alt="05_min_var_point_frontier png" src="https://github.com/user-attachments/assets/b44c3ed8-223f-4bda-a0ea-f29c0905b323" />
<img width="793" height="527" alt="06_min_var_table png" src="https://github.com/user-attachments/assets/6b603bb0-53d9-4f13-96f7-837ff21a9a4f" />

---

## Step 6 — Min-Var vs Current Portfolio (Risk/Return Trade-off)
Comparing the **Min-Var portfolio** with the **Current portfolio** helps to quantify:
- how much **extra risk** we are taking, and
- whether that extra risk is **compensated** by a higher expected return.

In this project:
- **Min-Var:** σ ≈ 5.90% and E[R] ≈ 10.85%
- **Current PF (Step 4):** σ ≈ 6.00% and E[R] ≈ 11.70%

**Difference (Current − Min-Var):**
- Volatility: **+0.10 percentage point**
- Expected return: **+0.85 percentage point**

Interpretation:  
The increase in volatility is very small, while the increase in expected return is relatively large. In this case, moving from **Min-Var** to the **Current portfolio** looks like a reasonable trade-off: slightly more risk for a clearly higher expected return, and the Current portfolio remains on the **Markowitz efficient frontier**.

| Situation | Interpretation |
|---|---|
| If σ(Current PF) is close to σ(Min-Var) and the expected return is only slightly higher | The investor is taking more risk for a relatively small gain in return. |
| If σ(Current PF) is much higher than σ(Min-Var) | The portfolio is aggressive relative to the most diversified combination of the same assets. |
| If there exists a portfolio on the efficient frontier with the same volatility as the Current PF but a higher expected return | The Current PF is inefficient (same risk, lower return). |

---

## What I observed while doing this project
This project helped me see how portfolio risk changes when, in the simulation, I varied the correlation between the assets. This simulation exercise helped me see and understand that **"Portfolio risk depends on individual risks AND on covariance"**. Indeed, **if covariance ↓, total risk ↓**, which shows the importance of **controlled diversification**.

the formula of the standard deviation of a 2-asset portfolio shows it:
- σₚ = √( w₁² · σ₁² + w₂² · σ₂² + 2 · w₁ · w₂ · ρ₁₂ · σ₁ · σ₂ )

in the case where **Case correlation = +1** : ρ₁₂ = 1, we get:
- σₚ = w₁ · σ₁ + w₂ · σ₂  
which corresponds to **Zero diversification: both assets move like one single block.**

in the **Case correlation = −1** : ρ₁₂ = −1, we get:
- σₚ = | w₁ · σ₁ − w₂ · σ₂ |  
here we can **choose** w₁, w₂ so that σₚ ≈ 0, which corresponds to **Almost perfect hedge: one asset offsets the other**

---

## Limitations
- Correlation/covariance between the assets is not stable over time, so the diversification benefit observed in the simulation may change across market regimes (especially during stress periods).
- This is a simplified two-asset, frictionless framework: the model uses only two stocks (real portfolios are multi-asset and often include cash, bonds, and other diversifiers), and it ignores real-world frictions such as transaction costs, taxes, liquidity constraints, and rebalancing costs.
