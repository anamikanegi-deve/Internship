# Retail Intelligence
## Sales Analytics and AI-Assisted Customer Segmentation

**Student:** Anamika Negi  
**Program:** AICTE | IBM SkillsBuild Data Analytics with AI Internship 2026 | BharatCares

## Project description
A single-file Python/Streamlit business-intelligence application that turns retail
invoice lines into sales KPIs, complete-month trends, product/country analysis,
four K-means customer segments, and explicit fact → insight → action recommendations.
This is an AI-assisted educational project; it is not an official IBM product.

## Problem statement
How can a retailer understand historical product demand, geographic dependence,
and customer purchasing patterns to prioritize inventory and retention experiments?
The goal is actionable insight rather than a large number of decorative charts.

## Dataset — source link required for submission
- **Dataset:** UCI Online Retail, contributed by Daqing Chen.
- **Dataset link:** https://archive.ics.uci.edu/dataset/352/online+retail
- **Direct archive:** https://archive.ics.uci.edu/static/public/352/online+retail.zip
- **Dataset DOI:** https://doi.org/10.24432/C5BW33
- **Source workbook:** Online Retail.xlsx; 541,909 invoice-line rows.
- **Source coverage:** 1 December 2010–9 December 2011; UK-based online gift retailer.
- **Analysis coverage:** 1 December 2010–30 November 2011, inclusive.
  Incomplete December 2011 is deliberately excluded.
- Unit prices are in sterling (GBP). No customer names, contact details, or passwords are used.

**Eligibility check before submission:** the internship transcript says not to use
the exact dataset used in the masterclasses. Confirm that UCI Online Retail was NOT
your masterclass dataset. This cannot be established from the supplied transcript alone.

## Four submission files
1. `AnamikaNegi_RetailIntelligence.py` — complete backend, segmentation and frontend.
2. `requirements.txt` — exact tested Python dependency versions.
3. `AnamikaNegi_ProjectReport.docx` — documentation, computed results and actual UI screenshots.
4. `README.md` — this file.

Submit each file separately. The transcript explicitly says no ZIP submission.
Upload the same four files to a GitHub repository and provide that repository's URL
in the form. A Google Drive folder URL is not a substitute for the GitHub field.

## Technologies
- Python 3.13 (tested version; recommended for these exact dependency pins)
- pandas / NumPy: cleaning, aggregation, RFM analysis and numeric reconciliation
- openpyxl: reading the source Excel workbook
- scikit-learn: StandardScaler, KMeans and silhouette score
- Streamlit: UI, navigation, searchable product lookup and CSV download
- Chart.js: embedded, validated chart snapshots; loaded from a public CDN
- Python standard library: download, ZIP extraction, paths, JSON, hashing and
  compressed chart embedding; these do not need separate pip installation

## Setup and run
Save the Python file and requirements file in one folder. Install Python 3.13.
Open a terminal in that folder:

```bash
python -m venv .venv
```

Activate on Windows:
```powershell
.venv\Scripts\Activate.ps1
```
Or on macOS/Linux:
```bash
source .venv/bin/activate
```

Install and launch:
```bash
python -m pip install -r requirements.txt
python -m streamlit run AnamikaNegi_RetailIntelligence.py
```
Open the local URL printed in the terminal, usually http://localhost:8501.
The first run downloads the public dataset (~23 MB) into `.cache/` beside the code.
Loading the workbook and calculating segments can take 1–3 minutes.
No API key, IBM Bob account, or paid service is needed to run this project.

### Analysis without the web UI
```bash
python AnamikaNegi_RetailIntelligence.py --analyze
```
This writes `outputs/metrics.json`, `validation.json`, and CSV files for monthly
results, countries, products, RFM, segments, cleaning and recommended actions.
Use `--output my_results` to choose another output folder.

