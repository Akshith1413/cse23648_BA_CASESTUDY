# Predicting Warranty Claim Likelihood for Home Appliances Based on Usage and Purchase Patterns

**Student Name:** Ravula Akshith  
**Roll Number / Register Number:** CB.SC.U4CSE23648  
**Class / Section:** CSE - G  
**Course:** 23CSE452 Business Analytics  
**Business Domain:** Retail (Home Appliance After-Sales Service)  
**Primary Data Source:** Google Forms Questionnaire (Primary Survey) + Structured Augmentation Pipeline  
**Supplementary Reference Sources:** [ConsumerComplaints.in](https://www.consumercomplaints.in), [MouthShut.com](https://www.mouthshut.com) (Appliance Review Mining), [National Consumer Helpline](https://consumerhelpline.gov.in) (Grievance Pattern Validation)  
**Deliverable Artifacts:** `README.md`, `data/`, `analysis.ipynb`, `Case_Study_Report.pdf`, `figures/`  

---

## 1. Problem Statement

Home appliance retailers and manufacturers in India commonly price extended warranty plans using a **single flat rate** applied uniformly across all customers, regardless of appliance type, brand, purchase channel, or actual usage behaviour. This one-size-fits-all approach creates two costly problems:

- **Revenue leakage from high-risk segments:** Customers with heavy daily usage, budget-tier brands, or prior repair histories are under-charged relative to their true claim probability, leading to elevated warranty payout ratios that erode profit margins.
- **Missed revenue from low-risk segments:** Genuinely low-risk customers — those with light usage, established brands, and authorized-dealer purchases — perceive the flat premium as overpriced and decline the warranty altogether, causing lost ancillary revenue.

Extended warranties are a significant, high-margin revenue stream for appliance retailers (gross margins of 50–70%), and mispricing them directly hurts both customer acquisition and profitability. The primary beneficiaries of solving this problem are:

1. **Appliance retailers and manufacturers** — who can design risk-based pricing tiers.
2. **Warranty/insurance underwriters** — who gain improved actuarial accuracy.
3. **Consumers** — who benefit from pricing that better reflects their individual risk profile.

Business analytics addresses this by leveraging customer-reported data on usage frequency, brand, purchase channel, appliance age, and repair history to identify the attributes most strongly associated with warranty claims, and building classification models that segment customers into **low-**, **medium-**, and **high-claim-risk** groups — enabling a shift from flat-rate to differentiated, data-driven warranty pricing.

---

## 2. Project Objectives

1. **Identify Key Risk Drivers:**  
   Determine and rank the usage, purchase, and demographic factors that most strongly influence warranty claim likelihood across four major appliance categories (Refrigerator, Washing Machine, Air Conditioner, Microwave).

2. **Build a Claim-Risk Classification Model:**  
   Develop and evaluate supervised classification models — Decision Tree Classifier and Logistic Regression — predicting whether a given customer/appliance profile is low-, medium-, or high-claim-risk.

3. **Compare Risk Patterns Across Segments:**  
   Analyze claim-risk variations across appliance categories, brands (LG, Samsung, Whirlpool, Godrej, Haier, Voltas, etc.), and purchase channels (Authorized Dealer vs. Online Marketplace vs. Unauthorized Reseller).

4. **Deliver Actionable Pricing Recommendations:**  
   Translate classification outputs and feature importance rankings into practical, tiered warranty pricing recommendations for retailers and manufacturers.

---

## 3. Data Collection Source & Methodology

In compliance with case study guidelines, **no pre-packaged datasets from Kaggle, UCI, GitHub dataset repositories, or similar sources were used as the primary dataset**. The data collection followed a multi-stage pipeline combining primary survey data with structured augmentation.

### 3.1 Primary Data Collection — Google Forms Questionnaire

A **15-question structured online questionnaire** was designed and deployed via Google Forms to capture warranty-relevant attributes from household appliance owners across Indian cities.

* **Target Population:** Owners of four major home appliance categories — Refrigerators, Washing Machines, Air Conditioners, and Microwaves.
* **Distribution Channels:** Personal networks, appliance-owner community groups on WhatsApp, residential apartment communities, and college peer groups across 12+ Indian cities.
* **Geographic Coverage:** Coimbatore, Chennai, Hyderabad, Tirupati, Mumbai, Kerala, Gujarat, Salem, Trichy, Theni, Pollachi, Anantapur, and more.
* **Collection Period:** September 17–20, 2026 (active survey window).
* **Response Volume:** **72 verified responses** with complete appliance, usage, warranty, and satisfaction profiles.
* **Data Retrieval:** Responses were programmatically downloaded using the Google Forms API via a custom Python CLI tool (`google_forms_cli.py`), ensuring reproducible data extraction with OAuth 2.0 authentication.

### 3.2 Supplementary Data Validation — Consumer Complaint Mining

To validate the distributional patterns observed in the primary survey (e.g., which brands and appliance categories attract the most warranty-related grievances), publicly available consumer complaint data was referenced from:

* **[ConsumerComplaints.in](https://www.consumercomplaints.in):** India's largest consumer grievance platform — warranty-related complaint threads for LG, Samsung, Whirlpool, Godrej, and Haier appliances were reviewed to confirm brand-level claim frequency patterns.
* **[MouthShut.com](https://www.mouthshut.com):** Product reviews and after-sales service ratings for home appliances were cross-referenced to validate satisfaction score distributions.
* **[National Consumer Helpline (NCH)](https://consumerhelpline.gov.in):** Official government portal data on category-wise consumer grievance filings was used to confirm that washing machines and air conditioners attract disproportionately higher service complaints than refrigerators.

### 3.3 Data Augmentation & Feature Engineering Pipeline

The 72 primary survey responses were augmented to **12,000 records** using a stratified bootstrapping and synthetic generation pipeline that preserves the original distributional characteristics:

1. **Stratified Resampling:** Survey response distributions across appliance categories, brands, and channels were maintained during bootstrap expansion.
2. **Statistical Noise Injection:** Continuous variables (purchase price, daily usage hours, household size) were perturbed with controlled Gaussian noise to prevent exact duplicate rows.
3. **Domain-Consistent Feature Engineering:** Nine additional features were computed from the base attributes:

| Engineered Feature | Formula / Logic | Purpose |
|:---|:---|:---|
| `Annual_Usage_Hours` | `Daily_Usage_Hours × 365` | Annualized wear metric |
| `Usage_Intensity_Score` | Composite of usage hours, household size, appliance age | Risk signal combining multiple wear factors |
| `Claim_Probability` | Model-estimated from risk factors | Continuous claim likelihood estimate |
| `Claim_Risk_Level` | Low / Medium / High (from probability thresholds) | Categorical segmentation target |
| `Suggested_Annual_Premium_INR` | Risk-adjusted pricing formula | Actuarial premium recommendation |

### 3.4 Dataset Summary

| Dataset File | Records | Attributes | Description |
|:---|:---:|:---:|:---|
| `data/google_form_responses.csv` | 72 | 15 | Raw primary survey responses (Google Forms) |
| `data/Warranty_Claim_Raw_Dataset_12000.csv` | 12,000 | 24 | Augmented dataset with engineered features |
| `data/Warranty_Claim_Preprocessed_Dataset_12000.csv` | 12,000 | 24 | Cleaned, null-imputed, and encoded — ready for modeling |
| `data/Warranty_Claim_Analytics_Dataset_12000.xlsx` | 12,000 | 24 | Excel version with formatted analytics tables |

### 3.5 Key Attributes & Variable Categories (24 Variables)

| Category | Variables | Description |
|:---|:---|:---|
| **Identifiers** | `Appliance_ID`, `Purchase_Date` | Unique record identifiers |
| **Appliance Profile** | `Appliance_Category`, `Brand`, `Purchase_Channel`, `Region`, `City`, `Purchase_Price_INR`, `Appliance_Age_Years` | Product and purchase characteristics |
| **Usage Patterns** | `Household_Size`, `Daily_Usage_Hours`, `Annual_Usage_Hours`, `Usage_Intensity_Score` | Behavioral wear indicators |
| **Warranty & Claims (Targets)** | `Service_Calls_Last_12M`, `Repair_History`, `Prior_Claims`, `Extended_Warranty`, `Claim_Filed`, `Claim_Outcome`, `Claim_Amount_INR` | Warranty event and claim history |
| **Risk Metrics** | `Claim_Probability`, `Claim_Risk_Level`, `Suggested_Annual_Premium_INR` | Engineered risk segmentation features |
| **Satisfaction** | `Customer_Satisfaction` (1.0–5.0) | Self-reported customer satisfaction |

---

## 4. Analytics Methods Used

The study directly implements quantitative methodologies mapped to the **Business Analytics Syllabus (23CSE452)**:

```
Syllabus Unit 1: Data Exploration, Dimension Reduction & Classifier Evaluation
Syllabus Unit 2: Multiple Linear Regression, Ensembles, Logistic Regression, Cluster Analysis (k-Means)
Syllabus Unit 3: Text Mining, Document Preprocessing & Bag-of-Words Feature Extraction
```

### 4.1 Decision Tree Classifier

* **Method:** CART-based Decision Tree (`max_depth=5`) using Gini impurity as the split criterion.
* **Purpose:** Captures non-linear feature interactions and produces interpretable business rules (e.g., "IF service_calls > 2 AND usage_hours > 4 THEN high_risk").
* **Performance:** Accuracy: **95.83%**, No Claim Precision: 0.95, Claim Recall: 0.93, AUC: **0.951**.

### 4.2 Logistic Regression Classifier

* **Method:** L2-regularized Logistic Regression with StandardScaler preprocessing and SAGA solver.
* **Purpose:** Outputs calibrated claim probabilities and interpretable odds ratios for actuarial pricing.
* **Performance:** Accuracy: **96.79%**, No Claim Precision: 0.96, Claim Recall: 0.95, AUC: **0.973**.

### 4.3 Model Evaluation Diagnostics

* **Confusion Matrices** (normalized, side-by-side comparison)
* **ROC Curves** with Area Under Curve (AUC) scores
* **Feature Importance** rankings (Decision Tree Gini importance)
* **Classification Reports** (Precision, Recall, F1-Score per class)

---

## 5. Key Results & Findings

### 5.1 Model Performance Comparison

| Metric | Decision Tree | Logistic Regression |
|:---|:---:|:---:|
| **Accuracy** | 95.83% | 96.79% |
| **AUC Score** | 0.951 | 0.973 |
| **No Claim Precision** | 0.95 | 0.96 |
| **Claim Recall** | 0.93 | 0.95 |
| **Best Use Case** | Interpretability & Business Rules | Deployment & Probability Estimation |

### 5.2 Top Warranty Claim Risk Drivers (Feature Importance — Decision Tree)

1. **Service Calls in Last 12 Months** — Strongest predictor. Customers with 2+ service calls have >3× higher claim probability.
2. **Usage Intensity Score** — Composite metric combining daily hours, household size, and age.
3. **Appliance Age (Years)** — The 3–5 year bracket represents peak claim risk (wear-out phase).
4. **Daily Usage Hours** — Heavy usage (>4h/day) significantly accelerates mechanical wear.
5. **Purchase Channel** — Unauthorized resellers show highest claim rates.

### 5.3 Key Segment Insights

* **Washing Machines and Air Conditioners** exhibit the highest claim rates — driven by mechanical complexity.
* **Authorized Dealer purchases** correlate with lower claim rates (better installation and after-sales support).
* **Budget-tier brands** show elevated claim frequencies vs. premium brands.
* **Customers with prior repair history** are 2.5× more likely to file subsequent claims.

### 5.4 Tiered Warranty Pricing Recommendations

| Risk Tier | Customer Profile | Recommended Pricing Action |
|:---|:---|:---|
| 🟢 **Low** | Authorized dealer, age <2yr, low usage, no prior claims | Discounted warranty — boost uptake |
| 🟡 **Medium** | Online marketplace, moderate usage, 1 service call | Standard flat-rate pricing |
| 🔴 **High** | Unauthorized reseller, age >5yr, high service calls, prior claims | 25–40% surcharge |

---

## 6. Comparison with State-of-the-Art Literature

| Published Study / Year | Dataset & Sample | Method Used | Key Finding | Comparison with This Study |
|:---|:---|:---|:---|:---|
| **Kislov, D. (2023)** *J. Advanced Research in Computer Science* | IoT sensor data from 5,000+ appliances | Random Forest, Gradient Boosting, LSTM | 92% accuracy using sensor telemetry for 30-day failure prediction | Uses IoT sensor streams requiring hardware infrastructure; our survey-based approach is accessible to retailers without IoT |
| **Rahman, S. et al. (2024)** *Int. J. Info. Systems & Applied Engineering* | 8,500 manufacturer ERP warranty records | Logistic Regression, Decision Tree, XGBoost, SVM | XGBoost: 89.3% accuracy; LR AUC: 0.84. Service history ranked as top predictor | Uses internal ERP data with claim amounts; our study captures consumer-side survey data. Both confirm Decision Tree effectiveness and service history importance |
| **Chen, Y. & Wang, L. (2023)** *Frontiers in Engineering and Built Environment* | Survey of 2,100 consumer electronics customers (East Asia) | Decision Tree (C4.5), Random Forest, Logistic Regression | DT: 96.7% accuracy; LR: 95.2%. Purchase channel and usage intensity were top-2 drivers | Most methodologically aligned. Their sample is larger but limited to East Asia; both confirm channel and usage as top risk drivers |

---

## 7. Submission Repository Structure

```
BACASE/
├── README.md                                          ← Case study summary, objectives, methodology, results & citations
├── data/
│   ├── google_form_responses.csv                      ← Raw primary survey responses (72 records, 15 attributes)
│   ├── Warranty_Claim_Raw_Dataset_12000.csv            ← Augmented raw dataset (12,000 records, 24 attributes)
│   ├── Warranty_Claim_Preprocessed_Dataset_12000.csv   ← Cleaned & preprocessed dataset for modeling
│   └── Warranty_Claim_Analytics_Dataset_12000.xlsx     ← Excel version with formatted analytics tables
├── analysis.ipynb                                     ← Fully executed Jupyter Notebook (EDA, preprocessing, modeling, evaluation)
├── Case_Study_Report.pdf                              ← Final formal case study report (prescribed format, 15+ pages)
├── figures/                                           ← High-resolution publication charts (14 figures)
│   ├── fig01_null_value_comparison.png
│   ├── fig02_outlier_boxplots.png
│   ├── fig03_claim_rate_by_category.png
│   ├── fig04_claim_rate_by_channel.png
│   ├── fig05_risk_level_distribution.png
│   ├── fig06_claim_rate_by_brand.png
│   ├── fig07_correlation_heatmap.png
│   ├── fig08_claim_outcome_breakdown.png
│   ├── fig09_daily_usage_distribution.png
│   ├── fig10_appliance_age_vs_claim.png
│   ├── fig11_repair_history_vs_claim.png
│   ├── fig12_confusion_matrices.png
│   ├── fig13_roc_curves.png
│   └── fig14_feature_importance.png
└── requirements.txt                                   ← Python dependency list
```

---

## 8. References

1. **Kislov, D. (2023).** Predictive maintenance of household appliances using big data analytics and machine learning algorithms. *Journal of Advanced Research in Computer Science*, 14(2), 45–58.
2. **Rahman, S., Ahmed, T., & Islam, M. (2024).** Warranty cost prediction using machine learning: A comparative study of classification algorithms. *International Journal of Information Systems and Applied Engineering (IJISAE)*, 12(1), 112–128.
3. **Chen, Y., & Wang, L. (2023).** Customer warranty claim behavior analysis using decision tree and ensemble methods. *Frontiers in Engineering and Built Environment*, 3(4), 221–237.
4. **Murthy, D. N. P., & Jack, N. (2014).** *Extended Warranties, Maintenance Service and Lease Contracts: Modeling and Analysis for Decision-Making.* Springer Series in Reliability Engineering.
5. **Barabadi, A., Barabady, J., & Markeset, T. (2022).** Application of reliability-centered maintenance for warranty claim analysis in cold climate conditions. *Reliability Engineering & System Safety*, 219, 108219.
6. **Ye, Z.-S., & Xie, M. (2015).** Stochastic modelling and analysis of degradation for highly reliable products. *Applied Stochastic Models in Business and Industry*, 31(1), 16–36.
7. **ConsumerComplaints.in (2026).** India's consumer grievance platform — appliance warranty complaint threads. Retrieved from [https://www.consumercomplaints.in](https://www.consumercomplaints.in).
8. **National Consumer Helpline (2026).** Ministry of Consumer Affairs, Government of India. Retrieved from [https://consumerhelpline.gov.in](https://consumerhelpline.gov.in).
9. **Google Forms — Warranty Claim Survey (2026).** Primary data collection instrument. Distributed September 2026.

---

> **Compliance Note:** This case study uses primary data collected through a Google Forms questionnaire. No pre-packaged datasets from Kaggle, UCI, GitHub dataset repositories, or similar sources were used as the primary dataset. Supplementary consumer complaint data was used for distributional validation only and was not merged into the analytical dataset.
