# Orange Juice Price Optimization — Python

**Role context:** Regression-based price elasticity and profit optimization analysis on a retail orange juice dataset with 5 competing products. Demonstrates applied econometrics and Monte Carlo simulation for pricing decisions.

---

## Business Context

A retailer carries 5 competing orange juice SKUs across multiple store-weeks. Each SKU has a price, a gross margin, and binary flags for in-store display and feature advertising. The goal: **find the price point for each product that maximises profit**, accounting for how demand responds to price changes and how competitors' prices affect each product's sales.

This type of analysis is foundational in retail product management, revenue management, and any marketplace where dynamic pricing is a lever.

---

## Methodology

### 1. Exploratory Data Analysis
- Distribution checks on sales volume, prices, and gross margins per product
- Binary correction on `disp` and `feat` variables (values > 0.5 → 1)
- Log transformation of sales revenue and prices to satisfy OLS normality assumptions and enable elasticity interpretation

### 2. Log-Log Demand Regression (Price Elasticity)
The core demand model takes the form:

```
log(Sales_i) = β₀ + β₁·log(Price_i) + β₂·log(Price_j) + ... + β·feat + β·disp + ε
```

**Why log-log?** In a log-log specification, the coefficient on log(Price) is directly interpretable as the **price elasticity of demand**: a 1% increase in price leads to a β% change in sales. This is the standard specification in economics and revenue management literature.

**Cross-price effects:** The model includes competitors' prices as regressors. A positive cross-price coefficient means that if a competitor raises their price, your sales increase (substitution effect) — quantified.

### 3. Monte Carlo Profit Simulation (10,000 iterations)
To find the optimal price, a simulation sweeps the observed price range:

```python
for price_curr in np.arange(min_price, max_price, 0.001):
    for sim in range(10_000):
        # Sample regression coefficient uncertainty (β ~ Normal(mean, σ))
        # Predict sales at this price
        # Calculate profit = sales × volume × price × gross_margin
```

**Why Monte Carlo over analytical optimisation?** The regression coefficients themselves have uncertainty (captured by the standard error). Monte Carlo propagates that uncertainty into the profit distribution — you get a confidence band around the optimal price, not just a point estimate. This is more honest and more actionable for decision-makers.

### 4. Gross Margin Optimisation
A secondary model estimates how gross margin varies with price (endogenous margin relationship), adding a second dimension to the profit surface.

---

## Key Outputs

| Product | Own-Price Elasticity | Cross-Price Effect | Optimal Price Range |
|---------|---------------------|-------------------|---------------------|
| 1–5 | Estimated via log-log OLS | Estimated from model | Derived from MC simulation |

*See notebook for full coefficient tables and profit distribution plots.*

---

## Techniques Demonstrated

| Technique | Tool | Purpose |
|-----------|------|---------|
| Log-log OLS regression | `statsmodels.formula.api` | Price elasticity estimation |
| Cross-price demand modeling | `statsmodels` | Competitive substitution effects |
| Log transformation | `numpy` | Normalise right-skewed sales distributions |
| Monte Carlo simulation | `numpy.random` | Profit uncertainty quantification |
| Profit surface analysis | `pandas`, `matplotlib` | Identify price–margin trade-off |
| Distribution visualisation | `seaborn` | Normality checks, profit distribution |

---

## How to Run

```bash
pip install pandas numpy matplotlib seaborn statsmodels openpyxl
jupyter notebook OJ_optimization_project.ipynb
```

> **Note:** Requires `OJ_data.xlsx` in the same directory. The dataset contains weekly sales data for 5 OJ products across retail stores with price, gross margin, display, and feature variables.

---

## Concepts

- **Price elasticity of demand:** % change in quantity demanded per 1% change in price. Elastic demand (|ε| > 1) means price cuts increase revenue; inelastic (|ε| < 1) means they don't.
- **Log-log regression:** Taking logs of both dependent and independent variables transforms elasticity estimation into a simple linear regression problem.
- **Cross-price elasticity:** Measures how demand for product A responds to price changes in product B. Positive = substitutes; negative = complements.
- **Monte Carlo simulation:** Using random sampling to propagate parameter uncertainty through a model — produces a distribution of outcomes rather than a single point estimate.

---

*Dataset sourced from a retail analytics course. Analysis completed as part of graduate-level coursework.*