### Offline/local-data fallback
Download and unzip the UCI archive yourself. Save the workbook as
`.cache/Online Retail.xlsx` beside the code. The app will use it without downloading.
For command-line analysis, a workbook elsewhere can be supplied:
```bash
python AnamikaNegi_RetailIntelligence.py --analyze --data "Online Retail.xlsx"
```
Internet is still needed for the chart CDN. If the CDN cannot load, use the
computed tables/CSV outputs; the chart provides a visible fallback message.

## Methodology
1. Remove exact duplicates; parse invoice dates and numerical fields.
2. Restrict to the documented twelve complete months.
3. Exclude cancellations/returns, non-positive quantities and non-positive prices.
4. Keep product codes matching five digits plus an optional letter. This excludes
   postage/manual/non-product adjustments. It is an explicit project assumption.
5. Compute product sales = Quantity × UnitPrice. Keep anonymous sales in sales KPIs.
6. For identified customers only, compute RFM:
   recency = days since last purchase at 1 December 2011; frequency = unique invoices;
   monetary = historical eligible product sales.
7. Apply log1p and StandardScaler to RFM, then KMeans(k=4, random_state=42, n_init=20).
8. Order display segment names by descending median monetary value.
9. Flag retention-review candidates using recency >90 days AND monetary at/above
   the 75th percentile. This is not a trained churn model.
10. Independently reconcile revenue, customer counts and sequential row accounting.

## Verified results
| Metric | Result |
|---|---:|
| Eligible product-sales rows | 497,379 |
| Product sales (GBP) | £9,615,927.75 |
| Orders | 18,955 |
| Identified customers | 4,293 |
| Average order value | £507.30 |
| Identified-customer share of product sales | 85.4% |
| UK share of product sales | 84.6% |
| Peak sales month | November 2011 |
| Retention-review candidates | 106 |
| Sample silhouette score | 0.340 |

Segment 1 contains 16.8% of identified customers
and contributes 64.5% of their historical product sales.
Recommendations include inventory planning, careful geographic diversification,
and controlled retention experiments; no uplift has been demonstrated.

## Dashboard views
- **Executive overview:** primary KPIs, monthly chart, facts and actions.
- **Sales & products:** country concentration, all monthly results, searchable
  product table, full country lookup and product CSV download.
- **Customers & retention:** mix comparison, segment profiles, model diagnostics
  and retention-review customer table.
- **Data quality & methodology:** sequential cleaning audit and transparent definitions.

## Validation and chart reproducibility
The code asserts that product, country and monthly sales all equal the overall
product-sales KPI; segment sales equal identified-customer sales; customer counts
and row-removal counts reconcile. Revenue is also checked independently using
the dot product of retained quantities and prices.
All four UI views were run and captured; product-search interaction was exercised.

The three compact HTML charts are verified snapshots of this fixed public source,
embedded in the Python file as compressed text so no separate frontend files are
needed. The application ALWAYS recomputes tables/KPIs. A fingerprint gates the
snapshots: if the source/results differ, the app hides snapshots instead of
showing stale charts, and freshly computed tables remain available.
They are not an arbitrary-dataset chart editor or a live-data sync.

## Limitations and responsible use
- Historical gift-retailer data do not establish current demand.
- Product sales exclude returns/fees and are NOT net revenue or profit.
- Missing CustomerID rows are not silently assigned to invented customers.
- Wholesalers and unusually large valid orders can affect customer clusters.
- Four clusters are a practical exploratory choice, not a proven optimal count.
- Silhouette measures separation, not predictive accuracy or business success.
- The recency rule is a heuristic; actual churn labels are unavailable.
- No train/test accuracy, causal driver, forecast or campaign uplift is claimed.
- Customer identifiers should be access-controlled in any real deployment.
- AI assisted code and documentation; numeric findings were executed and checked.
  Review and understand the work before submitting it under your name.

## Next improvements
Add inventory/cost data, verify product-code exceptions with a domain expert,
test stability across reporting periods, compare several cluster counts, and
evaluate campaigns with consent and randomized holdout groups.
