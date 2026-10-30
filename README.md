
# Turnover X-Ray · People Analytics

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?logo=python&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-PBIP%20%7C%20TMDL%20%7C%20DAX-F2C811?logo=powerbi&logoColor=black)
![HTML](https://img.shields.io/badge/Dashboard-HTML%20%2B%20ECharts-E34F26?logo=html5&logoColor=white)
![Status](https://img.shields.io/badge/status-complete-0CA30C)

A **People Analytics** project that uses statistics and machine learning to understand **why employees leave**, **who is likely to leave** and **what it costs to replace them**. It has two deliverables: an interactive HTML dashboard and a full Power BI project with a TMDL semantic model and 146 DAX measures.

> **About the data:** the analysis uses the **IBM HR Analytics Employee Attrition & Performance** dataset, a **fictional, public** dataset created by IBM data scientists for data-science study. It has 1,470 employees and 35 variables and no real people. The figures illustrate the method; they don't describe any real company.

> The dashboards are in Brazilian Portuguese.

![Overview](docs/01_visao_geral.png)

---

## Business questions

| # | Question | Key finding |
|---|---|---|
| 1 | What is the overall attrition rate? | **16.1%** (237 of 1,470), 95% CI 14.3%–18.1% |
| 2 | Which departments and roles have the highest turnover? | Sales leads with 20.6%. Sales Representative reaches **39.8%** (2.5× the average) |
| 3 | Do lower-paid employees leave more often? | Yes. The median income of leavers is **38% lower**, and the effect holds within the same job level |
| 4 | How does job satisfaction relate to attrition? | Attrition falls at every step up the satisfaction scale: **37%** at critical engagement vs **8%** at very high engagement |
| 5 | Which age groups and tenure ranges are most at risk? | Employees aged 18–24 with up to 1 year at the company: **60%** attrition |
| 6 | What is the profile of a high-risk employee? | Entry level, no stock options, single, little work experience, frequent business travel |
| + | Who is likely to leave, and what does the replacement gap cost? | **174 expected exits** in 12 months, ~21 open positions on average, ~$3.1M cost |

---

## Attrition risk simulator

Set up an employee profile and the simulator instantly returns the **probability of leaving**, shows **which factors push the risk up or down** and estimates the **replacement cost**.

![Simulator](docs/04_simulador.png)

- Multivariate logistic regression with 23 variables: standardized numeric inputs, odds ratios with 95% CIs, VIF < 5.
- Out-of-sample validation with stratified 5×3 cross-validation: **AUC 0.80**. The top 20% highest-risk employees account for **55%** of actual exits.
- Benchmarked against Random Forest and Gradient Boosting. The interpretable model scored highest.

---

## Dashboard highlights

![Risk profile](docs/02_perfil_de_risco.png)

- **Filters that recompute the statistics in the browser:** Wilson CIs, chi-square, Cramér's V, Cochran-Armitage trend, Mann-Whitney, Kaplan-Meier with log-rank, and AUC.
- A **funnel plot** shows which roles are true outliers and which just reflect the natural noise of small groups.
- **Kaplan-Meier survival curves** by job level, marital status, stock options, business travel and age group.
- **Risk personas** from a decision tree, plus a **priority retention list**.
- **Replacement gap:** a pay × satisfaction × age × tenure model with vacancy days, productivity ramp-up and hiring cost (SHRM 2025 benchmarks), plus retention scenarios with ROI.

![Replacement gap](docs/03_gap_de_reposicao.png)

---

## Statistical methods

| Technique | Purpose |
|---|---|
| Wilson 95% CI | Reliable intervals for rates, even in small groups |
| Chi-square + Cramér's V | Association between each variable and attrition, and effect size |
| Haberman adjusted residuals | Whether a category differs from the rest (\|z\| > 1.96) |
| Cochran-Armitage | Trend across ordered scales (satisfaction, bands) |
| Mann-Whitney, Welch, Cohen's d | Compare numeric variables between leavers and stayers |
| Benjamini-Hochberg (FDR) | Correction for 42 simultaneous tests |
| Information Value / WoE | Predictive power of each variable |
| Kaplan-Meier + log-rank | Retention over tenure |
| Logistic regression + odds ratios | Effect of each factor, holding the others constant |
| Decision tree | Readable risk personas |
| Little's law | Average open positions = exits per day × time to replace |

---

## Power BI

- **PBIP** project with a **TMDL** semantic model and a **PBIR** report: 9 pages, 101 visuals.
- **146 DAX measures** organized in display folders. They include Wilson CIs, chi-square with Cramér's V, Kaplan-Meier, AUC computed in DAX, ▲▼ arrows with conditional colors, and the replacement-gap model.
- A **field parameter** lets you analyze attrition across 24 dimensions, alongside what-if parameters and assumptions by job level.
- The report JSON files were validated against Microsoft's official schemas.

![Page layout](docs/powerbi_layout_paginas.png)

---

## Repository structure

```
├── dashboard/
│   ├── HR_Attrition_Dashboard.html     # full dashboard (9 tabs)
│   └── Simulador_Risco_Saida.html      # standalone simulator
├── powerbi/
│   ├── HR_Attrition.pbip               # open in Power BI Desktop
│   ├── HR_Attrition.SemanticModel/     # TMDL: tables, measures, relationships
│   ├── HR_Attrition.Report/            # PBIR: pages and visuals
│   ├── data/                           # CSVs loaded by the model
│   └── kit/                            # DAX, Power Query, TMDL script and theme
├── analise/                            # Python code that generates everything
├── dados/                              # original dataset
└── docs/                               # full documentation and images
```

## How to run

**Dashboard and simulator:** download the files in `dashboard/` and open them in a browser. An internet connection is needed to load the chart library and fonts.

**Power BI:**
1. Clone or extract the repository to `C:\HR_Attrition_People_Analytics\`. If you use another folder, update the `PastaDados` parameter after opening the project.
2. Open `powerbi/HR_Attrition.pbip` in Power BI Desktop.
3. Click **Refresh**.

**Reproduce the analysis:**
```bash
pip install -r analise/requirements.txt
python analise/analysis.py dados/WA_Fn-UseC_-HR-Employee-Attrition.csv build
python analise/build_html.py analise/dashboard_template.html build/dashboard_data.json dashboard/HR_Attrition_Dashboard.html
python analise/build_simulador.py build analise/dashboard_template.html dashboard/Simulador_Risco_Saida.html
```

The full methodology, worked formula examples, data dictionary and limitations are in [`docs/DOCUMENTACAO.md`](docs/DOCUMENTACAO.md) (Portuguese).

---

## Stack

Python (pandas, scipy, statsmodels, scikit-learn, lifelines) · Power BI (PBIP, TMDL, DAX, Power Query) · HTML/CSS/JavaScript (Apache ECharts)

## Limitations

- The dataset is fictional and has no dates. Kaplan-Meier uses tenure as a proxy, and the 12-month horizon is an assumption.
- Association is not causation. Odds ratios show where to look, not the guaranteed effect of an intervention.
- The overtime variable was deliberately excluded from the analysis.

## Author

**Romulo Azzi**: BI, data and marketing · MBA in People Management and Leadership (PUC)
<!-- add your LinkedIn link here -->

## Sources

- IBM HR Analytics Employee Attrition & Performance (fictional dataset for study, available on Kaggle)
- SHRM, *2025 Recruiting Benchmarking Report* (time to fill and cost per hire)
