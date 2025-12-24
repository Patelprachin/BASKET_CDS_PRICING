# Credit Spread Pricing for a Basket CDS  
### Copula-Based Monte Carlo Pricing of *k*-th-to-Default Credit Instruments

## Overview
This repository implements a **production-style quantitative framework** for pricing **Basket Credit Default Swaps (CDS)** with a focus on **k-th-to-default instruments (1st–5th)**.  

The project addresses a core problem in **credit portfolio modelling and XVA**:  
> *individual default probabilities are observable from market CDS curves, but the joint distribution of default times is latent and must be inferred.*

To solve this, the framework separates:
- **Marginal credit risk** via hazard-rate bootstrapping from CDS curves  
- **Default dependence** via copula-based correlation modelling  

The implementation mirrors methodologies used in **front-office credit structuring, CVA/XVA desks, and credit risk quant teams**.

---

## Key Capabilities
- Bootstrapping **piecewise-constant hazard rates** from CDS term structures (1Y–5Y)
- **Gaussian and Student-t copula** implementations for default dependence
- Correlation calibration from **historical equity returns** (robust to CDS microstructure noise)
- **Monte Carlo pricing engine** for all *k*-th-to-default tranches
- **Low-discrepancy sampling** (Sobol & Halton) for variance reduction
- Full **risk and sensitivity analysis**, including:
  - Default correlation stress tests
  - Single-name credit quality shocks (credit delta)
  - Recovery rate sensitivity
- Extensive **model validation diagnostics** used in XVA model governance

---

## Quantitative Methodology

### 1. Marginal Default Modelling (Hazard Rates)
- Market CDS spreads are used to bootstrap **term structures of hazard rates**
- Survival probabilities are derived under **ISDA-style conventions**
- A constant recovery rate of **40%** is assumed for calibration and sensitivity consistency

This step produces **risk-neutral marginal default distributions**, consistent with single-name CDS pricing.

---

### 2. Default Correlation & Copula Modelling
- Default dependence is modelled using:
  - **Gaussian copula** (benchmark, no tail dependence)
  - **Student-t copula** (captures joint tail risk)
- Correlation matrices are estimated from **weekly equity log-returns**
  - Avoids CDS spread microstructure noise
  - Consistent with industry practice when default data is sparse
- The Student-t copula degrees of freedom are calibrated via **maximum likelihood**
- Model selection is guided by **AIC and diagnostic tests**

This explicitly addresses **wrong-way risk and tail dependence**, which are critical for XVA and stressed credit scenarios.

---

### 3. Monte Carlo Pricing Engine
For each simulation:
1. Generate correlated uniform variables via the chosen copula  
2. Transform uniforms into **exact default times** using inverse hazard functions  
3. Sort default times and identify the *k*-th default  
4. Price premium and protection legs with accrual  
5. Average payoffs to obtain fair spreads  

The engine prices **all tranches (1st–5th) in a single simulation pass**, consistent with efficient desk-level implementations.

---

### 4. Variance Reduction
- **Sobol and Halton low-discrepancy sequences** are used
- Achieves materially faster convergence than pseudo-random Monte Carlo
- Pricing precision of **~1–2 bps at 100k simulations**

This is directly relevant for **desk-level latency and capital sensitivity runs**.

---

## Results & Validation
- Fair spreads satisfy the required **monotonicity condition**:
s₁ ≥ s₂ ≥ s₃ ≥ s₄ ≥ s₅
- Student-t copula produces **higher senior-tranche spreads**, reflecting realistic default clustering
- Correlation sensitivity **increases sharply for higher-order tranches**
- Recovery and credit-quality sensitivities behave consistently with credit theory

The framework passes standard **model risk and validation checks** used in regulated environments.

---

## Relevance to XVA & Credit Quant Roles
This project demonstrates hands-on experience with:
- Credit portfolio modelling
- Copula calibration and diagnostics
- Monte Carlo pricing engines
- Correlation and tail-risk modelling
- Sensitivity analysis used in CVA, FVA, and capital frameworks
- Reproducible, modular Python design aligned with quant libraries

It directly maps to responsibilities found in:
- **Front-Office Credit Quant**
- **XVA / CVA Quantitative Research**
- **Counterparty Credit Risk Modelling**
- **Structured Credit & Correlation Trading**
