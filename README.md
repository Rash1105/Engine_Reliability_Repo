# Engine Reliability — Predictive Maintenance & KPI Analytics

> Turn operational telemetry into fewer breakdowns, lower downtime, and data‑driven maintenance.

![Status](https://img.shields.io/badge/status-active-brightgreen) ![Python](https://img.shields.io/badge/python-3.9%2B-blue) ![PowerBI](https://img.shields.io/badge/Power%20BI-dashboards-yellow)

---

## 📌 Project Overview
This repo analyzes engine operational data to **identify drivers of breakdown risk** and to **forecast high‑risk units**. It combines a **Python notebook** for data cleaning and a **Power BI** page for executive KPIs.

- **Dataset size:** 316 rows, 15 fields  
- **Target variable:** `high_breakdown_risk` 
- **Goal:** Explain what drives failures, quantify risk, and propose actionable controls (fuel quality, RPM limits, material choice, maintenance timing).


## 🧠 Key Findings (business summary)
- **Breakdown rate ≈ 54%.** Highest‑risk cohort at **8 years** in service.
- **Top drivers:** **Combustion issue type** and **Max RPM**; turbocharger count also contributes.
- **BMEP matters:** Within a given year band, **lower BMEP ↔ higher failure risk** (performance decline signal).
- **Fuel quality:** **High NGI (nmol)** elevates breakdown risk.
- **Interaction hotspot:** **High RPM × High NGI** amplifies failures → ideal for alerting.
- **Certain piston materials** sustain higher median BMEP → **longer service life**.

> **Business impact:** Targeted predictive maintenance, RPM caps, and fuel‑quality controls can reduce unplanned downtime and warranty costs of engines.

---


## ⚙️ Environment & Setup

```bash
# 1) Clone
git clone https://github.com/Rash1105/Engine_Reliability_Repo.git
cd Engine_Reliability_Repo

# 2) Create environment
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate

# 3) Install deps
pip install -r requirements.txt
```

**`requirements.txt` (minimal)**
```
pandas
numpy
scikit-learn
matplotlib
PowerBI
```

---

## 🔎 Reproduce the Analysis

Open the notebook:
```
jupyter notebook notebooks/code.ipynb
```

Notebook outline:
1. **Data Understanding** — schema, data dictionary, nulls, outliers  
2. **Cleaning** — drop `op_set_2` (all null), remove duplicates, cap extreme `oph` outliers  
3. **EDA** — target distribution; correlations; segment analysis by issue type, RPM, BMEP, NGI, years in service  
4. **Risk Drivers** — feature importance (Random Forest), logistic coefficients (explainable)  
5. **Interactions** — **RPM × NGI** risk matrix; BMEP trend by year band  
6. **Modeling** — logistic regression baseline + Random Forest / XGBoost (cross‑validated); evaluate ROC‑AUC, Precision/Recall (optimize Recall)  
7. **Business Actions** — thresholds & alerts; triage list; projected KPI improvements

> Images for the README/slides can be exported to `reports/figures/`.

---

## 🧾 Data Dictionary (business‑focused)
- **oph** — Operating hours (usage & wear; plan service timing)  
- **pist_m** — Piston material (durability & cost trade‑off)  
- **issue_type** — Combustion issue class (typical/atypical/non‑related/non‑symptomatic)  
- **bmep** — Brake mean effective pressure (efficiency proxy)  
- **ng_imp** — Natural‑gas impurities (nmol) — fuel quality; corrosion/clogging risk  
- **past_dmg** — Past damage (recurrence risk indicator)  
- **resting_analysis_results** — Post‑operation health (0=normal,1=abnormal,2=critical)  
- **rpm_max** — Maximum RPM (mechanical stress)  
- **full_load_issues** — Issues at full load (stress‑test capture)  
- **number_up** — Unplanned events (reliability signal)  
- **number_tc** — Turbocharger count (complexity vs performance)  
- **op_set_1/3** — Operational settings (constant in sample → dropped)  
- **high_breakdown_risk** — Target label (0/1)

---

## 📊 Power BI — KPI Page (measures)

```DAX
Breakdown Rate % :=
DIVIDE( COUNTROWS(FILTER(Engine, Engine[high_breakdown_risk]=1)), COUNTROWS(Engine) )

Avg Max RPM := AVERAGE(Engine[rpm_max])

High Risk Units := COUNTROWS(FILTER(Engine, Engine[high_breakdown_risk]=1))

Full‑Load Issue Rate % :=
DIVIDE( COUNTROWS(FILTER(Engine, Engine[full_load_issues]=1)), COUNTROWS(Engine) )

-- Fuel quality thresholding
High NGI Threshold := 500
Pct Above Threshold % :=
DIVIDE( COUNTROWS(FILTER(Engine, Engine[ng_imp] > [High NGI Threshold])), COUNTROWS(Engine) )
```

**Recommended visuals**
- KPI cards: Breakdown Rate, Avg Max RPM, High‑Risk Units, Full‑Load Issue Rate  
- Bar: High‑risk rate by **issue type**  
- Line: High‑risk rate by **years in service**  
- Heatmap: **RPM bands × NGI bands** → High‑risk %  
- Combo: **Breakdown rate (bar) + Median BMEP (line)** by **piston material**

---

## 🧪 Modeling Notes
- **Baseline:** Logistic Regression (stakeholder‑friendly, interpretable coefficients)  
- **Production:** Random Forest / XGBoost (captures non‑linearities; tune for **Recall** to catch true risks)  
- **Validation:** Stratified CV; report ROC‑AUC, Precision/Recall, PR‑AUC; confusion matrix at business‑chosen threshold

**Top predictors (in this sample)**
- **Issue type**, **Max RPM**, **Turbochargers**; BMEP (declines), NGI (high), past damage all contribute.  
- Hotspot: **RPM > 1700** & **NGI > 500 nmol**.

---

## 🚀 90‑Day Action Plan
1. Embed **risk scoring** & **alerts** (NGI>500, RPM>1700, BMEP drop>10%) in Power BI.  
2. **Triage 164** high‑risk units by issue type, RPM, NGI, past damage.  
3. Operational: **RPM caps** when impurities elevated; expand **full‑load testing**; operator coaching.  
4. Procurement: pilot/standardize **piston materials** that sustain higher median BMEP.  
5. Data: fix null operational settings; add **parts‑replaced & cost** to quantify ROI.

---

## ▶️ Quick Start (one‑liner)
```bash
jupyter nbconvert --to notebook --execute notebooks/code.ipynb --output reports/code_executed.ipynb
```

---

## 📄 License
See `LICENSE` (choose MIT/Apache‑2.0/Proprietary as appropriate).

## 👤 Author
Created by **Avnish Sharma** — data & business analytics for predictive maintenance.
