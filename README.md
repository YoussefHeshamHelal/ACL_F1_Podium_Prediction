# F1 Podium Prediction

**Milestone 1 · DMET904 / CSEN 903 Advanced Computer Lab · German University in Cairo · Winter 2026**

For every driver entering a Formula 1 race, the model predicts whether that driver finishes on the podium (top 3).
It uses only information available **after qualifying and before the race starts**, on the Formula 1 World
Championship dataset (1950–2024, 14 relational tables).

| | |
|---|---|
| Kaggle notebook | <https://www.kaggle.com/code/youssefheshamhelal/f1-acl> |
| Report (PDF) | [`reports/F1_Podium_Prediction_Report.pdf`](reports/F1_Podium_Prediction_Report.pdf) |
| Notebook | [`notebooks/f1_podium_prediction.ipynb`](notebooks/f1_podium_prediction.ipynb) (executed, outputs included) |

**Team:** Youssef Hesham Abdelhamid Helal (58-0552), Samer Ramzy (58-19523), Kerollos Mohsen (58-14434), Dyala Elsmery (58-21656)

## Results (unseen test seasons 2022–2024)
| Model | PR-AUC | ROC-AUC | F1 | Precision | Recall |
|---|---|---|---|---|---|
| Logistic regression (baseline) | 0.707 | 0.935 | 0.667 | 0.606 | 0.740 |
| **Random forest (selected on validation)** | **0.714** | **0.938** | **0.686** | 0.622 | 0.765 |
| Gradient boosting | 0.718 | 0.938 | 0.677 | 0.643 | 0.716 |
| Shallow neural network (Keras) | 0.694 | 0.934 | 0.624 | 0.630 | 0.618 |
| *Grid rule (the first three on the grid)* | *0.647* | *0.914* | *0.649* | *0.650* | *0.647* |

- **Primary metric: PR-AUC.** Podiums are rare, about 15 % of starters.
- **Train / validation / test:** split by season: ≤ 2019 / 2020–21 / 2022–24.
- **Choices made on validation only:** hyperparameters and thresholds.

**Data-engineering answers:**
- **Q1:** front-row conversion rose from 68 % to 76 % after 2014. Suzuka, Abu Dhabi and Monaco are the top circuits.
- **Q2:** there is no home advantage once grid position is controlled for (OR 0.95, p = 0.56).
- **Q3:** mechanical retirements fell from 9.9 % (2014–21) to 6.1 % (2022–24). The Alpine lineage is the least
  reliable team in both eras.

## Notebook contents
One run-all notebook with these sections:
1. Setup and raw audit (PK/FK checks, sentinels, EDA before cleaning)
2. Cleaning (sentinels, semantic typing, duplicate rule, Indy 500, outliers, EDA after cleaning)
3. Data-engineering questions Q1–Q3
4. Leakage-safe feature engineering (time-travel test)
5. Season split, recency weighting, preprocessing-order experiments
6. Models (LR, random forest, gradient boosting, shallow NN): 3 attempts, all metrics on train / validation / test
7. Feature-group ablation and explainability (permutation importance, SHAP, LR coefficients, LIME)
8. Inference function `predict_podium(record)`, on a complete and a partially-missing record
9. Conclusions

The appendix is a race viewer: pick any race and compare the predicted podium with the real one.

## Repository structure
```
data/raw/            the 14 original CSVs (1950–2024 release, unmodified)
notebooks/           f1_podium_prediction.ipynb, the single run-all notebook
outputs/figures/     every figure saved by the notebook
reports/             analytical report (PDF + HTML source)
requirements.txt     pinned package versions (Python 3.11)
```

## How to run
**Locally** (Python 3.11):
```bash
python -m venv .venv
.venv/Scripts/activate            # Windows; on macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=3600 notebooks/f1_podium_prediction.ipynb
```
The notebook reads the data from `data/raw/`. A full run takes about 11 minutes on a laptop CPU.

**On Kaggle:**
1. Attach the dataset (*Add Input*). The notebook finds `results.csv` under `/kaggle/input` automatically.
2. Switch **Internet on** in the notebook settings. The first cell installs the pinned scikit-learn, shap and lime.
3. Run All. The data-version check in §1.2 confirms that the 1950–2024 release is attached.

## Data
Formula 1 World Championship (1950–2024), Kaggle / Ergast:
<https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020>. The raw CSVs are never modified;
all cleaning happens on in-memory copies.
