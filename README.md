# Engine Reliability — Data Cleaning (Python) & KPI Dashboard (Power BI)

> Clean the engine dataset in Python, export a **single source of truth CSV**, and build executive KPIs in **Power BI Desktop**.

---

## 🚀 What’s in this repo

- **Python (Jupyter notebook):** data cleaning & basic profiling only  
- **Power BI Desktop:** KPI pages and visuals (built separately from the cleaned CSV)

No ML modeling in this project; focus is **data quality → trustworthy KPIs**.

---

## 📁 Folder structure

```
Engine_Reliability_Repo/
├─ data/
│  ├─ raw/
│  │  └─ assessment - Data Analytics - INNIO.csv     # original file
│  └─ clean/
│     └─ engine_clean.csv                            # output from notebook
├─ notebooks/
│  └─ code.ipynb                                     # data cleaning & profiling
├─ powerbi/
│  └─ Engine_Reliability.pbix                        # Power BI file (optional in repo)
├─ requirements.txt
└─ README.md
```

---

## 🔧 Setup

```bash
# 1) Clone
git clone https://github.com/Rash1105/Engine_Reliability_Repo.git
cd Engine_Reliability_Repo

# 2) Install minimal deps
pip install -r requirements.txt
```

**requirements.txt**
```
pandas
numpy
jupyter
```

---

## 📓 Notebook: what it does (cleaning pipeline)

Open:
```bash
jupyter notebook notebooks/code.ipynb
```

The notebook performs:

1. **Load & schema check**  
2. **Duplicate removal**  
3. **Missing values**  
   - Drop fully-null columns (e.g., `op_set_2`)  
   - Median impute numeric gaps  
4. **Outliers**  
   - Cap extreme `oph` (Operating Hours) at the 99th percentile  
5. **Sanity checks**  
   - Target distribution (`high_breakdown_risk`)  
   - Quick summaries of key fields (RPM, BMEP, NGI, etc.)  
6. **Export cleaned data**  
   - Writes `data/clean/engine_clean.csv` for Power BI

> **Target/label:** `high_breakdown_risk` (0/1)

---

## 📊 Power BI 

Use **Power BI Desktop** to connect to `data/clean/engine_clean.csv`.


**Page 1 – Engine Reliability Overview**
- KPI cards:  
  - **Breakdown Rate %**  
  - **Average Max RPM**  
  - **High-Risk Units (count)**  
  - **Full-Load Issue Rate %**
- Bar: **High-risk rate by Issue Type**  
- Line: **High-risk rate by Years in Service**  
- Matrix: NGI (nmol), Past Damage, Unplanned Events, RPM Max by year band

**Page 2 – Component & Operational Stress**
- KPI cards:  
  - **No. of Unplanned Events**  
  - **Avg NGI (nmol)**  
  - **Installed Turbo Chargers**  
  - **Past Damages occurred**
- Combo: **Median BMEP (bar)** + **Breakdown Rate (line)** by **Years in Service**  
- Decomposition Tree (**Analyze:** Breakdown Risk Count)  
  **Explain by:** Issue Type → Years in Service → Resting Analysis → Piston Material → Past Damages → Installed Turbo Chargers


## 🔄 Refresh workflow

1. Drop a new raw CSV into `data/raw/`.  
2. Re-run `notebooks/code.ipynb` → produces `data/clean/engine_clean.csv`.  
3. Open Power BI Desktop → **Refresh** to update all KPIs/visuals.  

---

## 📚 Short data dictionary (business-focused)

- **oph** — Operating hours (usage & wear)  
- **issue_type** — Combustion issue type (typical/atypical/non-related/non-symptomatic)  
- **bmep** — Brake mean effective pressure (efficiency proxy)  
- **ng_imp** — Natural-gas impurities (nmol; fuel quality)  
- **rpm_max** — Maximum RPM (mechanical stress)  
- **past_dmg** — Past damage flag  
- **full_load_issues** — Issues under full-load test  
- **resting_analysis_results** — Post-run health (0=normal,1=abnormal,2=critical)  
- **high_breakdown_risk** — Target label (0/1)

---

## 👤 Author

Maintained by **Rashmi** — Data cleaning in Python & KPI storytelling in Power BI.
