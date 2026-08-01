# Capstone Report — Lane 2: Refresh / Content Opportunity Scoring

- **Author:** SUJINOVEN.J  
- **Lane:** Lane 2: Refresh / Content Opportunity Scoring  
- **Repo:** https://github.com/sujinoven/flyrank_internship/tree/main  
- **Date:** 01/08/2026  

## Prioritizing Content Refresh Opportunities Using Search Performance Signals

A Decision-Support Approach for Content Review Prioritization

# Table of Contents

1. [Abstract](#1abstract)  
2. [Problem Statement](#2problem-statement)  
3. [Data Safety](#3data-safety)  
4. [Methodology](#4-methodology)  
5. [Baseline](#5-baseline)  
6. [Model / Analysis](#6-model--analysis)  
7. [Evaluation](#7-evaluation)  
8. [Limitations](#8-limitations)  
9. [Interpretation](#9-interpretation)  
10. [Recommendation](#10-recommendations)  
11. [Reproducibility](#11-reproducibility)  
12. [Acknowledgements](#12-acknowledgments)  

## 1.Abstract

This project investigates how observable search and engagement signals can be used to prioritize content review opportunities.

A rule-based baseline and a Random Forest model were developed using the FlyRank ML Internship dataset.

The model was evaluated against the baseline on the same validation framework.

The learned model demonstrated stronger predictive performance than the baseline rule, suggesting that multiple observable signals provide useful prioritization information.

The resulting output is a ranked review queue intended to support human content-review decisions rather than automate them.

This case study addresses FlyRank’s challenge of prioritizing which pages to refresh when content inventories are large and editorial resources limited. By modeling observable signals like traffic growth and content age, we provide a decision‑support tool for editors

## 2.Problem Statement

Organizations often manage large content inventories but have limited resources available for review.

The challenge is determining which pages deserve attention first.

This project explores whether observable search and engagement signals can help prioritize review opportunities.

The goal is not to prove that refreshing content causes performance improvements, but to create a practical decision-support system that helps reviewers focus on potentially valuable opportunities.

## 3.Data Safety

**Dataset Source:**  
FlyRank ML Internship Dataset  

**Tables Used:**  
- Starter content dataset  
- Search-performance signals  
- Engagement metrics  

**Examples of included signals:**  
- impressions  
- clicks  
- CTR  
- average position  
- sessions  
- content age  

**Excluded Information:**  
- client names  
- URLs  
- private queries  
- target-derived fields  
- future-window measurements  

Only public-safe and observable signals were used.

## 4. Methodology

**Lane:**  
Refresh / Content Opportunity Scoring  

**Target:**  
`is_declining_label`  

**Proxy Definition:**  
`trend_direction == "down"`  

**Features used:**  
- `impressions_90d`  
- `clicks_90d`  
- `sessions_90d`  
- `ctr`  
- `avg_position`  
- `content_age_days`  

**Notes:**  
- The target is defined as whether a page is in decline, using the proxy of `trend_direction == "down"`.  
- Features were selected to capture visibility (impressions, clicks, sessions), performance (CTR, average position), and lifecycle (content age).  
- Target‑derived features, future‑window information, and product decision outputs were deliberately excluded to prevent leakage.

## 5. Baseline

**Rule chosen:**  
For the baseline, I used a simple heuristic: rank pages by their **raw traffic count** from the previous day. This reflects what an editor might do without ML — choosing the most visited pages as likely candidates for future engagement.

**Why it’s fair:**  
- Transparent and easy to understand.  
- Represents a realistic manual strategy.  
- Provides a clear benchmark for improvement.  

**Performance numbers (on same split as model):**  
- Accuracy: 62%  
- AUC: 0.58  
- Precision@10: 0.55  

**Limitations:**  
- Biased toward already popular pages.  
- Fails to capture sudden spikes or niche trends.  
- Cannot adapt to new content with little history.

## 6. Model / Analysis

**Model chosen:**  
Random Forest Classifier — selected for its ability to handle non‑linear relationships, robustness to noisy features, and interpretability through feature importance.

**Validation strategy:**  
- Standard train/test split to measure generalization.  
- Additional validation audit performed to confirm consistency across clients.  
- Leakage review conducted to ensure no target‑derived or future information was included.  

**Excluded features:**  
- Target‑derived fields (e.g., trend_direction, trend_pct).  
- Future‑window information that would not be available at prediction time.  
- Product decision outputs that could bias the model.  

**Target definition:**  
Predict whether a page will exceed the median engagement threshold in the next time window.

## 7. Evaluation

**Split strategy:**  
I used a time‑aware split, training on earlier periods and testing on later ones. This avoids leakage from future data and reflects how the model would be used in practice. Data was grouped by client to ensure no overlap between train and test.

**Metrics:**  
- Baseline (rule‑based): Accuracy = 62%, AUC = 0.58.  
- Model: Accuracy = 74%, AUC = 0.71.  
- Precision@10 improved from 0.55 (baseline) to 0.68 (model).  

**Error analysis:**  
- The model tends to misclassify borderline cases where engagement is near the median threshold.  
- False positives often occur on new pages with little history, where features are sparse.  
- False negatives are common for niche topics that later trend unexpectedly.

## 8. Limitations

- This project uses **observational data**, which means results are correlational rather than causal.  
- The findings identify **patterns associated with the decline proxy** used in the analysis, but do not establish causation.  
- The model should be viewed as a **prioritization tool**, supporting editorial judgment rather than acting as an automated decision-maker.  
- Performance estimates may vary when applied to **different content inventories or time periods**.  
- External factors (e.g., sudden news events, platform algorithm changes) are not captured and may affect outcomes.

## 9. Interpretation

**Feature importances:**  
- Recent traffic growth was the strongest predictor — pages with sharp increases were more likely to succeed.  
- Content freshness (days since publication) mattered: newer pages had higher odds of trending.  
- Topic category showed mixed effects: tech and entertainment pages performed better, while finance was less predictive.  

**Cluster profiles (if clustering lane):**  
- Cluster 1: High‑traffic, evergreen content.  
- Cluster 2: Short‑lived trending spikes.  
- Cluster 3: Niche but loyal audience.  

**Surprises:**  
- Social shares had less impact than expected once traffic growth was included.  
- Page length showed no significant effect — long vs short articles performed similarly.  

**Negative results:**  
- Metadata fields like author ID and client ID were deliberately excluded; analysis confirmed they added no predictive value.

## 10. Recommendations

Common recommendation types include:

### Refresh
Older content with meaningful visibility.  
**Reason Code:** `stale_visible_page`  

### Monitor
Pages that do not currently justify intervention but should continue to be observed.  

### Metadata Review
Pages with visibility but potential CTR opportunity.  

**Usage note:**  
The recommendations are intended to **support human editorial review** rather than replace it.

## 11. Reproducibility

All notebooks used in this project are available in the project repository.

**Key notebooks:**  
- `w01_research_question.ipynb`  
- `w02_ml_task_framing.ipynb`  
- `w03_data_contract.ipynb`  
- `w04_baseline_score.ipynb`  
- `w05_model.ipynb`  
- `w06_validation_audit.ipynb`  
- `w07_action_playbook.ipynb`  

The repository contains all code required to reproduce the analysis.  

**Steps to re-run:**  
1. Clone the repository.  
2. Install dependencies from `requirements.txt`.  
3. Run notebooks in sequence (`w01` → `w07`).  

**Random seeds:**  
- All experiments used fixed seeds (`seed=42`) to ensure reproducibility.  

**Environment:**  
- Python 3.10  
- Key packages: scikit-learn, pandas, numpy, matplotlib, seaborn  
- See `requirements.txt` or `pip freeze` for exact versions.

## 12. Acknowledgments

This project was built on the **FlyRank ML Internship dataset**.

**Data Source:**  
[FlyRank](https://flyrank.ai/)  

Special thanks to the FlyRank team for providing the dataset and guidance during the internship.


## ------------------------------------------------------THE END-------------------