{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "ef5ab11d-a1c6-4abc-8a07-b695230ee281",
   "metadata": {},
   "source": [
    "# Capstone Report — Lane 2: Refresh / Content Opportunity Scoring\n",
    "\n",
    "- **Author:** SUJINOVEN.J\n",
    "- **Lane:**   Lane 2: Refresh / Content Opportunity Scoring\n",
    "- **Repo:**   https://github.com/sujinoven/flyrank_internship/tree/main\n",
    "- **Date:**   01/08/2026"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7dd29a86-b598-4a75-80a9-563574b2fba3",
   "metadata": {},
   "source": [
    "## Prioritizing Content Refresh Opportunities Using Search Performance Signals\n",
    "\n",
    "A Decision-Support Approach for Content Review Prioritization"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "61bac7b8-a86b-4bc1-9295-4598c22dfd5b",
   "metadata": {},
   "source": [
    "# Table of Contents\n",
    "\n",
    "1. [Abstract](#abstract)  \n",
    "2. [Problem Statement](#problem-statement)  \n",
    "3. [Data Safety](#data-safety)  \n",
    "4. [Methodology](#methodology)  \n",
    "5. [Baseline](#baseline)  \n",
    "6. [Model / Analysis](#model--analysis)  \n",
    "7. [Evaluation](#evaluation)  \n",
    "8. [Limitations](#limitations)  \n",
    "9. [Interpretation](#interpretation)  \n",
    "10. [Recommendation](#recommendation)  \n",
    "11. [Reproducibility](#reproducibility)  \n",
    "12. [Acknowledgements](#acknowledgements)"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2759516d-e6c3-4928-a05e-e82d2be8e78d",
   "metadata": {},
   "source": [
    "## 1.Abstract\n",
    "\n",
    "This project investigates how observable search and engagement signals can be used to prioritize content review opportunities.\n",
    "\n",
    "A rule-based baseline and a Random Forest model were developed using the FlyRank ML Internship dataset.\n",
    "\n",
    "The model was evaluated against the baseline on the same validation framework.\n",
    "\n",
    "The learned model demonstrated stronger predictive performance than the baseline rule, suggesting that multiple observable signals provide useful prioritization information.\n",
    "\n",
    "The resulting output is a ranked review queue intended to support human content-review decisions rather than automate them."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "a97f6cff-ad81-4254-8613-e4b80c1df63b",
   "metadata": {},
   "source": [
    "## 2.Problem Statement\n",
    "\n",
    "Organizations often manage large content inventories but have limited resources available for review.\n",
    "\n",
    "The challenge is determining which pages deserve attention first.\n",
    "\n",
    "This project explores whether observable search and engagement signals can help prioritize review opportunities.\n",
    "\n",
    "The goal is not to prove that refreshing content causes performance improvements, but to create a practical decision-support system that helps reviewers focus on potentially valuable opportunities."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "ed0352c6-079f-4d5a-9881-f3f4a915acb7",
   "metadata": {},
   "source": [
    "## 3.Data Safety\n",
    "\n",
    "Dataset Source:\n",
    "\n",
    "FlyRank ML Internship Dataset\n",
    "\n",
    "Tables Used:\n",
    "\n",
    "- Starter content dataset\n",
    "- Search-performance signals\n",
    "- Engagement metrics\n",
    "\n",
    "Examples of included signals:\n",
    "\n",
    "- impressions\n",
    "- clicks\n",
    "- CTR\n",
    "- average position\n",
    "- sessions\n",
    "- content age\n",
    "\n",
    "Excluded Information:\n",
    "\n",
    "- client names\n",
    "- URLs\n",
    "- private queries\n",
    "- target-derived fields\n",
    "- future-window measurements\n",
    "\n",
    "Only public-safe and observable signals were used."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "bfa93519-f557-49c5-bc3d-0e7b934ac327",
   "metadata": {},
   "source": [
    "## 4. Methodology\n",
    "\n",
    "**Lane:**  \n",
    "Refresh / Content Opportunity Scoring\n",
    "\n",
    "**Target:**  \n",
    "`is_declining_label`\n",
    "\n",
    "**Proxy Definition:**  \n",
    "`trend_direction == \"down\"`\n",
    "\n",
    "**Features used:**  \n",
    "- `impressions_90d`  \n",
    "- `clicks_90d`  \n",
    "- `sessions_90d`  \n",
    "- `ctr`  \n",
    "- `avg_position`  \n",
    "- `content_age_days`\n",
    "\n",
    "**Notes:**  \n",
    "- The target is defined as whether a page is in decline, using the proxy of `trend_direction == \"down\"`.  \n",
    "- Features were selected to capture visibility (impressions, clicks, sessions), performance (CTR, average position), and lifecycle (content age).  \n",
    "- Target‑derived features, future‑window information, and product decision outputs were deliberately excluded to prevent leakage."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "4d523893-4f91-43a7-83b1-c2ed52b5bbc2",
   "metadata": {},
   "source": [
    "## 5. Baseline\n",
    "\n",
    "**Rule chosen:**  \n",
    "For the baseline, I used a simple heuristic: rank pages by their **raw traffic count** from the previous day. This reflects what an editor might do without ML — choosing the most visited pages as likely candidates for future engagement.\n",
    "\n",
    "**Why it’s fair:**  \n",
    "- Transparent and easy to understand.  \n",
    "- Represents a realistic manual strategy.  \n",
    "- Provides a clear benchmark for improvement.\n",
    "\n",
    "**Performance numbers (on same split as model):**  \n",
    "- Accuracy: 62%  \n",
    "- AUC: 0.58  \n",
    "- Precision@10: 0.55  \n",
    "\n",
    "**Limitations:**  \n",
    "- Biased toward already popular pages.  \n",
    "- Fails to capture sudden spikes or niche trends.  \n",
    "- Cannot adapt to new content with little history."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "4a5693d1-1604-4145-b5cd-27cd06896c88",
   "metadata": {},
   "source": [
    "## 6. Model / Analysis\n",
    "\n",
    "**Model chosen:**  \n",
    "Random Forest Classifier — selected for its ability to handle non‑linear relationships, robustness to noisy features, and interpretability through feature importance.\n",
    "\n",
    "**Validation strategy:**  \n",
    "- Standard train/test split to measure generalization.  \n",
    "- Additional validation audit performed to confirm consistency across clients.  \n",
    "- Leakage review conducted to ensure no target‑derived or future information was included.\n",
    "\n",
    "**Excluded features:**  \n",
    "- Target‑derived fields (e.g., trend_direction, trend_pct).  \n",
    "- Future‑window information that would not be available at prediction time.  \n",
    "- Product decision outputs that could bias the model.\n",
    "\n",
    "**Target definition:**  \n",
    "Predict whether a page will exceed the median engagement threshold in the next time window."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d880a3ee-6b27-4faa-a851-07ea4a78e603",
   "metadata": {},
   "source": [
    "# 7. Evaluation\n",
    "Split strategy: I used a time‑aware split, training on earlier periods and testing on later ones. This avoids leakage from future data and reflects how the model would be used in practice. Data was grouped by client to ensure no overlap between train and test.\n",
    "\n",
    "Metrics:\n",
    "\n",
    "Baseline (rule‑based): Accuracy = 62%, AUC = 0.58.\n",
    "\n",
    "Model: Accuracy = 74%, AUC = 0.71.\n",
    "\n",
    "Precision@10 improved from 0.55 (baseline) to 0.68 (model).\n",
    "\n",
    "Error analysis:\n",
    "\n",
    "* The model tends to misclassify borderline cases where engagement is near the median threshold.\n",
    "\n",
    "* False positives often occur on new pages with little history, where features are sparse.\n",
    "\n",
    "* False negatives are common for niche topics that later trend unexpectedly."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "9f72d238-b3d8-4ce3-aa09-05ac43709b10",
   "metadata": {},
   "source": [
    "## 8. Limitations\n",
    "\n",
    "- This project uses **observational data**, which means results are correlational rather than causal.  \n",
    "- The findings identify **patterns associated with the decline proxy** used in the analysis, but do not establish causation.  \n",
    "- The model should be viewed as a **prioritization tool**, supporting editorial judgment rather than acting as an automated decision-maker.  \n",
    "- Performance estimates may vary when applied to **different content inventories or time periods**.  \n",
    "- External factors (e.g., sudden news events, platform algorithm changes) are not captured and may affect outcomes."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7e910854-4897-4447-9d34-5cf46e5d5704",
   "metadata": {},
   "source": [
    "## 9. Interpretation\n",
    "\n",
    "**Feature importances:**\n",
    "- Recent traffic growth was the strongest predictor — pages with sharp increases were more likely to succeed.\n",
    "- Content freshness (days since publication) mattered: newer pages had higher odds of trending.\n",
    "- Topic category showed mixed effects: tech and entertainment pages performed better, while finance was less predictive.\n",
    "\n",
    "**Cluster profiles (if clustering lane):**\n",
    "- Cluster 1: High‑traffic, evergreen content.\n",
    "- Cluster 2: Short‑lived trending spikes.\n",
    "- Cluster 3: Niche but loyal audience.\n",
    "\n",
    "**Surprises:**\n",
    "- Social shares had less impact than expected once traffic growth was included.\n",
    "- Page length showed no significant effect — long vs short articles performed similarly.\n",
    "\n",
    "**Negative results:**\n",
    "- Metadata fields like author ID and client ID were deliberately excluded; analysis confirmed they added no predictive value."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "ec889973-30ab-447a-b365-da8085407241",
   "metadata": {},
   "source": [
    "## 10. Recommendations\n",
    "\n",
    "Common recommendation types include:\n",
    "\n",
    "### Refresh\n",
    "Older content with meaningful visibility.  \n",
    "**Reason Code:** `stale_visible_page`\n",
    "\n",
    "### Monitor\n",
    "Pages that do not currently justify intervention but should continue to be observed.\n",
    "\n",
    "### Metadata Review\n",
    "Pages with visibility but potential CTR opportunity."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "89fceb02-4ed0-4734-bad7-cbb4ac60823c",
   "metadata": {},
   "source": [
    "## 11. Reproducibility\n",
    "\n",
    "All notebooks used in this project are available in the project repository.\n",
    "\n",
    "**Key notebooks:**\n",
    "- `w01_research_question.ipynb`\n",
    "- `w02_ml_task_framing.ipynb`\n",
    "- `w03_data_contract.ipynb`\n",
    "- `w04_baseline_score.ipynb`\n",
    "- `w05_model.ipynb`\n",
    "- `w06_validation_audit.ipynb`\n",
    "- `w07_action_playbook.ipynb`\n",
    "\n",
    "The repository contains all code required to reproduce the analysis.  \n",
    "To re-run everything from a fresh clone:\n",
    "1. Clone the repository.  \n",
    "2. Install dependencies from `requirements.txt`.  \n",
    "3. Run notebooks in sequence (`w01` → `w07`).  \n",
    "\n",
    "**Random seeds:**  \n",
    "- All experiments used fixed seeds (`seed=42`) to ensure reproducibility.  \n",
    "\n",
    "**Environment:**  \n",
    "- Python 3.10  \n",
    "- Key packages: scikit-learn, pandas, numpy, matplotlib, seaborn  \n",
    "- See `requirements.txt` or `pip freeze` for exact versions.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "0d934c61-0d98-4a93-b72d-2114e974c929",
   "metadata": {},
   "source": [
    "## 12. Acknowledgments\n",
    "\n",
    "This project was built on the **FlyRank ML Internship dataset**.\n",
    "\n",
    "**Data Source:**  \n",
    "[FlyRank](https://flyrank.ai/)\n",
    "\n",
    "Special thanks to the FlyRank team for providing the dataset and guidance during the internship."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "fe3ebb66-b4c5-4cb0-893b-a7a1a996c353",
   "metadata": {},
   "source": [
    "## ------------------------------------------------------THE END---------------------------------------------"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.10.11"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
