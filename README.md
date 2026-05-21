### Due to the confidentiality of the project, we are unable to share the source code or the underlying data. However, we have prepared a detailed outline describing the methodology we followed and the overall structure of our implementation.

# Flight Turnaround Matching — Machine Learning & Optimization

This project aims to **automatically identify the correct departure flight corresponding to the true turnaround of an arrival flight**, using a combination of:

- a ranking model (**XGBRanker**) to score candidate flight pairs,
- the **Hungarian algorithm** to compute the optimal assignment,
- a full pipeline for **preprocessing**, **feature engineering**, **model selection**, and **hyperparameter optimization**.

---

## Project Structure

```
.
├── EAL_Linking_Data.xlsx         # Input dataset
├── EDA.ipynb                     # Exploratory Data Analysis
├── Hyperparameters_choice.py     # Optuna hyperparameter optimization for XGBRanker
├── Pipfile
├── Pipfile.lock
├── README.md
├── main.py                       # Full training + prediction pipeline
├── requirements.txt
├── src
│   ├── Preprocessing.py          # Data cleaning and filtering of coherent flights
│   ├── Feature_engineering.py    # Feature construction (time, geography, low-cost, transitions…)
│   ├── Hungarian_algorithm.py    # Optimal assignment using the Hungarian algorithm
│   └── model_choice.py           # Model selection and instantiation
└── utils.py                      # Utility functions (mappings, flight time, continent lookup…)

```

## Project Objective

The goal is to **predict the correct departure flight** for each arrival flight by considering:

- flight type (Cargo / Commercial),
- allowed turnaround time window,
- operational consistency (airline, aircraft type),
- geographic and temporal characteristics,
- assignment constraints (one departure per arrival).

---

## Preprocessing

The **Preprocessing** module:

- removes inconsistent or impossible flights,
- applies rules specific to *Cargo* or *Commercial* traffic,
- restricts the time window to reduce computational complexity,
- keeps only realistic arrival–departure candidates.

---

## Feature Engineering

Generated features include:

- parking time,
- estimated flight duration,
- origin/destination continent,
- Cargo/Commercial transitions,
- low‑cost vs legacy airline indicator,
- origin/destination consistency,
- aircraft size category.

---

## Modeling

The project supports several models:

- **XGBRanker** (main model),
- XGBoost Classifier,
- Random Forest,
- Logistic Regression,
- LightGBM.

Model selection example:

```python
clf = model(choice="XGBRanker", specificity="Cargo")
```

---

## Hyperparameter Optimization

Hyperparameters are optimized using **Optuna**, maximizing the **NDCG** score per arrival group.

---

## Hungarian Algorithm

After scoring all candidate pairs, the Hungarian algorithm:

- builds a cost/benefit matrix,
- maximizes the total predicted score,
- returns the optimal arrival → departure assignment.

---

## Running the Pipeline

Run the full workflow with:

```bash
python main.py
```

This script:

1. loads the dataset,
2. generates candidate pairs,
3. builds features,
4. trains the model,
5. applies the Hungarian algorithm,
6. outputs final predictions.

---

## Results

We obtained **strong recall performance** using our two‑stage pipeline:  
an **XGBRanker** to score all candidate arrival–departure pairs, followed by the **Hungarian algorithm** to select the optimal matching.  
This combination consistently identifies the correct turnaround flight with high reliability.


---

## Note

Some functions may appear complex, but this is **the result of deliberate optimization to keep computational complexity as low as possible**, especially during candidate pair generation.
