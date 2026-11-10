# Hospital Visit Review Pipeline

An end-to-end data pipeline built with Python, SQL, and a machine learning
classifier. It generates synthetic hospital visit records, validates them,
stores them in a SQLite database, analyzes them with SQL, and trains a
decision tree model to flag the visits a billing or quality team should
review first.

No real patient data is used anywhere in this project. All records are
synthetic, generated with a fixed random seed so that anyone who runs the
notebook gets exactly the same data and the same results.

## What the pipeline does

1. **Generate** — 800 synthetic visit records (department, payer, wait time,
   amount billed, amount collected) using NumPy and pandas. A review flag is
   defined by two business rules: the hospital collected less than half of
   the billed amount, or the patient waited more than 90 minutes.
2. **Validate** — data quality checks before anything else: no missing
   values in any column, no duplicate visit IDs, value ranges inspected
   with descriptive statistics.
3. **Store** — the cleaned data is loaded into a SQLite database table.
4. **Analyze** — SQL queries with GROUP BY aggregations summarize visits by
   department and by payer.
5. **Model** — a scikit-learn decision tree classifier learns to predict the
   review flag from wait time, billed amount, and collection rate.
6. **Audit** — the model's errors are inspected individually instead of
   accepting the accuracy score at face value.

## Findings

- 185 of 800 visits (23%) met at least one review rule.
- **SQL analysis found no meaningful differences** in average wait time
  across departments (29.8 to 33.2 minutes) or in average collection rate
  across payer types (0.669 to 0.697). The data was generated with no group
  effects built in, so the honest result is reported: there is no
  department or payer story in this dataset. Flagged counts follow group
  size, not group behavior.
- The classifier reached **99% accuracy** on 200 unseen test visits
  (confusion matrix: 151 correct clears, 47 correct flags, 0 false alarms,
  2 misses).
- **Error audit:** both missed visits had wait times of 92.9 and 92.4
  minutes, just over the 90-minute threshold, with high collection rates.
  The model had learned a cutoff of roughly 93 minutes from the training
  data, so both errors are borderline boundary cases with a clear cause,
  not data or logic failures.

## Tools

Python, pandas, NumPy, SQLite (sqlite3), SQL, scikit-learn, Jupyter
(DataLab). Seed and random states are fixed throughout, so the notebook is
fully reproducible.

## Files

- `hospital_visit_review_pipeline.ipynb` — the full pipeline notebook,
  with the audit conclusion written in the notebook itself.
- `er_visits.csv` — the generated dataset.
