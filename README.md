
# Healthcare Analytics Platform: Clinical Operations & Financial Risk

A technical data engineering, quality audit, and business intelligence platform built across 27,891 transactional encounters and 24,000+ medical procedures. This platform flattens back-end automated transformations into a compressed, relational Power BI data model to expose operational bottlenecks and system revenue leakages.

### 📌 Core Business Objective

How do encounter classes, payer coverage gaps, and procedure cost distributions drive overall hospital operational expenses and resource utilization, and where are the primary financial leakage points?

### ⚙️ Technology Stack & Analytical Methods

The data engineering pipeline and validation framework were constructed using the following core tools and technologies:
* Python (Pandas): Utilized for structural data preprocessing, datatype manipulation (pd.to_datetime), missing value handling, and empirical statistical comparisons (.head(), .describe(), .groupby(), and .agg(['mean', 'median', 'count'])).
* pandasql & SQLite: Implemented as the primary SQL query engine. Structured aggregation syntax—including SUM(CASE WHEN...), GROUP BY, and HAVING filters—was executed natively on pandas DataFrames using SQLite engine syntax via pysqldf.
* DataFrame Column Filtering & df.query: Employed for vectorised dataset manipulation, isolating multi-year chronological outliers, and filtering private commercial insurance cohorts away from government entities.
* Power BI Desktop (Import Mode): Developed an integrated data model using Many-to-One (*:1) relational links with Single Cross-Filter Direction across Patients, Encounters, Payers, and Procedures tables.

### 📊 Summary of Empirical Findings

* EHR Outlier Correction: Standard averages (Mean) falsely indicated routine clinic visits took 9.5 hours due to an unclosed chart glitch open for 1,872 days (over 5 years). Median modeling exposed the true ground truth: a standardized 15-minute operational baseline across 24,000+ records.
* Commercial Payer Disconnect: Merged payer profiles unmasked an EDI clearinghouse pipeline breakdown. Anthem registers a 100.00% zero-payout rate across 704 claims ($0.00 total covered cost), with Cigna (97.65%), Aetna (95.94%), and Humana (95.76%) showing near-total rejections. Government programs (Medicare/Medicaid) remain stable with zero-payouts under 0.6%.
* Financial Toxicity: 48.71% of the entire dataset (13,586 encounters) registers $0.00 in insurance coverage, forcing $70.42 Million into self-pay bad debt. Because commercial coverage drops to 13.10% for the class, Urgent Care leaves individual patients with the highest average out-of-pocket bill ($5,534.76).
* Resource and Revenue Anchors: Renal Dialysis (2,746 cases) represents the largest physical resource bottleneck due to its rigid 3-hour session constraint. Financially, Electrical Cardioversion serves as the network's core anchor, generating over $35.8 Million across 1,383 cases.

### 📐 Data-Quality Verification Matrix


* Metric Vector / Table	Record Count (N)	Calculated Mean	Calculated Median	Preprocessing Quality Status
* Ambulatory Duration	12,537 rows	568.76 min	15.00 min	🔴 Highly Distorted: Filtered out 1,872-day administrative chart loop.
* Outpatient Duration	6,300 rows	352.55 min	15.00 min	🔴 Highly Distorted: Corrected manual batch-closure timestamps.
* Inpatient Duration	1,135 rows	2,210.50 min	1,440.00 min	🟢 High Integrity: Accurately mirrors standard 24-hour overnight stays.
* Anthem Coverage	704 claims	$0.00	$0.00	🟡 Systemic Gap: Flagged 100% zero-payout anomaly for clearinghouse audit.
* Medicaid Claim Cost	1,443 claims	$6,205.22	$1,005.38	🟡 Right-Skewed: Financial averages pulled up by rare intense procedures.
* Medicare Payouts	11,371 rows	$2,167.55	$234.72	🟢 High Integrity: High-volume consistency confirms solid baseline tracking.


### 🧮 Core Power BI DAX Measures

The interactive visualization layer utilizes explicit, high-performance DAX measures to keep execution light on 8GB RAM virtual environments:
```dax
Total Visits = COUNT(clean_encounters_payers[Id])
```

```dax
Median Duration Minutes = MEDIAN(clean_encounters_payers[DURATION_MINUTES])
```
```dax
Zero Payout Rate % = 
DIVIDE(
    CALCULATE([Total Visits], clean_encounters_payers[PAYER_COVERAGE] = 0),
    [Total Visits],
    0
)
```
```dax
Total Staff Hours Spent = 
SUMX(
    clean_procedures,
    (clean_procedures[total_occurrance] * clean_procedures[avg_duration_minutes]) / 60
)
```
```dax
Total Procedures = COUNTROWS(clean_procedures)
```

### 📋 Actionable Recommendations

* EHR Governance: Enforce 48-hour automated chart timeouts for outpatient records to completely neutralize multi-year statistical data distortions.
* Revenue Cycle: Convene immediate billing contract audits targeting Anthem, Cigna, and Aetna to remap the broken EDI clearinghouse loop freezing active revenue.
* Capacity Planning: Transition the 3-hour Renal Dialysis pipeline into strict, predictive multi-shift tiers to optimize physical chair utilization and nursing staff allocations.
Your finalized, tool-mapped README is complete. Are you ready to save your files inside your Windows VM and finalize your project submission?
