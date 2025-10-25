# Marketing-Channel-ROI-Optimization-Budget-Allocation

**Dataset:** `digital_marketing_campaign_dataset.csv` (Kaggle: *Predict Conversion in Digital Marketing*)  
**Notebook:** `marketing_roi_analysis.ipynb`  
**Assumption:** Average Order Value (AOV) = **$100**

> This project cleans and analyzes real user-level marketing data, runs an A/B test with a **PSM** robustness check, computes **Average ROI & CAC** by channel, fits **campaign-level M‑ROI** curves, and converts insights into a **budget reallocation plan** with guardrails.

## Quick Start
GitHub’s notebook preview **cannot render Plotly interactive charts**. To properly view the visualizations:
- Download the notebook → open locally in JupyterLab / VS Code / Colab.  
  ```bash
  jupyter notebook notebooks/marketing_roi_analysis.ipynb
  ```
## Data Access
The dataset is publicly available on Kaggle:
- [Predict Conversion in Digital Marketing Dataset](https://www.kaggle.com/datasets/rabieelkharoua/predict-conversion-in-digital-marketing-dataset)
**Reproduce locally:**
```bash
# Option 1 — direct download from Kaggle
import kagglehub
path = kagglehub.dataset_download("rabieelkharoua/predict-conversion-in-digital-marketing-dataset")
print("Path to dataset files:", path)

# Option 2 — manual download
# Go to the link above and place the CSV file as:
# data/digital_marketing_campaign_dataset.csv
```
> `data/` is intentionally **not** tracked by Git to keep the repo lightweight.

## Project Overview
- **Business questions:** Where should we spend (or cut) across channels to improve profit today, and should we roll out the “Conversion” campaign strategy?
- **Method:**  
  1) Two‑proportion Z‑test on `CampaignType` (**Awareness vs Conversion**), then **Propensity Score Matching (PSM)** on *(Age, Gender, Income, WebsiteVisits)* and re‑test.  
  2) Compute **Average ROI** and **CAC** by `CampaignChannel` using AOV=$100.  
  3) Reconstruct **campaign‑level** observations and fit quadratic revenue curves to obtain **Marginal ROI (M‑ROI)** at the **current average spend** per channel.  
  4) **Local, first‑order** budget rotation: move dollars from low‑MROI to high‑MROI channels under constant total budget and a 5% **learning floor** per channel.


## Key Findings
1) **A/B Test:** “**Conversion**” outperforms “Awareness.”  
   - Raw: Z=**8.117**, p≈0.0; CR(A)=**0.856**, CR(B)=**0.934**.  
   - After **PSM**: Z=**7.129**, p≈0.0; CR(A)=**0.850**, CR(B)=**0.942**. 

2) **Average ROI & CAC:** With AOV=$100 and spend magnitudes in the millions, **all channels are unprofitable on average** (ROI ≈ **−0.982**; CAC ≈ **$5.6k–$5.8k** per conversion). This reflects a deliberate stress‑test setup (very high spend vs modest AOV). 

3) **Marginal ROI (current point):** On incremental dollars, rankings are:  
   **Social Media (~0.00168)** ≥ **SEO (~0.00160)** ≥ **PPC (~0.00144)** ≥ **Email (~0.00131)** ≥ **Referral (~0.00124)**.  
   *(Units: incremental **$ revenue** per incremental **$ spend** at the **current average spend**.)* 


## Business Recommendations
- **Rollout decision:** Roll out **Conversion** strategy with guardrails (keep a holdout, re‑test monthly).   
- **Constant‑budget rotation (10% cap, 5% floor):** Move **$4.0M** from **Referral → Social Media**. **First‑order lift ≈ $1,780.** Keep other channels flat.   
- **If profit‑optimal cuts are allowed:** Since **M‑ROI < 1** for every channel, the *profit‑optimal* action is to **decrease overall spend** toward points where M‑ROI ≈ 1 (or until business constraints bind).  
- **Tracking:** Exported plan and ROI/M‑ROI matrix for sign‑off; monitor weekly ROI/CR post‑change; maintain ≥5–10% **learning floor** per channel. 


## Tech Stack
- **Python:** pandas, numpy, scikit‑learn, statsmodels, plotly
- **Statistical methods:** Two‑proportion Z‑test, **Propensity Score Matching (PSM)**, OLS quadratic fit for campaign‑level revenue, first‑order M‑ROI rotation with guardrails.


## Repo Structure
```
.
├── README.md                               # This file
├── requirements.txt
├── data/
│   └── digital_marketing_campaign_dataset.csv   # (user-provided; see Data Access)
├── notebooks/
│   └── marketing_roi_analysis.ipynb        # Main analysis
└── outputs/
    └── tables/
        ├── budget_plan.csv                 # Final per-channel plan (exported by notebook)
        └── roi_mroi_matrix.csv             # Average ROI & M-ROI table
```
> Paths under `outputs/` are produced by Step **6.4 (Lite)** in the notebook.


## Notes & Next Steps
- **Observational caveat:** Causal claims require controls (we used PSM) and assumptions. 
- **Calibrate AOV** with finance; run an AOV sensitivity check if needed (the current AOV=$100 is conservative vs total spend).  
- **Advanced:** Consider a Marketing Mix Model (MMM) to capture cross‑channel interactions once more time‑series signal is available.

