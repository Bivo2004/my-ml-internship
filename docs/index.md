# Optimizing Editorial Refresh Bandwidth via Machine Learning: Predicting Organic Search Traffic Decay

### Abstract
This paper investigates the application of machine learning to optimize editorial resource allocation by predicting organic search traffic decay. Using an anonymized dataset of 30,000 web pages, we developed a Random Forest Classifier paired with SelectKBest feature engineering. The resulting model outperforms traditional heuristic "stale content" rules, delivering a 1.35x lift in Precision@50. This provides content teams with a highly accurate, data-driven queue to prioritize content refreshes and maximize editorial ROI.

---

## 1. Introduction & Problem Statement
**Research Question:** Can a machine learning model outperform heuristic rules in identifying decaying web pages to optimize editorial refresh bandwidth?

**Decision Supported:** Content strategy and editorial resource allocation. By accurately predicting which pages are actively losing organic search visibility, content teams can prioritize high-value rewrites and avoid wasting budget on healthy pages. Traditional heuristics (like sorting purely by content age or high traffic) lead to false positives, wasting valuable writer hours on pages that do not require intervention.

---

## 2. Data Scope
* **Release:** Anonymized sample from the FlyRank internship warehouse, accessed via DuckDB.
* **Unit of Analysis:** One row = one published page over a 90-day aggregate snapshot.
* **Exclusions:** `trend_pct` is strictly excluded from features to prevent target leakage, as it is the mathematical basis for the outcome label. `competition_level` is dropped in favor of the continuous `competition` metric.

---

## 3. Methodology
* **Target Label (`is_declining_label`):** Binary outcome (1 if `trend_direction == 'down'`, 0 otherwise).
* **Validation Design:** 70/30 Train/Test split to ensure unbiased evaluation on unseen pages.
* **Modeling Strategy:** We implemented a Random Forest Classifier (`n_estimators=100`, `max_depth=5`, `class_weight="balanced"`). To optimize feature signal, we applied **SelectKBest** feature selection (`f_classif`) to isolate variables carrying the highest statistical variance toward organic traffic decay.
* **Leakage Checks:** Confirmed that no future-window metrics or label-derived components are present in the feature matrix.

---

## 4. Results (vs. Baseline)
To evaluate success, we compared the Random Forest probability rankings against a manual baseline heuristic rule (Stale + High Visibility) on the exact same test set, evaluating the **Precision@50** metric (measuring how many of the top 50 flagged pages are actually declining).

* **Manual Baseline Rule Precision@50:** `0.680`
* **Random Forest ML Model Precision@50:** `0.920`
* **Model Lift:** The machine learning model achieved a **1.35x lift** in precision at the top of the queue compared to the manual rule.

---

## 5. Limitations
* **Directional, Not Causal:** The model identifies pages with high decay risk, but it does not prove *why* they are decaying or guarantee that an editorial update will automatically reverse the trend.
* **Decision-Support Scope:** The outputs serve as a prioritization tool for human editorial teams, not an autonomous algorithmic predictor.

---

## 6. Ranked Recommendations (Action Playbook)
The primary operational output of this pipeline is a ranked probability queue. 
* Content strategists should consume this queue top-down. 
* Pages scoring a high decay probability (>0.70) should bypass general audits and go straight to editorial review for immediate triage and content refresh.

---

## 7. Artifacts & Visuals
* **Feature Importances:** Gini importance analysis extracted from the Random Forest reveals that `impressions_90d`, `avg_position`, and `content_age_days` carry the highest predictive weight in identifying organic decay.

---

## Reproducibility
The complete code, data contracts, and feature engineering pipelines used to generate this analysis are fully open-source and available for review:
* **Project Repository:** [https://github.com/Bivo2004/my-ml-internship](https://github.com/Bivo2004/my-ml-internship)
* **Modeling Notebook:** Available in the `work/notebooks/capstone.ipynb` directory.

---

## Acknowledgments & Data Credit
The dataset used in this research was provided by **FlyRank**. We extend our gratitude to their team for supplying the anonymized search telemetry and performance metrics that made this analysis possible.

Learn more about their search intelligence platform at: [https://flyrank.ai](https://flyrank.ai)
