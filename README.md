# FMCG Supply Chain Optimisation — Optimal Warehouse Shipment Quantity

> **🎓 CAPSTONE PROJECT** · PGP in Data Science & Business Analytics

![Course](https://img.shields.io/badge/Course-Capstone-6f42c1)
![Grade](https://img.shields.io/badge/Grade-A%20(4.0)-success)
![Type](https://img.shields.io/badge/Type-Regression%20%C2%B7%20Ensemble-blue)
![Tools](https://img.shields.io/badge/Tools-Python%20%C2%B7%20scikit--learn%20%C2%B7%20XGBoost-orange)

An end-to-end regression study that predicts the **optimal quantity of product to ship to each warehouse per quarter** for a fast-moving consumer goods company entering the instant-noodles market.

---

## Business Problem

The company runs a national warehouse network with serious supply-chain imbalances:

- In some regions **demand far exceeds supply** → stockouts and lost sales.
- In others **excess inventory accumulates and deteriorates** → wastage and inflated holding costs.

Margins in FMCG are thin, so this mismatch is both operationally disruptive and financially costly. Allocation was previously driven by heuristics or simple averages that ignore each warehouse's operational, locational and infrastructural characteristics.

## Objective

Build a predictive regression model using historical operational and sales data to determine the optimal quantity (in tons) to ship to each warehouse per quarter. The target variable is **`product_wg_ton`** — total product weight shipped to a warehouse over the last three months.

## Data

| Attribute | Detail |
|---|---|
| Warehouses | **25,000** |
| Columns | **24** — operational, logistical, infrastructural, geographical & regulatory |
| Target range | 2,065 – 55,151 tons (mean 22,103 · SD 11,608) |
| Example features | distance from hub, number of workers, retail shops served, refill requests, transport issues, warehouse breakdowns, government checks, competitor presence, distributor count, capacity size, zone, owner type, government certificate grade |

## Approach

1. **Exploratory data analysis** — univariate, bivariate and multivariate analysis across all features (17 figures, 8 key insights).
2. **Data leakage detection** — identified that `storage_issue_reported_l3m` had a near-perfect correlation (**r = 0.99**) with the target and a near-constant ratio across all 25,000 records, proving it was a *derived reporting metric*, not an independent predictor. Dropped unconditionally.
3. **Disciplined preprocessing** — the train-test split was performed **before** any statistical fitting, so all imputation medians/modes, outlier fences and scaling parameters were computed on the 20,000-row training set only, then applied to both sets. This corrected an earlier interim pipeline that had leaked information and inflated results.
4. **Missing values** — median imputation for `workers_num` (~4% missing), mode imputation for `approved_wh_govt_certificate` (~3.6%), and dropped `wh_est_year` entirely (47.5% missing would have forced ~12,000 records to a single imputed value).
5. **Outliers** — IQR capping (Winsorization) on four columns: `workers_num`, `transport_issue_l1y`, `Competitor_in_mkt`, `retail_shop_num`.
6. **Encoding & scaling** — ordinal encoding (capacity size, certificate grade), binary encoding (location, owner type), one-hot with `drop_first` (zone, regional zone), `StandardScaler` fitted on train only.
7. **Baseline** — OLS linear regression (statsmodels) for interpretability and a transparent performance floor.
8. **Advanced models** — six tree-based/ensemble models at defaults: Decision Tree, Bagging, Random Forest, AdaBoost, Gradient Boosting, XGBoost.
9. **Hyperparameter tuning** — tuned the three most promising models (Gradient Boosting, Random Forest, XGBoost).
10. **Evaluation** — **RMSE in tons** as the primary metric (interpretable in business units and appropriately punitive of large errors), with MAE and R² reported alongside.

## Key Findings & Results

| Model | Test RMSE | Notes |
|---|---|---|
| **Gradient Boosting (best)** | **9,909 tons** | Near-zero overfitting gap of 39 tons |
| OLS baseline | 10,379 tons | MAE 8,512 · R² 0.19 · F = 181.9 (p = 0.00) |
| Decision Tree (unconstrained) | — | Perfect train fit, test R² = −0.55 (worse than the mean) |

**Significant OLS predictors**

- Each additional quarterly warehouse **breakdown** adds **~+3,580 tons** to allocation.
- Each **transport incident** reduces allocation by **~−2,170 tons**.
- Each **government certificate grade** upgrade adds **~+1,889 tons** (~9,000 ton/quarter gap between Grade A+ and Grade C).
- **Urban** warehouses receive ~+717 tons vs rural; **temperature-controlled** warehouses ~+489 tons.

**Key EDA insights**

- The target is **multimodal** — warehouses naturally cluster into distinct shipment tiers, so a single average-based allocation strategy is inappropriate.
- **Zone and capacity size** have minimal independent predictive power.
- **Distance from hub** shows no relationship with shipment volume — an untapped logistics cost lever.

## Recommendations

1. Replace heuristic allocation with the tuned predictive model to align supply with actual regional demand.
2. Treat **transport reliability** as the most controllable negative driver — reducing incidents directly increases achievable allocation.
3. Factor **distance from hub** into allocation decisions to cut logistics spend without hurting service levels.
4. Recognise the **distinct shipment tiers** rather than applying one average quantity to all warehouses.
5. Use regional demand patterns to **target advertising** in high-potential catchment areas, and highlight the sustainability benefit of reduced food wastage.

## Skills Demonstrated

End-to-end regression modelling · data-leakage detection · leakage-free preprocessing · missing-value strategy · IQR capping · ordinal / binary / one-hot encoding · OLS with statsmodels · six ensemble & tree-based models · hyperparameter tuning · RMSE / MAE / R² evaluation · translating model output into supply-chain strategy.

## Repository Structure

```
├── notebooks/
│   └── capstone_supply_chain_notebook.html   # full analysis notebook (exported)
├── reports/
│   └── FMCG_Supply_Chain_Capstone_Report.pdf # final capstone report
├── screenshots/
│   ├── notebook_chart_01.png … notebook_chart_10.png
│   └── report_page_01.png … report_page_04.png
└── README.md
```

## Visual Highlights

| Analysis | Visual |
|---|---|
| EDA & target distribution | `screenshots/notebook_chart_01.png` |
| Model comparison & residuals | `screenshots/notebook_chart_05.png` |
| Feature importance | `screenshots/notebook_chart_08.png` |
| Business report | `screenshots/report_page_01.png` |

---

*PGP in Data Science & Business Analytics · Great Lakes Executive Learning / The University of Texas at Austin, McCombs School of Business. Content drawn from the project notebook, final business report and course transcript.*
